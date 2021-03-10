---
layout: default
---
### pythings: class attributes
##### 2019-01-26

These days I have encountered some mysterious bugs while interacting with class attributes, and
I thought it's worthwhile to look further into it. Let's start with some background on classes:

>
> A class contains data field descriptions (or properties, fields, data members, or attributes). 
> These are usually field types and names that will be associated with state variables at program run time; 
> these state variables either belong to the __class__ or specific instances of the class.
>
> -- [Class (computer programming)](https://en.wikipedia.org/wiki/Class_(computer_programming)#Structure), Wikipedia
> 

So a _class attribute_ is essentially an attribute belonging to a class. This means any changes
made to the class attribute should be reflected in instances of the class. For example, `operating_system` 
would be the class attribute in the following class:

```py
class Minion:
    operating_system = 'RHEL'

>> # create two instances of Minion
>> m1 = Minion()
>> m2 = Minion()
>>
>> # reassign class attribute
>> Minion.operating_system = 'Centos'
>>
>> m1.operating_system
>> 'Centos'
>> m2.operating_system 
>> 'Centos'
```
Here change to the class attribute is propagated on its instances. But in Python, we can actually
reassign the class attribute from the specific instance:
```py
>> m1.operating_system = 'Centos'
>>
>> m1.operating_system
>> 'Centos'
>> m2.operating_system
>> 'RHEL'
```
Notice how m2's value has not changed. Well this is because when a class attribute is assigned
by an instance, Python will add it to its instance [namespace](https://docs.python.org/2.7/tutorial/classes.html#python-scopes-and-namespaces) 
which "overrides" its value from the class namespace. 
However, m2 is still accessing the attribute from the class namespace because such value is missing in its instance namespace. 

This gets more interesting with _mutable_ objects. Suppose we wish to keep track of the priority
of images on the minion:

```py
class Minion:
    images = {}
    
    def add_os_image(self, os, priority):
        self.images[os] = priority

>> # create two instances of Minion
>> m1 = Minion()
>> m2 = Minion()
>>
>> m1.add_os_image('Centos': 3)
>> 
>> m1.images
>> {'Centos': 3}
>> m2.images
>> {'Centos': 3}
>> m2.add_os_image('RHEL': 1)
>> 
>> m3 = Minion()
>> m3.images
>> {'Centos': 3, 'RHEL': 1}
``` 
What's happened is that class attribute changes (made from an instance) is propagated to _ALL_ instances of the class!

Well this is because during the assignment of the class attribute, the attribute is passed in by reference
to the instance namespace so all attributes are effectively referencing the same location in memory. 
Better to use an instance attribute here instead. 

Does that mean mutable class attributes should never be used? IMHO there could be situations where
 one wishes to maintain information collected from class instances. For example:
tracking all os images ever created on minion instances to monitor duplicates or just for bookkeeping purposes.  

* * *
[Markup reference](./another-page.html)


