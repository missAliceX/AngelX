---
layout: post
title: "Web Application"
date: 2021-07-13
excerpt: "Want to see something?"
tag:
comments: false
---

# How to become a Front End Software Engineer

_Want to see something? Got to build it yourself, sorry._

After this project, you will be pretty much as good as any Joe out there. Yes, we are doing a tech spec again. Get used to it.

# Project Pages Tech Spec

## Summary

Create a web application to let users propose, vote and tag projects.

## Context

We will learn to build a web application by doing. The principles you learned from building the [Particle Simulator](/particle-simulator) and [Agar.io](/agar-io) will come in handy.

## Goals

- Create a project proposal page
- Create project listing page
- Integrate the functionality of these pages

## Non-Goals

- Do not create sign-in, user profile, or other pages because you'll be asked to create your own capstone project later
- Supply data to the pages from a server as it will be covered in the next post

## Plan

We will use React to create the web application using the principles of component design. Design the page based on a mock-up.

### Project Proposal Page ([Mock Up](https://www.figma.com/file/elCdGNnjq9l8ESs43WmNSM/crowd-funding-app?node-id=13%3A11))

Allows users to propose a project with the following details. All fields are required unless specified:

- Project Name (single input)
- Tagline (single input)
- Problem Statements (input list)
- Potential Solutions (input list)
- Tags (input list, item is removable)
- Cover image link (single input, not required)
- Video link (single input, not required)

Create a back button which will lead to the _Project Listing Page_ when clicked.

### Project Listing Page ([Mock Up](https://www.figma.com/file/elCdGNnjq9l8ESs43WmNSM/crowd-funding-app?node-id=34%3A167))

Display a list of projects with the following basic details:

- Project Name
- Project Stage
- Tagline
- Cover Image
- View Count
- Activities Count

Create a create project button which will lead to the _Project Proposal Page_ when clicked.

# Tutorial

Let's help you get set up with React.

## Setup

