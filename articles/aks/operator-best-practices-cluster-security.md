---
title: Procedure consigliate per l'operatore - Sicurezza dei cluster nel servizio Azure Kubernetes
description: Procedure consigliate per l'operatore del cluster per la gestione della sicurezza e degli aggiornamenti dei cluster nel servizio Azure Kubernetes
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: iainfou
ms.openlocfilehash: 5acabc8381422b9202b041cf849af3b35809a3c0
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53111201"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Procedure consigliate per la sicurezza e gli aggiornamenti dei cluster nel servizio Azure Kubernetes

La sicurezza dei carichi di lavoro e dei dati è un fattore fondamentale da tenere in considerazione relativamente alla gestione dei cluster nel servizio Azure Kubernetes. È particolarmente importante proteggere l'accesso alle risorse e ai carichi di lavoro quando si eseguono cluster multi-tenant con isolamento logico. Per ridurre al minimo il rischio di attacchi, è necessario anche applicare i più recenti aggiornamenti della sicurezza per Kubernetes e per il sistema operativo dei nodi.

Questo articolo illustra in particolare come proteggere il cluster del servizio Azure Kubernetes (AKS). Si apprenderà come:

> [!div class="checklist"]
> * Usare Azure Active Directory e i controlli degli accessi in base al ruolo per proteggere l'accesso al server API
> * Proteggere l'accesso del contenitore alle risorse dei nodi
> * Aggiornare un cluster AKS alla versione più recente di Kubernetes
> * Mantenere i nodi aggiornati e applicare automaticamente le patch di protezione

È anche possibile leggere le procedure consigliate per la [gestione delle immagini del contenitore][best-practices-container-image-management] e la [sicurezza dei pod][best-practices-pod-security].

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Proteggere l'accesso al server dell'API e ai nodi del cluster

**Indicazioni sulle procedure consigliate** - La protezione dell'accesso al server dell'API Kubernetes è una delle cose più importanti da fare per proteggere il cluster. Integrare il controllo degli accessi in base al ruolo di Kubernetes con Azure Active Directory per controllare l'accesso al server dell'API. Questi controlli consentono di proteggere il servizio Azure Kubernetes allo stesso modo in cui si protegge l'accesso alle sottoscrizioni di Azure.

Il server dell'API Kubernetes fornisce un singolo punto di connessione per le richieste di esecuzione di azioni all'interno di un cluster. Per proteggere e controllare l'accesso al server dell'API, limitare l'accesso e concedere le autorizzazioni di accesso con privilegi minime necessarie. Questo approccio non è esclusivo di Kubernetes, ma è particolarmente importante quando il cluster AKS è isolato logicamente per l'uso in più tenant.

Azure Active Directory (AD) offre una soluzione di gestione delle identità di livello aziendale che si integra con i cluster AKS. Dal momento che Kubernetes non fornisce una soluzione di gestione delle identità, senza Azure AD sarebbe difficile limitare l'accesso al server dell'API in modo granulare. Grazie ai cluster integrati con Azure AD nel servizio Azure Kubernetes è possibile usare gli account utente e di gruppo esistenti per autenticare gli utenti nel server dell'API.

![Integrazione di Azure Active Directory per i cluster del servizio Kubernetes di Azure (AKS)](media/operator-best-practices-cluster-security/aad-integration.png)

Usare il controllo degli accessi in base al ruolo di Kubernetes e l'integrazione con Azure AD per proteggere il server dell'API e concedere il minor numero di autorizzazioni necessarie a un set di risorse con ambito, ad esempio un singolo spazio dei nomi. A utenti o gruppi diversi in Azure AD possono essere concessi ruoli di controllo degli accessi in base al ruolo diversi. Queste autorizzazioni granulari consentono di limitare l'accesso al server dell'API e forniscono un chiaro audit trail delle azioni eseguite.

Come procedura consigliata, per fornire l'accesso a file e cartelle usare gruppi invece di singole identità e usare l'appartenenza ai *gruppi* di Azure AD invece dei singoli *utenti* per associare gli utenti ai ruoli di controllo degli accessi in base al ruolo. Se cambia l'appartenenza a un gruppo di un utente, cambiano di conseguenza anche le sue autorizzazioni di accesso nel cluster AKS. Se si associa l'utente direttamente a un ruolo, la relativa funzione lavorativa può cambiare. L'appartenenza ai gruppi di Azure AD verrebbe aggiornata, ma le autorizzazioni nel cluster AKS non verrebbero aggiornate di conseguenza. In uno scenario di questo tipo l'utente finisce per avere più autorizzazioni del necessario.

