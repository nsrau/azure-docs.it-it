---
title: Integrare Azure Active Directory con il servizio Azure Kubernetes
description: Come creare cluster del servizio Azure Kubernetes abilitati per Azure Active Directory.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/09/2018
ms.author: iainfou
ms.openlocfilehash: 9bdd3060219907f95454bfc9248572f796afd72e
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2018
ms.locfileid: "53437607"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Integrare Azure Active Directory con il servizio Azure Kubernetes

Il servizio Azure Kubernetes può essere configurato per usare Azure Active Directory (AD) per l'autenticazione utente. In questa configurazione, è possibile accedere a un cluster servizio Azure Kubernetes tramite il token di autenticazione di Azure Active Directory. Inoltre, gli amministratori di cluster possono configurare il controllo degli accessi (RBAC) in base al ruolo di Kubernetes sulla base delle identità utente o dell'appartenenza a un gruppo della directory.

Questo documento illustra la distribuzione di tutti i prerequisiti necessari per servizio Azure Kubernetes e Azure AD, la distribuzione di un cluster abilitato per Azure AD e la creazione di un semplice ruolo RBAC nel cluster servizio Azure Kubernetes.

Si applicano le limitazioni seguenti:

- I cluster servizio Azure Kubernetes non abilitati RBAC esistenti non possono essere attualmente aggiornati per usare un ruolo RBAC.
- Gli utenti *Guest* di Azure AD, se si usa, ad esempio, un account di accesso federato da una directory diversa, non sono supportati.

## <a name="authentication-details"></a>Dettagli di autenticazione

L'autenticazione di Azure AD è disponibile per i cluster di servizio Azure Kubernetes con OpenID Connect. OpenID Connect è un livello di gestione delle identità basato sul protocollo OAuth 2.0. Per altre informazioni su OpenID Connect, consulta [documentazione di OpenID Connect][open-id-connect].

