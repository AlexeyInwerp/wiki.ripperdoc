---
date: 2025-08-02
title: Einfache Dokumentation mit Docsy
linkTitle: Docsy-Ankündigung
description: >
  Das Docsy-Hugo-Theme ermöglicht es Projektbetreuern und Mitwirkenden, sich auf Inhalte zu konzentrieren,
  anstatt eine Website-Infrastruktur von Grund auf neu zu erfinden.
author: Riona MacNamara ([@rionam](https://twitter.com/bepsays))
resources:
  - src: "**.{png,jpg}"
    title: "Image #:counter"
    params:
      byline: "Photo: Riona MacNamara / CC-BY-CA"
---

{{% alert title="Übersetzter Platzhalter" color="warning" %}}
Dies ist eine vorläufige, maschinell erzeugte deutsche Übersetzung. Die verbindliche Fassung ist die englische Originalversion: [View in English](/wiki/en/blog/news/first-post/).
{{% /alert %}}

**Dies ist ein typischer Blogbeitrag mit Bildern.**

Das Frontmatter enthält das Datum des Blogbeitrags, seinen Titel, eine kurze Beschreibung für die Blog-Übersichtsseite und den Autor.

## Bilder einbinden

Hier ist ein Bild (`featured-sunset-get.png`) mit Bildunterschrift und Bildnachweis.

{{< imgproc sunset Fill "600x300" >}}
Fetch and scale an image in the upcoming Hugo 0.43.
{{< /imgproc >}}

Das Frontmatter dieses Beitrags legt Eigenschaften fest, die allen Bildressourcen zugewiesen werden:

```
resources:
- src: "**.{png,jpg}"
  title: "Image #:counter"
  params:
    byline: "Photo: Riona MacNamara / CC-BY-CA"
```

Um das Bild in eine Seite einzubinden, werden die Details folgendermaßen angegeben:

```
{{< imgproc sunset Fill "600x300" >}}
Fetch and scale an image in the upcoming Hugo 0.43.
{{< /imgproc >}}
```

Das Bild wird in der im Frontmatter angegebenen Größe und mit dem Bildnachweis gerendert.
