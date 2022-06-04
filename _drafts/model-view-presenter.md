---
layout: post
title:  "Model View Presenter"
date:   2022-04-29
categories: webdev design
---
Nowadays, most front-end web applications use a framework like React or Vue.

When an application grows, it's natural to add more libraries, such as
[Redux](https://redux.js.org/) for state management, [ReactRouter](some
routing) for managing routes, etc.

There's nothing inherently wrong with that approach, but like everything in
life, there's no one-size-fits-all solution.

This approach is not ideal if:

* Your application is really small and simple
* You care about the final size (as in KB) of your application
* You have non-standard needs
* Your team doesn't have experience with the framework you want to use
* You don't want to be locked-in to a single framework or library
* You want to share code across other applications which might use a different
  framework

... and I'm sure you can come up with more reasons.

In this post I'll show you an alternative, library-free way to organize your
JavaScript (or any language, really) code, so you have maximum flexibility for
your project.

## MVP: Model-View-Presenter
Model-View-Presenter, or MVP for short, it's a design pattern from the 90's
that was created to deal with complex user interfaces (think Desktop apps).

Quoting from [Wikipedia](https://en.wikipedia.org/wiki/Model–view–presenter):

> MVP is a user interface architectural pattern engineered to facilitate
> automated unit testing and improve the separation of concerns in
> presentation logic.

It's a very object-oriented pattern. To implement MVP, you need to divide your
application in 3 layers: `models`, `views` and `presenters`.

* The model layer is your data. The entities of your business logic.
* The view displays data (models) and handles user events.
* The presenter acts upon the model and the view. It retrieves data from
  repositories (models), and formats it for display in the view.

The MVP division is merely conceptual, and it can vary slightly from app to
app. There is no MVP library (although you could use or create one, it's not
needed), all you need to do is create some files or folders and keep the
separation of concerns by yourself.

In MVP, the data flow looks like this:

```
 +-----------------------+
 |         View          |
 +-----------------------+
    |              ^
    |              |
user events  update view
    |              |
    v              |
 +-----------------------+
 |       Presenter       |
 +-----------------------+
    |              ^
    |              |
update model  on model changed
    |              |
    v              |
 +-----------------------+
 |         Model         |
 +-----------------------+

```

It's important that the model and view layers don't access each other
directly. They communicate through the presenter.

In fact, the model and view layers don't know about the presenter layer
either, as they use the observer pattern to communicate.

It's important to note that because the view and model layers are isolated and
don't depend on anything, they can easily be replaced by other objects that
implements the same interface.

If all of that seems abstract, don't worry, I will show a concrete example in
the next section.

## Show me the code already
The data flow diagram is helpful to understand how MVP works, but here is how
it would work in real app:

* A user clicks a button
* The view triggers an event: "My button was clicked"
* The presenter was listening to this event, and does some work
* The presenter changed some models while doing work 
* Because the models were changed, they trigger an event "I was changed"
* The presenter was listening to this event, and re-renders the view 

Let's see some code already. Assume we have a very simple model layer:

```javascript
class Counter extends PubSub {
  constructor(value) {
    super();
    this.value = value;
  }

  increment() {
    this.value += 1;
    this.trigger("change");
  }
}
```

We extend from `PubSub`, which stands for
[Publish/Subscribe](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern).
It simply gives us the `trigger` and `on` methods.

Our view could look like this:

```javascript
class View extends PubSub {
  constructor(value) {
    super();
    this.value = value;
  }

  // returns a DOM element representing this view
  render() {
    const info = document.createElement("div");
    info.innerText = `Value is ${this.value}`;

    const button = document.createElement("button");
    button.innerText = "Increment";
    button.addEventListener("click", () => this.trigger("increment-clicked"));

    const element = document.createElement("div");
    element.appendChild(info);
    element.appendChild(button);

    return element;
  }
}
```

Finally, our presenter:

```javascript
class Presenter {
  constructor(el) {
    this.el = el;
    this.model = new Counter(1);
    this.model.on("change", () => this.render());
  }

  render() {
    const value = this.model.value;
    const view = new View(value);
    view.on("increment-clicked", () => {
      this.model.increment();
    });

    this.el.innerHTML = "";
    this.el.appendChild(view.render());
  }
}

const container = document.querySelector("#app");
const presenter = new Presenter(container);
presenter.render();
```

This is a very simple example, but we can see the data flow clearly. You can
see a working example below:

<iframe src="https://codesandbox.io/embed/mvp-example-gk0p6z?fontsize=14&hidenavigation=1&module=%2Fsrc%2Findex.js&theme=dark"
     style="width:100%; height:500px; border:0; border-radius: 0; overflow:hidden;"
     title="mvp-example"
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
   ></iframe>

## FileMaker and FMBond
At [Beezwax](https://www.beezwax.net/) we do a lot of FileMaker work. So much
that we have created [FMBond](https://www.npmjs.com/package/@beezwax/fmbond),
an npm package which makes the interaction between FileMaker and JavaScript
quite easy.

FileMaker can create a WebView (an instance of Safari) and render a front-end
JavaScript application just fine, but it has some considerable differences
from regular front-end apps. The main difference being that your data will
come from FileMaker instead of an HTTP API.

Because MVP allows us to abstract the model layer away, this is not an issue
for us.

FileMaker data is transformed into _models_ (plain old JavaScript objects),
using a hierarchical structure (eg: A `Post` model has many `Comment` models,
and can be accessed as `post.comments`):

```javascript
FMBond
  .PerformScript('some script to get data', { param1: 'somevalue' })
  .then((data) => {
    const repository = new Repository(data)
    const el = document.querySelector('#my-app')
    const app = new Presenter({ repository, el })
  })
```

The repository is the entry point to access your data. You can think of it as
a `db` object.

The API of the repository could look like this:

```
const data = ... get from filemaker or other source
const repo = new Repository(data)

// find a post by id
const post = repo.posts.find(1) // find by id 1
post.comments.length // access related models

// find a comment by id
const comment = repo.comments.find(1) // find comment
const post = comment.post // access related model
```

The implementation can vary from project to project, what's important is to
have a central place where you can get access to all your models.

## Swapping Parts
This architecture gives us lots of flexibility and room to grow our app.

We can, for example, swap the model layer from FileMaker to a new object that
uses HTTP requests instead, and move our app to a regular browser instead of
having it live in FileMaker. We can even have it live at both places, and
choose what implementation we want to use depending on the environment. We
simply use the FileMaker model layer when in a FileMaker web view, and the
HTTP model layer when in a regular browser.

As long as both model layers implement the same interface, we're good. We
could even share some tests to make sure they behave the same way.

We could also replace our simple view with something like React, and take
advantage of regular React tools, like component state and event handlers:

```javascript
class MyPresenter {
  constructor ({ repo, el }) {
    this.view = this.render(el)
    this.repo = repo
    this.handleNameChanged = this.handleNameChanged.bind(this)

    repo.on('name-changed', (name) => {
      this.view.setName(name)
    })
  }

  handleNameChanged (event) {
    const name = event.value
    const person = this.repo.find(1)
    person.name = name
    this.repo.trigger('name-changed', name)
  }

  render (el) {
    const person = this.repo.people.find(1)
    return ReactDOM.render(
      <MyReactComponent 
        name={person.name} 
        age={person.age} 
        onNameChanged={this.handleNameChanged} />
      , el
    )
  }
}
```