Dall'interno del cluster Kubernetes viene usata l'autenticazione del token del webhook per verificare i token di autenticazione. L'autenticazione del token del webhook viene configurata e gestita come parte del cluster servizio Azure Kubernetes. Per altre informazioni sull'autenticazione del token del webhook, consulta [documentazione dell'autenticazione del webhook][kubernetes-webhook].

> [!NOTE]
> Quando si configura Azure AD per l'autenticazione servizio Azure Kubernetes, vengono configurate due applicazioni di Azure AD. Questa operazione deve essere completata da un amministratore tenant di Azure.

## <a name="create-server-application"></a>Creare l'applicazione server

La prima applicazione di Azure AD viene usata per ottenere l'appartenenza ai gruppi di Azure AD degli utenti.

1. Selezionare **Azure Active Directory** > **Registrazioni per l'app** > **Registrazione nuova applicazione**.

  Assegnare un nome all'applicazione, selezionare **App Web / API** per il tipo di applicazione e immettere un valore URI formattato per **URL accesso**. Selezionare **Crea** al termine.

  ![Creare la registrazione di Azure AD](media/aad-integration/app-registration.png)

2. Selezionare **Manifesto** e modificare il valore `groupMembershipClaims` in `"All"`.

  Salvare gli aggiornamenti al termine.

  ![Impostare l'appartenenza al gruppo su All](media/aad-integration/edit-manifest.png)

3. Tornare all'applicazione Azure AD e selezionare **Impostazioni** > **Chiavi**.

  Aggiungere una descrizione della chiave, selezionare una scadenza e selezionare **Salva**. Prendere nota del valore della chiave. Quando si distribuisce un cluster servizio Azure Kubernetes abilitato per Azure AD, questo valore viene definito `Server application secret`.

  ![Ottenere la chiave privata dell'applicazione](media/aad-integration/application-key.png)

4. Tornare all'applicazione di Azure AD, selezionare **Impostazioni** > **Autorizzazioni necessarie** > **Aggiungi** > **Selezionare un'API** > **Microsoft Graph** > **Seleziona**.

  ![Selezionare l'API Graph](media/aad-integration/graph-api.png)

5. In **Autorizzazioni applicazione** selezionare **Lettura dati directory**.

  ![Impostare le autorizzazioni dell'applicazione per Graph](media/aad-integration/read-directory.png)

6. In **Autorizzazioni delegate** selezionare **Accedi e leggi il profilo di un altro utente** e **Lettura dati directory**. Salvare gli aggiornamenti al termine.

  ![Impostare le autorizzazioni dell'applicazione per Graph](media/aad-integration/delegated-permissions.png)

  Selezionare **Operazione completata**.

7. Scegliere *Microsoft Graph* nell'elenco delle API, quindi selezionare **Concedi autorizzazioni**. Questo passaggio avrà esito negativo se l'account corrente non è un amministratore del tenant.

  ![Impostare le autorizzazioni dell'applicazione per Graph](media/aad-integration/grant-permissions.png)

  Quando le autorizzazioni sono state concesse correttamente, viene visualizzata nel portale la notifica seguente:

  ![Notifica di esito positivo delle autorizzazioni concesse](media/aad-integration/permissions-granted.png)

8. Tornare all'applicazione e prendere nota di **ID applicazione**. Quando si distribuisce un cluster servizio Azure Kubernetes abilitato per Azure AD, questo valore viene definito `Server application ID`.

  ![Ottenere l'ID applicazione](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>Creare l'applicazione client

La seconda applicazione di Azure AD viene usata per l'accesso con l'interfaccia della riga di comando di Kubernetes (kubectl).

1. Selezionare **Azure Active Directory** > **Registrazioni per l'app** > **Registrazione nuova applicazione**.

  Assegnare un nome all'applicazione, selezionare **Nativa** per il tipo di applicazione e immettere un valore URI formattato per **URI di reindirizzamento**. Selezionare **Crea** al termine.

  ![Creare la registrazione per AAD](media/aad-integration/app-registration-client.png)

2. Dall'applicazione Azure AD selezionare **Impostazioni** > **Autorizzazioni richieste** > **Aggiungi** > **Selezionare un'API** e cercare il nome dell'applicazione server creata nell'ultimo passaggio del documento.

  ![Configurare le autorizzazioni per l'applicazione](media/aad-integration/select-api.png)

3. Inserire un segno di spunta accanto all'applicazione e fare clic su **Seleziona**.

  ![Selezionare l'endpoint dell'applicazione server AAD servizio Azure Kubernetes](media/aad-integration/select-server-app.png)

  Selezionare **Operazione completata**

4. Selezionare l'API del server dall'elenco e quindi scegliere **Concedi autorizzazioni**:

  ![Concedere le autorizzazioni](media/aad-integration/grant-permissions-client.png)

5. Tornare all'applicazione AD e prendere nota di **ID applicazione**. Quando si distribuisce un cluster servizio Azure Kubernetes abilitato per Azure AD, questo valore viene definito `Client application ID`.

  ![Ottenere l'ID dell'applicazione](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>Ottenere l'ID tenant

Infine, ottenere l'ID del tenant di Azure. Questo valore viene usato anche quando si distribuisce il cluster servizio Azure Kubernetes.

Dal portale di Azure selezionare **Azure Active Directory** > **Proprietà** e prendere nota di **ID directory**. Quando si distribuisce un cluster servizio Azure Kubernetes abilitato per Azure AD, questo valore viene definito `Tenant ID`.

![Ottenere l'ID del tenant di Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>Distribuire il cluster

Usare il comando [az group create][az-group-create] per creare un gruppo di risorse per il cluster servizio Azure Kubernetes.

```azurecli
az group create --name myResourceGroup --location eastus
```

Distribuire il cluster con il comando [az servizio Azure Kubernetes create][az-aks-create]. Sostituire i valori nel comando di esempio seguente con i valori raccolti durante la creazione delle applicazioni Azure AD.

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

## <a name="create-rbac-binding"></a>Creare l'associazione RBAC

Prima di poter usare un account di Azure Active Directory con il cluster servizio Azure Kubernetes, è necessario creare un'associazione di ruolo o un'associazione di ruolo del cluster. I *ruoli* definiscono le autorizzazioni da concedere e le *associazioni* le applicano agli utenti desiderati. Queste assegnazioni possono essere applicate a uno spazio dei nomi specifico o all'intero cluster. Per altre informazioni, vedere [Uso di autorizzazioni del controllo degli accessi in base al ruolo][rbac-authorization].

Prima di tutto, usare il comando [az servizio Azure Kubernetes get-credentials][az-aks-get-credentials] con l'argomento `--admin` per accedere al cluster con l'accesso amministrativo.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Successivamente, usare il manifesto seguente per creare un ClusterRoleBinding per un account di Azure AD. Questo esempio concede all'account l'accesso completo a tutti gli spazi dei nomi del cluster. Creare un file, ad esempio *rbac-aad-user.yaml*, e incollare i contenuti seguenti. Aggiornare il nome utente con un nome dal tenant di Azure AD:

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
  name: "user@contoso.com"
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

Dopo l'esecuzione di qualsiasi comando kubectl, verrà richiesto di eseguire l'autenticazione con Azure. Seguire quindi le istruzioni visualizzate sullo schermo.

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.9.9
aks-nodepool1-79590246-1   Ready     agent     1h        v1.9.9
aks-nodepool1-79590246-2   Ready     agent     1h        v1.9.9
```

Al termine, il token di autenticazione viene memorizzato nella cache. Verrà richiesto di eseguire di nuovo l'accesso solo dopo la scadenza del token o quando viene ricreato il file di configurazione di Kubernetes.

Se, dopo aver eseguito l'accesso, viene visualizzato un messaggio di errore di autorizzazione, verificare che l'utente con cui si accede non sia un utente Guest in Azure AD (questo è spesso il caso se si usa un account di accesso federato da una directory diversa).

```console
error: You must be logged in to the server (Unauthorized)
```

## <a name="next-steps"></a>Passaggi successivi

Scoprire di più sulla protezione dei cluster Kubernetes con RBAC nella documentazione [Using RBAC Authorization][rbac-authorization] (Uso delle autorizzazioni RBAC).

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[rbac-authorization]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
