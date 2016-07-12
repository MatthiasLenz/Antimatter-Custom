# Mobile menu for single page layout

On mobile the Antimatter theme uses a menu button which opens a menu sidebar. 
If you are using a modular page with `onpage_menu: true` the desktop menu shows the modules which are part of your modular pages.
On mobile, this menu only shows the normal site menu, so if you have a single page layout, the menu only has the **Home** option.

##How to fix it

### base.html.twig
First we wrap the slidebar code in a block, so that we can modify it in our modular.html.twig
```PHP
{% block slidebar %}
  <div class="sb-slidebar sb-left sb-width-thin">
      <div id="panel">
      {% include 'partials/navigation.html.twig' %}
      </div>
  </div>
{% endblock %}
```

### navigation_modular.html.twig
A modified navigation twig, which we use on the mobile site. We replace 
```PHP
{% for page in pages.children.visible %}
  {% set current_page = (page.active or page.activeChild) ? 'active' : '' %}
  <li class="{{ current_page }}">
      <a href="{{ page.url }}">
          {% if page.header.icon %}<i class="fa fa-{{ page.header.icon }}"></i>{% endif %}
          {{ page.menu }}
      </a>
  </li>
{% endfor %}
```
with
```PHP
{% for module in page.collection() %}
  {% set current_module = (module.active or module.activeChild) ? 'active' : '' %}
  <li class="sb-close {{ current_module }}"><a data-toggle="collapse" data-target=".navbar-collapse.in"
    href="#{{ _self.pageLinkName(module.menu) }}">{{  module.menu }}</a></li>
{% endfor %}
```
so that it loops through the modules in `page.collection()` instead of the page in `page.children`.

### modular.html.twig
We need to tell our modular twig to use the new navigation twig, for this we override the new slidebar block in base.html.twig
```PHP
{% block slidebar %}
  <div class="sb-slidebar sb-left sb-width-thin">
    <div id="panel">
      {% if show_onpage_menu %}
        {% include 'partials/navigation_modular.html.twig' %}
      {% else %}
        {% include 'partials/navigation.html.twig' %}
      {% endif %}
    </div>
  </div>
{% endblock %}
```
