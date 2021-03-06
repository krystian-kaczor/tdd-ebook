How to start?
=============

Whenever I sat down with a person that was about to write their first code in a Statement-first manner, the person would first stare at the screen, then at me, then would say: “what now?". It is easy to say: “You know how to write code, you know how to write a unit test for it, just this time start with the latter rather than the first", but for many people, this is something that blocks them completely. If you are one of them, do not worry -- you are not alone. I decided to dedicate this chapter solely to techniques for kicking off a Statement when there is no code.

Start with a good name
----------------------

It may sound obvious, but some people are having serious trouble describing the behavior they expect from their code. If you can name such behavior, it is a great starting point.

I know not everybody pays attention to naming their Statements, mainly because the Statements are considered as tests and second-level citizens -- as long as they run and “prove the code does not contain defects", they are considered sufficient. We will take a look at some examples of bad names and then I will introduce to you some rules of good naming.

### Consequences of bad naming

As I said, many people do not really care how their Statements are named. This is a symptom of treating the Specification as garbage or leftovers -- such situation is dangerous, because as soon as this kind of thinking is established, it leads to bad, unmaintainable Specification that looks more like lumps of accidental code put together in a haste than a living documentation. Imagine that your Specification consists of names like this:

-   `TrySendPacket()`
-   `TrySendPacket2()`
-   `testSendingManyPackets()`
-   `testWrongPacketOrder1()`
-   `testWrongPacketOrder2()`

and try for yourself how difficult it is to answer the following questions:

1.  How do you know what situation each Statement describes?
2.  How do you know whether the Statement describes a single situation,
    or few of them at the same time?
3.  How do you know whether the assertions inside those Statements are
    really the right ones assuming each Statement was written by someone
    else or a long time ago?
4.  How do you know whether the Statement should stay or be removed when
    you modify the functionality it specifies?
5.  If your changes in production code make a Statement evaluate to
    false, how do you know whether the Statement is no longer correct or
    the production code is wrong?
6.  How do you know whether you will not introduce a duplicate Statement
    for a behavior that is already specified by another Statement when
    adding to Specification originally created by another team member?
7.  How do you estimate, by looking at the runner tool report, whether
    the fix for failing Statement will be easy or not?
8.  How do you answer a new developer in your team when they ask you
    “what is this Statement for?"
9.  How can you keep track of the Statements already made about the
    specified class vs those that still need to be made?

### What does a good name contain? 

For the name of the Statement to be of any use, it has to describe the expected behavior. At minimum, it should describe what happens under what circumstances. let's take a look at one of the names Steve freeman and Nat Pryce came up in their great book Growing Object Oriented Software Guided By Tests:

```java
notifiesListenersThatServerIsUnavailableWhenCannotConnectToItsMonitoringPort()
```

Note few things about the name of the Statement:

1.  It describes a behavior of an instance of a specified class. Note that it does not contain method name that triggers the behavior, because what is specified is not a method, but the behavior itself. The Statement name simply tells what an instance does (“notifies listeners that server is unavailable") under certain circumstances (“when cannot connect to its monitoring port"). It is important because such description is what you can derive from thinking about responsibilities of a class, so you do not need to know any of its method signature or the code that is inside of the class. Hence, this is something you can come up with before implementing -- you just need to know why you created this class and build on this knowledge.
2.  The name is relatively long. Really, really, **really** do not worry about it. As long as you are describing a single behavior, it's fine. I know usually people are hesitant to give long names to the Statements, because they try to apply the same rules to those names as to method names in production code (and in production code too long method name can be a sign of it having too many responsibilities). Let me make it clear -- these two cases are different. In case of Statements, methods are not invoked by anyone besides the automatic runner applications, so they will not obfuscate any code that would need to call them with their long names. Sure, we could put all the information in the comment instead of Statement name and leave the name short, like this:

    ```csharp
    [Fact]
    //Notifies listeners that server 
    //is unavailable when cannot connect
    //to its monitoring port
    public void Statement_002()
    {
      //...
    }
    ```
    
    There are two downsides to this solution: one is that we now have to
    put extra information (`Statement_002`) which is required only by
    compiler, because every method needs to have a name -- there is
    usually no value a human could derive from such a name. The second
    downside is that when the Statement is evaluated to false, the
    automated runner shows you the following line:
    `Statement_002: FAILED` -- note that all the information included in
    the comment isn’t present in the failure report. It is really better
    to receive a report such as:

    `notifiesListenersThatServerIsUnavailableWhenCannotConnectToItsMonitoringPort: FAILED`

    because then a lot of information about the Statement that fails is
    available from the runner window.

