---
title: Usare i criteri di sicurezza dei pod nel servizio Azure Kubernetes (AKS)Use pod security policies in Azure Kubernetes Service (AKS)
description: Informazioni su come controllare le ammissioni di pod usando PodSecurityPolicy nel servizio Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 04/08/2020
ms.openlocfilehash: 9e3a17e4775150247ef7924dffec68cc86a0bcac
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998348"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Anteprima: proteggere il cluster usando i criteri di sicurezza dei pod nel servizio Azure Kubernetes (AKS)Preview - Secure your cluster using pod security policies in Azure Kubernetes Service (AKS)

Per migliorare la sicurezza del cluster AKS, è possibile limitare i pod che possono essere pianificati. I pod che richiedono risorse non consentiti non possono essere eseguiti nel cluster AKS. Questo accesso viene definito utilizzando i criteri di sicurezza dei pod. Questo articolo illustra come usare i criteri di sicurezza dei pod per limitare la distribuzione dei pod in AKS.

> [!IMPORTANT]
> Le funzionalità di anteprima di AKS sono l'opt-in self-service. Le anteprime vengono fornite "così come sono" e "come disponibili" e sono escluse dai contratti di servizio e dalla garanzia limitata. Le anteprime AKS sono parzialmente coperte dal supporto clienti in base al massimo sforzo. Di conseguenza, queste funzionalità non sono destinate all'uso in produzione. Per ulteriori informazioni, vedere i seguenti articoli di supporto:
>
> * [Criteri di supporto AKS][aks-support-policies]
> * [Domande frequenti relative al supporto tecnico Azure][aks-faq]

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. Se è necessario un cluster servizio Azure Kubernetes, vedere la Guida introduttiva su servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

È necessaria l'interfaccia della riga di comando di Azure versione 2.0.61 o successiva installata e configurata. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere  [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga comando di aks-preview

Per utilizzare i criteri di sicurezza dei pod, è necessaria l'estensione *aks-preview* CLI versione 0.4.1 o successiva. Installare l'estensione aks-preview di Azure CLI usando il comando [az extension add,][az-extension-add] quindi verificare la presenza di eventuali aggiornamenti disponibili usando il comando [az extension update:Install][az-extension-update] the *aks-preview* Azure CLI extension using the az extension add command, then check for any available updates using the az extension update command:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Registrare il provider di funzionalità dei criteri di sicurezza del podRegister pod security policy feature provider

Per creare o aggiornare un cluster AKS per l'utilizzo di criteri di sicurezza dei pod, abilitare innanzitutto un flag di funzionalità nella sottoscrizione. Per registrare il flag di funzionalità *PodSecurityPolicyPreview,* utilizzare il comando [az feature register][az-feature-register] come illustrato nell'esempio seguente:

