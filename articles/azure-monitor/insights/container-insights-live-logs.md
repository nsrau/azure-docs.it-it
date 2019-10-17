---
title: Visualizzare i log di Monitoraggio di Azure per i contenitori in tempo reale | Microsoft Docs
description: Questo articolo descrive la visualizzazione in tempo reale dei log del contenitore (stdout/stderr) ed eventi senza usare kubectl con monitoraggio di Azure per i contenitori.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: d947b44177e9aa5777d759286d982e974e378497
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389781"
---
# <a name="how-to-view-logs-and-events-in-real-time-preview"></a>Come visualizzare i log e gli eventi in tempo reale (anteprima)
Il monitoraggio di Azure per i contenitori include una funzionalità, attualmente in anteprima, che fornisce una visualizzazione in tempo reale dei log del contenitore del servizio Kubernetes di Azure (stdout/stderr) ed eventi senza dover eseguire comandi kubectl. Quando si seleziona una delle opzioni, viene visualizzato un nuovo riquadro sotto la tabella dei dati sulle prestazioni nella visualizzazione **nodi**, **controller**e **contenitori** . Mostra la registrazione in tempo reale e gli eventi generati dal motore di contenitori per facilitare ulteriormente la risoluzione dei problemi in tempo reale.

>[!NOTE]
>Questa funzionalità è disponibile in tutte le aree di Azure, tra cui Azure Cina. Attualmente non è disponibile in Azure per enti pubblici statunitensi.

>[!NOTE]
>Per il corretto funzionamento di questa funzionalità, è necessario l'accesso del **ruolo utente cluster del servizio Azure Kubernetes** alla risorsa cluster. [Altre informazioni sul ruolo utente cluster di Azure Kubernetes](https://docs.microsoft.com/en-us/azure/aks/control-kubeconfig-access#available-cluster-roles-permissions).

I log attivi supportano tre diversi metodi per controllare l'accesso ai log:

1. Servizio Azure Kubernetes senza autorizzazione del controllo degli accessi in base al ruolo di Kubernetes abilitata
2. Servizio Azure Kubernetes con autorizzazione del controllo degli accessi in base al ruolo di Kubernetes abilitata
3. AKS abilitato con Single Sign-on basato su SAML di Azure Active Directory (AD)

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Cluster Kubernetes con controllo degli accessi in base al ruolo abilitato
 
Se si ha un cluster Kubernetes non configurato con l'autorizzazione del controllo degli accessi in base al ruolo di Kubernetes oppure integrato con il Single Sign-On di Azure AD, non è necessario eseguire la procedura seguente. Poiché l'autorizzazione Kubernetes usa kube-api, sono necessarie autorizzazioni di sola lettura.

## <a name="kubernetes-rbac-authorization"></a>Autorizzazione del controllo degli accessi in base al ruolo di Kubernetes
Se è stata abilitata l'autorizzazione del controllo degli accessi in base al ruolo di Kubernetes, è necessario applicare l'associazione di ruolo del cluster. I passaggi di esempio seguenti illustrano come configurare l'associazione di ruolo del cluster da questo modello di configurazione yaml. 

1. Copiare e incollare il file yaml e salvarlo come LogReaderRBAC.yaml.  

    ```
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRole 
    metadata: 
       name: containerHealth-log-reader 
    rules: 
       - apiGroups: [""] 
         resources: ["pods/log", "events"] 
         verbs: ["get", "list"]  
    --- 
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRoleBinding 
    metadata: 
       name: containerHealth-read-logs-global 
    roleRef: 
        kind: ClusterRole 
        name: containerHealth-log-reader 
        apiGroup: rbac.authorization.k8s.io 
    subjects: 
       - kind: User 
         name: clusterUser 
         apiGroup: rbac.authorization.k8s.io
    ```

2. Se si sta configurando per la prima volta, si applica l'associazione regola cluster eseguendo il comando seguente: `kubectl create -f LogReaderRBAC.yaml`. Se in precedenza è stato abilitato il supporto per l'anteprima dei log attivi prima di aver introdotto i registri eventi attivi, per aggiornare la configurazione, eseguire il comando seguente: `kubectl apply -f LogReaderRBAC.yaml`.

## <a name="configure-aks-with-azure-active-directory"></a>Configurare il servizio Azure Kubernetes con Azure Active Directory

