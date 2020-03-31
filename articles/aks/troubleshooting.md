---
title: Risolvere i problemi comuni del servizio Azure Kubernetes
description: Informazioni su come individuare e risolvere i problemi comuni quando si usa il servizio Azure Kubernetes
services: container-service
author: sauryadas
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: saudas
ms.openlocfilehash: 7bdabf2ec109fe96c28185bd1a2a680ce19c2650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368333"
---
# <a name="aks-troubleshooting"></a>Risoluzione dei problemi di servizio Azure Kubernetes

Quando si creano o gestiscono cluster del servizio Azure Kubernetes (AKS), in alcuni casi potrebbero verificarsi problemi. In questo articolo sono descritti alcuni problemi comuni e i passaggi per risolverli.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>In generale, dove è possibile trovare informazioni sul debug di problemi di Kubernetes?

Consultare la [guida ufficiale per la risoluzione dei problemi dei cluster di Kubernetes](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
È disponibile anche una [guida alla risoluzione dei problemi](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md) pubblicata da un tecnico Microsoft per la risoluzione dei problemi relativi a pod, nodi, cluster e altre funzionalità.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Durante la creazione o l’aggiornamento, viene visualizzato un errore di tipo “quota superata”. Cosa devo fare? 

È necessario [richiedere i core](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Qual è l'impostazione del numero massimo di pod per nodo per il servizio Azure Kubernetes?

Il numero massimo di pod per nodo è 30 per impostazione predefinita se si distribuisce un cluster del servizio Azure Kubernetes nel portale di Azure.
Il numero massimo di pod per nodo è 110 per impostazione predefinita se si distribuisce un cluster del servizio Azure Kubernetes nell’interfaccia della riga di comando di Azure. Assicurarsi di usare la versione più recente dell’interfaccia della riga di comando di Azure. Questa impostazione predefinita può essere modificata usando il flag `–-max-pods` nel comando `az aks create`.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Viene visualizzato l'errore "insufficientSubnetSize" durante la distribuzione di un cluster del servizio Azure Kubernetes con funzionalità di rete avanzate. Cosa devo fare?

Se viene utilizzato Azure CNI (rete avanzata), AKS alloca gli indirizzi IP in base ai "max-pod" per nodo configurato. In base ai pod max configurati per nodo, la dimensione della subnet deve essere maggiore del prodotto del numero di nodi e dell'impostazione del pod max per nodo. La seguente equazione descrive questo:

Dimensioni della subnet > numero di nodi nel cluster (tenendo conto dei requisiti di scalabilità futuri) - max pod per set di nodi.

Per altre informazioni, vedere [Pianificare l'indirizzamento IP per il cluster](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Il pod è bloccato in modalità “CrashLoopBackOff”. Cosa devo fare?

Potrebbero esistere vari motivi per cui il modo rimane bloccato in tale modalità. Potrebbe essere opportuno verificare:

* Il pod stesso con `kubectl describe pod <pod-name>`.
* I log con `kubectl logs <pod-name>`.

Per altre informazioni su come risolvere i problemi di pod, vedere [Debug delle applicazioni](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Qual è la procedura per abilitare RBAC in un cluster esistente?

Purtroppo, l'abilitazione del controllo degli accessi in base al ruolo (RBAC) nei cluster esistenti non è al momento supportata. È necessario creare in modo esplicito nuovi cluster. Se si usa l'interfaccia della riga di comando, RBAC è abilitato per impostazione predefinita. Se si usa il portale del servizio Azure Kubernetes, nel flusso di lavoro di creazione è disponibile un pulsante che consente di abilitare RBAC.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>È stato creato un cluster con RBAC abilitato tramite l'interfaccia della riga di comando di Azure con le impostazioni predefinite o il portale di Azure e nel dashboard di Kubernetes vengono visualizzati diversi avvisi. Il dashboard non generava avvisi. Cosa devo fare?

Il motivo per cui vengono visualizzati avvisi nel dashboard è che ora il cluster è abilitato con RBAC e l'accesso è stato disabilitato per impostazione predefinita. In generale, questo approccio è considerato una procedura appropriata perché l'esposizione predefinita del dashboard a tutti gli utenti del cluster può comportare rischi per la sicurezza. Se si vuole comunque abilitare il dashboard, seguire le indicazioni riportate in questo [post di blog](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Non è possibile connettersi al dashboard. Cosa devo fare?

Il modo più semplice per accedere al servizio all'esterno del cluster consiste nell'eseguire `kubectl proxy`, per inoltrare le richieste inviate a localhost sulla porta 8001 al server API Kubernetes. Da qui, il server API può usare un proxy per il servizio: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Se il dashboard di Kubernetes non è `kube-proxy` visualizzato, verificare se il pod è in esecuzione nello `kube-system` spazio dei nomi. Se non è esecuzione, eliminare il pod in modo che venga riavviato.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Non è possibile ottenere i log usando i log di kubectl o non è possibile connettersi al server API. Viene visualizzato il messaggio "Errore dal server: errore di composizione back-end: composizione tcp...". Cosa devo fare?

Assicurarsi che il gruppo di sicurezza di rete predefinito non sia stato modificato e che entrambe le porte 22 e 9000 siano aperte per la connessione al server API. Controllare se `tunnelfront` il pod è in esecuzione nello `kubectl get pods --namespace kube-system` spazio dei nomi del sistema *kube* utilizzando il comando . In caso contrario, forzare l’eliminazione del pod per riavviarlo.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Sto tentando di aggiornare o ridimensionare e viene visualizzato un "messaggio: Changing property 'imageReference' is not allowed" error. Come si risolve il problema?

È possibile che questo errore venga visualizzato in seguito alla modifica di tag nei nodi dell'agente all'interno del cluster del servizio Azure Kubernetes. La modifica e l'eliminazione di tag e altre proprietà delle risorse nel gruppo di risorse MC_* può causare risultati imprevisti. La modifica delle risorse nel gruppo MC_* del cluster del servizio Kubernetes di Azure è una violazione dell'obiettivo del livello di servizio (SLO).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Ricevo errori che il cluster è in stato di errore e l'aggiornamento o la scalabilità non funzionerà fino a quando non viene risolto

*L'assistenza per la risoluzione dei problemi èhttps://aka.ms/aks-cluster-failed*

Questo errore si verifica quando i cluster entrano in uno stato di errore per diversi motivi. Attenersi alla procedura seguente per risolvere lo stato di errore del cluster prima di ripetere l'operazione non riuscita in precedenza:

1. Finché il cluster `failed` non `upgrade` `scale` è in stato di stato e le operazioni non avranno esito positivo. I problemi e le soluzioni principali comuni includono:Common root issues and resolutions include:
    * Scalabilità con quota di **calcolo (CRP) insufficiente.** Per risolvere il problema, ridimensionare innanzitutto il cluster a uno stato obiettivo stabile all'interno della quota. Seguire quindi questi [passaggi per richiedere un aumento](../azure-portal/supportability/resource-manager-core-quotas-request.md) della quota di calcolo prima di tentare di aumentare nuovamente oltre i limiti di quota iniziali.
    * Scalabilità di un cluster con rete avanzata e **risorse di subnet (rete) insufficienti.** Per risolvere il problema, ridimensionare innanzitutto il cluster a uno stato obiettivo stabile all'interno della quota. Seguire quindi [questi passaggi per richiedere un aumento](../azure-resource-manager/templates/error-resource-quota.md#solution) della quota di risorse prima di tentare di aumentare nuovamente oltre i limiti di quota iniziali.
2. Una volta risolta la causa sottostante dell'errore di aggiornamento, il cluster deve essere in uno stato di esito positivo. Una volta verificato uno stato riuscito, ripetere l'operazione originale.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Si ricevono errori durante il tentativo di aggiornamento o scalabilità dello stato in corso di aggiornamento del cluster o aggiornamento non riuscito

*L'assistenza per la risoluzione dei problemi èhttps://aka.ms/aks-pending-upgrade*

Le operazioni di aggiornamento e scalabilità in un cluster con un pool a nodo singolo o un cluster con [pool di più nodi](use-multiple-node-pools.md) si escludono a vicenda. Non è possibile aggiornare e ridimensionare contemporaneamente un cluster o un pool di nodi. Al contrario, ogni tipo di operazione deve essere completato nella risorsa di destinazione prima della richiesta successiva sulla stessa risorsa. Di conseguenza, le operazioni sono limitate quando le operazioni di aggiornamento o ridimensionamento attive sono in corso o tentate e successivamente non sono riuscite. 

Per diagnosticare l'esecuzione `az aks show -g myResourceGroup -n myAKSCluster -o table` del problema per recuperare lo stato dettagliato nel cluster. Sulla base del risultato:

* Se il cluster esegue l'aggiornamento attivo, attendere il termine dell'operazione. Se l'operazione ha esito positivo, ripetere l'operazione non riuscita in precedenza.
* Se l'aggiornamento del cluster non è riuscito, seguire i passaggi descritti nella sezione precedente.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>È possibile spostare il cluster in una sottoscrizione diversa o con il cluster in un nuovo tenant?

Se il cluster AKS è stato spostato in una sottoscrizione diversa o il cluster proprietario della sottoscrizione in un nuovo tenant, il cluster perderà funzionalità a causa della perdita delle assegnazioni di ruolo e dei diritti delle entità servizio. **AKS non supporta** lo spostamento di cluster tra sottoscrizioni o tenant a causa del vincolo this.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Ricevo errori durante il tentativo di usare funzionalità che richiedono set di scalabilità di macchine virtuali

*Questa assistenza per la risoluzione dei problemi è diretta da aka.ms/aks-vmss-enablement*

È possibile che vengano visualizzati errori che indicano che il cluster AKS non si trova in un set di scalabilità di macchine virtuali, ad esempio l'esempio seguente:You may receive errors that indicate your AKS cluster is not on a virtual machine scale set, such as the following example:

**AgentPool 'agentpool' has set auto scaling as enabled but is not on Virtual Machine Scale Sets**

Per usare funzionalità come il cluster autoscaler o più pool di nodi, è necessario creare cluster AKS che utilizzano set di scalabilità di macchine virtuali. Vengono restituiti errori se si tenta di usare funzionalità che dipendono da set di scalabilità di macchine virtuali e si ha come destinazione un cluster AKS con set di scalabilità di macchine normali e non virtuali.

Seguire i passaggi prima di *iniziare* il documento appropriato per creare correttamente un cluster AKS:

* [Usare il cluster autoscaler](cluster-autoscaler.md)
* [Creare e utilizzare pool di più nodiCreate and use multiple node pools](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Quali restrizioni di denominazione vengono applicate per le risorse e i parametri AKS?

*Questa assistenza per la risoluzione dei problemi è diretta da aka.ms/aks-naming-rules*

Le restrizioni di denominazione vengono implementate sia dalla piattaforma Azure che da AKS. Se il nome o un parametro interrompe una di queste restrizioni, viene restituito un errore che chiede di fornire un input diverso. Si applicano le seguenti linee guida comuni per la denominazione:

* I nomi dei cluster devono essere compresi tra 1 e 63 caratteri. Gli unici caratteri consentiti sono lettere, numeri, trattini e caratteri di sottolineatura. Il primo e l'ultimo carattere devono essere una lettera o un numero.
* Il nome del gruppo *di risorse MC_* AKS combina il nome del gruppo di risorse e il nome della risorsa. La sintassi generata automaticamente di `MC_resourceGroupName_resourceName_AzureRegion` non deve essere maggiore di 80 caratteri. Se necessario, ridurre la lunghezza del nome del gruppo di risorse o il nome del cluster AKS.
* Il *dnsPrefix* deve iniziare e terminare con valori alfanumerici e deve essere compreso tra 1 e 54 caratteri. I caratteri validi includono valori alfanumerici e trattini (-). Il *dnsPrefix* non può includere caratteri speciali, ad esempio un punto (.).

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Si ricevono errori quando si tenta di creare, aggiornare, ridimensionare, eliminare o aggiornare il cluster, tale operazione non è consentita mentre è in corso un'altra operazione.

*Questa assistenza per la risoluzione dei problemi è diretta da aka.ms/aks-pending-operation*

Le operazioni del cluster sono limitate quando un'operazione precedente è ancora in corso. Per recuperare uno stato dettagliato del `az aks show -g myResourceGroup -n myAKSCluster -o table` cluster, utilizzare il comando . Usare il proprio gruppo di risorse e il nome del cluster AKS in base alle esigenze.

In base all'output dello stato del cluster:

* Se il cluster si trova in uno stato di provisioning diverso da *Operazione riuscita* o Operazione non *riuscita*, attendere che l'operazione (*Aggiornamento/Aggiornamento/Creazione/Ridimensionamento/Eliminazione/Migrazione*) termini. Al termine dell'operazione precedente, riprovare l'ultima operazione del cluster.

* Se il cluster ha un aggiornamento non riuscito, seguire i passaggi descritti [Se si ricevono errori che indicano che il cluster è in stato di errore e l'aggiornamento o il ridimensionamento non funzioneranno fino a quando non verrà risolto](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>Ricevo errori che l'entità servizio non è stata trovata quando si tenta di creare un nuovo cluster senza passarne uno esistente.

Quando si crea un cluster AKS, è necessaria un'entità servizio per creare risorse per conto dell'utente. AKS offre la possibilità di crearne uno nuovo al momento della creazione del cluster, ma ciò richiede che Azure Active Directory propaghi completamente la nuova entità servizio in un tempo ragionevole per avere il cluster riuscito nella creazione. Quando questa propagazione richiede troppo tempo, la convalida del cluster non riuscirà a creare perché non riesce a trovare un'entità servizio disponibile per eseguire questa operazione. 

Utilizzare le seguenti soluzioni alternative per questo:
1. Usare un'entità servizio esistente che è già stata propagata tra le aree ed esiste per passare in AKS in fase di creazione del cluster.
2. Se si usano script di automazione, aggiungere ritardi tra la creazione dell'entità servizio e la creazione di cluster AKS.
3. Se si usa il portale di Azure, tornare alle impostazioni del cluster durante la creazione e ripetere la pagina di convalida dopo alcuni minuti.

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>Ricevo errori dopo aver limitato il traffico in uscita

Quando si limita il traffico in uscita da un cluster AKS, sono disponibili porte in uscita/regole di rete [obbligatorie e facoltative](limit-egress-traffic.md) e regole FQDN/applicazione per AKS. Se le impostazioni sono in conflitto con una di queste `kubectl` regole, potrebbe non essere possibile eseguire alcuni comandi. È inoltre possibile che vengano visualizzati errori durante la creazione di un cluster AKS.

Verificare che le impostazioni non siano in conflitto con le porte/regole di rete obbligatorie o facoltative e con il nome di dominio completo e le regole di applicazione.

## <a name="azure-storage-and-aks-troubleshooting"></a>Azure Storage and AKS Troubleshooting

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>Quali sono le versioni stabili consigliate di Kubernetes per il disco di Azure? 

| Versione Kubernetes | Versione consigliata |
| -- | :--: |
| 1.12 | 1.12.9 o versione successiva |
| 1.13 | 1.13.6 o versione successiva |
| 1,14 | 1.14.2 o versione successiva |


### <a name="what-versions-of-kubernetes-have-azure-disk-support-on-the-sovereign-cloud"></a>Quali versioni di Kubernetes supportano il disco di Azure nel cloud sovrano?

| Versione Kubernetes | Versione consigliata |
| -- | :--: |
| 1.12 | 1.12.0 o versione successiva |
| 1.13 | 1.13.0 o versione successiva |
| 1,14 | 1.14.0 o versione successiva |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>WaitForAttach non riuscito per il disco di Azure: analisi "/dev/disk/azure/scsi1/lun1": sintassi non valida

In Kubernetes versione 1.10, MountVolume.WaitForAttach potrebbe non riuscire con un rimontaggio del disco di Azure.In Kubernetes version 1.10, MountVolume.WaitForAttach may fail with an Azure Disk remount.

In Linux, è possibile che venga visualizzato un errore di formato DevicePath non corretto. Ad esempio:

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

In Windows, è possibile che venga visualizzato un errore relativo al numero di DevicePath(LUN) errato. Ad esempio:

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

Questo problema è stato risolto nelle seguenti versioni di Kubernetes:

| Versione Kubernetes | Versione fissa |
| -- | :--: |
| 1,10 | 1.10.2 o versione successiva |
| 1.11 | 1.11.0 o versione successiva |
| 1.12 e versioni successive | N/D |

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Errore durante l'impostazione di uid e gid in mountOptions per Azure DiskFailure when setting uid and gid in mountOptions for Azure Disk

Il disco di Azure usa il file system ext4,xfs per impostazione predefinita e le opzioni mount, ad esempio uid x,gid-x non possono essere impostate in fase di montaggio. Se, ad esempio, si tenta di impostare mountOptions uid-999,gid-999, viene visualizzato un errore simile al seguente:

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

È possibile attenuare il problema eseguendo una delle operazioni seguenti:

* [Configurare il contesto](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) di sicurezza per un pod impostando uid in runAsUser e gid in fsGroup. Ad esempio, la seguente impostazione imposterà pod run come root, rendendolo accessibile a qualsiasi file:

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
  > Poiché gid e uid sono montati come root o 0 per impostazione predefinita. Se gid o uid sono impostati come non-root, ad esempio 1000, Kubernetes utilizzerà `chown` per modificare tutte le directory e i file in quel disco. Questa operazione può richiedere molto tempo e può rendere il montaggio del disco molto lento.

* Usare `chown` in initContainers per impostare gid e uid.Use in initContainers to set gid and uid. Ad esempio:

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="error-when-deleting-azure-disk-persistentvolumeclaim-in-use-by-a-pod"></a>Errore durante l'eliminazione del disco di Azure PersistentVolumeClaim in uso da un podError when deleting Azure Disk PersistentVolumeClaim in use by a pod

Se si tenta di eliminare un disco di Azure PersistentVolumeClaim utilizzato da un pod, è possibile che venga visualizzato un errore. Ad esempio:

```console
$ kubectl describe pv pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06
...
Message:         disk.DisksClient#Delete: Failure responding to request: StatusCode=409 -- Original Error: autorest/azure: Service returned an error. Status=409 Code="OperationNotAllowed" Message="Disk kubernetes-dynamic-pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06 is attached to VM /subscriptions/{subs-id}/resourceGroups/MC_markito-aks-pvc_markito-aks-pvc_westus/providers/Microsoft.Compute/virtualMachines/aks-agentpool-25259074-0."
```

In Kubernetes versione 1.10 e successive, è disponibile una funzionalità di protezione PersistentVolumeClaim abilitata per impostazione predefinita per evitare questo errore. Se si utilizza una versione di Kubernetes che non dispone della correzione per questo problema, è possibile ridurre questo problema eliminando il pod utilizzando PersistentVolumeClaim prima di eliminare persistentVolumeClaim.


### <a name="error-cannot-find-lun-for-disk-when-attaching-a-disk-to-a-node"></a>Errore "Impossibile trovare Lun per disco" quando si collega un disco a un nodo

Quando si collega un disco a un nodo, è possibile che venga visualizzato il seguente errore:

```console
MountVolume.WaitForAttach failed for volume "pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6" : Cannot find Lun for disk kubernetes-dynamic-pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6
```

Questo problema è stato risolto nelle seguenti versioni di Kubernetes:

| Versione Kubernetes | Versione fissa |
| -- | :--: |
| 1,10 | 1.10.10 o versione successiva |
| 1.11 | 1.11.5 o versione successiva |
| 1.12 | 1.12.3 o versione successiva |
| 1.13 | 1.13.0 o versione successiva |
| 1.14 e versioni successive | N/D |

Se si utilizza una versione di Kubernetes che non dispone della correzione per questo problema, è possibile attenuare il problema attendendo alcuni minuti e riprovare.

### <a name="azure-disk-attachdetach-failure-mount-issues-or-io-errors-during-multiple-attachdetach-operations"></a>Errore di collegamento/scollegamento del disco di Azure, problemi di montaggio o errori di I/O durante più operazioni di collegamento/scollegamentoAzure Disk attach/detach failure, mount issues, or I/O errors during multiple attach/detach operations

A partire da Kubernetes versione 1.9.2, quando si eseguono più operazioni di collegamento/scollegamento in parallelo, è possibile che si verifichino i seguenti problemi del disco a causa di una cache di macchina virtuale dirty:Starting in Kubernetes version 1.9.2, when running multiple attach/detach operations in parallel, you may see the following disk issues due to a dirty VM cache:

* Errori di collegamento/scollegamento del disco
* Errori di I/O del disco
* Disconnessione del disco imprevista dalla macchina virtuale
* VM in esecuzione in stato di errore a causa del collegamento di disco non esistente

Questo problema è stato risolto nelle seguenti versioni di Kubernetes:

| Versione Kubernetes | Versione fissa |
| -- | :--: |
| 1,10 | 1.10.12 o versione successiva |
| 1.11 | 1.11.6 o versione successiva |
| 1.12 | 1.12.4 o versione successiva |
| 1.13 | 1.13.0 o versione successiva |
| 1.14 e versioni successive | N/D |

Se si utilizza una versione di Kubernetes che non dispone della correzione per questo problema, è possibile attenuare il problema provando quanto segue:

* Se un disco è in attesa di staccarsi per un lungo periodo di tempo, provare a scollegare il disco manualmente

### <a name="azure-disk-waiting-to-detach-indefinitely"></a>Disco di Azure in attesa di scollegamento a tempo indeterminatoAzure Disk waiting to detach indefinitely

In alcuni casi, se un'operazione di sconnessione del disco di Azure non riesce al primo tentativo, non ritenterà l'operazione di scollegamento e rimarrà collegata alla macchina virtuale del nodo originale. Questo errore può verificarsi quando si sposta un disco da un nodo a un altro. Ad esempio:

```console
[Warning] AttachVolume.Attach failed for volume "pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" : Attach volume "kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" to instance "/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-0" failed with compute.VirtualMachinesClient#CreateOrUpdate: Failure sending request: StatusCode=0 -- Original Error: autorest/azure: Service returned an error. Status= Code="ConflictingUserInput" Message="Disk '/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/disks/kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9' cannot be attached as the disk is already owned by VM '/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-1'."
```

Questo problema è stato risolto nelle seguenti versioni di Kubernetes:

| Versione Kubernetes | Versione fissa |
| -- | :--: |
| 1.11 | 1.11.9 o versione successiva |
| 1.12 | 1.12.7 o versione successiva |
| 1.13 | 1.13.4 o versione successiva |
| 1.14 e versioni successive | N/D |

Se si utilizza una versione di Kubernetes che non dispone della correzione per questo problema, è possibile attenuare il problema scollegando manualmente il disco.

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Errore di scollegamento del disco di Azure che causa potenziali problemi di race condition e elenco di dischi dati non validiAzure Disk detach failure leading to potential race condition issue and invalid data disk list

Quando un disco di Azure non riesce a disconnettersi, verrà eseguito un nuovo tentativo fino a sei volte per scollegare il disco usando il back off esponenziale. Manterrà anche un blocco a livello di nodo nell'elenco dei dischi dati per circa 3 minuti. Se l'elenco dei dischi viene aggiornato manualmente durante tale periodo di tempo, ad esempio un'operazione di collegamento o disconnessione manuale, l'elenco dei dischi mantenuto dal blocco a livello di nodo sarà obsoleto e causerà instabilità nella macchina virtuale del nodo.

Questo problema è stato risolto nelle seguenti versioni di Kubernetes:

| Versione Kubernetes | Versione fissa |
| -- | :--: |
| 1.12 | 1.12.9 o versione successiva |
| 1.13 | 1.13.6 o versione successiva |
| 1,14 | 1.14.2 o versione successiva |
| 1.15 e versioni successive | N/D |

Se si utilizza una versione di Kubernetes che non dispone della correzione per questo problema e la macchina virtuale del nodo dispone di un elenco di dischi obsoleti, è possibile ridurre il problema scollegando tutti i dischi non esistenti dalla macchina virtuale come una singola operazione in blocco. **Scollegando singolarmente dischi non esistenti potrebbe non riuscire.**


### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Un numero elevato di dischi di Azure causa un collegamento/scollegamento lento

Quando il numero di dischi di Azure collegati a una macchina virtuale del nodo è maggiore di 10, le operazioni di collegamento e disconnessione potrebbero essere lente. Questo problema è noto e al momento non sono disponibili soluzioni alternative.

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Errore di scollegamento del disco di Azure che porta a una macchina virtuale del nodo potenziale in stato di erroreAzure Disk detach failure leading to potential node VM in failed state

In alcuni casi limite, una disconnessione del disco di Azure potrebbe non riuscire parzialmente e lasciare la macchina virtuale del nodo in uno stato di errore.

Questo problema è stato risolto nelle seguenti versioni di Kubernetes:

| Versione Kubernetes | Versione fissa |
| -- | :--: |
| 1.12 | 1.12.10 o versione successiva |
| 1.13 | 1.13.8 o versione successiva |
| 1,14 | 1.14.4 o versione successiva |
| 1.15 e versioni successive | N/D |

Se si utilizza una versione di Kubernetes che non dispone della correzione per questo problema e la macchina virtuale del nodo è in stato di errore, è possibile ridurre il problema aggiornando manualmente lo stato della macchina virtuale utilizzando uno dei seguenti:

* Per un cluster basato su set di disponibilità:For an availability set-based cluster:
    ```azurecli
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* Per un cluster basato su VMSS:For a VMSS-based cluster:
    ```azurecli
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Azure Files and AKS Troubleshooting

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Quali sono le versioni stabili consigliate di Kubernetes per i file di Azure?
 
| Versione Kubernetes | Versione consigliata |
| -- | :--: |
| 1.12 | 1.12.6 o versione successiva |
| 1.13 | 1.13.4 o versione successiva |
| 1,14 | 1.14.0 o versione successiva |

### <a name="what-versions-of-kubernetes-have-azure-files-support-on-the-sovereign-cloud"></a>Quali versioni di Kubernetes supportano File di Azure nel cloud sovrano?

| Versione Kubernetes | Versione consigliata |
| -- | :--: |
| 1.12 | 1.12.0 o versione successiva |
| 1.13 | 1.13.0 o versione successiva |
| 1,14 | 1.14.0 o versione successiva |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Quali sono i mountOptions predefiniti quando si usano File di Azure?

Impostazioni consigliate:

| Versione Kubernetes | valore di fileMode e dirMode|
| -- | :--: |
| 1.12.0 - 1.12.1 | 0755 |
| 1.12.2 e versioni successive | 0777 |

Se si usa un cluster con Kubernetes versione 1.8.5 o successiva e si crea dinamicamente il volume persistente con una classe di archiviazione, è possibile specificare le opzioni di montaggio nell'oggetto classe di archiviazione. L'esempio seguente imposta *0777*:

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

Alcune impostazioni *mountOptions* utili aggiuntive:

* *mfsymlinks* farà montare (cifs) i file di Azure supporteranno i collegamenti simbolici
* *nobrl* impedirà l'invio di richieste di blocco dell'intervallo di byte al server. Questa impostazione è necessaria per alcune applicazioni che interrompono con cifs stile blocchi dell'intervallo di byte obbligatorio. La maggior parte dei server cifs non supporta ancora la richiesta di blocchi di intervalli di byte consultivi. In caso *nobrl*contrario, le applicazioni che interrompono con i blocchi dell'intervallo di byte obbligatorio con stile cifs possono causare messaggi di errore simili a:
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Errore "Impossibile modificare le autorizzazioni" quando si utilizza file di AzureError "could not change permissions" when using Azure Files

Quando si esegue PostgreSQL nel plug-in File di Azure, è possibile che venga visualizzato un errore simile al:When running PostgreSQL on the Azure Files plugin, you may see an error similar to:

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

Questo errore è causato dal plug-in File di Azure usando il protocollo cifs/SMB. Quando si utilizza il protocollo cifs/SMB, le autorizzazioni per file e directory non possono essere modificate dopo il montaggio.

Per risolvere questo problema, utilizzare subPath con il *plug-in* disco di Azure. 

> [!NOTE] 
> Per il tipo di disco ext3/4, si trova una directory persa, dopo la formattazione del disco.

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>File di Azure ha un'elevata latenza rispetto al disco di Azure quando si gestiscono molti file di piccole dimensioniAzure Files has high latency compared to Azure Disk when handling many small files

In some case, such as handling many small files, you may experience high latency when using Azure Files when compared to Azure Disk.

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>Errore durante l'abilitazione dell'impostazione "Consenti accesso Consenti accesso dalla rete selezionata" nell'account di archiviazioneError when enabling "Allow access allow access from selected network" setting on storage account

Se si abilita *L'autorizzazione l'accesso dalla rete selezionata* in un account di archiviazione usato per il provisioning dinamico in AKS, verrà visualizzato un errore quando AKS crea una condivisione file:If you enable allow access from selected network on a storage account that is used for dynamic provisioning in AKS, you will get an error when AKS creates a file share:

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

Questo errore è dovuto al fatto che il *persistentvolume-controller* Kubernetes non è presente sulla rete scelta quando l'impostazione *consente l'accesso dalla rete selezionata.*

È possibile ridurre il problema usando il provisioning statico con File di [Azure.](azure-files-volume.md)

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>File di Azure non riesce a rimontare nel contenitore di WindowsAzure Files fails to remount in Windows pod

Se un pod di Windows con un montaggio File di Azure viene eliminato e quindi pianificato per essere ricreato nello stesso nodo, il montaggio avrà esito negativo. Questo errore è `New-SmbGlobalMapping` dovuto all'esito negativo del comando poiché il montaggio di File di Azure è già installato nel nodo.

Ad esempio, è possibile che venga visualizzato un errore simile al seguente:For example, you may see an error similar to:

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

Questo problema è stato risolto nelle seguenti versioni di Kubernetes:

| Versione Kubernetes | Versione fissa |
| -- | :--: |
| 1.12 | 1.12.6 o versione successiva |
| 1.13 | 1.13.4 o versione successiva |
| 1.14 e versioni successive | N/D |

### <a name="azure-files-mount-fails-due-to-storage-account-key-changed"></a>Montaggio dei file di Azure non riesce a causa della modifica della chiave dell'account di archiviazioneAzure Files mount fails due to storage account key changed

Se la chiave dell'account di archiviazione è stata modificata, è possibile che vengano visualizzati errori di montaggio file di Azure.If your storage account key has changed, you may see Azure Files mount failures.

È possibile ridurre il problema aggiornando manualmente il campo azurestorageaccountkey manualmente nel segreto di file di Azure con la chiave dell'account di archiviazione con codifica base64.You can mitigate the issue by doing manually updating the *azurestorageaccountkey* field manually in Azure file secret with your base64-encoded storage account key.

Per codificare la chiave dell'account di `base64`archiviazione in base64, è possibile usare . Ad esempio:

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Per aggiornare il file `kubectl edit secret`segreto di Azure, usare . Ad esempio:

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Dopo alcuni minuti, il nodo dell'agente tenterà nuovamente il montaggio del file azure con la chiave di archiviazione aggiornata.

### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>La scalabilità automatica del cluster non viene ridimensionata con errore di errore non è riuscito a correggere le dimensioni dei gruppi di nodi

Se il cluster autoscaler non è scalabile verso l'alto/verso il basso e viene visualizzato un errore simile al seguente nei [registri di autoscaler][view-master-logs]del cluster.

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

Questo errore è dovuto a una condizione di gara di autoscaler del cluster a monte in cui il cluster autoscaler termina con un valore diverso da quello effettivamente presente nel cluster. Per uscire da questo stato, è sufficiente disabilitare e riabilitare il [cluster autoscaler][cluster-autoscaler].

### <a name="slow-disk-attachment-getazuredisklun-takes-10-to-15-minutes-and-you-receive-an-error"></a>Allegato del disco lento, GetAzureDiskLun richiede da 10 a 15 minuti e viene visualizzato un errore

Nelle versioni di Kubernetes **precedenti alla 1.15.0** è possibile che venga visualizzato un errore, ad esempio **Errore WaitForAttach, Impossibile trovare Lun per disco**.  La soluzione per questo è quello di attendere circa 15 minuti e riprovare.

<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md
