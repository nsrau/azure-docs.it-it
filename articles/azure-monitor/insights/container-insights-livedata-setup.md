---
title: Configurare monitoraggio di Azure per i dati dinamici dei contenitori (anteprima) | Microsoft Docs
description: Questo articolo descrive come configurare la visualizzazione in tempo reale dei log del contenitore (stdout/stderr) ed eventi senza usare kubectl con monitoraggio di Azure per i contenitori.
ms.topic: conceptual
ms.date: 02/14/2019
ms.custom: references_regions
ms.openlocfilehash: 6fdd2d0a97357a2126ff37c0840b1f7da2859da5
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682673"
---
# <a name="how-to-set-up-the-live-data-preview-feature"></a>Come configurare la funzionalità dati in tempo reale (anteprima)

Per visualizzare i dati in tempo reale (anteprima) con monitoraggio di Azure per i contenitori dai cluster di Azure Kubernetes Service (AKS), è necessario configurare l'autenticazione per concedere l'autorizzazione per l'accesso ai dati di Kubernetes. Questa configurazione di sicurezza consente l'accesso in tempo reale ai dati tramite l'API Kubernetes direttamente nel portale di Azure.

Questa funzionalità supporta i metodi seguenti per controllare l'accesso ai log, agli eventi e alle metriche:

- Servizio Azure Kubernetes senza autorizzazione del controllo degli accessi in base al ruolo di Kubernetes abilitata
- Servizio Azure Kubernetes con autorizzazione del controllo degli accessi in base al ruolo di Kubernetes abilitata
    - AKS configurato con l'associazione di ruoli del cluster **[clusterMonitoringUser](/rest/api/aks/managedclusters/listclustermonitoringusercredentials?view=azurermps-5.2.0&preserve-view=true)**
- AKS abilitato con Single Sign-on basato su SAML di Azure Active Directory (AD)

Queste istruzioni richiedono l'accesso amministrativo al cluster Kubernetes e, se si configura per l'uso di Azure Active Directory (AD) per l'autenticazione degli utenti, l'accesso amministrativo ai Azure AD.

Questo articolo illustra come configurare l'autenticazione per controllare l'accesso alla funzionalità dati in tempo reale (anteprima) dal cluster:

- Cluster AKS abilitato per il controllo degli accessi in base al ruolo
- Azure Active Directory cluster AKS integrato.

