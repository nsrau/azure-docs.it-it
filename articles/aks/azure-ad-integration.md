---
title: Integrare Azure Active Directory con il servizio Azure Kubernetes
description: Come creare cluster del servizio Azure Kubernetes abilitati per Azure Active Directory.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/26/2019
ms.author: iainfou
ms.openlocfilehash: 2a218a48223c81e009b83cb1f129601a8035e18e
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138452"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Integrare Azure Active Directory con il servizio Azure Kubernetes

Il servizio Azure Kubernetes può essere configurato per usare Azure Active Directory (AD) per l'autenticazione utente. In questa configurazione, è possibile accedere a un cluster del servizio contenitore di AZURE usando il token di autenticazione di Azure Active Directory. Inoltre, gli amministratori di cluster sono in grado di configurare Kubernetes accesso basato sui ruoli controllo degli accessi in base all'appartenenza identità o una directory dell'utente.

Questo articolo illustra come distribuire i prerequisiti per servizio contenitore di AZURE e Azure AD e quindi come distribuire un cluster di Azure Active Directory abilitata e creare un ruolo RBAC di base del cluster servizio contenitore di AZURE usando il portale di Azure. È anche possibile [completare questi passaggi usando Azure CLI][azure-ad-cli].

Si applicano le limitazioni seguenti:

- Azure AD può essere abilitato solo quando si crea un nuovo cluster abilitato per il controllo degli accessi in base al ruolo. Non è possibile abilitare Azure AD in un cluster del servizio Azure Kubernetes.
- *Guest* agli utenti di Azure AD, ad esempio come se si usa un accesso federato da una directory diversa, non sono supportati.

## <a name="authentication-details"></a>Dettagli di autenticazione

L'autenticazione di Azure AD è disponibile per i cluster di servizio Azure Kubernetes con OpenID Connect. OpenID Connect è un livello di gestione delle identità basato sul protocollo OAuth 2.0. Per altre informazioni su OpenID Connect, consulta [documentazione di OpenID Connect][open-id-connect].