Per altre informazioni sull'integrazione con Azure AD e sul controllo degli accessi in base al ruolo, vedere [Opzioni di accesso e identità per il servizio Kubernetes di Azure][aks-best-practices-identity].

## <a name="secure-container-access-to-resources"></a>Proteggere l'accesso del contenitore alle risorse

**Indicazioni sulle procedure consigliate** - Limitare l'accesso alle azioni che possono essere eseguite dai contenitori. Concedere il minor numero possibile di autorizzazioni ed evitare l'uso dell'accesso radice o dell'escalation dei privilegi.

Così come è opportuno concedere a utenti o gruppi il minor numero di privilegi necessari, anche i contenitori dovrebbero essere limitati ai soli processi e azioni che devono eseguire. Per ridurre al minimo i rischi di attacchi, non configurare applicazioni e contenitori che richiedono l'escalation dei privilegi o l'accesso radice. Ad esempio, impostare `allowPrivilegeEscalation: false` nel manifesto del pod. Questi *contesti di protezione dei pod* sono integrati in Kubernetes e consentono di definire autorizzazioni aggiuntive come l'account utente o di gruppo da usare o le funzionalità Linux da esporre. Per altre procedure consigliate, vedere [Proteggere l'accesso dei pod alle risorse][pod-security-contexts].

Per un controllo più granulare delle azioni dei contenitori, è anche possibile usare funzionalità di sicurezza predefinite di Linux, come *AppArmor* e *seccomp*. Queste funzionalità vengono definite a livello di nodo e quindi implementate tramite un manifesto pod.

### <a name="app-armor"></a>AppArmor

Per limitare le azioni che possono essere eseguite dai contenitori, è possibile usare il modulo di protezione del kernel di Linux [AppArmor][k8s-apparmor]. Il modulo è disponibile come parte del sistema operativo del nodo AKS sottostante ed è abilitato per impostazione predefinita. Occorre creare profili di AppArmor che limitano azioni come la lettura, la scrittura o l'esecuzione oppure funzioni di sistema come il montaggio dei file system. I profili AppArmor predefiniti limitano l'accesso a vari percorsi `/proc` e `/sys` e consentono di isolare logicamente i contenitori dal nodo sottostante. AppArmor funziona non solo per i pod Kubernetes ma per qualsiasi applicazione in esecuzione su Linux.

![Profili AppArmor in uso in un cluster AKS per limitare le azioni dei contenitori](media/operator-best-practices-container-security/apparmor.png)

Per una dimostrazione di AppArmor in azione, l'esempio seguente crea un profilo che impedisce la scrittura nei file. Accedere tramite [SSH][aks-ssh] a un nodo AKS, quindi creare un file denominato *deny-write.profile* e incollare il contenuto seguente:

```
#include <tunables/global>
profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
  #include <abstractions/base>
  
  file,
  # Deny all file writes.
  deny /** w,
}
```

Per aggiungere profili AppArmor occorre usare il comando `apparmor_parser`. Aggiungere il profilo ad AppArmor e specificare il nome del profilo creato nel passaggio precedente:

```console
sudo apparmor_parser deny-write.profile
```

Se il profilo viene correttamente analizzato e applicato ad AppArmor, non viene restituito alcun output. Si ritorna al prompt dei comandi.

Dal computer locale creare ora un manifesto pod denominato *aks-apparmor.yaml* e incollare il contenuto seguente. Questo manifesto definisce un'annotazione per `container.apparmor.security.beta.kubernetes` e fa riferimento al profilo *deny-write* creato nei passaggi precedenti:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-apparmor
  annotations:
    container.apparmor.security.beta.kubernetes.io/hello: localhost/k8s-apparmor-example-deny-write
spec:
  containers:
  - name: hello
    image: busybox
    command: [ "sh", "-c", "echo 'Hello AppArmor!' && sleep 1h" ]
