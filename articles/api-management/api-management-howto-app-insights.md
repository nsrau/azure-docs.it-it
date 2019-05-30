---
title: Come integrare Gestione API di Azure con Azure Application Insights | Microsoft Docs
description: Informazioni su come registrare e visualizzare gli eventi da Gestione API di Azure in Azure Application Insights.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 3bbab82831fba389cd4bf172e7ea762d5971579b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241853"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Come integrare Gestione API di Azure con Azure Application Insights

Gestione API di Azure consente una facile integrazione con Azure Application Insights, un servizio estendibile per sviluppatori Web che compilano e gestiscono app in più piattaforme. Questa guida illustra ogni passaggio di tale integrazione e descrive le strategie per ridurre l'impatto sulle prestazioni nell'istanza del servizio Gestione API.

## <a name="prerequisites"></a>Prerequisiti

Per seguire questa guida, è necessario avere un'istanza di Gestione API di Azure. Se non è disponibile, completare prima l'[esercitazione](get-started-create-service-instance.md).

## <a name="create-an-azure-application-insights-instance"></a>Creare un'istanza di Azure Application Insights

Prima di poter usare Azure Application Insights, è necessario creare un'istanza del servizio.

1. Aprire il **portale di Azure** e passare ad **Application Insights**.  
    ![Creazione di Application Insights](media/api-management-howto-app-insights/apim-app-insights-instance-1.png)  
2. Fare clic su **+ Aggiungi**.  
    ![Creazione di Application Insights](media/api-management-howto-app-insights/apim-app-insights-instance-2.png)  
3. Compilare il modulo. Selezionare **Generale** come **tipo di applicazione**.
4. Fare clic su **Create**(Crea).

## <a name="create-a-connection-between-azure-application-insights-and-azure-api-management-service-instance"></a>Creare una connessione tra Azure Application Insights e l'istanza del servizio Gestione API di Azure

1. Passare all'**istanza del servizio Gestione API di Azure** nel **portale di Azure**.
2. Selezionare **Application Insights** nel menu a sinistra.
3. Fare clic su **+ Aggiungi**.  
    ![Logger di Application Insights](media/api-management-howto-app-insights/apim-app-insights-logger-1.png)  
4. Selezionare l'istanza di **Application Insights** creata in precedenza e immettere una breve descrizione.
5. Fare clic su **Create**(Crea).
6. È appena stato creato un logger di Azure Application Insights con una chiave di strumentazione. Verrà ora visualizzato nell'elenco.  
    ![Logger di Application Insights](media/api-management-howto-app-insights/apim-app-insights-logger-2.png)  

> [!NOTE]
> A parte, viene creata un'entità [Logger](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger/createorupdate) nell'istanza di gestione API, contenente la chiave di strumentazione dell'istanza di Application Insights.

## <a name="enable-application-insights-logging-for-your-api"></a>Abilitare la registrazione di Application Insights per l'API

1. Passare all'**istanza del servizio Gestione API di Azure** nel **portale di Azure**.
2. Selezionare **API** nel menu a sinistra.
3. Fare clic sull'API, in questo caso **Demo Conference API**.
4. Passare alla scheda **Impostazioni** dalla barra in alto.
5. Scorrere verso il basso fino alla sezione **Log di diagnostica**.  
    ![Logger di Application Insights](media/api-management-howto-app-insights/apim-app-insights-api-1.png)  
6. Selezionare la casella **Abilita**.
7. Selezionare il logger associato nell'elenco a discesa **Destinazione**.
8. Immettere **100** come **Sampling (%)** (Campionamento - %) e selezionare la casella di controllo **Always log errors** (Registra sempre gli errori).
9. Fare clic su **Save**.

> [!WARNING]
> Sovrascrivere il valore predefinito **0** nei **primi byte del campo corpo** potrebbe ridurre in modo significativo le prestazioni delle API.

> [!NOTE]
> A parte viene creata un'entità [Diagnostica](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/diagnostic/createorupdate) denominata "applicationinsights" a livello di API.

