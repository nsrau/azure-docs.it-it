---
title: Creare e condividere i dashboard dei dati di Azure Log Analytics | Microsoft Docs
description: Questa esercitazione spiega in che modo i dashboard di Log Analytics visualizzano tutte le ricerche log salvate, offrendo un punto di vista unico su tutto l'ambiente.
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: magoedte
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: b065269a27ad3764399802b4a2bc3076b7349623
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Creare e condividere i dashboard dei dati di Log Analytics

I dashboard di Log Analytics visualizzano tutte le ricerche log salvate, offrendo la possibilità di cercare, correlare e condividere i dati operativi IT nell'organizzazione.  Questa esercitazione illustra la creazione di una ricerca log che consente di supportare un dashboard condiviso accessibile dal team di supporto alle operazioni IT.  Si apprenderà come:

> [!div class="checklist"]
> * Creare un dashboard condiviso nel portale di Azure
> * Visualizzare una ricerca log delle prestazioni 
> * Aggiungere una ricerca log a un dashboard condiviso 
> * Personalizzare un riquadro in un dashboard condiviso

Per completare l'esempio contenuto in questa esercitazione, è necessario disporre di una macchina virtuale esistente [connessa all'area di lavoro di Log Analytics](log-analytics-quick-collect-azurevm.md).  
 
## <a name="log-in-to-azure-portal"></a>Accedere al portale di Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-shared-dashboard"></a>Creare un dashboard condiviso

Il primo oggetto visualizzato dopo l'accesso al portale di Microsoft Azure è un [dashboard](../azure-portal/azure-portal-dashboards.md).<br> ![Dashboard del portale di Azure](media/log-analytics-tutorial-dashboards/log-analytics-portal-dashboard.png)

Qui è possibile raggruppare i dati operativi più importanti di tutte le risorse di Azure per l'IT, inclusi i dati di telemetria di Azure Log Analytics.  Prima di procedere alla visualizzazione di una ricerca log, è necessario creare un dashboard e condividerlo.  In questo modo possiamo metterlo da parte prima di usare la ricerca log delle prestazioni di esempio, che verrà visualizzata come grafico a linee, e aggiungerla al dashboard.  

Per creare un dashboard, selezionare il pulsante **Nuovo dashboard** accanto al nome del dashboard corrente.<br> ![Creare un nuovo dashboard nel portale di Azure](media/log-analytics-tutorial-dashboards/log-analytics-create-dashboard-01.png)

Questa azione crea un nuovo dashboard privato vuoto e attiva la modalità di personalizzazione, in cui è possibile assegnare un nome al dashboard e aggiungere o ridisporre i riquadri. Modificare il nome del dashboard, specificare *Dashboard di esempio* per questa esercitazione e quindi selezionare **Fatto**.<br><br> ![Salvare il dashboard di Azure personalizzato](media/log-analytics-tutorial-dashboards/log-analytics-create-dashboard-02.png)

Quando si crea un dashboard, per impostazione predefinita il dashboard è privato e può quindi essere visualizzato solo dall'utente da cui è stato creato. Per renderlo visibile ad altri utenti, usare il pulsante **Condividi** visualizzato insieme agli altri comandi relativi ai dashboard.<br> ![Condividere un nuovo dashboard nel portale di Azure](media/log-analytics-tutorial-dashboards/log-analytics-share-dashboard.png) 

Viene chiesto di scegliere una sottoscrizione e un gruppo di risorse in cui pubblicare il dashboard. Per praticità, l'esperienza di pubblicazione del portale propone un modello che prevede l'inserimento dei dashboard in un gruppo di risorse denominato **dashboards**(dashboard).  Verificare la sottoscrizione selezionata e quindi fare clic su **Pubblica**.  L'accesso alle informazioni visualizzate nel dashboard è controllato con il [controllo degli accessi in base alle risorse di Azure](../active-directory/role-based-access-control-configure.md).   

## <a name="visualize-a-log-search"></a>Visualizzare una ricerca log

È possibile creare query di base in un'unica riga dal portale per la ricerca log nel portale di Azure. Il portale per la ricerca log può essere usato senza avviare un portale esterno ed è possibile usarlo per eseguire svariate funzioni, tra le quali la creazione di regole di avviso, la creazione di gruppi di computer e l'esportazione dei risultati delle query. 

Il [portale Advanced Analytics](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-the-Analytics-portal) è un portale dedicato che offre funzionalità avanzate non disponibili nel portale per la ricerca log. Le funzionalità includono la possibilità di modificare una query su più righe, eseguire codice in modo selettivo, Intellisense sensibile al contesto e analisi intelligente. Nel portale Advanced Analytics è possibile creare una visualizzazione delle prestazioni sotto forma di grafico, salvarla per ricerche future e aggiungerla al dashboard condiviso creato in precedenza.   

Avviare il portale Advanced Analytics da un collegamento nel portale per la ricerca log.<br> ![Avviare il portale Advanced Analytics](media/log-analytics-tutorial-dashboards/log-analytics-advancedportal-01.png)

Nel portale di Analytics immettere la query seguente per restituire solo i record di utilizzo del processore per computer Windows e Linux, raggruppati per Computer e TimeGenerated e visualizzati in un grafico visivo:

```
Perf | where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1m), Computer | render timechart
```

Salvare la query selezionando il pulsante **Salva query** nell'angolo in alto a destra.<br> ![Salvare la query nel portale Advanced Analytics](media/log-analytics-tutorial-dashboards/log-analytics-advancedportal-02.png)<br><br> Nel pannello di controllo **Salva query** indicare un nome, ad esempio *Macchine virtuali di Azure - Utilizzo del processore*, e quindi fare clic su **Salva**.  In questo modo è possibile creare una raccolta di query comuni per le ricerche o modificarla senza doverla riscrivere per intero.  Infine, aggiungerla al dashboard condiviso creato in precedenza selezionando il pulsante **Pin chart to your Azure dashboard** (Aggiungi grafico al dashboard di Azure) nell'angolo centrale destro della pagina.  

La query aggiunta al dashboard presenta un titolo generico e un commento sotto di essa.<br> ![Dashboard di esempio di Azure](media/log-analytics-tutorial-dashboards/log-analytics-modify-dashboard-01.png)<br><br>  Assegnare alla query un nome significativo facilmente comprensibile per chi la visualizza.  Fare clic con il pulsante destro del mouse sul riquadro e scegliere **Edit tile** (Modifica riquadro).  Dopo aver completato la personalizzazione del titolo e del sottotitolo per il riquadro, fare clic su **Aggiorna**.  Verrà visualizzato un banner per chiedere all'utente se pubblicare o ignorare le modifiche.  Fare clic su **Pubblica modifiche** e quindi chiudere il pannello di controllo **Edit Tile** (Modifica riquadro).  

![Configurazione completata del dashboard di esempio](media/log-analytics-tutorial-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato illustrato come creare un dashboard nel portale di Azure e aggiungervi una ricerca log.  Procedere all'esercitazione successiva per scoprire le varie risposte che è possibile implementare in base ai risultati delle ricerche log.  

> [!div class="nextstepaction"]
> [Rispondere a eventi con avvisi di Log Analytics](log-analytics-tutorial-response.md)
