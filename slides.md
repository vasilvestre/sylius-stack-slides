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

# Autonomes

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

- Help PHP developers be productive and enjoy their work

<!--
Maintenant que je vous ai donné le contexte du projet, Sylius/Stack se qualifie comme une collection d'outil
-->

---
layout: center
level: 2
---

# 1. Twig extension

- SortBy
- RouteExists
- TestHtmlAttributeExtension

<!--
Il dispose de package très classiques comme des extensions Twig, ils permettent par exemple :
- d'ajouter des balises twig dans l'env test pour tester plus facilement l'html
-->

---
layout: section
level: 2
---

# 2. Grid/Resource

https://nahan.fr/utilisation-de-gridbundle-hors-de-sylius

<!--
La Grid et le Resource permettent respectivement de créer une grille générique (de type crud admin ou bien front), filtrer, paginer...

La Grid est totalement autonome et permet de gérer tout type de source de données, aussi bien via Doctrine que des fichiers CSV par exemple.

La Resource est autonome aussi mais est complexe à prendre en main sans la Grid. La ressource créer des controleurs agnostique comme fondation à votre site.
-->

---
layout: default
hideInToc: true
---

# Grid/Resource

```php
use Sylius\Resource\Metadata\AsResource;
use Sylius\Resource\Metadata\BulkDelete;
use Sylius\Resource\Metadata\Create;
use Sylius\Resource\Metadata\Index;
use Sylius\Resource\Metadata\Update;

#[AsResource(
    section: 'admin',
    formType: UserType::class,
    templatesDir: '@SyliusAdminUi/crud',
    operations: [
        new Create(),
        new Update(),
        new Index(),
        new BulkDelete(),
    ]
)]
class MyEntity
{
}
```

---
layout: default
hideInToc: true
---

# Grid/Resource

```php
use Sylius\Resource\Metadata\AsResource;
use Sylius\Resource\Metadata\BulkDelete;
use Sylius\Resource\Metadata\Create;
use Sylius\Resource\Metadata\Index;
use Sylius\Resource\Metadata\Update;

#[AsResource(
    section: 'admin',
    formType: UserType::class,
    templatesDir: '@SyliusAdminUi/crud',
    routePrefix: '/admin',
    operations: [
        new Create(),
        new Update(formType: UpdateUserType::class),
        new Index(grid: CollaboratorGrid::class),
        new BulkDelete(),
    ],
    alias: 'app.collaborator',
)]
class MyEntity
{
}
```

---
layout: section
level: 2
---

# 3. Admin/BootstrapAdmin

<!--
L'admin est un composant qui crée un admin très léger/vide au avec la configuration de menu, un visuel, des templates de base à étendre.

Pour BootstrapAdmin, la solution est la même mais avec bootstrap déjà en place pour avoir une base usable très rapidement
-->

---
layout: section
hideInToc: true
---

# Admin

<img src="/simple_admin.png" alt="Empty/blank admin" />

<!--
C'est pas terrible hein ?

La debug bar nous apprend qu'on a un login, un admin, des templates, des twig hooks mais rien de s'affiche.
Voyons comment résoudre ça !
-->

---
layout: center
hideInToc: true
---

# Bootstrap admin

```bash
composer require sylius/bootstrap-admin-ui
```

---
layout: section
hideInToc: true
---

# Bootstrap admin

<img src="/admin_login.png" alt="Simple admin login" />

<!--
Bootstrap admin est en fait un "thème" de l'admin qui configure les templates, blocks etc..

A noter que le login ici ne fait rien, il n'existe que comme base pour construire votre login.
-->

---
layout: section
hideInToc: true
---

# Bootstrap admin

<img src="/admin.png" alt="Simple admin" />

<!--
Nous disposons d'un menu, d'un header, d'une structure de page donc et configurable
-->

---
layout: section
level: 2
---

# 4. TwigHooks

Au lieu des template events

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

```yaml
# config/packages/sylius_ui.yaml
sylius_ui:
    events:
        sylius.shop.layout.after_header:
            blocks:
                my_block_name: 'block.html.twig'
# remplacement
        sylius.shop.layout.header.grid:
          blocks:
            logo: 'logo.html.twig'
```

<!--
Pour customiser, il est vous est demandé de vous reporter au ThemeBundle qui s'occupe normalement au sein de Sylius
de gérer les templates events.
-->
---
layout: center
hideInToc: true
---

# Twig hooks

<img src="/hook_workflow.png" alt="Twig hooks" style="
height: 400px;
">

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

```twig
<!-- Template content -->
  <div id="container">
  {% hook 'hook_name' with { _prefixes: ['custom_prefix_not_related_with_a_parent'] } %}
  </div>
<!-- Additional content -->
```

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
- pouvoir l'utiliser en dehors de Sylius
- intégrer Symfony UX et des composants Twig nativement

Différences :
- composant standalone
- fonctionne avec les components
- va plus loin grâce aux préfixes pour éviter les collisions

<!--
standalone donc potentiellement utilisable pour de présents ou futurs frameworks, cms..
-->

---
layout: center
hideInToc: true
---

# Étendre l'admin avec des Twig hooks

<!--
-->

---
layout: center
hideInToc: true
---

# Twig hooks & admin

<img src="/sidebar_what_hook.png" alt="Twig hooks" />

<!--
Voici comment override un hook dans l'admin, on repère quel est le hook concerné et son path
-->

---
layout: center
hideInToc: true
---

# Twig hooks & admin

```twig
# templates/bundles/SyliusBootstrapAdminUiBundle/shared/crud/common/sidebar/logo.html.twig

{% set dashboard_path = hookable_metadata.context.routing.dashboard_path|default('/admin') %}

<h1 class="navbar-brand">
    <a href="{{ dashboard_path }}">
        Coucou Exotec :)
    </a>
</h1>
```

---
layout: center
hideInToc: true
---

# Twig hooks & admin

<img src="/sidebar_template.png" alt="Twig hooks" />

<!--
On peut remplacer le lien via templates/bundles/
-->

---
layout: center
hideInToc: true
---

# Twig hooks & admin

```yml
# config/packages/sylius_twig_hooks.yaml
sylius_twig_hooks:
  hooks:
    'sylius_admin.common.component.sidebar':
      logo:
        template: 'admin/sidebar/logo.html.twig'
        priority: 0
```

<!--
On peut remplacer le lien via templates/bundles/
-->

---
layout: center
hideInToc: true
---

# Twig hooks & admin

<img src="/sidebar_hook.png" alt="Twig hooks" />

<!--
On peut remplacer le lien via templates/bundles/
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

Hooks : il pourrait servir de base pour des projets qui proposerait des UI modulables et extensibles

---
layout: section
hideInToc: true
---

# Conclusion
Monofony est mort, vive Monofony ! (et Sylius/Stack)

Peu de documentation

Les TwigHooks sont peu matures

Sylius rend à l'écosystème Symfony

---
layout: end
hideInToc: true
---

<!--
Remercier Akawaka, le staff notamment Thibault et Grégoire Hebert
-->
