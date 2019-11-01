---
title: Creare e condividere i dashboard dei dati di Azure Log Analytics | Microsoft Docs
description: Questa esercitazione spiega in che modo i dashboard di Log Analytics visualizzano tutte le query di log salvate, offrendo un punto di vista unico su tutto l'ambiente.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: tutorial
author: mgoedtel
ms.author: magoedte
ms.date: 06/19/2019
ms.custom: mvc
ms.openlocfilehash: baa510e060a3bca46110842600262345072b1a18
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894785"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Creare e condividere i dashboard dei dati di Log Analytics

I dashboard di Log Analytics possono visualizzare tutte le query di log salvate, offrendo la possibilità di cercare, correlare e condividere i dati operativi IT nell'organizzazione.  Questa esercitazione illustra la creazione di una query di log che verrà usata per un dashboard condiviso accessibile dal team di supporto alle operazioni IT.  Si apprenderà come:

> [!div class="checklist"]
> * Creare un dashboard condiviso nel portale di Azure
> * Visualizzare una query di log relativa alle prestazioni 
> * Aggiungere una query di log a un dashboard condiviso 
> * Personalizzare un riquadro in un dashboard condiviso

Per completare l'esempio contenuto in questa esercitazione, è necessario disporre di una macchina virtuale esistente [connessa all'area di lavoro Log Analytics](quick-collect-azurevm.md).  
 
## <a name="sign-in-to-azure-portal"></a>Accedere al portale di Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-shared-dashboard"></a>Creare un dashboard condiviso
Selezionare **Dashboard** per aprire il [dashboard](../../azure-portal/azure-portal-dashboards.md) predefinito. Il dashboard avrà un aspetto diverso rispetto all'esempio riportato di seguito.

![Dashboard del portale di Azure](media/tutorial-logs-dashboards/log-analytics-portal-dashboard.png)

Qui è possibile raggruppare i dati operativi più importanti di tutte le risorse di Azure per l'IT, inclusi i dati di telemetria di Azure Log Analytics.  Prima di procedere alla visualizzazione di una query di log, è necessario creare un dashboard e condividerlo.  Sarà quindi possibile concentrarsi sulla query di log di esempio relativa alle prestazioni, che verrà visualizzata come grafico a linee, e aggiungerla al dashboard.  

Per creare un dashboard, selezionare il pulsante **Nuovo dashboard** accanto al nome del dashboard corrente.

![Creare un nuovo dashboard nel portale di Azure](media/tutorial-logs-dashboards/log-analytics-create-dashboard-01.png)

Questa azione crea un nuovo dashboard privato vuoto e attiva la modalità di personalizzazione, in cui è possibile assegnare un nome al dashboard e aggiungere o ridisporre i riquadri. Modificare il nome del dashboard specificando *Dashboard di esempio* per questa esercitazione e quindi selezionare **Fine personalizzazione**.<br><br> ![Salvare il dashboard di Azure personalizzato](media/tutorial-logs-dashboards/log-analytics-create-dashboard-02.png)

Quando si crea un dashboard, per impostazione predefinita il dashboard è privato e può quindi essere visualizzato solo dall'utente da cui è stato creato. Per renderlo visibile ad altri utenti, usare il pulsante **Condividi** visualizzato insieme agli altri comandi relativi ai dashboard.

![Condividere un nuovo dashboard nel portale di Azure](media/tutorial-logs-dashboards/log-analytics-share-dashboard.png) 

Viene chiesto di scegliere una sottoscrizione e un gruppo di risorse in cui pubblicare il dashboard. Per praticità, l'esperienza di pubblicazione del portale propone un modello che prevede l'inserimento dei dashboard in un gruppo di risorse denominato **dashboards**(dashboard).  Verificare la sottoscrizione selezionata e quindi fare clic su **Pubblica**.  L'accesso alle informazioni visualizzate nel dashboard è controllato con il [controllo degli accessi in base alle risorse di Azure](../../role-based-access-control/role-assignments-portal.md).   

## <a name="visualize-a-log-query"></a>Visualizzare una query di log
[Log Analytics](../log-query/get-started-portal.md) è un portale dedicato usato per lavorare con le query di log e i relativi risultati. Le funzionalità includono la possibilità di modificare una query su più righe, eseguire codice in modo selettivo, Intellisense sensibile al contesto e analisi intelligente. In questa esercitazione si userà Log Analytics per creare una visualizzazione delle prestazioni sotto forma di grafico, salvarla per query future e aggiungerla al dashboard condiviso creato in precedenza.

Aprire Log Analytics scegliendo **Log** dal menu Monitoraggio di Azure. Viene visualizzata una nuova query vuota.

![Home page](media/tutorial-logs-dashboards/homepage.png)

Immettere la query seguente per restituire i record di utilizzo del processore per computer Windows e Linux, raggruppati in base ai valori Computer e TimeGenerated e visualizzati in un grafico. Fare clic su **Esegui** per eseguire la query e visualizzare il grafico risultante.

```Kusto
Perf 
| where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" 
| summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1hr), Computer 
| render timechart
```

Salvare la query selezionando il pulsante **Salva query** nella parte superiore della pagina.

![Salvare la query](media/tutorial-logs-dashboards/save-query.png)

Nel pannello di controllo **Salva query** specificare un nome, ad esempio *Macchine virtuali di Azure - Utilizzo del processore* e una categoria, ad esempio *Dashboard*, e quindi fare clic su **Salva**.  In questo modo è possibile creare una raccolta di query comuni che è possibile usare e modificare.  Infine, aggiungere la query al dashboard condiviso creato in precedenza selezionando il pulsante **Aggiungi al dashboard** nell'angolo in alto a destra nella pagina e quindi selezionando il nome del dashboard.

La query aggiunta al dashboard presenta un titolo generico e un commento sotto di essa.

![Esempio di dashboard di Azure](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-01.png)

 Assegnare alla query un nome significativo facilmente comprensibile per chi la visualizza.  Fare clic sul pulsante Modifica per personalizzare il titolo e il sottotitolo per il riquadro e quindi fare clic su **Aggiorna**.  Verrà visualizzato un banner per chiedere all'utente se pubblicare o ignorare le modifiche.  Fare clic su **Salva una copia**.  

![Configurazione completata del dashboard di esempio](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato illustrato come creare un dashboard nel portale di Azure e aggiungervi una query di log.  Passare all'esercitazione successiva per informazioni sulle diverse risposte che è possibile implementare in base ai risultati delle query di log.  

> [!div class="nextstepaction"]
> [Rispondere a eventi con avvisi di Log Analytics](tutorial-response.md)
