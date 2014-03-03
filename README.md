# Coupling And Cohesion: Overview

Most of the ideas discussed in this essay come from Kent Beck's 2009 article *Coupling and
Cohesion* (http://www.threeriversinstitute.org/blog/?p=104), as well as Robert Martin's 2005
compendium *Principles of Object Oriented Development*
(http://butunclebob.com/ArticleS.UncleBob.PrinciplesOfOod).  I probably first read both of
these writings around 2010.  The notions of complexity they introduced were immediately
intuitive and applealing to me, and I began to see their relevance to my own work right
away, but it took several more years of professional work before their usefulness became
really clear.  This essay is not the final word on anything, only my attempt to clarify some
of my own recent thinking on the subject.  I hope others find it useful to think about these
things with me.

# Definitions

**Coupling** and **cohesion** are phenomena that occur within a software system.  When
measured and considered together, they can be used to answer questions about the complexity
of the system.  That is, they can tell us something about the ease or difficulty of a)
understanding how a system works and b) modifying the system's behavior.

Coupling and cohesion are defined in terms of a *system* and its *components*.  These terms
are deliberately chosen to be ambiguous and abstract, because the concepts are abstract.
They each manifest naturally, in some form, within any kind of system we might care to
consider.  For example, a class is a system composed of methods; a library is a system
composed of classes; an application is a system composed of libraries (and other loose
classes, usually); a federation is a system composed of applications; etc. etc. etc.  The
sort of reasoning that can be done about the complexity of one of those examples is not
different from any other one.

**Coupling:** two components of a system are said to be *coupled* to the extent that one
depends on the other.  Put more simply, good coupling means that our codebase is clean and
the dependencies are easy to understand.  Bad coupling means that our codebase is an
entangled mess, and the dependencies are hard to understand.  Examples:

- If the system in question were an application written in an object-oriented language, and
  the components were the classes of that application, coupling might be measured in terms
  of the number of places where methods of one call call methods of another.  Fewer call
  sites is better, from a complexity standpoint.

- For a system made up of several applications communicating with each other's APIs (via
  HTTP or whatever).  Coupling here might be measured in terms of the number of different
  endpoints of each application that are invoked by each other application.  Again, fewer
  endpoints is better, from a complexity standpoint.

Once we start looking at different kinds of systems and the way they manifest internal
coupling, we find that the variations of this pattern are endless, and it's always the case
that if we can reduce the coupling between a system's components, we can reduce the overall
complexity of the system accordingly.

**Cohesion:** a component of a system is said to be *cohesive* to the extent that it is
stable in the face of changes made to components that it is coupled to.  Put more simply,
it's a measure of how well the contents of the component "hang together", and whether the
component has a singularity of purpose.  Good cohesion means that a component has few
responsibilities (it does one thing well) and a simple interface, and bad cohesion means
that it has many responsibilities (it tries to do many things) and/or a complex
interface. Examples:

 - *TODO*


# Coupling and Cohesion in Practice

In practice, measures of coupling and cohesion are always interdependent.  That is, changing
one variable always changes the other, and in fact it impossible to optimize one without
degrading the other.  For example, modifying a system to improve the cohesion of its
components is a good thing to do, but it generally results in having smaller components than
we had before and therefore more of them, and adding more components to a system tends to
increase its overall coupling.  Conversely, if we try to improve (reduce) the coupling of a
system by merging small components together into larger ones, thereby reducing the number of
dependencies between components, the new components will necessarily have to have more
responsibilties than the previous ones, and thus poorer cohesion.

Note that it is never possible to actually have perfect decoupling within a system, because
that is only achieved when none of the components interact with each other, and that's not
really a system, just an inert collection of components.  Coupling is to a certain extent a
good thing and a completely necessary thing, and in fact it's the thing that enables the
functionality of the system.  It's only when it gets out of hand and begins to hamper
productivity that it becomes a problem.