> [!CAUTION]
> Quando si registra una funzionalità in un abbonamento, al momento non è possibile annullare la registrazione di tale funzionalità. Dopo aver abilitato alcune funzionalità di anteprima, le impostazioni predefinite possono essere utilizzate per tutti i cluster AKS quindi creati nella sottoscrizione. Non abilitare le funzionalità di anteprima nelle sottoscrizioni di produzione. Usare un abbonamento separato per testare le funzionalità di anteprima e raccogliere commenti e suggerimenti.

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. È possibile controllare lo stato di registrazione usando il comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione del provider di risorse *Microsoft.ContainerService* usando il comando [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Panoramica dei criteri di sicurezza dei pod

In un cluster Kubernetes, un controller di ammissione viene utilizzato per intercettare le richieste al server API quando deve essere creata una risorsa. Il controller di ammissione può quindi *convalidare* la richiesta di risorse in base a un set di regole o *modificare* la risorsa per modificare i parametri di distribuzione.

*PodSecurityPolicy* è un controller di ammissione che convalida una specifica del pod soddisfa i requisiti definiti. Questi requisiti possono limitare l'uso di contenitori con privilegi, l'accesso a determinati tipi di archiviazione o l'utente o il gruppo con cui il contenitore può essere eseguito. Quando si tenta di distribuire una risorsa in cui le specifiche del pod non soddisfano i requisiti descritti nei criteri di sicurezza del pod, la richiesta viene negata. Questa possibilità di controllare quali pod possono essere pianificati nel cluster AKS impedisce alcune possibili vulnerabilità di sicurezza o escalation dei privilegi.

Quando si abilitano i criteri di sicurezza dei pod in un cluster AKS, vengono applicati alcuni criteri predefiniti. Questi criteri predefiniti offrono un'esperienza predefinita per definire quali pod possono essere pianificati. Tuttavia, gli utenti del cluster possono incorrere in problemi di distribuzione dei pod fino a quando non si definiscono i propri criteri. L'approccio consigliato è:

* Creare un cluster AKS
* Definire criteri di sicurezza dei pod personalizzati
* Abilitare la funzionalità dei criteri di sicurezza dei pod

Per mostrare come i criteri predefiniti limitano le distribuzioni di pod, in questo articolo viene innanzitutto abilitata la funzionalità dei criteri di sicurezza dei pod, quindi viene creato un criterio personalizzato.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Abilitare i criteri di sicurezza dei pod in un cluster AKSEnable pod security policy on an AKS cluster

È possibile abilitare o disabilitare i criteri di sicurezza dei pod utilizzando il comando [az aks update.][az-aks-update] L'esempio seguente abilita i criteri di sicurezza dei pod sul nome del cluster *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*.

> [!NOTE]
> Per l'uso reale, non abilitare i criteri di sicurezza dei pod finché non sono stati definiti criteri personalizzati. In questo articolo vengono abilitati i criteri di sicurezza dei pod come primo passaggio per vedere come vengono distribuzionate le distribuzioni dei pod dei criteri predefiniti.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Criteri AKS predefiniti

Quando si abilitano i criteri di sicurezza dei pod, AKS crea un criterio predefinito denominato *privileged*. Non modificare o rimuovere il criterio predefinito. Creare invece criteri personalizzati che definiscono le impostazioni che si desidera controllare. Diamo innanzitutto un'occhiata a quali sono questi criteri predefiniti come influiscono sulle distribuzioni di pod.

Per visualizzare i criteri disponibili, utilizzare il comando [kubectl get psp,][kubectl-get] come illustrato nell'esempio seguente

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

I criteri di sicurezza del pod *con privilegi* vengono applicati a qualsiasi utente autenticato nel cluster AKS. Questa assegnazione è controllata da ClusterRoles e ClusterRoleBindings.This assignment is controlled by ClusterRoles and ClusterRoleBindings. Usare il comando [kubectl get rolebindings][kubectl-get] e cercare *default:privileged:* binding nello spazio dei nomi *kube-system:*

```console
kubectl get rolebindings default:privileged -n kube-system -o yaml
```

Come illustrato nel seguente output condensato, *il psp:restricted* ClusterRole viene assegnato a tutti gli utenti *di sistema:autenticati.* Questa capacità fornisce un livello di base di restrizioni senza che vengano definite le proprie politiche.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  [...]
  name: default:privileged
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:privileged
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:masters
```

È importante comprendere in che modo questi criteri predefiniti interagiscono con le richieste degli utenti per pianificare i pod prima di iniziare a creare criteri di sicurezza dei pod personalizzati. Nelle sezioni successive, programmiamo alcuni pod per vedere questi criteri predefiniti in azione.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Creare un utente di test in un cluster AKSCreate a test user in an AKS cluster

Per impostazione predefinita, quando si usa il comando [az aks get-credentials,][az-aks-get-credentials] `kubectl` le credenziali di *amministratore* per il cluster AKS vengono aggiunte alla configurazione. L'utente amministratore ignora l'applicazione dei criteri di sicurezza dei pod. Se si usa l'integrazione di Azure Active Directory per i cluster AKS, è possibile accedere con le credenziali di un utente non amministratore per visualizzare l'applicazione dei criteri in azione. In questo articolo viene creato un account utente di test nel cluster AKS che è possibile usare.

Creare uno spazio dei nomi di esempio denominato *psp-aks* per le risorse di test usando il comando [kubectl create namespace.][kubectl-create] Creare quindi un account di servizio denominato non admin-user utilizzando il comando kubectl create serviceaccount:Then, create a service account named *nonadmin-user* using the [kubectl create serviceaccount][kubectl-create] command:

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Successivamente, creare un RoleBinding per l'utente non admin per eseguire azioni di base nello spazio dei nomi utilizzando il comando kubectl create rolebinding:Next, create a RoleBinding for the *nonadmin-user* to perform basic actions in the namespace using the [kubectl create rolebinding][kubectl-create] command:

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Creare comandi alias per l'amministratore e l'utente non amministratoreCreate alias commands for admin and non-admin user

Per evidenziare la differenza tra l'utente amministratore normale durante l'utilizzo `kubectl` e l'utente non amministratore creato nei passaggi precedenti, creare due alias della riga di comando:

* L'alias **kubectl-admin** è per l'utente amministratore normale ed ha come ambito lo spazio dei nomi *psp-aks.*
* L'alias **kubectl-nonadminuser** è per *l'utente non admin* creato nel passaggio precedente ed ha come ambito lo spazio dei nomi *psp-aks.*

Creare questi due alias come illustrato nei seguenti comandi:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Testare la creazione di un pod con privilegi

Esaminiamo innanzitutto cosa succede quando si pianifica un `privileged: true`pod con il contesto di sicurezza di . Questo contesto di sicurezza aumenta i privilegi del contenitore. Nella sezione precedente che mostrava i criteri di sicurezza del pod AKS predefiniti, i criteri *limitati* devono negare questa richiesta.

Creare un `nginx-privileged.yaml` file denominato e incollare il seguente manifesto YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: nginx:1.14.2
      securityContext:
        privileged: true
```

Create il contenitore utilizzando il comando [kubectl apply][kubectl-apply] e specificate il nome del file YAML:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

Il pod non viene pianificato, come illustrato nell'output di esempio seguente:The pod fails to be scheduled, as shown in the following example output:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: []
```

Il pod non raggiunge la fase di programmazione, quindi non ci sono risorse da eliminare prima di andare avanti.

## <a name="test-creation-of-an-unprivileged-pod"></a>Testare la creazione di un contenitore senza privilegi

Nell'esempio precedente, la specifica del pod ha richiesto un'escalation con privilegi. Questa richiesta viene negata dai criteri di sicurezza del pod *con restrizioni* di default, pertanto il pod non viene pianificato. Proviamo ora a eseguire lo stesso pod NGINX senza la richiesta di escalation dei privilegi.

Creare un `nginx-unprivileged.yaml` file denominato e incollare il seguente manifesto YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
```

Create il contenitore utilizzando il comando [kubectl apply][kubectl-apply] e specificate il nome del file YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Il pod non viene pianificato, come illustrato nell'output di esempio seguente:The pod fails to be scheduled, as shown in the following example output:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged.yaml": pods "nginx-unprivileged" is forbidden: unable to validate against any pod security policy: []
```

Il pod non raggiunge la fase di programmazione, quindi non ci sono risorse da eliminare prima di andare avanti.

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Testare la creazione di un pod con un contesto utente specifico

Nell'esempio precedente, l'immagine del contenitore ha tentato automaticamente di usare root per associare NGINX alla porta 80.In the previous example, the container image automatically tried to use root to bind NGINX to port 80. Questa richiesta è stata negata dai criteri di sicurezza del pod *con restrizioni* predefiniti, pertanto il pod non viene avviato. Proviamo ora a eseguire lo stesso pod NGINX con `runAsUser: 2000`un contesto utente specifico, ad esempio .

Creare un `nginx-unprivileged-nonroot.yaml` file denominato e incollare il seguente manifesto YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
      securityContext:
        runAsUser: 2000
```

Create il contenitore utilizzando il comando [kubectl apply][kubectl-apply] e specificate il nome del file YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

Il pod non viene pianificato, come illustrato nell'output di esempio seguente:The pod fails to be scheduled, as shown in the following example output:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged-nonroot.yaml": pods "nginx-unprivileged-nonroot" is forbidden: unable to validate against any pod security policy: []
```

Il pod non raggiunge la fase di programmazione, quindi non ci sono risorse da eliminare prima di andare avanti.

## <a name="create-a-custom-pod-security-policy"></a>Creare criteri di sicurezza dei pod personalizzatiCreate a custom pod security policy

Ora che hai visto il comportamento dei criteri di sicurezza del pod predefiniti, ti permettiamo *all'utente non amministratore* di pianificare correttamente i pod.

Creare un criterio per rifiutare i pod che richiedono l'accesso con privilegi. Altre opzioni, ad esempio *runAsUser* o *i volumi*consentiti, non sono limitate in modo esplicito. Questo tipo di criterio nega una richiesta di accesso con privilegi, ma consente in caso contrario al cluster di eseguire i pod richiesti.

Creare un `psp-deny-privileged.yaml` file denominato e incollare il seguente manifesto YAML:

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: psp-deny-privileged
spec:
  privileged: false
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  runAsUser:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  volumes:
  - '*'
```

Creare i criteri utilizzando il comando [kubectl apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Per visualizzare i criteri disponibili, utilizzare il comando [kubectl get psp,][kubectl-get] come illustrato nell'esempio seguente. Confrontare il criterio *psp-deny-privileged* con il criterio *con restrizioni* predefinito applicato negli esempi precedenti per creare un pod. Solo l'utilizzo dell'escalation *PRIV* è negato dalla politica. Non esistono restrizioni sull'utente o sul gruppo per il criterio *psp-deny-privileged.*

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Consenti all'account utente di utilizzare i criteri di sicurezza del pod personalizzato

Nel passaggio precedente è stato creato un criterio di sicurezza del pod per rifiutare i pod che richiedono l'accesso con privilegi. Per consentire l'utilizzo del criterio, creare un *ruolo* o un *ClusterRole*. Quindi, associare uno di questi ruoli utilizzando un *RoleBinding* o *ClusterRoleBinding*.

Per questo esempio, creare un ClusterRole che consente di *utilizzare* il criterio *psp-deny-privileged* creato nel passaggio precedente. Creare un `psp-deny-privileged-clusterrole.yaml` file denominato e incollare il seguente manifesto YAML:

```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: psp-deny-privileged-clusterrole
rules:
- apiGroups:
  - extensions
  resources:
  - podsecuritypolicies
  resourceNames:
  - psp-deny-privileged
  verbs:
  - use
```

Creare ClusterRole utilizzando il comando [kubectl apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Creare ora un ClusterRoleBinding per utilizzare il ClusterRole creato nel passaggio precedente. Creare un `psp-deny-privileged-clusterrolebinding.yaml` file denominato e incollare il seguente manifesto YAML:

```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: psp-deny-privileged-clusterrolebinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp-deny-privileged-clusterrole
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:serviceaccounts
```

Creare un ClusterRoleBinding utilizzando il comando [kubectl apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> Nel primo passaggio di questo articolo, la funzionalità dei criteri di sicurezza dei pod è stata abilitata nel cluster AKS. La procedura consigliata consisteva nell'abilitare la funzionalità dei criteri di sicurezza dei pod solo dopo aver definito i propri criteri. Questa è la fase in cui è necessario abilitare la funzionalità dei criteri di sicurezza dei pod. Sono stati definiti uno o più criteri personalizzati e gli account utente sono stati associati a tali criteri. Ora è possibile abilitare in modo sicuro la funzionalità dei criteri di sicurezza dei pod e ridurre al minimo i problemi causati dai criteri predefiniti.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Testare nuovamente la creazione di un pod senza privilegi

Con l'applicazione dei criteri di sicurezza del pod personalizzato e un'associazione per l'account utente per l'utilizzo dei criteri, proviamo a creare di nuovo un pod senza privilegi. Utilizzate lo `nginx-privileged.yaml` stesso manifesto per creare il pod utilizzando il comando [kubectl apply:][kubectl-apply]

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Il pod è pianificato correttamente. Quando controllate lo stato del contenitore utilizzando il comando [kubectl get pods,][kubectl-get] il pod è *Running*:

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

In questo esempio viene illustrato come creare criteri di sicurezza pod personalizzati per definire l'accesso al cluster AKS per utenti o gruppi diversi. I criteri AKS predefiniti forniscono controlli limitati sui pod che possono essere eseguiti, quindi crea criteri personalizzati per definire correttamente le restrizioni necessarie.

Eliminate il pod NGINX senza privilegi utilizzando il comando [kubectl delete][kubectl-delete] e specificate il nome del manifesto YAML:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per disabilitare i criteri di sicurezza dei pod, usate di nuovo il comando [az aks update.][az-aks-update] L'esempio seguente disabilita i criteri di sicurezza dei pod sul nome del cluster *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

Eliminare quindi ClusterRole e ClusterRoleBinding:Next, delete the ClusterRole and ClusterRoleBinding:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

Eliminare i criteri di sicurezza utilizzando il comando [kubectl delete][kubectl-delete] e specificare il nome del manifesto YAML:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Infine, eliminare lo spazio dei nomi *psp-aks:*

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come creare criteri di sicurezza dei pod per impedire l'utilizzo dell'accesso con privilegi. Esistono molte funzionalità che un criterio può applicare, ad esempio il tipo di volume o l'utente RunAs. Per ulteriori informazioni sulle opzioni disponibili, consultate i documenti di riferimento sui criteri di [sicurezza dei pod Kubernetes.][kubernetes-policy-reference]

Per ulteriori informazioni sulla limitazione del traffico di rete pod, consultate Proteggere il traffico tra pod utilizzando i criteri di [rete in AKS.][network-policies]

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubernetes-policy-reference]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#policy-reference

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policies]: use-network-policies.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
