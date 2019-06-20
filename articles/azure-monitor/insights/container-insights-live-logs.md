---
title: Visualizzare i log di Monitoraggio di Azure per i contenitori in tempo reale | Microsoft Docs
description: Questo articolo descrive la visualizzazione dei log del contenitore (stdout o stderr) e gli eventi in tempo reale senza dover usare kubectl con monitoraggio di Azure per contenitori.
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
ms.date: 06/19/2019
ms.author: magoedte
ms.openlocfilehash: 7fd9248fd38054b7f0e1fad2888d8b0d4cf2e60c
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274234"
---
# <a name="how-to-view-logs-and-events-in-real-time-preview"></a>Come visualizzare i log ed eventi in tempo reale (anteprima)
Monitoraggio di Azure per contenitori include una funzionalità, che è attualmente in anteprima, che offre una visualizzazione in tempo reale i log dei contenitori di Azure Kubernetes Service (AKS) (stdout o stderr) e gli eventi senza dover eseguire i comandi di kubectl. Quando si seleziona l'opzione desiderata, un nuovo riquadro viene visualizzato sotto la tabella di dati delle prestazioni nel **i nodi**, **controller**, e **contenitori** visualizzazione. Mostra la registrazione in tempo reale e gli eventi generati dal modulo del contenitore per ulteriore assistenza nella risoluzione dei problemi in tempo reale.

>[!NOTE]
>**Collaboratore** accesso alla risorsa del cluster è necessario per usare questa funzionalità.
>

I log in tempo reale supportano tre metodi diversi per controllare l'accesso ai log:

1. Servizio Azure Kubernetes senza autorizzazione del controllo degli accessi in base al ruolo di Kubernetes abilitata
2. Servizio Azure Kubernetes con autorizzazione del controllo degli accessi in base al ruolo di Kubernetes abilitata
3. Servizio contenitore di AZURE abilitata con Azure Active Directory (AD) basato su SAML single sign-in

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

2. Se si sta configurando, per la prima volta, si crea l'associazione della regola cluster eseguendo il comando seguente: `kubectl create -f LogReaderRBAC.yaml`. Se è abilitato in precedenza il supporto per visualizzare in anteprima in tempo reale dei log prima è stato introdotto in tempo reale i registri eventi, per aggiornare la configurazione, eseguire il comando seguente: `kubectl apply -f LogReaderRBAC.yml`.

## <a name="configure-aks-with-azure-active-directory"></a>Configurare il servizio Azure Kubernetes con Azure Active Directory

Il servizio Azure Kubernetes può essere configurato in modo da usare Azure Active Directory (AD) per l'autenticazione utente. Se si sta configurando, per la prima volta, vedere [integrare Azure Active Directory con Azure Kubernetes Service](../../aks/azure-ad-integration.md). Durante la procedura per creare il [applicazione client](../../aks/azure-ad-integration.md#create-the-client-application), specificare quanto segue:

