---
title: Panoramica di Azure Content Moderator | Microsoft Docs
description: Informazioni su come usare Content Moderator per tenere traccia, contrassegnare, valutare e filtrare il contenuto inappropriato nel contenuto generato dall'utente.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/15/2017
ms.author: sajagtap
ms.openlocfilehash: cd9e2e9c10e9dc5ba118c8319f76174bf6f0da9f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377753"
---
# <a name="what-is-content-moderator"></a>Informazioni su Content Moderator

La moderazione del contenuto è il processo di monitoraggio per l'individuazione di contenuto potenzialmente offensivo, indesiderato e rischioso. Il contenuto moderato può essere rappresentato da immagini, testo e video.

## <a name="where-it-is-used"></a>Scenari d'uso

L'elenco seguente include alcuni scenari di esempio in cui viene usato Content Moderator:

- Marketplace online che eseguono la moderazione dei cataloghi di prodotti e del contenuto generato dagli utenti
- Le aziende produttrici di giochi che sottopongono a moderazione gli artefatti di gioco generati dagli utenti e le chat room
- Le piattaforme di messaggistica di social networking che eseguono la moderazione di immagini, testo e video aggiunti dagli utenti
- Le grandi aziende nel settore dei media che implementano la moderazione centralizzata del contenuto
- Provider di soluzioni didattiche per scuola primaria e secondaria che filtrano il contenuto non appropriato e offensivo per studenti e docenti

## <a name="what-it-includes"></a>Cosa include

Content Moderator è costituito da varie API di servizi Web e da uno strumento di revisione predefinito con partecipazione umana che facilita la moderazione di immagini, testo e video.

![Diagramma a blocchi di Content Moderator](images/content-moderator-block-diagram.png)

## <a name="apis"></a>API

Content Moderator include le API seguenti:
  - [**API Moderazione testo**](text-moderation-api.md): usare questa API per analizzare il testo per individuare contenuto blasfemo, esplicito, allusivo, offensivo e informazioni personali.
  - [**API Elenco di termini personalizzati**](try-terms-list-api.md): usare questa API per il rilevamento in base a elenchi personalizzati di termini oltre ai termini predefiniti. Usare questi elenchi per bloccare o consentire il contenuto in base a criteri specifici per il contenuto.  
  - [**API Moderazione immagini**](image-moderation-api.md): usare questa API per esaminare le immagini per individuare eventuale contenuto per adulti e razzista, per il rilevamento di testo nelle immagini tramite OCR (Optical Character Recognition) e per il rilevamento dei visi.
  - [**API Elenco di immagini personalizzato**](try-image-list-api.md): usare questa API per il rilevamento in base a elenchi personalizzati di immagini in contenuto pre-identificato che non è necessario classificare nuovamente.
  - [**API Moderazione video**](video-moderation-api.md): usare questa API per esaminare video per individuare potenziale contenuto per adulti e razzista.
  - [**API Revisione**](try-review-api-job.md): usare le operazioni [Processi](try-review-api-job.md), [Revisione](try-review-api-review.md) e [Flusso di lavoro](try-review-api-workflow.md) per creare e automatizzare flussi di lavoro con contributo umano all'interno dello strumento di revisione.

## <a name="human-review-tool"></a>Strumento di revisione umana

La sottoscrizione di Content Moderator include lo [strumento di revisione umana](Review-Tool-User-Guide/human-in-the-loop.md) predefinito. Usare l'API Revisione indicata in precedenza per creare revisioni di testo, immagini e video per consentire ai moderatori umani di prendere decisioni finali.

![Strumento di revisione dei video di Content Moderator](images/video-review-default-view.png)

## <a name="next-steps"></a>Passaggi successivi

Vedere [Introduzione a Content Moderator](quick-start.md) per iniziare a usare Content Moderator.
