---
title: Eseguire il debug delle API con la traccia delle richieste in Gestione API di Azure | Microsoft Docs
description: Eseguire le procedure di questa esercitazione per esaminare l'elaborazione delle richieste con Gestione API di Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: ff3dde8ac95b678866ba6f5216ba23357b067765
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203534"
---
# <a name="debug-your-apis-using-request-tracing"></a>Eseguire il debug delle API con la traccia delle richieste

Questa esercitazione descrive come esaminare l'elaborazione delle richieste per aiutare gli sviluppatori a eseguire il debug e la risoluzione dei problemi correlati alle API. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Tenere traccia di una chiamata

![Controllo API](media/api-management-howto-api-inspector/api-inspector001.PNG)

## <a name="prerequisites"></a>Prerequisiti

+ Acquisire familiarità con la [terminologia di Gestione API di Azure](api-management-terminology.md).
+ Completare l'argomento di avvio rapido seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).
+ Completare anche l'esercitazione seguente: [Importare e pubblicare la prima API](import-and-publish.md)

## <a name="trace-a-call"></a>Tenere traccia di una chiamata

![Tenere traccia delle API](media/api-management-howto-api-inspector/06-DebugYourAPIs-01-TraceCall.png)

1. Selezionare **API**.
2. Fare clic su **Demo Conference API** (API Demo Conference) nell'elenco di API.
3. Passare alla scheda **Test**.
4. Selezionare l'operazione **GetSpeakers**.
5. Includere un'impostazione HTTP denominata **Ocp-Apim-Trace** con valore impostato su **true**.

    > [!NOTE]
    > Se l'intestazione Ocp-Apim-Subscription-Key non viene popolata automaticamente, può essere recuperata passando al portale per sviluppatori ed esponendo le chiavi nella pagina del profilo.

6. Fare clic su **"Invia"** per effettuare una chiamata API. 
7. Attendere il completamento della chiamata. 
8. Passare alla scheda **Traccia** della **console API**. Fare clic su uno dei collegamenti seguenti per visualizzare informazioni di traccia dettagliate: **inbound**, **backend**, **outbound**.

    Nella sezione **inbound** vengono visualizzate le richieste originali che Gestione API ha ricevuto dal chiamante e tutti i criteri applicati alla richiesta, inclusi i criteri rate-limit e set-header aggiunti al passaggio 2.

    Nella sezione **backend** vengono visualizzate le richieste che Gestione API ha inviato al back-end dell'API e la risposta che ha ricevuto.

    Nella sezione **outbound** vengono visualizzati tutti i criteri applicati alla risposta prima di restituirla al chiamante.

    > [!TIP]
    > Ogni passaggio mostra inoltre il tempo trascorso da quando la richiesta è stata ricevuta da Gestione API.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Tenere traccia di una chiamata

Passare all'esercitazione successiva:

> [!div class="nextstepaction"]
> [Usare le revisioni](api-management-get-started-revise-api.md)
