---
title: "A customized subscribe modal popup"
date: 2021-05-07T16:20:05Z
backgroundColor: "#e5e0c9"
backgroundImage: "data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 80 80' width='80' height='80'%3E%3Cpath fill='%2355245a' fill-opacity='1' d='M14 16H9v-2h5V9.87a4 4 0 1 1 2 0V14h5v2h-5v15.95A10 10 0 0 0 23.66 27l-3.46-2 8.2-2.2-2.9 5a12 12 0 0 1-21 0l-2.89-5 8.2 2.2-3.47 2A10 10 0 0 0 14 31.95V16zm40 40h-5v-2h5v-4.13a4 4 0 1 1 2 0V54h5v2h-5v15.95A10 10 0 0 0 63.66 67l-3.47-2 8.2-2.2-2.88 5a12 12 0 0 1-21.02 0l-2.88-5 8.2 2.2-3.47 2A10 10 0 0 0 54 71.95V56zm-39 6a2 2 0 1 1 0-4 2 2 0 0 1 0 4zm40-40a2 2 0 1 1 0-4 2 2 0 0 1 0 4zM15 8a2 2 0 1 0 0-4 2 2 0 0 0 0 4zm40 40a2 2 0 1 0 0-4 2 2 0 0 0 0 4z'%3E%3C/path%3E%3C/svg%3E"
---

Create a new liquid file in your snippets folder called popup.liquid.
Add the following code to the file:

```
<script>
document.addEventListener("DOMContentLoaded", function () {
  let popupdisplayed = sessionStorage.getItem("popupdisplayed");
  const modal = document.querySelector("#modal-id");
  const overlay = document.querySelector("#overlay-id");
  let store = document.querySelectorAll(".setStorage");
  let initialFocus = document.querySelector("#close");
  let fadeIn = {{settings.fadein}} * 1000;
    
  if (popupdisplayed == "true") {
    modal.classList.remove("visible");
    overlay.classList.remove("visible");
  } else {
    setTimeout(function () {
    modal.classList.add("visible");
    overlay.classList.add("visible");
    initialFocus.focus();
  }, fadeIn);    
  }

  store.forEach((stored) => {
    stored.addEventListener("click", (e) => {
      sessionStorage.setItem("popupdisplayed", true);
      modal.classList.remove("visible");
      overlay.classList.remove("visible");
    });
  });
  document.querySelector("#Subscribe").addEventListener("click", (e) => { 
     sessionStorage.setItem("popupdisplayed", true);
  });
  
  document.addEventListener("keydown", (e) => {
    if (event.keyCode == 27) {
      sessionStorage.setItem("popupdisplayed", true);
      modal.classList.remove("visible");
      overlay.classList.remove("visible");
    }
  });
});
   
</script>

<div class="subscribe-overlay setStorage" id="overlay-id"></div>
<div class="subscribe-modal" id="modal-id">
   <button type="button" id="close" class="closeBtn setStorage" data-dismiss="subscribe-modal" aria-label="Close"><span aria-hidden="true">Close</span></button>
    {%- if settings.popup_image -%}
    <img src="{{settings.popup_image | img_url: '400x'}}" alt="test">
   {% else %}
  <div style="height: 2rem;"></div>
    {%- endif -%}
  	<h1>{{settings.popup_title}}</h1>
    <p>{{settings.popup_message}}</p>
  
   {% form 'customer' %}
     {%- if form.errors -%}
        <div class="subscribe-message">
          {{ form.errors | default_errors }}
        </div>
     {%- endif -%}
      {% if form.posted_successfully? %}
 		 <script>
           sessionStorage.setItem("popupdisplayed", false);
         </script>
         <p class="subscribe-message">Thanks for joining</p>
      {% else %}
        <div>
          <input type="hidden" name="contact[tags]" value="newsletter">
          <input type="email"
            name="contact[email]"
            id="Email"
            class="email"
            value="{% if customer %}{{ customer.email }}{% endif %}"
            placeholder="{{ 'general.newsletter_form.email_placeholder' | t }}"
            aria-label="{{ 'general.newsletter_form.email_placeholder' | t }}"
            {%- if form.errors -%}
              aria-invalid="true"
            {%- endif -%}
            autocorrect="off"
            autocapitalize="off"
			required   
            autofocus>
          <span class="">
            <button type="submit" class="button" id="Subscribe">
             {{settings.button_text}}
            </button>
          </span>
        </div>
      {% endif %}
    {% endform %}    
</div>
   
<style>
.subscribe-overlay {
  display: none;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 50;
  background: rgba(0, 0, 0, 0.5);
} 
.subscribe-modal {
  --img-ratio: 3/2;
  display:none;
  width: max(20vw, 400px);
  max-width: 100%;
  max-height: 100%;
  background:white;
  position: fixed;
  z-index: 100;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  flex-direction: column;
  border-radius: 0.5rem;
}
.subscribe-modal > * + * {
  margin-top: 1rem;
}
.subscribe-modal > img {
  height: max(18vh, 12rem);
  object-fit: cover;
  width: 100%;
  margin-top: 0;
  border-radius: 0.5rem;
}
@supports (aspect-ratio: 1) {
  .subscribe-modal > img {
    aspect-ratio: var(--img-ratio);
    height: auto;
  }
}
.subscribe-modal > :not(img) {
  margin-left: 1rem;
  margin-right: 1rem;
}
.subscribe-modal > :last-of-type:not(img, h1, p, input, button) {
  margin-bottom: 1rem;
}
.subscribe-modal h1 {
  text-align: center;
  font-size:1.5rem;
}
.subscribe-modal p {
  text-align: center;
} 
.email {
  width: 100%;
  border: none;
  background: hsl(0 0% 93%);
  border-radius: 0.25rem;
  margin-bottom:0.5rem;
}
.closeBtn {
  position: absolute;
  z-index: 1000;
  top: 1rem;
  right: 0px;
  color: darkgray;
  padding: 5px 10px;
  font-size: 1.2em;
  border: none;
  background: rgba(255, 255, 255, 0);
}
.closeBtn:hover {
  outline: none;
  color: black;
}
 .closeBtn:focus{
  outline: 1px dashed #3e68ff;
  color: black;
}
.visible {
  display: flex;
  animation: fade-in 2s;
}
@keyframes fade-in {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
button.button {
  font-size: 1.5rem;
  border: none;
  background-color: transparent;
  padding: 0;
  cursor: pointer;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  align-self: start;
  background-color: {{settings.button_color}};
  color: {{settings.button_text_color}};
  border-radius: 8px;
  padding: 0.25em 0.75em;
  min-width: 100%;
  min-height: 44px;
  text-align: center;
  line-height: 1.1;
  transition: 220ms all ease-in-out;
}
button.button:hover,
button.button:active{
  background-color: {{settings.button_hover_color}};
  }
button.button:focus {
    outline-style: solid;
    outline-color: transparent;
    box-shadow: 0 0 0 4px #3e68ff;
  }  
  
.subscribe-message {
  border: 1px solid #b8860b;
  background-color: #fff;
  color: #b8860b;
  padding: 1rem 0.5rem;
  font-size: 1.25rem;
}  
</style>

```

