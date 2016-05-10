---
layout: post
title: Testing in Python Using DDT
excerpt: "How to do data-driven-testing using the DDT library in Python."
tags: [python, ddt, testing]
author: dustin_schoenbrun
---

# Testing in Python

One of the things that I am most passionate about in the realms of software
engineering is good tests. Whether it be through unit tests, functional tests,
integration tests, or scenario tests, I have a passion for quality in software.

Most developers know about writing unit tests as a way of assuring at least some
nominal sense of code quality. This is great, and more developers should use them!
For our example here, we will need to test various values into a method that
will create a weapon out of a type of metal, say for an RPG or something of the
like. Let's assume that we can make four different types of weapons out of four
different types of metal. These are represented in the `weapons` and `metals` lists below,
respectively.

Let's also assume that we have a class called `Armament` that contains the
type of weapon and what it's made out of. A simple implementation is given
below:

{% highlight python %}
weapons = ["sword", "spear", "axe", "voulge"]
metals = ["copper", "silver", "gold", "mithril"]

class Armament(Object):
  def __init__(self, weapon_kind, metal):
    if weapon_kind not in weapons:
      raise TypeError("Invalid weapon type")
    self.kind = weapon_kind

    if metal not in metals:
      raise TypeError("Invalid metal type")
    self.metal = metal
{% endhighlight %}

# Testing the Smithy

We can utilize the builtin `unittest` library to provide us with some struture and some basic
assertions to create our tests. Let's say that we wanted to see if we could successfully create
each weapon out of each type of metal. To test this, we could do sixteen unit tests. One for
each combination of weapon and metal to make it. Below are some unit tests that I created to make
each type of weapon in copper:

{% highlight python %}
import unittest

from armament import Armament

class TestArmament(unittest.TestCase):
    def test_create_copper_sword(self):
        weapon = Armament("sword", "copper")
        self.assertEqual("sword", weapon.kind)
        self.assertEqual("copper", weapon.metal)

    def test_create_copper_spear(self):
        weapon = Armament("spear", "copper")
        self.assertEqual("spear", weapon.kind)
        self.assertEqual("copper", weapon.metal)

    def test_create_copper_axe(self):
        weapon = Armament("axe", "copper")
        self.assertEqual("axe", weapon.kind)
        self.assertEqual("copper", weapon.metal)

    def test_create_copper_voulge(self):
        weapon = Armament("voulge", "copper")
        self.assertEqual("voulge", weapon.kind)
        self.assertEqual("copper", weapon.metal)

    # Repeat for silver, gold, and mithril

if __name__ == "__main__":
    unittest.main()

{% endhighlight %}

As you can see, this gets old pretty quick. There are a lot of bad code smells
in the above code, such as duplication of code everywhere. Now, we could clean
this up by having a helper function that each of the test cases calls that does
the asserts with given values for kind and metal. But even then we have sixteen
test cases running around that all do the same thing. There has to be a better
way...

# Enter the DDT library

DDT is a Python library used for Data-Driven Testing, which also gives the DDT
library its name. It allows a developer to "multiply" tests using different
sets of data. This way, you can have one test definition in your code but can
get many test cases exercised from it. You can install the DDT library easily
on your system using pip:

{% highlight bash %}
pip install ddt
{% endhighlight %}

# Redoing the Tests

Now that we have DDT, we can reconfigure our tests to utilize the DDT library
to make our test file much simpler. Check out the example below:

{% highlight python %}
import ddt
import unittest

from armament import Armament

# We need this decorator to show that this is a ddt-enabled class
@ddt.ddt
class TestArmamentDDT(unittest.TestCase):
    # We need the unpack decorator to unpack our dictionaries, otherwise they
    # would just be passed in as they are
    @ddt.unpack
    @ddt.data(
        {"weapon_kind": "sword", "metal": "copper"},
        {"weapon_kind": "sword", "metal": "silver"},
        {"weapon_kind": "sword", "metal": "gold"},
        {"weapon_kind": "sword", "metal": "mithril"},
        {"weapon_kind": "spear", "metal": "copper"},
        {"weapon_kind": "spear", "metal": "silver"},
        {"weapon_kind": "spear", "metal": "gold"},
        {"weapon_kind": "spear", "metal": "mithril"},
        {"weapon_kind": "axe", "metal": "copper"},
        {"weapon_kind": "axe", "metal": "silver"},
        {"weapon_kind": "axe", "metal": "gold"},
        {"weapon_kind": "axe", "metal": "mithril"},
        {"weapon_kind": "voulge", "metal": "copper"},
        {"weapon_kind": "voulge", "metal": "silver"},
        {"weapon_kind": "voulge", "metal": "gold"},
        {"weapon_kind": "voulge", "metal": "mithril"},
    )
    def test_create_weapons(self, weapon_kind, metal):
        weapon = Armament(weapon_kind, metal)
        self.assertEqual(weapon_kind, weapon.kind)
        self.assertEqual(metal, weapon.metal)

if __name__ == "__main__":
    unittest.main()

{% endhighlight %}

If we run our tests, we should get the output shown below:

{% highlight text %}
Zeromus :: Code/python/ddt_demo
  [23:28:04] % python3 test_armament_ddt.py
................
----------------------------------------------------------------------
Ran 16 tests in 0.001s

OK
{% endhighlight %}

# So What's the Point?

The code shown above is indeed functionally identical to the tests that we wrote
earlier. Instead of having nearly a hundred lines of test cases cluttering up
a test file, we have one test case with different data sets that we are passing
into it. This allows us to change the test case's verification logic in one
place and one place alone. We can also add new test cases by simply adding new
data sets in the `@ddt.data` decorator.

# Works Cited

* [Python unittest Library Documentation](https://docs.python.org/3/library/unittest.html)
* [DDT Documentation](http://ddt.readthedocs.org/)
* [DDT on GitHub](https://github.com/txels/ddt)
