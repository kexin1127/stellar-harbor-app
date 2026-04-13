---
title: 'Home'
date: 2023-10-24
type: landing

design:
  spacing: '4rem'

sections:
  - block: biography
    content:
      username: me
      button:
        text: Download CV
        url: uploads/resume.pdf
    design:
      show_status: false
      spacing:
        padding: ['0', '0', '6rem', '0']
      banner:
        filename: background.jpg
      biography:
        style: 'text-align: justify; font-size: 0.8em;'
      avatar:
        size: large
        shape: rounded

  - block: collection
    id: papers
    content:
      title: Featured Publications
      filters:
        folders:
          - publication
        featured_only: true
    design:
      view: article-grid
      columns: 1

  - block: collection
    content:
      title: Recent Publications
      filters:
        folders:
          - publication
        featured_only: false
    design:
      view: citation

  - block: experience
    id: experience
    content:
      username: me
    design:
      date_format: 'January 2006'
      is_education_first: false

  - block: collection
    id: projects
    content:
      title: Projects
      subtitle: ''
      filters:
        folders:
          - project
    design:
      view: card
      columns: 2
---
