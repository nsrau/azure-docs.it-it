---
title: Visualizzare i dati in tempo reale (anteprima) con Monitoraggio di Azure per i contenitori . Documenti Microsoft
description: Questo articolo descrive la visualizzazione in tempo reale di log, eventi e metriche di pod di Kubernetes senza usare kubectl in Monitoraggio di Azure per i contenitori.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 9e7c7a7b7bf276b3451cee1d289b8b07ac0f40ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79216556"
---
# <a name="how-to-view-kubernetes-logs-events-and-pod-metrics-in-real-time"></a>Come visualizzare i log, gli eventi e le metriche dei pod di Kubernetes in tempo reale

Monitoraggio di Azure per i contenitori include la funzionalità Live Data (anteprima), una funzionalità di diagnostica avanzata che consente di accedere direttamente ai log del contenitore del servizio Azure Kubernetes (AKS) (stdout/stderror), agli eventi e alle metriche dei pod. Espone l'accesso `kubectl logs -c`diretto `kubectl get` a `kubectl top pods`, eventi e . Un riquadro della console mostra i log, gli eventi e le metriche generati dal motore contenitore per facilitare ulteriormente la risoluzione dei problemi in tempo reale.

In questo articolo viene fornita una panoramica dettagliata e viene illustrato come utilizzare questa funzionalità. 