3.  Using a name that describes a (single) behavior allows you to track
    quickly why the Statement is false when it is. Suppose a Statement
    is true when you start refactoring, but at one point it starts being
    evaluated as false and the report in the runner looks like this:
    `TrySendingHttpRequest: FAILED` -- it does not really tell you
    anything more than that an attempt is made to send a HTTP request,
    but, for instance, does not tell you whether your specified object
    is the sender (that should try to send this request under some
    circumstances) or the receiver (that should handle such request
    properly). To actually know what went wrong, you have to go
    Statement body and scan its source code. Now compare it to the
    following name:
    `ShouldRespondWithAnAckWheneverItReceivesAHttpRequest`. Now when it
    evaluates to false, you can tell what is broken -- the object no
    longer responds with an ACK to HTTP request. Sometimes this is
    enough to identify which part of the code is in fault of this
    evaluation failure.

### My favourite convention

There are many conventions for naming Statements appropriately. My
favorite is the one developed by Dan North, which makes each Statement
name begin with the word `Should`. So for example, I would name
a Statement:

`ShouldReportAllErrorsSortedAlphabeticallyWhenErrorsOccurDuringSearch()`

The name of the Specification (i.e. class name) answers the question
“who should do it?", i.e. when I have a class named `SortingOperation`
and want to say that it “should sort all items in ascending order when
performed", I say it like this:

```csharp
public class SortingOperationSpecification
{
 [Fact] public void
 ShouldSortAllItemsInAscendingOrderWhenPerformed()
 {
 }
}
```

The “should" word was introduced by Dan to weaken the statement
following it and thus, to allow questioning what you are stating and ask
yourself the question: “should it really?". If this causes uncertainty,
then it is high time to talk to a domain expert and make sure you
understand well what you need to accomplish. If you are not a native
English speaker, the “should" prefix will probably have a weaker
influence on you -- that is why I don't insist on you using it. I like
it though.

When inventing a name, It is important to put the main focus on what
result or action is expected from an object. If you do not, you quickly
find it troublesome. As an example, one of my colleagues was specifying
a class `UserId` and wrote the following name for the Statement about
comparison of two identifiers:

`EqualOperationShouldPassForTwoInstancesWithTheSameUserName()`.

Note that this is not from the perspective of a single object, but
rather from the perspective of an operation that is executed on it,
which means that we stopped thinking in terms of object responsibilities
and started thinking in terms of operation correctness, which is farther
away from our assumption that we are writing a Specification consisting
of Statements. This name should be something more like:

`ShouldReportThatItIsEqualToAnotherIdThatHasTheSameUserName()`.

In general, when I find myself having trouble with naming like this, I am suspicious of the following things possibly happening:

1.  I am not specifying a behavior class, but rather an outcome of a method
2.  I am specifying more than one behavior
3.  The behavior is too complicated and hence I need to change my design (more on this later)
4.  I am naming the behavior on too low level of abstraction, putting too many details in the name - my personal rule is that I can only come to this conclusion when all the previous points fail me.

### But can't the name really get too long?