Now you need to edit the code in the settings_schema.json file. It's located in your theme's config folder. The formatting of this file can be difficult, you can find Shopify's guidelines here: https://shopify.dev/docs/themes/settings

Adding this code will make the popup content customizable through the theme editor:

```
 {
    "name": {
      "en": "Subscribe Popup"
    },
      "settings": [
      {
        "type": "text",
        "id": "popup_title",
        "default": "Sign-up",
        "label": "Title"
      },
      {
        "type": "textarea",
        "id": "popup_message",
        "default": "Subscribe to our newsletter",
        "label": "Message"
      },
      {
        "type": "text",
        "id": "button_text",
        "default": "SIGN-UP",
        "label": "Title"
      },
      {
        "type": "color",
        "id": "button_color",
        "label": "Button color",
        "default": "#ffa500"
      },
      {
        "type": "color",
        "id": "button_text_color",
        "label": "Button text color",
        "default": "#fff"
      },
      {
        "type": "color",
        "id": "button_hover_color",
        "label": "Button hover color",
        "default": "#ff4500"
      },
      {
        "type": "range",
        "id": "fadein",
        "min": 0,
        "max": 10,
        "step": 1,
        "unit": "sec",
        "label": "Time before popup to appear",
        "default": 4
      },
      {
        "type": "checkbox",
        "id": "show_popup",
        "default": true,
        "label": "Show popup"
      },
      {
        "type": "image_picker",
        "id": "popup_image",
        "label": "Select an image for the popup"
      }
    ]
  },
```
Finally add the following code to your theme.liquid file. Place the code before the closing body tag </body>.

```
  {% assign show-popup = {{settings.show_popup}} %}
  {% if show-popup == true %}
  {% render "popup" %}
  {% endif %}
</body>
```

The popup should now appear in your customizable theme settings:
![shopify theme settings](images/settings.png)

![shopify theme settings](images/customize-popup.png)