```

Distribuire il pod di esempio usando il comando [kubectl apply][kubectl-apply]:

```console
kubectl apply -f aks-apparmor.yaml
```

Dopo aver distribuito il pod, usare il comando [kubectl exec][kubectl-exec] per scrivere in un file. Come illustrato nell'output di esempio seguente, il comando non può essere eseguito:

```
$ kubectl exec hello-apparmor touch /tmp/test

touch: /tmp/test: Permission denied
command terminated with exit code 1
```

Per altre informazioni su AppArmor, vedere [AppArmor][k8s-apparmor] nella documentazione di Kubernetes.

### <a name="secure-computing"></a>seccomp

Mentre AppArmor funziona con qualsiasi applicazione Linux, [seccomp (*sec*ure *comp*uting, elaborazione sicura)][seccomp] funziona a livello di processo. Anche seccomp è un modulo di protezione del kernel di Linux ed è supportato in modo nativo dal runtime Docker usato dai nodi AKS. seccomp limita le chiamate ai processi che possono essere eseguite dai contenitori. Occorre creare dei filtri che definiscano le azioni da consentire o negare e quindi usare annotazioni all'interno di un manifesto YAML pod da associare al filtro seccomp. Questo approccio rispecchia la procedura consigliata che indica di concedere al contenitore solo le autorizzazioni minime necessarie per l'esecuzione.

Per una dimostrazione di seccomp in azione, creare un filtro che impedisce di cambiare le autorizzazioni su un file. Accedere tramite [SSH][aks-ssh] a un nodo AKS, quindi creare un filtro seccomp denominato */var/lib/kubelet/seccomp/prevent-chmod* e incollare il contenuto seguente:

```
{
  "defaultAction": "SCMP_ACT_ALLOW",
  "syscalls": [
    {
      "name": "chmod",
      "action": "SCMP_ACT_ERRNO"
    }
  ]
}
```

Dal computer locale creare ora un manifesto pod denominato *aks-seccomp.yaml* e incollare il contenuto seguente. Questo manifesto definisce un'annotazione per `seccomp.security.alpha.kubernetes.io` e fa riferimento al filtro *prevent-chmod* creato nel passaggio precedente:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: chmod-prevented
  annotations:
    seccomp.security.alpha.kubernetes.io/pod: localhost/prevent-chmod
spec:
  containers:
  - name: chmod
    image: busybox
    command:
      - "chmod"
    args:
     - "777"
     - /etc/hostname
  restartPolicy: Never
```

Distribuire il pod di esempio usando il comando [kubectl apply][kubectl-apply]:

```console
kubectl apply -f ./aks-seccomp.yaml
```

Visualizzare lo stato dei pod usando il comando [kubectl get pods][kubectl-get]. Il pod segnala un errore. Come illustrato nell'output di esempio seguente, il filtro seccomp impedisce l'esecuzione del comando `chmod`:

```
$ kubectl get pods

NAME                      READY     STATUS    RESTARTS   AGE
chmod-prevented           0/1       Error     0          7s
```

Per altre informazioni sui filtri disponibili, vedere [Seccomp security profiles for Docker][seccomp] (Profili di sicurezza di seccomp per Docker).

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Eseguire regolarmente l'aggiornamento alla versione più recente di Kubernetes

**Indicazioni sulle procedure consigliate** - Per mantenere l'ambiente aggiornato con le nuove funzionalità e correzioni di bug, aggiornare regolarmente la versione di Kubernetes nel cluster AKS.

Kubernetes rilascia nuove funzionalità con maggiore frequenza rispetto alle piattaforme di infrastruttura più tradizionali. Gli aggiornamenti di Kubernetes includono nuove funzionalità e correzioni di bug o correzioni rapide per la sicurezza. Le nuove funzionalità passano in genere dallo stato *alpha* allo stato *beta* per diventare infine *stabili* e generalmente disponibili e consigliate per l'uso in ambiente di produzione. Questo ciclo di rilascio dovrebbe consentire di aggiornare Kubernetes senza riscontrare regolarmente modifiche di rilievo o dover modificare le proprie distribuzioni e i modelli.

