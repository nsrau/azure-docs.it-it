---
title: Monitorare le API pubblicate con Gestione API di Azure | Microsoft Docs
description: Eseguire le procedure di questa esercitazione per monitorare le API con Gestione API di Azure.
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: bdca9d4968e9e68314f350787907f15e417821f7
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2017
---
# <a name="monitor-published-apis"></a>Monitorare le API pubblicate

Monitoraggio di Azure è un servizio di Azure che offre un'unica origine per il monitoraggio di tutte le risorse di Azure. Con Monitoraggio di Azure è possibile visualizzare, eseguire query, indirizzare, archiviare ed effettuare operazioni sulle metriche e sui log provenienti dalle risorse di Azure, tra cui Gestione API. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Visualizzare log di attività
> * Visualizzare i log di diagnostica
> * Visualizzare le metriche dell'API 
> * Configurare una regola di avviso quando l'API riceve delle chiamate non autorizzate

Il video seguente illustra come monitorare Gestione API usando Monitoraggio di Azure. 

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]
>
>

## <a name="prerequisites"></a>Prerequisiti

+ Completare la guida introduttiva seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).
+ Completare anche l'esercitazione seguente: [Importare e pubblicare la prima API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="diagnostic-logs"></a>Visualizzare log di attività

I log attività offrono informazioni dettagliate sulle operazioni eseguite nei servizi Gestione API. L'uso del log attività consente di acquisire informazioni dettagliate su qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita sui servizi Gestione API. 

> [!NOTE]
> I log attività non includono le operazioni di lettura (GET) né le operazioni eseguite nel portale di pubblicazione classico o usando le API di gestione originali.

È possibile accedere ai log attività del servizio Gestione API o ai log di tutte le risorse di Azure in Monitoraggio di Azure. 

Per visualizzare i log di attività:

1. Dall'istanza di **Gestione API** fare clic su **Log attività**.

## <a name="view-diagnostic-logs"></a>Visualizzare i log di diagnostica

I log di diagnostica offrono informazioni dettagliate sulle operazioni e gli errori importanti per il controllo e per la risoluzione dei problemi. I log di diagnostica differiscono dai log attività. I log attività offrono informazioni approfondite sulle operazioni eseguite nelle risorse di Azure. I log di diagnostica forniscono informazioni approfondite sulle operazioni che la risorsa esegue automaticamente.

Per accedere ai log di diagnostica:

1. Dall'istanza di **Gestione API** fare clic su **Log di diagnostica**.

## <a name="view-metrics-of-your-apis"></a>Visualizzare le metriche delle API

Gestione API genera le metriche ogni minuto in modo da ottenere una visibilità quasi in tempo reale dello stato e dell'integrità delle API. Di seguito è riportato un riepilogo delle metriche disponibili:

* Capacità (anteprima): consente di decidere se eseguire l'aggiornamento o il downgrade dei servizi di Gestione API. La metrica viene emessa ogni minuto e riflette la capacità del gateway nel momento in cui viene eseguito il report. La metrica ha un intervallo compreso tra 0 e 100 e viene calcolata in base alle risorse gateway, quali uso della CPU e della memoria.
* Totale richieste gateway: numero di richieste di API nel periodo. 
* Richieste gateway riuscite: numero di richieste di API che hanno ricevuto codici di risposta HTTP corretta, tra cui 304, 307 e qualsiasi valore inferiore a 301, ad esempio, 200. 
* Richieste gateway non riuscite: numero di richieste di API che hanno ricevuto codici di risposta HTTP errata, tra cui 400 e qualsiasi valore superiore a 500.
* Richieste gateway non autorizzate: numero di richieste di API che hanno ricevuto codici di risposta HTTP tra cui 401, 403 e 429. 
* Altre richieste gateway: numero di richieste di API che hanno ricevuto codici di risposta HTTP che non appartengono a una delle categorie precedenti, ad esempio, 418.

Per accedere alle metriche:

1. Selezionare **Metriche** dal menu nella parte inferiore della pagina.
2. Dall'elenco a discesa selezionare le metriche desiderate. È possibile aggiungere più metriche. 
    
    Selezionare ad esempio **Totale richieste gateway** e **Richieste gateway non riuscite** dall'elenco delle metriche disponibili.
3. Il grafico mostra il numero totale di chiamate API. Viene indicato anche il numero di chiamate API non riuscite. 

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>Configurare una regola di avviso per le richieste non autorizzate

È possibile configurare un avviso basato sulle metriche e sui log attività. Monitoraggio di Azure consente di configurare un avviso in modo che, se attivato, esegua queste operazioni:

* Inviare una notifica via posta elettronica
* Chiamare un webhook
* Richiamare un'app per la logica di Azure

Per configurare gli avvisi:

1. Selezionare **Regole di avviso** nella barra dei menu nella parte inferiore della pagina.
2. Selezionare **Aggiungi avviso per la metrica**.
3. Immettere un **nome** per questo avviso.
4. Selezionare **Richieste del gateway non autorizzate** come metrica da monitorare.
5. Selezionare **Invia messaggio di posta elettronica a proprietari, collaboratori e lettori**.
6. Premere **OK**.
7. Provare a chiamare l'API Conferenza senza una chiave API. In quanto proprietario del servizio Gestione API, l'utente riceverà un avviso di posta elettronica. 

    > [!TIP]
    > Quando viene attivata, la regola di avviso può anche chiamare un webhook o un'app per la logica di Azure.

    ![set-up-alert](./media/api-management-azure-monitor/set-up-alert.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Visualizzare log di attività
> * Visualizzare i log di diagnostica
> * Visualizzare le metriche dell'API 
> * Configurare una regola di avviso quando l'API riceve delle chiamate non autorizzate

Passare all'esercitazione successiva:

> [!div class="nextstepaction"]
> [Tracciare le chiamate](api-management-howto-api-inspector.md)