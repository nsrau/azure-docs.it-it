---
title: Risolvere i problemi comuni del servizio Azure Kubernetes
description: Informazioni su come individuare e risolvere i problemi comuni quando si usa il servizio Azure Kubernetes
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: 2c25069ce5231a1f89027dea69579231f0fe4bcd
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517088"
---
# <a name="aks-troubleshooting"></a>Risoluzione dei problemi di servizio Azure Kubernetes

Quando si creano o gestiscono cluster del servizio Azure Kubernetes (AKS), in alcuni casi potrebbero verificarsi problemi. In questo articolo sono descritti alcuni problemi comuni e i passaggi per risolverli.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>In generale, dove è possibile trovare informazioni sul debug di problemi di Kubernetes?

Consultare la [guida ufficiale per la risoluzione dei problemi dei cluster di Kubernetes](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
È disponibile anche una [guida alla risoluzione dei problemi](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md) pubblicata da un tecnico Microsoft per la risoluzione dei problemi relativi a pod, nodi, cluster e altre funzionalità.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Durante la creazione o l’aggiornamento, viene visualizzato un errore di tipo “quota superata”. Cosa devo fare? 

È necessario [richiedere i core](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Qual è l'impostazione del numero massimo di pod per nodo per il servizio Azure Kubernetes?

Il numero massimo di pod per nodo è 30 per impostazione predefinita se si distribuisce un cluster del servizio Azure Kubernetes nel portale di Azure.
Il numero massimo di pod per nodo è 110 per impostazione predefinita se si distribuisce un cluster del servizio Azure Kubernetes nell’interfaccia della riga di comando di Azure. Assicurarsi di usare la versione più recente dell’interfaccia della riga di comando di Azure. Questa impostazione predefinita può essere modificata usando il flag `–-max-pods` nel comando `az aks create`.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Viene visualizzato l'errore "insufficientSubnetSize" durante la distribuzione di un cluster del servizio Azure Kubernetes con funzionalità di rete avanzate. Cosa devo fare?

Se si usa Azure CNI (funzionalità di rete avanzate), il servizio Azure Kubernetes prealloca gli indirizzi IP in base al valore "max-pods" per nodo configurato. Il numero di nodi in un cluster del servizio Azure Kubernetes può essere compreso tra 1 e 110. In base al numero massimo di pod per nodo configurato, le dimensioni della subnet devono essere maggiori del prodotto tra il numero di nodi e il numero massimo di pod per nodo. L'equazione di base seguente descrive questo requisito:

Dimensioni della subnet > numero di nodi del cluster (prendendo in considerazione i requisiti di scalabilità futuri) * numero massimo di pod per nodo.

Per altre informazioni, vedere [Pianificare l'indirizzamento IP per il cluster](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Il pod è bloccato in modalità “CrashLoopBackOff”. Cosa devo fare?

Potrebbero esistere vari motivi per cui il modo rimane bloccato in tale modalità. Potrebbe essere opportuno verificare:

* Il pod stesso con `kubectl describe pod <pod-name>`.
* I log con `kubectl log <pod-name>`.

Per altre informazioni su come risolvere i problemi di pod, vedere [Debug delle applicazioni](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Qual è la procedura per abilitare RBAC in un cluster esistente?

Purtroppo, l'abilitazione del controllo degli accessi in base al ruolo (RBAC) nei cluster esistenti non è al momento supportata. È necessario creare in modo esplicito nuovi cluster. Se si usa l'interfaccia della riga di comando, RBAC è abilitato per impostazione predefinita. Se si usa il portale del servizio Azure Kubernetes, nel flusso di lavoro di creazione è disponibile un pulsante che consente di abilitare RBAC.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>È stato creato un cluster con RBAC abilitato tramite l'interfaccia della riga di comando di Azure con le impostazioni predefinite o il portale di Azure e nel dashboard di Kubernetes vengono visualizzati diversi avvisi. Il dashboard non generava avvisi. Cosa devo fare?

Il motivo per cui vengono visualizzati avvisi nel dashboard è che ora il cluster è abilitato con RBAC e l'accesso è stato disabilitato per impostazione predefinita. In generale, questo approccio è considerato una procedura appropriata perché l'esposizione predefinita del dashboard a tutti gli utenti del cluster può comportare rischi per la sicurezza. Se si vuole comunque abilitare il dashboard, seguire le indicazioni riportate in questo [post di blog](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Non è possibile connettersi al dashboard. Cosa devo fare?

Il modo più semplice per accedere al servizio all'esterno del cluster consiste nell'eseguire `kubectl proxy`, per inoltrare le richieste inviate a localhost sulla porta 8001 al server API Kubernetes. Da qui, il server API può usare un proxy per il servizio: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Se non viene visualizzato il dashboard di Kubernetes, controllare se il pod `kube-proxy` è eseguito nello spazio dei nomi `kube-system`. Se non è esecuzione, eliminare il pod in modo che venga riavviato.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Non è possibile ottenere i log usando i log di kubectl o non è possibile connettersi al server API. Ricevo un messaggio di errore dal server: errore durante la connessione del back-end: Dial TCP... ". Cosa devo fare?

Verificare che il gruppo di sicurezza di rete predefinito non sia stato modificato e che sia la porta 22 che la 9000 siano aperte per la connessione al server API. Controllare se il Pod `tunnelfront` è in esecuzione nello spazio dei nomi *Kube-System* usando il comando `kubectl get pods --namespace kube-system`. In caso contrario, forzare l’eliminazione del pod per riavviarlo.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Si sta tentando di eseguire l'aggiornamento o la scalabilità e viene ricevuto un errore "messaggio: la modifica della proprietà' imageReference ' non è consentita". Come si risolve il problema?

È possibile che questo errore venga visualizzato in seguito alla modifica di tag nei nodi dell'agente all'interno del cluster del servizio Azure Kubernetes. La modifica e l'eliminazione di tag e altre proprietà delle risorse nel gruppo di risorse MC_* può causare risultati imprevisti. La modifica delle risorse nel gruppo MC_* del cluster del servizio Kubernetes di Azure è una violazione dell'obiettivo del livello di servizio (SLO).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Si ricevono errori che il cluster è in stato di errore e l'aggiornamento o il ridimensionamento non funziona finché non viene risolto

*Questa assistenza per la risoluzione dei problemi è diretta da https://aka.ms/aks-cluster-failed*

Questo errore si verifica quando i cluster entrano in uno stato di errore per diversi motivi. Attenersi alla procedura seguente per risolvere lo stato di errore del cluster prima di riprovare a eseguire l'operazione precedentemente non riuscita:

1. Fino a quando il cluster non è `failed` stato, le operazioni di `upgrade` e `scale` non riusciranno. I problemi e le soluzioni principali comuni includono:
    * Ridimensionamento con **quota di calcolo (CRP) insufficiente**. Per risolvere il cluster, ridimensionare prima di tutto il cluster fino a uno stato di obiettivo stabile entro la quota. Seguire quindi questa [procedura per richiedere un aumento della quota di calcolo](../azure-supportability/resource-manager-core-quotas-request.md) prima di riprovare a eseguire la scalabilità verticale oltre i limiti di quota iniziali.
    * Ridimensionamento di un cluster con rete avanzata e **risorse insufficienti per la subnet (rete)** . Per risolvere il cluster, ridimensionare prima di tutto il cluster fino a uno stato di obiettivo stabile entro la quota. Seguire quindi [questa procedura per richiedere un aumento della quota di risorse](../azure-resource-manager/resource-manager-quota-errors.md#solution) prima di provare a eseguire la scalabilità verticale oltre i limiti iniziali della quota.
2. Una volta risolta la cause sottostante dell'errore di aggiornamento, il cluster deve essere in uno stato di esito positivo. Una volta verificato uno stato di esito positivo, ripetere l'operazione originale.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Si sono verificati errori durante il tentativo di aggiornare o ridimensionare lo stato in corso di aggiornamento del cluster o l'aggiornamento non è riuscito

*Questa assistenza per la risoluzione dei problemi è diretta da https://aka.ms/aks-pending-upgrade*

Le operazioni di aggiornamento e ridimensionamento in un cluster con un pool a nodo singolo o un cluster con [più pool di nodi](use-multiple-node-pools.md) si escludono a vicenda. Non è possibile avere un cluster o un pool di nodi simultaneamente per l'aggiornamento e la scalabilità. Al contrario, ogni tipo di operazione deve essere completato sulla risorsa di destinazione prima della richiesta successiva sulla stessa risorsa. Di conseguenza, le operazioni sono limitate quando le operazioni di aggiornamento o ridimensionamento attive vengono eseguite o tentate e successivamente non riuscite. 

Per facilitare la diagnosi del problema, eseguire `az aks show -g myResourceGroup -n myAKSCluster -o table` per recuperare lo stato dettagliato del cluster. In base al risultato:

* Se il cluster sta aggiornando attivamente, attendere che l'operazione venga terminata. In caso di esito positivo, ripetere l'operazione precedentemente non riuscita.
* Se il cluster non è stato aggiornato, seguire i passaggi descritti nella sezione precedente.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>È possibile spostare il cluster in una sottoscrizione o in una sottoscrizione diversa con il cluster in un nuovo tenant?

Se il cluster AKS è stato spostato in una sottoscrizione diversa o nella sottoscrizione proprietaria del cluster a un nuovo tenant, il cluster perderà la funzionalità a causa della perdita di assegnazioni di ruolo e dei diritti di entità servizio. AKS non supporta lo stato di **trasferimento dei cluster tra sottoscrizioni o tenant** a causa del vincolo this.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Ricevo errori durante il tentativo di usare le funzionalità che richiedono set di scalabilità di macchine virtuali

*Questa assistenza per la risoluzione dei problemi è diretta da aka.ms/aks-vmss-enablement*

È possibile che vengano visualizzati errori che indicano che il cluster AKS non si trova in un set di scalabilità di macchine virtuali, come nell'esempio seguente:

**AgentPool ' AgentPool ' ha impostato la scalabilità automatica come abilitato ma non nei set di scalabilità di macchine virtuali**

Per usare funzionalità come il ridimensionamento automatico del cluster o più pool di nodi, è necessario creare cluster AKS che usano i set di scalabilità di macchine virtuali. Gli errori vengono restituiti se si prova a usare funzionalità che dipendono dai set di scalabilità di macchine virtuali e si fa riferimento a un cluster AKS normale di un set di scalabilità di macchine virtuali.

Seguire i passaggi *prima di iniziare* nel documento appropriato per creare correttamente un cluster AKS:

* [Usare il ridimensionamento automatico del cluster](cluster-autoscaler.md)
* [Creare e usare più pool di nodi](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Quali restrizioni di denominazione sono applicate per le risorse e i parametri AKS?

*Questa assistenza per la risoluzione dei problemi è diretta da aka.ms/aks-naming-rules*

Le restrizioni di denominazione sono implementate sia dalla piattaforma Azure che da AKS. Se un nome di risorsa o un parametro interrompe una di queste restrizioni, viene restituito un errore in cui viene chiesto di fornire un input diverso. Si applicano le linee guida di denominazione comuni seguenti:

* Il nome del gruppo di risorse *MC_* AKS combina il nome del gruppo di risorse e il nome della risorsa. La sintassi generata automaticamente di `MC_resourceGroupName_resourceName_AzureRegion` non deve essere maggiore di 80 caratteri. Se necessario, ridurre la lunghezza del nome del gruppo di risorse o del cluster AKS.
* Il *dnsPrefix* deve iniziare e terminare con valori alfanumerici. I caratteri validi includono valori alfanumerici e trattini (-). Il *dnsPrefix* non può includere caratteri speciali, ad esempio un punto (.).

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Si ricevono errori durante il tentativo di creare, aggiornare, ridimensionare, eliminare o aggiornare il cluster. tale operazione non è consentita perché è in corso un'altra operazione.

*Questa assistenza per la risoluzione dei problemi è diretta da aka.ms/aks-pending-operation*

Le operazioni del cluster sono limitate quando è ancora in corso un'operazione precedente. Per recuperare uno stato dettagliato del cluster, usare il comando `az aks show -g myResourceGroup -n myAKSCluster -o table`. Usare il proprio gruppo di risorse e il nome del cluster AKS in base alle esigenze.

In base all'output dello stato del cluster:

* Se il cluster si trova in uno stato di provisioning diverso da *succeeded* o *failed*, attendere che venga terminata l'operazione (*aggiornamento/aggiornamento/creazione/ridimensionamento/eliminazione/migrazione*). Al termine dell'operazione precedente, riprovare a eseguire l'operazione più recente del cluster.

* Se si verifica un errore di aggiornamento del cluster, attenersi alla procedura descritta [per la ricezione di errori nel caso in cui il cluster si trova in stato di errore e l'aggiornamento o il ridimensionamento non funzionerà fino a quando non sarà stato risolto](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>Si ricevono errori che non sono stati trovati dall'entità servizio quando si tenta di creare un nuovo cluster senza passarne uno esistente.

Quando si crea un cluster AKS, è necessario che un'entità servizio crei risorse per conto dell'utente. AKS offre la possibilità di crearne uno nuovo in fase di creazione del cluster, ma ciò richiede che Azure Active Directory propaghi completamente la nuova entità servizio in un tempo ragionevole per consentire la creazione del cluster. Quando questa propagazione richiede troppo tempo, la convalida del cluster non riuscirà a creare perché non è possibile trovare un'entità servizio disponibile a tale scopo. 

Usare le soluzioni alternative seguenti:
1. Usare un'entità servizio esistente che è già stata propagata tra le aree ed esiste per passare ad AKS al momento della creazione del cluster.
2. Se si usano gli script di automazione, aggiungere i ritardi di tempo tra la creazione dell'entità servizio e la creazione del cluster AKS.
3. Se si usa portale di Azure, tornare alle impostazioni del cluster durante la creazione e ripetere la pagina di convalida dopo alcuni minuti.

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>Si ricevono errori dopo la limitazione del traffico in uscita

Quando si limita il traffico in uscita da un cluster AKS, sono disponibili le porte in uscita e le regole di rete [consigliate e facoltative](limit-egress-traffic.md) e le regole di dominio/FQDN/applicazione per AKS. Se le impostazioni sono in conflitto con una di queste regole, potrebbe non essere possibile eseguire determinati comandi di `kubectl`. È anche possibile che vengano visualizzati errori durante la creazione di un cluster AKS.

Verificare che le impostazioni non siano in conflitto con le porte in uscita, le regole di rete e le regole di dominio FQDN/applicazione consigliate o facoltative.

## <a name="azure-storage-and-aks-troubleshooting"></a>Risoluzione dei problemi relativi ad archiviazione di Azure e AKS

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>Quali sono le versioni stabili consigliate di Kubernetes per il disco di Azure? 

| Versione di Kubernetes | Versione consigliata |
| -- | :--: |
| 1,12 | 1.12.9 o versione successiva |
| 1,13 | 1.13.6 o versione successiva |
| 1,14 | 1.14.2 o versione successiva |


### <a name="what-versions-of-kubernetes-have-azure-disk-support-on-the-sovereign-cloud"></a>Quali versioni di Kubernetes dispongono di supporto dischi di Azure nel cloud sovrano?

| Versione di Kubernetes | Versione consigliata |
| -- | :--: |
| 1,12 | 1.12.0 o versione successiva |
| 1,13 | 1.13.0 o versione successiva |
| 1,14 | 1.14.0 o versione successiva |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>WaitForAttach non riuscito per il disco di Azure: analisi di "/dev/disk/Azure/SCSI1/LUN1": sintassi non valida

In Kubernetes versione 1,10, MountVolume. WaitForAttach potrebbe non riuscire con il rimontaggio del disco di Azure.

In Linux potrebbe essere visualizzato un errore di formato DevicePath errato. ad esempio:

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

In Windows, è possibile che venga visualizzato un errore di numero DevicePath (LUN) errato. ad esempio:

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

Questo problema è stato risolto nelle versioni seguenti di Kubernetes:

| Versione di Kubernetes | Versione fissa |
| -- | :--: |
| 1,10 | 1.10.2 o versione successiva |
| 1.11 | 1.11.0 o versione successiva |
| 1,12 e versioni successive | N/D |

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Errore durante l'impostazione di UID e GID in mountOptions per il disco di Azure

Per impostazione predefinita, il disco di Azure usa ext4, XFS filesystem e mountOptions come UID = x, GID = x non può essere impostato in fase di montaggio. Se, ad esempio, si è tentato di impostare mountOptions UID = 999, GID = 999, viene visualizzato un errore simile al seguente:

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

Per attenuare il problema, eseguire una delle operazioni seguenti:

* [Configurare il contesto di sicurezza per un pod](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) impostando uid in runAsUser e GID in fsGroup. Ad esempio, l'impostazione seguente imposta Pod RunAs come radice e la rende accessibile a qualsiasi file:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

  >[!NOTE]
  > Poiché GID e UID sono montati come radice o 0 per impostazione predefinita. Se gid o UID sono impostati come non radice, ad esempio 1000, Kubernetes utilizzerà `chown` per modificare tutte le directory e i file del disco. Questa operazione può richiedere molto tempo e può facilitare il montaggio del disco.

* Usare `chown` in initContainers per impostare GID e UID. ad esempio:

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="error-when-deleting-azure-disk-persistentvolumeclaim-in-use-by-a-pod"></a>Errore durante l'eliminazione di PersistentVolumeClaim del disco di Azure in uso da un pod

Se si prova a eliminare un disco PersistentVolumeClaim di Azure che viene usato da un pod, potrebbe essere visualizzato un errore. ad esempio:

```console
$ kubectl describe pv pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06
...
Message:         disk.DisksClient#Delete: Failure responding to request: StatusCode=409 -- Original Error: autorest/azure: Service returned an error. Status=409 Code="OperationNotAllowed" Message="Disk kubernetes-dynamic-pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06 is attached to VM /subscriptions/{subs-id}/resourceGroups/MC_markito-aks-pvc_markito-aks-pvc_westus/providers/Microsoft.Compute/virtualMachines/aks-agentpool-25259074-0."
```

In Kubernetes versione 1,10 e successive è disponibile una funzionalità di protezione PersistentVolumeClaim abilitata per impostazione predefinita per evitare questo errore. Se si usa una versione di Kubernetes che non ha la correzione per questo problema, è possibile attenuare questo problema eliminando il pod usando il PersistentVolumeClaim prima di eliminare il PersistentVolumeClaim.


### <a name="error-cannot-find-lun-for-disk-when-attaching-a-disk-to-a-node"></a>Errore "Impossibile trovare lun per il disco" quando si connette un disco a un nodo

Quando si connette un disco a un nodo, è possibile che venga visualizzato l'errore seguente:

```console
MountVolume.WaitForAttach failed for volume "pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6" : Cannot find Lun for disk kubernetes-dynamic-pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6
```

Questo problema è stato risolto nelle versioni seguenti di Kubernetes:

| Versione di Kubernetes | Versione fissa |
| -- | :--: |
| 1,10 | 1.10.10 o versione successiva |
| 1.11 | 1.11.5 o versione successiva |
| 1,12 | 1.12.3 o versione successiva |
| 1,13 | 1.13.0 o versione successiva |
| 1,14 e versioni successive | N/D |

Se si usa una versione di Kubernetes che non ha la correzione per questo problema, è possibile attenuare il problema attendendo alcuni minuti e riprovare.

### <a name="azure-disk-attachdetach-failure-mount-issues-or-io-errors-during-multiple-attachdetach-operations"></a>Errore di collegamento/scollegamento del disco di Azure, problemi di montaggio o errori di I/O durante più operazioni di collegamento/scollegamento

A partire da Kubernetes versione 1.9.2, quando si eseguono più operazioni di collegamento/scollegamento in parallelo, è possibile che si verifichino i problemi del disco seguenti a causa di una cache di VM Dirty:

* Errori di collegamento/scollegamento del disco
* Errori di I/O su disco
* Scollegamento del disco imprevisto dalla macchina virtuale
* Esecuzione della macchina virtuale in stato di errore a causa del montaggio di un disco non esistente

Questo problema è stato risolto nelle versioni seguenti di Kubernetes:

| Versione di Kubernetes | Versione fissa |
| -- | :--: |
| 1,10 | 1.10.12 o versione successiva |
| 1.11 | 1.11.6 o versione successiva |
| 1,12 | 1.12.4 o versione successiva |
| 1,13 | 1.13.0 o versione successiva |
| 1,14 e versioni successive | N/D |

Se si usa una versione di Kubernetes che non ha la correzione per questo problema, è possibile attenuare il problema provando quanto segue:

* Se un disco è in attesa di disconnettersi per un lungo periodo di tempo, provare a scollegare il disco manualmente

### <a name="azure-disk-waiting-to-detach-indefinitely"></a>Disco di Azure in attesa di scollegarsi per un periodo illimitato

In alcuni casi, se un'operazione di scollegamento del disco di Azure non riesce al primo tentativo, l'operazione di scollegamento non verrà ritentata e rimarrà collegata alla VM del nodo originale. Questo errore può verificarsi quando si trasferisce un disco da un nodo a un altro. ad esempio:

```console
[Warning] AttachVolume.Attach failed for volume “pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9” : Attach volume “kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" to instance “/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-0” failed with compute.VirtualMachinesClient#CreateOrUpdate: Failure sending request: StatusCode=0 -- Original Error: autorest/azure: Service returned an error. Status= Code=“ConflictingUserInput” Message=“Disk ‘/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/disks/kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9’ cannot be attached as the disk is already owned by VM ‘/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-1’.”
```

Questo problema è stato risolto nelle versioni seguenti di Kubernetes:

| Versione di Kubernetes | Versione fissa |
| -- | :--: |
| 1.11 | 1.11.9 o versione successiva |
| 1,12 | 1.12.7 o versione successiva |
| 1,13 | 1.13.4 o versione successiva |
| 1,14 e versioni successive | N/D |

Se si usa una versione di Kubernetes che non ha la correzione per questo problema, è possibile attenuare il problema scollegando manualmente il disco.

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Errore di scollegamento del disco di Azure che causa potenziali problemi di race condition e elenco di dischi dati non validi

Quando non è possibile scollegare un disco di Azure, il disco verrà ritentato fino a sei volte per scollegare il disco con il backup esponenziale. Verrà inoltre tenuto un blocco a livello di nodo nell'elenco dei dischi dati per circa 3 minuti. Se l'elenco dei dischi viene aggiornato manualmente durante tale periodo di tempo, ad esempio un'operazione di collegamento o scollegamento manuale, l'elenco dei dischi mantenuti dal blocco a livello di nodo sarà obsoleto e l'instabilità della macchina virtuale del nodo.

Questo problema è stato risolto nelle versioni seguenti di Kubernetes:

| Versione di Kubernetes | Versione fissa |
| -- | :--: |
| 1,12 | 1.12.9 o versione successiva |
| 1,13 | 1.13.6 o versione successiva |
| 1,14 | 1.14.2 o versione successiva |
| 1,15 e versioni successive | N/D |

Se si usa una versione di Kubernetes che non ha la correzione per questo problema e la macchina virtuale del nodo ha un elenco di dischi obsoleti, è possibile attenuare il problema scollegando tutti i dischi non esistenti dalla macchina virtuale come un'unica operazione bulk. **I dischi scollegati singolarmente non esistenti potrebbero non riuscire.**


### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Un numero elevato di dischi di Azure causa collegamento/scollegamento lento

Quando il numero di dischi di Azure collegati a una macchina virtuale del nodo è maggiore di 10, le operazioni di collegamento e scollegamento possono risultare lente. Questo è un problema noto e al momento non sono disponibili soluzioni alternative.

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Errore di scollegamento del disco di Azure che causa la potenziale VM del nodo in stato di errore

In alcuni casi Edge, una scollegamento del disco di Azure potrebbe non riuscire e lasciare la macchina virtuale del nodo in stato di errore.

Questo problema è stato risolto nelle versioni seguenti di Kubernetes:

| Versione di Kubernetes | Versione fissa |
| -- | :--: |
| 1,12 | 1.12.10 o versione successiva |
| 1,13 | 1.13.8 o versione successiva |
| 1,14 | 1.14.4 o versione successiva |
| 1,15 e versioni successive | N/D |

Se si usa una versione di Kubernetes che non ha la correzione per questo problema e la macchina virtuale del nodo è in uno stato di errore, è possibile attenuare il problema aggiornando manualmente lo stato della macchina virtuale usando uno dei seguenti:

* Per un cluster basato su set di disponibilità:
    ```console
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* Per un cluster basato su VMSS:
    ```console
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Risoluzione dei problemi relativi a File di Azure e AKS

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Quali sono le versioni stabili consigliate di Kubernetes per file di Azure?
 
| Versione di Kubernetes | Versione consigliata |
| -- | :--: |
| 1,12 | 1.12.6 o versione successiva |
| 1,13 | 1.13.4 o versione successiva |
| 1,14 | 1.14.0 o versione successiva |

### <a name="what-versions-of-kubernetes-have-azure-files-support-on-the-sovereign-cloud"></a>Quali versioni di Kubernetes hanno supporto File di Azure nel cloud sovrano?

| Versione di Kubernetes | Versione consigliata |
| -- | :--: |
| 1,12 | 1.12.0 o versione successiva |
| 1,13 | 1.13.0 o versione successiva |
| 1,14 | 1.14.0 o versione successiva |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Quali sono i mountOptions predefiniti quando si usa File di Azure?

Impostazioni consigliate:

| Versione di Kubernetes | valore fileMode e dirMode|
| -- | :--: |
| 1.12.0-1.12.1 | 0755 |
| 1.12.2 e versioni successive | 0777 |

Se si usa un cluster con Kuberetes versione 1.8.5 o successiva e si crea dinamicamente il volume permanente con una classe di archiviazione, è possibile specificare le opzioni di montaggio nell'oggetto classe di archiviazione. L'esempio seguente imposta *0777*:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

Altre impostazioni di *mountOptions* utili:

* *mfsymlinks* renderà file di Azure Mount (CIFS) supporta i collegamenti simbolici
* *nobrl* impedisce l'invio di richieste di blocco di intervalli di byte al server. Questa impostazione è necessaria per alcune applicazioni che si interrompono con blocchi di intervallo di byte obbligatori in stile CIFS. La maggior parte dei server CIFS non supporta ancora la richiesta di blocchi di intervallo di byte di avviso. Se non si usa *nobrl*, le applicazioni che si interrompono con blocchi di intervallo di byte obbligatori dello stile CIFS possono causare messaggi di errore simili ai seguenti:
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Errore "Impossibile modificare le autorizzazioni" quando si utilizza File di Azure

Quando si esegue PostgreSQL nel plug-in File di Azure, è possibile che venga visualizzato un errore simile al seguente:

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

Questo errore è causato dal plug-in File di Azure usando il protocollo CIFS/SMB. Quando si usa il protocollo CIFS/SMB, non è stato possibile modificare le autorizzazioni di file e directory dopo il montaggio.

Per risolvere questo problema, usare il *sottopercorso* insieme al plug-in dischi di Azure. 

> [!NOTE] 
> Per il tipo di disco ext3/4, dopo la formattazione del disco è presente una directory persa e trovata.

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>File di Azure presenta una latenza elevata rispetto al disco di Azure durante la gestione di molti file di piccole dimensioni

In alcuni casi, ad esempio la gestione di molti file di piccole dimensioni, è possibile che si verifichi una latenza elevata quando si usa File di Azure rispetto al disco di Azure.

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>Errore durante l'abilitazione dell'impostazione "Consenti accesso Consenti accesso da rete selezionata" per l'account di archiviazione

Se si abilita *Consenti l'accesso da rete selezionata* in un account di archiviazione usato per il provisioning dinamico in AKS, viene ricevuto un errore quando AKS crea una condivisione file:

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

Questo errore è dovuto al fatto che Kubernetes *persistentvolume-controller* non si trova nella rete scelta quando l'impostazione *Consenti l'accesso dalla rete selezionata*.

Per attenuare il problema, è possibile usare il [provisioning statico con file di Azure](azure-files-volume.md).

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Non è possibile rimontare File di Azure in Windows Pod

Se un pod Windows con un File di Azure montaggio viene eliminato e quindi pianificato per essere ricreato nello stesso nodo, il montaggio avrà esito negativo. Questo errore si verifica a causa della mancata esecuzione del comando `New-SmbGlobalMapping` perché il montaggio del File di Azure è già montato nel nodo.

Ad esempio, potrebbe essere visualizzato un errore simile al seguente:

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

Questo problema è stato risolto nelle versioni seguenti di Kubernetes:

| Versione di Kubernetes | Versione fissa |
| -- | :--: |
| 1,12 | 1.12.6 o versione successiva |
| 1,13 | 1.13.4 o versione successiva |
| 1,14 e versioni successive | N/D |

### <a name="azure-files-mount-fails-due-to-storage-account-key-changed"></a>Il montaggio File di Azure non riesce a causa della modifica della chiave dell'account di archiviazione

Se la chiave dell'account di archiviazione è cambiata, è possibile che vengano visualizzati File di Azure errori di montaggio.

È possibile attenuare il problema aggiornando manualmente il campo *azurestorageaccountkey* in Secret file di Azure con la chiave dell'account di archiviazione con codifica Base64.

Per codificare la chiave dell'account di archiviazione in Base64, è possibile usare `base64`. ad esempio:

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Per aggiornare il file Secret di Azure, usare `kubectl edit secret`. ad esempio:

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Dopo alcuni minuti, il nodo Agent tenterà di ritentare il montaggio del file di Azure con la chiave di archiviazione aggiornata.