>[!NOTE]
>I cluster AKS abilitati come [cluster privati](https://azure.microsoft.com/updates/aks-private-cluster/) non sono supportati con questa funzionalità. Questa funzione si basa sull'accesso diretto all'API Kubernetes tramite un server proxy dal browser. L'abilitazione della sicurezza di rete per bloccare l'API Kubernetes da questo proxy bloccherà il traffico. 

>[!NOTE]
>Questa funzionalità è disponibile in tutte le aree di Azure, inclusa la Cina di Azure.This feature is available in all Azure regions, including Azure China. Attualmente non è disponibile in Azure US Government.It is currently not available in Azure US Government.

Per assistenza nella configurazione o nella risoluzione dei problemi relativi alla funzionalità Live Data (anteprima), consulta la nostra [guida alla configurazione.](container-insights-livedata-setup.md) Questa funzionalità accede direttamente all'API Kubernetes e ulteriori informazioni sul modello di autenticazione sono disponibili [qui](https://kubernetes.io/docs/concepts/overview/kubernetes-api/). 

## <a name="live-data-preview-functionality-overview"></a>Panoramica della funzionalità Live Data (anteprima)

### <a name="search"></a>Ricerca

![Esempio di filtro del riquadro della console di Live Data](./media/container-insights-livedata-overview/livedata-pane-filter-example.png)

La funzionalità Live Data (anteprima) include la funzionalità di ricerca. Nel campo **Cerca** è possibile filtrare i risultati digitando una parola chiave o un termine e tutti i risultati corrispondenti vengono evidenziati per consentire una revisione rapida. Durante la visualizzazione degli eventi, è inoltre possibile limitare i risultati utilizzando la pillola **di filtro** trovata a destra della barra di ricerca. A seconda della risorsa selezionata, la pillola elenca un Pod, uno spazio dei nomi o un cluster da scegliere.  

### <a name="scroll-lock-and-pause"></a>BLOC SCORR e Pausa 

Per sospendere lo scorrimento automatico e controllare il comportamento del riquadro, consentendo di scorrere manualmente la nuova lettura dei dati, è possibile utilizzare l'opzione **Scorrimento.** Per riattivare lo scorrimento automatico, è sufficiente selezionare nuovamente l'opzione **Scorrimento.** È inoltre possibile sospendere il recupero dei dati del registro o dell'evento selezionando l'opzione **Pausa** e quando si è pronti per riprendere, è sufficiente selezionare **Riproduci**.  

![Il riquadro della console Live Data sospende la visualizzazione dal vivo](./media/container-insights-livedata-overview/livedata-pane-scroll-pause-example.png)

>[!IMPORTANT]
>Ti consigliamo di sospendere o mettere in pausa lo scorrimento automatico solo per un breve periodo di tempo durante la risoluzione di un problema. Queste richieste possono influire sulla disponibilità e la limitazione dell'API Kubernetes nel cluster. 

>[!IMPORTANT]
>Nessun dato viene memorizzato in modo permanente durante il funzionamento di questa funzione. Tutte le informazioni acquisite durante la sessione vengono eliminate quando si chiude il browser o ci si allontana da esso. I dati rimangono presenti solo per la visualizzazione all'interno della finestra di cinque minuti della funzionalità delle metriche; vengono eliminate anche tutte le metriche più vecchie di cinque minuti. Le query del buffer Live Data (anteprima) entro limiti di utilizzo della memoria ragionevoli (devono essere più specifiche qui, cosa è ragionevole?). 

## <a name="view-logs"></a>Visualizzare i log

È possibile visualizzare i dati di log in tempo reale così come vengono generati dal motore contenitore dalla visualizzazione **Nodi**, **Controller**e **Contenitori** . Per visualizzare i dati del registro, eseguire la procedura seguente.

1. Nel portale di Azure passare al gruppo di risorse cluster AKS e selezionare la risorsa AKS.

2. Nel dashboard del cluster AKS, in **Monitoraggio** sul lato sinistro, scegliere **Insights**. 

3. Selezionare la scheda **Nodi**, **Controller**o **Contenitori.**

4. Selezionare un oggetto dalla griglia delle prestazioni e nel riquadro delle proprietà che si trova sul lato destro selezionare **l'opzione Visualizza dati dinamici (anteprima).** Se il cluster AKS è configurato con Single Sign-On con Azure AD, viene richiesto di eseguire l'autenticazione al primo utilizzo durante la sessione del browser. Selezionare l'account e completare l'autenticazione con Azure.  

    >[!NOTE]
    >Quando si visualizzano i dati dall'area di lavoro di Log Analytics selezionando l'opzione **Visualizza in analisi** dal riquadro delle proprietà, i risultati della ricerca nei log verranno potenzialmente visualizzati **Nodi**, **Set daemon**, **Set di replica**, **Processi** **cron**, **Pod**e **Contenitori** che potrebbero non esistere più. Anche il tentativo di cercare nei log `kubectl` un contenitore che non è disponibile in avrà esito negativo anche in questo caso. Esaminare la funzionalità [Visualizza nell'analisi](container-insights-log-search.md#search-logs-to-analyze-data) per ulteriori informazioni sulla visualizzazione di log cronologici, eventi e metriche.  

Dopo la corretta autenticazione, il riquadro della console Live Data (anteprima) verrà visualizzato sotto la griglia dei dati sulle prestazioni in cui è possibile visualizzare i dati di log in un flusso continuo. Se l'indicatore di stato di recupero mostra un segno di spunta verde, che si trova all'estrema destra del riquadro, significa che i dati possono essere recuperati e inizia lo streaming alla console.  

![Opzione Dei dati della visualizzazione del riquadro delle proprietà dei nodi](./media/container-insights-livedata-overview/node-properties-pane.png)  

Il titolo del riquadro mostra il nome del contenitore con cui è raggruppato il contenitore.

## <a name="view-events"></a>Visualizzare eventi

È possibile visualizzare i dati degli eventi in tempo reale così come vengono generati dal motore contenitore dalla visualizzazione **Nodi**, **Controller**, **Contenitori**e **Distribuzioni (anteprima)** quando è selezionato un contenitore, un pod, un nodo, un set di repliche, DaemonSet, un processo, un cronJob o una distribuzione. Per visualizzare gli eventi, eseguire la procedura seguente.

1. Nel portale di Azure passare al gruppo di risorse cluster AKS e selezionare la risorsa AKS.

2. Nel dashboard del cluster AKS, in **Monitoraggio** sul lato sinistro, scegliere **Insights**. 

3. Selezionare la scheda **Nodi**, **Controller**, **Contenitori**o **Distribuzioni (anteprima).**

4. Selezionare un oggetto dalla griglia delle prestazioni e nel riquadro delle proprietà che si trova sul lato destro selezionare **l'opzione Visualizza dati dinamici (anteprima).** Se il cluster AKS è configurato con Single Sign-On con Azure AD, viene richiesto di eseguire l'autenticazione al primo utilizzo durante la sessione del browser. Selezionare l'account e completare l'autenticazione con Azure.  

    >[!NOTE]
    >Quando si visualizzano i dati dall'area di lavoro di Log Analytics selezionando l'opzione **Visualizza in analisi** dal riquadro delle proprietà, i risultati della ricerca nei log verranno potenzialmente visualizzati **Nodi**, **Set daemon**, **Set di replica**, **Processi** **cron**, **Pod**e **Contenitori** che potrebbero non esistere più. Anche il tentativo di cercare nei log `kubectl` un contenitore che non è disponibile in avrà esito negativo anche in questo caso. Esaminare la funzionalità [Visualizza nell'analisi](container-insights-log-search.md#search-logs-to-analyze-data) per ulteriori informazioni sulla visualizzazione di log cronologici, eventi e metriche.  

Dopo la corretta autenticazione, il riquadro della console Live Data (anteprima) verrà visualizzato sotto la griglia dei dati sulle prestazioni. Se l'indicatore di stato di recupero mostra un segno di spunta verde, che si trova all'estrema destra del riquadro, significa che i dati possono essere recuperati e inizia lo streaming alla console. 
    
Se l'oggetto selezionato è un contenitore, selezionare l'opzione **Eventi** nel riquadro. Se è stato selezionato un nodo, un pod o un controller, la visualizzazione degli eventi viene selezionata automaticamente. 

![Riquadro delle proprietà del controller](./media/container-insights-livedata-overview/controller-properties-live-event.png)  

Il titolo del riquadro mostra il nome del contenitore con cui è raggruppato il contenitore.

### <a name="filter-events"></a>Filtrare gli eventi 

Durante la visualizzazione degli eventi, è inoltre possibile limitare i risultati utilizzando la pillola **di filtro** trovata a destra della barra di ricerca. A seconda della risorsa selezionata, la pillola elenca un Pod, uno spazio dei nomi o un cluster da scegliere.  

## <a name="view-metrics"></a>Visualizzare le metriche 

È possibile visualizzare i dati delle metriche in tempo reale così come vengono generati dal motore del contenitore dalla visualizzazione **Nodi** o **Controller** solo quando viene selezionato un **Pod.** Per visualizzare le metriche, eseguire la procedura seguente.

1. Nel portale di Azure passare al gruppo di risorse cluster AKS e selezionare la risorsa AKS.

2. Nel dashboard del cluster AKS, in **Monitoraggio** sul lato sinistro, scegliere **Insights**. 

3. Selezionare la scheda **Nodi** o **Controller.**

4. Selezionare un oggetto **Pod** dalla griglia delle prestazioni e nel riquadro delle proprietà che si trova sul lato destro selezionare **l'opzione Visualizza dati dinamici (anteprima).** Se il cluster AKS è configurato con Single Sign-On con Azure AD, viene richiesto di eseguire l'autenticazione al primo utilizzo durante la sessione del browser. Selezionare l'account e completare l'autenticazione con Azure.  

    >[!NOTE]
    >Quando si visualizzano i dati dall'area di lavoro di Log Analytics selezionando l'opzione **Visualizza in analisi** dal riquadro delle proprietà, i risultati della ricerca nei log verranno potenzialmente visualizzati **Nodi**, **Set daemon**, **Set di replica**, **Processi** **cron**, **Pod**e **Contenitori** che potrebbero non esistere più. Anche il tentativo di cercare nei log `kubectl` un contenitore che non è disponibile in avrà esito negativo anche in questo caso. Esaminare la funzionalità [Visualizza nell'analisi](container-insights-log-search.md#search-logs-to-analyze-data) per ulteriori informazioni sulla visualizzazione di log cronologici, eventi e metriche.  

Dopo la corretta autenticazione, il riquadro della console Live Data (anteprima) verrà visualizzato sotto la griglia dei dati sulle prestazioni. I dati delle metriche vengono recuperati e iniziano lo streaming nella console per la presentazione nei due grafici. Il titolo del riquadro mostra il nome del contenitore con cui è raggruppato il contenitore.

![Visualizza esempio di metriche del pod](./media/container-insights-livedata-overview/pod-properties-live-metrics.png)  

## <a name="next-steps"></a>Passaggi successivi

- Per continuare a imparare a usare Monitoraggio di Azure e monitorare altri aspetti del cluster AKS, vedere Visualizzare l'integrità del [servizio Azure Kubernetes](container-insights-analyze.md).

- Visualizzare esempi di query di [log](container-insights-log-search.md#search-logs-to-analyze-data) per visualizzare query ed esempi predefiniti per creare avvisi, visualizzazioni o eseguire ulteriori analisi dei cluster.
