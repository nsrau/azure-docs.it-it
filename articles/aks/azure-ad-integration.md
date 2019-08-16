---
title: Integrare Azure Active Directory con il servizio Azure Kubernetes
description: Come creare cluster Azure Kubernetes Service (AKS) abilitati per Azure Active Directory
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/26/2019
ms.author: mlearned
ms.openlocfilehash: 5dabbb6458d0d0d4af51490bea0c3f38a7c5c41d
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69542901"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Integrare Azure Active Directory con il servizio Azure Kubernetes

Azure Kubernetes Service (AKS) può essere configurato per usare Azure Active Directory (Azure AD) per l'autenticazione utente. In questa configurazione è possibile accedere a un cluster AKS usando il Azure AD token di autenticazione.

Gli amministratori del cluster possono configurare il controllo degli accessi in base al ruolo Kubernetes (RBAC) in base all'identità dell'utente o al gruppo di directory.

Questo articolo spiega come:

- Distribuire i prerequisiti per AKS e Azure AD.
- Distribuire un cluster abilitato per Azure AD.
- Creare un ruolo di base RBAC nel cluster AKS usando il portale di Azure.

È anche possibile completare questa procedura usando l'interfaccia della riga di comando di [Azure][azure-ad-cli].

> [!NOTE]
> Azure AD possibile abilitare solo quando si crea un nuovo cluster abilitato per il controllo degli accessi in base al ruolo. Non è possibile abilitare Azure AD in un cluster del servizio Azure Kubernetes.

## <a name="authentication-details"></a>Dettagli di autenticazione

Azure AD autenticazione viene fornita ai cluster AKS con OpenID Connect. OpenID Connect è un livello di gestione delle identità basato sul protocollo OAuth 2.0.

