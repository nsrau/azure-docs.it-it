---
title: Configurare Monitor di Azure per i contenitori Live Data (anteprima)Set up Azure Monitor for containers Live Data (preview) Documenti Microsoft
description: Questo articolo descrive come configurare la visualizzazione in tempo reale dei log dei contenitori (stdout/stderr) e degli eventi senza usare kubectl con Monitoraggio di Azure per i contenitori.
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: f19071ca642cd229cbd7d49b4eab90c970672eee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275373"
---
# <a name="how-to-set-up-the-live-data-preview-feature"></a>Come configurare la funzione Live Data (anteprima)

Per visualizzare Live Data (anteprima) con Monitoraggio di Azure per i contenitori dei cluster del servizio Kubernetes di Azure, è necessario configurare l'autenticazione per concedere l'autorizzazione per l'accesso ai dati Kubernetes. Questa configurazione di sicurezza consente l'accesso in tempo reale ai dati tramite l'API Kubernetes direttamente nel portale di Azure.This security configuration allows real-time access to your data through the Kubernetes API directly in the Azure portal.

Questa funzionalità supporta i metodi seguenti per controllare l'accesso ai log, agli eventi e alle metriche:This feature supports the following methods to control access to the logs, events, and metrics:

- Servizio Azure Kubernetes senza autorizzazione del controllo degli accessi in base al ruolo di Kubernetes abilitata
- Servizio Azure Kubernetes con autorizzazione del controllo degli accessi in base al ruolo di Kubernetes abilitata
    - AKS configurato con il cluster di binding del ruolo del ** [clusterMonitoringUser](https://docs.microsoft.com/rest/api/aks/managedclusters/listclustermonitoringusercredentials?view=azurermps-5.2.0)**
- AKS abilitato con l'accesso Single Sign-On basato su SAML di Azure Active Directory (AD)

Queste istruzioni richiedono sia l'accesso amministrativo al cluster Kubernetes che, se si configura l'utilizzo di Azure Active Directory (AD) per l'autenticazione utente, l'accesso amministrativo ad Azure AD.  

In questo articolo viene illustrato come configurare l'autenticazione per controllare l'accesso alla funzionalità Live Data (anteprima) dal cluster:This article explains how to configure authentication to control access to the Live Data (preview) feature from the cluster:

- Cluster AKS abilitato al controllo degli accessi in base al ruolo
- Cluster AKS integrato in Azure Active Directory.Azure Active Directory integrated AKS cluster. 

>[!NOTE]
>I cluster AKS abilitati come [cluster privati](https://azure.microsoft.com/updates/aks-private-cluster/) non sono supportati con questa funzionalità. Questa funzione si basa sull'accesso diretto all'API Kubernetes tramite un server proxy dal browser. L'abilitazione della sicurezza di rete per bloccare l'API Kubernetes da questo proxy bloccherà il traffico. 

>[!NOTE]
>Questa funzionalità è disponibile in tutte le aree di Azure, inclusa la Cina di Azure.This feature is available in all Azure regions, including Azure China. Attualmente non è disponibile in Azure US Government.It is currently not available in Azure US Government.

## <a name="authentication-model"></a>Modello di autenticazione

Le funzionalità di Live Data (anteprima) utilizzano l'API `kubectl` Kubernetes, identica allo strumento da riga di comando. Gli endpoint API Kubernetes utilizzano un certificato autofirmato, che il browser non sarà in grado di convalidare. Questa funzionalità utilizza un proxy interno per convalidare il certificato con il servizio AKS, assicurando che il traffico sia attendibile.

Il portale di Azure richiede di convalidare le credenziali di accesso per un cluster di Azure Active Directory e di reindirizzare l'utente alla configurazione della registrazione client durante la creazione del cluster (e riconfigurato in questo articolo). Questo comportamento è simile al `kubectl`processo di autenticazione richiesto da . 

>[!NOTE]
>L'autorizzazione al cluster è gestita da Kubernetes e il modello di sicurezza con cui è configurato. Gli utenti che accedono a questa funzionalità richiedono l'autorizzazione per scaricare `az aks get-credentials -n {your cluster name} -g {your resource group}`la configurazione Kubernetes (*kubeconfig*), simile all'esecuzione di . Questo file di configurazione contiene il token di autorizzazione e autenticazione per il ruolo utente del cluster di servizi **di Azure Kubernetes**, nel caso di cluster AKS e abilitati per il controllo degli accessi in base al ruolo di Azure. Contiene informazioni su Azure AD e dettagli di registrazione client quando AKS è abilitato con il Single Sign-On basato su SAML di Azure Active Directory (AD).

>[!IMPORTANT]
>Gli utenti di queste funzionalità richiedono il [ruolo utente del cluster di Azure Kubernetes](../../azure/role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role permissions) per il cluster per scaricare la `kubeconfig` funzionalità e . Gli utenti **non** richiedono l'accesso dei collaboratori al cluster per utilizzare questa funzionalità. 

## <a name="using-clustermonitoringuser-with-rbac-enabled-clusters"></a>Utilizzo di clusterMonitoringUser con cluster abilitati al controllo degli accessi in base al ruoloUsing clusterMonitoringUser with RBAC-enabled clusters

Per eliminare la necessità di applicare ulteriori modifiche alla configurazione per consentire ai ruoli utente Kubernetes di associare il **clusterAccesso** utente alla funzionalità Live Data (anteprima) dopo [l'abilitazione](#configure-kubernetes-rbac-authorization) dell'autorizzazione RBAC, AKS ha aggiunto una nuova associazione del ruolo del cluster Kubernetes denominata **clusterMonitoringUser**. Questo binding del ruolo del cluster dispone di tutte le autorizzazioni necessarie per accedere all'API Kubernetes e agli endpoint per l'utilizzo della funzionalità Live Data (anteprima).

Per utilizzare la funzionalità Live Data (anteprima) con questo nuovo utente, è necessario essere membri del ruolo [Collaboratore](../../role-based-access-control/built-in-roles.md#contributor) nella risorsa cluster AKS. Monitoraggio di Azure per i contenitori, se abilitato, è configurato per l'autenticazione tramite questo utente per impostazione predefinita. Se l'associazione del ruolo clusterMonitoringUser non esiste in un cluster, **clusterUser** viene utilizzato per l'autenticazione.

AKS ha rilasciato questa nuova associazione di ruolo nel gennaio 2020, pertanto i cluster creati prima di gennaio 2020 non lo hanno. Se si dispone di un cluster creato prima di gennaio 2020, il nuovo **clusterMonitoringUser** può essere aggiunto a un cluster esistente eseguendo un'operazione PUT nel cluster o eseguendo qualsiasi altra operazione nel cluster tha esegue un'operazione PUT nel cluster, ad esempio l'aggiornamento della versione del cluster.

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Cluster Kubernetes con controllo degli accessi in base al ruolo abilitato

Se si ha un cluster Kubernetes non configurato con l'autorizzazione del controllo degli accessi in base al ruolo di Kubernetes oppure integrato con il Single Sign-On di Azure AD, non è necessario eseguire la procedura seguente. Ciò è dovuto al fatto che si dispone di autorizzazioni amministrative per impostazione predefinita in una configurazione non basata su controllo degli accessi in base al ruolo.

## <a name="configure-kubernetes-rbac-authorization"></a>Configurare l'autorizzazione RBAC Kubernetes

Quando si abilita l'autorizzazione RBAC Kubernetes, vengono utilizzati due utenti: **clusterUser** e **clusterAdmin** per accedere all'API Kubernetes. È simile all'esecuzione `az aks get-credentials -n {cluster_name} -g {rg_name}` senza l'opzione amministrativa. Ciò significa che il **clusterUser** deve essere concesso l'accesso agli endpoint nell'API Kubernetes.

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

2. Per aggiornare la configurazione, `kubectl apply -f LogReaderRBAC.yaml`eseguire il comando seguente: .

>[!NOTE] 
> Se è stata applicata `LogReaderRBAC.yaml` una versione precedente del file al cluster, aggiornarlo copiando e incollando il nuovo codice illustrato nel passaggio 1 precedente, quindi eseguire il comando illustrato nel passaggio 2 per applicarlo al cluster.

## <a name="configure-ad-integrated-authentication"></a>Configurare l'autenticazione integrata di Active Directory 

Un cluster AKS configurato per usare Azure Active Directory (AD) per l'autenticazione utente utilizza le credenziali di accesso della persona che accede a questa funzionalità. In questa configurazione è possibile accedere a un cluster AKS usando il token di autenticazione di Azure AD.

La registrazione del client Azure AD deve essere riconfigurata per consentire al portale di Azure di reindirizzare le pagine di autorizzazione come URL di reindirizzamento attendibile. Agli utenti di Azure AD viene quindi concesso l'accesso direttamente agli stessi endpoint API Kubernetes tramite **ClusterRoles** e **ClusterRoleBindings**. 

Per ulteriori informazioni sulla configurazione avanzata della sicurezza in Kubernetes, consultare la documentazione di [Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/). 

>[!NOTE]
>Se si sta creando un nuovo cluster abilitato al controllo degli accessi in base al ruolo, vedere Integrare Azure Active Directory con il [servizio Azure Kubernetes](../../aks/azure-ad-integration.md) e seguire i passaggi per configurare l'autenticazione di Azure AD. Durante i passaggi per creare l'applicazione client, una nota in tale sezione evidenzia i due URL di reindirizzamento che è necessario creare per Monitoraggio di Azure per i contenitori corrispondenti a quelli specificati nel passaggio 3 di seguito.

### <a name="client-registration-reconfiguration"></a>Riconfigurazione della registrazione client

1. Individuare la registrazione client per il cluster Kubernetes in Azure AD in **Azure Active Directory > le registrazioni dell'app** nel portale di Azure.Locate the client registration for your Kubernetes cluster in Azure AD under Azure Active Directory > App registrations in the Azure portal.

2. Selezionare **Autenticazione** nel riquadro sinistro. 

3. Aggiungere due URL di reindirizzamento a questo elenco come tipi di applicazione **Web.** Il primo valore dell'URL di base deve essere `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` e il secondo valore dell'URL di base deve essere `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.

    >[!NOTE]
    >Se si usa questa funzionalità in Azure Cina, il `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` primo valore dell'URL `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`di base deve essere e il secondo valore dell'URL di base deve essere . 
    
4. Dopo aver registrato gli URL di reindirizzamento, in **Concessione implicita**selezionare le opzioni Token di **accesso** e **token ID** e quindi salvare le modifiche.

>[!NOTE]
>La configurazione dell'autenticazione con Azure Active Directory per l'accesso Single Sign-On può essere eseguita solo durante la distribuzione iniziale di un nuovo cluster AKS. Non è possibile configurare l'accesso Single Sign-On per un cluster del servizio Azure Kubernetes già distribuito.
  
>[!IMPORTANT]
>Se Azure AD è stato riconfigurato per l'autenticazione utente usando l'URI aggiornato, cancellare la cache del browser per assicurarsi che il token di autenticazione aggiornato venga scaricato e applicato.

## <a name="grant-permission"></a>Concedere un'autorizzazione

A ogni account Azure AD deve essere concessa l'autorizzazione per le API appropriate in Kubernetes per poter accedere alla funzionalità Live Data (anteprima). I passaggi per concedere l'account Azure Active Directory sono simili a quelli descritti nella sezione [Autenticazione RBAC Kubernetes.The](#configure-kubernetes-rbac-authorization) steps to grant the Azure Active Directory account are similar to the steps described in the Kubernetes RBAC authentication section. Prima di applicare il modello di configurazione yaml al cluster, sostituire **clusterUser** in **ClusterRoleBinding** con l'utente desiderato. 

>[!IMPORTANT]
>Se l'utente per cui si concede l'associazione RBAC si trova nello stesso tenant di Azure AD, assegnare le autorizzazioni in base a userPrincipalName.If the user you grant the RBAC binding for is in the same Azure AD tenant, assign permissions based on the userPrincipalName. Se l'utente si trova in un tenant di Azure AD diverso, eseguire una query per e usare la proprietà objectId.

Per ulteriori informazioni sulla configurazione del cluster AKS **ClusterRoleBinding**, vedere [Create RBAC binding](../../aks/azure-ad-integration-cli.md#create-rbac-binding).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato l'autenticazione, è possibile visualizzare [le metriche,](container-insights-livedata-metrics.md) [le distribuzioni, gli](container-insights-livedata-deployments.md)eventi e i [log](container-insights-livedata-overview.md) in tempo reale dal cluster.
