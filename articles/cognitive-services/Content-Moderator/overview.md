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
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: f7fef00cfff9295036d7545470f86e27314e6451
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258699"
---
# <a name="what-is-azure-content-moderator"></a>Che cos'è Azure Content Moderator?

L'API Content Moderator di Azure è un servizio cognitivo che consente di controllare il contenuto di testi, immagini e video per individuare materiale potenzialmente offensivo, rischioso o indesiderato. Quando tale materiale viene trovato, il servizio applica al contenuto apposite etichette (flag). L'app può quindi gestire il contenuto contrassegnato per garantire la conformità alle normative o gestire un ambiente con le caratteristiche previste per gli utenti. Per altre informazioni sul significato dei diversi flag per il contenuto, vedere la sezione [API di Content Moderator](#content-moderator-apis).

## <a name="where-it-is-used"></a>Scenari d'uso

Ecco alcuni scenari in cui uno sviluppatore di software o un team potrebbe scegliere di usare Content Moderator:

- Marketplace online che sottopongono a moderazione i cataloghi dei prodotti e altro contenuto generato dagli utenti
- Aziende produttrici di giochi che sottopongono a moderazione gli artefatti di gioco generati dagli utenti e le chat room
- Piattaforme di messaggistica di social networking che eseguono la moderazione di immagini, testo e video aggiunti dagli utenti
- Grandi aziende nel settore dei media che implementano la moderazione centralizzata del contenuto
- Provider di soluzioni didattiche per scuola primaria e secondaria che filtrano il contenuto non appropriato per studenti e docenti

## <a name="what-it-includes"></a>Cosa include

Il servizio Content Moderator è costituito da diverse API di servizi Web disponibili tramite chiamate REST e un .NET SDK. Include anche lo strumento di revisione umana, che consente a revisori umani di agevolare il servizio e migliorare o ottimizzare la relativa funzione di moderazione.

![Diagramma a blocchi relativo a Content Moderator che mostra le API di moderazione, le API di revisione e lo strumento di revisione umana](images/content-moderator-block-diagram.png)

### <a name="content-moderator-apis"></a>API di Content Moderator

Il servizio Content Moderator include le API per gli scenari seguenti.

| Azione | DESCRIZIONE |
| ------ | ----------- |
|[**Moderazione testo**](text-moderation-api.md)| Analizza il testo per individuare contenuto offensivo, sessualmente esplicito o allusivo, oltre a contenuto volgare e informazioni personali.|
|[**Elenchi di termini personalizzati**](try-terms-list-api.md)| Consente di analizzare il testo in base a un elenco personalizzato di termini oltre ai termini predefiniti. Usare gli elenchi personalizzati per bloccare o consentire il contenuto in base a criteri specifici per il contenuto.|  
|[**Moderazione immagini**](image-moderation-api.md)| Analizza le immagini per individuare eventuale contenuto per adulti o spinto, rileva il testo presente nelle immagini tramite OCR (Optical Character Recognition) e rileva i visi.|
|[**Elenchi di immagini personalizzati**](try-image-list-api.md)| Analizza le immagini in base a un elenco personalizzato di immagini. Usare gli elenchi di immagini personalizzati per filtrare le istanze di contenuti ricorrenti che non si vuole classificare nuovamente.|
|[**Moderazione video**](video-moderation-api.md)| Analizza i video per individuare contenuto per adulti o spinto e restituisce i marcatori temporali per tale contenuto.|
|[**Revisione**](try-review-api-job.md)| Usare le operazioni [Processi](try-review-api-job.md), [Revisione](try-review-api-review.md) e [Flusso di lavoro](try-review-api-workflow.md) per creare e automatizzare flussi di lavoro human-in-the-loop con lo strumento di revisione umana. L'API di Flusso di lavoro non è ancora disponibile tramite .NET SDK.|

### <a name="human-review-tool"></a>Strumento di revisione umana

Il servizio Content Moderator include anche lo [strumento di revisione umana](Review-Tool-User-Guide/human-in-the-loop.md) basato sul Web. 

![Home page dello strumento di revisione umana di Content Moderator](images/homepage.PNG)

È possibile usare le API di revisione per configurare revisioni del team per il contenuto di testi, immagini e video in base a filtri specificati. In questo modo i moderatori umani potranno prendere la decisione finale in merito alla moderazione. L'input umano non serve al training del servizio, ma l'azione combinata di servizio e team di revisione umana consente agli sviluppatori di trovare un giusto equilibrio di efficienza e accuratezza.

## <a name="data-privacy-and-security"></a>Sicurezza e privacy dei dati
Come con tutti i Servizi cognitivi, gli sviluppatori che usano il servizio Content Moderator devono conoscere i criteri di Microsoft sui dati dei clienti. Per altre informazioni, vedere la [pagina sui Servizi cognitivi](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) nel Centro protezione di Microsoft.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare il servizio Content Moderator, seguire le istruzioni in [Acquisire familiarità con Content Moderator](quick-start.md) sul Web.