Dall'interno del cluster Kubernetes viene usata l'autenticazione del token del webhook per verificare i token di autenticazione. L'autenticazione del token del webhook viene configurata e gestita come parte del cluster servizio Azure Kubernetes. Per altre informazioni sull'autenticazione del token del webhook, consulta [documentazione dell'autenticazione del webhook][kubernetes-webhook].

Per fornire l'autenticazione di Azure AD per un cluster AKS, vengono create due applicazioni Azure AD. La prima applicazione è un componente server che fornisce l'autenticazione degli utenti. La seconda applicazione è un componente client che viene usato quando viene richiesto per l'interfaccia della riga di comando per l'autenticazione. Questa applicazione client usa l'applicazione server per l'autenticazione effettivo delle credenziali forniti dal client.

> [!NOTE]
> Quando si configura Azure AD per l'autenticazione servizio Azure Kubernetes, vengono configurate due applicazioni di Azure AD. I passaggi di delegare le autorizzazioni per tutte le applicazioni devono essere completati da un amministratore di tenant di Azure.

## <a name="create-server-application"></a>Creare l'applicazione server

La prima applicazione di Azure AD viene usata per ottenere l'appartenenza ai gruppi di Azure AD degli utenti. Creare l'applicazione nel portale di Azure.

1. Selezionare **Azure Active Directory** > **registrazioni per l'App** > **nuova registrazione**.

    * Assegnare all'applicazione un nome, ad esempio *AKSAzureADServer*.
    * Per la **tipi di account supportati**, scegliere *gli account in questa directory dell'organizzazione solo*.
    * Scegli *Web* per il **URI di reindirizzamento** digitare e immettere un valore qualsiasi URI formattati, ad esempio *https://aksazureadserver*.
    * Selezionare **registrare** al termine.

1. Selezionare **Manifesto** e modificare il valore `groupMembershipClaims` in `"All"`.

    ![Impostare l'appartenenza al gruppo su All](media/aad-integration/edit-manifest.png)

    **Salvare** gli aggiornamenti al termine dell'operazione.

1. Nel riquadro di spostamento a sinistra dell'applicazione Azure AD, selezionare **certificati e i segreti**.

    * Scegli **+ nuovo segreto client**.
    * Aggiungere una descrizione della chiave, ad esempio *server di Azure AD AKS*. Scegliere un'ora di scadenza e quindi selezionare **Add**.
    * Prendere nota del valore della chiave. Che ha visualizzato solo questa volta iniziale. Quando si distribuisce un cluster AKS abilitata AD Azure, questo valore viene indicato come il `Server application secret`.

1. Nel riquadro di spostamento a sinistra dell'applicazione Azure AD, selezionare **le autorizzazioni API**, quindi scegliere **+ Aggiungi un'autorizzazione**.

    * Sotto **Microsoft APIs**, scegliere *Microsoft Graph*.
    * Scegli **autorizzazioni delegate**, quindi inserire un segno di spunta accanto a **Directory > All (lettura dati directory)**.
        * Se un valore predefinito delegata l'autorizzazione per **utente > User. Read (Accedi e Leggi il profilo utente)** non esiste, inserire un segno di spunta questa autorizzazione.
    * Scegli **autorizzazioni applicazione**, quindi inserire un segno di spunta accanto a **Directory > All (lettura dati directory)**.

        ![Impostare le autorizzazioni di graph](media/aad-integration/graph-permissions.png)

    * Scegli **aggiungere autorizzazioni** per salvare gli aggiornamenti.

    * Sotto il **concedere il consenso** keychains al **concedere il consenso dell'amministratore**. Questo pulsante è disattivato e non è disponibile se l'account corrente non è un amministratore del tenant.

        Quando le autorizzazioni sono state concesse correttamente, viene visualizzata nel portale la notifica seguente:

        ![Notifica di esito positivo delle autorizzazioni concesse](media/aad-integration/permissions-granted.png)

1. Nel riquadro di spostamento a sinistra dell'applicazione Azure AD, selezionare **esporre un'API**, quindi scegliere **+ Aggiungi un ambito**.
    
    * Impostare una *nome ambito*, *nome visualizzato di consenso dell'amministratore*, e *descrizione del consenso dell'amministratore*, ad esempio *AKSAzureADServer*.
    * Assicurarsi che il **lo stato** è impostata su *abilitato*.

        ![Esporre l'app server come un'API per l'uso con altri servizi](media/aad-integration/expose-api.png)

    * Scegli **Aggiungi ambito**.

1. Tornare all'applicazione **Overview** pagina e prendere nota del **ID applicazione (client)**. Quando si distribuisce un cluster AKS abilitata AD Azure, questo valore viene indicato come il `Server application ID`.

   ![Ottenere l'ID applicazione](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>Creare l'applicazione client

La seconda applicazione di Azure AD viene utilizzata quando l'accesso con la CLI di Kubernetes (`kubectl`).

1. Selezionare **Azure Active Directory** > **registrazioni per l'App** > **nuova registrazione**.

    * Assegnare all'applicazione un nome, ad esempio *AKSAzureADClient*.
    * Per la **tipi di account supportati**, scegliere *gli account in questa directory dell'organizzazione solo*.
    * Scegli *Web* per il **URI di reindirizzamento** digitare e immettere un valore qualsiasi URI formattati, ad esempio *https://aksazureadclient*.
    * Selezionare **registrare** al termine.

1. Nel riquadro di spostamento a sinistra dell'applicazione Azure AD, selezionare **le autorizzazioni API**, quindi scegliere **+ Aggiungi un'autorizzazione**.

    * Selezionare **API My**, quindi scegliere l'applicazione server di Azure AD creato nel passaggio precedente, ad esempio *AKSAzureADServer*.
    * Scegli **autorizzazioni delegate**, quindi inserire un segno di spunta accanto alle app del server Azure AD.

        ![Configurare le autorizzazioni per l'applicazione](media/aad-integration/select-api.png)

    * Selezionare **aggiungere autorizzazioni**.

    * Sotto il **concedere il consenso** keychains al **concedere il consenso dell'amministratore**. Questo pulsante è disattivato e non è disponibile se l'account corrente non è un amministratore del tenant.

        Quando le autorizzazioni sono state concesse correttamente, viene visualizzata nel portale la notifica seguente:

        ![Notifica di esito positivo delle autorizzazioni concesse](media/aad-integration/permissions-granted.png)

1. Nel riquadro di spostamento a sinistra dell'applicazione Azure AD, annotare il **ID applicazione**. Quando si distribuisce un cluster servizio Azure Kubernetes abilitato per Azure AD, questo valore viene definito `Client application ID`.

   ![Ottenere l'ID dell'applicazione](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>Ottenere l'ID tenant

Infine, ottenere l'ID del tenant di Azure. Questo valore viene usato quando si crea il cluster AKS.

Dal portale di Azure selezionare **Azure Active Directory** > **Proprietà** e prendere nota di **ID directory**. Quando si crea un cluster AKS abilitata AD Azure, questo valore viene indicato come il `Tenant ID`.

![Ottenere l'ID del tenant di Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>Distribuire il cluster

Usare il comando [az group create][az-group-create] per creare un gruppo di risorse per il cluster servizio Azure Kubernetes.

```azurecli
az group create --name myResourceGroup --location eastus
```

Distribuire il cluster con il comando [az servizio Azure Kubernetes create][az-aks-create]. Sostituire i valori nel comando di esempio seguente con i valori raccolti durante la creazione di applicazioni di Azure AD per l'ID del server app e segreto, ID app client e ID tenant:

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

Sono necessari alcuni minuti per creare il cluster AKS.

## <a name="create-rbac-binding"></a>Creare l'associazione RBAC

Prima di poter usare un account di Azure Active Directory con il cluster servizio Azure Kubernetes, è necessario creare un'associazione di ruolo o un'associazione di ruolo del cluster. I *ruoli* definiscono le autorizzazioni da concedere e le *associazioni* le applicano agli utenti desiderati. Queste assegnazioni possono essere applicate a uno spazio dei nomi specifico o all'intero cluster. Per altre informazioni, vedere [Uso di autorizzazioni del controllo degli accessi in base al ruolo][rbac-authorization].

In primo luogo, usare il [az aks get-credentials] [ az-aks-get-credentials] comando con il `--admin` argomento per accedere al cluster con l'accesso come amministratore.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Creare quindi un ClusterRoleBinding per un account Azure AD che si desidera concedere l'accesso al cluster AKS. Nell'esempio seguente consente all'account di accesso completo a tutti gli spazi dei nomi nel cluster.

- Se l'utente si concede che l'associazione di RBAC per è nello stesso tenant di Azure AD, è possibile assegnare autorizzazioni in base al nome dell'entità utente (UPN). Passare al passaggio per creare il manifesto YAML per il ClusterRuleBinding.

- Se l'utente è in un Azure AD diversi tenant, eseguire una query per e usare la *objectId* proprietà invece. Se necessario, ottenere il *objectId* dell'utente richiesto account tramite il [show utente di ad az] [ az-ad-user-show] comando. Fornire il nome dell'entità utente (UPN) dell'account richiesto:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Creare un file, ad esempio *rbac-aad-user.yaml*, e incollare i contenuti seguenti. L'ultima riga, sostituire *userPrincipalName_or_objectId* con l'ID oggetto o UPN a seconda se l'utente è lo stesso tenant di Azure AD o meno.

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

Applicare il binding usando il comando [kubectl apply][kubectl-apply] come illustrato nell'esempio seguente:

```console
kubectl apply -f rbac-aad-user.yaml
```

È anche possibile creare un'associazione di ruolo per tutti i membri di un gruppo di Azure AD. I gruppi di Azure AD vengono specificati tramite l'ID oggetto del gruppo, come mostrato nell'esempio seguente. Creare un file, ad esempio *rbac-aad-group.yaml*, e incollare i contenuti seguenti. Aggiornare l'ID oggetto del gruppo con un ID dal tenant di Azure AD:

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

Applicare il binding usando il comando [kubectl apply][kubectl-apply] come illustrato nell'esempio seguente:

```console
kubectl apply -f rbac-aad-group.yaml
```

Per altre informazioni sulla protezione di un cluster Kubernetes con RBAC, vedere [Using RBAC Authorization][rbac-authorization] (Uso delle autorizzazioni RBAC).

## <a name="access-cluster-with-azure-ad"></a>Accedere al cluster con Azure AD

Successivamente, effettuare il pull del contesto per l'utente non amministratore con il comando [az servizio Azure Kubernetes get-credentials][az-aks-get-credentials].

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Dopo aver eseguito un `kubectl` comando, viene richiesto di autenticarsi con Azure. Seguire sullo schermo le istruzioni per completare il processo, come illustrato nell'esempio seguente:

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

Al termine dell'esercitazione, viene memorizzato nella cache il token di autenticazione. Sono reinserire solo per l'accesso quando il token è scaduto o il file di configurazione di Kubernetes creato nuovamente.

Se viene visualizzato un messaggio di errore di autorizzazione dopo aver avuto accesso correttamente, verificare che vengano soddisfatte le condizioni seguenti:
1. L'utente si accede è diverso da un Guest nell'istanza di Azure AD (in questo scenario è spesso il caso se si usa un account federato da una directory diversa).
2. L'utente non deve essere membro di più di 200 gruppi.

```console
error: You must be logged in to the server (Unauthorized)
```

## <a name="next-steps"></a>Passaggi successivi

Per usare i gruppi e utenti di Azure AD per controllare l'accesso alle risorse del cluster, vedere [controllare l'accesso alle risorse del cluster con controllo degli accessi basata sui ruoli e le identità di Azure AD in AKS][azure-ad-rbac].

Per altre informazioni su come proteggere i cluster Kubernetes, vedere [opzioni di accesso e identità per AKS)][rbac-authorization].

Per le procedure consigliate sul controllo delle identità e risorse, vedere [procedure consigliate per l'autenticazione e autorizzazione nel servizio contenitore di AZURE][operator-best-practices-identity].

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
