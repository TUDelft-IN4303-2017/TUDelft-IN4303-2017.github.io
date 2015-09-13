---
layout: page
title: "Team"
excerpt: "Team"
tags: ["team"]
---

## Contact

Handling questions by e-mail is very inefficient, why we try to avoid it as much as possible. Twitter works pretty well for short questions. Lectures and lab sessions are natural points of contacts with instructors and teaching assistants. We also offer walk-in hours on Wednesday mornings. For longer discussions, we prefer appointments outside our walk-in hours. To make an appointment, please send the responsible instructor an email with

* a detailed description of your problem: Where are you struggling? What questions do you have? How can we help you?
* some alternative dates: We prefer appointments in the morning. Keep in mind that we will need some time to prepare the appointment.

You will receive an email with

* some tasks for you to prepare the appointment,
* the date of our appointment, which you are asked to confirm.

## Team Members

{% for author in site.data.authors %}
  {% include _team-member.html %}
{% endfor %}