Il servizio Azure Kubernetes può essere configurato in modo da usare Azure Active Directory (AD) per l'autenticazione utente. Se si sta configurando per la prima volta, vedere [integrare Azure Active Directory con il servizio Azure Kubernetes](../../aks/azure-ad-integration.md). Durante i passaggi per creare l' [applicazione client](../../aks/azure-ad-integration.md#create-the-client-application), specificare quanto segue:

-  **URI di reindirizzamento**: è necessario creare due tipi di applicazione **Web** . Il primo valore dell'URL di base deve essere `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` e il secondo valore dell'URL di base deve essere `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.
- Dopo la registrazione dell'applicazione, nella pagina **Panoramica** selezionare **autenticazione** nel riquadro a sinistra. Nella pagina **autenticazione** , in **Impostazioni avanzate** , concedere in modo implicito **token di accesso** e **token ID** , quindi salvare le modifiche.

>[!NOTE]
>Se si usa questa funzionalità nell'area di Azure Cina, il primo valore dell'URL di base deve essere `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` e il secondo valore dell'URL di base deve essere `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.

>[!NOTE]
>La configurazione dell'autenticazione con Azure Active Directory per l'accesso Single Sign-on può essere eseguita solo durante la distribuzione iniziale di un nuovo cluster AKS. Non è possibile configurare l'accesso Single Sign-On per un cluster del servizio Azure Kubernetes già distribuito.
  
>[!IMPORTANT]
>Se è stato riconfigurato Azure AD per l'autenticazione utente usando l'URI aggiornato, deselezionare la cache del browser per verificare che il token di autenticazione aggiornato venga scaricato e applicato.   

## <a name="view-live-logs-and-events"></a>Visualizza registri ed eventi in tempo reale

È possibile visualizzare gli eventi di log in tempo reale generati dal motore di contenitori dalla visualizzazione **nodi**, **controller**e **contenitori** . Dal riquadro Proprietà selezionare **Visualizza dati in tempo reale (anteprima)** e viene visualizzato un riquadro sotto la tabella dati sulle prestazioni in cui è possibile visualizzare i log e gli eventi in un flusso continuo.

![Opzione per la visualizzazione dei log attivi del riquadro Proprietà nodo](./media/container-insights-live-logs/node-properties-live-logs-01.png)  

I messaggi di log e di evento sono limitati in base al tipo di risorsa selezionato nella vista.

| Visualizza | Tipo di risorsa | Log o evento | Dati presentati |
|------|---------------|--------------|----------------|
| Nodi | Nodo | Event | Quando gli eventi selezionati di un nodo non vengono filtrati e mostrano eventi Kubernetes a livello di cluster. Il titolo del riquadro Mostra il nome del cluster. |
| Nodi | Pod | Event | Quando un pod è selezionato, gli eventi vengono filtrati in base al relativo spazio dei nomi. Il titolo del riquadro Mostra lo spazio dei nomi del Pod. | 
| Controller | Pod | Event | Quando un pod è selezionato, gli eventi vengono filtrati in base al relativo spazio dei nomi. Il titolo del riquadro Mostra lo spazio dei nomi del Pod. |
| Controller | Controller | Event | Quando si seleziona un controller, gli eventi vengono filtrati in base al relativo spazio dei nomi. Il titolo del riquadro Mostra lo spazio dei nomi del controller. |
| Nodi/controller/contenitori | Contenitore | Log | Il titolo del riquadro Mostra il nome del Pod con il quale viene raggruppato il contenitore. |

Se il cluster del servizio Azure Kubernetes è configurato con il Single Sign-On di AAD, durante la sessione del browser viene chiesto di eseguire l'autenticazione per il primo utilizzo. Selezionare l'account e completare l'autenticazione con Azure.  

Dopo l'autenticazione, il riquadro dei log in tempo reale verrà visualizzato nella parte inferiore del riquadro centrale. Se l'indicatore dello stato di recupero mostra un segno di spunta verde sul lato destro del riquadro, significa che è possibile recuperare dati.
    
  ![Dati recuperati nel riquadro dei log in tempo reale](./media/container-insights-live-logs/live-logs-pane-01.png)  

Nella barra di ricerca è possibile filtrare in base alla parola chiave per evidenziare il testo nel log o nell'evento e, nella barra di ricerca all'estrema destra, viene visualizzato il numero di risultati corrispondenti al filtro.

  ![Esempio di filtro nel riquadro dei log in tempo reale](./media/container-insights-live-logs/live-logs-pane-filter-example-01.png)

Quando si visualizzano gli eventi, è anche possibile limitare i risultati usando la pillola di **filtro** trovata a destra della barra di ricerca. A seconda della risorsa selezionata, la pillola elenca un pod, uno spazio dei nomi o un cluster da cui scegliere.  

Per sospendere lo scorrimento automatico e controllare il comportamento del riquadro e consentire di scorrere manualmente i nuovi dati letti, fare clic sull'opzione di **scorrimento** . Per riabilitare lo scorrimento automatico, è sufficiente fare di nuovo clic sull'opzione **Scroll** . È anche possibile sospendere il recupero dei dati di log o degli eventi facendo clic sull'opzione **Sospendi** e, quando si è pronti per riprendere, è sufficiente fare clic su **Riproduci**.  

![Visualizzazione dei log in tempo reale in pausa](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

È possibile passare ai log di monitoraggio di Azure per visualizzare i log dei contenitori cronologici selezionando **Visualizza log** del contenitore **nella visualizzazione elenco a discesa in Analytics**.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su come usare Monitoraggio di Azure e monitorare altri aspetti del cluster del servizio Azure Kubernetes, vedere [Visualizzare l'integrità del servizio Kubernetes di Azure](container-insights-analyze.md).

- Visualizzare gli [esempi di query di log](container-insights-log-search.md#search-logs-to-analyze-data) per visualizzare query e esempi predefiniti per valutare o personalizzare gli avvisi, la visualizzazione o l'analisi dei cluster.
