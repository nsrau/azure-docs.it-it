---
title: Analizzare l&quot;utilizzo dei dati in Log Analytics | Documentazione di Microsoft
description: "È possibile usare il dashboard Utilizzo in Log Analytics per visualizzare la quantità di dati inviata al servizio OMS."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/14/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 92083407c2d2902227842e4320e9ad2ae6a3fa0b
ms.openlocfilehash: 9ee0a8a01a335cc7a97375e65e1bbc77b1aa76e5


---
# <a name="analyze-data-usage-in-log-analytics"></a>Analizzare l'utilizzo dei dati in Log Analytics
Log Analytics raccoglie i dati e li invia periodicamente al servizio OMS.  È possibile usare il dashboard **Utilizzo di Log Analytics** per visualizzare la quantità di dati inviata al servizio OMS. Il dashboard mostra anche la quantità di dati inviata dalle soluzioni e la frequenza dell'invio di dati da parte dei server.

> [!NOTE]
> Se si ha un account gratuito, è possibile inviare solo 500 MB di dati al giorno al servizio OMS. Se si raggiunge il limite giornaliero, l'analisi dei dati viene arrestata e riprende all'inizio del giorno successivo. In questo caso, è necessario inviare di nuovo i dati che non sono stati accettati o elaborati da OMS.

Se è stato superato o si sta per raggiungere il limite di utilizzo giornaliero, facoltativamente è possibile rimuovere una soluzione per ridurre la quantità di dati da inviare al servizio OMS. Per altre informazioni sulla rimozione di soluzioni vedere l'articolo [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md)(Aggiungere soluzioni di Log Analytics dalla raccolta soluzioni).

![dashboard di utilizzo](./media/log-analytics-usage/usage-dashboard01.png)

Il dashboard **Utilizzo di Log Analytics** visualizza le informazioni seguenti:

- Volume dati
    - Volume dati nel tempo (in base all'ambito temporale corrente)
    - Volume dati per soluzione
    - Dati non associati a un computer
- Computer
    - Computer che inviano dati
    - Computer senza dati nelle ultime 24 ore
- Offerte
    - Nodi di informazioni dettagliate e analisi
    - Nodi di automazione e controllo
    - Nodi di sicurezza
- Prestazioni
    - Tempo impiegato per raccogliere e indicizzare i dati
- Elenco di query

## <a name="to-work-with-usage-data"></a>Per gestire i dati di utilizzo
1. Se questa operazione non è già stata eseguita, accedere al [portale di Azure](https://portal.azure.com), usando la sottoscrizione di Azure.
2. Scegliere **Altri servizi** dal menu **Hub** e digitare **Log Analytics** nell'elenco di risorse. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Fare clic su **Log Analytics**.  
    ![Hub di Azure](./media/log-analytics-usage/hub.png)
3. Il dashboard **Log Analytics** visualizza un elenco delle aree di lavoro. Selezionare un'area di lavoro.
4. Nel dashboard dell'*area di lavoro* fare clic su **Utilizzo di Log Analytics**.
5. Nel dashboard **Utilizzo di Log Analytics** fare clic su **Ora: Ultime 24 ore** per modificare l'intervallo di tempo.  
    ![Intervallo di tempo](./media/log-analytics-usage/time.png)
6. Visualizzare i pannelli delle categorie di utilizzo che mostrano le aree a cui si è interessati. Scegliere un pannello e quindi fare clic su un elemento per visualizzare altri dettagli in [Ricerca log](log-analytics-log-searches.md).  
    ![Pannello dell'utilizzo dei dati di esempio](./media/log-analytics-usage/blade.png)
7. Nel dashboard Ricerca log esaminare i risultati restituiti dalla ricerca.  
    ![Ricerca log sull'utilizzo dei dati di esempio](./media/log-analytics-usage/usage-log-search.png)


## <a name="next-steps"></a>Passaggi successivi
* Vedere [Ricerche nei log in Log Analytics](log-analytics-log-searches.md) per visualizzare le informazioni dettagliate raccolte e inviate a OMS dalle funzionalità e dalle soluzioni.



<!--HONumber=Nov16_HO3-->


