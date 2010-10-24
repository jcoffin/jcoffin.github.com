---
layout: default
title: Cohesion and Coupling&colon; Principles of Orthogonal, Object Orientated Programming
---

# {{ page.title }} 

## Orthogonal Software

In mathematics, orthogonality describes the property two vectors have when they
are perpendicular to each other. Each vector will advance indefinitely into
space, never to intersect. Well designed software is orthogonal. Each of its
components can be altered without effecting other components. Making precise
changes with predictable outcomes is easy. 

Orthogonal design is the union of two principles, cohesion and coupling.

## Cohesion

Cohesion describes the focus of an individual software component. When a
component has only one responsibility, and therefore only one reason to change,
then it has high cohesion. When a component has many responsibilities, and
therefore many reasons to change, then it has low cohesion. Low cohesion is
also noticeable when common responsibilities are spread throughout unrelated
components.

Components with high cohesion are more robust than components with low
cohesion. If an object representing an engine is responsible for both
accelerating and decelerating, then a change to the acceleration implementation
could inadvertently effect the deceleration implementation. If, however, the
engine object is responsible for acceleration and a separate brake object is
responsible for deceleration, then a change to the acceleration implementation
in the engine object is unlikely to effect the deceleration implementation in
the brake object. 

Maintaining highly cohesive components is easy. If all the logic dealing with
deceleration is in the brake object, and that logic is not spread throughout
your system, then you won't have to go hunting around your system whenever you
make changes to the deceleration logic. You only need to look at your break
object.

Orthogonal design requires highly cohesive components. Keep your components
small and focused.

## Coupling

Whereas cohesion is used to describe a single software component, coupling is
used to describe the relationship between components. Coupling is the extent to
which a component of your software depends on other components. 

Loosely coupled components have fewer dependencies than tightly coupled
components. Modifying a tightly coupled component is difficult. If a car
object, boat object, and plane object are all tightly coupled to an engine
object, then a modification to the engine object will necessitate changes to
the car, boat, and plane objects. And if any of those objects are tightly
coupled to other objects, then those other objects will also have to be
changed, and so on and so forth.

Orthogonal software requires software components to be loosely coupled to one
another.  

## Two Simple Examples

Below are two examples of a simple Dictionary written in Ruby. The dictionary
supports reading and writing definitions to a file in either plain text or XML.
We will compare them using the principles of cohesion and coupling to prove the
second example is more orthogonal than the first. 

### Dictionary Example 1
{% highlight ruby %}
def read_dictionary(file)
  if File.extname(file) == ".xml"
    # read and return definitions in XML from file
  else
    # read and return definitions in text from file
  end
end

def write_dictionary(file, definitions)
  if File.extname(file) == ".xml"
    # write definitions to file in XML
  else
    # write definitions to file in text
  end
end

definitions = Hash.new
definitions["autodidact"] = "a person who has learned a subject without a teacher"
definitions["cogent"] = "clear, logical, and convincing"
definitions["pedagogy"] = "the method and practice of teaching"

file = "dictionary.txt"

write_dictionary(file, definitions)
read_dictionary(file)
{% endhighlight %}

The above example is straightforward. We store the definitions in a hash, and
we use two methods `read_dictionary` and `write_dictionary` to manage reading
and writing to the file system in either plain text or XML.

### Dictionary Example 2
{% highlight ruby %}
class Dictionary
  def initialize(file)
    @definitions = Hash.new
    @file = file
  end

  def add_definition(term, definition)
    @definitions[term] = definition
  end

  def self.instance(file)
    if File.extname(file) == ".xml"
      XMLDictionary.new(file)
    else
      TextDictionary.new(file)
    end
  end
end

class XMLDictionary < Dictionary
  def write
    # write XML to @file using the @definitions hash
  end
  def read
    # read XML from @file and populate the @definitions hash
  end
end

class TextDictionary < Dictionary
  def write
    # write text to @file using the @definitions hash
  end
  def read
    # read text from @file and populate the @definitions hash
  end
end

dictionary = Dictionary.instance("dictionary.txt")
dictionary.add_definition("autodidact", "a person who has learned a subject without a teacher")
dictionary.add_definition("cogent", "clear, logical, and convincing")
dictionary.add_definition("pedagogy", "the method and practice of teaching")

dictionary.write
dictionary.read
{% endhighlight %}

The example above is more verbose, but it is also more orthogonal. Lets see why.

## Analysis

Lets begin by looking at these examples using the principle of cohesion. We
learned above that common procedures spread among multiple contexts is
indicative of low cohesion. In Example 1, there are three common tasks that are
spread among multiple contexts. First, and most obvious, the logic determining
file format is repeated in the `read_dictionary` and `write_dictionary`
methods. Second, the XML formatting tasks are spread between the
`read_dictionary` and `write_dictionary` methods. And finally, the plain text
formatting tasks are also spread between the `read_dictionary` and
`write_dictionary` methods. Adding or removing a file format, changing the XML
format, or changing the plain text format, all require changing the
`read_dictionary` and `write_dictionary` methods.

In Example 2, however, the file format logic, the XML formatting logic, and the
plain text formatting logic are each restricted to their own context. The file
format logic is restricted to the Dictionary class method `instance`. The XML
formatting tasks are restricted to the XMLDictionary object. And the plain text
formatting tasks are restricted to the TextDictionary object. The Dictionary
class, the XMLDictionary object, and the TextDictionary object all have higher
functional cohesion than the methods in Example 1. As a result, it is easier to
make changes. We wouldn't have to hunt down multiple contexts when adding a
file format.

Lets now analyze the examples using the principle of coupling. In Example 1,
the two methods `write_dictionary` and `read_dictionary` are coupled together.
Although the two methods don't explicitly depend upon each other, they are
implicitly related via the file format. Any changes to the file format logic
will necessitate changes to both methods. 

In Example 2, however, file format logic is decoupled from read/write
formatting. You can change the file format logic without changing XML or plain
text formatting logic. Lets, for example, assume the requirements have changed.
We want to accept a file name without an extension as plain text. You only need
to change the `Dictionary.instance` class method. You don't need to touch the
`read` or `write` methods of the Dictionary subclasses. In Example 1, you would
have to change the `read_dictionary` method and the `write_dictionary` method. 

Example 2 has also reduced coupling between the controlling code and the model
objects. Notice how the controlling code doesn't concern itself with file
format at all. It doesn't care if it is working with an XMLDictionary object or
a TextDictionary object. We can easily add or remove a file format without
making any changes to the controlling code. 

Notice how highly cohesive components that are loosely coupled to one another
produce an orthogonal system. The second example illustrates the advantage of
orthogonal code. It is understandable and flexible. Keep your components
focused and try to minimize the dependencies between them. 

