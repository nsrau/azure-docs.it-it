---
title: Connettersi ai feed RSS da App per la logica di Azure | Microsoft Docs
description: Automatizzare le attività e i flussi di lavoro per il monitoraggio e la gestione dei feed RSS con App per la logica di Azure
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.assetid: a10a6277-ed29-4e68-a881-ccdad6fd0ad8
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 70b250074395977f70ac1b3eb0ce3ffdc96fced1
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050838"
---
# <a name="manage-rss-feeds-by-using-azure-logic-apps"></a>Gestire i feed RSS con App per la logica di Azure

Con App per la logica di Azure e il connettore RSS, è possibile creare attività e flussi di lavoro automatizzati per qualsiasi feed RSS, ad esempio:

* Eseguire il monitoraggio quando vengono pubblicati gli elementi del feed RSS.
* Elencare tutti gli elementi del feed RSS.

RSS (Rich Site Summary), noto anche come Really Simple Syndication, è un formato comune per la diffusione Web e viene usato per la pubblicazione di contenuto aggiornato di frequente, ad esempio post di blog e notizie. Molti autori di contenuto offrono feed RSS per consentire agli utenti di eseguire la sottoscrizione al contenuto. 

È possibile usare un trigger RSS che ottiene le risposte da un feed RSS e rende l'output disponibile per altre azioni. È possibile usare un'azione RSS nelle app per la logica per eseguire un'attività con il feed RSS. Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/). 

* L'URL per un feed RSS.

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L'app per la logica in cui si vuole accedere al feed RSS. Per iniziare con un trigger di RSS, [creare un'app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per usare un'azione di RSS, avviare l'app per la logica con un altro trigger, ad esempio, il trigger **Ricorrenza**.

## <a name="connect-to-an-rss-feed"></a>Connettersi a un feed RSS

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Scegliere un percorso: 

   * Per le app per la logica vuote, nella casella di ricerca immettere "rss" come filtro. Nell'elenco dei trigger selezionare il trigger desiderato. 

     -oppure-

   * Per le app per la logica esistenti, nel passaggio in cui si vuole aggiungere un'azione, scegliere **Nuovo passaggio**. Nella casella di ricerca immettere "rss" come filtro. Nell'elenco delle azioni selezionare l'azione desiderata.

1. Specificare i dettagli necessari per l'azione o il trigger selezionato e continuare a creare il flusso di lavoro dell'app per la logica.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche su trigger, azioni e limiti, illustrati dalla descrizione OpenAPI (in precedenza Swagger) del connettore, esaminare la [pagina di riferimento](/connectors/rss/) del connettore.

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)