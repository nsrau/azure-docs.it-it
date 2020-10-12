---
title: Note sulla versione di Azure Stack Edge Pro GA | Microsoft Docs
description: Descrive i problemi e le risoluzioni critici aperti per il Azure Stack Edge Pro che esegue la versione di disponibilità generale.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 09/29/2020
ms.author: alkohli
ms.openlocfilehash: c43f7ba52ed0f6018ee32583011bb92786708119
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91460476"
---
# <a name="azure-stack-edge-pro-with-gpu-general-availability-ga-release-notes"></a>Note sulla versione di Azure Stack Edge Pro con disponibilità generale GPU

Le note sulla versione seguenti identificano i problemi critici aperti e i problemi risolti per la versione di disponibilità generale (GA) per i dispositivi Azure Stack Edge Pro con GPU.

Le note sulla versione vengono aggiornate continuamente e i problemi che richiedono una soluzione alternativa vengono aggiunti man mano che vengono individuati. Prima di distribuire il dispositivo Azure Stack Edge Pro, esaminare attentamente le informazioni contenute nelle note sulla versione.

Questo articolo si applica alla versione **Pro 2009 di Azure stack Edge** , che esegue il mapping al numero di versione del software **2.1.1364.2110**.

## <a name="whats-new"></a>Novità

Nella versione di Azure Stack Edge 2009 sono disponibili le nuove funzionalità seguenti. 

