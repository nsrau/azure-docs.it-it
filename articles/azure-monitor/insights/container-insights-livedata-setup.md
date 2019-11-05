---
title: Configurare monitoraggio di Azure per i contenitori in tempo reale (anteprima) | Microsoft Docs
description: Questo articolo descrive come configurare la visualizzazione in tempo reale dei log del contenitore (stdout/stderr) ed eventi senza usare kubectl con monitoraggio di Azure per i contenitori.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2019
ms.author: magoedte
ms.openlocfilehash: 596c5ad378d471c6c98616a48f44e96c365ee0bb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514368"
---
# <a name="how-to-setup-the-live-data-preview-feature"></a>Come configurare la funzionalità dati in tempo reale (anteprima)

Per visualizzare i dati in tempo reale (anteprima) con monitoraggio di Azure per i contenitori dai cluster di Azure Kubernetes Service (AKS), è necessario configurare l'autenticazione per concedere l'autorizzazione per l'accesso ai dati di Kubernetes. Questa configurazione di sicurezza consente l'accesso in tempo reale ai dati tramite l'API Kubernetes direttamente nel portale di Azure.

Questa funzionalità supporta tre diversi metodi per controllare l'accesso ai log, agli eventi e alle metriche:

- Servizio Azure Kubernetes senza autorizzazione del controllo degli accessi in base al ruolo di Kubernetes abilitata
- Servizio Azure Kubernetes con autorizzazione del controllo degli accessi in base al ruolo di Kubernetes abilitata
- AKS abilitato con Single Sign-on basato su SAML di Azure Active Directory (AD)

Queste istruzioni richiedono l'accesso amministrativo al cluster Kubernetes e, se si configura per l'uso di Azure Active Directory (AD) per l'autenticazione degli utenti, l'accesso amministrativo ai Azure AD.  

Questo articolo illustra come configurare l'autenticazione per controllare l'accesso alla funzionalità dati in tempo reale (anteprima) dal cluster:

- Cluster AKS abilitato per il controllo degli accessi in base al ruolo
- Azure Active Directory cluster AKS integrato. 