>[!NOTE]
>I cluster AKS abilitati come [cluster privati](https://azure.microsoft.com/updates/aks-private-cluster/) non sono supportati con questa funzionalità. Questa funzionalità si basa sull'accesso diretto all'API Kubernetes tramite un server proxy dal browser. L'abilitazione della sicurezza di rete per bloccare l'API Kubernetes da questo proxy bloccherà il traffico.

## <a name="authentication-model"></a>Modello di autenticazione

Le funzionalità dei dati in tempo reale (anteprima) usano l'API Kubernetes, identica allo `kubectl` strumento da riga di comando. Gli endpoint dell'API Kubernetes utilizzano un certificato autofirmato che non sarà possibile convalidare nel browser. Questa funzionalità Usa un proxy interno per convalidare il certificato con il servizio AKS, assicurando che il traffico sia attendibile.

Il portale di Azure richiede di convalidare le credenziali di accesso per un cluster Azure Active Directory e di reindirizzare l'utente alla configurazione della registrazione del client durante la creazione del cluster e riconfigurata in questo articolo. Questo comportamento è simile al processo di autenticazione richiesto da `kubectl` .

>[!NOTE]
>L'autorizzazione per il cluster viene gestita da Kubernetes e dal modello di sicurezza con cui è configurato. Per gli utenti che accedono a questa funzionalità è necessaria l'autorizzazione per scaricare la configurazione Kubernetes (*kubeconfig*), simile a in esecuzione `az aks get-credentials -n {your cluster name} -g {your resource group}` . Questo file di configurazione contiene il token di autorizzazione e di autenticazione per il **ruolo utente del cluster di servizi Kubernetes di Azure**, nel caso di cluster Azure e abilitati per il controllo degli accessi in base al ruolo, senza autorizzazione RBAC abilitata Contiene informazioni sui Azure AD e i dettagli di registrazione del client quando AKS è abilitato con l'accesso Single Sign-on basato su SAML di Azure Active Directory (AD).

>[!IMPORTANT]
>Per scaricare e usare questa funzionalità, gli utenti di questa funzionalità richiedono il [ruolo utente del cluster Kubernetes di Azure](../../role-based-access-control/built-in-roles.md) per il cluster `kubeconfig` . Per utilizzare questa funzionalità, gli utenti **non** necessitano dell'accesso come collaboratore al cluster.

## <a name="using-clustermonitoringuser-with-rbac-enabled-clusters"></a>Uso di clusterMonitoringUser con cluster abilitati per RBAC

Per eliminare la necessità di applicare modifiche di configurazione aggiuntive per consentire l'associazione del ruolo utente Kubernetes **clusterUser** l'accesso alla funzionalità dati in tempo reale (anteprima) dopo aver abilitato l'autorizzazione [RBAC](#configure-kubernetes-rbac-authorization) , AKS ha aggiunto una nuova associazione di ruoli del cluster Kubernetes denominata **clusterMonitoringUser**. Questa associazione di ruoli del cluster dispone di tutte le autorizzazioni necessarie predefinite per accedere all'API Kubernetes e agli endpoint per l'utilizzo della funzionalità dati in tempo reale (anteprima).

Per usare la funzionalità dati in tempo reale (anteprima) con questo nuovo utente, è necessario essere un membro del ruolo [collaboratore](../../role-based-access-control/built-in-roles.md#contributor) sulla risorsa cluster AKS. Il monitoraggio di Azure per i contenitori, se abilitato, è configurato per l'autenticazione con questo utente per impostazione predefinita. Se l'associazione di ruolo clusterMonitoringUser non esiste in un cluster, per l'autenticazione viene usato **clusterUser** .

AKS ha rilasciato questa nuova associazione di ruolo nel 2020 gennaio, quindi i cluster creati prima di gennaio 2020 non lo hanno. Se si dispone di un cluster creato prima del 2020 gennaio, è possibile aggiungere il nuovo **clusterMonitoringUser** a un cluster esistente eseguendo un'operazione Put nel cluster o eseguendo qualsiasi altra operazione nel cluster che esegue un'operazione Put nel cluster, ad esempio l'aggiornamento della versione del cluster.

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Cluster Kubernetes con controllo degli accessi in base al ruolo abilitato

Se si ha un cluster Kubernetes non configurato con l'autorizzazione del controllo degli accessi in base al ruolo di Kubernetes oppure integrato con il Single Sign-On di Azure AD, non è necessario eseguire la procedura seguente. Questo perché per impostazione predefinita sono disponibili autorizzazioni amministrative in una configurazione non RBAC.

## <a name="configure-kubernetes-rbac-authorization"></a>Configurare l'autorizzazione RBAC Kubernetes

Quando si Abilita l'autorizzazione RBAC di Kubernetes, vengono usati due utenti: **clusterUser** e **clusterAdmin** per accedere all'API Kubernetes. Questa operazione è simile all'esecuzione `az aks get-credentials -n {cluster_name} -g {rg_name}` senza l'opzione amministrativa. Ciò significa che è necessario concedere a **clusterUser** l'accesso agli endpoint nell'API Kubernetes.

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

2. Per aggiornare la configurazione, eseguire il comando seguente: `kubectl apply -f LogReaderRBAC.yaml` .

>[!NOTE]
> Se è stata applicata una versione precedente del `LogReaderRBAC.yaml` file al cluster, aggiornarla copiando e incollando il nuovo codice illustrato nel passaggio 1 precedente, quindi eseguire il comando illustrato nel passaggio 2 per applicarlo al cluster.

## <a name="configure-ad-integrated-authentication"></a>Configurare l'autenticazione integrata di Active Directory

Un cluster AKS configurato per usare Azure Active Directory (AD) per l'autenticazione utente usa le credenziali di accesso della persona che accede a questa funzionalità. In questa configurazione è possibile accedere a un cluster AKS usando il Azure AD token di autenticazione.

Azure AD la registrazione client deve essere riconfigurata per consentire al portale di Azure di reindirizzare le pagine di autorizzazione come URL di reindirizzamento attendibile. Agli utenti di Azure AD viene quindi concesso l'accesso direttamente agli stessi endpoint dell'API Kubernetes tramite **ClusterRoles** e **ClusterRoleBindings**.

Per altre informazioni sulla configurazione della sicurezza avanzata in Kubernetes, vedere la [documentazione di Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/).

>[!NOTE]
>Se si sta creando un nuovo cluster abilitato per il controllo degli accessi in base al ruolo, vedere [integrare Azure Active Directory con il servizio Azure Kubernetes](../../aks/azure-ad-integration-cli.md) e seguire i passaggi per configurare l'autenticazione Azure ad. Durante i passaggi per creare l'applicazione client, una nota nella sezione evidenzia i due URL di reindirizzamento che è necessario creare per il monitoraggio di Azure per i contenitori corrispondenti a quelli specificati nel passaggio 3 riportato di seguito.

### <a name="client-registration-reconfiguration"></a>Riconfigurazione della registrazione client

1. Individuare la registrazione client per il cluster Kubernetes in Azure AD in **Azure Active Directory > registrazioni app** nella portale di Azure.

2. Selezionare **autenticazione** nel riquadro a sinistra.

3. Aggiungere due URL di reindirizzamento a questo elenco come tipi di applicazione **Web** . Il primo valore dell'URL di base deve essere `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` e il secondo valore dell'URL di base deve essere `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` .

    >[!NOTE]
    >Se si usa questa funzionalità in Azure Cina, il primo valore dell'URL di base deve essere `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` e il secondo valore dell'URL di base deve essere `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` .

4. Dopo la registrazione degli URL di reindirizzamento, in **concessione implicita** selezionare le opzioni **token di accesso** e **token ID** , quindi salvare le modifiche.

>[!NOTE]
>La configurazione dell'autenticazione con Azure Active Directory per l'accesso Single Sign-on può essere eseguita solo durante la distribuzione iniziale di un nuovo cluster AKS. Non è possibile configurare l'accesso Single Sign-On per un cluster del servizio Azure Kubernetes già distribuito.

>[!IMPORTANT]
>Se è stato riconfigurato Azure AD per l'autenticazione utente usando l'URI aggiornato, deselezionare la cache del browser per verificare che il token di autenticazione aggiornato venga scaricato e applicato.

## <a name="grant-permission"></a>Concedere un'autorizzazione

Per accedere alla funzionalità dei dati in tempo reale (anteprima), è necessario concedere a ogni account Azure AD le autorizzazioni per le API appropriate in Kubernetes. I passaggi per concedere il Azure Active Directory account sono simili ai passaggi descritti nella sezione [autenticazione RBAC di Kubernetes](#configure-kubernetes-rbac-authorization) . Prima di applicare il modello di configurazione YAML al cluster, sostituire **clusterUser** in **ClusterRoleBinding** con l'utente desiderato.

>[!IMPORTANT]
>Se l'utente che si concede l'associazione RBAC per si trova nello stesso tenant Azure AD, assegnare le autorizzazioni in base a userPrincipalName. Se l'utente si trova in un tenant Azure AD diverso, eseguire una query per e utilizzare la proprietà objectId.

Per ulteriori informazioni sulla configurazione del **ClusterRoleBinding** del cluster AKS, vedere [create Kubernetes RBAC binding](../../aks/azure-ad-integration-cli.md#create-kubernetes-rbac-binding).

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver configurato l'autenticazione, è possibile visualizzare le [metriche](container-insights-livedata-metrics.md), le [distribuzioni](container-insights-livedata-deployments.md)e [gli eventi e i log](container-insights-livedata-overview.md) in tempo reale dal cluster.
