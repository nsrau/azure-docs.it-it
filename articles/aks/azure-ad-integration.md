---
title: Integrare Azure Active Directory con il servizio Azure Kubernetes
description: Come creare cluster abilitato Azure Active Directory di Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/26/2019
ms.author: mlearned
ms.openlocfilehash: 80137023643630e8472a70fcca6cb656aeba7123
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616381"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Integrare Azure Active Directory con il servizio Azure Kubernetes

Azure Kubernetes Service (AKS) può essere configurato per usare Azure Active Directory (Azure AD) per l'autenticazione utente. In questa configurazione, è possibile accedere a un cluster del servizio contenitore di AZURE usando il token di autenticazione di Azure AD.

Gli amministratori di cluster possono configurare Kubernetes accesso basato sui ruoli controllo degli accessi in base all'appartenenza identità o una directory dell'utente.

Questo articolo spiega come:

- Distribuire i prerequisiti per servizio contenitore di AZURE e Azure AD.
- Distribuire un cluster abilitato per AD Azure.
- Creare un ruolo RBAC di base del cluster servizio contenitore di AZURE usando il portale di Azure.

È anche possibile completare questi passaggi tramite il [CLI Azure][azure-ad-cli].

> [!NOTE]
> Azure AD può essere abilitata solo quando si crea un nuovo cluster abilitato RBAC. Non è possibile abilitare Azure AD in un cluster del servizio Azure Kubernetes.

## <a name="authentication-details"></a>Dettagli di autenticazione

Autenticazione di Azure AD è disponibile per i cluster AKS con OpenID Connect. OpenID Connect è un livello di gestione delle identità basato sul protocollo OAuth 2.0.

