---
title: Ottimizzare l&quot;ambiente con la soluzione Service Fabric in Log Analytics | Documentazione Microsoft
description: "È possibile usare la soluzione Service Fabric per valutare i rischi e l&quot;integrità delle applicazioni Service Fabric, dei servizi micro, dei nodi e dei cluster."
services: log-analytics
documentationcenter: 
author: niniikhena
manager: jochan
editor: 
ms.assetid: 9c91aacb-c48e-466c-b792-261f25940c0c
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2016
ms.author: nini
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2fe6c2b23c816a5ec5bb00199725cdf0b29b31f0


---
# <a name="service-fabric-solution-in-log-analytics"></a>Soluzione Service Fabric in Log Analytics
> [!div class="op_single_selector"]
> * [Gestione risorse](log-analytics-service-fabric-azure-resource-manager.md)
> * [PowerShell](log-analytics-service-fabric.md)
> 
> 

In questo articolo viene descritto come usare la soluzione Service Fabric in Log Analytics per identificare e risolvere i problemi nel cluster di Service Fabric.

La soluzione Service Fabric usa i dati della Diagnostica di Azure provenienti dalle macchine virtuali Service Fabric, raccogliendo questi dati dalle tabelle di Azure WAD. Successivamente, Log Analytics legge gli eventi del framework di Service Fabric, tra cui: **Eventi del servizio affidabile**, **Eventi relativi agli attori**, **Eventi operativi** ed **Eventi ETW personalizzati**. Grazie al dashboard della soluzione Service Fabric è possibile vedere i problemi degni di nota e gli eventi rilevanti nell'ambiente Service Fabric.

Per iniziare a usare la soluzione, è necessario connettere il cluster di Service Fabric a un'area di lavoro di Log Analytics. Ecco i tre scenari da prendere in considerazione:

1. Se il cluster di Service Fabric non è stato distribuito, eseguire i passaggi descritti nella sezione ***Distribuire un cluster di Service Fabric connesso a un'area di lavoro di Log Analytics*** per distribuire un nuovo cluster e configurarlo per il reporting in Log Analytics.
2. Se si desidera raccogliere i dati dei contatori delle prestazioni dagli host per usare altre soluzioni OMS, come ad esempio Security nel cluster di Service Fabric, seguire i passaggi descritti nella sezione ***Distribuire un cluster di Service Fabric connesso a un'area di lavoro di OMS con installata l'estensione VM.***
3. Se il cluster di Service Fabric è già stato distribuito e si desidera connetterlo a Log Analytics, seguire i passaggi descritti nella sezione ***Aggiunta di un account di archiviazione esistente a Log Analytics.***

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace"></a>Distribuire un cluster di Service Fabric connesso a un'area di lavoro di Log Analytics.
Questo modello consente di:

1. Distribuire un cluster di Azure Service Fabric già connesso a un'area di lavoro di Log Analytics. È possibile scegliere tra creare una nuova area di lavoro durante la distribuzione del modello e immettere il nome di un'area di lavoro di Log Analytics esistente.
2. Aggiungere l'account di archiviazione per la diagnostica all'area di lavoro di Log Analytics.
3. Abilitare la soluzione Service Fabric nell'area di lavoro di Log Analytics.

