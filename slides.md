---
titleTemplate: '%s'
theme: seriph
background: https://cover.sli.dev
highlighter: shiki
lineNumbers: true
info: false
css: unocss
hideInToc: true
---

# Sylius/Stack

et son impact sur l'écosystème Symfony

<!--
Bonjour, dans cette présentation je vais vous parler d'un nouveau repository à l'initiative de Sylius et son impact sur l'écosystème Symfony.
-->

---
layout: center
hideInToc: true
---

# De quoi on parle ?

<Toc />

<!--
Nous allons parler en gros, les raisons de son existence, qu'est-ce qu'il propose et nous allons clore le sujet sur son avenir
-->

---
layout: center
class: text-center
src: ./pages/presentations.md
---

---
layout: section
level: 1
---

# Pourquoi ça existe ?

<!--
Tout d'abord pourquoi ça existe ?

Est-ce que tout le monde sait ce qu'est Sylius ici ?
-->

---
layout: section
level: 2
---

# Composants de Sylius

Composants utilisés et éprouvés par Sylius depuis 9 ans

<!--
Ce qui compose Sylius/Stack ce sont des composants ou parties de Sylius qui ont pu être extrait depuis peu.
Certaines composants sont la depuis quasiment le début de Sylius, ils ont/sont responsables de la DX qu'on apprécié souvent tant dans Sylius.
-->

---
layout: section
level: 2
---

# ... réutilisables

Non liés à Sylius (sylius/sylius) donc réutilisables

<!--
Avec des efforts depuis des années de la part de Sylius et Loïc Frémont, ces composants sont utilisables en dehors de Sylius sur des projets.

En parlant de Loïc, certains savent peut-être qu'il a mené un projet nommé Monofony, monofony étant "Sylius sans l'ecommerce".
-->

---
layout: section
level: 2
---

# Comme Monofony

Et pourquoi l'oublier aussi tôt ?

[//]: # (Monofony devient des pack de code &#40;pour créer du CQRS actuellement&#41;)

<!--
Vous pouvez désormais l'oublier sa solution.

Si un effort a du être fait sur les composants, c'est parceque Monofony n'était pas parfait, il nécessitait du boiler code, il était difficile a installer sur un projet en cours, le code pouvait facilement créer des collisions et n'existe désormais plus dans sa forme tel qu'on la connait
-->

---
layout: section
title: Sylius/Stack est une collection d'outil
level: 1
---

# Sylius/Stack est une collection d'outil

<!--
Maintenant que je vous ai donné le contexte du projet, Sylius/Stack se qualifie comme une collection d'outil
-->

---
layout: section
level: 2
---

# Pour des choses simples

twig extension (balise de test par exemple)

<!--
Il dispose de package très classiques comme des extensions Twig, ils permettent par exemple :
- d'ajouter des balises twig dans l'env test pour tester plus facilement l'html
-->

---
layout: section
level: 2
---

# Grid/Resource

Grid autonome mais + pratique avec Sylius/Resource (pas forcément lié a doctrine)
https://nahan.fr/utilisation-de-gridbundle-hors-de-sylius

<!--
La Grid et le Resource permettent respectivement de créer une grille (de type crud admin ou bien front) et de générer des URLS, indiquer des formulaires, processer des formulaires, trouver des templates automatiquement...

La Grid est totalement autonome et permet de gérer tout type de source de données, aussi bien via Doctrine que des fichiers CSV par exemple.

La resource est autonome mais fonctionne clef en main avec le bundle de Resource et permet une génération complète de Crud très aisément.
-->

---
layout: section
level: 2
---

# Admin/BootstrapAdmin

L'admin simple et via bootstrap

bootstrap utilise le symfony/ux

<!--
L'admin est un composant qui crée un admin très léger/vide au avec la configuration de menu, un visuel, des templates de base à étendre.

Pour BootstrapAdmin, la solution est la même mais avec bootstrap déjà en place pour avoir une base usable très rapidement
-->

---
layout: section
level: 2
---

# TwigHooks

Au lieu des template event

<!--
Pas très mature

l'impact que ça pourrait avoir pour de nombreux projets (netgen, cms bolt, etc etc)
-->

---
layout: center
hideInToc: true
---

# Template event

```php
{# First we are setting the event_prefix based on route as it was mentioned before #}
{% set event_prefix = metadata.applicationName ~ '.admin.' ~ metadata.name ~ '.create' %}

{# And then the slot name is appended to the event_prefix #}
{{ sylius_template_event([event_prefix, 'sylius.admin.create'], _context) }}
```

---
layout: center
hideInToc: true
---

# Twig hooks

```twig
<!-- Template content -->
  <div id="container">
  {% hook 'hook_name' with { _prefixes: ['custom_prefix_not_related_with_a_parent'] } %}
  </div>
<!-- Additional content -->
```

<!--
Hooks inherit their parent names and append their own. 

Thanks to this mechanism, it is not necessary to write the entire composite name in the hook. 

If the hook is fired inside another hookable, the configuration must reflect that. For instance:
-->
---
layout: center
hideInToc: true
---

# Twig hooks

<img height="300px" width="800px" src="/hook_workflow.png" alt="Twig hooks" />

---
layout: center
hideInToc: true
---

# Twig hooks

```yml
sylius_twig_hooks:
  hooks:
    'sylius_admin.common.index':
      sidebar:
        template: '@SyliusAdmin/shared/crud/common/sidebar.html.twig'
```

---
layout: center
hideInToc: true
---

# Twig hooks

```php
namespace App\Twig\Components;

use Symfony\UX\TwigComponent\Attribute\AsTwigComponent;

#[AsTwigComponent]
final class MyComponent
{
}
```

```yml
sylius_twig_hooks:
  hooks:
    'my_hook':
      'my_hookable':
        component: 'MyComponent'
```

---
layout: center
hideInToc: true
---

# Twig hooks

Motivation & différences

Motivation :
- le rendre autonome
- pouvoir l'utiliser en dehors de Sylius

Différences :
- va plus loin grâce aux préfixes pour éviter les collisions et de rajouter des strings entre deux
- fonctionne avec les components
- composant standalone

<!--
standalone donc potentiellement utilisable pour de présents ou futurs frameworks
-->

---
layout: section
title: Son futur & conclusion
level: 1
---

# Son futur & conclusion

---
layout: section
hideInToc: true
---

# Son futur

(possible)

Behat-bridge : pouvoir tester facilement le BO

Front : avec des bases du login et des twig hooks pour étendre

---
layout: section
hideInToc: true
---

# Conclusion
Monofony est mort, vive Monofony ! (et Sylius/Stack)

Peu de docs

TwigHooks sont peu matures

Points positifs pour l'écosystème Symfony

---
layout: end
hideInToc: true
---

<!--
Remercier Akawaka, le staff notamment Thibault et Grégoire Hebert
-->
