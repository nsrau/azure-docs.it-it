---
title: Configurare la sicurezza per l'accesso e la gestione di Azure Time Series Insights | Microsoft Docs
description: Questo articolo descrive come configurare la sicurezza e le autorizzazioni come criteri di accesso per la gestione e come criteri di accesso ai dati per proteggere Azure Time Series Insights.
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: anshan
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: c7d4079c9106226e0d07aa97c4a52c16ddb257c3
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2018
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Concedere l'accesso ai dati in un ambiente Time Series Insights con il portale di Azure

Gli ambienti Time Series Insights hanno due tipologie indipendenti di criteri di accesso:

* Criteri di accesso di gestione
* Criteri di accesso ai dati

Entrambi criteri concedono alle entità di sicurezza di Azure Active Directory (utenti e app) varie autorizzazioni per un determinato ambiente. Le entità di sicurezza (utenti e app) devono appartenere all'istanza di Active Directory (nota come tenant di Azure) associata alla sottoscrizione contenente l'ambiente.

I criteri di accesso di gestione concedono le autorizzazioni relative alla configurazione dell'ambiente, che includono:
*   Creazione ed eliminazione dell'ambiente, delle origini evento e dei set di dati di riferimento
*   Gestione dei criteri di accesso ai dati.

I criteri di accesso ai dati concedono le autorizzazioni per eseguire query sui dati, modificare i dati di riferimento nell'ambiente e condividere le prospettive e le query salvate associate all'ambiente.

Le due tipologie di criteri consentono una netta separazione tra l'accesso alla gestione dell'ambiente e l'accesso ai dati all'interno di esso. È ad esempio possibile configurare un ambiente in modo che il relativo proprietario/creatore sia rimosso dall'accesso ai dati. In più, a utenti e servizi a cui è consentita la lettura dei dati dell'ambiente può non essere concesso l'accesso alla configurazione dell'ambiente stesso.

## <a name="grant-data-access"></a>Concedere l'accesso ai dati
Eseguire la procedura seguente per concedere l'accesso ai dati per un'entità utente:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Individuare l'ambiente Time Series Insights. Digitare **Time Series** nella casella di **ricerca**. Selezionare **ambiente Time Series** nei risultati della ricerca. 

3. Selezionare l'ambiente Time Series Insights nell'elenco.
   
4. Selezionare **Criteri di accesso ai dati** e quindi selezionare **+ Aggiungi**.
  ![Gestire l'origine Time Series Insights: ambiente](media/data-access/getstarted-grant-data-access1.png)

5. Selezionare **Seleziona utente**.  Cercare il nome utente o l'indirizzo di posta elettronica per individuare l'utente che si vuole aggiungere. Fare clic su **Seleziona** per confermare la selezione. 

   ![Gestire l'origine Time Series Insights: aggiunta](media/data-access/getstarted-grant-data-access2.png)

6. Selezionare **Selezionare un ruolo**. Scegliere il ruolo di accesso appropriato per l'utente:
   - Selezionare **Collaboratore** se si vuole consentire all'utente di modificare i dati di riferimento e condividere le prospettive e le query salvate con altri utenti dell'ambiente. 
   - In alternativa, selezionare **Lettore** per consentire all'utente di eseguire query sui dati e salvare query personali (non condivise) nell'ambiente.

   Selezionare **OK** per confermare la scelta del ruolo.

   ![Gestire l'origine Time Series Insights: selezione dell'utente](media/data-access/getstarted-grant-data-access3.png)

8. Fare clic su **OK** nella pagina **Selezionare il ruolo utente**.

   ![Gestire l'origine Time Series Insights: selezione del ruolo](media/data-access/getstarted-grant-data-access4.png)

9. La pagina **Criteri di accesso ai dati** elenca gli utenti e i ruoli per ogni utente.

   ![Gestire l'origine Time Series Insights: risultati](media/data-access/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Passaggi successivi
* Apprendere [come aggiungere un'origine evento hub eventi all'ambiente Azure Time Series Insights](time-series-insights-how-to-add-an-event-source-eventhub.md).
* [Inviare eventi](time-series-insights-send-events.md) all'origine evento.
* Visualizzare l'ambiente in [Time Series Insights Explorer](https://insights.timeseries.azure.com).