- **Classi di archiviazione** : in questa versione sono disponibili classi di archiviazione che consentono di eseguire il provisioning dinamico dell'archiviazione. Per altre informazioni, vedere [gestione dell'archiviazione Kubernetes nel dispositivo GPU Pro Azure stack Edge](azure-stack-edge-gpu-kubernetes-storage.md#dynamicprovisioning). 
- **Dashboard di Kubernetes con server delle metriche** : in questa versione viene aggiunto un dashboard di Kubernetes con un componente aggiuntivo per il server di metriche. È possibile usare il dashboard per ottenere una panoramica delle applicazioni in esecuzione sul dispositivo Azure Stack Edge Pro, visualizzare lo stato delle risorse del cluster Kubernetes e visualizzare gli eventuali errori verificatisi nel dispositivo. Il server delle metriche aggrega l'utilizzo di CPU e memoria tra le risorse Kubernetes nel dispositivo. Per altre informazioni, vedere [usare il dashboard di Kubernetes per monitorare il dispositivo GPU Pro Azure stack Edge](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).
- **Azure Arc Enabled Kubernetes on Azure stack Edge Pro** : a partire da questa versione, è possibile distribuire i carichi di lavoro delle applicazioni nel dispositivo Azure stack Edge Pro tramite Azure Arc abilitato Kubernetes. Azure Arc è uno strumento di gestione ibrido che consente di distribuire applicazioni nei cluster Kubernetes. Per altre informazioni, vedere [distribuire carichi di lavoro tramite Azure Arc sul dispositivo Azure stack Edge Pro](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).  

## <a name="known-issues"></a>Problemi noti 

La tabella seguente fornisce un riepilogo dei problemi noti relativi al dispositivo Azure Stack Edge Pro.

| No. | Funzionalità | Problema | Soluzione alternativa/commenti |
| --- | --- | --- | --- |
|**1.**|Funzionalità di anteprima |Per questa versione GA, le funzionalità seguenti: Azure Resource Manager locali, VM, Kubernetes, Azure Arc Enabled Kubernetes, servizio multiprocesso (MP) per GPU-sono tutte disponibili in anteprima per il dispositivo Pro Azure Stack Edge.  |Queste funzionalità saranno disponibili a livello generale in una versione successiva. |
| **2.** |Azure Stack Edge Pro e Azure SQL | Per la creazione del database SQL è necessario l'accesso amministratore.   |Eseguire i passaggi seguenti anziché i passaggi 1-2 in [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database) . <ul><li>Nell'interfaccia utente locale del dispositivo abilitare l'interfaccia di calcolo. Selezionare **calcolo > porta # > Abilita per calcolo > applica.**</li><li>Scarica `sqlcmd` dal computer client da https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>Connettersi all'indirizzo IP dell'interfaccia di calcolo (la porta abilitata), aggiungendo ", 1401" alla fine dell'indirizzo.</li><li>Il comando finale sarà simile al seguente: sqlcmd-S {Interface IP}, 1401-U SA-P "Strong! Passw0rd ".</li>Al termine di questa operazione, i passaggi 3-4 dalla documentazione corrente devono essere identici. </li></ul> |
| **3.** |Aggiorna| Le modifiche incrementali apportate ai BLOB ripristinati tramite l' **aggiornamento** non sono supportate |Per gli endpoint BLOB, gli aggiornamenti parziali dei BLOB dopo un aggiornamento possono comportare il caricamento degli aggiornamenti nel cloud. Ad esempio, sequenza di azioni come:<ul><li>Creare un BLOB nel cloud. O eliminare un BLOB caricato in precedenza dal dispositivo.</li><li>Aggiornare il BLOB dal cloud al dispositivo usando la funzionalità di aggiornamento.</li><li>Aggiornare solo una parte del BLOB usando le API REST di Azure SDK.</li></ul>Queste azioni possono comportare l'aggiornamento delle sezioni aggiornate del BLOB nel cloud. <br>**Soluzione alternativa**: usare strumenti come Robocopy o una normale copia di file tramite Esplora risorse o la riga di comando per sostituire i BLOB interi.|
|**4.**|Limitazione|Durante la limitazione, se nel dispositivo non sono consentite nuove scritture, le scritture eseguite dal client NFS avranno esito negativo con l'errore "autorizzazione negata".| L'errore verrà visualizzato come riportato di seguito:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: non è possibile creare la directory ' test ': autorizzazione negata|
|**5.**|Inserimento di archiviazione BLOB|Quando si usa AzCopy versione 10 per l'inserimento dell'archiviazione BLOB, eseguire AzCopy con l'argomento seguente: `Azcopy <other arguments> --cap-mbps 2000`| Se questi limiti non vengono forniti per AzCopy, potrebbe inviare un numero elevato di richieste al dispositivo e causare problemi con il servizio.|
|**6.**|Account di archiviazione a livelli|Quando si usano gli account di archiviazione a livelli, si applica quanto segue:<ul><li> Sono supportati solo i BLOB in blocchi. I BLOB di pagine non sono supportati.</li><li>Non è disponibile alcun supporto per snapshot o copia API.</li><li> L'inserimento del carico di lavoro Hadoop tramite `distcp` non è supportato perché usa l'operazione di copia.</li></ul>||
|**7.**|Connessione NFS share|Se più processi eseguono la copia nella stessa condivisione e l' `nolock` attributo non viene utilizzato, è possibile che vengano visualizzati errori durante la copia.|L' `nolock` attributo deve essere passato al comando mount per copiare i file nella condivisione NFS. Ad esempio: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**8.**|Cluster Kubernetes|Quando si applica un aggiornamento sul dispositivo che esegue un cluster kubernetes, le macchine virtuali kubernetes vengono riavviate e riavviate. In questo caso, solo i pod distribuiti con le repliche specificate vengono ripristinati automaticamente dopo un aggiornamento.  |Se sono stati creati singoli pod all'esterno di un controller di replica senza specificare un set di repliche, questi Pod non verranno ripristinati automaticamente dopo l'aggiornamento del dispositivo. Sarà necessario ripristinare i pod.<br>Un set di repliche sostituisce i pod eliminati o interrotti per qualsiasi motivo, ad esempio un errore del nodo o un aggiornamento del nodo di interruzione. Per questo motivo, è consigliabile usare un set di repliche anche se l'applicazione richiede un singolo POD.|
|**9.**|Cluster Kubernetes|Kubernetes su Azure Stack Edge Pro è supportato solo con Helm V3 o versione successiva. Per altre informazioni, vedere [domande frequenti: rimozione di un timone](https://v3.helm.sh/docs/faq/).|
|**10.**|Kubernetes con abilitazione di Azure Arc |Per la versione GA, Azure Arc Enabled Kubernetes viene aggiornato dalla versione 0.1.18 a 0.2.9. Poiché l'aggiornamento di Kubernetes abilitato per Azure Arc non è supportato nel dispositivo Azure Stack Edge, è necessario ridistribuire Azure Arc Enabled Kubernetes.|Seguire questa procedura:<ol><li>[Applicare gli aggiornamenti software e Kubernetes del dispositivo](azure-stack-edge-gpu-install-update.md).</li><li>Connettersi all' [interfaccia di PowerShell del dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md).</li><li>Rimuovere l'agente di Azure ARC esistente. Digitare: `Remove-HcsKubernetesAzureArcAgent`.</li><li>Distribuire [Azure Arc in una nuova risorsa](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md). Non usare una risorsa di Azure ARC esistente.</li></ol>|
|**11.**|Kubernetes con abilitazione di Azure Arc|Le distribuzioni di Azure Arc non sono supportate se il proxy Web è configurato sul dispositivo Azure Stack Edge Pro.||
|**12.**|Kubernetes |La porta 31000 è riservata per il dashboard di Kubernetes. Analogamente, nella configurazione predefinita, gli indirizzi IP 172.28.0.1 e 172.28.0.10 sono riservati rispettivamente per il servizio Kubernetes e per il servizio DNS di base.|Non usare IP riservati.|
|**13.**|Kubernetes |Kubernetes attualmente non consente i servizi LoadBalancer multiprotocollo. Ad esempio, un servizio DNS che avrebbe dovuto rimanere in attesa su TCP e UDP. |Per ovviare a questa limitazione di Kubernetes con MetalLB, è possibile creare due servizi (uno per TCP, uno per UDP) nello stesso selettore di Pod. Questi servizi usano la stessa chiave di condivisione e spec. loadBalancerIP per condividere lo stesso indirizzo IP. Gli IP possono anche essere condivisi se si dispone di più servizi rispetto agli indirizzi IP disponibili. <br> Per altre informazioni, vedere [condivisione degli indirizzi IP](https://metallb.universe.tf/usage/#ip-address-sharing).|
|**14.**|Cluster Kubernetes|I moduli esistenti di Azure IoT Edge Marketplace possono richiedere modifiche da eseguire su IoT Edge sul dispositivo Azure Stack Edge.|Per altre informazioni, vedere Modificare Azure IoT Edge moduli da Marketplace per l'esecuzione nel dispositivo Azure Stack Edge.<!-- insert link-->|
|**15.**|Kubernetes |I montaggi con binding basato su file non sono supportati con Azure IoT Edge in Kubernetes sul dispositivo Azure Stack Edge.|IoT Edge usa un livello di conversione per tradurre le `ContainerCreate` Opzioni nei costrutti Kubernetes. La creazione di `Binds` mappe per la directory hostPath e quindi i montaggi di binding basati su file non possono essere associati a percorsi in IOT Edge contenitori. Se possibile, eseguire il mapping della directory padre.|
|**16.**|Kubernetes |Se si importano certificati personalizzati per IoT Edge e si aggiungono quelli nel dispositivo Azure Stack Edge dopo che il calcolo è stato configurato nel dispositivo, i nuovi certificati non vengono prelevati.|Per risolvere questo problema, è necessario caricare i certificati prima di configurare il calcolo nel dispositivo. Se il calcolo è già configurato, [connettersi all'interfaccia di PowerShell del dispositivo ed eseguire IOT Edge comandi](azure-stack-edge-gpu-connect-powershell-interface.md#use-iotedge-commands). Riavviare `iotedged` e `edgehub` pod.|
|**17.**|Certificati |In alcuni casi, lo stato del certificato nell'interfaccia utente locale può richiedere alcuni secondi per l'aggiornamento. |Potrebbero essere interessati gli scenari seguenti nell'interfaccia utente locale.<ul><li>Colonna **stato** nella pagina **certificati** .</li><li>Riquadro **sicurezza** nella pagina attività **iniziali** .</li><li>Riquadro **configurazione** nella pagina **Panoramica** .</li></ul>  |
|**17.**|IoT Edge |I moduli distribuiti tramite IoT Edge non possono utilizzare la rete host. | |
|**18.**|Calcolo + Kubernetes |Compute/Kubernetes non supporta il proxy Web NTLM. ||
|**19.**|Calcolo + proxy Web + aggiornamento |Se il calcolo è configurato con il proxy Web, il calcolo dell'aggiornamento potrebbe non riuscire. |Si consiglia di disabilitare il calcolo prima dell'aggiornamento. |

<!--|**18.**|Azure Private Edge Zone (Preview) |There is a known issue with Virtual Network Function VM if the VM was created on Azure Stack Edge device running earlier preview builds such as 2006/2007b and then the device was updated to 2009 GA release. The issue is that the VNF information can't be retrieved or any new VNFs can't be created unless the VNF VMs are deleted before the device is updated.  |Before you update Azure Stack Edge device to 2009 release, use the PowerShell command `get-mecvnf` followed by `remove-mecvnf <VNF guid>` to remove all Virtual Network Function VMs one at a time. After the upgrade, you will need to redeploy the same VNFs.|-->




## <a name="next-steps"></a>Passaggi successivi

- [Preparare la distribuzione di Azure Stack dispositivo Edge Pro con GPU](azure-stack-edge-gpu-deploy-prep.md)

