---
title: Home
layout: home
nav_order: 0
---

<button class="btn js-toggle-dark-mode">Preview dark color scheme</button>

<script>
function toggleDarkLightMode() {
  const toggleDarkMode = document.querySelector('.js-toggle-dark-mode');

  jtd.addEvent(toggleDarkMode, 'click', function(){
    if (jtd.getTheme() === 'dark') {
      jtd.setTheme('light');
      toggleDarkMode.textContent = 'Preview dark color scheme';
    } else {
      jtd.setTheme('dark');
      toggleDarkMode.textContent = 'Return to the light side';
    }
  });
}
</script>

![](https://github.com/acidanthera/OpenCorePkg/raw/master/Docs/Logos/Logo.png)

This website aims to give a better readable version of [OpenCorePkg Configuration.pdf](https://github.com/acidanthera/OpenCorePkg/blob/master/Docs/Configuration.pdf)