[![Distribuisci in Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-oms%2F%2Fazuredeploy.json)

Dopo aver selezionato il pulsante di distribuzione indicato sopra, si accederà all'area del portale di Azure con i parametri da modificare. Assicurarsi di creare un nuovo gruppo di risorse se si immette un nuovo nome dell'area di lavoro di Log Analytics: ![Service Fabric](./media/log-analytics-service-fabric/2.png)

![Service Fabric](./media/log-analytics-service-fabric/3.png)

Accettare le note legali e premere "Crea" per avviare la distribuzione. Una volta completata la distribuzione, la nuova area di lavoro e il cluster creati dovrebbero apparire e le tabelle WADServiceFabric*Event, WADWindowsEventLogs e WADETWEvent dovrebbero essere state aggiunte:

![Service Fabric](./media/log-analytics-service-fabric/4.png)

## <a name="deploy-a-service-fabric-cluster-connected-to-an-oms-workspace-with-vm-extension-installed"></a>Distribuire un cluster di Service Fabric connesso a un'area di lavoro di OMS con installata l'estensione VM.
Questo modello consente di:

1. Distribuire un cluster di Azure Service Fabric già connesso a un'area di lavoro di Log Analytics. È possibile creare una nuova area di lavoro o usarne una esistente.
2. Aggiungere gli account di archiviazione per la diagnostica all'area di lavoro di Log Analytics.
3. Abilitare la soluzione Service Fabric nell'area di lavoro di Log Analytics.
4. Installare l'estensione agente MMA in ciascun set di scalabilità di macchine virtuali nel cluster di Service Fabric. Avendo installato l'agente MMA, è possibile visualizzare le metriche delle prestazioni relative ai nodi.

[![Distribuisci in Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-vmss-oms%2F%2Fazuredeploy.json)

Seguendo la stessa procedura descritta sopra, immettere i parametri necessari e avviare una distribuzione. Anche questa volta verranno visualizzati la nuova area di lavoro, il cluster e le tabelle WAD create:

![Service Fabric](./media/log-analytics-service-fabric/5.png)

### <a name="viewing-performance-data"></a>Visualizzazione dei dati sulle prestazioni
Per visualizzare i dati sulle prestazioni dai nodi:
</br>

* Avviare l'area di lavoro di Log Analytics dal portale di Azure.

![Service Fabric](./media/log-analytics-service-fabric/6.png)

* Andare a Impostazioni nel riquadro a sinistra e selezionare dati >> Contatori delle prestazioni di Windows >> "Aggiungi i contatori delle prestazioni selezionati": ![Service Fabric](./media/log-analytics-service-fabric/7.png)
* In Ricerca log, usare le query seguenti per approfondire le metriche principali relative ai nodi:
  </br>
  
    a. Confrontare l'uso medio della CPU in tutti i nodi nell'ultima ora per determinare i nodi che hanno avuto problemi e l'intervallo di tempo in cui un nodo ha avuto un picco:
  
    ``` Type=Perf ObjectName=Processor CounterName="% Processor Time"|measure avg(CounterValue) by Computer Interval 1HOUR. ```
  
    ![Service Fabric](./media/log-analytics-service-fabric/10.png)

    b. Visualizzare i grafici a linee simili relativi alla memoria disponibile in ciascun nodo con questa query:

    ```Type=Perf ObjectName=Memory CounterName="Available MBytes Memory" | measure avg(CounterValue) by Computer Interval 1HOUR.```

    Per visualizzare un elenco di tutti i nodi, indicante il valore medio esatto relativo ai MByte disponibili per ciascun nodo, usare questa query:

    ```Type=Perf (ObjectName=Memory) (CounterName="Available MBytes") | measure avg(CounterValue) by Computer ```

    ![Service Fabric](./media/log-analytics-service-fabric/11.png)


    c. Se si desidera eseguire un'analisi approfondita di un nodo specifico esaminando la media oraria, il valore minimo e massimo e il 75° percentile riguardo all'uso della CPU, è possibile farlo mediante la query seguente (sostituire campo uso):

    ```Type=Perf CounterName="% Processor Time" InstanceName=_Total Computer="BaconDC01.BaconLand.com"| measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR```

    ![Service Fabric](./media/log-analytics-service-fabric/12.png)

    Leggere altre informazioni sulle metriche delle prestazioni di Log Analytics [qui]. (https://blogs.technet.microsoft.com/msoms/tag/metrics/)


## <a name="adding-an-existing-storage-account-to-log-analytics"></a>Aggiunta di un account di archiviazione esistente a Log Analytics
Questo modello aggiunge semplicemente gli account di archiviazione esistenti a un'area di lavoro di Log Analytics nuova o esistente.
</br>

[![Distribuisci in Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Foms-existing-storage-account%2Fazuredeploy.json)

> [!NOTE]
> Per la selezione di un gruppo di risorse, se si usa un'area di lavoro di Log Analytics già esistente, selezionare "Usa esistente" e cercare il gruppo di risorse che contiene l'area di lavoro di OMS. Altrimenti, crearne uno nuovo.
> ![Service Fabric](./media/log-analytics-service-fabric/8.png)
> 
> 

Dopo avere distribuito il modello, sarà possibile visualizzare l'account di archiviazione connesso all'area di lavoro di Log Analytics. In questo esempio, all'area di lavoro di Exchange è stato aggiunto un ulteriore account di archiviazione, creato in precedenza.
![Service Fabric](./media/log-analytics-service-fabric/9.png)

## <a name="view-service-fabric-events"></a>Visualizzare gli eventi di Service Fabric
Una volta completate le distribuzioni e dopo aver abilitato la soluzione Service Fabric nell'area di lavoro, selezionare il riquadro **Service Fabric** nel portale di Log Analytics per avviare il dashboard di Service Fabric. Il dashboard include le colonne nella tabella seguente. Ogni colonna elenca i primi dieci eventi per numero corrispondente ai criteri della colonna per l'intervallo di tempo specificato. È possibile eseguire una ricerca di log che fornisce l'intero elenco facendo clic su **Visualizza tutto** nella parte inferiore destra di ciascuna colonna o facendo clic sull'intestazione di colonna.

| **Evento di Service Fabric** | **description** |
| --- | --- |
| Errori rilevanti |Visualizzazione dei problemi, ad esempio RunAsyncFailures RunAsynCancellations e Node Downs. |
| Eventi operativi |Eventi operativi rilevanti, ad esempio l'aggiornamento dell'applicazione e le distribuzioni. |
| Eventi del servizio affidabile |Eventi del servizio affidabile rilevanti come Runasyncinvocations. |
| Eventi relativi agli attori |Gli eventi relativi agli attori rilevanti generati dai micro-servizi, ad esempio le eccezioni generate da un metodo attore, le attivazioni e disattivazioni relative all'attore e così via. |
| Eventi dell'applicazione |Tutti gli eventi ETW personalizzati che sono stati generati dalle applicazioni. |

![Dashboard di Service Fabric](./media/log-analytics-service-fabric/sf3.png)

![Dashboard di Service Fabric](./media/log-analytics-service-fabric/sf4.png)

La tabella seguente illustra i metodi di raccolta dei dati e altri dettagli sulla modalità di raccolta dei dati per Service Fabric.

| Piattaforma | Agente diretto | Agente SCOM | Archiviazione di Azure | SCOM obbligatorio? | Dati dell'agente SCOM inviati con il gruppo di gestione | frequenza della raccolta |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |![No](./media/log-analytics-malware/oms-bullet-red.png) |![No](./media/log-analytics-malware/oms-bullet-red.png) |![Sì](./media/log-analytics-malware/oms-bullet-green.png) |![No](./media/log-analytics-malware/oms-bullet-red.png) |![No](./media/log-analytics-malware/oms-bullet-red.png) |10 minuti |

> [!NOTE]
> È possibile modificare l'ambito di questi eventi nella soluzione Service Fabric facendo clic su **Dati basati sugli ultimi 7 giorni** nella parte superiore del dashboard. È anche possibile mostrare gli avvisi generati negli ultimi 7 giorni, nell'ultimo giorno o nelle ultime sei ore. In alternativa, è possibile selezionare **Personalizzato** e specificare un intervallo di date personalizzato.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
* Per visualizzare i dati dettagliati sugli eventi Service Fabric usare [Ricerche log in Log Analytics](log-analytics-log-searches.md).




<!--HONumber=Nov16_HO3-->


