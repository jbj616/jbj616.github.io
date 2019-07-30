---
title: "Spring-Study"
layout: archive
permalink: /categories/spring_study
author_profile: true
sidebar_main: true
---

{% assign posts = site.categories.spring_study | sort:"date" %}

{% for post in posts %}
{% include archive-single.html type=page.entries_layout %}
{% endfor %}