Per altre informazioni su OpenID Connect, vedere [autorizzare l'accesso ad applicazioni web con OpenID Connect e Azure AD][open-id-connect].

All'interno di un cluster Kubernetes, viene utilizzata l'autenticazione tramite token webhook per i token di autenticazione. L'autenticazione del token del webhook viene configurata e gestita come parte del cluster servizio Azure Kubernetes.

Per altre informazioni sull'autenticazione basata su token webhook, vedere la [autenticazione basata su Token Webhook][kubernetes-webhook] sezione nella documentazione di Kubernetes.

Per fornire l'autenticazione di Azure AD per un cluster AKS, vengono create due applicazioni Azure AD. La prima applicazione è un componente server che fornisce l'autenticazione dell'utente. La seconda applicazione è un componente client che viene usato quando richiesto per l'interfaccia della riga di comando per l'autenticazione. Questa applicazione client usa l'applicazione server per l'autenticazione effettivo delle credenziali forniti dal client.

> [!NOTE]
> Quando si configura Azure AD per l'autenticazione del servizio contenitore di AZURE, vengono configurate due applicazioni Azure AD. I passaggi necessari per delegare le autorizzazioni per ogni applicazione devono essere completati da un amministratore di tenant di Azure.

## <a name="create-the-server-application"></a>Creare l'applicazione server

La prima applicazione di Azure AD viene applicata per ottenere l'appartenenza al gruppo di Azure AD dell'utente. Per creare l'applicazione nel portale di Azure:

1. Selezionare **Azure Active Directory** > **registrazioni per l'App** > **nuova registrazione**.

    a. Assegnare all'applicazione un nome, ad esempio *AKSAzureADServer*.

    b. Per la **tipi di account supportati**, selezionare **gli account in questa directory dell'organizzazione solo**.
    
    c. Scegli **Web** per l'URI di reindirizzamento digitare e quindi immettere qualsiasi valore formattato URI, ad esempio *https://aksazureadserver* .

    d. Selezionare **registrare** quando hai finito.

2. Selezionare **Manifest**, quindi modificare il **groupMembershipClaims:** valore come **tutti**. Quando hai finito con gli aggiornamenti, selezionare **salvare**.

    ![Impostare l'appartenenza al gruppo su All](media/aad-integration/edit-manifest.png)

3. Nel riquadro a sinistra dell'applicazione Azure AD, selezionare **certificati e i segreti**.

    a. Selezionare **+ nuovo segreto client**.

    b. Aggiungere una descrizione della chiave, ad esempio *server di Azure AD AKS*. Scegliere un'ora di scadenza e quindi selezionare **Add**.

    c. Si noti il valore della chiave, che viene visualizzato solo in questo momento. Quando si distribuisce un cluster AKS abilitata AD Azure, questo valore viene chiamato il segreto dell'applicazione server.

4. Nel riquadro a sinistra dell'applicazione Azure AD, selezionare **le autorizzazioni API**, quindi selezionare **+ Aggiungi un'autorizzazione**.

    a. Sotto **Microsoft APIs**, selezionare **Microsoft Graph**.

    b. Selezionare **autorizzazioni delegate**, quindi selezionare la casella di controllo accanto a **Directory > All (lettura dati directory)** .

    c. Se un valore predefinito delegata l'autorizzazione per **utente > User. Read (Accedi e Leggi il profilo utente)** non esiste, selezionare la casella di controllo accanto a esso.

    d. Selezionare **autorizzazioni applicazione**, quindi selezionare la casella di controllo accanto a **Directory > All (lettura dati directory)** .

    ![Impostare le autorizzazioni di graph](media/aad-integration/graph-permissions.png)

    e. Selezionare **aggiungere autorizzazioni** per salvare gli aggiornamenti.

    f. Sotto **concedere il consenso**, selezionare **concedere il consenso dell'amministratore**. Questo pulsante non è disponibile se l'account corrente non è un amministratore del tenant.

    Quando le autorizzazioni vengono concesse correttamente, la notifica seguente viene visualizzata nel portale:

   ![Notifica di esito positivo delle autorizzazioni concesse](media/aad-integration/permissions-granted.png)

5. Nel riquadro a sinistra dell'applicazione Azure AD, selezionare **esporre un'API**, quindi selezionare **+ Aggiungi un ambito**.
    
    a. Immettere un **nome ambito**, un **nome visualizzato di consenso dell'amministratore**e quindi un' **descrizione del consenso dell'amministratore** , ad esempio *AKSAzureADServer*.

    b. Assicurarsi che **lo stato** è impostata su **abilitato**.

    ![Esporre l'app server come un'API per l'uso con altri servizi](media/aad-integration/expose-api.png)

    c. Selezionare **Aggiungi ambito**.

6. Tornare all'applicazione **Overview** pagina e prendere nota di **ID applicazione (client)** . Quando si distribuisce un cluster AKS abilitata AD Azure, questo valore viene chiamato ID dell'applicazione server.

    ![Ottenere l'ID applicazione](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>Creare l'applicazione client

La seconda applicazione di Azure AD viene usata quando si accede con la CLI di Kubernetes (kubectl).

1. Selezionare **Azure Active Directory** > **registrazioni per l'App** > **nuova registrazione**.

    a. Assegnare all'applicazione un nome, ad esempio *AKSAzureADClient*.

    b. Per la **tipi di account supportati**, selezionare **gli account in questa directory dell'organizzazione solo**.

    c. Selezionare **Web** per l'URI di reindirizzamento digitare e quindi immettere un valore qualsiasi formato URI, ad esempio *https://aksazureadclient* .

    d. Selezionare **registrare** quando hai finito.

2. Nel riquadro a sinistra dell'applicazione Azure AD, selezionare **le autorizzazioni API**, quindi selezionare **+ Aggiungi un'autorizzazione**.

    a. Selezionare **API My**, quindi scegliere l'applicazione server di Azure AD creato nel passaggio precedente, ad esempio *AKSAzureADServer*.

    b. Selezionare **autorizzazioni delegate**e quindi selezionare la casella di controllo accanto alle app del server Azure AD.

    ![Configurare le autorizzazioni per l'applicazione](media/aad-integration/select-api.png)

    c. Selezionare **aggiungere autorizzazioni**.

    d. Sotto **concedere il consenso**, selezionare **concedere il consenso dell'amministratore**. Questo pulsante non è disponibile se l'account corrente non è un amministratore del tenant. Quando vengono concesse le autorizzazioni, la notifica seguente viene visualizzata nel portale:

    ![Notifica di esito positivo delle autorizzazioni concesse](media/aad-integration/permissions-granted.png)

3. Nel riquadro a sinistra dell'applicazione Azure AD, selezionare **autenticazione**.

    - Sotto **tipo di client predefinito**, selezionare **Yes** a **trattare il client come un client pubblico**.

5. Nel riquadro a sinistra dell'applicazione Azure AD, annotare l'ID applicazione Quando si distribuisce un cluster AKS abilitata AD Azure, questo valore viene chiamato l'ID client dell'applicazione.

   ![Ottenere l'ID dell'applicazione](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>Ottenere l'ID tenant

Successivamente, ottenere l'ID del tenant di Azure. Questo valore viene usato quando si crea il cluster AKS.

Dal portale di Azure, selezionare **Azure Active Directory** > **delle proprietà** e notare il **ID Directory**. Quando si crea un cluster AKS abilitata AD Azure, questo valore viene chiamato l'ID tenant.

![Ottenere l'ID del tenant di Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>Distribuire il cluster AKS

Usare la [creare il gruppo di az][az-group-create] comando per creare un gruppo di risorse per il cluster AKS.

```azurecli
az group create --name myResourceGroup --location eastus
```

Usare la [az aks create][az-aks-create] comando per distribuire il cluster AKS. Successivamente, sostituire i valori nel comando seguente. Usare i valori raccolti durante la creazione di applicazioni di Azure AD per l'ID dell'app server, segreto dell'app, ID app client e ID tenant.

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

Un cluster AKS richiede alcuni minuti per creare.

## <a name="create-an-rbac-binding"></a>Creare un'associazione di RBAC

Prima di usare un account Azure Active Directory con un cluster del servizio contenitore di AZURE, è necessario creare un'associazione di ruolo o un'associazione di ruolo del cluster. I ruoli definiscono le autorizzazioni da concedere e associazioni applicano agli utenti desiderati. Queste assegnazioni possono essere applicate a uno spazio dei nomi specifico o all'intero cluster. Per altre informazioni, vedere [autorizzazione tramite RBAC][rbac-authorization].

In primo luogo, usare il [az aks get-credentials][az-aks-get-credentials] con il `--admin` argomento per accedere al cluster con l'accesso come amministratore.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Successivamente, creare ClusterRoleBinding per un account Azure AD che si desidera concedere l'accesso al cluster AKS. L'esempio seguente concede all'account di accesso completo a tutti gli spazi dei nomi nel cluster:

- Se l'utente si concede che l'associazione di RBAC per è nello stesso tenant di Azure AD, è possibile assegnare autorizzazioni in base al nome dell'entità utente (UPN). Passare al passaggio per creare il manifesto YAML per ClusterRoleBinding.

- Se l'utente è in un Azure AD diversi tenant, eseguire una query per e usare la **objectId** proprietà invece. Se necessario, ottenere l'objectId dell'account necessari utente usando il [show utente di ad az][az-ad-user-show] comando. Fornire il nome dell'entità utente (UPN) dell'account richiesto:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Creare un file, ad esempio *rbac-aad-user.yaml*e quindi incollare il contenuto seguente. L'ultima riga, sostituire **userPrincipalName_or_objectId** con l'ID oggetto o UPN. La scelta dipende dal fatto che l'utente è lo stesso tenant di Azure AD o non.

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

Applica l'associazione utilizzando il [kubectl applicare][kubectl-apply] seguente come mostrato nell'esempio seguente:

```console
kubectl apply -f rbac-aad-user.yaml
```

È anche possibile creare un'associazione di ruolo per tutti i membri di un gruppo di Azure AD. Gruppi di Azure AD vengono specificati usando l'ID oggetto gruppo, come illustrato nell'esempio seguente.

Creare un file, ad esempio *rbac-aad-group.yaml*e quindi incollare il contenuto seguente. Aggiornare l'ID oggetto del gruppo con un ID dal tenant di Azure AD:

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

Applica l'associazione utilizzando il [kubectl applicare][kubectl-apply] seguente come mostrato nell'esempio seguente:

```console
kubectl apply -f rbac-aad-group.yaml
```

Per altre informazioni sulla protezione di un cluster Kubernetes con RBAC, vedere [con autorizzazione RBAC][rbac-authorization].

## <a name="access-the-cluster-with-azure-ad"></a>Accedere al cluster con Azure AD

Estrarre il contesto per l'utente senza privilegi di amministratore usando il [az aks get-credentials][az-aks-get-credentials] comando.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Dopo aver eseguito la `kubectl` comando, verrà richiesto di eseguire l'autenticazione con Azure. Seguire sullo schermo le istruzioni per completare il processo, come illustrato nell'esempio seguente:

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

Quando il processo viene completato, viene memorizzato nella cache il token di autenticazione. Solo viene chiesto di accedere di nuovo quando il token scade o viene ricreato il file di configurazione di Kubernetes.

Se viene visualizzato un messaggio di errore di autorizzazione dopo avere effettuato l'acceso, controllare i criteri seguenti:

```console
error: You must be logged in to the server (Unauthorized)
```


- L'ID di oggetto appropriato o UPN, a seconda se l'account utente è nello stesso tenant di Azure AD o non definito.
- L'utente non è un membro dei gruppi di più di 200.
- Il segreto definito nella registrazione dell'applicazione per server corrisponde al valore configurato con `--aad-server-app-secret`.

## <a name="next-steps"></a>Passaggi successivi

Per usare i gruppi e utenti di Azure AD per controllare l'accesso alle risorse del cluster, vedere [controllare l'accesso alle risorse del cluster con controllo degli accessi basata sui ruoli e le identità di Azure AD nel servizio contenitore di AZURE][azure-ad-rbac].

Per altre informazioni su come i cluster Kubernetes sicuri, vedere [opzioni di accesso e identità per AKS][rbac-authorization].

Per altre informazioni sul controllo delle identità e risorse, vedere [procedure consigliate per l'autenticazione e autorizzazione nel servizio contenitore di AZURE][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
