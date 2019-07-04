---
title: Usare i criteri di sicurezza pod in Azure Kubernetes Service (AKS)
description: Informazioni su come controllare l'ufficio ammissioni pod usando PodSecurityPolicy in Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/17/2019
ms.author: iainfou
ms.openlocfilehash: 9da722006651cfc9e9f2a175d5c330ba5df08123
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447076"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Anteprima - proteggere il cluster usando criteri di sicurezza pod in Azure Kubernetes Service (AKS)

Per migliorare la sicurezza del cluster servizio contenitore di AZURE, è possibile limitare i POD che possono essere pianificati. I POD che richiedono le risorse che non si consente non è possibile eseguire nel cluster AKS. È possibile definire l'accesso tramite i criteri di sicurezza pod. Questo articolo illustra come usare i criteri di sicurezza pod per limitare la distribuzione di POD nel servizio contenitore di AZURE.

> [!IMPORTANT]
> Funzionalità di anteprima del servizio contenitore di AZURE sono self-service, fornire il consenso esplicito. Vengono fornite per raccogliere commenti e suggerimenti e bug dalla community. In fase di anteprima, queste funzionalità non sono destinate all'uso di produzione. Le funzionalità in anteprima pubblica rientrano nel supporto "best effort". Assistenza dai team di supporto tecnico di AKS è disponibile durante le ore lavorative Pacifico (PST) solo timezone. Per altre informazioni, vedere i seguenti articoli di supporto:
>
> * [Criteri di supporto servizio contenitore di AZURE][aks-support-policies]
> * [Domande frequenti relative al supporto tecnico Azure][aks-faq]

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster AKS esistente. Se è necessario un cluster del servizio contenitore di AZURE, vedere la Guida introduttiva AKS [tramite la CLI di Azure][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal].

È necessario la CLI di Azure versione 2.0.61 o versione successiva installato e configurato. Eseguire  `az --version` per trovare la versione. Se è necessario installare o eseguire l'aggiornamento, vedere [installare CLI Azure][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga comando di aks-preview

Per usare i criteri di sicurezza pod, è necessario il *aks-preview* CLI versione dell'estensione 0.4.1 o versione successiva. Installare il *aks-preview* estensione di comando di Azure usando la [Aggiungi estensione az][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] comando::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Registra provider di funzionalità dei criteri di sicurezza pod

Per creare o aggiornare un cluster servizio contenitore di AZURE per usare i criteri di sicurezza pod, è necessario attivare un flag funzionalità per la sottoscrizione. Per registrare il *PodSecurityPolicyPreview* flag delle funzionalità, utilizzare il [register funzionalità az][az-feature-register] seguente come mostrato nell'esempio seguente:

> [!CAUTION]
> Quando si registra una funzionalità in una sottoscrizione, attualmente non è possibile annullare la registrazione tale funzionalità. Dopo aver abilitato alcune funzionalità di anteprima, potrebbero essere utilizzati i valori predefiniti per tutti i cluster AKS quindi creati nella sottoscrizione. Non abilitare la funzionalità di anteprima nella sottoscrizione di produzione. Usare una sottoscrizione separata per le funzionalità di anteprima di test e raccogliere commenti e suggerimenti.

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. È possibile controllarne lo stato di registrazione usando il [elenco delle funzionalità az][az-feature-list] comando:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione dei *containerservice* provider di risorse usando la [register di az provider][az-provider-register] comando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Panoramica dei criteri di sicurezza pod

In un cluster Kubernetes, un controller di ammissione viene usato per intercettare le richieste per il server dell'API quando è necessario creare una risorsa. Il controller di ricovero può quindi *convalidare* la richiesta di risorse rispetto a un set di regole, o *mutare* la risorsa per modificare i parametri di distribuzione.

