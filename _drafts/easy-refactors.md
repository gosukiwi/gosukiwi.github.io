---
layout: post
title:  "Easy Refactors"
date:   2022-04-29
categories: webdev
---

The following are some easy refactorings you can systematically apply to your
code.

## Repeated Method
I like [Sandi Metz](https://sandimetz.com/) approach of preferring duplication
over the wrong abstraction.

Rather than overthinking about your code, just let it repeat twice or thrice.
Once you have repetition, it's much easier to refactor.

The easiest and most common way to refactor is to identify the parts that
change and the parts that stay the same, and remove the parts that change into
parameters.

```ruby
def create_federico
  hobbies = Hobbies.find([1, 2, 3])
  pets = Pets.find([5, 6, 7])
  Person.create(name: 'Federico', hobbies: hobbies, pets: pets)
end

def create_john
  hobbies = Hobbies.find([6, 7, 8])
  pets = Pets.find([9, 1, 2])
  Person.create(name: 'John', hobbies: hobbies, pets: pets)
end
```

Refactors to

```ruby
def create_person(hobby_ids, pet_ids, name)
  hobbies = Hobbies.find(hobby_ids)
  pets = Pets.find(pet_ids)
  Person.create(name: name, hobbies: hobbies, pets: pets)
end
```
