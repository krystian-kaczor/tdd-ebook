It is not a test
================

Up to now, I was cheating on you. I told you that the little executable
snippets of code are ‘tests’ and that they’re here to ‘check’ or
‘verify’ something. Now it is time to reveal the truth.

When a test becomes something else 
----------------------------------

I studied in Łódź, a large city in the center of Poland. As probably all
other students in all other countries, we have had lectures, exercises
and exams. The exams were pretty hard, especially considered that my
computer science group was on the faculty of electronic and electric
engineering, so we had to grasp a lot of classes that did not have
anything to do with programming, for instance electrotechnics,
solid-state physics or electronic and electrical metrology.

Knowing that the exams were difficult and that it was hard to learn
everything during preparation, the lecturers would give us exemplary
exams from previous years. The questions were different than during
actual exams, but the structure and types of questions asked (practice
vs. theory etc.) was similar. We would usually get these exemplary
questions before we started learning really hard (which was usually at
the end of semester). Guess what happened then? As you might suspect, we
did not use the tests we received just to ‘verify’ or ‘check’ our
knowledge after we finished learning. Those tests were actually the
first thing we went to before even starting to learn. Why was that so?
What use were the tests when we knew we would not know most of the
answers?

I guess my lecturers would disagree with me, but I find it quite amusing
that what we were really doing back then was ‘Lean’. Lean is an approach
where, among others, there is a rigorous emphasis on eliminating waste.
Every feature or product that is produced while not being needed by
anyone is considered waste. That is because if something is not needed,
there is no reason to assume it will ever be needed. In such case the
entire feature or product is a waste -- it has no value. Even if it WILL
be ever needed, it will require some rework anyway to fit the real
customer needs. In this case, the initial amount of work that went into
the parts of solution that had to be replaced by another parts anyway is
a waste -- it never brought any money (I am not talking about such things
as customer demos, but finished, polished features or products).

So, in order to eliminate waste, there is huge pressure nowadays to
“pull features from demand" instead of “pushing them" into the product.
In other words, every feature is there to satisfy concrete need. If not,
the effort is considered wasted and the money drown.

Going back to the exams, why the approach of first looking through the exemplary tests can be considered ‘lean’? That is because, when we treat passing an exam as our goal, then everything that does not put us closer to this goal is considered a waste. Let's suppose the exam is theory only -- why then practice the exercises? It would probably pay off a lot more to study theoretical side of the topics. Such knowledge could be obtained from those exemplary tests. So, the tests were a kind of specification of what was needed to pass the exam, letting us pull the value (i.e. our knowledge) from demand (information obtained from a realistic tests) rather that pushing it from implementation (i.e. learning everything in a course book chapter after chapter).

So the tests became something else. They proved very valuable before the
‘implementation’ (i.e. learning for the exam) because:

1.  they helped us focus on what was needed to reach our goal
2.  they brought our attention away from what was **not** needed to
    reach our goal

That was the value of a test before learning. Note that the tests we
would usually receive were not exactly what we would encounter at the
time of exam, so we still had to guess. Still, the role of a **test as
specification of a need** was already visible.

Taking it to the software development land
------------------------------------------

I chose this lengthy metaphor to show you that ‘test’ is really another way of specifying a requirement or a need and that it is not something that is counter-intuitive -- it occurs in our everyday lives. This is also true in software development. Let's take the following ‘test’ and see what kind of needs it specifies:

```csharp
var reporting = new ReportingFeature();
var anyPowerUser = Any.Of(Users.Admin, Users.Auditor);
Assert.True(reporting.CanBePerformedBy(anyPowerUser));
```

(In this example, we used `Any.Of()` method that returns any enumeration
value from the specified list. Here, we say “give me a value that is
either `Users.Admin` or `Users.Auditor`“.)

Let's look at those (only!) three lines of code, imagining that the production code that makes this ‘test’ pass does not exist yet. What can we learn from these three lines about what the code needs to supply? Count with me:

1.  We need a reporting feature
2.  We need to support a notion of users and privileges
3.  We need to support a domain concept of power user, who is either an
    administrator or an auditor
4.  Power users will need to be privileged to use the reporting feature
    (note that it does not specify which other users should or should
    not be able to use this feature -- we would need a separate ‘test’
    for that).

Also, are already after the phase of designing an API that will fulfill
the need. Don’t you think it is pretty much information about the
application from just three lines of code?

A Specification instead of a Test Suite
---------------------------------------

I hope that you can see now that what we called ‘a test’ is really a kind of specification. The discovery is very recent, so there isn’t a clear terminology on it yet. Some like to call the process of using tests as specifications: Specification By Example, to say that the tests are really examples that help specify and clarify the behavior of developed part of functionality. The terminology is still not rock-solid, so you might encounter different naming for different things. For example, a ‘test’ is often referred to as ‘spec’, or an ‘example’, or a ‘behavior description’, or a ‘specification statement’
or a ‘fact about the developed system’ (the xUnit.NET framework marks each ‘test’ with a `[Fact]` attribute, suggesting that by writing it, we are stating a single fact about developed code. By the way, xUnit.NET also allows us to state ‘theories’ about our code, but let's leave it for now).

The time has come to make a deal: I will establish some naming conventions for this book to be consistent, but leave you with the freedom to follow your own if you so desire. The terminology shift is for pedagogical reasons -- I am not trying to create a movement to change established terms or to invent a new methodology or anything -- my hope is that using this terminology throughout the book will let you look differently at some things[^opensourcebook]. So, let's agree that for the sake of this book:

**Specification Statement** (or simply **Statement**, starting with capital letter)

:   will be used instead of ‘test’ or ‘test method’

**Specification** (or simply **Spec**), also starting with capital letter

:   will be used instead of ‘test suite’ or ‘test list’

**False Statement**

:   will be used instead of ‘failing test’

**True Statement**

:   will be used instead of ‘passing test’

From time to time, I will refer back to the ‘traditional’ terminology,
because it is better established and you’ve probably heard some terms
already and may wonder how it should be understood in context of
thinking of tests as specification.

From your experience, you may know paper or word specifications, written
in plain English or other spoken language. Our specification is
different than these specifications in at least few ways:

1.  it is not written fully up-front (more on this in the next
    chapters).
2.  it is executable -- you can run it to see whether the code adheres to
    the specification or not.
3.  it is written in source code rather than in spoken language -- which
    is both good (less room for misunderstanding -- source code is the
    most formal and structured way of writing specifications) and bad
    (great care must be taken to keep the specification readable).

[^opensourcebook]: besides, this book is open source, so if you don't like it, you are free to create a fork and change the terminology!