*PodSecurityPolicy* è un controller di ricovero che convalida una specifica del pod soddisfi i requisiti definiti. Questi requisiti possono limitare l'utilizzo di contenitori con privilegi, l'accesso a determinati tipi di archiviazione, oppure l'utente o gruppo che contenitore possono essere eseguiti come. Quando si tenta di distribuire una risorsa in cui le specifiche del pod non soddisfano i requisiti descritti nei criteri di sicurezza pod, la richiesta viene negata. Questo consente di controllare i POD che possono essere pianificati in, il servizio contenitore di AZURE in cluster impedisce alcune vulnerabilità di sicurezza o intensificarsi di privilegi.

Quando si abilitano criteri di sicurezza pod in un cluster AKS, alcuni criteri predefiniti vengono applicati. Questi criteri predefiniti forniscono un'esperienza di out-of-the-box per definire cosa POD può essere pianificata. Utenti del cluster, tuttavia, potrebbero verificarsi problemi relativi alla distribuzione di POD fino a quando non si definiscono i propri criteri. L'approccio consigliato consiste nel:

* Creare un cluster del servizio Azure Container
* Definire i propri criteri di sicurezza pod
* Abilitare la funzionalità di criteri di sicurezza pod

Per mostrare come criteri predefiniti di limitano le distribuzioni di pod, in questo articolo è abilitare innanzitutto la funzionalità di criteri di sicurezza pod, quindi creare un criterio personalizzato.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Abilitare i criteri di sicurezza pod in un cluster servizio contenitore di AZURE

È possibile abilitare o disabilitare i criteri di sicurezza pod tramite il [az aks aggiornare][az-aks-update] comando. I seguenti esempio abilita pod criteri di sicurezza sul nome del cluster *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*.

> [!NOTE]
> Per l'uso di scenari reali, non abilitare i criteri di sicurezza pod fino a quando non sono stati definiti criteri personalizzati. In questo articolo, si abilitano criteri di sicurezza pod come primo passaggio per visualizzare come criteri predefiniti di limitano i pod le distribuzioni.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Criteri predefiniti di AKS

Quando si abilitano criteri di sicurezza pod, servizio contenitore di AZURE crea due criteri predefinito denominati *con privilegi* e *limitato*. Non modificare o rimuovere questi criteri predefiniti. In alternativa, creare i propri criteri che definiscono le impostazioni desiderate al controllo. È possibile iniziare a esaminare quali questi criteri predefiniti sono impatto che hanno le distribuzioni di pod.

Per visualizzare i criteri disponibili, usare il [kubectl get psp][kubectl-get] comando, come illustrato nell'esempio seguente. Come parte del valore predefinito *limitato* dei criteri, all'utente viene negato *PRIV* usare per l'escalation dei privilegi pod e l'utente *MustRunAsNonRoot*.

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted   false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

Il *limitato* criteri di sicurezza pod viene applicato a qualsiasi utente autenticato nel cluster AKS. Questa assegnazione è controllata dalla ClusterRoles e ClusterRoleBindings. Usare la [kubectl get clusterrolebindings][kubectl-get] comandi e cercare il *predefinito: limitate:* associazione:

```console
kubectl get clusterrolebindings default:restricted -o yaml
```

Come illustrato nell'output sintetico seguente, il *punto di servizio PXE: limitato* ClusterRole viene assegnato a qualsiasi *system: autenticato* agli utenti. Questa funzionalità offre un livello base di restrizioni senza i propri criteri da definire.

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

È importante comprendere l'interagiscono con le richieste degli utenti per pianificare i POD prima di iniziare a creare i criteri di sicurezza personalizzati pod questi criteri predefiniti. Nelle sezioni successive, è possibile pianificare un pod per visualizzare questi criteri predefiniti in azione.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Creare un utente test in un cluster AKS

Per impostazione predefinita, quando si usa la [az aks get-credentials][az-aks-get-credentials] comando, il *admin* le credenziali per il cluster AKS e aggiunto al `kubectl` config. L'utente amministratore consente di ignorare l'imposizione dei criteri di sicurezza pod. Se si utilizza l'integrazione di Azure Active Directory per i cluster servizio contenitore di AZURE, è possibile accedere con le credenziali di un utente senza privilegi di amministratore per visualizzare l'applicazione dei criteri in azione. In questo articolo, è possibile creare un account utente di test nel cluster AKS che è possibile usare.

