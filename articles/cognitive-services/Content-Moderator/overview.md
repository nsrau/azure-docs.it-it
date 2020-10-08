---
title: Che cos'è Azure Content Moderator?
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare Content Moderator per tenere traccia, contrassegnare, valutare e filtrare il materiale inappropriato nel contenuto generato dall'utente.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 09/15/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: content moderator, azure content moderator, moderazione online, software di filtro dei contenuti, servizio di moderazione del contenuto, moderazione del contenuto
ms.openlocfilehash: aa1f4c1fab9b3d8a1b5db0e965f1608e694646b2
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91596860"
---
# <a name="what-is-azure-content-moderator"></a>Che cos'è Azure Content Moderator?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Content Moderator è un servizio di intelligenza artificiale che consente di gestire i contenuti potenzialmente offensivi, rischiosi o in altro modo indesiderati. Include il servizio di moderazione del contenuto basato sull'intelligenza artificiale che analizza testo, immagini e video e applica automaticamente i flag di contenuto, oltre allo strumento di revisione, un ambiente di moderazione online per un team di revisori umani.

È possibile integrare un software di filtro dei contenuti nell'app per garantire la conformità con le normative o mantenere l'ambiente previsto per gli utenti.

## <a name="where-its-used"></a>Scenari di utilizzo

Ecco alcuni scenari in cui uno sviluppatore di software o un team potrebbe aver bisogno di un servizio di moderazione del contenuto:

- Marketplace online che sottopongono a moderazione i cataloghi prodotti e altro contenuto generato dagli utenti.
- Aziende produttrici di giochi che sottopongono a moderazione gli artefatti di gioco generati dagli utenti e le chat room.
- Piattaforme di messaggistica di social networking che sottopongono a moderazione le immagini, il testo e i video aggiunti dagli utenti.
- Grandi aziende nel settore dei media che implementano la moderazione centralizzata del contenuto.
- Provider di soluzioni didattiche per scuola primaria e secondaria che filtrano il contenuto non appropriato per studenti e docenti.

> [!IMPORTANT]
> Non è possibile usare Content Moderator per filtrare immagini correlate allo sfruttamento illegale di minori. Tuttavia, le organizzazioni idonee possono usare il [servizio cloud PhotoDNA](https://www.microsoft.com/photodna "Servizio cloud Microsoft PhotoDNA") per filtrare questo tipo di contenuto.

## <a name="what-it-includes"></a>Cosa include

Il servizio Content Moderator è costituito da diverse API di servizi Web disponibili tramite chiamate REST e un .NET SDK. Include anche uno strumento di revisione che consente a revisori umani di supportare il servizio e migliorarne o ottimizzarne la funzione di moderazione.

## <a name="moderation-apis"></a>API per la moderazione

Il servizio Content Moderator include le API per la moderazione, che verificano se nel contenuto è presente materiale potenzialmente inappropriato o sgradevole.

![diagramma a blocchi relativo alle API per la moderazione di Content Moderator](images/content-moderator-mod-api.png)

La tabella seguente descrive i diversi tipi di API per la moderazione.

| Gruppo di API | Descrizione |
| ------ | ----------- |
|[**Moderazione testo**](text-moderation-api.md)| Analizza il testo per individuare contenuto offensivo, sessualmente esplicito o allusivo, oltre a contenuto volgare e informazioni personali.|
|[**Elenchi di termini personalizzati**](try-terms-list-api.md)| Consente di analizzare il testo in base a un elenco personalizzato di termini unitamente ai termini predefiniti. Usare gli elenchi personalizzati per bloccare o consentire il contenuto in base a criteri specifici per il contenuto.|  
|[**Moderazione immagini**](image-moderation-api.md)| Analizza le immagini per individuare eventuale contenuto per adulti o spinto, rileva il testo presente nelle immagini tramite OCR (Optical Character Recognition) e rileva i visi.|
|[**Elenchi di immagini personalizzati**](try-image-list-api.md)| Analizza le immagini in base a un elenco personalizzato di immagini. Usare gli elenchi di immagini personalizzati per filtrare le istanze di contenuti ricorrenti che non si vuole classificare nuovamente.|
|[**Moderazione video**](video-moderation-api.md)| Analizza i video per individuare contenuto per adulti o spinto e restituisce i marcatori temporali per tale contenuto.|

## <a name="review-apis"></a>Verificare le API

Le API di revisione consentono di integrare la pipeline di moderazione con revisori umani. Usare le operazioni [Processi](review-api.md#jobs), [Revisioni](review-api.md#reviews) e [Flusso di lavoro](review-api.md#workflows) per creare e automatizzare flussi di lavoro che prevedono l'intervento umano con lo [strumento di revisione](#review-tool) (descritto più avanti).

> [!NOTE]
> L'API del flusso di lavoro non è ancora disponibile in .NET SDK, ma può essere usata con l'endpoint REST.

![diagramma a blocchi relativo alle API di revisione di Content Moderator](images/content-moderator-rev-api.png)

## <a name="review-tool"></a>Strumento di revisione

Il servizio Content Moderator include anche lo [strumento di revisione](Review-Tool-User-Guide/human-in-the-loop.md) basato sul Web, che ospita le recensioni di contenuti che i moderatori dovranno elaborare. L'input umano non serve al training del servizio, ma l'azione combinata di servizio e team di revisione umana consente agli sviluppatori di trovare un equilibrio corretto tra efficienza e accuratezza. Lo strumento di revisione include anche un front-end intuitivo per numerose risorse di Content Moderator.

![Home page dello strumento di revisione di Content Moderator](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Sicurezza e privacy dei dati

Come con tutti i Servizi cognitivi, gli sviluppatori che usano il servizio Content Moderator devono conoscere i criteri di Microsoft sui dati dei clienti. Per altre informazioni, vedere la [pagina sui Servizi cognitivi](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) nel Centro protezione di Microsoft.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare Content Moderator nel portale Web, vedere [Provare Content Moderator sul Web](quick-start.md). Oppure, completare una [guida di avvio rapido sulla libreria client](client-libraries.md) per implementare gli scenari di base nel codice.