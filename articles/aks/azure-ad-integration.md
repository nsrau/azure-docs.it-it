---
title: Integrare Azure Active Directory con il servizio Azure Kubernetes
description: Come creare cluster di Azure Active Directory abilitati per Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 02/02/2019
ms.openlocfilehash: de57a46f92fab2486aa7722daf8745a01be1f4f6
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617588"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Integrare Azure Active Directory con il servizio Azure Kubernetes

Il servizio Azure Kubernetes (AKS) può essere configurato per usare Azure Active Directory (Azure AD) per l'autenticazione utente. In questa configurazione è possibile accedere a un cluster AKS usando il token di autenticazione di Azure AD.

Gli amministratori del cluster possono configurare il controllo degli accessi in base al ruolo (RBAC) di Kubernetes in base all'identità o all'appartenenza al gruppo di directory di un utente.

Questo articolo spiega come:

- Distribuire i prerequisiti per AKS e Azure AD.
- Distribuire un cluster abilitato per Azure AD.
- Creare un ruolo RBAC di base nel cluster AKS usando il portale di Azure.Create a basic RBAC role in the AKS cluster by using the Azure portal.

È anche possibile completare questi passaggi usando l'interfaccia della riga di comando di [Azure.][azure-ad-cli]

> [!NOTE]
> Azure AD può essere abilitato solo quando si crea un nuovo cluster abilitato al controllo degli accessi in base al ruolo. Non è possibile abilitare Azure AD in un cluster del servizio Azure Kubernetes.

## <a name="authentication-details"></a>Dettagli di autenticazione

Azure AD authentication is provided to AKS clusters that have OpenID Connect. OpenID Connect è un livello di gestione delle identità basato sul protocollo OAuth 2.0.