Per altre informazioni su OpenID Connect, vedere [autorizzare l'accesso ad applicazioni Web con OpenID Connect e Azure ad][open-id-connect].

All'interno di un cluster Kubernetes, l'autenticazione del token webhook viene usata per i token di autenticazione. L'autenticazione del token del webhook viene configurata e gestita come parte del cluster servizio Azure Kubernetes.

Per ulteriori informazioni sull'autenticazione del token webhook, vedere la sezione [autenticazione del token webhook][kubernetes-webhook] nella documentazione di Kubernetes.

Per fornire l'autenticazione Azure AD per un cluster AKS, vengono create due applicazioni Azure AD. La prima applicazione è un componente server che fornisce l'autenticazione utente. La seconda applicazione è un componente client usato quando viene richiesto dall'interfaccia della riga di comando per l'autenticazione. Questa applicazione client utilizza l'applicazione server per l'autenticazione effettiva delle credenziali fornite dal client.

> [!NOTE]
> Quando si configura Azure AD per l'autenticazione AKS, vengono configurate due applicazioni Azure AD. I passaggi per delegare le autorizzazioni per ogni applicazione devono essere completati da un amministratore tenant di Azure.

## <a name="create-the-server-application"></a>Creare l'applicazione server

Viene applicata la prima applicazione Azure AD per ottenere l'appartenenza a un gruppo di Azure AD dell'utente. Per creare l'applicazione nel portale di Azure:

1. Selezionare **Azure Active Directory** > registrazioni appnuova > **registrazione**.

    a. Assegnare un nome all'applicazione, ad esempio *AKSAzureADServer*.

    b. Per i **tipi di account supportati**, selezionare **account solo in questa directory aziendale**.
    
    c. Scegliere **Web** per il tipo di URI di reindirizzamento, quindi immettere qualsiasi valore in *https://aksazureadserver* formato URI, ad esempio.

    d. Al termine, selezionare **Register (registra** ).

2. Selezionare **manifesto**e quindi modificare il valore di **groupMembershipClaims:** **All**. Al termine dell'aggiornamento, selezionare **Salva**.

    ![Impostare l'appartenenza al gruppo su All](media/aad-integration/edit-manifest.png)

3. Nel riquadro sinistro dell'applicazione Azure AD selezionare **certificati & segreti**.

    a. Selezionare **+ nuovo segreto client**.

    b. Aggiungere una descrizione della chiave, ad esempio *AKS Azure ad server*. Scegliere una data di scadenza, quindi selezionare **Aggiungi**.

    c. Si noti il valore della chiave, che viene visualizzato solo in questo momento. Quando si distribuisce un cluster AKS abilitato per Azure AD, questo valore viene chiamato segreto dell'applicazione server.

4. Nel riquadro sinistro dell'applicazione Azure AD selezionare **autorizzazioni API**e quindi selezionare **+ Aggiungi un'autorizzazione**.

    a. In **Microsoft API**selezionare **Microsoft Graph**.

    b. Selezionare **autorizzazioni delegate**, quindi selezionare la casella di controllo accanto a **directory > directory. Read. All (lettura dati directory)** .

    c. Se un'autorizzazione delegata predefinita per **user > User. Read (accesso e lettura del profilo utente)** non esiste, selezionare la casella di controllo accanto.

    d. Selezionare **Autorizzazioni applicazione**, quindi selezionare la casella di controllo accanto a **directory > directory. Read. All (lettura dati directory)** .

    ![Impostare le autorizzazioni del grafo](media/aad-integration/graph-permissions.png)

    e. Selezionare **Aggiungi autorizzazioni** per salvare gli aggiornamenti.

    f. In **Concedi consenso**selezionare **concedi il consenso dell'amministratore**. Questo pulsante non è disponibile se l'account corrente non è un amministratore tenant.

    Quando le autorizzazioni vengono concesse correttamente, nel portale viene visualizzata la notifica seguente:

   ![Notifica di esito positivo delle autorizzazioni concesse](media/aad-integration/permissions-granted.png)

5. Nel riquadro sinistro dell'applicazione Azure AD selezionare **esporre un'API**e quindi selezionare **+ Aggiungi un ambito**.
    
    a. Immettere un **nome di ambito**, un **nome visualizzato del consenso dell'amministratore**e quindi una descrizione del consenso dell' **amministratore** , ad esempio *AKSAzureADServer*.

    b. Verificare che **lo stato** sia impostato su **abilitato**.

    ![Esporre l'app Server come API da usare con altri servizi](media/aad-integration/expose-api.png)

    c. Selezionare **Aggiungi ambito**.

6. Tornare alla pagina **Panoramica** dell'applicazione e prendere nota dell' **ID applicazione (client)** . Quando si distribuisce un cluster AKS abilitato per Azure AD, questo valore è denominato ID applicazione server.

    ![Ottenere l'ID applicazione](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>Creare l'applicazione client

La seconda applicazione Azure AD viene usata quando si accede con l'interfaccia della riga di comando di Kubernetes (kubectl).

1. Selezionare **Azure Active Directory** > registrazioni appnuova > **registrazione**.

    a. Assegnare un nome all'applicazione, ad esempio *AKSAzureADClient*.

    b. Per i **tipi di account supportati**, selezionare **account solo in questa directory aziendale**.

    c. Selezionare **Web** per il tipo di URI di reindirizzamento, quindi immettere qualsiasi valore in *https://aksazureadclient* formato URI, ad esempio.

    d. Al termine, selezionare **Register (registra** ).

2. Nel riquadro sinistro dell'applicazione Azure AD selezionare **autorizzazioni API**e quindi selezionare **+ Aggiungi un'autorizzazione**.

    a. Selezionare **API personali**, quindi scegliere l'applicazione di Azure ad server creata nel passaggio precedente, ad esempio *AKSAzureADServer*.

    b. Selezionare **autorizzazioni delegate**, quindi selezionare la casella di controllo accanto all'app Azure ad server.

    ![Configurare le autorizzazioni per l'applicazione](media/aad-integration/select-api.png)

    c. Selezionare **Aggiungi autorizzazioni**.

    d. In **Concedi consenso**selezionare **concedi il consenso dell'amministratore**. Questo pulsante non è disponibile se l'account corrente non è un amministratore tenant. Quando vengono concesse le autorizzazioni, nel portale viene visualizzata la notifica seguente:

    ![Notifica di esito positivo delle autorizzazioni concesse](media/aad-integration/permissions-granted.png)

3. Nel riquadro sinistro dell'applicazione Azure AD selezionare **autenticazione**.

    - In **tipo di client predefinito**selezionare **Sì** per **trattare il client come client pubblico**.

5. Nel riquadro sinistro dell'applicazione Azure AD prendere nota dell'ID applicazione. Quando si distribuisce un cluster AKS abilitato per Azure AD, questo valore viene chiamato ID dell'applicazione client.

   ![Ottenere l'ID dell'applicazione](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>Ottenere l'ID tenant

Ottenere quindi l'ID del tenant di Azure. Questo valore viene usato quando si crea il cluster AKS.

Dal portale di Azure selezionare **Azure Active Directory** > **Proprietà** e annotare l' **ID directory**. Quando si crea un cluster AKS abilitato per Azure AD, questo valore viene chiamato ID tenant.

![Ottenere l'ID del tenant di Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>Distribuire il cluster AKS

Usare il comando [AZ Group create][az-group-create] per creare un gruppo di risorse per il cluster AKS.

```azurecli
az group create --name myResourceGroup --location eastus
```

Usare il comando [AZ AKS create][az-aks-create] per distribuire il cluster AKS. Sostituire quindi i valori nel comando di esempio seguente. Usare i valori raccolti quando sono state create le applicazioni Azure AD per l'ID app Server, il segreto app, l'ID app client e l'ID tenant.

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

Per la creazione di un cluster AKS sono necessari alcuni minuti.

## <a name="create-an-rbac-binding"></a>Creare un'associazione RBAC

> [!NOTE]
> Il nome dell'associazione del ruolo cluster fa distinzione tra maiuscole e minuscole.

Prima di usare un account di Azure Active Directory con un cluster AKS, è necessario creare un'associazione di ruoli o un'associazione di ruoli del cluster. I ruoli definiscono le autorizzazioni da concedere e le associazioni le applicano agli utenti desiderati. Queste assegnazioni possono essere applicate a uno spazio dei nomi specifico o all'intero cluster. Per ulteriori informazioni, vedere [utilizzo dell'autorizzazione RBAC][rbac-authorization].

Usare prima di tutto il comando [AZ AKS Get-][az-aks-get-credentials] credentials `--admin` con l'argomento per accedere al cluster con accesso amministrativo.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Successivamente, creare ClusterRoleBinding per un account Azure AD a cui si vuole concedere l'accesso al cluster AKS. L'esempio seguente concede all'account l'accesso completo a tutti gli spazi dei nomi nel cluster:

- Se l'utente che si concede l'associazione RBAC per si trova nello stesso tenant Azure AD, assegnare le autorizzazioni in base al nome dell'entità utente (UPN). Passare al passaggio per creare il manifesto YAML per ClusterRoleBinding.

- Se l'utente si trova in un tenant Azure AD diverso, eseguire una query per e utilizzare la proprietà **ObjectID** . Se necessario, ottenere il valore objectId dell'account utente richiesto usando il comando [AZ ad User Show][az-ad-user-show] . Specificare il nome dell'entità utente (UPN) dell'account necessario:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Creare un file, ad esempio *RBAC-AAD-User. YAML*, quindi incollare il contenuto seguente. Nell'ultima riga sostituire **userPrincipalName_or_objectId** con l'UPN o l'ID oggetto. La scelta varia a seconda che l'utente sia lo stesso Azure AD tenant.

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

Applicare il binding usando il comando [kubectl Apply][kubectl-apply] come illustrato nell'esempio seguente:

```console
kubectl apply -f rbac-aad-user.yaml
```

È anche possibile creare un'associazione di ruolo per tutti i membri di un gruppo di Azure AD. I gruppi di Azure AD vengono specificati utilizzando l'ID oggetto gruppo, come illustrato nell'esempio seguente.

Creare un file, ad esempio *RBAC-AAD-Group. YAML*, quindi incollare il contenuto seguente. Aggiornare l'ID oggetto del gruppo con un ID dal tenant di Azure AD:

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

Applicare il binding usando il comando [kubectl Apply][kubectl-apply] come illustrato nell'esempio seguente:

```console
kubectl apply -f rbac-aad-group.yaml
```

Per altre informazioni sulla protezione di un cluster Kubernetes con RBAC, vedere [uso dell'autorizzazione RBAC][rbac-authorization].

## <a name="access-the-cluster-with-azure-ad"></a>Accedere al cluster con Azure AD

Estrarre il contesto per l'utente non amministratore usando il comando [AZ AKS Get-credentials][az-aks-get-credentials] .

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Dopo aver eseguito il `kubectl` comando, verrà richiesto di eseguire l'autenticazione tramite Azure. Seguire le istruzioni visualizzate per completare il processo, come illustrato nell'esempio seguente:

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

Al termine del processo, il token di autenticazione viene memorizzato nella cache. Viene richiesto di eseguire di nuovo l'accesso solo quando il token scade oppure il file di configurazione Kubernetes viene ricreato.

Se viene visualizzato un messaggio di errore di autorizzazione dopo aver eseguito correttamente l'accesso, verificare i criteri seguenti:

```console
error: You must be logged in to the server (Unauthorized)
```


- È stato definito l'ID oggetto o UPN appropriato, a seconda che l'account utente si trovi nello stesso tenant Azure AD o meno.
- L'utente non è membro di più di 200 gruppi.
- Il segreto definito nella registrazione dell'applicazione per il server corrisponde al valore configurato tramite `--aad-server-app-secret`.

## <a name="next-steps"></a>Passaggi successivi

Per usare Azure AD utenti e gruppi per controllare l'accesso alle risorse del cluster, vedere [controllare l'accesso alle risorse del cluster usando il controllo degli accessi in base al ruolo e le identità di Azure ad in AKS][azure-ad-rbac].

Per altre informazioni su come proteggere i cluster Kubernetes, vedere [Opzioni di accesso e identità per AKS][rbac-authorization].

Per altre informazioni sull'identità e sul controllo delle risorse, vedere [procedure consigliate per l'autenticazione e l'autorizzazione in AKS][operator-best-practices-identity].

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
