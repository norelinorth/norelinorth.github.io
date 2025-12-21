---
layout: page
title: Contact
permalink: /contact/
---

<h3>Send me a message!</h3>
<p>I'd love to hear from you. Just fill out the form below, and I'll get back to you as soon as possible.</p>

<!-- Your finished Formspree Form -->
<form
  action="https://formspree.io/f/xaqwjllg"
  method="POST"
>
  <label style="display: block;">
    Your Name:
    <input type="text" name="name" required style="width: 100%; padding: 8px; margin-top: 5px; border: 1px solid #ccc; border-radius: 4px;">
  </label>
  
  <label style="display: block; margin-top: 1rem;">
    Your Email (so I can reply):
    <input type="email" name="_replyto" required style="width: 100%; padding: 8px; margin-top: 5px; border: 1px solid #ccc; border-radius: 4px;">
  </label>
  
  <label style="display: block; margin-top: 1rem;">
    Your Message:
    <textarea name="message" rows="6" required style="width: 100%; padding: 8px; margin-top: 5px; border: 1px solid #ccc; border-radius: 4px;"></textarea>
  </label>

  <!-- Your submit button -->
  <button type="submit" style="margin-top: 1.5rem; padding: 10px 15px; background-color: #007bff; color: white; border: none; border-radius: 4px; cursor: pointer;">
    Send Message
  </button>
</form>
