---
layout: post
title: "Ekal AI - Side Project"
categories: [Projects]
tags: [ai, edtech]
---

I have finally been able to create an app out of an idea that I have been thinking about for some time now. This cuts into the AI-Edtech space. The main motivation for this is my 6 year old who has so many questions and is a curious learner. We now have the power of AI but there are not many applications tailored for young learners.

## The Story Behind the Name
In the Mahabharata, a young boy named Ekalaivan(Ekalavya), wanted to learn archery from the greatest teacher then, guru Dronacharya. But he was turned away by the teacher, as he was not a warrior prince, but belonged to a forest tribe. He felt dejected but didn't give up. He built a clay statue of Drona in the forest and considered the statue as his own guru. He practised for hours every day and eventually became so good that he shocked Arjuna himself with his skills at archery. The story iterates the fact that "The determination to learn is more crucial than having a teacher itself". 
This story is the heart of Ekal AI - an app that enables any child to learn anything they want to.

## What Ekal AI Does
Ekal AI is a web-app where students design their own AI teacher persona for any subject they want to learn about. Here's the flow:

1. Create your profile - Tell us your name, upload your photo(optional)
2. Add a subject - Math, Programming, Gita or even Astronomy, Philosophy. The sky is your limit
3. Upload your study material - PDF textbooks, workbooks, notes or even ancient scriptures
4. Design your teacher - Choose name, salutation and personality.
Eg: Warm and patient, cool and composed or Strict and Socratic.
5. Design your classes - Mention how you want your teacher to take your class.
Do you want the teacher to teach you each concept followed by a question to let your think? Do you want real-world examples? Do you want practise exercises?
6. Attend your classes in the virtual classrooms.
7. Track your progress - Topics you have covered are tracked automatically.

## What is special about Ekal AI
Most AI tutors are one-size-fits-all. A child who learns through story-telling gets the same bot that splits out data from the book or internet(which might be too much for the child to understand and track progress). In Ekal AI, the student is in charge of the curriculum, the material, and the teacher's personality. This creates metacognitive ownership — a child who has thought about how they want to learn is already a better learner.

## The Tech Stack
Built with Next.js (App Router), TypeScript, Tailwind CSS v4 and the model runs on Ollama(Run entirely locally with open-source models, zero API cost) . The teacher's system prompt is dynamically built from:
- The teacher's personality and salutation
- The parsed subject document (PDF/TXT)
- Custom teaching instructions the student provides
- The student's name and progress history

It's essentially RAG + persona engineering designed for children.

