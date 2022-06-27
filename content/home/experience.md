---
# An instance of the Experience widget.
# Documentation: https://wowchemy.com/docs/page-builder/
widget: experience

# This file represents a page section.
headless: true

# Order that this section appears on the page.
weight: 40

title: Extracurricular Activites
subtitle:

# Date format for experience
#   Refer to https://wowchemy.com/docs/customization/#date-format
date_format: Jan 2006

# Experiences.
#   Add/remove as many `experience` items below as you like.
#   Required fields are `title`, `company`, and `date_start`.
#   Leave `date_end` empty if it's your current employer.
#   Begin multi-line descriptions with YAML's `|2-` multi-line prefix.
experience:
  - title: A POTENTiAL BLOOD STEM CELL DONOR
    company: China Marrow Donor Program (CMDP)
    company_url: 'http://www.cmdp.org.cn/'
    company_logo: org-gc
    location: China
    date_start: '2020-11-01'
    date_end: ''
    description: |2-
        For more information: [my story]({{< ref "extracurricular/cmdp.md" >}})

  - title: Professor of Semiconductor Physics
    company: University X
    company_url: ''
    company_logo: org-x
    location: California
    date_start: '2016-01-01'
    date_end: '2020-12-31'
    description: Taught electronic engineering and researched semiconductor physics.

design:
  columns: '2'
---
