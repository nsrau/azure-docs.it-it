---
title: Risolvere i problemi comuni del servizio Azure Kubernetes
description: Informazioni su come individuare e risolvere i problemi comuni quando si usa il servizio Azure Kubernetes
services: container-service
ms.topic: troubleshooting
ms.date: 05/16/2020
ms.openlocfilehash: f9831077d1f2850d39e4ef5e5ba35245f16cd683
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83724995"
---
# <a name="aks-troubleshooting"></a>Risoluzione dei problemi di servizio Azure Kubernetes

Quando si creano o gestiscono cluster del servizio Azure Kubernetes, in alcuni casi possono verificarsi problemi. In questo articolo sono descritti alcuni problemi comuni e i passaggi per risolverli.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>In generale, dove è possibile trovare informazioni sul debug di problemi di Kubernetes?

Consultare la [guida ufficiale per la risoluzione dei problemi dei cluster di Kubernetes](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
È disponibile anche una [guida alla risoluzione dei problemi](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md) pubblicata da un tecnico Microsoft per la risoluzione dei problemi relativi a pod, nodi, cluster e altre funzionalità.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Durante la creazione o l’aggiornamento, viene visualizzato un errore di tipo “quota superata”. Cosa devo fare? 

 [Richiedere altre memorie centrali](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Qual è l'impostazione del numero massimo di pod per nodo per il servizio Azure Kubernetes?

Il numero massimo di pod per nodo è 30 per impostazione predefinita se si distribuisce un cluster del servizio Azure Kubernetes nel portale di Azure.
Il numero massimo di pod per nodo è 110 per impostazione predefinita se si distribuisce un cluster del servizio Azure Kubernetes nell’interfaccia della riga di comando di Azure. Assicurarsi di usare la versione più recente dell’interfaccia della riga di comando di Azure. Questa impostazione può essere modificata usando il flag `–-max-pods` nel comando `az aks create`.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Viene visualizzato l'errore "insufficientSubnetSize" durante la distribuzione di un cluster del servizio Azure Kubernetes con funzionalità di rete avanzate. Cosa devo fare?

Quando si usa il plug-in di rete Azure CNI, il servizio Azure Kubernetes alloca gli indirizzi IP in base al parametro relativo al numero massimo di pod per nodo. La dimensione della subnet deve essere maggiore del numero di nodi in base all'impostazione del numero massimo di pod per nodo. L'equazione seguente descrive questo caso:

Dimensione della subnet > numero di nodi nel cluster (prendendo in considerazione i requisiti di scalabilità futuri) * numero massimo di pod per set di nodi.

Per altre informazioni, vedere [Pianificare l'indirizzamento IP per il cluster](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Il pod è bloccato in modalità “CrashLoopBackOff”. Cosa devo fare?

Potrebbero esistere vari motivi per cui il modo rimane bloccato in tale modalità. Potrebbe essere opportuno verificare:

* Il pod stesso con `kubectl describe pod <pod-name>`.
* I log con `kubectl logs <pod-name>`.

Per altre informazioni su come risolvere i problemi di pod, vedere [Debug delle applicazioni](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-role-based-access-control-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Qual è la procedura per abilitare il controllo degli accessi in base al ruolo (RBAC) in un cluster esistente?

L'abilitazione del controllo degli accessi in base al ruolo (RBAC) nei cluster esistenti non è al momento supportata. Deve essere impostata durante la creazione di nuovi cluster. Il controllo degli accessi in base al ruolo è abilitato per impostazione predefinita quando si usa l'interfaccia della riga di comando, il portale o una versione API successiva alla `2020-03-01`.

## <a name="i-created-a-cluster-with-rbac-enabled-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>È stato creato un cluster con il controllo degli accessi in base al ruolo abilitato e ora vengono visualizzati molti avvisi nel dashboard di Kubernetes. Il dashboard non generava avvisi. Cosa devo fare?

Il motivo degli avvisi è che il cluster è abilitato per il controllo degli accessi in base al ruolo e l'accesso al dashboard è ora limitato per impostazione predefinita. In generale, questo approccio è considerato una procedura appropriata perché l'esposizione predefinita del dashboard a tutti gli utenti del cluster può comportare rischi per la sicurezza. Se si vuole comunque abilitare il dashboard, seguire le indicazioni riportate in questo [post di blog](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Non è possibile connettersi al dashboard. Cosa devo fare?

Il modo più semplice per accedere al servizio all'esterno del cluster consiste nell'eseguire `kubectl proxy`, per inoltrare le richieste inviate a localhost sulla porta 8001 al server API Kubernetes. Da qui, il server API può usare un proxy per il servizio: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/`.

Se non viene visualizzato il dashboard di Kubernetes, controllare se il pod `kube-proxy` è in esecuzione nello spazio dei nomi `kube-system`. Se non è esecuzione, eliminare il pod in modo che venga riavviato.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Non è possibile ottenere i log usando i log di kubectl o non è possibile connettersi al server API. Viene visualizzato un errore simile a "Errore del server: errore durante il contatto con il back-end: contattare tcp…". Cosa devo fare?

Verificare che le porte 22, 9000 e 1194 siano aperte per la connessione al server API. Controllare se il pod `tunnelfront` o `aks-link` è in esecuzione nello spazio dei nomi *kube-system* usando il comando `kubectl get pods --namespace kube-system`. In caso contrario, forzare l’eliminazione del pod per riavviarlo.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Errore `"Changing property 'imageReference' is not allowed"` durante l'aggiornamento o il ridimensionamento. Come si risolve il problema?

È possibile che questo errore venga visualizzato in seguito alla modifica di tag nei nodi dell'agente all'interno del cluster del servizio Azure Kubernetes. Modificare o eliminare tag e altre proprietà delle risorse nel gruppo di risorse MC_* può causare risultati imprevisti. La modifica delle risorse nel gruppo MC_* del cluster del servizio Azure Kubernetes è una violazione dell'obiettivo del livello di servizio (SLO).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Errori relativi al fatto che il cluster è in stato di errore e che l'aggiornamento o il ridimensionamento non funzioneranno finché il problema non verrà risolto

*Questa assistenza per la risoluzione dei problemi è diretta da https://aka.ms/aks-cluster-failed* .

Questo errore si verifica quando i cluster entrano in uno stato di errore per diversi motivi. Per risolvere lo stato di errore del cluster prima di riprovare a eseguire l'operazione precedentemente non riuscita, attenersi alla procedura seguente:

1. Fino a quando il cluster sarà nello stato `failed`, le operazioni di `upgrade` e `scale` avranno esito negativo. I problemi e le soluzioni principali comuni includono:
    * Ridimensionamento con **quota di calcolo insufficiente**. Per risolvere, prima di tutto ridimensionare il cluster fino a uno stato di obiettivo stabile entro la quota. Seguire quindi questa [procedura per richiedere un aumento della quota di calcolo](../azure-portal/supportability/resource-manager-core-quotas-request.md), prima di provare a eseguire l'aumento delle prestazioni oltre i limiti di quota iniziali.
    * Ridimensionamento di un cluster con rete avanzata e **risorse insufficienti per la subnet (rete)** . Per risolvere, prima di tutto ridimensionare il cluster fino a uno stato di obiettivo stabile entro la quota. Seguire quindi questa [procedura per richiedere un aumento della quota di risorse](../azure-resource-manager/templates/error-resource-quota.md#solution), prima di provare a eseguire l'aumento delle prestazioni oltre i limiti di quota iniziali.
2. Dopo aver risolto la causa sottostante dell'errore di aggiornamento, lo stato del cluster dovrebbe essere di esito positivo. Una volta verificato uno stato di esito positivo, ripetere l'operazione originale.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade"></a>Errori durante il tentativo di aggiornamento o ridimensionamento che indicano che il cluster è in aggiornamento o che l'aggiornamento non è riuscito

*Questa assistenza per la risoluzione dei problemi è diretta da https://aka.ms/aks-pending-upgrade* .

 Non è possibile aggiornare e ridimensionare contemporaneamente un cluster o un pool di nodi. Al contrario, ogni tipo di operazione deve essere completata sulla risorsa di destinazione prima dell'esecuzione della richiesta successiva sulla stessa risorsa. Di conseguenza, le operazioni sono limitate quando si verificano o si tenta di eseguire operazioni attive di aggiornamento o ridimensionamento. 

Per facilitare la diagnosi del problema, eseguire `az aks show -g myResourceGroup -n myAKSCluster -o table` in modo da recuperare lo stato dettagliato del cluster. In base al risultato:

* Se il cluster sta aggiornando attivamente, attendere il completamento dell'operazione. In caso di esito positivo, ripetere l'operazione precedentemente non riuscita.
* Se il cluster non è stato aggiornato, seguire i passaggi descritti nella sezione precedente.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>È possibile spostare il cluster in un'altra sottoscrizione o una sottoscrizione con cluster in un nuovo tenant?

Se il cluster del servizio Azure Kubernetes è stato spostato in un'altra sottoscrizione o la sottoscrizione del cluster è stata spostata in un nuovo tenant, il cluster non funzionerà a causa delle autorizzazioni di identità del cluster mancanti. **Il servizio Azure Kubernetes non supporta lo spostamento dei cluster tra sottoscrizioni o tenant** a causa di questo vincolo.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Errori durante il tentativo di usare le funzionalità che richiedono set di scalabilità di macchine virtuali

*Questa assistenza per la risoluzione dei problemi è diretta da aka.ms/aks-vmss-enablement*.

È possibile che vengano visualizzati errori che indicano che il cluster del servizio Azure Kubernetes non si trova in un set di scalabilità di macchine virtuali, come nell'esempio seguente:

**Per il pool di agenti `<agentpoolname>` la scalabilità automatica è impostata come abilitata, ma non è abilitata nei set di scalabilità di macchine virtuali**

Le funzionalità come il ridimensionamento automatico del cluster o i pool di nodi multipli richiedono set di scalabilità di macchine virtuali come `vm-set-type`.

Per creare correttamente un cluster del servizio Azure Kubernetes, seguire i passaggi *preliminari* nel documento appropriato:

* [Usare il ridimensionamento automatico del cluster](cluster-autoscaler.md)
* [Creare e usare pool di nodi multipli](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Restrizioni di denominazione applicate per le risorse e i parametri del servizio Azure Kubernetes

*Questa assistenza per la risoluzione dei problemi è diretta da aka.ms/aks-naming-rules*.

Le restrizioni di denominazione sono implementate sia dalla piattaforma Azure che dal servizio Azure Kubernetes. Se un nome di risorsa o un parametro viola una di queste restrizioni, viene restituito un errore in cui viene chiesto di fornire un input differente. Si applicano le linee guida di denominazione comuni seguenti:

* I nomi dei cluster devono avere una lunghezza compresa tra 1 e 63 caratteri. Gli unici caratteri consentiti sono lettere, numeri, trattini e caratteri di sottolineatura. Il primo e l'ultimo carattere devono essere una lettera o un numero.
* Il nodo del servizio Azure Kubernetes o il nome del gruppo di risorse *MC_* combina il nome del gruppo di risorse e il nome della risorsa. La sintassi generata automaticamente di `MC_resourceGroupName_resourceName_AzureRegion` non deve superare gli 80 caratteri. Se necessario, ridurre la lunghezza del nome del gruppo di risorse o del cluster del servizio Azure Kubernetes. È anche possibile [personalizzare il nome del gruppo di risorse del nodo](cluster-configuration.md#custom-resource-group-name).
* Il prefisso *dnsPrefix* deve iniziare e terminare con valori alfanumerici e deve avere una lunghezza compresa tra 1 e 54 caratteri. I caratteri validi includono valori alfanumerici e trattini (-). Il prefisso *dnsPrefix* non può includere caratteri speciali, ad esempio un punto (.).
* I nomi dei pool di nodi del servizio Azure Kubernetes devono contenere tutti caratteri minuscoli e devono avere una lunghezza compresa tra 1 e 11 caratteri per i pool di nodi Linux e tra 1 e 6 caratteri per i pool di nodi Windows. Il nome deve iniziare con una lettera e gli unici caratteri consentiti sono lettere e numeri.

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Errori durante il tentativo di creare, aggiornare, ridimensionare o eliminare il cluster. Operazione non consentita quando è in corso un'altra operazione

*Questa assistenza per la risoluzione dei problemi è diretta da aka.ms/aks-pending-operation*.

Le operazioni del cluster sono limitate quando è ancora in corso un'operazione precedente. Per recuperare uno stato dettagliato del cluster, usare il comando `az aks show -g myResourceGroup -n myAKSCluster -o table`. Usare il proprio gruppo di risorse e il nome del cluster del servizio Azure Kubernetes, in base alle esigenze.

In base all'output dello stato del cluster:

* Se il cluster si trova in uno stato di provisioning diverso da *Riuscito* o *Non riuscito*, attendere il completamento dell'operazione (*aggiornamento/creazione/ridimensionamento/eliminazione/migrazione*). Al termine dell'operazione precedente, ripetere l'ultima operazione del cluster.

* Se l'aggiornamento del cluster ha esito negativo, seguire i passaggi descritti in [Errori relativi al fatto che il cluster è in stato di errore e che l'aggiornamento o il ridimensionamento non funzioneranno finché il problema non verrà risolto](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).

## <a name="received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster"></a>Errore indicante che l'entità servizio non è stata trovata o non è valida per la creazione di un nuovo cluster

Quando si crea un cluster del servizio Azure Kubernetes, è necessaria un'entità servizio o un'identità gestita per creare risorse per conto dell'utente. Il servizio Azure Kubernetes crea automaticamente una nuova entità servizio al momento della creazione del cluster o ne riceve una esistente. Quando si usa un'entità servizio creata automaticamente, Azure Active Directory deve propagarla a ogni area, in modo che la creazione abbia esito positivo. Quando la propagazione richiede troppo tempo, la convalida di creazione da parte del cluster avrà esito negativo perché non è possibile trovare un'entità servizio disponibile a tale scopo. 

Per questo problema, usare le soluzioni alternative seguenti:
* Usare un'entità servizio esistente, che è già stata propagata tra le aree ed esiste per passare al servizio Azure Kubernetes al momento della creazione del cluster.
* Se si usano gli script di automazione, aggiungere i ritardi di tempo tra la creazione dell'entità servizio e la creazione del cluster del servizio Azure Kubernetes.
* Se si usa il portale di Azure, tornare alle impostazioni del cluster durante la creazione e ripetere la pagina di convalida dopo alcuni minuti.





## <a name="im-receiving-errors-after-restricting-egress-traffic"></a>Errori dopo la limitazione del traffico in uscita

Quando si limita il traffico in uscita da un cluster del servizio Azure Kubernetes, sono presenti regole per le porte in uscita, regole di rete, regole di FQDN e dell'applicazione [necessarie e facoltative consigliate](limit-egress-traffic.md) per il servizio Azure Kubernetes. Se le impostazioni sono in conflitto con una di queste regole, alcuni comandi `kubectl` non funzioneranno correttamente. È anche possibile che vengano visualizzati errori durante la creazione di un cluster del servizio Azure Kubernetes.

Verificare che le impostazioni non siano in conflitto con le regole per le porte in uscita, le regole di rete, le regole di FQDN e dell'applicazione necessarie o facoltative consigliate.

## <a name="azure-storage-and-aks-troubleshooting"></a>Risoluzione dei problemi del servizio Azure Kubernetes e di Archiviazione di Azure

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>Quali sono le versioni stabili consigliate di Kubernetes per il disco di Azure? 

| Versione di Kubernetes | Versione consigliata |
|--|:--:|
| 1.12 | 1.12.9 o successive |
| 1.13 | 1.13.6 o successive |
| 1,14 | 1.14.2 o successive |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>WaitForAttach non riuscito per il disco di Azure: analisi di "/dev/disk/azure/scsi1/lun1": sintassi non valida

In Kubernetes versione 1.10, MountVolume.WaitForAttach potrebbe non riuscire con un rimontaggio del disco di Azure.

In Linux, è possibile che venga visualizzato un errore di formato DevicePath errato. Ad esempio:

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

In Windows, è possibile che venga visualizzato un errore di numero DevicePath (LUN) errato. Ad esempio:

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

Questo problema è stato risolto nelle versioni di Kubernetes seguenti:

| Versione di Kubernetes | Versione corretta |
|--|:--:|
| 1,10 | 1.10.2 o successive |
| 1.11 | 1.11.0 o successive |
| 1.12 e successive | N/D |


### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Errore durante l'impostazione di UID e GID nelle opzioni di montaggio del disco di Azure

Per impostazione predefinita, il disco di Azure usa i file system ext4 e xfs e le opzioni di montaggio come UID = x e GID = x non possono essere impostate in fase di montaggio. Se, ad esempio, si è tentato di impostare le opzioni di montaggio UID = 999 e GID = 999, viene visualizzato un errore simile al seguente:

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

Per attenuare il problema, è possibile eseguire una delle opzioni seguenti:

* [Configurare il contesto di sicurezza per un pod](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) impostando UID in runAsUser e GID in fsGroup. Ad esempio, di seguito viene impostata l'esecuzione del pod come radice e rendendola accessibile a qualsiasi file:

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
  > Poiché GID e UID sono montati come radice o 0 per impostazione predefinita, se GID o UID sono impostati come non radice, ad esempio 1000, Kubernetes userà `chown` per modificare tutte le directory e i file del disco. Questa operazione può richiedere molto tempo e può rallentare il montaggio del disco.

* Usare `chown` in initContainers per impostare GID e UID. Ad esempio:

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Errore di scollegamento del disco di Azure che causa potenziali problemi di race condition e un elenco di dischi dati non validi

Quando non è possibile scollegare un disco di Azure, l'operazione verrà ritentata fino a sei volte per scollegare il disco con interruzioni esponenziali. Verrà inoltre tenuto un blocco a livello di nodo nell'elenco dei dischi dati per circa 3 minuti. Se l'elenco dischi viene aggiornato manualmente durante questo periodo di tempo, l'elenco dischi utilizzato dal blocco a livello di nodo risulterà obsoleto e causerà instabilità nel nodo.

Questo problema è stato risolto nelle versioni di Kubernetes seguenti:

| Versione di Kubernetes | Versione corretta |
|--|:--:|
| 1.12 | 1.12.9 o successive |
| 1.13 | 1.13.6 o successive |
| 1,14 | 1.14.2 o successive |
| 1.15 e successive | N/D |

Se si usa una versione di Kubernetes che non dispone della correzione per questo problema e il nodo ha un elenco dischi obsoleto, è possibile attenuare il problema scollegando tutti i dischi non esistenti dalla macchina virtuale come operazione in blocco. **Lo scollegamento dei dischi non esistenti singolarmente può avere esito negativo.**

### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Un numero elevato di dischi di Azure causa lentezza nelle operazioni di collegamento/scollegamento

Quando il numero di operazioni di collegamento/scollegamento dei dischi di Azure destinati a una macchina virtuale a nodo singolo è maggiore di 10, o maggiore di 3 quando sono destinati a un singolo pool di set di scalabilità di macchine virtuali, le operazioni potrebbero essere più lente del previsto se eseguite in sequenza. Questo problema è un limite noto e al momento non sono disponibili soluzioni alternative. [Elemento User Voice per supportare operazioni di collegamento/scollegamento parallelo oltre il numero](https://feedback.azure.com/forums/216843-virtual-machines/suggestions/40444528-vmss-support-for-parallel-disk-attach-detach-for).

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Errore di scollegamento del disco di Azure che causa un potenziale stato di errore della macchina virtuale del nodo

In alcuni casi limite, uno scollegamento del disco di Azure potrebbe avere esito negativo e lasciare la macchina virtuale del nodo in stato di errore.

Questo problema è stato risolto nelle versioni di Kubernetes seguenti:

| Versione di Kubernetes | Versione corretta |
|--|:--:|
| 1.12 | 1.12.10 o successive |
| 1.13 | 1.13.8 o successive |
| 1,14 | 1.14.4 o successive |
| 1.15 e successive | N/D |

Se si usa una versione di Kubernetes che non dispone della correzione per questo problema e il nodo si trova in stato di errore, è possibile attenuare il problema aggiornando manualmente lo stato della macchina virtuale usando uno dei metodi seguenti:

* Per un cluster basato su set di disponibilità:
    ```azurecli
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* Per un cluster basato su set di scalabilità di macchine virtuali:
    ```azurecli
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Risoluzione dei problemi di File di Azure e del servizio Azure Kubernetes

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Quali sono le versioni stabili consigliate di Kubernetes per i file di Azure?
 
| Versione di Kubernetes | Versione consigliata |
|--|:--:|
| 1.12 | 1.12.6 o successive |
| 1.13 | 1.13.4 o successive |
| 1,14 | 1.14.0 o successive |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Quali sono gli oggetti mountOptions predefiniti quando si usa File di Azure?

Impostazioni consigliate:

| Versione di Kubernetes | Valore fileMode e dirMode|
|--|:--:|
| 1.12.0 - 1.12.1 | 0755 |
| 1.12.2 e successive | 0777 |

È possibile specificare le opzioni di montaggio nell'oggetto della classe di archiviazione. L'esempio seguente imposta *0777*:

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

Di seguito sono riportate altre impostazioni utili di *mountOptions*:

* *mfsymlinks* consente che il montaggio di File di Azure (cifs) supporti i collegamenti simbolici
* *nobrl* impedisce l'invio di richieste di blocco di intervalli di byte al server. Questa impostazione è necessaria per alcune applicazioni che si interrompono con blocchi di intervallo di byte obbligatori in stile cifs. La maggior parte dei server cifs non supporta ancora la richiesta di blocchi di intervallo di byte di avviso. Se non si usa *nobrl*, le applicazioni che si interrompono con blocchi di intervallo di byte obbligatori in stile cifs possono causare messaggi di errore simili al seguente:
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Errore "Impossibile modificare le autorizzazioni" quando si usa File di Azure

Quando si esegue PostgreSQL nel plug-in File di Azure, è possibile che venga visualizzato un errore simile al seguente:

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

Questo errore è causato dal plug-in File di Azure che usa il protocollo cifs/SMB. Quando si usa il protocollo cifs/SMB, non è possibile modificare le autorizzazioni di file e directory dopo il montaggio.

Per risolvere questo problema, usare *subPath* con il plug-in Dischi di Azure. 

> [!NOTE] 
> Per il tipo di disco ext3/4, dopo la formattazione del disco è presente una directory lost-found.

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>File di Azure presenta una latenza elevata rispetto a Dischi di Azure durante la gestione di molti file di piccole dimensioni

In alcuni casi, ad esempio la gestione di molti file di piccole dimensioni, è possibile che si verifichi una latenza elevata quando si usa File di Azure rispetto a Dischi di Azure.

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>Errore durante l'abilitazione dell'impostazione "Consenti l'accesso da rete selezionata" per l'account di archiviazione

Se si abilita *Consenti l'accesso da rete selezionata* in un account di archiviazione usato per il provisioning dinamico nel servizio Azure Kubernetes, si riceve un errore quando il servizio Azure Kubernetes crea una condivisione file:

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

Questo errore è dovuto al fatto che *persistentvolume-controller* di Kubernetes non si trova nella rete scelta quando si imposta *Consenti l'accesso da rete selezionata*.

È possibile attenuare il problema usando il [provisioning statico con File di Azure](azure-files-volume.md).

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Non è possibile rimontare File di Azure nel pod di Windows

Se un pod di Windows con un montaggio di File di Azure viene eliminato e quindi pianificato per essere ricreato nello stesso nodo, il montaggio avrà esito negativo. Questo errore si verifica a causa della mancata esecuzione del comando `New-SmbGlobalMapping` perché il montaggio di File di Azure è già montato nel nodo.

Ad esempio, si potrebbe visualizzare un errore simile al seguente:

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

Questo problema è stato risolto nelle versioni di Kubernetes seguenti:

| Versione di Kubernetes | Versione corretta |
|--|:--:|
| 1.12 | 1.12.6 o successive |
| 1.13 | 1.13.4 o successive |
| 1.14 e successive | N/D |

### <a name="azure-files-mount-fails-because-of-storage-account-key-changed"></a>Il montaggio di File di Azure ha esito negativo a causa della modifica della chiave dell'account di archiviazione

Se la chiave dell'account di archiviazione è cambiata, è possibile che vengano visualizzati errori di montaggio di File di Azure.

È possibile attenuare il problema aggiornando manualmente il campo `azurestorageaccountkey` in un segreto di File di Azure con la chiave dell'account di archiviazione con codifica base64.

Per codificare la chiave dell'account di archiviazione in base64, è possibile usare `base64`. Ad esempio:

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Per aggiornare il file del segreto di Azure, usare `kubectl edit secret`. Ad esempio:

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Dopo alcuni minuti, il nodo dell'agente tenterà di eseguire nuovamente il montaggio di File di Azure con la chiave di archiviazione aggiornata.


### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>La scalabilità automatica del cluster ha esito negativo e restituisce un errore indicante che non è stato possibile correggere le dimensioni del gruppo di nodi

Se la scalabilità automatica del cluster non aumenta o diminuisce, viene visualizzato un errore simile a quello riportato di seguito nei [log di scalabilità automatica del cluster][view-master-logs].

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

Questo errore è dovuto a una race condition di scalabilità automatica del cluster upstream. In tal caso, il servizio di scalabilità automatica del cluster termina con un valore diverso da quello effettivamente presente nel cluster. Per uscire da questo stato, disabilitare e riabilitare la [scalabilità automatica del cluster][cluster-autoscaler].

### <a name="slow-disk-attachment-getazuredisklun-takes-10-to-15-minutes-and-you-receive-an-error"></a>Collegamento lento del disco, GetAzureDiskLun richiede da 10 a 15 minuti e viene visualizzato un errore

Nelle versioni di Kubernetes **precedenti a 1.15.0**, è possibile che venga visualizzato un errore simile a **Errore WaitForAttach non riesce a trovare il numero di unità logica per il disco**.  La soluzione alternativa per questo problema consiste nell'attendere circa 15 minuti e riprovare.

<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md
