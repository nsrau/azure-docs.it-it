---
title: Usare i criteri di sicurezza pod in Azure Kubernetes Service (AKS)
description: Informazioni su come controllare l'ammissione di pod usando PodSecurityPolicy in Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/17/2019
ms.author: mlearned
ms.openlocfilehash: 374e86409be08f1f9859b3e325dda57080b89dbf
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033998"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Anteprima-proteggere il cluster usando i criteri di sicurezza pod in Azure Kubernetes Service (AKS)

Per migliorare la sicurezza del cluster AKS, è possibile limitare i pod che è possibile pianificare. I pod che richiedono risorse non consentite non possono essere eseguiti nel cluster AKS. Questo accesso viene definito usando i criteri di sicurezza pod. Questo articolo illustra come usare i criteri di sicurezza di pod per limitare la distribuzione di pod in AKS.

> [!IMPORTANT]
> Le funzionalità di anteprima di AKS sono il consenso esplicito self-service. Le anteprime vengono fornite "così come sono" e "come disponibili" e sono escluse dai contratti di servizio e dalla garanzia limitata. Le anteprime AKS sono parzialmente coperte dal supporto tecnico per il massimo sforzo. Di conseguenza, queste funzionalità non sono destinate all'uso in produzione. Per ulteriori informazioni, vedere gli articoli di supporto seguenti:
>
> * [Criteri di supporto AKS][aks-support-policies]
> * [Domande frequenti relative al supporto tecnico Azure][aks-faq]

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster AKS esistente. Se è necessario un cluster AKS, vedere la Guida introduttiva di AKS [usando l'interfaccia della][aks-quickstart-cli] riga di comando di Azure o [l'portale di Azure][aks-quickstart-portal].

È necessaria l'interfaccia della riga di comando di Azure versione 2.0.61 o successiva installata e configurata. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [installare l'interfaccia][install-azure-cli]della riga di comando di Azure.

### <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga comando di aks-preview

Per usare i criteri di sicurezza di Pod, è necessaria l'estensione dell'interfaccia della riga di comando *AKS-Preview* 0.4.1 o versione successiva. Installare l'estensione dell'interfaccia della riga di comando di Azure *AKS-Preview* usando il comando [AZ Extension Add][az-extension-add] , quindi verificare la presenza di eventuali aggiornamenti disponibili usando il comando [AZ Extension Update][az-extension-update] ::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Registrare il provider di funzionalità dei criteri di sicurezza Pod

Per creare o aggiornare un cluster AKS per usare i criteri di sicurezza Pod, abilitare prima di tutto un flag funzionalità per la sottoscrizione. Per registrare il flag della funzionalità *PodSecurityPolicyPreview* , usare il comando [AZ feature Register][az-feature-register] , come illustrato nell'esempio seguente:

> [!CAUTION]
> Quando si registra una funzionalità in una sottoscrizione, attualmente non è possibile annullare la registrazione di tale funzionalità. Dopo aver abilitato alcune funzionalità di anteprima, è possibile usare i valori predefiniti per tutti i cluster AKS, quindi creati nella sottoscrizione. Non abilitare le funzionalità di anteprima nelle sottoscrizioni di produzione. Usare una sottoscrizione separata per testare le funzionalità di anteprima e raccogliere commenti e suggerimenti.

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. È possibile controllare lo stato della registrazione usando il comando [AZ feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione del provider di risorse *Microsoft. servizio contenitore* usando il comando [AZ provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Panoramica dei criteri di sicurezza di Pod

In un cluster Kubernetes, un controller di ammissione viene usato per intercettare le richieste al server API quando è necessario creare una risorsa. Il controller di ammissione può quindi convalidare la richiesta di risorse in base a un set di regole o *mutare* la risorsa per modificare i parametri di distribuzione.

*PodSecurityPolicy* è un controller di ammissione che convalida una specifica Pod soddisfa i requisiti definiti. Questi requisiti possono limitare l'uso di contenitori con privilegi, l'accesso a determinati tipi di archiviazione o l'utente o il gruppo con cui il contenitore può essere eseguito. Quando si tenta di distribuire una risorsa in cui le specifiche del Pod non soddisfano i requisiti indicati nei criteri di sicurezza Pod, la richiesta viene negata. Questa possibilità di controllare quali Pod è possibile pianificare nel cluster AKS impedisce alcune possibili vulnerabilità di sicurezza o escalation dei privilegi.

Quando si abilitano i criteri di sicurezza pod in un cluster AKS, vengono applicati alcuni criteri predefiniti. Questi criteri predefiniti offrono un'esperienza predefinita per definire quali Pod è possibile pianificare. Tuttavia, gli utenti del cluster potrebbero riscontrare problemi durante la distribuzione di pod fino a quando non si definiscono i propri criteri. L'approccio consigliato consiste nell'eseguire le operazioni seguenti:

* Creare un cluster del servizio Azure Container
* Definire i propri criteri di sicurezza Pod
* Abilitare la funzionalità dei criteri di sicurezza Pod

Per mostrare in che modo i criteri predefiniti limitano le distribuzioni Pod, in questo articolo viene prima abilitata la funzionalità criteri di sicurezza Pod, quindi si crea un criterio personalizzato.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Abilitare i criteri di sicurezza pod in un cluster AKS

È possibile abilitare o disabilitare i criteri di sicurezza pod usando il comando [AZ AKS Update][az-aks-update] . Nell'esempio seguente vengono abilitati i criteri di sicurezza Pod nel nome del cluster *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*.

> [!NOTE]
> Per usare il mondo reale, non abilitare i criteri di sicurezza pod fino a quando non sono stati definiti criteri personalizzati. In questo articolo vengono abilitati i criteri di sicurezza pod come primo passaggio per vedere come i criteri predefiniti limitano le distribuzioni pod.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Criteri AKS predefiniti

Quando si abilitano i criteri di sicurezza Pod, AKS crea due criteri predefiniti denominati privileged e Restricted. Non modificare o rimuovere questi criteri predefiniti. In alternativa, creare criteri personalizzati che definiscono le impostazioni che si desidera controllare. Per prima cosa si esaminano i criteri predefiniti che influiscano sulle distribuzioni pod.

Per visualizzare i criteri disponibili, usare il comando [kubectl Get PSP][kubectl-get] , come illustrato nell'esempio seguente. Nell'ambito dei criteri di *restrizione* predefiniti, all'utente viene negato l'uso di *priv* per l'escalation dei pod con privilegi e l'utente *MustRunAsNonRoot*.

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted   false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

Il criterio di sicurezza Pod con *restrizioni* viene applicato a qualsiasi utente autenticato nel cluster AKS. Questa assegnazione viene controllata da ClusterRoles e ClusterRoleBindings. Usare il comando [kubectl Get clusterrolebindings][kubectl-get] e cercare l' *impostazione predefinita:* Restricted: binding:

```console
kubectl get clusterrolebindings default:restricted -o yaml
```

Come illustrato nel seguente output condensato, il ClusterRole *PSP:* Restricted viene assegnato a qualsiasi *sistema: Authenticated* Users. Questa possibilità offre un livello di base per le restrizioni senza definire i propri criteri.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  [...]
  name: default:restricted
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:restricted
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:authenticated
```

È importante comprendere come questi criteri predefiniti interagiscono con le richieste degli utenti per pianificare i pod prima di iniziare a creare i propri criteri di sicurezza pod. Nelle prossime sezioni si pianificano alcuni pod per visualizzare questi criteri predefiniti in azione.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Creare un utente di test in un cluster AKS

Per impostazione predefinita, quando si usa il comando [AZ AKS Get-credentials][az-aks-get-credentials] , le credenziali di *amministratore* per il cluster AKS e `kubectl` aggiunte alla configurazione. L'utente amministratore ignora l'applicazione dei criteri di sicurezza pod. Se si usa Azure Active Directory integrazione per i cluster AKS, è possibile accedere con le credenziali di un utente non amministratore per vedere l'applicazione dei criteri in azione. In questo articolo viene creato un account utente di test nel cluster AKS che è possibile usare.

Creare uno spazio dei nomi di esempio denominato *PSP-AKS* per le risorse di test usando il comando [kubectl create namespace][kubectl-create] . Quindi, creare un account del servizio denominato *nonadmin-User* usando il comando [kubectl create ServiceAccount][kubectl-create] :

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Successivamente, creare un oggetto Roleing per l' *utente non amministratore* per eseguire le azioni di base nello spazio dei nomi usando il comando [kubectl create rolement][kubectl-create] :

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Creare comandi alias per utenti amministratori e non amministratori

Per evidenziare la differenza tra l'utente amministratore normale quando si `kubectl` USA e l'utente non amministratore creato nei passaggi precedenti, creare due alias della riga di comando:

* L'alias **kubectl-admin** è per l'utente amministratore normale e ha come ambito lo spazio dei nomi *PSP-AKS* .
* L'alias **kubectl-nonadminuser** è per l' *utente non amministratore* creato nel passaggio precedente e ha come ambito lo spazio dei nomi *PSP-AKS* .

Creare questi due alias, come illustrato nei comandi seguenti:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Testare la creazione di un pod con privilegi

Si contesterà innanzitutto cosa accade quando si pianifica un pod con il contesto di `privileged: true`sicurezza di. Questo contesto di sicurezza inoltra i privilegi del Pod. Nella sezione precedente sono stati illustrati i criteri di sicurezza di AKS Pod predefiniti, i criteri *limitati* dovrebbero negare questa richiesta.

Creare un file denominato `nginx-privileged.yaml` e incollare il manifesto YAML seguente:

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

Creare il pod usando il comando [kubectl Apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

Il Pod non viene pianificato, come illustrato nell'output di esempio seguente:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: [spec.containers[0].securityContext.privileged: Invalid value: true: Privileged containers are not allowed]
```

Il Pod non raggiunge la fase di pianificazione, quindi non ci sono risorse da eliminare prima di procedere.

## <a name="test-creation-of-an-unprivileged-pod"></a>Creazione di test di un pod senza privilegi

Nell'esempio precedente, la specifica Pod ha richiesto l'escalation dei privilegi. Questa richiesta viene negata dai criteri di sicurezza Pod con *restrizioni* predefiniti, pertanto non è possibile pianificare il pod. Provare ora a eseguire lo stesso pod NGINX senza la richiesta di escalation dei privilegi.

Creare un file denominato `nginx-unprivileged.yaml` e incollare il manifesto YAML seguente:

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

Creare il pod usando il comando [kubectl Apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

L'utilità di pianificazione Kubernetes accetta la richiesta pod. Tuttavia, se si esamina lo stato del pod usando `kubectl get pods`, si verifica un errore:

```console
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS                       RESTARTS   AGE
nginx-unprivileged   0/1     CreateContainerConfigError   0          26s
```

Usare il comando [Pod kubectl descrivere][kubectl-describe] per esaminare gli eventi per il pod. Nell'esempio seguente viene illustrato il contenitore e l'immagine che richiedono le autorizzazioni radice, anche se non sono state richieste:

```console
$ kubectl-nonadminuser describe pod nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                     From                               Message
  ----     ------     ----                    ----                               -------
  Normal   Scheduled  7m14s                   default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged to aks-agentpool-34777077-0
  Warning  Failed     5m2s (x12 over 7m13s)   kubelet, aks-agentpool-34777077-0  Error: container has runAsNonRoot and image will run as root
  Normal   Pulled     2m10s (x25 over 7m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
```

Anche se non è stato richiesto alcun accesso con privilegi, l'immagine del contenitore per NGINX deve creare un'associazione per la porta *80*. Per associare le porte *1024* e precedenti, l'utente *root* è obbligatorio. Quando il Pod tenta di avviarsi, i criteri di sicurezza Pod *limitati* negano questa richiesta.

Questo esempio mostra che i criteri di sicurezza Pod predefiniti creati da AKS sono attivi e limitano le azioni che un utente può eseguire. È importante comprendere il comportamento di questi criteri predefiniti, perché potrebbe non essere necessario negare un pod di base NGINX.

Prima di procedere al passaggio successivo, eliminare il pod di test usando il comando [kubectl Delete Pod][kubectl-delete] :

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Testare la creazione di un pod con un contesto utente specifico

Nell'esempio precedente, l'immagine del contenitore ha provato automaticamente a usare la radice per associare NGINX alla porta 80. Questa richiesta è stata negata dai criteri di sicurezza Pod con *restrizioni* predefiniti, quindi il Pod non viene avviato. Provare ora a eseguire lo stesso pod NGINX con un contesto utente specifico, ad esempio `runAsUser: 2000`.

Creare un file denominato `nginx-unprivileged-nonroot.yaml` e incollare il manifesto YAML seguente:

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

Creare il pod usando il comando [kubectl Apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

L'utilità di pianificazione Kubernetes accetta la richiesta pod. Tuttavia, se si esamina lo stato del pod usando `kubectl get pods`, si verifica un errore diverso rispetto all'esempio precedente:

```console
$ kubectl-nonadminuser get pods

NAME                         READY   STATUS              RESTARTS   AGE
nginx-unprivileged-nonroot   0/1     CrashLoopBackOff    1          3s
```

Usare il comando [Pod kubectl descrivere][kubectl-describe] per esaminare gli eventi per il pod. L'esempio ridotto seguente Mostra gli eventi pod:

```console
$ kubectl-nonadminuser describe pods nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                   From                               Message
  ----     ------     ----                  ----                               -------
  Normal   Scheduled  2m14s                 default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged-nonroot to aks-agentpool-34777077-0
  Normal   Pulled     118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
  Normal   Created    118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Created container
  Normal   Started    118s (x3 over 2m12s)  kubelet, aks-agentpool-34777077-0  Started container
  Warning  BackOff    105s (x5 over 2m11s)  kubelet, aks-agentpool-34777077-0  Back-off restarting failed container
```

Gli eventi indicano che il contenitore è stato creato e avviato. Non c'è niente di evidente come il motivo per cui il Pod si trova in uno stato di errore. Esaminiamo i log di pod usando il comando [kubectl logs][kubectl-logs] :

```console
kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous
```

L'output del log di esempio seguente indica che all'interno della configurazione di NGINX si verifica un errore di autorizzazione quando il servizio tenta di avviarsi. Questo errore si verifica di nuovo perché è necessario eseguire l'associazione alla porta 80. Sebbene la specifica Pod abbia definito un account utente normale, questo account utente non è sufficiente a livello di sistema operativo perché il servizio NGINX venga avviato e associato alla porta con restrizioni.

```console
$ kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous

2019/03/28 22:38:29 [warn] 1#1: the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
nginx: [warn] the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
2019/03/28 22:38:29 [emerg] 1#1: mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
nginx: [emerg] mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
```

Anche in questo caso, è importante comprendere il comportamento dei criteri di sicurezza di Pod predefiniti. Questo errore è stato un po' più difficile da tenere traccia e anche in questo caso potrebbe non essere previsto che venga negato un pod di base NGINX.

Prima di procedere al passaggio successivo, eliminare il pod di test usando il comando [kubectl Delete Pod][kubectl-delete] :

```console
kubectl-nonadminuser delete -f nginx-unprivileged-nonroot.yaml
```

## <a name="create-a-custom-pod-security-policy"></a>Creare un criterio di sicurezza Pod personalizzato

Ora che è stato visto il comportamento dei criteri di sicurezza di Pod predefiniti, è possibile consentire all'utente non *amministratore* di pianificare correttamente i pod.

Creiamo un criterio per rifiutare i pod che richiedono l'accesso con privilegi. Altre opzioni, ad esempio *runAsUser* o *volumi*consentiti, non sono esplicitamente limitate. Questo tipo di Criteri nega una richiesta di accesso con privilegi, ma in caso contrario consente al cluster di eseguire i pod richiesti.

Creare un file denominato `psp-deny-privileged.yaml` e incollare il manifesto YAML seguente:

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

Creare il criterio usando il comando [kubectl Apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Per visualizzare i criteri disponibili, usare il comando [kubectl Get PSP][kubectl-get] , come illustrato nell'esempio seguente. Confrontare il criterio con *privilegi per PSP-Deny* con i criteri di *restrizione* predefiniti applicati negli esempi precedenti per creare un pod. Solo l'uso dell'escalation di *priv* viene negato dal criterio. Non sono previste restrizioni per l'utente o il gruppo per i criteri con *privilegi PSP-Deny-* .

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted            false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Consenti all'account utente di usare i criteri di sicurezza Pod personalizzati

Nel passaggio precedente sono stati creati criteri di sicurezza pod per rifiutare i pod che richiedono l'accesso con privilegi. Per consentire l'utilizzo del criterio, è necessario creare un *ruolo* o un *ClusterRole*. Quindi, associare uno di questi ruoli utilizzando un oggetto *roleName* o *ClusterRoleBinding*.

Per questo esempio, creare un ClusterRole che consenta di *usare* il criterio con *privilegi PSP-Deny* creato nel passaggio precedente. Creare un file denominato `psp-deny-privileged-clusterrole.yaml` e incollare il manifesto YAML seguente:

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

Creare il ClusterRole usando il comando [kubectl Apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

A questo punto, creare un ClusterRoleBinding per usare il ClusterRole creato nel passaggio precedente. Creare un file denominato `psp-deny-privileged-clusterrolebinding.yaml` e incollare il manifesto YAML seguente:

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

Creare una ClusterRoleBinding usando il comando [kubectl Apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> Nel primo passaggio di questo articolo, la funzionalità dei criteri di sicurezza Pod è stata abilitata nel cluster AKS. La procedura consigliata consiste nell'abilitare la funzionalità dei criteri di sicurezza pod solo dopo aver definito i propri criteri. Questa è la fase in cui è possibile abilitare la funzionalità dei criteri di sicurezza pod. Sono stati definiti uno o più criteri personalizzati e gli account utente sono stati associati a tali criteri. A questo punto è possibile controllare in modo sicuro la funzionalità dei criteri di sicurezza di Pod e ridurre al minimo i problemi causati dai criteri predefiniti.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Testare di nuovo la creazione di un pod senza privilegi

Con i criteri di sicurezza personalizzati di Pod applicati e un binding per l'account utente per l'uso del criterio, provare a creare di nuovo un pod senza privilegi. Usare lo stesso `nginx-privileged.yaml` manifesto per creare il pod usando il comando [kubectl Apply][kubectl-apply] :

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

La pianificazione del Pod è stata completata. Quando si controlla lo stato del pod usando il comando [kubectl Get][kubectl-get] pods, il Pod è *in esecuzione*:

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

Questo esempio illustra come creare criteri di sicurezza Pod personalizzati per definire l'accesso al cluster AKS per utenti o gruppi diversi. I criteri AKS predefiniti forniscono controlli rigidi sui pod che è possibile eseguire, quindi creare criteri personalizzati per definire correttamente le restrizioni necessarie.

Eliminare il Pod senza privilegi NGINX usando il comando [kubectl Delete][kubectl-delete] e specificare il nome del manifesto YAML:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per disabilitare i criteri di sicurezza di Pod, usare di nuovo il comando [AZ AKS Update][az-aks-update] . Nell'esempio seguente vengono disabilitati i criteri di sicurezza Pod nel nome del cluster *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

Eliminare quindi ClusterRole e ClusterRoleBinding:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

Eliminare i criteri di rete usando il comando [kubectl Delete][kubectl-delete] e specificare il nome del manifesto YAML:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Eliminare infine lo spazio dei nomi *PSP-AKS* :

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato come creare un criterio di sicurezza pod per impedire l'uso dell'accesso con privilegi. È possibile applicare un criterio a numerose funzionalità, ad esempio il tipo di volume o l'utente RunAs. Per altre informazioni sulle opzioni disponibili, vedere la documentazione di riferimento per i [criteri di sicurezza Pod Kubernetes][kubernetes-policy-reference].

Per altre informazioni sulla limitazione del traffico di rete Pod, vedere [proteggere il traffico tra i pod usando i criteri di rete in AKS][network-policies].

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