Few paragraphs ago, I told you not to worry about a length of Statement names. But I have to admit that there are times when the name really gets too long. A rule I try to follow is that a name of a Statement should be more readable than its content. Thus, if it takes me less time to understand the point of a Statement by reading its body than by reading its name, then the name is too long. In such case, I try to apply the heuristics described above to find and fix the root cause of the problem. 

Start by filling the GIVEN-WHEN-THEN structure with the obvious
----------------------------------------------------------------

This is a technique that is applicable when you come up with
a GIVEN-WHEN-THEN structure for the Statement or a good name for it
(GIVEN-WHEN-THEN structure can be easily derived from a good name and
vice versa). Anyway, this technique is about taking the GIVEN, WHEN and
THEN parts and translating them into code in almost literal, brute-force
way, then add all the missing pieces that are required for the code to
compile and run.

### Example

let's try this on a simple problem of comparing two users. We assume that
a user should be equal to another when it has the same name as the other
one:

```gherkin
Given a user with any name
When I compare it to another user with the same name
Then it should appear equal to this other user
```

Let's start with the translation

The first line:

```gherkin
Given a user with any name
```

can be translated literally to code like this:

```csharp
var user = new User(anyName);
```

Then the second line:

```gherkin
When I compare it to another user with the same name
```

can be written as:

```csharp
user.Equals(anotherUserWithTheSameName);
```

Great! Now the last line:

```gherkin
Then it should appear equal to this other user
```

and its translation into the code:

```csharp 
Assert.True(areUsersEqual);
```

Ok, so we have made the translation, now let's summarize this and see
what is missing to make this code compile:

```csharp
[Fact] public void 
ShouldAppearEqualToAnotherUserWithTheSameName()
{
  //GIVEN
  var user = new User(anyName);

  //WHEN
  user.Equals(anotherUserWithTheSameName);

  //THEN
  Assert.True(areUsersEqual);
}
```

As we expected, this will not compile. Notably, our compiler might point
us towards the following gaps:

1.  A declaration of `anyName` variable.
2.  A declaration of `anotherUserWithTheSameName` object.
3.  The variable `areUsersEqual` is both not declared and it does not
    hold the comparison result.
4.  If this is our first Statement, we might not even have the `User`
    class defined at all.

The compiler created a kind of a small TODO list for us, which is nice.
Note that, while we do not have full compiling code, filling the gaps
boils down to making a few trivial declarations and assignments:

1.  `anyName` can be declared as

    `var anyName = Any.String();`

2.  `anotherUserWithTheSameName` can be declared as

    `var anotherUserWithTheSameName = new User(anyName);`

3.  `areUsersEqual` can be declared and assigned this way:

    `var areUsersEqual = user.Equals(anotherUserWithTheSameName);`

4.  If `User` class does not exist, we can add it by simply stating:

    `public class User {}`

Putting it all together:

```csharp
[Fact] public void 
ShouldAppearEqualToAnotherUserWithTheSameName()
{
  //GIVEN
  var anyName = Any.String();
  var user = new User(anyName);
  var anotherUserWithTheSameName = new User(anyName);

  //WHEN
  var areUsersEqual = user.Equals(anotherUserWithTheSameName);

  //THEN
  Assert.True(areUsersEqual);
}
```

And that's it -- the Statement is complete!

Start from the end
------------------

This is a technique that I suggest to people that seem to have
absolutely no idea how to start. I got it from Kent Beck’s book Test
Driven Development by Example. It seems funny at start, but is quite
powerful. The trick is to write the Statement ‘backwards’, i.e. starting
with what the Statement asserts to be true (in terms of the
GIVEN-WHEN-THEN structure, we would say that we start with our THEN).

This works because, while we are many times quite sure of our goal (i.e.
what the outcome of the behavior should be), but are unsure of how to
get there.

### Example

