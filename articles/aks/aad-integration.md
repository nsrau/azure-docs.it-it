---
title: Integrare Azure Active Directory con il servizio Kubernetes di Azure
description: Come creare cluster del servizio Kubernetes di Azure abilitati per Azure Active Directory.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 6/17/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 7d157d50bbcd25edd9cd6693a71fb04535cbeb79
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937382"
---
# <a name="integrate-azure-active-directory-with-aks---preview"></a>Integrare Azure Active Directory con AKS - Anteprima

Il servizio Kubernetes di Azure (AKS) può essere configurato per usare Azure Active Directory per l'autenticazione utente. In questa configurazione, è possibile accedere a un cluster del servizio Kubernetes di Azure tramite il token di autenticazione di Azure Active Directory. Inoltre, gli amministratori di cluster possono configurare il controllo degli accessi in base al ruolo di Kubernetes sulla base delle identità utente o dell'appartenenza a un gruppo della directory.

Questo documento illustra in dettaglio la creazione di tutti i prerequisiti necessari per AKS e Azure AD, la distribuzione di un cluster abilitato per Azure AD e la creazione di un semplice ruolo RBAC nel cluster AKS.

> [!IMPORTANT]
> L'integrazione di RBAC ed Azure AD per il servizio Kubernetes di Azure (AKS) è attualmente in **anteprima**. Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.
>

## <a name="authentication-details"></a>Dettagli di autenticazione

L'autenticazione di Azure AD è disponibile per i cluster di Kubernetes di Azure con OpenID Connect. OpenID Connect è un livello di gestione delle identità basato sul protocollo OAuth 2.0. Altre informazioni su OpenID Connect sono disponibili nella [documentazione di OpenID Connect][open-id-connect].

