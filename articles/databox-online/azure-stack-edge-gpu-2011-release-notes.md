---
title: Note sulla versione di Azure Stack Edge Pro 2011
description: Descrive i problemi e le risoluzioni critici per il Azure Stack Edge Pro che esegue la versione 2011.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 11/24/2020
ms.author: alkohli
ms.openlocfilehash: 158e3cc17c146c5e05959d2faf7d75c9bcc12dcb
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467231"
---
# <a name="azure-stack-edge-2011-release-notes"></a>Note sulla versione di Azure Stack Edge 2011

Le note sulla versione seguenti identificano i problemi critici aperti e i problemi risolti per la versione 2011 per i dispositivi Azure Stack Edge. Queste note sulla versione sono valide per Azure Stack GPU Pro Edge, Azure Stack Edge Pro R e i dispositivi Mini R Azure Stack Edge. Le funzionalità e i problemi che corrispondono a un modello specifico vengono chiamati laddove applicabile.

Le note sulla versione vengono aggiornate continuamente e i problemi che richiedono una soluzione alternativa vengono aggiunti man mano che vengono individuati. Prima di distribuire il dispositivo, esaminare attentamente le informazioni contenute nelle note sulla versione.

Questo articolo si applica alla versione di **Azure stack Edge 2011**<!--which maps to software version number **2.1.XXXX.XXXX**-->.

## <a name="whats-new"></a>Novità

Nella versione di Azure Stack Edge 2011 sono disponibili le nuove funzionalità seguenti. 