1. [Install latest version of NodeJS](https://nodejs.org/en/download/)
1. Go to the GitHub page for [`crowd-funding-app`](https://github.com/missAliceX/crowd-funding-app) and fork the repository
1. Open terminal and clone your forked repository:
```
git clone https://github.com/<your-username>/crowd-funding-app
cd crowd-funding-app
ls -l
```
1. Start the app with the command below. Your browser should open:
```
npm start
```
<figure>
	<a href="{{ site.url }}/assets/img/projects/web-application/1.png"><img src="{{ site.url }}/assets/img/projects/web-application/1.png"></a>
	<figcaption>This looks easy... right?</figcaption>
</figure>


### Let's Break it Down...

<figure>
	<a href="{{ site.url }}/assets/img/break-it-down.gif"><img src="{{ site.url }}/assets/img/break-it-down.gif"></a>
	<figcaption>Who wanna?</figcaption>
</figure>

## Overview

The most dominant way to build a front-end application is **component or atomic design**. We can summary front-end design to consist of the steps below:

1. Build pages with reusable and granular components
1. Supply data to pages to render each component
1. Send data and requests to the server based on user action (events)

## Components

Components are functionally stand-alone user interface (UI) parts that you can reuse create more components and pages.

### Input Field

Try to put this code in `App.js` to see an input in action on its own:
```
import Input from "components/input";
import "index.css";
import "styles/text.scss";

function App() {
  return <Input
    inline
    placeholder="type, then click x"
  />;
}

export default App;
```

It works. Uh... so what? Ummm...! You can create bigger, better components with it without knowing too well how it works; in software engineering in general, this idea is called **creating an interface**.

### Input List

Now use the following code. Try clicking the `x` icon again and see what happens:
```
import Input from "components/input-list";
import "index.css";
import "styles/text.scss";

function App() {
  return <InputList id="exmaples" />;
}

export default App;
```

Oh, it doesn't do the same thing. Why?

Let's look at the [code in `/components/input-list.js`](https://github.com/missAliceX/crowd-funding-app/blob/master/src/components/input-list.js#L38-L42), where it is used. `<InputList />` uses the `<Input />` component and passes a function to its `onDelete` attribute. What does the input component do with it though?

Let's look at the [code in `/components/input.js`](https://github.com/missAliceX/crowd-funding-app/blob/master/src/components/input.js#L93-L96). It looks like `<Input />` is a **functional component** that takes the property `onDelete` and apply it to the `onClick` attribute. When the `onDelete` property is not present, it sets the value to an empty string instead. [`onClick` is a default attribute for HTML elements](https://www.w3schools.com/jsref/event_onclick.asp) where it will trigger when a user clicks on the said element.

### Styles

Why does the input field look the way it does? Try [removing the code from here](https://github.com/missAliceX/crowd-funding-app/blob/master/src/components/input.js#L17-L32) and add them back a bit at a time.

## Challenge 1: Make your own Input Field
Now, try to design your own input field. Sorry, you don't get to be creative yet. Recreate [this input field from Material Design](https://material.io/components/text-fields#usage). You should rewrite `input.js` and make it compatible with `input-list.js`. Here are a few steps to guide you:

1. Identify the parts it is consisted of.
1. Identify its behavior when you hover, click, etc.
1. Create the skeleton and start fleshing it out.
1. Write unit tests in `input.test.js`

Use this template to help you start implementing your input field:

```
import styled from "styled-components";

const Wrapper = styled.div`
/* add your CSS styles here */
`

const Label = styled.div`
/* add your CSS styles here */
`

const InputField = styled.input`
/* add your CSS styles here */
`

function Input(props) {
	return (
		// edit your HTML below
		<Wrapper>
				<Label />
				<InputField />
		</Wrapper>
	)
}

// This allows the component to be imported from other files.
export default Input;
```

Remember to make a new branch and pull request before you commit your code.
{: .notice}

### Tests

Remember to write tests. You are not really a software engineer otherwise. Admittedly, this is my least favorite part of writing code. Some people are crazy enough to write tests first and then implement the code after; this is called **test-driven development** - for the anally retentive.

To run tests:
```
npm test
```

## Challenge 2: Make the Navbar responsive

What is responsive? Responsive design is that your user interface (UI) looks good no matter the screen size. You can try resizing your browser window while looking at the `crowd-funding-app` page. Additionally, you can try out how your app would look on different devices right on the Chrome browser:

<figure>
	<a href="{{ site.url }}/assets/img/projects/web-application/2.gif"><img src="{{ site.url }}/assets/img/projects/web-application/2.gif"></a>
	<figcaption>The Navbar hugs the left and bottom of the screen.</figcaption>
</figure>

Now switch your branch to `unresponsive` to begin this challenge. There are a number of that reads the following to help you understand where to add responsive styles:

```
/* when the screen is small, ... */
@media only screen and (max-width: ${common.screensize}) {
	/* add your responsive styles here */
}
```

Instructions:

1. Do it.
1. You can now return to `master` branch.

## Challenge 3: Complete the Project Proposal Page

At this point, you practiced creating your own `<Input />` component and earned the right to use it. Continue implementing the `Project Proposal Page`; it should be a breeze up to the "Tags" section.

### Reacting to a User Event

When implementing "Cover Image URL" and "Intro Video URL", you will need to learn how to create another element after the user paste in a URL. Let's look back to the steps in Challenge 1 and apply it to this scenario. The `<InputList />` component can already create another element after user enters text. Can we use similar code to add a `previewImage` and `previewVideo` feature to the `<Input />` component? You may find it helpful to be able to [embed YouTube videos](https://www.w3schools.com/html/html_youtube.asp).

## Challenge 4: Complete the Project Listing Page

There are a few more new concepts to learn from creating the Project Listing Page. You will need to create two new components:

1. Search bar with a plus button which leads to the Project Proposal Page using the `react-router-dom` [`<Link />` component](https://stackoverflow.com/questions/29244731/react-router-how-to-manually-invoke-link).
1. Create Project Card using the [`<Grid />` component](https://material-ui.com/components/grid/) such that it is **responsive**.
1. Create the Project Listing Page component and add it to the router in `App.js`

## Challenge 5: Request and Send Data to a Server

... TODO ...

# Conclusion

This is a lot of work! Good job... -- me! You have done quite a lot as well. Here is what you have learned:

## Buzzwords
- [Atomic (modular/component) design](https://bradfrost.com/blog/post/atomic-web-design/)
- [Test Driven Development](https://en.wikipedia.org/wiki/Test-driven_development)

## Skills Acquired
- Front-end Unit-testing, Mocking
- Using a server side REST API
- Browser Events (clicks, key presses)
- Responsive Design (media query and Grid)
- Router (URL and links)
- React Hooks (useState, useEffect, useHistory)
- React Component / HTML Rendering
- Styled Component / CSS