Dall'interno del cluster Kubernetes viene usata l'autenticazione del token del webhook per verificare i token di autenticazione. L'autenticazione del token del webhook viene configurata e gestita come parte del cluster AKS. Altre informazioni sull'autenticazione del token del webhook sono disponibili nella [documentazione dell'autenticazione del webhook][kubernetes-webhook].

> [!NOTE]
> Quando si configura Azure AD per l'autenticazione AKS, vengono configurate due applicazioni di Azure AD. Questa operazione deve essere completata da un amministratore tenant di Azure.

## <a name="create-server-application"></a>Creare l'applicazione server

La prima applicazione di Azure AD viene usata per ottenere l'appartenenza ai gruppi di Azure AD degli utenti.

1. Selezionare **Azure Active Directory** > **Registrazioni per l'app** > **Registrazione nuova applicazione**.

  Assegnare un nome all'applicazione, selezionare **App Web / API** per il tipo di applicazione e immettere un valore URI formattato per **URL accesso**. Selezionare **Crea** al termine.

  ![Creare la registrazione di Azure AD](media/aad-integration/app-registration.png)

2. Selezionare **Manifesto** e modificare il valore `groupMembershipClaims` in `"All"`.

  Salvare gli aggiornamenti al termine.

  ![Impostare l'appartenenza al gruppo su All](media/aad-integration/edit-manifest.png)

3. Tornare all'applicazione Azure AD e selezionare **Impostazioni** > **Chiavi**.

  Aggiungere una descrizione della chiave, selezionare una scadenza e selezionare **Salva**. Prendere nota del valore della chiave. Quando si distribuisce un cluster AKS abilitato per Azure AD, questo valore viene definito `Server application secret`.

  ![Ottenere la chiave privata dell'applicazione](media/aad-integration/application-key.png)

4. Tornare all'applicazione di Azure AD, selezionare **Impostazioni** > **Autorizzazioni necessarie** > **Aggiungi** > **Selezionare un'API** > **Microsoft Graph** > **Seleziona**.

  In **Autorizzazioni applicazione** selezionare **Lettura dati directory**.

  ![Impostare le autorizzazioni dell'applicazione per Graph](media/aad-integration/read-directory.png)

5. In **Autorizzazioni delegate** selezionare **Accedi e leggi il profilo di un altro utente** e **Lettura dati directory**. Salvare gli aggiornamenti al termine.

  ![Impostare le autorizzazioni dell'applicazione per Graph](media/aad-integration/delegated-permissions.png)

6. Selezionare **Chiudi** e **Concedi autorizzazioni** per completare questo passaggio. Questo passaggio avrà esito negativo se l'account corrente non è un amministratore del tenant.

  ![Impostare le autorizzazioni dell'applicazione per Graph](media/aad-integration/grant-permissions.png)

7. Tornare all'applicazione e prendere nota di **ID applicazione**. Quando si distribuisce un cluster AKS abilitato per Azure AD, questo valore viene definito `Server application ID`.

  ![Ottenere l'ID applicazione](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>Creare l'applicazione client

La seconda applicazione di Azure AD viene usata per l'accesso con l'interfaccia della riga di comando di Kubernetes (kubectl).

1. Selezionare **Azure Active Directory** > **Registrazioni per l'app** > **Registrazione nuova applicazione**.

  Assegnare un nome all'applicazione, selezionare **Nativa** per il tipo di applicazione e immettere un valore URI formattato per **URI di reindirizzamento**. Selezionare **Crea** al termine.

  ![Creare la registrazione per AAD](media/aad-integration/app-registration-client.png)

2. Dall'applicazione Azure AD selezionare **Impostazioni** > **Autorizzazioni richieste** > **Aggiungi** > **Selezionare un'API** e cercare il nome dell'applicazione server creata nell'ultimo passaggio del documento.

  ![Configurare le autorizzazioni per l'applicazione](media/aad-integration/select-api.png)

3. Inserire un segno di spunta accanto all'applicazione e fare clic su **Seleziona**.

  ![Selezionare l'endpoint dell'applicazione server AAD AKS](media/aad-integration/select-server-app.png)

4. Selezionare **Chiudi** e **Concedi autorizzazioni** per completare questo passaggio.

  ![Concedere le autorizzazioni](media/aad-integration/grant-permissions-client.png)

5. Tornare all'applicazione AD e prendere nota di **ID applicazione**. Quando si distribuisce un cluster AKS abilitato per Azure AD, questo valore viene definito `Client application ID`.

  ![Ottenere l'ID dell'applicazione](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>Ottenere l'ID tenant

Infine, ottenere l'ID del tenant di Azure. Questo valore viene usato anche quando si distribuisce il cluster AKS.

Dal portale di Azure selezionare **Azure Active Directory** > **Proprietà** e prendere nota di **ID directory**. Quando si distribuisce un cluster AKS abilitato per Azure AD, questo valore viene definito `Tenant ID`.

![Ottenere l'ID del tenant di Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>Distribuire il cluster

Usare il comando [az group create][az-group-create] per creare un gruppo di risorse per il cluster AKS.

```azurecli
az group create --name myAKSCluster --location eastus
```

Distribuire il cluster con il comando [az aks create][az-aks-create]. Sostituire i valori nel comando di esempio seguente con i valori raccolti durante la creazione delle applicazioni Azure AD.

```azurecli
az aks create --resource-group myAKSCluster --name myAKSCluster --generate-ssh-keys --enable-rbac \
  --aad-server-app-id 7ee598bb-0000-0000-0000-83692e2d717e \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 7ee598bb-0000-0000-0000-83692e2d717e \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

## <a name="create-rbac-binding"></a>Creare l'associazione RBAC

Prima di poter usare un account di Azure Active Directory con il cluster AKS, è necessario creare un'associazione di ruolo o un'associazione di ruolo del cluster.

Prima di tutto, usare il comando [az aks get-credentials][az-aks-get-credentials] con l'argomento `--admin` per accedere al cluster con l'accesso amministrativo.

```azurecli
az aks get-credentials --resource-group myAKSCluster --name myAKSCluster --admin
```

Successivamente, usare il manifesto seguente per creare un ClusterRoleBinding per un account di Azure AD. Aggiornare il nome utente con uno dal tenant di Azure AD. Questo esempio concede all'account l'accesso completo a tutti gli spazi dei nomi del cluster.

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

È anche possibile creare un'associazione di ruolo per tutti i membri di un gruppo di Azure AD. Il manifesto seguente concede a tutti i membri del gruppo `kubernetes-admin` l'accesso amministrativo al cluster.

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
   name: "kubernetes-admin"
```

Per altre informazioni sulla protezione di un cluster Kubernetes con RBAC, vedere [Using RBAC Authorization][rbac-authorization] (Uso delle autorizzazioni RBAC).

## <a name="access-cluster-with-azure-ad"></a>Accedere al cluster con Azure AD

Successivamente, effettuare il pull del contesto per l'utente non amministratore con il comando [az aks get-credentials][az-aks-get-credentials].

```azurecli
az aks get-credentials --resource-group myAKSCluster --name myAKSCluster
```

Dopo l'esecuzione di qualsiasi comando kubectl, verrà richiesto di eseguire l'autenticazione con Azure. Seguire quindi le istruzioni visualizzate sullo schermo.

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-42032720-0   Ready     agent     1h        v1.9.6
aks-nodepool1-42032720-1   Ready     agent     1h        v1.9.6
aks-nodepool1-42032720-2   Ready     agent     1h        v1.9.6
```

Al termine, il token di autenticazione viene memorizzato nella cache. Verrà richiesto di eseguire di nuovo l'accesso solo dopo la scadenza del token o quando viene ricreato il file di configurazione di Kubernetes.

## <a name="next-steps"></a>Passaggi successivi

Scoprire di più sulla protezione dei cluster Kubernetes con RBAC nella documentazione [Using RBAC Authorization][rbac-authorization] (Uso delle autorizzazioni RBAC).

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[rbac-authorization]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az_aks_get_credentials
[az-group-create]: /cli/azure/group#az_group_create
[open-id-connect]: ../active-directory/develop/active-directory-protocols-openid-connect-code.md
