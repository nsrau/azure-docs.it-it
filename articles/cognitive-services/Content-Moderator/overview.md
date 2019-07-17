---
title: Che cos'è Azure Content Moderator?
titlesuffix: Azure Cognitive Services
description: Informazioni su come usare Content Moderator per tenere traccia, contrassegnare, valutare e filtrare il materiale inappropriato nel contenuto generato dall'utente.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: d814a943bc8dc789abe84b33583714beb998c0ef
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607010"
---
# <a name="what-is-azure-content-moderator"></a>Che cos'è Azure Content Moderator?

L'API Content Moderator di Azure è un servizio cognitivo che consente di controllare il contenuto di testi, immagini e video per individuare materiale potenzialmente offensivo, rischioso o indesiderato. Quando tale materiale viene trovato, il servizio applica al contenuto apposite etichette (flag). L'app può quindi gestire il contenuto contrassegnato per garantire la conformità alle normative o gestire un ambiente con le caratteristiche previste per gli utenti. Per altre informazioni sul significato dei diversi flag per il contenuto, vedere la sezione [API per la moderazione](#moderation-apis).

## <a name="where-it-is-used"></a>Scenari d'uso

Ecco alcuni scenari in cui uno sviluppatore di software o un team potrebbe scegliere di usare Content Moderator:

- Marketplace online che sottopongono a moderazione i cataloghi prodotti e altro contenuto generato dagli utenti.
- Aziende produttrici di giochi che sottopongono a moderazione gli artefatti di gioco generati dagli utenti e le chat room.
- Piattaforme di messaggistica di social networking che sottopongono a moderazione le immagini, il testo e i video aggiunti dagli utenti.
- Grandi aziende nel settore dei media che implementano la moderazione centralizzata del contenuto.
- Provider di soluzioni didattiche per scuola primaria e secondaria che filtrano il contenuto non appropriato per studenti e docenti.

> [!NOTE]
> Non è possibile usare Content Moderator per filtrare immagini correlate allo sfruttamento illegale di minori. Tuttavia, organizzazioni qualificate possono usare il [servizio cloud PhotoDNA](https://www.microsoft.com/photodna "Servizio cloud Microsoft PhotoDNA") per filtrare questo tipo di contenuto.

## <a name="what-it-includes"></a>Cosa include

Il servizio Content Moderator è costituito da diverse API di servizi Web disponibili tramite chiamate REST e un .NET SDK. Include anche lo strumento di revisione umana, che consente a revisori umani di agevolare il servizio e migliorare o ottimizzare la relativa funzione di moderazione.

## <a name="moderation-apis"></a>API per la moderazione

Il servizio Content Moderator include le API per la moderazione, che verificano se nel contenuto è presente materiale potenzialmente inappropriato o sgradevole.

![diagramma a blocchi relativo alle API per la moderazione di Content Moderator](images/content-moderator-mod-api.png)

La tabella seguente descrive i diversi tipi di API per la moderazione.

| Gruppo di API | DESCRIZIONE |
| ------ | ----------- |
|[**Moderazione testo**](text-moderation-api.md)| Analizza il testo per individuare contenuto offensivo, sessualmente esplicito o allusivo, oltre a contenuto volgare e informazioni personali.|
|[**Elenchi di termini personalizzati**](try-terms-list-api.md)| Consente di analizzare il testo in base a un elenco personalizzato di termini oltre ai termini predefiniti. Usare gli elenchi personalizzati per bloccare o consentire il contenuto in base a criteri specifici per il contenuto.|  
|[**Moderazione immagini**](image-moderation-api.md)| Analizza le immagini per individuare eventuale contenuto per adulti o spinto, rileva il testo presente nelle immagini tramite OCR (Optical Character Recognition) e rileva i visi.|
|[**Elenchi di immagini personalizzati**](try-image-list-api.md)| Analizza le immagini in base a un elenco personalizzato di immagini. Usare gli elenchi di immagini personalizzati per filtrare le istanze di contenuti ricorrenti che non si vuole classificare nuovamente.|
|[**Moderazione video**](video-moderation-api.md)| Analizza i video per individuare contenuto per adulti o spinto e restituisce i marcatori temporali per tale contenuto.|

## <a name="review-apis"></a>Verificare le API

Le API di revisione consentono di integrare la pipeline di moderazione con revisori umani. Usare le operazioni [Processi](review-api.md#jobs), [Revisioni](review-api.md#reviews) e [Flusso di lavoro](review-api.md#workflows) per creare e automatizzare flussi di lavoro che prevedono l'intervento umano con lo [strumento di revisione](#the-review-tool) (descritto più avanti).

> [!NOTE]
> L'API del flusso di lavoro non è ancora disponibile in .NET SDK, ma può essere usata con l'endpoint REST.

![diagramma a blocchi relativo alle API di revisione di Content Moderator](images/content-moderator-rev-api.png)

## <a name="the-review-tool"></a>Strumento di revisione

Il servizio Content Moderator include anche lo [strumento di revisione](Review-Tool-User-Guide/human-in-the-loop.md) basato sul Web, che ospita le recensioni di contenuti che i moderatori dovranno elaborare. L'input umano non serve al training del servizio, ma l'azione combinata di servizio e team di revisione umana consente agli sviluppatori di trovare un giusto equilibrio di efficienza e accuratezza. Lo strumento di revisione include anche un front-end intuitivo per un'ampia varietà di risorse di Content Moderator.

![Home page dello strumento di revisione umana di Content Moderator](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Sicurezza e privacy dei dati

Come con tutti i Servizi cognitivi, gli sviluppatori che usano il servizio Content Moderator devono conoscere i criteri di Microsoft sui dati dei clienti. Per altre informazioni, vedere la [pagina sui Servizi cognitivi](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) nel Centro protezione di Microsoft.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare il servizio Content Moderator, seguire le istruzioni in [Acquisire familiarità con Content Moderator](quick-start.md) sul Web.