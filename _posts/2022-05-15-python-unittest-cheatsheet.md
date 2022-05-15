---
layout: post
title:  "Python unittest Cheatsheet"
date:   2022-05-15
categories: webdev python
---
Python is a great language with lots of awesome libraries and resources. Most
of those resources are beginner-focused though, and spend lots of words to say
very little. Not to mention the awful advertisements, pop-ups, paywalls, etc.

The [official docs](https://docs.python.org/3/library/unittest.html) on
`unittest` are good, but it feels too encyclopedic, and it's hard to find a
quick overview.

In this post I'll give you just that, a quick and to the point overview on how
to test with Python's built-in `unittest` framework.

# Test Discovery
Python's `unittest` module includes test discovery, which means it will scan
your project folder and find all relevant test files to run.

You can customize it if you want, the _convention_ is this:

* Put your tests into a `test` directory in your project root
* The `test` directory must be a valid package, and include a `__init__.py`
* All sub-packages must also include a `__init__.py`
* Each module you want to test must start with `test`, for example: `test_my_class.py`
* Each test method must start with `test`

Here's an example directory structure:

```
test
├── __init__.py
└── my_package
    ├── __init__.py
    ├── game
    │   ├── __init__.py
    │   └── core
    │       ├── __init__.py
    │       ├── test_pubsub.py
    │       ├── test_sprite.py
    │       └── test_vector.py
    ├── parser
    │   ├── __init__.py
    │   ├── test_input.py
    │   ├── test_mapfile_parser.py
    │   └── test_parser.py
    └── test_models.py
```

And here's an example test file:


```python
import unittest
from engine.game.core.vector import Vector


class TestVector(unittest.TestCase):
    def test_add(self):
        v1 = Vector(2, 3)
        v2 = Vector(1, 1)

        v3 = v1 + v2

        self.assertEqual(v3.x, 3)
        self.assertEqual(v3.y, 4)
```

With that in place, you can run your tests by simply running `python -m
unittest`.

You can see all of the assert methods [in the official
documentation](https://docs.python.org/3/library/unittest.html#assert-methods).

## Mocking
The `unittest` module includes great mocking support. When mocking, I like to
follow a few rules:

* Never mock the object under test
* Mock as little as possible
* Make sure the mock object mimics the implementation of the real object

We can easily do all this with `unittest.mock`:

```python
import unittest
import appgamekit as agk
from unittest.mock import patch, Mock
from engine.game.core.sprite import Sprite
from engine.game.core.image import Image


class TestSprite(unittest.TestCase):
    @patch('engine.game.core.sprite.agk', spec=agk)
    def test_dispose(self, agk):
        agk.create_sprite.return_value = 7
        image = Mock(spec=Image, id=1)

        sprite = Sprite(image)
        sprite.dispose()

        agk.delete_sprite.assert_called_once_with(7)
        image.dispose.assert_called_once()
```

You can read [the official
documentation](https://docs.python.org/3/library/unittest.mock.html#quick-guide)
for more details, but the gist is this:

* You can use the `@patch` decorator to replace anything with a mock (so you
  don't __need__ dependency injection, although it should be preferred as it's
  better object oriented design)
* You can use `Mock` to create a mock object, which will _mimic_ the object
  passed in `spec=`. This is very important, as if we ever change the real
  object, we want our tests to complain

In our code, `agk` is an [external
library](https://fascimania.itch.io/appgamekit-for-python) which creates a
game window, sprites, sounds, text, and all that's required to create a game.
We don't want to test the real thing in a unit test (we would do it in an
integration test), so we mock it away.

We pass the `spec` parameter to the `@patch` decorator so we make sure the
test will raise an error if we use something in the mock that doesn't exist in
the real object.

We set the return value of the `agk.create_sprite` method, and also create a
mock for `Image`, which is a dependency of `Sprite`. Note that we also pass
`spec` there, and an `id` attribute which will be equal to `1`.

We can then assert our mocks were called in the way we expect to finish our
little test.

## That's it!
If you want to know more about testing in general, feel free to check out my
other blog post: [OOP Fundamentals: Quick and Dirty Guide to
Testing](https://blog.beezwax.net/oop-fundamentals-quick-and-dirty-guide-to-testing/).

For the complete documentation on Python's `unittest`, check out [the official
documentation](https://docs.python.org/3/library/unittest.html).
