+++
title = "Clean code's problem isn't about performance"
date = 2026-06-02
tags = ["enterprise software", "frameworks", "development", "quality", "fungibility"]
+++
Lately, I've joined a medium sized SaaS company and started contributing to their monolithic Java Springboot Application. It's honestly not a bad project and I've been able to get some new learnings related to how monolithic enterprise SaaS applications are built up. As I've been learning though, I've started to notice some trends and it's been an eye openign experience coming from my "systems design" experience mostly related to embedded, game and containerized microservice development using languages like C, C#, Go and Rust.

Realistically, there's nothing intrinsically _wrong_ with Spring, but as a relative newcomer to Spring Enterprise software and solving some of the more awkward problems for our team (such as Spring authentication breaking between version upgrades) or running into certain legacy foot guns like Open Session In View, the fact that these were actually problems felt a bit baffling to me as the solutions to something like open session in view in SQL is.... kind of difficult? The main reason being that the way SQL oriented applications want you to architect your code keeps you aware of important details like connection lifetimes and connection pooling.

Contrary to the more "straight-forward" systems design langauges like C, Go, Rust and Zig, Spring takes Java (a relatively straight-forward language) and, in an effort to make things "simple" moves much of the complexity of the application behind arcane tags and @at-tributes. This magical `@`ttribute oriented design is intended to hide the complexity of mundane details like connection pooling with declarative models. Want to make a value a database table? _Slap on the_ `@Entity`! How about adding associations? Just put your `@SomethingToSomething` relationship attribute on there! What about making sure that your function is transactional? Here's `@transactional` to save the day!

