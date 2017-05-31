---
title: Criteri di accesso ai dati in Azure Time Series Insights | Microsoft Docs
description: Questa esercitazione illustra come gestire i criteri di accesso ai dati in Time Series Insights
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2017
ms.author: omravi
ms.translationtype: Human Translation
ms.sourcegitcommit: 300958a69fc854cb8db02120a383a4cbbfcacd7b
ms.openlocfilehash: e975c6d8f217bc73948c0c046204b16b1a742bc7
ms.contentlocale: it-it
ms.lasthandoff: 05/20/2017

---

# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Concedere l'accesso ai dati in un ambiente Time Series Insights con il portale di Azure

Gli ambienti Time Series Insights hanno due tipologie indipendenti di criteri di accesso:

* Criteri di accesso di gestione
* Criteri di accesso ai dati

Entrambi criteri concedono alle entità di sicurezza di Azure Active Directory (utenti e app) varie autorizzazioni per un determinato ambiente. Le entità di sicurezza (utenti e app) devono appartenere all'istanza di Active Directory ("tenant di Azure") associata alla sottoscrizione contenente l'ambiente.

I criteri di accesso di gestione concedono le autorizzazioni relative alla configurazione dell'ambiente, che includono:
*    Creazione ed eliminazione dell'ambiente, delle origini evento e dei set di dati di riferimento
*    Gestione dei criteri di accesso ai dati.

I criteri di accesso ai dati concedono le autorizzazioni per eseguire query sui dati, modificare i dati di riferimento nell'ambiente e condividere le prospettive e le query salvate associate all'ambiente.

Le due tipologie di criteri consentono una netta separazione tra l'accesso alla gestione dell'ambiente e l'accesso ai dati all'interno di esso. È ad esempio possibile configurare un ambiente in modo che il relativo proprietario/creatore sia rimosso dall'accesso ai dati. Analogamente, a utenti e servizi a cui è consentita la lettura dei dati dell'ambiente può non essere concesso l'accesso alla configurazione dell'ambiente.

## <a name="grant-data-access"></a>Concedere l'accesso ai dati
La procedura seguente illustra come concedere l'accesso ai dati per un'entità utente:

1.    Accedere al [portale di Azure](https://portal.azure.com).
2.    Fare clic su "Tutte le risorse" nel menu sul lato sinistro del portale di Azure.
3.    Selezionare l'ambiente Time Series Insights.

  ![Gestire l'origine Time Series Insights: ambiente](media/data-access/getstarted-grant-data-access1.png)

4.    Selezionare "Accesso al piano dati" e fare clic su "Aggiungi".

  ![Gestire l'origine Time Series Insights: aggiunta](media/data-access/getstarted-grant-data-access2.png)

5.    Fare clic su "Seleziona utente".
6.    Cercare e selezionare l'utente in base all'indirizzo di posta elettronica.
7.    Fare clic su "Seleziona" nel pannello "Selezionare l'utente".

  ![Gestire l'origine Time Series Insights: selezione dell'utente](media/data-access/getstarted-grant-data-access3.png)

8.    Fare clic su "Seleziona ruolo".
9.    Selezionare "Collaboratore" se si vuole consentire all'utente di modificare i dati di riferimento e condividere le prospettive e le query salvate con altri utenti dell'ambiente. In alternativa, selezionare "Lettore" per consentire all'utente di eseguire query sui dati e salvare query personali (non condivise) nell'ambiente.
10.    Fare clic su "OK" nel pannello "Selezionare un ruolo".

  ![Gestire l'origine Time Series Insights: selezione del ruolo](media/data-access/getstarted-grant-data-access4.png)

11.    Fare clic su "OK" nel pannello "Selezionare il ruolo utente".
12.    Dovrebbe essere visualizzato:

  ![Gestire l'origine Time Series Insights: risultati](media/data-access/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Passaggi successivi

* [Creare un'origine evento](time-series-insights-add-event-source.md)
* [Inviare eventi](time-series-insights-send-events.md) all'origine evento
* Visualizzare l'ambiente nel [portale di Time Series Insights](https://insights.timeseries.azure.com)

