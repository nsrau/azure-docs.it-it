---
title: Visualizzare Monitoraggio di Azure per le distribuzioni di contenitori (anteprima)View Azure Monitor for containers Deployments (preview) Documenti Microsoft
description: Questo articolo descrive la visualizzazione in tempo reale delle distribuzioni di Kubernetes senza usare kubectl in Monitoraggio di Azure per i contenitori.
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

Con Monitoraggio di Azure per i contenitori, la funzionalità Distribuzioni (anteprima) della visualizzazione emula l'accesso diretto agli oggetti di distribuzione di Kubernetes in tempo reale esponendo i comandi `kubeclt get deployments` e `kubectl describe deployment {your deployment}` . 

>[!NOTE]
>I cluster AKS abilitati come [cluster privati](https://azure.microsoft.com/updates/aks-private-cluster/) non sono supportati con questa funzionalità. Questa funzione si basa sull'accesso diretto all'API Kubernetes tramite un server proxy dal browser. L'abilitazione della sicurezza di rete per bloccare l'API Kubernetes da questo proxy bloccherà il traffico. 

>[!NOTE]
>Questa funzionalità è disponibile in tutte le aree di Azure, inclusa la Cina di Azure.This feature is available in all Azure regions, including Azure China. Attualmente non è disponibile in Azure US Government.It is currently not available in Azure US Government.

Per ulteriori informazioni, consultare la documentazione di Kubernetes sulle [distribuzioni](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/). 

## <a name="how-it-works"></a>Funzionamento

La funzionalità Live Data (anteprima) accede direttamente all'API Kubernetes e ulteriori informazioni sul modello di autenticazione sono disponibili [qui](https://kubernetes.io/docs/concepts/overview/kubernetes-api/). 

La funzionalità Distribuzioni (anteprima) esegue un carico una tantera (aggiornabile) sull'endpoint `/apis/apps/v1/deployments`delle distribuzioni. Consente di selezionare una determinata distribuzione e caricare i dettagli `/apis/apps/v1/namespaces/${nameSpace}/deployments/${deploymentName}`di definimento della distribuzione specifica sull'endpoint di distribuzione. 

Se si seleziona **Aggiorna** nella parte superiore sinistra della pagina, l'elenco di distribuzione viene aggiornato. In questo modo si `kubectl` simula la riesecuzione del comando. 

>[!IMPORTANT]
>Nessun dato viene memorizzato in modo permanente durante il funzionamento di questa funzione. Tutte le informazioni acquisite durante la sessione vengono eliminate quando si chiude il browser o ci si allontana da esso.  

>[!NOTE]
>Non è possibile aggiungere dati Live Data (anteprima) dalla console a un dashboard di Azure.You cannot pin Live Data (Preview) data from the console to an Azure dashboard.

## <a name="deployments-describe"></a>Le distribuzioni descrivono

Per visualizzare Descrivere i dettagli di una `kubectl describe deployment`distribuzione, che equivale a , eseguire la procedura seguente.

1. Nel portale di Azure passare al gruppo di risorse cluster AKS e selezionare la risorsa AKS.

2. Nel dashboard del cluster AKS, in **Monitoraggio** sul lato sinistro, scegliere **Insights**. 

3. Selezionare la scheda **Distribuzioni (anteprima).**

    ![Visualizzazione Distribuzioni nel portale di AzureDeployments view in the Azure portal](./media/container-insights-livedata-deployments/deployment-view.png)

La visualizzazione mostra un elenco di tutte le distribuzioni in esecuzione insieme allo spazio `kubectl get deployments –all-namespaces`dei nomi e ad altre informazioni dettagliate, emulando l'esecuzione del comando. È possibile ordinare i risultati selezionando una qualsiasi delle colonne. 

![Dettagli del riquadro delle proprietà delle distribuzioni](./media/container-insights-livedata-deployments/deployment-properties-pane-details.png)

Quando si seleziona una distribuzione dall'elenco, viene visualizzato automaticamente un riquadro delle proprietà sul lato destro della pagina. Vengono visualizzate informazioni relative alla distribuzione selezionata che `kubectl describe deployment {deploymentName}`verrà visualizzata se è stato eseguito il comando . Avrete notato che le informazioni di descrivere mancano alcuni dettagli. In particolare manca il **modello.** Selezionando la scheda **Raw** è possibile passare ai dettagli di Descrivere non analizzati.  

![Dettagli non elaborati del riquadro delle proprietà delle distribuzioni](./media/container-insights-livedata-deployments/deployment-properties-pane-raw.png)

Mentre si esaminano i dettagli di distribuzione, è possibile visualizzare i log e gli eventi del contenitore in tempo reale. Selezionare Mostra **console live** e il riquadro della console Live Data (anteprima) verrà visualizzato sotto la griglia dei dati delle distribuzioni in cui è possibile visualizzare i dati del log in tempo reale in un flusso continuo. Se l'indicatore di stato di recupero mostra un segno di spunta verde, che si trova all'estrema destra del riquadro, significa che i dati possono essere recuperati e inizia lo streaming alla console.

È inoltre possibile filtrare in base agli eventi a livello di spazio dei nomi o di cluster. Per altre informazioni sulla visualizzazione dei dati in tempo reale nella console, vedere [Visualizzare Live Data (anteprima) con Monitoraggio di Azure per i contenitori.](container-insights-livedata-overview.md) 

![Le distribuzioni visualizzano i dati in tempo reale nella console](./media/container-insights-livedata-deployments/deployments-console-view-events.png)

## <a name="next-steps"></a>Passaggi successivi

- Per continuare a imparare a usare Monitoraggio di Azure e monitorare altri aspetti del cluster AKS, vedere Visualizzare l'integrità del [servizio Azure Kubernetes](container-insights-analyze.md).

- Visualizzare esempi di query di [log](container-insights-log-search.md#search-logs-to-analyze-data) per visualizzare query ed esempi predefiniti per creare avvisi, visualizzazioni o eseguire ulteriori analisi dei cluster.