## What's Coming Next
1. Fine-tune the model - Right now, the AI teacher runs on Qwen-8B, a capable but heavyweight model that needs a server or a powerful computer. The teaching persona is shaped entirely through prompts — a detailed system message that describes the teacher's style, subject knowledge, and pedagogy. It works well, but it's slow, expensive to run, and inaccessible without an internet connection. Instead of explaining how to teach in every prompt (prompt engineering is a discipline that I don't get it right myself, how will children/end-users find it), we train a smaller model so that good pedagogy is baked in. 
2. Session summaries — Before each class, compress the conversation into a learning summary so context stays fresh across sessions
3. Using AI to gauge the student's engagement in each classroom.
4. Weekly report sent to child/parent on the their progress.

You can check it out at [ekalai.one](https://www.ekalai.one). You can login with just a user name. No payment/card details needed.

## A quick look

<div class="carousel" style="position:relative;overflow:hidden;border-radius:8px;box-shadow:0 2px 12px rgba(0,0,0,.15)">
  <div class="carousel-track" style="display:flex;transition:transform .35s ease;will-change:transform">
    <div class="carousel-slide" style="min-width:100%;text-align:center">
      <img src="/assets/img/ekal-ai/homepage.png" alt="Home page" style="max-width:100%;height:auto;display:block;margin:0 auto">
      <p style="margin:.5rem 0 .75rem;font-style:italic;font-size:.9rem">Home — create your profile and get started</p>
    </div>
    <div class="carousel-slide" style="min-width:100%;text-align:center">
      <img src="/assets/img/ekal-ai/teaching_instructions.png" alt="Teaching instructions" style="max-width:100%;height:auto;display:block;margin:0 auto">
      <p style="margin:.5rem 0 .75rem;font-style:italic;font-size:.9rem">Teaching instructions — tell your teacher how you want to learn</p>
    </div>
    <div class="carousel-slide" style="min-width:100%;text-align:center">
      <img src="/assets/img/ekal-ai/subjects.png" alt="Subjects" style="max-width:100%;height:auto;display:block;margin:0 auto">
      <p style="margin:.5rem 0 .75rem;font-style:italic;font-size:.9rem">Subjects — add any subject and upload your study material</p>
    </div>
    <div class="carousel-slide" style="min-width:100%;text-align:center">
      <img src="/assets/img/ekal-ai/teachers_roster.png" alt="Teachers roster" style="max-width:100%;height:auto;display:block;margin:0 auto">
      <p style="margin:.5rem 0 .75rem;font-style:italic;font-size:.9rem">Teachers roster — design and manage your AI teachers</p>
    </div>
    <div class="carousel-slide" style="min-width:100%;text-align:center">
      <img src="/assets/img/ekal-ai/classroom.png" alt="Classroom" style="max-width:100%;height:auto;display:block;margin:0 auto">
      <p style="margin:.5rem 0 .75rem;font-style:italic;font-size:.9rem">Classroom — attend your personalised class</p>
    </div>
    <div class="carousel-slide" style="min-width:100%;text-align:center">
      <img src="/assets/img/ekal-ai/progress.png" alt="Progress" style="max-width:100%;height:auto;display:block;margin:0 auto">
      <p style="margin:.5rem 0 .75rem;font-style:italic;font-size:.9rem">Progress — topics covered are tracked automatically</p>
    </div>
  </div>
  <button id="carousel-prev" style="position:absolute;top:50%;left:.5rem;transform:translateY(-50%);background:rgba(0,0,0,.45);color:#fff;border:none;border-radius:50%;width:2rem;height:2rem;font-size:1rem;cursor:pointer;line-height:1">&#8249;</button>
  <button id="carousel-next" style="position:absolute;top:50%;right:.5rem;transform:translateY(-50%);background:rgba(0,0,0,.45);color:#fff;border:none;border-radius:50%;width:2rem;height:2rem;font-size:1rem;cursor:pointer;line-height:1">&#8250;</button>
  <div style="text-align:center;padding:.4rem 0">
    <span class="carousel-dots"></span>
  </div>
</div>

<script>
$(function () {
  var track = $('.carousel-track')[0];
  var total = track.children.length;
  var cur = 0;

  var dotsEl = $('.carousel-dots')[0];
  var dots = Array.from({length: total}, function(_, i) {
    var d = document.createElement('span');
    d.style.cssText = 'display:inline-block;width:.5rem;height:.5rem;border-radius:50%;margin:0 .2rem;background:#ccc;cursor:pointer;transition:background .2s';
    $(d).on('click', function() { goTo(i); });
    dotsEl.appendChild(d);
    return d;
  });

  function goTo(n) {
    cur = (n + total) % total;
    var slideWidth = track.parentElement.offsetWidth;
    track.style.transform = 'translateX(-' + (cur * slideWidth) + 'px)';
    dots.forEach(function(d, i) { d.style.background = i === cur ? '#666' : '#ccc'; });
  }

  $('#carousel-prev').on('click', function() { goTo(cur - 1); });
  $('#carousel-next').on('click', function() { goTo(cur + 1); });

  var startX = 0;
  $(track).on('touchstart', function(e) { startX = e.originalEvent.touches[0].clientX; });
  $(track).on('touchend', function(e) {
    var dx = e.originalEvent.changedTouches[0].clientX - startX;
    if (Math.abs(dx) > 40) goTo(cur + (dx < 0 ? 1 : -1));
  });

  goTo(0);
});
</script>
