---
title: Visualizzare monitoraggio di Azure per le distribuzioni di contenitori (anteprima) | Microsoft Docs
description: Questo articolo descrive la visualizzazione in tempo reale delle distribuzioni Kubernetes senza usare kubectl in monitoraggio di Azure per i contenitori.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 7d0344851e1db8c014a1bb16b228a0c2f76444d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75404765"
---
# <a name="how-to-view-deployments-preview-in-real-time"></a>Come visualizzare le distribuzioni (anteprima) in tempo reale

Con monitoraggio di Azure per i contenitori, la funzionalità Visualizza distribuzioni (anteprima) emula l'accesso diretto agli oggetti di distribuzione Kubernetes in tempo reale esponendo i `kubeclt get deployments` comandi e. `kubectl describe deployment {your deployment}` 

>[!NOTE]
>I cluster AKS abilitati come [cluster privati](https://azure.microsoft.com/updates/aks-private-cluster/) non sono supportati con questa funzionalità. Questa funzionalità si basa sull'accesso diretto all'API Kubernetes tramite un server proxy dal browser. L'abilitazione della sicurezza di rete per bloccare l'API Kubernetes da questo proxy bloccherà il traffico. 

>[!NOTE]
>Questa funzionalità è disponibile in tutte le aree di Azure, tra cui Azure Cina. Attualmente non è disponibile in Azure per enti pubblici statunitensi.

Per altre informazioni, vedere la documentazione di Kubernetes sulle [distribuzioni](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/). 

## <a name="how-it-works"></a>Funzionamento

La funzionalità dati in tempo reale (anteprima) consente di accedere direttamente all'API Kubernetes e altre informazioni sul modello di autenticazione sono disponibili [qui](https://kubernetes.io/docs/concepts/overview/kubernetes-api/). 

La funzionalità distribuzioni (anteprima) consente di eseguire un caricamento una volta (aggiornabile) nell'endpoint `/apis/apps/v1/deployments`distribuzioni. Consente di selezionare una distribuzione specifica e di caricare i dettagli della descrizione per la distribuzione specifica nell'endpoint `/apis/apps/v1/namespaces/${nameSpace}/deployments/${deploymentName}`di distribuzione. 

Selezionando **Aggiorna** nella parte superiore sinistra della pagina viene aggiornato l'elenco di distribuzione. Questa operazione simula la ripetizione dell'esecuzione `kubectl` del comando. 

>[!IMPORTANT]
>Nessun dato viene archiviato in modo permanente durante il funzionamento di questa funzionalità. Tutte le informazioni acquisite durante la sessione vengono eliminate quando si chiude il browser o si esce dall'esplorazione.  

>[!NOTE]
>Non è possibile aggiungere dati in tempo reale (anteprima) dalla console di a un dashboard di Azure.

## <a name="deployments-describe"></a>Descrizione delle distribuzioni

Per visualizzare i dettagli della descrizione di una distribuzione, equivalente a `kubectl describe deployment`, seguire questa procedura.

1. Nella portale di Azure passare al gruppo di risorse del cluster AKS e selezionare la risorsa AKS.

2. Nel dashboard del cluster AKS, in **monitoraggio** sul lato sinistro, scegliere **Insights**. 

3. Selezionare la scheda **distribuzioni (anteprima)** .

    ![Visualizzazione distribuzioni nel portale di Azure](./media/container-insights-livedata-deployments/deployment-view.png)

La visualizzazione Mostra un elenco di tutte le distribuzioni in esecuzione insieme allo spazio dei nomi e altre informazioni dettagliate, emulando l'esecuzione `kubectl get deployments –all-namespaces`del comando. È possibile ordinare i risultati selezionando una delle colonne. 

![Dettagli riquadro Proprietà distribuzioni](./media/container-insights-livedata-deployments/deployment-properties-pane-details.png)

Quando si seleziona una distribuzione dall'elenco, un riquadro delle proprietà viene visualizzato automaticamente sul lato destro della pagina. Mostra informazioni correlate alla distribuzione selezionata che verrebbero visualizzate se è stato eseguito il comando `kubectl describe deployment {deploymentName}`. Si potrebbe notare che le informazioni di descrizione non contengono alcuni dettagli. In particolare, il **modello** è mancante. Selezionando la scheda non **elaborata** è possibile passare ai dettagli della descrizione non analizzata.  

![Dettagli non elaborati riquadro Proprietà distribuzioni](./media/container-insights-livedata-deployments/deployment-properties-pane-raw.png)

Mentre si esaminano i dettagli di distribuzione, è possibile visualizzare in tempo reale i log e gli eventi del contenitore. Selezionare la **console attiva e il** riquadro della console dati in tempo reale (anteprima) verranno visualizzati sotto la griglia dati distribuzioni in cui è possibile visualizzare i dati del log in tempo reale in un flusso continuo. Se l'indicatore di stato di recupero Mostra un segno di spunta verde, che si trova all'estrema destra del riquadro, significa che i dati possono essere recuperati e inizia lo streaming alla console.

È anche possibile filtrare in base agli eventi a livello di spazio dei nomi o cluster. Per altre informazioni sulla visualizzazione dei dati in tempo reale nella console, vedere [visualizzare i dati dinamici (anteprima) con monitoraggio di Azure per i contenitori](container-insights-livedata-overview.md). 

![Le distribuzioni visualizzano i dati dinamici nella console](./media/container-insights-livedata-deployments/deployments-console-view-events.png)

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come usare monitoraggio di Azure e monitorare altri aspetti del cluster AKS, vedere [visualizzare l'integrità del servizio Azure Kubernetes](container-insights-analyze.md).

- Visualizzare gli [esempi di query di log](container-insights-log-search.md#search-logs-to-analyze-data) per visualizzare query e esempi predefiniti per creare avvisi, visualizzazioni o eseguire ulteriori analisi dei cluster.