>[!NOTE]
>I cluster AKS abilitati come [cluster privati](https://azure.microsoft.com/updates/aks-private-cluster/) non sono supportati con questa funzionalità. Questa funzionalità si basa sull'accesso diretto all'API Kubernetes tramite un server proxy dal browser. L'abilitazione della sicurezza di rete per bloccare l'API Kubernetes da questo proxy bloccherà il traffico. 

>[!NOTE]
>Questa funzionalità è disponibile in tutte le aree di Azure, tra cui Azure Cina. Attualmente non è disponibile in Azure per enti pubblici statunitensi.

## <a name="authentication-model"></a>Modello di autenticazione

Le funzionalità dei dati in tempo reale (anteprima) usano l'API Kubernetes, identica allo strumento da riga di comando `kubectl`. Gli endpoint dell'API Kubernetes utilizzano un certificato autofirmato che non sarà possibile convalidare nel browser. Questa funzionalità Usa un proxy interno per convalidare il certificato con il servizio AKS, assicurando che il traffico sia attendibile.

Il portale di Azure richiede di convalidare le credenziali di accesso per un cluster Azure Active Directory e di reindirizzare l'utente alla configurazione della registrazione del client durante la creazione del cluster e riconfigurata in questo articolo. Questo comportamento è simile al processo di autenticazione richiesto dal `kubectl`. 

>[!NOTE]
>L'autorizzazione per il cluster viene gestita da Kubernetes e dal modello di sicurezza con cui è configurato. Per gli utenti che accedono a questa funzionalità è necessaria l'autorizzazione a scaricare la configurazione Kubernetes (*kubeconfig*), simile a `az aks get-credentials -n {your cluster name} -g {your resource group}`in esecuzione. Questo file di configurazione contiene il token di autorizzazione e di autenticazione per il **ruolo utente del cluster di servizi Kubernetes di Azure**, nel caso di cluster Azure e abilitati per il controllo degli accessi in base al ruolo, senza autorizzazione RBAC abilitata Contiene informazioni sui Azure AD e i dettagli di registrazione del client quando AKS è abilitato con l'accesso Single Sign-on basato su SAML di Azure Active Directory (AD).

>[!IMPORTANT]
>Per scaricare il `kubeconfig` e usare questa funzionalità, gli utenti di questa funzionalità richiedono il [ruolo utente del cluster Kubernetes di Azure](../../azure/role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role permissions) per il cluster. Per utilizzare questa funzionalità, gli utenti **non** necessitano dell'accesso come collaboratore al cluster. 

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Cluster Kubernetes con controllo degli accessi in base al ruolo abilitato

Se si ha un cluster Kubernetes non configurato con l'autorizzazione del controllo degli accessi in base al ruolo di Kubernetes oppure integrato con il Single Sign-On di Azure AD, non è necessario eseguire la procedura seguente. Questo perché per impostazione predefinita sono disponibili autorizzazioni amministrative in una configurazione non RBAC.

## <a name="configure-kubernetes-rbac-authentication"></a>Configurare l'autenticazione Kubernetes RBAC

Quando si Abilita l'autorizzazione RBAC di Kubernetes, vengono usati due utenti: **clusterUser** e **clusterAdmin** per accedere all'API Kubernetes. Questa operazione è simile all'esecuzione di `az aks get-credentials -n {cluster_name} -g {rg_name}` senza l'opzione amministrativa. Ciò significa che è necessario concedere a **clusterUser** l'accesso agli endpoint nell'API Kubernetes.

I passaggi di esempio seguenti illustrano come configurare l'associazione di ruolo del cluster da questo modello di configurazione yaml.

1. Copiare e incollare il file yaml e salvarlo come LogReaderRBAC.yaml.  

    ```
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRole 
    metadata: 
       name: containerHealth-log-reader 
    rules: 
        - apiGroups: ["", "metrics.k8s.io", "extensions", "apps"] 
          resources: 
             - "pods/log" 
             - "events" 
             - "nodes" 
             - "pods" 
             - "deployments" 
             - "replicasets" 
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

2. Per aggiornare la configurazione, eseguire il comando seguente: `kubectl apply -f LogReaderRBAC.yaml`.

>[!NOTE] 
> Se è stata applicata una versione precedente del file di `LogReaderRBAC.yaml` al cluster, aggiornarla copiando e incollando il nuovo codice illustrato nel passaggio 1 precedente, quindi eseguire il comando illustrato nel passaggio 2 per applicarlo al cluster.

## <a name="configure-ad-integrated-authentication"></a>Configurare l'autenticazione integrata di Active Directory 

Un cluster AKS configurato per usare Azure Active Directory (AD) per l'autenticazione utente usa le credenziali di accesso della persona che accede a questa funzionalità. In questa configurazione è possibile accedere a un cluster AKS usando il Azure AD token di autenticazione.

Azure AD la registrazione del client deve essere riconfigurata per consentire al portale di Azure di reindirizzare le pagine di autorizzazione come URL di reindirizzamento attendibile. Agli utenti di Azure AD viene quindi concesso l'accesso direttamente agli stessi endpoint dell'API Kubernetes tramite **ClusterRoles** e **ClusterRoleBindings**. 

Per altre informazioni sulla configurazione della sicurezza avanzata in Kubernetes, vedere la [documentazione di Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/). 

>[!NOTE]
>Se si sta creando un nuovo cluster abilitato per il controllo degli accessi in base al ruolo, vedere [integrare Azure Active Directory con il servizio Azure Kubernetes](../../aks/azure-ad-integration.md) e seguire i passaggi per configurare l'autenticazione Azure ad. Durante i passaggi per creare l'applicazione client, una nota in questa sezione evidenzia i due URL di reindirizzamento che è necessario creare per il monitoraggio di Azure per i contenitori. 

### <a name="client-registration-reconfiguration"></a>Riconfigurazione della registrazione client

1. Individuare la registrazione client per il cluster Kubernetes in Azure AD in **Azure Active Directory > registrazioni app** nella portale di Azure.

2. Selezionare **autenticazione** nel riquadro a sinistra. 

3. Aggiungere due URL di reindirizzamento a questo elenco come tipi di applicazione **Web** . Il primo valore dell'URL di base deve essere `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` e il secondo valore dell'URL di base deve essere `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.

    >[!NOTE]
    >Se si usa questa funzionalità in Azure Cina, il primo valore dell'URL di base deve essere `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` e il secondo valore dell'URL di base deve essere `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`. 
    
4. Dopo la registrazione degli URL di reindirizzamento, in **Impostazioni avanzate**selezionare le opzioni **token di accesso** e **token ID** , quindi salvare le modifiche.

>[!NOTE]
>La configurazione dell'autenticazione con Azure Active Directory per l'accesso Single Sign-on può essere eseguita solo durante la distribuzione iniziale di un nuovo cluster AKS. Non è possibile configurare l'accesso Single Sign-On per un cluster del servizio Azure Kubernetes già distribuito.
  
>[!IMPORTANT]
>Se è stato riconfigurato Azure AD per l'autenticazione utente usando l'URI aggiornato, deselezionare la cache del browser per verificare che il token di autenticazione aggiornato venga scaricato e applicato.

## <a name="grant-permission"></a>Concedere l'autorizzazione

Per accedere alla funzionalità dei dati in tempo reale (anteprima), è necessario concedere a ogni account Azure AD le autorizzazioni per le API appropriate in Kubernetes. I passaggi per concedere il Azure Active Directory account sono simili ai passaggi descritti nella sezione [autenticazione RBAC di Kubernetes](#configure-kubernetes-rbac-authentication) . Prima di applicare il modello di configurazione YAML al cluster, sostituire **clusterUser** in **ClusterRoleBinding** con l'utente desiderato. 

>[!IMPORTANT]
>Se l'utente che si concede l'associazione RBAC per si trova nello stesso tenant Azure AD, assegnare le autorizzazioni in base a userPrincipalName. Se l'utente si trova in un tenant Azure AD diverso, eseguire una query per e utilizzare la proprietà objectId.

Per ulteriori informazioni sulla configurazione del **ClusterRoleBinding**del cluster AKS, vedere [creare un'associazione RBAC](../../aks/azure-ad-integration-cli.md#create-rbac-binding).

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver configurato l'autenticazione, è possibile visualizzare le [metriche](container-insights-livedata-metrics.md), le [distribuzioni](container-insights-livedata-deployments.md)e [gli eventi e i log](container-insights-livedata-overview.md) in tempo reale dal cluster.