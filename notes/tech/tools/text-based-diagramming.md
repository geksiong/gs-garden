---
title: Text-based Diagramming Tools
date: 2022-12-26

tags:
- diagramming
---

I draw a lot of diagrams for work, usually freehand on paper/whiteboard, or with Visio/draw.io, but sometimes I also use text-based diagramming tools. I like the fact that they can be versioned controlled and potentially automatically generated (it hasn't materialised for me though), but they can also be very verbose and slower to produce (except for sequence diagrams which is a sweet spot for using text-based diagramming). I'm still searching for the perfect text-based diagramming tool.

## PlantUML

One of the most established tools out there. PlantUML is a command-line Java program.

### Pros

- Well-supported by many tools. You can render PlantUML diagrams with Confluence, GitLab (not on GitHub though), VSCode
- Comes with PlantUML server
- Many diagram types
- Good for Sequence diagrams, Class diagrams
- Some degree of customisation is possible, resulting in extensions like C2 diagrams

### Cons

- Can't render in browser due to dependency on Java and Dot.
- Horrible layout, despite the manual controls
- Different syntax for each diagram type
- Inconsistent features e.g. handwritten/sketch look and feel
- Lack of support for fonts, markdown, images, etc - all the wonderful things we are used to on the web are not there...

## MermaidJS

Javascript-based tool that is finding quite a bit of popularity due to it being supported by both GitHub and GitLab.

### Pros

- Able to render in browser
- Good support in VSCode, GitHub, GitLab
- Recently added a potentially useful diagram type for Git history trees

### Cons

- I find it even uglier than PlantUML
- Inconsistent features: e.g. notes in sequence diagrams but not in class diagrams

## D2

This one is up and coming. Written in Go. It has design principles and approach that I generally agree with - establish a consistent diagramming language first, and it has better layout and look than other tools.

### Pros

- Rather than trying to define different diagram types, it focuses more on shapes, connections and nesting as a primary construct.
- Modern features: markdown, code blocks, images
- Database diagrams are possible (but the relationships are currently a pain to write)
- Diagrams look nice

### Cons

- No browser support yet
- Some things are too verbose e.g. styling of lines, arrow types
- Class diagrams: lack support for interfaces

### Honourable Mentions

- Web sequence diagrams
- Kroki - an all-in-one editor for various diagramming engines