Unfortunately, as a newbie, it leads me to ask the question "So wait, how does this work?" _waaaaaaaaaaaaaaaay_ more than any other language I've worked in. It also sends me for the documentation where before I'd usually just kind of understand it via the code itself. How does the `@Transactional` attribute work? What is a proxy? Why is the stack trace 200 between my code and the side effect when 190 of them Something Proxyu bean? Your guess is as good as mine! Better go to the docs! Oh wait. Spring rivals _microslop_ for quality in the documentation (note, if you've never had to read `learn.microsoft.com` you are lucky).

Additionally, my long standing knowledge of SQL kind of comes grinding up against this ecosystem whenever I run into a problem. In my brain, every database problem is just a `SELECT thing FROM thang JOIN ON otherThing WHERE condition ORDER BY X LIMIT BY Y` and you run it in your sql querier of choice against a dummy database and adjust until it looks good. Problem solved.

But you know, SQL is for nerds and we don't have time for learning that. We've got features to ship and disruption to cause. Hibernate gets rid of all that _nerdy junk_ and let's us just think about objects and their relations then we can just shove it all in something that extends `JpaRepositoryBean` and hibernate will do all the nerdy homework that's for suckers! Back to shipping features, daily scrums, story points and far too much jargon to shake a stick at.

Using JPA/hibernate vs SQL as an example, consider the following hypothetical java class

```java
// Foo.java
@Entity
public class Foo {
    @Id
    @GeneratedValue
    private int id;

    @OneToOne(mappedBy = "foo", fetch = FetchType.LAZY)
    private Bar bar;
}

// Bar.java
@Entity
public class Bar {
    @Id
    @GeneratedValue
    private int id;

    @OneToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "foo_id")
    private Foo foo;
}
```

These are simple classes with a one to one two way relationship. This is kind of _more or less_ how you would want to structure things with Spring's ORMs for object modeling purposes. If you have a Bar, you want to be able to get it's foo, and vice versa. It seems sensible on the surface. Also, for performance, you would also be prone to thinking "Hey, let's lazy load, no sense in loading both unless we need to access it, right?

At first it all seems like magic, because you just define your interfaces, let Hibernate/JPA write all that pesky SQL for you and -- as some might say -- Bob's your uncle. That is... until it hits production and you start to learn that _lazy is a lie_ in this case.

That's because your expectations of hybernate are too high.

```sql
-- What you probably expected it would write if you needed it this way. 
SELECT f.id, b.id FROM Foo f JOIN Bar b ON f.bar_id = b.id;

-- What Hibernate generates instead with the bidirectional mapping:
SELECT f.id, f.bar_id FROM Foo f;
SELECT b.id FROM Bar b WHERE b.foo_id = ?;  -- repeated for every @OnetoOne relationship
```

In the code base that I'm onboarding into, one of core objects had _five_ such relations, which means that every query to our Entity _emitted 6 queries_ when a single join + coerce would have sufficed.

Further more, something simple like:

```sql
-- It should be this
SELECT a.id, b.id, c.id, d.id FROM A a
JOIN B b ON a.b_id = b.id
JOIN C c ON a.c_id = c.id
JOIN D d ON a.d_id = d.id
ORDER BY b.id;
```

Has to go through their awkward SQL, but not `Specification`, `CriteriaBuilder` and `SpecificationQuery` (I might be misremembering the exact class) with such excessively wordy and untestable java code that it makes me wonder _what's the freaking point_? At this point, learning postgres's dialetc of SQL is not only more easy and portable, _but easier_ than learning Spring's madness of classes!!!

Needless to say, I keep asking myself _why_? Why are there so many foot guns in this ecosystem? Why are there so many hyper specific edge cases? Why is it that

- Hibernate makes is so easy to write two way `@OneToOne`  bindings so lazy loading causes additional invisible selects (even though you were thinking you were safe by selecting lazy)?
- That navigating even the simplest codebase is not only requires, but is _encouraged_ to create a dozen abstraction layers so you wear out your "Go to defintion" motion (or hotkey if you aren't a vim motions user)
- Stuck perpetually reading through awful barely documented Spring code that basically always sends you to the depth of stack overflow in search of writings from other lost and weary souls searching for the isolated hermits that can elucidate us of the mysteries of the code.

To be honest, I actually have more of an idea now. And it kind of comes back to the title. Some odd years ago, there was a bit of a debate going around about Bob Martin's "Clean Code" philosophy. Many things like how much of it is built around the ideas of dynamic dispatch, short functions, creating recipes that work towards the tag line of "In order to go fast, you have to go well". And this spawned a bit of a debate about how many of the recipes prescibed (basically reading as if Bob were spring's founder or something) were _absolutely atrocious_ for performance.

Now, the criticisms are absolutely _one hundred percent valid_, but I don't actually think it is the real reason this methodology is a problem. Clean code was _never concerned with performance to begin with_ so it makes _perfect sense_ that they would incentivize building software with call stacks that read as an recursive bean-proxy salad.

Performance was never the point.
Deep knowledge was never the point.
Technology and tools were never the point.

Clean Code, such as Spring, is optimized for Developer Fungibility.

Springs emphasis on automagic wiring, hiding as many implementation details as possible, and building the reflection oriented framework allows developers to each silo themselves off into their own little land of beans and services. There's no need to worry about _how_ they get to theirs because the God in the machine of Springboot's framework will dutifully wire up anything with the appropriate labels, creates classes and inject them, wire up dependencies, etc.

It means that developers can ship chunks of code with reckless abandon and then get moved around as disposable cogs in the corporate machine (if they are lucky) or laid off and sent to look for work yet again. It is the kind of framework and business model that works incredibly well for staffing firms that ship technologists around on a project by project basis where deep technical knowledge is not the priority, but the ability to hop in write some quick code to check off deliverables and then hop out.

It's really just the precursor to vibe coding. No one wants to know how it works, they just want to sprinkle magic and let the machine gods do the rest.

Ultimately, this is a rather pragmatic way to manage projects. There's a certain beauty to knowing that the quality of the code is kind of immaterial because it's someone else's problem in the future. From a management perspective there's a beauty in being able to hire anyone that takes an 8 weeks spring course to wire up your code a bit. And when you don't have to ask that people have any technical knowhow and just need to be a X frameworker it decreases hiring risks.

Like, for every time I think "Oh, why did you implement a stale job cleaner by hand? You can just create a pg-cron?" I realize the reason is mostly "If we used that, we'd have to expect that they understand postgres" instead of allowing them to just read the spring code.

And that's really what it all comes down to. A pragmatic choice to optimize staffing and titanic projects instead of pushing deep technical competence and  proper technical competence. For big long running projects, in many cases, team changes and head counts are just _going to happen_ and being able to easily and trivbially replace engineers is a rather huge HR win. It does come at a price though, and -- in my opinion -- explains why so many companies went crazy with AI and vibe coding. The selling point of AI agents is that they are a Yes Man in programmer form. It's all about risk assessment and pragmatic approaches to staffing, _rather_ that effective means to develop the best software. While I personally find it frustrating, And I guess that's fine