- **Disponibilità a livello generale dei dispositivi r di Azure stack Edge Pro e di Azure stack Edge Mini** -r: a partire da questa versione, saranno disponibili Azure stack Edge Pro r e i dispositivi mini r Azure stack Edge. Per ulteriori informazioni, vedere [che cos'è Azure stack Edge Pro r](azure-stack-edge-j-series-overview.md) e [che cos'è Azure stack mini r Edge](azure-stack-edge-k-series-overview.md).  
- **Gestione cloud delle macchine virtuali** : a partire da questa versione, è possibile creare e gestire le macchine virtuali nel dispositivo tramite il portale di Azure. Per ulteriori informazioni, vedere [la pagina relativa alla distribuzione di macchine virtuali tramite il portale di Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
- **Integrazione con monitoraggio di Azure** : è ora possibile usare monitoraggio di Azure per monitorare i contenitori dalle applicazioni di calcolo in esecuzione nel dispositivo. L'archivio delle metriche di monitoraggio di Azure non è supportato in questa versione. Per altre informazioni, vedere come [abilitare monitoraggio di Azure nel dispositivo](azure-stack-edge-gpu-enable-azure-monitor.md).
- **Registro contenitori perimetrale** : in questa versione è disponibile un registro contenitori perimetrale che fornisce un repository sul dispositivo perimetrale. È possibile usare questo registro per archiviare e gestire le immagini del contenitore. Per altre informazioni, vedere [Enable Edge container Registry](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md). 
- **Rete privata virtuale (VPN)** : usare la VPN per fornire un altro livello di crittografia per i dati che passano tra i dispositivi e il cloud. VPN è disponibile solo su Azure Stack Edge Pro R e Azure Stack Edge Mini R. Per ulteriori informazioni, vedere come [configurare una VPN nel dispositivo](azure-stack-edge-mini-r-configure-vpn-powershell.md). 
- **Ruotare le chiavi di crittografia-a-Rest** : ora sarà possibile ruotare le chiavi di crittografia-i-Rest usate per proteggere le unità nel dispositivo. Questa funzionalità è disponibile solo per i dispositivi Azure Stack Edge Pro R e Azure Stack Edge Mini R. Per altre informazioni, vedere [ruotare le chiavi di crittografia-](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#manage-access-to-device-data)dati inattivi.
- **Registrazione proattiva** : a partire da questa versione, è possibile abilitare la raccolta dei log proattiva nel dispositivo in base agli indicatori di integrità del sistema per facilitare la risoluzione di eventuali problemi del dispositivo. Per altre informazioni, vedere [raccolta di log proattiva nel dispositivo](azure-stack-edge-gpu-proactive-log-collection.md).


## <a name="known-issues-in-2011-release"></a>Problemi noti nella versione 2011

La tabella seguente fornisce un riepilogo dei problemi noti della versione 2011.

| No. | Feature | Problema | Soluzione alternativa/commenti |
| --- | --- | --- | --- |
|**1.**|Funzionalità di anteprima |Per questa versione, le funzionalità seguenti: Azure Resource Manager locali, VM, gestione cloud di VM, Azure Arc abilitato Kubernetes, VPN per Azure Stack Edge Pro R e Azure Stack Edge Mini R, servizio multiprocesso (MP) per la GPU Pro Azure Stack Edge-sono tutti disponibili in anteprima.  |Queste funzionalità saranno disponibili a livello generale nelle versioni successive. |
|**2.**|Dashboard di Kubernetes | L'endpoint *https* per il dashboard di Kubernetes con certificato SSL non è supportato. | |
|**3.**|Kubernetes |Il registro contenitori perimetrale non funziona quando il proxy Web è abilitato.|La funzionalità sarà disponibile in una versione futura. |
|**4.**|Kubernetes |Il registro contenitori perimetrale non funziona con i moduli IoT Edge.| |
|**5.**|Kubernetes |Kubernetes non supporta ":" nei nomi delle variabili di ambiente usati dalle applicazioni .NET. Questa operazione è necessaria anche per il funzionamento di griglia di eventi IoT Edge modulo sul dispositivo Azure Stack Edge e su altre applicazioni. Per ulteriori informazioni, vedere la [documentazione di ASP.NET Core](/aspnet/core/fundamentals/configuration/?tabs=basicconfiguration&view=aspnetcore-3.1#environment-variables).|Sostituire ":" con il doppio carattere di sottolineatura. Per ulteriori informazioni, vedere la pagina relativa al [problema Kubernetes](https://github.com/kubernetes/kubernetes/issues/53201)|
|**6.** |Azure Arc + cluster Kubernetes |Per impostazione predefinita, quando la risorsa `yamls` viene eliminata dal repository git, le risorse corrispondenti non vengono eliminate dal cluster Kubernetes.  |È necessario impostare `--sync-garbage-collection`  in Arc OperatorParams per consentire l'eliminazione delle risorse quando viene eliminato dal repository git. Per altre informazioni, vedere [eliminare una configurazione](../azure-arc/kubernetes/use-gitops-connected-cluster.md#additional-parameters). |
|**7.**|NFS |Le applicazioni che usano NFS Share Mounts sul dispositivo per scrivere i dati devono usare la scrittura esclusiva. In questo modo si garantisce che le Scritture vengano scritte nel disco.| |
|**8.**|Configurazione di calcolo |La configurazione di calcolo non riesce nelle configurazioni di rete in cui i gateway o i commutatori o i router rispondono alle richieste ARP (Address Resolution Protocol) per i sistemi che non esistono nella rete.| |
|**9.**|COMPUTE e Kubernetes |Se Kubernetes è impostato per primo sul dispositivo, attesta tutte le GPU disponibili. Di conseguenza, non è possibile creare macchine virtuali Azure Resource Manager usando GPU dopo la configurazione di Kubernetes. |Se il dispositivo ha 2 GPU, è possibile creare 1 VM che usa la GPU e quindi configurare Kubernetes. In questo caso, Kubernetes utilizzerà la GPU rimanente disponibile 1. |


## <a name="known-issues-from-previous-releases"></a>Problemi noti delle versioni precedenti 

Nella tabella seguente viene fornito un riepilogo dei problemi noti rilevati dalle versioni precedenti.

| No. | Feature | Problema | Soluzione alternativa/commenti |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge Pro e Azure SQL | Per la creazione del database SQL è necessario l'accesso amministratore.   |Eseguire i passaggi seguenti anziché i passaggi 1-2 in [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database) . <ul><li>Nell'interfaccia utente locale del dispositivo abilitare l'interfaccia di calcolo. Selezionare **calcolo > porta # > Abilita per calcolo > applica.**</li><li>Scarica `sqlcmd` dal computer client da https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>Connettersi all'indirizzo IP dell'interfaccia di calcolo (la porta abilitata), aggiungendo ", 1401" alla fine dell'indirizzo.</li><li>Il comando finale sarà simile al seguente: sqlcmd-S {Interface IP}, 1401-U SA-P "Strong! Passw0rd ".</li>Al termine di questa operazione, i passaggi 3-4 dalla documentazione corrente devono essere identici. </li></ul> |
| **2.** |Aggiorna| Le modifiche incrementali apportate ai BLOB ripristinati tramite l' **aggiornamento** non sono supportate |Per gli endpoint BLOB, gli aggiornamenti parziali dei BLOB dopo un aggiornamento possono comportare il caricamento degli aggiornamenti nel cloud. Ad esempio, sequenza di azioni come:<ul><li>Creare un BLOB nel cloud. O eliminare un BLOB caricato in precedenza dal dispositivo.</li><li>Aggiornare il BLOB dal cloud al dispositivo usando la funzionalità di aggiornamento.</li><li>Aggiornare solo una parte del BLOB usando le API REST di Azure SDK.</li></ul>Queste azioni possono comportare l'aggiornamento delle sezioni aggiornate del BLOB nel cloud. <br>**Soluzione alternativa**: usare strumenti come Robocopy o una normale copia di file tramite Esplora risorse o la riga di comando per sostituire i BLOB interi.|
|**3.**|Limitazione|Durante la limitazione, se nel dispositivo non sono consentite nuove scritture, le scritture eseguite dal client NFS avranno esito negativo con l'errore "autorizzazione negata".| L'errore verrà visualizzato come riportato di seguito:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: non è possibile creare la directory ' test ': autorizzazione negata|
|**4.**|Inserimento di archiviazione BLOB|Quando si usa AzCopy versione 10 per l'inserimento dell'archiviazione BLOB, eseguire AzCopy con l'argomento seguente: `Azcopy <other arguments> --cap-mbps 2000`| Se questi limiti non vengono forniti per AzCopy, potrebbe inviare un numero elevato di richieste al dispositivo e causare problemi con il servizio.|
|**5.**|Account di archiviazione a livelli|Quando si usano gli account di archiviazione a livelli, si applica quanto segue:<ul><li> Sono supportati solo i BLOB in blocchi. I BLOB di pagine non sono supportati.</li><li>Non è disponibile alcun supporto per snapshot o copia API.</li><li> L'inserimento del carico di lavoro Hadoop tramite `distcp` non è supportato perché usa l'operazione di copia.</li></ul>||
|**6.**|Connessione NFS share|Se più processi eseguono la copia nella stessa condivisione e l' `nolock` attributo non viene utilizzato, è possibile che vengano visualizzati errori durante la copia.|L' `nolock` attributo deve essere passato al comando mount per copiare i file nella condivisione NFS. Ad esempio: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**7.**|Cluster Kubernetes|Quando si applica un aggiornamento sul dispositivo che esegue un cluster kubernetes, le macchine virtuali kubernetes vengono riavviate e riavviate. In questo caso, solo i pod distribuiti con le repliche specificate vengono ripristinati automaticamente dopo un aggiornamento.  |Se sono stati creati singoli pod all'esterno di un controller di replica senza specificare un set di repliche, questi Pod non verranno ripristinati automaticamente dopo l'aggiornamento del dispositivo. Sarà necessario ripristinare i pod.<br>Un set di repliche sostituisce i pod eliminati o interrotti per qualsiasi motivo, ad esempio un errore del nodo o un aggiornamento del nodo di interruzione. Per questo motivo, è consigliabile usare un set di repliche anche se l'applicazione richiede un singolo POD.|
|**8.**|Cluster Kubernetes|Kubernetes su Azure Stack Edge Pro è supportato solo con Helm V3 o versione successiva. Per altre informazioni, vedere [domande frequenti: rimozione di un timone](https://v3.helm.sh/docs/faq/).|
|**9.**|Kubernetes con abilitazione di Azure Arc |Per la versione GA, Azure Arc Enabled Kubernetes viene aggiornato dalla versione 0.1.18 a 0.2.9. Poiché l'aggiornamento di Kubernetes abilitato per Azure Arc non è supportato nel dispositivo Azure Stack Edge, è necessario ridistribuire Azure Arc Enabled Kubernetes.|Seguire questa procedura:<ol><li>[Applicare gli aggiornamenti software e Kubernetes del dispositivo](azure-stack-edge-gpu-install-update.md).</li><li>Connettersi all' [interfaccia di PowerShell del dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md).</li><li>Rimuovere l'agente di Azure ARC esistente. Digitare: `Remove-HcsKubernetesAzureArcAgent`.</li><li>Distribuire [Azure Arc in una nuova risorsa](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md). Non usare una risorsa di Azure ARC esistente.</li></ol>|
|**10.**|Kubernetes con abilitazione di Azure Arc|Le distribuzioni di Azure Arc non sono supportate se il proxy Web è configurato sul dispositivo Azure Stack Edge Pro.||
|**11.**|Kubernetes |La porta 31000 è riservata per il dashboard di Kubernetes. La porta 31001 è riservata al registro contenitori perimetrale. Analogamente, nella configurazione predefinita, gli indirizzi IP 172.28.0.1 e 172.28.0.10 sono riservati rispettivamente per il servizio Kubernetes e per il servizio DNS di base.|Non usare IP riservati.|
|**12.**|Kubernetes |Kubernetes attualmente non consente i servizi LoadBalancer multiprotocollo. Ad esempio, un servizio DNS che avrebbe dovuto rimanere in attesa su TCP e UDP. |Per ovviare a questa limitazione di Kubernetes con MetalLB, è possibile creare due servizi (uno per TCP, uno per UDP) nello stesso selettore di Pod. Questi servizi usano la stessa chiave di condivisione e spec. loadBalancerIP per condividere lo stesso indirizzo IP. Gli IP possono anche essere condivisi se si dispone di più servizi rispetto agli indirizzi IP disponibili. <br> Per altre informazioni, vedere [condivisione degli indirizzi IP](https://metallb.universe.tf/usage/#ip-address-sharing).|
|**13.**|Cluster Kubernetes|I moduli esistenti di Azure IoT Edge Marketplace possono richiedere modifiche da eseguire su IoT Edge sul dispositivo Azure Stack Edge.|Per altre informazioni, vedere Modificare Azure IoT Edge moduli da Marketplace per l'esecuzione nel dispositivo Azure Stack Edge.<!-- insert link-->|
|**14.**|Kubernetes |I montaggi con binding basato su file non sono supportati con Azure IoT Edge in Kubernetes sul dispositivo Azure Stack Edge.|IoT Edge usa un livello di conversione per tradurre le `ContainerCreate` Opzioni nei costrutti Kubernetes. La creazione di `Binds` mappe per la directory hostPath e quindi i montaggi di binding basati su file non possono essere associati a percorsi in IOT Edge contenitori. Se possibile, eseguire il mapping della directory padre.|
|**15.**|Kubernetes |Se si importano certificati personalizzati per IoT Edge e si aggiungono quelli nel dispositivo Azure Stack Edge dopo che il calcolo è stato configurato nel dispositivo, i nuovi certificati non vengono prelevati.|Per risolvere questo problema, è necessario caricare i certificati prima di configurare il calcolo nel dispositivo. Se il calcolo è già configurato, [connettersi all'interfaccia di PowerShell del dispositivo ed eseguire IOT Edge comandi](azure-stack-edge-gpu-connect-powershell-interface.md#use-iotedge-commands). Riavviare `iotedged` e `edgehub` pod.|
|**16.**|Certificati |In alcuni casi, lo stato del certificato nell'interfaccia utente locale può richiedere alcuni secondi per l'aggiornamento. |Potrebbero essere interessati gli scenari seguenti nell'interfaccia utente locale.<ul><li>Colonna **stato** nella pagina **certificati** .</li><li>Riquadro **sicurezza** nella pagina attività **iniziali** .</li><li>Riquadro **configurazione** nella pagina **Panoramica** .</li></ul>  |
|**17.**|IoT Edge |I moduli distribuiti tramite IoT Edge non possono utilizzare la rete host. | |
|**18.**|Calcolo + Kubernetes |Compute/Kubernetes non supporta il proxy Web NTLM. ||
|**19.**|Calcolo + proxy Web + aggiornamento |Se il calcolo è configurato con il proxy Web, il calcolo dell'aggiornamento potrebbe non riuscire. |Si consiglia di disabilitare il calcolo prima dell'aggiornamento. |
|**20.**|Kubernetes + aggiornamento |Le versioni precedenti del software, ad esempio le versioni 2008, presentano un problema race condition aggiornamento che causa l'esito negativo dell'aggiornamento con ClusterConnectionException. |L'uso delle compilazioni più recenti dovrebbe ovviare a questo problema. Se il problema persiste, è sufficiente ritentare l'aggiornamento, che dovrebbe funzionare.|


<!--|**18.**|Azure Private Edge Zone (Preview) |There is a known issue with Virtual Network Function VM if the VM was created on Azure Stack Edge device running earlier preview builds such as 2006/2007b and then the device was updated to 2009 GA release. The issue is that the VNF information can't be retrieved or any new VNFs can't be created unless the VNF VMs are deleted before the device is updated.  |Before you update Azure Stack Edge device to 2009 release, use the PowerShell command `get-mecvnf` followed by `remove-mecvnf <VNF guid>` to remove all Virtual Network Function VMs one at a time. After the upgrade, you will need to redeploy the same VNFs.|-->


## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare il dispositivo](azure-stack-edge-gpu-install-update.md)

