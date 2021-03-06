---
title: "Adding a Customized Size Swatch"
date: 2020-12-30T16:20:14Z
backgroundColor: "#e5e0c9"
backgroundImage: "data:image/svg+xml,%3Csvg width='42' height='44' viewBox='0 0 42 44' xmlns='http://www.w3.org/2000/svg'%3E%3Cg id='Page-1' fill='none' fill-rule='evenodd'%3E%3Cg id='brick-wall' fill='%2355245a' fill-opacity='1'%3E%3Cpath d='M0 0h42v44H0V0zm1 1h40v20H1V1zM0 23h20v20H0V23zm22 0h20v20H22V23z'/%3E%3C/g%3E%3C/g%3E%3C/svg%3E"
---

{{< aria-hidden-span >}}{{< drop-cap >}}
M{{< /drop-cap >}}any{{< /aria-hidden-span >}} {{< sr-only-span >}}Many{{< /sr-only-span >}} themes come with a dropdown as the default size selector. Here's how to change this to, for example, a list of buttons, similar to a color swatch. In this example I'm using Shopify's Venture theme, but it should be similar for most other themes.

In the **product-template.liquid** file in the **sections folder** search for the code beginning with:

```
 {% unless product.has_only_default_variant %}
```

and ending with :

```
{% endunless %}
```

and replaace everything between with this code:

```
 {% unless product.has_only_default_variant %}
            {% for option in product.options_with_values %}

              <div class="selector-wrapper js product-form__item">

                {% if option.name == "Size" %}
                <label>{{ option.name }}</label>

                {% assign index = forloop.index %}

                <div class="size-swatch">
                {% for value in option.values %}

                    {% assign variant_label_state = true %}
                      {% if product.options.size == 1 %}
                      {% unless product.variants[forloop.index0].available  %}
                      {% assign variant_label_state = false %}
                      {% endunless %}
                      {% endif %}

                	<input class="single-option-selector-{{ section.id }}"
                           id="size-{{forloop.index}}"
                           type="radio"
                           name="size"
                           value="{{ value | escape }}"
                           data-index="option{{ index }}"
                           {% if option.selected_value == value %} checked="checked"{% endif %}
                           {% unless variant_label_state %} disabled="disabled"{% endunless %}
              		/>
                    <label for="size-{{forloop.index}}">{{value}}</label>

                {% endfor %}
                </div>

                {% else %}
                <!-- default selector start-->
                <label {% if option.name == 'default' %}class="label--hidden" {% endif %}for="SingleOptionSelector-{{ section.id }}-{{ forloop.index0 }}">{{ option.name }}</label>
                <select class="single-option-selector single-option-selector-{{ section.id }} product-form__input"
                  id="SingleOptionSelector-{{ forloop.index0 }}"
                  data-name="{{ option.name }}"
                  data-index="option{{ forloop.index }}">
                  {% for value in option.values %}
                    <option value="{{ value | escape }}"{% if option.selected_value == value %}
                            selected="selected"{% endif %}>{{ value }}</option>
                  {% endfor %}
                </select>
                 <!-- default selector end-->
                {% endif %}

              </div>
            {% endfor %}
          {% endunless %}
```

Add styling to the end of the theme.css file in the assets folder; something like this:

```
                  .size-swatch{
                    display: flex;

                  }
                  .size-swatch input{
                    opacity: 0;
                    width: 0;
                    height: 0;
                  }
                  .size-swatch label{
                    padding:1em;
                    max-height:44px;
                    border: 1px solid lightgray;
                    display: block;


                  }
                 .size-swatch input:disabled + label {
                   opacity: 0.35;
                   cursor: default;
                   text-decoration: line-through;
                  }

                  .size-swatch input:focus + label {
                    box-shadow: 0 0 0 0.05em #fff, 0 0 0.15em 0.1em green;
                  }

```