In the case of cohesion, on the other hand, it *is* possible to achieve perfection.  For
example, a class that contains just a single method is a perfectly cohesive component of its
system.  However, we're not going to want to take things to that extreme in practice,
because of the surge in coupling it would incur (a system made up of a large number of tiny
components will tend to manifest a great amount of coupling among them).

In a sense, coupling is a manifestation of what a system *does*, and cohesion is a
manifestation of what the system *doesn't do*. [Does this sound too cosmic?? there is a
metaphor i want to employ here, something about figure vs. ground or yin vs. yang]. Together
they embody functionality, but by the same token they also embody complexity.


# Getting Concrete: An Object-Oriented Application

In order to have a deeper exploration of these concepts, let's focus on a concrete setting
and see how the forces play out there.  Imagine an application written in an object-oriented
language, i.e. one where the code is written in terms of classes and methods.  The language
doesn't matter, and the specifics of the application don't matter.  The point is that we
have a setting where there is a system (the application), and the system has components (the
classes that make up the application).  Given this setting, we can think about coupling in
terms of the way the methods of one class invoke the methods of another, and we can think
about cohesion in terms of the number of responsiblities each class serves within the
application.

In this example system, coupling is a little bit easier to think about than cohesion.  We
can measure it in simple terms: given class A and class B, how many different methods of B
are invoked by methods of A (or vice-versa)?  Additionally, for each method of B (or A) that
gets called, how many different variants does that method have? (For example, if a method
allows the caller to pass some optional arguments that might change its behavior, how many
different options are there?)

*[TODO: Note that all we care about is the number of different ways A can call into B.  We
don't care at all at about how many times a given method might get called at runtime.  That
would be relevant when doing performance analysis, but it's not germane to a discussion of
the complexity of the code.  Likewise, when a method offers several optional behaviors, it's
irrelevant to ask whether some of those options might not really be used at runtime; we have
to consider the code in its static form, and always account for the _potential_ complexity,
whether or not that complexity ever really occurs when the code is deployed. Not sure if
this is the right place to explain this, though.]*

As for cohesion, it's harder to measure quantitatively, but there are some useful ways to
think about it.
- When we invoke one method of a given class, how many other of its methods get invoked?
  (Ideally, all of them.)
- If one of the callers of a given class is removed from the system, how many methods within
  the given class become unreachable? (Ideally, none of them.)
- When we run the tests for the class, for each method of its public interface, how many
  combinations of preconditions and postconditions are there? (Ideally, exactly one per
  method.)

[With these notions in hand yadda yadda]

# Getting More Concrete: A Simple Blog Application, in Rails

Step 1: We build a simple Rails app with Users, Posts and Comments, and the bare minimum in
terms of controller endpoints.  Coupling is naturally low, cohesion is naturally high.

Step 2: We add some new features at the controller level (dispatching emails and posting
facebook items in response to certain events).  Coupling remains reasonably low, cohesion
starts to take a hit at the controller layer.

Step 3: Things get worse when we add new controller endpoints with slightly different
behaviors; one that renders a minimal layout for mobile browsers, and one that renders JSON
for an API. Cohesion stays steadily mediocre within each controller, but due to code
duplication, total coupling between the controllers and other components in the system
starts to increase in whole-number multiples.

Step 4: We attempt to fix the problem by pushing behavior down into the models. Sure, that
sounds like a good idea! The code will be DRY!  The coupling between each controller and the
rest of the system returns to its previous tolerable level, but now the cohesion of the
model layer has taken a nosedive.

Step 5: Refactor from the previous step to extract a service class that coordinates the
interaction between the controllers and the rest of the system.  Overall coupling within the
system increases slightly, because anytime we add a new component, it brings with it new
dependencies.  However, the cohesion of each component is quite high, and in the overall
trade-off, this system has less complexity than either of the ones we had after steps 3 and
4.  (Note: a "service" in the sense we use it here doesn't have to be anything in
particular.  Basically, any class that is pure "business logic" and isn't a controller or
model could be considered a service.)


