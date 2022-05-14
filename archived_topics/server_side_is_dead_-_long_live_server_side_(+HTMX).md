
# Server-Side is Dead! Long Live Server-Side (+ HTMX)

## Abstract / Elevator Pitch (300 characters)

With no 'standard' approach to front-end other than the built-in templating and so many JS front-end frameworks and tools and options, it's easy to get thrown for a loop. HTMX lets us stick to what django is good at - server-side stuff, mainly - without feeling like we're missing out on modern tools.

## Audience Level

- Beginner to intermediate

## Objectives

By the end of this talk, audience members will have a better understanding of what HTMX is, will understand concrete examples of how it can be applied to their django projects, and will have a list of resources for further learning and discussion.

## Outline

- The current state (5 min)
    - Django templates
    - Ajax, transitions, and asynchronous front end approaches
    - Heavy JS Frameworks (Vue, React, jQuery)

- A different mindset (9 min)
    - Getting back to the heart of HATEOAS
    - Splitting up complicated views

- Feature and approach walk-throughs (20 min)
  For a variety of common web application features, we will take a look at a typical django approach and how one might approach the problem with django + htmx.

    1. Messaging inbox functionality (read, archive)
        - The default Django approach
        - Django + HTMX approach
    2. One-click settings
        - The default Django approach
        - Django + HTMX approach
    3. Multiple forms in multiple tabs
        - The default Django approach
        - Django + HTMX approach
    4. Formsets and an HTMX approach
        - The default Django approach
        - Django + HTMX approach

- Tips, best practices, and pitfalls (6 min)
    - CSRF Tokens
    - Lightweight JS libraries which compliment HTMX
    - Simplifying things with django-htmx
    - Resources for further reading and community

## Notes

Format: 40 minute talk

I have been fortunate to work with htmx since shortly after its inception. Since then I have integrated it into several features of a moderate sized commercial django-based SaaS application. I have also written a blog post with an [example implementation](https://jacklinke.com/ajax-enabled-checkbox-and-select-with-django-and-htmx) of htmx with django for building an interactive settings page, have created an [example to-do list app](https://github.com/jacklinke/django-htmx-todo-list) using django and htmx, and actively take part in conversations about django and htmx on the htmx Discord, on Reddit, and on Twitter.


## Tags

    Django, HTMX, Ajax, front-end, javascript, web, tools, user experience, beginner, ux

## Link to the actual talks that were given based on this CFP

[htmx-talk-2021](https://github.com/jacklinke/htmx-talk-2021)