Creare uno spazio dei nomi di campione denominato *punto di servizio PXE-aks* per le risorse di test usando la [kubectl creare lo spazio dei nomi][kubectl-create] comando. Quindi, creare un account di servizio denominato *utente nonadmin* usando la [kubectl create serviceaccount][kubectl-create] comando:

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Creare quindi un RoleBinding per il *nonadmin-utente* per eseguire le azioni di base dello spazio dei nomi usando la [kubectl create rolebinding][kubectl-create] comando:

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Creare i comandi alias per l'utente amministratore e utente non amministratore

Per evidenziare la differenza tra l'utente di amministrazione di normali quando si usa `kubectl` e l'utente non amministratore creato nei passaggi precedenti, creare due alias della riga di comando:

* Il **kubectl-admin** alias sia per l'utente di amministrazione di normali e ha come ambito il *punto di servizio PXE-aks* dello spazio dei nomi.
* Il **kubectl nonadminuser** alias è per il *nonadmin utente* creato nel passaggio precedente e ha come ambito il *punto di servizio PXE-aks* dello spazio dei nomi.

Creare questi due alias, come illustrato nei comandi seguenti:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Testare la creazione di un pod con privilegi

È possibile prima di tutto verificare cosa accade quando si pianifica un pod con il contesto di sicurezza di `privileged: true`. In questo contesto di sicurezza esegue l'escalation dei privilegi del pod. Nella sezione precedente che mostrava il pod del servizio contenitore di AZURE predefinito i criteri di sicurezza, il *limitato* criteri può negare questa richiesta.

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

Creare il pod usando il [kubectl applicare][kubectl-apply] comando e specificare il nome del manifesto YAML:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

I pod non riesce a essere pianificato, come illustrato nell'output di esempio seguente:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: [spec.containers[0].securityContext.privileged: Invalid value: true: Privileged containers are not allowed]
```

I pod non raggiunge la fase di pianificazione, pertanto non presenta alcuna risorsa da eliminare prima di passare.

## <a name="test-creation-of-an-unprivileged-pod"></a>Testare la creazione di un pod senza privilegi

Nell'esempio precedente, la specifica di pod ha richiesto l'escalation dei privilegi. Questa richiesta viene negata per l'impostazione predefinita *limitato* pod dei criteri di sicurezza, in modo che il pod non riesce a essere pianificato. Proviamo ora in esecuzione tale stesso pod NGINX senza la richiesta di escalation dei privilegi.

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

Creare il pod usando il [kubectl applicare][kubectl-apply] comando e specificare il nome del manifesto YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

L'utilità di pianificazione di Kubernetes accetta la richiesta di pod. Tuttavia, se si esamina lo stato del pod tramite `kubectl get pods`, si verifica un errore:

```console
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS                       RESTARTS   AGE
nginx-unprivileged   0/1     CreateContainerConfigError   0          26s
```

Usare la [kubectl descrivono pod][kubectl-describe] comando per esaminare gli eventi per i pod. Esempio sintetico seguente mostra che il contenitore e l'immagine richiedono autorizzazioni root, anche se è non ne fanno richiesta:

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

Anche se non richiediamo alcun accesso con privilegi, è necessario creare un'associazione per la porta l'immagine del contenitore per NGINX *80*. Per associare porte *1024* e versioni precedenti, il *radice* utente viene richiesto. Quando il pod tenta di avviare, il *limitato* criteri di sicurezza pod negano la richiesta.

In questo esempio mostra che i criteri di sicurezza pod predefinito creati dal servizio contenitore di AZURE sono attive e limitano le azioni che eseguibili dagli utenti. È importante comprendere il comportamento di questi criteri predefiniti, come si potrebbe non riconoscere un pod NGINX di base a cui viene negato.

Prima di passare al passaggio successivo, eliminare il pod di test usando il [kubectl eliminare pod][kubectl-delete] comando:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Testare la creazione di un pod con un contesto utente specifico

Nell'esempio precedente, l'immagine del contenitore automaticamente cercato di utilizzare radice per l'associazione NGINX sulla porta 80. Questa richiesta è stata negata per l'impostazione predefinita *limitato* pod dei criteri di sicurezza, in modo che non è possibile avviare il pod. Proviamo ora in esecuzione tale stesso pod NGINX con un contesto utente specifico, ad esempio `runAsUser: 2000`.

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

Creare il pod usando il [kubectl applicare][kubectl-apply] comando e specificare il nome del manifesto YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

L'utilità di pianificazione di Kubernetes accetta la richiesta di pod. Tuttavia, se si esamina lo stato del pod tramite `kubectl get pods`, si verifica un errore diverso rispetto all'esempio precedente:

```console
$ kubectl-nonadminuser get pods