Il servizio Azure Kubernetes supporta quattro versioni secondarie di Kubernetes. Questo significa che, quando viene introdotta una nuova versione di patch secondaria, vengono ritirate la versione secondaria precedente e le versioni delle patch supportate. Gli aggiornamenti secondari a Kubernetes avvengono su base periodica. Assicurarsi di disporre di un processo di governance che controlli lo stato del sistema ed esegua l'aggiornamento quando necessario in modo da non perdere il diritto al supporto. Per altre informazioni, vedere [Versioni Kubernetes supportate nel servizio Kubernetes di Azure][aks-supported-versions].

Per verificare le versioni disponibili per il cluster in uso, usare il comando [az aks get-upgrades][az-aks-get-upgrades] come illustrato nell'esempio seguente:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

È quindi possibile aggiornare il cluster AKS tramite il comando [az aks upgrade][az-aks-upgrade]. Il processo di aggiornamento isola e svuota un nodo alla volta, pianifica i pod sui nodi rimanenti e quindi distribuisce un nuovo nodo che esegue le versioni più recenti del sistema operativo e di Kubernetes.

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.11.3
```

Per altre informazioni sugli aggiornamenti nel servizio Azure Container, vedere [Versioni Kubernetes supportate nel servizio Kubernetes di Azure][aks-supported-versions] e [Aggiornare un cluster di Azure Kubernetes Service (AKS)][aks-upgrade].

## <a name="process-node-updates-and-reboots-using-kured"></a>Elaborare gli aggiornamenti e i riavvii dei nodi tramite kured

**Indicazioni sulle procedure consigliate** - Il servizio Azure Kubernetes scarica e installa automaticamente le correzioni per la sicurezza in ogni nodo del ruolo di lavoro, ma esegue il riavvio automaticamente se necessario. Usare `kured` per controllare i riavvii in sospeso, quindi isolare e svuotare il nodo per consentirne il riavvio e applicare gli aggiornamenti in modo che sia il più possibile protetto relativamente al sistema operativo.

Ogni sera i nodi AKS ricevono le patch di protezione disponibili tramite il canale di aggiornamento della distribuzione. Questo comportamento viene configurato automaticamente quando i nodi vengono distribuiti in un cluster AKS. Per ridurre al minimo le interruzioni del servizio e l'impatto sui carichi di lavoro in esecuzione, i nodi non vengono riavviati automaticamente se una patch di protezione o un aggiornamento del kernel lo richiede.

Il progetto open-source [kured (KUbernetes REboot Daemon)][kured] di Weaveworks controlla i riavvii di nodi in sospeso. Quando a un nodo vengono applicati aggiornamenti che richiedono un riavvio, il nodo viene isolato e svuotato in sicurezza per spostare e pianificare i pod in altri nodi del cluster. Dopo il riavvio, il nodo viene reinserito nel cluster e Kubernetes riprende la pianificazione dei pod su di esso. Per ridurre al minimo le interruzioni del servizio, a `kured` è consentito riavviare un solo nodo alla volta.

![Processo di riavvio dei nodi AKS tramite kured](media/operator-best-practices-cluster-security/node-reboot-process.png)

Se si vuole un controllo più dettagliato sull'esecuzione dei riavvi, è possibile integrare `kured` con Prometheus per impedire i riavvii quando sono in corso altri eventi di manutenzione o sono presenti problemi nel cluster. Questa integrazione riduce al minimo le ulteriori complicazioni riavviando i nodi mentre si stanno attivamente risolvendo altri problemi.

Per altre informazioni su come gestire i riavvii dei nodi, vedere [Applicare aggiornamenti di sicurezza e del kernel ai nodi nel servizio Kubernetes di Azure (AKS)][aks-kured].

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato in particolare come proteggere il cluster del servizio Azure Kubernetes (AKS). Per implementare alcune di queste aree, vedere gli articoli seguenti:

* [Integrare Azure Active Directory con AKS][aks-aad]
* [Aggiornare un cluster di Azure Kubernetes Service (AKS)][aks-upgrade]
* [Applicare aggiornamenti di sicurezza e del kernel ai nodi nel servizio Kubernetes di Azure (AKS)][aks-kured]

<!-- EXTERNAL LINKS -->
[kured]: https://github.com/weaveworks/kured
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://docs.docker.com/engine/security/seccomp/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[aks-supported-versions]: supported-kubernetes-versions.md
[aks-upgrade]: upgrade-cluster.md
[aks-best-practices-identity]: concepts-identity.md
[aks-kured]: node-updates-kured.md
[aks-aad]: aad-integration.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md