Per altre informazioni su OpenID Connect, vedere [Autorizzare l'accesso alle applicazioni Web usando OpenID Connect e Azure AD.][open-id-connect]

All'interno di un cluster Kubernetes, l'autenticazione token webhook viene utilizzata per i token di autenticazione. L'autenticazione del token del webhook viene configurata e gestita come parte del cluster servizio Azure Kubernetes.

Per ulteriori informazioni sull'autenticazione token webhook, vedere la sezione Autenticazione token Webhook nella documentazione di Kubernetes.For more information about [webhook token authentication,][kubernetes-webhook] see the Webhook token Authentication section in Kubernetes Documentation.

Per fornire l'autenticazione di Azure AD per un cluster AKS, vengono create due applicazioni di Azure AD. La prima applicazione è un componente server che fornisce l'autenticazione utente. La seconda applicazione è un componente client che viene utilizzato quando richiesto dall'interfaccia della riga di comando per l'autenticazione. Questa applicazione client utilizza l'applicazione server per l'autenticazione effettiva delle credenziali fornite dal client.

> [!NOTE]
> Quando si configura Azure AD per l'autenticazione AKS, vengono configurate due applicazioni di Azure AD. I passaggi per delegare le autorizzazioni per ogni applicazione devono essere completati da un amministratore tenant di Azure.The steps to delegate permissions for each application must be completed by an Azure tenant administrator.

## <a name="create-the-server-application"></a>Creare l'applicazione serverCreate the server application

La prima applicazione Azure AD viene applicata per ottenere l'appartenenza al gruppo di Azure AD di un utente. Per creare questa applicazione nel portale di Azure:To create this application in the Azure portal:

1. Selezionare**Registrazioni** > app **Azure Active Directory** > **Nuova registrazione**.

    a. Assegnare un nome all'applicazione, ad esempio *AKSAzureADServer*.

    b. Per **Tipi di account supportati**, selezionare Account solo in questa directory **dell'organizzazione**.
    
    c. Scegliere **Web** per il tipo URI di reindirizzamento e quindi *https://aksazureadserver*immettere qualsiasi valore in formato URI, ad esempio .

    d. Al termine, seleziona **Registra.**

2. Selezionare **Manifesto**, quindi modificare il **valore groupMembershipClaims:** come **All**. Al termine degli aggiornamenti, selezionare **Salva**.

    ![Impostare l'appartenenza al gruppo su All](media/aad-integration/edit-manifest.png)

3. Nel riquadro sinistro dell'applicazione Azure AD selezionare **Certificati & segreti**.

    a. Selezionare **: Nuovo segreto client**.

    b. Aggiungere una descrizione chiave, ad esempio *Un server Azure AD AKS*. Scegliere un'ora di scadenza e quindi selezionare **Aggiungi**.

    c. Si noti il valore della chiave, che viene visualizzato solo in questo momento. Quando si distribuisce un cluster AKS abilitato per Azure AD, questo valore è denominato segreto dell'applicazione server.

4. Nel riquadro sinistro dell'applicazione Azure AD, selezionare **Autorizzazioni API**, quindi selezionare **Aggiungi un'autorizzazione**.

    a. In **API Microsoft**selezionare Microsoft **Graph**.

    b. Selezionare **Autorizzazioni delegate**, quindi selezionare la casella di controllo accanto a **Directory > Directory.Read.All (Lettura dati directory)**.

    c. Se non esiste un'autorizzazione delegata predefinita per **User > User.Read (Accedi e leggi profilo utente),** selezionare la casella di controllo accanto ad essa.

    d. Selezionare **Autorizzazioni applicazione**, quindi selezionare la casella di controllo accanto a **Directory > Directory.Read.All (Lettura dati directory)**.

    ![Impostare le autorizzazioni per i grafici](media/aad-integration/graph-permissions.png)

    e. Selezionare **Aggiungi autorizzazioni** per salvare gli aggiornamenti.

    f. In **Consenso alla sovvenzione**selezionare **Concedere il consenso dell'amministratore**. Questo pulsante non sarà disponibile se l'account corrente in uso non è elencato come amministratore tenant.

    Quando le autorizzazioni vengono concesse correttamente, nel portale viene visualizzata la notifica seguente:When permissions are successfully granted, the following notification is displayed in the portal:

   ![Notifica di esito positivo delle autorizzazioni concesse](media/aad-integration/permissions-granted.png)

5. Nel riquadro sinistro dell'applicazione Azure AD, selezionare **Esporre un'API**, quindi selezionare **Aggiungi un ambito**.
    
    a. Immettere un **nome di ambito**, un nome visualizzato del consenso **dell'amministratore**e quindi una descrizione del **consenso dell'amministratore,** ad esempio *AKSAzureADServer*.

    b. Assicurarsi che **Stato** sia impostato su **Abilitato**.

    ![Esporre l'app server come un'API da usare con altri servizi](media/aad-integration/expose-api.png)

    c. Selezionare **Aggiungi ambito**.

6. Tornare alla pagina **Panoramica** dell'applicazione e prendere nota **dell'ID applicazione (client).** Quando si distribuisce un cluster AKS abilitato per Azure AD, questo valore è denominato ID applicazione server.

    ![Ottenere l'ID applicazione](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>Creare l'applicazione client

La seconda applicazione Azure AD viene usata quando si accede con la CLI Kubernetes (kubectl).

1. Selezionare**Registrazioni** > app **Azure Active Directory** > **Nuova registrazione**.

    a. Assegnare un nome all'applicazione, ad esempio *AKSAzureADClient*.

    b. Per **Tipi di account supportati**, selezionare Account solo in questa directory **dell'organizzazione**.

    c. Selezionare **Web** per il tipo DI URI di reindirizzamento *https://aksazureadclient*e quindi immettere qualsiasi valore in formato URI, ad esempio .

    >[!NOTE]
    >Se si crea un nuovo cluster abilitato al controllo degli accessi in base al ruolo per supportare Monitoraggio di Azure per i contenitori, aggiungere i due URL di reindirizzamento aggiuntivi seguenti a questo elenco come tipi di applicazione **Web.If** you are creating a new RBAC-enabled cluster to support Azure Monitor for containers, add the following two additional redirect URLs to this list as Web application types. Il primo valore dell'URL di base deve essere `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` e il secondo valore dell'URL di base deve essere`https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`
    >
    >Se si usa questa funzionalità in Azure Cina, il `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` primo valore dell'URL di base deve essere e il secondo valore dell'URL di base deve essere`https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`
    >
    >Per altre informazioni, vedere [Come configurare la funzionalità Live Data (anteprima)](../azure-monitor/insights/container-insights-livedata-setup.md) per Monitoraggio di Azure per i contenitori e la procedura per configurare l'autenticazione nella sezione [Configurare l'autenticazione integrata](../azure-monitor/insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication) di Active Directory.For further information, see How to setup the Live Data (preview) feature for Azure Monitor for containers, and the steps for configuring authentication under the Configure AD integrated authentication section.

    d. Al termine, seleziona **Registra.**

2. Nel riquadro sinistro dell'applicazione Azure AD, selezionare **Autorizzazioni API**, quindi selezionare **Aggiungi un'autorizzazione**.

    a. Selezionare **API personali**e quindi scegliere l'applicazione server di Azure AD creata nel passaggio precedente, ad esempio *AKSAzureADServer*.

    b. Selezionare **Autorizzazioni delegate**e quindi selezionare la casella di controllo accanto all'app server di Azure AD.

    ![Configurare le autorizzazioni per l'applicazione](media/aad-integration/select-api.png)

    c. Selezionare **Aggiungi autorizzazioni**.

    d. In **Consenso alla sovvenzione**selezionare **Concedere il consenso dell'amministratore**. Questo pulsante non è disponibile se l'account corrente non è un amministratore tenant. Quando le autorizzazioni vengono concesse, nel portale viene visualizzata la notifica seguente:

    ![Notifica di esito positivo delle autorizzazioni concesse](media/aad-integration/permissions-granted.png)

3. Nel riquadro sinistro dell'applicazione Azure AD selezionare **Autenticazione**.

    - In **Tipo di client predefinito**selezionare **Sì** per Considerare il client come **client pubblico.**

5. Nel riquadro sinistro dell'applicazione Azure AD prendere nota dell'ID applicazione. Quando si distribuisce un cluster AKS abilitato per Azure AD, questo valore viene chiamato ID applicazione client.

   ![Ottenere l'ID dell'applicazione](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>Ottenere l'ID tenantGet the tenant ID

Ottenere quindi l'ID del tenant di Azure.Next, get the ID of your Azure tenant. Questo valore viene utilizzato quando si crea il cluster AKS.

Nel portale di Azure selezionare**Proprietà** di **Azure Active Directory** > e prendere nota dell'ID **directory.** Quando si crea un cluster AKS abilitato per Azure AD, questo valore viene chiamato ID tenant.

![Ottenere l'ID del tenant di Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>Distribuire il cluster AKSDeploy the AKS cluster

Usare il comando [az group create][az-group-create] per creare un gruppo di risorse per il cluster servizio Azure Kubernetes.

```azurecli
az group create --name myResourceGroup --location eastus
```

Utilizzare il comando [az aks create][az-aks-create] per distribuire il cluster AKS. Sostituire quindi i valori nel comando di esempio seguente. Usare i valori raccolti quando sono state create le applicazioni di Azure AD per l'ID app server, il segreto dell'app, l'ID app client e l'ID tenant.

```azurecli
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --generate-ssh-keys \
  --aad-server-app-id b1536b67-29ab-4b63-b60f-9444d0c15df1 \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 8aaf8bd5-1bdd-4822-99ad-02bfaa63eea7 \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

La creazione di un cluster AKS richiede alcuni minuti.

## <a name="create-an-rbac-binding"></a>Creare un'associazione RBACCreate an RBAC binding

> [!NOTE]
> Per il nome dell'associazione del ruolo del cluster viene fatta distinzione tra maiuscole e minuscole.

Prima di usare un account Azure Active Directory con un cluster AKS, è necessario creare l'associazione di ruolo o l'associazione di ruoli del cluster. I ruoli definiscono le autorizzazioni da concedere e le associazioni le applicano agli utenti desiderati. Queste assegnazioni possono essere applicate a uno spazio dei nomi specifico o all'intero cluster. Per altre informazioni, vedere [Uso di autorizzazioni del controllo degli accessi in base al ruolo][rbac-authorization].

In primo luogo, utilizzare il comando az `--admin` [aks get-credentials][az-aks-get-credentials] con l'argomento per accedere al cluster con accesso di amministratore.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Successivamente, creare ClusterRoleBinding per un account azure AD a cui si vuole concedere l'accesso al cluster AKS. L'esempio seguente fornisce all'account l'accesso completo a tutti gli spazi dei nomi del cluster:The following example gives the account full access to all namespaces in the cluster:

- Se l'utente per cui si concede l'associazione RBAC si trova nello stesso tenant di Azure AD, assegnare le autorizzazioni in base al nome dell'entità utente (UPN). Passare al passaggio per creare il manifesto YAML per ClusterRoleBinding.Move on the step to create the YAML manifest for ClusterRoleBinding.

- Se l'utente si trova in un tenant di Azure AD diverso, eseguire una query per e usare invece la proprietà **objectId.** Se necessario, ottenere l'id oggetto dell'account utente richiesto utilizzando il comando [az ad user show.][az-ad-user-show] Specificare il nome dell'entità utente (UPN) dell'account richiesto:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Creare un file, ad esempio *rbac-aad-user.yaml*, quindi incollare il contenuto seguente. Nell'ultima riga sostituire **userPrincipalName_or_objectId** con l'UPN o l'ID oggetto. La scelta dipende dal fatto che l'utente sia o meno lo stesso tenant di Azure AD.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

Applicare l'associazione utilizzando il comando [kubectl apply][kubectl-apply] come illustrato nell'esempio seguente:

```console
kubectl apply -f rbac-aad-user.yaml
```

È anche possibile creare un'associazione di ruolo per tutti i membri di un gruppo di Azure AD. I gruppi di Azure AD vengono specificati usando l'ID oggetto gruppo, come illustrato nell'esempio seguente.

Creare un file, ad esempio *rbac-aad-group.yaml*, quindi incollare il contenuto seguente. Aggiornare l'ID oggetto del gruppo con un ID dal tenant di Azure AD:

 ```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
   kind: Group
   name: "894656e1-39f8-4bfe-b16a-510f61af6f41"
```

Applicare l'associazione utilizzando il comando [kubectl apply][kubectl-apply] come illustrato nell'esempio seguente:

```console
kubectl apply -f rbac-aad-group.yaml
```

Per altre informazioni sulla protezione di un cluster Kubernetes con RBAC, vedere [Using RBAC Authorization][rbac-authorization] (Uso delle autorizzazioni RBAC).

## <a name="access-the-cluster-with-azure-ad"></a>Accedere al cluster con Azure ADAccess the cluster with Azure AD

Estrarre il contesto per l'utente non amministratore utilizzando il comando [az aks get-credentials.][az-aks-get-credentials]

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Dopo aver `kubectl` eseguito il comando, verrà richiesto di eseguire l'autenticazione tramite Azure.After you run the command, you'll be prompted to authenticate by using Azure. Seguire le istruzioni visualizzate per completare il processo, come illustrato nell'esempio seguente:

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

Al termine del processo, il token di autenticazione viene memorizzato nella cache. Al termine del token viene richiesto di eseguire nuovamente l'accesso o quando il file di configurazione Kubernetes viene ricreato.

Se viene visualizzato un messaggio di errore di autorizzazione dopo aver eseguito correttamente l'accesso, verificare i seguenti criteri:

```console
error: You must be logged in to the server (Unauthorized)
```


- L'ID oggetto o l'UPN appropriati sono stati definiti, a seconda che l'account utente si trovi o meno nello stesso tenant di Azure AD.
- L'utente non è un membro di più di 200 gruppi.
- Il segreto definito nella registrazione dell'applicazione `--aad-server-app-secret`per il server corrisponde al valore configurato utilizzando .

## <a name="next-steps"></a>Passaggi successivi

Per usare gli utenti e i gruppi di Azure AD per controllare l'accesso alle risorse del cluster, vedere [Controllare l'accesso alle risorse del cluster usando][azure-ad-rbac]il controllo degli accessi in base al ruolo e le identità di Azure AD in AKS.

Per ulteriori informazioni su come proteggere i cluster Kubernetes, vedere Opzioni di [accesso e identità per AKS.][rbac-authorization]

Per ulteriori informazioni sul controllo delle identità e delle risorse, vedere [Procedure consigliate per l'autenticazione e l'autorizzazione in AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