NAME                         READY   STATUS              RESTARTS   AGE
nginx-unprivileged-nonroot   0/1     CrashLoopBackOff    1          3s
```

Usare la [kubectl descrivono pod][kubectl-describe] comando per esaminare gli eventi per i pod. Esempio sintetico seguente mostra gli eventi di pod:

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

Gli eventi indicano che il contenitore è stato creato e avviato. Non è immediatamente evidente per quanto riguarda il motivo per cui il pod è in stato di errore. Diamo un'occhiata ai log del pod usando il [kubectl log][kubectl-logs] comando:

```console
kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous
```

L'output di log di esempio seguente fornisce un valore che indica che all'interno della configurazione di NGINX stesso, non esiste un errore di autorizzazione quando si tenta di avviare il servizio. Anche in questo caso, questo errore è causato dalla necessità di eseguire l'associazione alla porta 80. Sebbene la specifica di pod definiti un account utente normale, questo account utente non è sufficiente in a livello di sistema operativo per il servizio NGINX per avviare e associare la porta con restrizioni.

```console
$ kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous

2019/03/28 22:38:29 [warn] 1#1: the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
nginx: [warn] the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
2019/03/28 22:38:29 [emerg] 1#1: mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
nginx: [emerg] mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
```

Anche in questo caso, è importante comprendere il comportamento dei criteri di sicurezza pod predefinito. Questo errore è stato un po' più difficile da rilevare, e anche in questo caso, si potrebbe non riconoscere un pod NGINX di base a cui viene negato.

Prima di passare al passaggio successivo, eliminare il pod di test usando il [kubectl eliminare pod][kubectl-delete] comando:

```console
kubectl-nonadminuser delete -f nginx-unprivileged-nonroot.yaml
```

## <a name="create-a-custom-pod-security-policy"></a>Creare un criterio di sicurezza pod personalizzato

Ora che si è visto il comportamento dei criteri di sicurezza pod predefinito, è possibile fornire un modo per il *nonadmin utente* per pianificare correttamente i POD.

È possibile creare un criterio per rifiutare i POD che richiedono l'accesso con privilegi. Altre opzioni, ad esempio *runAsUser* o consentiti *volumi*, non sono limitati in modo esplicito. Questo tipo di criteri viene negata una richiesta per l'accesso con privilegi, ma in caso contrario, consente al cluster eseguire i POD richiesti.

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

Creare il criterio tramite il [kubectl applicare][kubectl-apply] comando e specificare il nome del manifesto YAML:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Per visualizzare i criteri disponibili, usare il [kubectl get psp][kubectl-get] comando, come illustrato nell'esempio seguente. Confrontare le *punto di servizio PXE-deny-con privilegi* dei criteri con il valore predefinito *limitato* criteri che è stato applicato negli esempi precedenti per creare un pod. Solo l'uso di *PRIV* negato dai criteri di escalation dei blocchi. Non esistono restrizioni in cui l'utente o gruppo per il *punto di servizio PXE-deny-privilegi* criteri.

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted            false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Consenti account utente da usare i criteri di sicurezza pod personalizzato

Nel passaggio precedente, si creano criteri di sicurezza pod per rifiutare i POD che l'accesso con privilegiata di richiesta. Per consentire i criteri da usare, si crea una *ruolo* o una *ClusterRole*. Associare quindi uno di questi ruoli usando un *RoleBinding* oppure *ClusterRoleBinding*.

Per questo esempio, creare un ClusterRole che consenta *usano* il *punto di servizio PXE-deny-con privilegi* criterio creato nel passaggio precedente. Creare un file denominato `psp-deny-privileged-clusterrole.yaml` e incollare il manifesto YAML seguente:

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

Creare il ClusterRole usando il [kubectl applicare][kubectl-apply] comando e specificare il nome del manifesto YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Creare ora un ClusterRoleBinding per l'uso di ClusterRole creato nel passaggio precedente. Creare un file denominato `psp-deny-privileged-clusterrolebinding.yaml` e incollare il manifesto YAML seguente:

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

Creare un ClusterRoleBinding usando il [kubectl applicare][kubectl-apply] comando e specificare il nome del manifesto YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> Nel primo passaggio di questo articolo, è stata abilitata la funzionalità di criteri di sicurezza pod nel cluster AKS. La procedura consigliata era consentire solo la funzionalità di criteri di sicurezza pod dopo aver definito i propri criteri. Si tratta della fase in cui è necessario abilitare la funzionalità di criteri di sicurezza pod. Sono stati definiti uno o più criteri personalizzati e gli account utente sono stati associati a tali criteri. È ora possibile in modo sicuro i criteri di sicurezza pod delle funzionalità e ridurre al minimo i problemi causati dai criteri predefiniti.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Testare nuovamente la creazione di un pod senza privilegi

Con i criteri di sicurezza pod personalizzato applicato e un'associazione per l'account utente a usare i criteri, è possibile tentare nuovamente di creare un pod senza privilegi. Usare lo stesso `nginx-privileged.yaml` manifesto per creare il pod usando la [kubectl applicare][kubectl-apply] comando:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

I pod è stato pianificato. Quando si seleziona lo stato del pod tramite il [kubectl get pods][kubectl-get] comando, il pod viene *esecuzione*:

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

Questo esempio illustra come creare i criteri di sicurezza pod personalizzato per definire l'accesso al cluster servizio contenitore di AZURE per diversi utenti o gruppi. L'impostazione predefinita i criteri di servizio contenitore di AZURE forniscono controlli rigorosi sugli quali i POD possono eseguire, quindi creare criteri personalizzati per definire correttamente le restrizioni che è necessario.

Eliminare il pod NGINX senza privilegi usando il [kubectl eliminare][kubectl-delete] comando e specificare il nome del manifesto YAML:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per disabilitare i criteri di sicurezza pod, utilizzare il [aggiornare az aks][az-aks-update] nuovo il comando. I seguenti esempio disabilita pod criteri di sicurezza sul nome del cluster *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

Successivamente, eliminare il ClusterRole e ClusterRoleBinding:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

Eliminare il criterio di rete tramite [kubectl eliminare][kubectl-delete] comando e specificare il nome del manifesto YAML:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Infine, eliminare il *punto di servizio PXE-aks* dello spazio dei nomi:

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo illustra come creare un criterio di sicurezza pod per impedire l'uso dell'accesso con privilegi. Esistono numerose funzionalità che è possibile applicare un criterio, ad esempio tipo di volume o l'utente RunAs. Per altre informazioni sulle opzioni disponibili, vedere la [documentazione di riferimento dei criteri di sicurezza di pod Kubernetes][kubernetes-policy-reference].

Per altre informazioni su come limitare il traffico di rete di pod, vedere [proteggere il traffico tra i POD usando i criteri di rete in AKS][network-policies].

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