Imagine we are writing a class for granting access to a reporting
functionality based on roles. We do not have any idea what the API
should look like and how to write our Statement, but we know one thing:
in our domain the access can be either granted or denied. Let's take
the successful case (just because it is the first one we can think of)
and, starting backwards, start with the following assertion:

```csharp
//THEN
Assert.True(accessGranted);
```

Ok, that part was easy, but did we make any progress with that? Of
course we did -- we now have a non-compiling code and the compilation
error is because of the `accessGranted` variable. Now, in contrast to
the previous approach (with translating our GIVEN-WHEN-THEN structure
into a Statement), our goal is not to make this compile as soon as
possible. The goal is to answer ourselves a question: how do I know
whether the access is granted or not? The answer: it is the result of
authorization of the allowed role. Ok, so let's just write it down,
ignoring everything that stands in our way (I know that most of us have
a habit to add a class or a variable as soon as we find out that we need
it. If you are like that, then please turn off this habit while writing
Statements -- it will only throw you off the track and steal your focus
from what is important. The key to doing TDD successfully is to learn to
use something that does not exist yet like it existed):

```csharp
//WHEN
var accessGranted 
 = authorization.IsAccessToReportingGrantedTo(
  roleAllowedToUseReporting);
```

Note that we do not know what `roleAllowedToUseReporting` is, neither do
we know what is `authorization`, but that did not stop us from writing
this line. Also, the `IsAccessToReportingGrantedTo()` method is just
taken from the top of our head. It is not defined anywhere, it just made
sense to write it like this, because it is the most direct translation
of what we had in mind.

Anyway, this new line answers the question on where do we take the
`accessGranted` from, but makes us ask further questions:

1.  Where does the `authorization` variable come from?
2.  Where does the `roleAllowedToUseReporting` variable come from?

As for `authorization`, we do not have anything specific to say about it
other than that it is an object of a class that we do not have yet. In
order to proceed, let's pretend that we have such a class. How do we
call it? The instance name is `authorization`, so it is quite
straightforward to name the class `Authorization` and instantiate it in
the simplest way we can think of:

```csharp
//GIVEN
var authorization = new Authorization();
```

Now for the `roleAllowedToUseReporting`. The first question that comes
to mind when looking at this is: which roles are allowed to use
reporting? Let's assume that in our domain, this is either an
Administrator or an Auditor. Thus, we know what is going to be the value
of this variable. As for the type, there are various ways we can model
a role, but the most obvious one for a type that has few possible values
is an enum. So:

```csharp
//GIVEN
var roleAllowedToUseReporting = Any.Of(Roles.Admin, Roles.Auditor);
```

And so, working our way backwards, we have arrived at the final solution
(we still need to give this Statement a name, but, provided what we
already know, this is an easy task):

```csharp
[Fact] public void
ShouldAllowAccessToReportingWhenAskedForEitherAdministratorOrAuditor()
{
 //GIVEN
 var roleAllowedToUseReporting = Any.Of(Roles.Admin, Roles.Auditor);
 var authorization = new Authorization();

 //WHEN
 var accessGranted = authorization
  .IsAccessToReportingGrantedTo(roleAllowedToUseReporting);

 //THEN
 Assert.True(accessGranted);
}
```

Start by invoking a method when you have one
--------------------------------------------

Sometimes, we have to add a new class that implements an existing
interface required by another class. The fact of implementing an
interface imposes what methods should the new class support. If this
point is already decided, we can start our Statement by first calling
the method and then discovering what we need to supply.

### Example

Suppose we have an application holding a lot of data that, among other
things, handles importing am existing database from another instance of
the application. As importing a database can be a lengthy process,
a message box is displayed each time when user chooses to perform the
import and this message box displays the following message: “Johnny,
please sit down and enjoy your coffee for a few minutes as we take time
to import your database" (given user name is Johnny). The class that
implements it looks like this:

```csharp
public class FriendlyMessages
{
  public string 
  HoldOnASecondWhileWeImportYourDatabase(string userName)
  {
    return string.Format("{0}, "
      + "please sit down and enjoy your coffee "
      + "for a few minutes as we take time "
      + "to import your database",
      userName);
  }
}
```

Now, imagine that our management told us that they need to ship a trial
version with some features disabled, including importing an existing
database. Among all the things we need to do to make it happen, we also
need to display a different string with message saying that this is
a trial version and the feature is locked. We can do it by extracting an
interface from the `FriendlyMessages` class and using it to put in an
instance of another class implementing this interface when the
application discovers that it is being run as a trial version. The
extracted interface looks like this:

```csharp
public interface Messages
{
  string HoldOnASecondWhileWeImportYourDatabase(string userName);
}
```

So our new implementation is forced to support the
`HoldOnASecondWhileWeImportYourDatabase` method. Thus, we when
implementing the class, we start with the following:

```csharp
public class TrialVersionMessages : Messages
{
 public string HoldOnASecondWhileWeImportYourDatabase(string userName)
 {
   throw new NotImplementedException();
 }
}
```

Now, we are ready to start writing a Statement. Assuming we do not know
where to start, we just start with creating an object and invoking the
method that needs to be implemented:

```csharp
[Fact] 
public void TODO()
{
 //GIVEN
 var trialMessages = new TrialVersionMessages();
 
 //WHEN
 trialMessages.HoldOnASecondWhileWeImportYourDatabase();

 //THEN
 Assert.True(false); //to remember about it
}
```

As you can see, we added an assertion that always fails at the end
because we do not have any assertions yet and the Statement would
otherwise be already evaluated as true and we would rather have it
remind ourselves that it is not finished. Other than this, the Statement
does not compile anyway, because the method
`HoldOnASecondWhileWeImportYourDatabase` takes a string argument and we
passed none. This makes us ask the question what is this argument and
what is its role in the behavior triggered by the
`HoldOnASecondWhileWeImportYourDatabase` method. Seems like it is a user
name and we want it to be somewhere in the result of the method. Thus,
we can add it to the Statement like this:

```csharp
[Fact] 
public void TODO()
{
 //GIVEN
 var trialMessages = new TrialVersionMessages();
 var userName = Any.String();
 
 //WHEN
 trialMessages.
  HoldOnASecondWhileWeImportYourDatabase(userName);

 //THEN
 Assert.True(false); //to remember about it
}
```

Now, this compiles but is evaluated as false because of the guard
assertion that we put at the end. Our goal is to substitute it with
a real assertion for a real expected result. The return value of the
`HoldOnASecondWhileWeImportYourDatabase` is a string message, so all we
need to do is to come up with the message that we expect in case of
trial version:

```csharp
[Fact] 
public void TODO()
{
 //GIVEN
 var trialMessages = new TrialVersionMessages();
 var userName = Any.String();
 
 //WHEN
 var message = trialMessages.
  HoldOnASecondWhileWeImportYourDatabase(userName);

 //THEN
 var expectedMessage = 
  string.Format("{0}, better get some pocket money!", userName);

 Assert.Equal(expectedMessage, message);
}
```

and all that is left is to find a good name for the Statement. This
isn’t an issue since we already specified the desired behavior in the
code, so we can just summarize it as something like
`ShouldYieldAMessageSayingThatFeatureIsLockedWhenAskedForImportDatabaseMessage`.

Summary
-------

These few techniques can be useful when you are stuck and do not know
how to start. Note that the examples given are simplistic and assume
that there is one object that takes some kind of input parameter and
returns a well defined result. This is not how most of the
object-oriented world is built. In object oriented world, we have a lot
of objects communicating with other objects, sending messages, invoking
methods on each other and often having no return values. Do not worry,
all of these techniques work in this concept and we’ll be revisiting
them as soon as we learn how to do TDD in fully object-oriented world
(that is, after we introduce a concept of mock objects). For now, I’m
trying to keep it simple.
