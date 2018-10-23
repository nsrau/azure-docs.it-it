---
title: Che cos'è Azure Content Moderator?
titlesuffix: Azure Cognitive Services
description: Informazioni su come usare Content Moderator per tenere traccia, contrassegnare, valutare e filtrare il materiale inappropriato nel contenuto generato dall'utente.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: overview
ms.date: 10/05/2018
ms.author: sajagtap
ms.openlocfilehash: 5756e8fb451b073c68271359848ab27373ad85ed
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2018
ms.locfileid: "49309553"
---
# <a name="what-is-content-moderator"></a>Informazioni su Content Moderator

La moderazione del contenuto è il processo di monitoraggio di testo, immagini o contenuto video per individuare materiale potenzialmente offensivo, indesiderato o rischioso. Il contenuto contrassegnato può poi essere nascosto o gestito in altro modo per mantenere la conformità alle normative o un ambiente con le caratteristiche previste per gli utenti.

## <a name="where-it-is-used"></a>Scenari d'uso

L'elenco seguente include alcuni scenari di esempio in cui è possibile usare Content Moderator:

- Marketplace online che eseguono la moderazione dei cataloghi di prodotti e del contenuto generato dagli utenti
- Aziende produttrici di giochi che sottopongono a moderazione gli artefatti di gioco generati dagli utenti e le chat room
- Piattaforme di messaggistica di social networking che eseguono la moderazione di immagini, testo e video aggiunti dagli utenti
- Le grandi aziende nel settore dei media che implementano la moderazione centralizzata del contenuto
- Provider di soluzioni didattiche per scuola primaria e secondaria che filtrano il contenuto non appropriato e offensivo per studenti e docenti

## <a name="what-it-includes"></a>Cosa include

Content Moderator è costituito da varie API di servizi Web e da uno strumento di revisione predefinito con partecipazione umana che facilita la moderazione di immagini, testo e video.

![Diagramma a blocchi di Content Moderator](images/content-moderator-block-diagram.png)

### <a name="apis"></a>API

Il servizio Content Moderator include le API seguenti:
  - [**API Moderazione testo**](text-moderation-api.md): usare questa API per analizzare il testo per individuare contenuto blasfemo, esplicito, allusivo, offensivo e informazioni personali.
  - [**API Elenco di termini personalizzati**](try-terms-list-api.md): usare questa API per il rilevamento in base a elenchi personalizzati di termini oltre ai termini predefiniti. Usare questi elenchi per bloccare o consentire il contenuto in base a criteri specifici per il contenuto.  
  - [**API Moderazione immagini**](image-moderation-api.md): usare questa API per esaminare le immagini per individuare eventuale contenuto per adulti e razzista, per il rilevamento di testo nelle immagini tramite OCR (Optical Character Recognition) e per il rilevamento dei visi.
  - [**API Elenco di immagini personalizzato**](try-image-list-api.md): usare questa API per il rilevamento in base a elenchi personalizzati di immagini in contenuto pre-identificato che non è necessario classificare nuovamente.
  - [**API Moderazione video**](video-moderation-api.md): usare questa API per esaminare video per individuare potenziale contenuto per adulti e razzista.
  - [**API Revisione**](try-review-api-job.md): usare le operazioni [Processi](try-review-api-job.md), [Revisione](try-review-api-review.md) e [Flusso di lavoro](try-review-api-workflow.md) per creare e automatizzare flussi di lavoro con contributo umano all'interno dello strumento di revisione.

### <a name="human-review-tool"></a>Strumento di revisione umana

La sottoscrizione di Content Moderator include lo [strumento di revisione umana](Review-Tool-User-Guide/human-in-the-loop.md) predefinito. Usare l'API Revisione indicata in precedenza per creare revisioni di testo, immagini e video per consentire ai moderatori umani di prendere decisioni finali.

![Strumento di revisione dei video di Content Moderator](images/video-review-default-view.png)

## <a name="next-steps"></a>Passaggi successivi

Vedere [Introduzione a Content Moderator](quick-start.md) per iniziare a usare Content Moderator.