- **(Facoltativo) URI di reindirizzamento**: Si tratta di un **Web** tipo di applicazione e il valore di URL di base devono essere `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.
- Dopo la registrazione dell'applicazione, dal **Overview** pagina Scegli **autenticazione** nel riquadro a sinistra. Nel **Authentication** nella pagina **impostazioni avanzate** concedere in modo implicito **i token di accesso** e **i token ID** e quindi salvare il modifiche.

>[!NOTE]
>Configurazione dell'autenticazione con Azure Active Directory per single sign-on può essere eseguita solo durante la distribuzione iniziale di un nuovo cluster AKS. Non è possibile configurare l'accesso Single Sign-On per un cluster del servizio Azure Kubernetes già distribuito.
  
>[!IMPORTANT]
>Se è stato riconfigurato Azure AD per l'autenticazione utente tramite l'URI aggiornato, cancellare la cache del browser per verificare che il token di autenticazione aggiornate verrà scaricato e applicato.   

## <a name="view-live-logs-and-events"></a>Visualizzare i log in tempo reale ed eventi

È possibile visualizzare gli eventi del log in tempo reale mentre vengono generati dal motore di contenitore del **i nodi**, **controller**, e **contenitori** visualizzazione. Nel riquadro proprietà, si seleziona **visualizzare i dati in tempo reale (anteprima)** opzione e un riquadro viene visualizzato sotto la tabella di dati delle prestazioni in cui è possibile visualizzare i log ed eventi in un flusso continuo.

![Opzione di nodo proprietà riquadro Visualizza i log in tempo reale](./media/container-insights-live-logs/node-properties-live-logs-01.png)  

I messaggi di log e degli eventi sono limitati in base a quale tipo di risorsa è selezionato nella visualizzazione.

| visualizzazione | Tipo di risorsa | Log o un evento | Dati presentati |
|------|---------------|--------------|----------------|
| Nodi | Nodo | Event | Quando si seleziona un nodo gli eventi non vengono filtrati e mostrano gli eventi di Kubernetes a livello di cluster. Il titolo del riquadro mostra il nome del cluster. |
| Nodi | Pod | Event | Quando viene selezionato un pod vengono filtrati gli eventi per lo spazio dei nomi. Il titolo del riquadro mostra lo spazio dei nomi del pod. | 
| Controller | Pod | Event | Quando viene selezionato un pod vengono filtrati gli eventi per lo spazio dei nomi. Il titolo del riquadro mostra lo spazio dei nomi del pod. |
| Controller | Controller | Event | Quando viene selezionato un controller vengono filtrati gli eventi per lo spazio dei nomi. Il titolo del riquadro mostra lo spazio dei nomi del controller. |
| Nodi/controller/contenitori | Contenitore | Log | Il titolo del riquadro mostra che il nome del pod del contenitore è raggruppato con. |

Se il cluster del servizio Azure Kubernetes è configurato con il Single Sign-On di AAD, durante la sessione del browser viene chiesto di eseguire l'autenticazione per il primo utilizzo. Selezionare l'account e completare l'autenticazione con Azure.  

Dopo l'autenticazione, il riquadro dei log in tempo reale verrà visualizzato nella parte inferiore del riquadro centrale. Se l'indicatore dello stato di recupero mostra un segno di spunta verde sul lato destro del riquadro, significa che è possibile recuperare dati.
    
  ![Dati recuperati nel riquadro dei log in tempo reale](./media/container-insights-live-logs/live-logs-pane-01.png)  

Nella barra di ricerca, è possibile filtrare per parola chiave per evidenziare il testo nel log o l'evento e nella barra di ricerca all'estrema destra, Mostra il numero di risultati corrispondenti out del filtro.

  ![Esempio di filtro nel riquadro dei log in tempo reale](./media/container-insights-live-logs/live-logs-pane-filter-example-01.png)

Durante la visualizzazione eventi, è inoltre possibile limitare i risultati usando il **filtro** partite trovato a destra della barra di ricerca. A seconda della risorsa per cui è stata selezionata, le partite elenca un pod, lo spazio dei nomi o cluster tra cui scegliere.  

Per sospendere per lo scorrimento automatico e controllare il comportamento del riquadro e consentono di scorrere manualmente i nuovi dati di lettura, fare clic sui **scorrimento** opzione. Per abilitare nuovamente per lo scorrimento automatico, è sufficiente fare clic il **scorrimento** opzione nuovamente. È anche possibile sospendere il recupero dei dati di log o eventi facendo clic sui **sospendere** opzione e quando si è pronti per riprendere, è sufficiente fare clic su **riprodurre**.  

![Visualizzazione dei log in tempo reale in pausa](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

È possibile passare al log di monitoraggio di Azure per visualizzare i log dei contenitori cronologici selezionando **visualizzare i log dei contenitori** nell'elenco a discesa **vista in analitica**.

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni su come usare Monitoraggio di Azure e monitorare altri aspetti del cluster del servizio Azure Kubernetes, vedere [Visualizzare l'integrità del servizio Kubernetes di Azure](container-insights-analyze.md).
- Vista [esempi di query di log](container-insights-log-search.md#search-logs-to-analyze-data) per visualizzare query predefinito ed esempi per valutare o personalizzare per gli avvisi, la visualizzazione o l'analisi dei cluster.
