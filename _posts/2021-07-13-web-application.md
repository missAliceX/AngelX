---
layout: post
title: "Web Application"
date: 2021-07-13
excerpt: "Want to see something?"
tag:
comments: false
---

# How to build a web application

_Want to see something? Got to build it yourself, sorry._

Yes, we are doing a tech spec again. Get used to it.

# Project Pages Tech Spec

## Summary

Create a web application to let users propose, vote and tag projects.

## Context

We will learn to build a web application by doing. The principles you learned from building the [Particle Simulator](/particle-simulator) and [Agar.io](/agar-io) will come in handy.

## Goals

- Create a project proposal page
- Create project listing page
- Create a project details page
- Integrate the functionality of these pages

## Non-Goals

- Do not create sign-in, user profile, or other pages because you'll be asked to create your own capstone project later
- Supply data to the pages from a server as it will be covered in the next post

## Plan

We will use React to create the web application using the principles of component design. Design the page based on this [mock-up](https://www.figma.com/file/elCdGNnjq9l8ESs43WmNSM/lifepath.ai?node-id=0%3A1).

### Project Proposal Page

Allows users to propose a project with the following details. All fields are required unless specified:

- Project Name
- Tagline
- Problem Statements (list of text)
- Potential Solutions (list, votable)
- Tags (list of relevant topics, at least one required)
- Cover image link (not required)
- Video link (not required)

Create a back button which will lead to the _Project Listing Page_ when clicked.

### Project Listing Page

Display a list of projects with the following basic details and opens its _Project Details Page_ when clicked:

- Project Name
- Tagline
- Cover Image
- Up and down vote button
- View count

Create a project proposal button which will lead to the _Project Proposal Page_ when clicked.

### Project Details Page

Display the following information for a project:

- Video
- Cover image
- View count
- Up and down vote stats
- Project Name
- Tagline
- Problem Statements
- Potential Solutions
- Tags

Create a back button which will return to the _Project Listing Page_ when clicked.

# Tutorial
