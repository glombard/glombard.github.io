---
layout: post
title: What's the context of this Context?
tags: android
---

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr"><a href="https://twitter.com/rallat">@rallat</a> Context!</p>&mdash; Gert Lombard (@codeblast) <a href="https://twitter.com/codeblast/status/636679726757613568">August 26, 2015</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

When I see Android code like this, it makes me feel uncomfortable:

	class Foo {
		private final Context mContext;

		public Foo(Context context) {
			mContext = context;
		}

		....
	}

You may be thinking: *"What on earth are you talking about? That's a super common pattern in Android development to pass `Context` around from class to class. We need a `Context` instance for all sorts of things like accessing system services etc! Oh wait, I get it, you're talking about the `m` prefix for class member variable names which is specific to Java code in Android!"*

No, I'm not talking about the weird and unnecessary "Hungarian notation" prefix in `mContext`, although I agree it is an eyesore, but that's a topic for a different blog post! ;-)

**tl;dr:** *Inject/pass explicit types (e.g. `LayoutInflater`) to classes where they are needed, instead of passing `Context` all over the place.*

I find the pervasiveness of this habit of passing a `Context` instance into class constructors and method parameters confusing and disturbing. It's unfortunate that the Android framework provided the `Context` base class as a kind of [god object](https://lostechies.com/chrismissal/2009/05/28/anti-patterns-and-worst-practices-monster-objects/) or together with things like `getSystemService()` as a kind of [service locator](http://blog.ploeh.dk/2010/02/03/ServiceLocatorisanAnti-Pattern/) - i.e. it's the class that knows and does everything!

Why is it a bad idea to pass `Context` to a class's contructor or a method, when everyone does it and you can't do anything in Android without a `Context`?

Some examples of issues:

- If I'm not the original author of this code, I will wonder: which context is this? Activity, Application, Service?
- Can I hold a reference to this context in case I need it later? No, not unless it's the application context. But how do I know for sure 
this is the application context? Especially if this class is used in different context further down the object graph and I don't know who the caller is. It will eventually cause leaks somewhere e.g. activity leak, because some class will inadvertently hold a reference to the Activity.
- Can I use LayoutInflator on it? No, not if it's not the context of the activity I'm expecting.
- It's a violation of the [Law of Demeter](http://c2.com/cgi/wiki/LawOfDemeter?LawOfDemeter). For example, when I write the unit test for class `Foo` which takes `Context` as a constructor dependency, how do I know which parts of `Context` I need to mock for the test to legitimately pass? If the class is sufficiently complex, I'll just have to "guess" and figure it out by trial and error. Worse, I may have to mock `A` to return a mock of `B` to return a mock of `C`... Now the unit test is becoming unwieldly.

**A solution**

Assuming you're convinced at this point that it's a bad idea to pass `Context` down several levels from class to class, what can we do to improve the situation?

Apply this simple principle: **communicate your intention explicitly in your code.**

In other words, in many cases, you don't really want the `Context` reference itself, you just want to use it to invoke some action or retrieve some system service. In that case, rather pass the exact service(s) explicitly to the constructor of the class instead of `Context`.

As a simple example to demonstrate the point, consider this snippet from a simple hypothetical list adapter:

    private final Context mContext;
    private final String[] mValues;

    public MyListAdapter(Context context, String[] values) {
      this.mContext = context;
      this.mValues = values;
    }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
      LayoutInflater inflater = LayoutInflater.from(mContext);
      View rowView = inflater.inflate(R.layout.rowlayout, parent, false);
      ...

We pass the `Context` into the adapter, but the code doesn't actually need a `Context`, it really needs a `LayoutInflater` instead! There is no need carrying the `Context` around all over the place just in case we need it some day.

Let's refactor this code to make it slightly easier to understand, reason about and slightly easier to unit test:

    private final LayoutInflater mLayoutInflater;
    private final String[] mValues;

    public MyListAdapter(LayoutInflater layoutInflater, String[] values) {
      this.mLayoutInflater = layoutInflater;
      this.mValues = values;
    }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
      View rowView = mLayoutInflater.inflate(R.layout.rowlayout, parent, false);
      ...

Even if you need need to retrieve multiple objects from `Context` and not just one as in this example, then it's still better to pass separate references to those objects.

Of course there are many exceptions to this advice, because often you'll be forced to pass `Context` around through several layers of an object hierarchy because some leaf node which you didn't write requires it, but even then, you can still be more explicit with your paramter types (and/or parameter names) to communicate your intention more clearly.

For examle, let's say our list adapter really needs the activity `Context`, then I'd propose refactoring the code to accept an `Activity` instance instead, so it's clear what kind of `Context` we're expecting here:

    public MyListAdapter(Activity activityContext, String[] values) {
      this.mContext = activityContext;
      this.mValues = values;
    }

Or if you're using Dagger for dependency injection, you could use qualifier annotations like `@ActivityContext` and `@ApplicationContext` to differentiate (or even better if possible, separate your Dagger scopes to make it impossible to inject an Activity reference into a service that will outlive the activity...)

Also see: [Context, What Context?](https://possiblemobile.com/2013/06/context/) by [Dave Smith](https://twitter.com/devunwired)