| Nome impostazione                        | Tipo di valore                        | Descrizione                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Abilita                              | boolean                           | Specifica se la registrazione di questa API è abilitata.                                                                                                                                                                                                                                                                                                |
| Destination                         | Logger di Azure Application Insights | Specifica il logger di Azure Application Insights da usare                                                                                                                                                                                                                                                                                           |
| Sampling (%) (Campionamento - %)                        | decimal                           | Valori compresi tra 0 e 100 (percentuale). <br/> Specifica la percentuale di richieste che verranno registrate in Azure Application Insights. Un campionamento pari allo 0% indica che verranno registrate zero richieste, mentre un campionamento del 100% indica che verranno registrate tutte le richieste. <br/> Questa impostazione viene usata per ridurre l'impatto sulle prestazioni dovuto alla registrazione delle richieste in Azure Application Insights (vedere la sezione seguente). |
| Always log errors (Registra sempre gli errori)                   | boolean                           | Se questa impostazione viene selezionata, tutti gli errori verranno registrati in Azure Application Insights, indipendentemente dall'impostazione di **campionamento**.                                                                                                                                                                                                                  |
| Opzioni di base: Headers              | list                              | Specifica le intestazioni che verranno registrate in Azure Application Insights per le richieste e risposte.  Impostazione predefinita: non vengono registrate intestazioni.                                                                                                                                                                                                             |
| Opzioni di base: Primi byte del corpo  | numero intero                           | Specifica il numero di primi byte del corpo che verranno registrati in Azure Application Insights per le richieste e risposte.  Impostazione predefinita: il corpo non viene registrato.                                                                                                                                                                                              |
| Opzioni avanzate: Richiesta front-end  |                                   | Specifica se e come le *richieste front-end* verranno registrate in Azure Application Insights. La *richiesta front-end* è una richiesta in arrivo al servizio Gestione API di Azure.                                                                                                                                                                        |
| Opzioni avanzate: Risposta front-end |                                   | Specifica se e come le *risposte front-end* verranno registrate in Azure Application Insights. La *risposta front-end* è una risposta in uscita dal servizio Gestione API di Azure.                                                                                                                                                                   |
| Opzioni avanzate: Richieste back-end   |                                   | Specifica se e come le *richieste back-end* verranno registrate in Azure Application Insights. La *richiesta back-end* è una richiesta in uscita dal servizio Gestione API di Azure.                                                                                                                                                                        |
| Opzioni avanzate: Risposta back-end  |                                   | Specifica se e come le *risposte back-end* verranno registrate in Azure Application Insights. La *risposta back-end* è una risposta in arrivo al servizio Gestione API di Azure.                                                                                                                                                                       |

> [!NOTE]
> È possibile specificare i logger a livelli diversi: logger per singole API o un logger per tutte le API.
>  
> Se si specificano entrambi:
> + Se sono logger diversi, verranno usati entrambi (multiplexing di log).
> + Se sono gli stessi logger, ma hanno impostazioni diverse, quello per l'API singola (livello più granulare) eseguirà l'override di quello per tutte le API.

## <a name="what-data-is-added-to-azure-application-insights"></a>Dati aggiunti ad Azure Application Insights

Azure Application Insights riceve:

+ Elemento di telemetria *richiesta*, per ogni richiesta in ingresso (*richiesta front-end*, *risposta front-end*).
+ Elemento di telemetria *dipendenza*, per ogni richiesta inoltrata a un servizio back-end (*richiesta back-end*, *risposta back-end*).
+ Elemento di telemetria *eccezione*, per ogni richiesta non riuscita.

Una richiesta non riuscita è una richiesta che:

+ Non è riuscita a causa di una connessione client chiusa.
+ Ha attivato una sezione *on-error* dei criteri delle API.
+ Ha un codice di stato HTTP della risposta corrispondente a 4xx o 5xx.

## <a name="performance-implications-and-log-sampling"></a>Implicazioni sulle prestazioni e campionamento dei log

> [!WARNING]
> La registrazione di tutti gli eventi può comportare gravi implicazioni per le prestazioni, a seconda della frequenza delle richieste in ingresso.

In base ai test di carico interni, l'abilitazione di questa funzionalità ha causato una riduzione del 40%-50% della velocità effettiva quando la frequenza delle richieste superava le 1.000 richieste al secondo. Azure Application Insights è progettato per usare l'analisi statistica per valutare le prestazioni dell'applicazione. Non è pensato come sistema di controllo e non è adatto alla registrazione di ogni singola richiesta di API con volumi elevati.

È possibile modificare il numero di richieste da registrare regolando l'impostazione **Campionamento** (vedere i passaggi precedenti). Un valore pari al 100% indica che vengono registrate tutte le richieste, mentre un valore pari allo 0% indica che non vengono eseguite registrazioni. Il **campionamento** consente di ridurre il volume dei dati di telemetria, impedendo un considerevole degrado delle prestazioni, pur conservando i vantaggi della registrazione.

Evitando di registrare le intestazioni e il corpo di richieste e risposte, si riducono considerevolmente anche i problemi di prestazioni.

## <a name="video"></a>Video

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Passaggi successivi

+ Altre informazioni su [Azure Application Insights](https://docs.microsoft.com/azure/application-insights/).
+ Considerare la possibilità di eseguire la [registrazione con Hub eventi di Azure](api-management-howto-log-event-hubs.md).
