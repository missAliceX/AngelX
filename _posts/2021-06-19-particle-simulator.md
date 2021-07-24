---
layout: post
title: "Hello World"
date: 2021-06-19
excerpt: "Intro to software engineering"
tag:
comments: false
---

# Let's build a Particle Simulator

<figure>
	<a href="{{ site.url }}/assets/img/pikachu.png"><img src="{{ site.url }}/assets/img/pikachu.png"></a>
	<figcaption>This is your face. Seriously, you are capable of a lot more than you think. Let's get started.</figcaption>
</figure>

# Setting things up...

## Code Editor

This is where you can type out your code. You can use anything you like.

- [VS Code](https://code.visualstudio.com/) is generally used at the workplace.

## Workspace

1. Create a folder for all yours projects.
1. [Download the project folder](https://github.com/missAliceX/particle-simulator/archive/refs/heads/master.zip)
1. Unzip the file into a folder.

# Make a particle simulator

Open said project in your code editor. You will notice folders ending with `-solution`. Do not open it until you have spent 10-15 minutes on each problem.

## Challenege 1: Draw a circle on HTML canvas

Let's check out what we should get after we solve the challenge by opening `particle-simulator/1-solution/index.html` in your browser:

<figure>
	<a href="{{ site.url }}/assets/img/projects/particle-simulator/1.png"><img src="{{ site.url }}/assets/img/projects/particle-simulator/1.png"></a>
	<figcaption>A blue dot on the screen. Not very impressive.</figcaption>
</figure>

Open the `particle-simulator/1` folder. You will see two files:

```
particle-simulator/1
  index.html
  index.js
```

The `index.html` file contains the `canvas` element. This is the element that we are going to draw our circle in. The style attribute contains some CSS code which changes the position and appearance of the canvas element.

{% highlight html %}
<canvas
  id="myCanvas"
  width="1000"
  height="600"
  style="..."
/>
{% endhighlight %}

Notice there's also a script which loads the JavaScript code. The browser will load the `<canvas>`, and `index.js` in that order because we specified `<canvas>` first. Try specifying `index.js` before `canvas` and refresh the page again.

{% highlight html %}

<script src="index.js"></script>

{% endhighlight %}

---

Anyways, this is your challenge in `index.js`:

{% highlight javascript %}

function draw() {
// Put the code to draw a circle here
// Hint: Google it - "HTML canvas draw circle"
}

draw(); // This runs the draw() function

{% endhighlight %}

## Challenege 2: Draw a bunch of circles

What's so hard about this? Nothing, actually, you just do the same thing but notice what changed. We now have `utils.js` and `circle.js`. Let's just try looking at the solution in the browser again `particle-simulator/2-solution/index.html`.

<figure>
	<a href="{{ site.url }}/assets/img/projects/particle-simulator/2-err.png"><img src="{{ site.url }}/assets/img/projects/particle-simulator/2-err.png"></a>
	<figcaption>Nothing! Oh, but I see an error in the inspector console.</figcaption>
</figure>

Dang... Better fix the bug so you can at least see the solution.

## Challenege 2.1: Fix Bug

<figure>
	<a href="{{ site.url }}/assets/img/projects/particle-simulator/2.png"><img src="{{ site.url }}/assets/img/projects/particle-simulator/2.png"></a>
	<figcaption>Okay. Looks like it works now.</figcaption>
</figure>

What? I am not going to help. You are going to fix this bug yourself. Hint: I already told you about this bug before.

---

### Classes

We will learn about classes. Try running this code:

{% highlight javascript %}

class Circle {
  constructor(color) {
    this.color = color;
    this.radius = 5;
  }
  cry() {
    console.log("the circle cries ${this.radius} ${this.color}");
  }
}
var circle = new Circle("red");
circle.cry();
circle.radius = 7;
circle.cry();

{% endhighlight %}

---

Now copy over your `draw()` from before to `circle.js` and give it a spin. Do you see circles on the canvas? I bet you see errors. Fix them. Hint: mess with the code above and learn how classes work.

### Arrays

In `index.js`, we will render all the circles onto the canvas:

1. We created an `Array()` variable named `objs`.
1. We pushed some `Circle()` objects into it.
1. We call the `draw()` function.
1. The `draw()` function loops through each object.
1. It invokes the `draw()` function of each objects, circles in our case.
1. What does the `obj.draw()` do? Hint: look at the **Classes** section again

{% highlight javascript %}

function draw(objs) {
  for (let obj of objs) {
    obj.draw();
  }
}

// Can you create circles with different colors?
// Can you create more circles?
// Can you use a loop like you see above to push these circles instead?
var objs = new Array();
objs.push(new Circle("blue"));
objs.push(new Circle("green"));
objs.push(new Circle("red"));
draw(objs);

{% endhighlight %}

Play with the `objs` array and see if you can draw more circles with different colors. Try these colors: `#FF00FF`, `rgba(100, 100, 20, 0.4)`.

## Challenege 3: Move the Circles around

<figure>
	<a href="{{ site.url }}/assets/img/projects/particle-simulator/3.gif"><img src="{{ site.url }}/assets/img/projects/particle-simulator/3.gif"></a>
	<figcaption>You get the drill.</figcaption>
</figure>

We have now transformed our `index.js` code into a class as well. Look at the console log, the `render()` seems to be running over and over again. When a function calls itself inside its code, we call this recursion. It turns out that's how game engines draws the pixels on our screen, one frame at a time. Implement the render function:

1. Make the circles move.
1. Do it!

## Challenege 4: Make circles bounce off wall

<figure>
	<a href="{{ site.url }}/assets/img/projects/particle-simulator/4.gif"><img src="{{ site.url }}/assets/img/projects/particle-simulator/4.gif"></a>
	<figcaption>Bonk. Bonk. Bonk.</figcaption>
</figure>

You now have to write a collision algorithm. If this is your first time, congrats on giving me your virginity! You should look up **conditional statements** yourself and figure out how to use it here. Here's some **psuedo-code** for this algorithm to help you get started:

1. Determine the next `x` and `y` position
1. If the next `x` is less than 0, meaning it disappeared off to the left:
    1. Set the direction to a general right direction
    1. Make sure the `x` position will make the circle visible
1. If the next `x` is greater than the canvas's width, meaning it disappeared off to the left:
    1. Set the direction to a general left direction
    1. Make sure the `x` position will make the circle visible
1. If the next `y` is less than 0, meaning it disappeared off to the top:
    1. Set the direction to a general down direction
    1. Make sure the `y` position will make the circle visible
1. If the next `y` is greater than the canvas's height, meaning it disappeared off to the bottom:
    1. Set the direction to a general up direction
    1. Make sure the `y` position will make the circle visible

## Challenege 5: Click click click click

<figure>
	<a href="{{ site.url }}/assets/img/projects/particle-simulator/5.gif"><img src="{{ site.url }}/assets/img/projects/particle-simulator/5.gif"></a>
	<figcaption>Rainbow!</figcaption>
</figure>

We are not building an app if there's no user input. Make it fun. Here's the **technical requirements**:

1. Clicking on the canvas should spawn a new circle with random color
1. The new circle should appear exactly where you clicked it

# Conclusion

Congratulations! You have just done something from start to finish. In the process, you picked up valuable skills. These are the highlights of your accomplishments.

1. Debugging - You figured out why `2_solution` wasn't running
1. Overcame unexpected challenges - You overcame the *clear canvas* problem in **Challenge 3**
1. Turn someone's idea into reality - You learned to turn psuedo-code into actual code
1. Reverse engineering - You figured out how classes and arrays work by looking at the code
1. Reading documentations - You learned how to look up conditionals and apply it to the pseudo-code

<div markdown="0" class="bot-nav">
  <a href="javascript:history.back()" class="btn">Back</a>
  <a href="{{ site.url }}/agar-io" class="btn">Assignment: Recreate Agar.IO</a>
</div>
