---
layout: about
title: about
permalink: /

profile:
  align: right
  image: prof_pic.jpg
  image_circular: false # crops the image to make it circular

selected_papers: true # includes a list of papers marked as "selected={true}"
social: true # includes social icons at the bottom of the page

announcements:
  enabled: true # includes a list of news items
  scrollable: true # adds a vertical scroll bar if there are more than 3 news items
  limit: 5 # leave blank to include all the news in the `_news` folder

latest_posts:
  enabled: false
  scrollable: true # adds a vertical scroll bar if there are more than 3 new posts items
  limit: 3 # leave blank to include all the blog posts
---

I'm a second-year computer science PhD student at [Northeastern University](https://www.khoury.northeastern.edu/), advised by Professor [Byron C. Wallace](https://www.byronwallace.com/). I completed my undergraduate studies at [Brown University](https://www.brown.edu/), where I received an Sc.B. in Applied Mathematics–Computer Science.

My research focuses on the **interpretability of large language models (LLMs)**, with a particular focus on clinical and biomedical applications. The question I would like to study during my PhD is: _how can we use mechanistic interpretability in realistic, high-stakes settings in a way that is actionable?_

Recently, I have been working on **model organisms**, models trained with deliberately installed behaviors, and using them as testbeds for a wide range of interpretability techniques.

<script>
  // Mailto links do nothing for visitors without a desktop mail client, so the email icon
  // copies the address instead, using al_email_protect's click-to-copy handler and toast.
  document.addEventListener("DOMContentLoaded", function () {
    var link = document.querySelector('a[title="Email"][href^="mailto:"]');
    if (!link) return;
    var parts = link.getAttribute("href").slice("mailto:".length).split("@");
    link.classList.add("al-email-protect");
    link.setAttribute("data-eu", parts[0]);
    link.setAttribute("data-ed", parts[1]);
    link.setAttribute("href", "#");
    link.setAttribute("title", "Copy email address");
  });
</script>
