---
title: Note sulla versione di Azure Stack Edge Pro Preview | Microsoft Docs
description: Vengono descritti i problemi e le risoluzioni critici aperti per la versione di anteprima di Azure Stack Edge Pro che esegue l'anteprima.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/07/2020
ms.author: alkohli
ms.openlocfilehash: 25db4e7f3e4e1f7056979c4c40c6ffc61f340439
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96345372"
---
# <a name="azure-stack-edge-pro-with-gpu-preview-release-notes"></a>Note sulla versione di Azure Stack Edge Pro con GPU Preview

Le note sulla versione seguenti identificano i problemi critici aperti e i problemi risolti per la versione di anteprima 2008 per i dispositivi Pro Azure Stack Edge con GPU.

Le note sulla versione vengono aggiornate continuamente e i problemi che richiedono una soluzione alternativa vengono aggiunti man mano che vengono individuati. Prima di distribuire il dispositivo Azure Stack Edge Pro, esaminare attentamente le informazioni contenute nelle note sulla versione.

Questo articolo si applica alla versione software seguente: **Azure stack Edge Pro 2008**. 

<!--- **2.1.1328.1904**-->

## <a name="whats-new"></a>Novità

Le nuove funzionalità seguenti sono state aggiunte in Azure Stack versione 2008 Edge. A seconda della versione di anteprima specifica del software in esecuzione, è possibile che venga visualizzato un subset di queste funzionalità. 

- **Classi di archiviazione** : nella versione precedente, è possibile effettuare il provisioning statico solo di archiviazione tramite condivisioni SMB o NFS per le applicazioni con stato distribuite nel cluster Kubernetes in esecuzione nel dispositivo Azure stack Edge Pro. In questa versione sono state aggiunte le classi di archiviazione che consentono di eseguire dinamicamente il provisioning dell'archiviazione. Per altre informazioni, vedere [gestione dell'archiviazione Kubernetes nel dispositivo GPU Pro Azure stack Edge](azure-stack-edge-gpu-kubernetes-storage.md#dynamicprovisioning). 
- **Dashboard di Kubernetes con server delle metriche** : in questa versione viene aggiunto un dashboard di Kubernetes con un componente aggiuntivo per il server di metriche. È possibile usare il dashboard per ottenere una panoramica delle applicazioni in esecuzione sul dispositivo Azure Stack Edge Pro, visualizzare lo stato delle risorse del cluster Kubernetes e visualizzare gli eventuali errori verificatisi nel dispositivo. Il server delle metriche aggrega l'utilizzo di CPU e memoria tra le risorse Kubernetes nel dispositivo. Per altre informazioni, vedere [usare il dashboard di Kubernetes per monitorare il dispositivo GPU Pro Azure stack Edge](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).
- **Azure Arc for Azure stack Edge Pro** : a partire da questa versione, è possibile distribuire i carichi di lavoro delle applicazioni sul dispositivo Azure stack Edge Pro tramite Azure Arc. Azure Arc è uno strumento di gestione ibrido che consente di distribuire applicazioni nei cluster Kubernetes. Per altre informazioni, vedere [distribuire carichi di lavoro tramite Azure Arc sul dispositivo Azure stack Edge Pro](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).  

## <a name="known-issues"></a>Problemi noti 

La tabella seguente fornisce un riepilogo dei problemi noti relativi al dispositivo Azure Stack Edge Pro.

| No. | Funzionalità | Problema | Soluzione alternativa/commenti |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge Pro e Azure SQL | Per la creazione del database SQL è necessario l'accesso amministratore.   |Eseguire i passaggi seguenti anziché i passaggi 1-2 in [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database) . <ul><li>Nell'interfaccia utente locale del dispositivo abilitare l'interfaccia di calcolo. Selezionare **calcolo > porta # > Abilita per calcolo > applica.**</li><li>Scarica `sqlcmd` dal computer client da https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>Connettersi all'indirizzo IP dell'interfaccia di calcolo (la porta abilitata), aggiungendo ", 1401" alla fine dell'indirizzo.</li><li>Il comando finale sarà simile al seguente: sqlcmd-S {Interface IP}, 1401-U SA-P "Strong! Passw0rd ".</li>Al termine di questa operazione, i passaggi 3-4 dalla documentazione corrente devono essere identici. </li></ul> |
| **2.** |Aggiorna| Le modifiche incrementali apportate ai BLOB ripristinati tramite l' **aggiornamento** non sono supportate |Per gli endpoint BLOB, gli aggiornamenti parziali dei BLOB dopo un aggiornamento possono comportare il caricamento degli aggiornamenti nel cloud. Ad esempio, sequenza di azioni come:<ul><li>Creare un BLOB nel cloud. O eliminare un BLOB caricato in precedenza dal dispositivo.</li><li>Aggiornare il BLOB dal cloud al dispositivo usando la funzionalità di aggiornamento.</li><li>Aggiornare solo una parte del BLOB usando le API REST di Azure SDK.</li></ul>Queste azioni possono comportare l'aggiornamento delle sezioni aggiornate del BLOB nel cloud. <br>**Soluzione alternativa**: usare strumenti come Robocopy o una normale copia di file tramite Esplora risorse o la riga di comando per sostituire i BLOB interi.|
|**3.**|Limitazione|Durante la limitazione, se nel dispositivo non sono consentite nuove scritture, le scritture eseguite dal client NFS avranno esito negativo con l'errore "autorizzazione negata".| L'errore verrà visualizzato come riportato di seguito:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: non è possibile creare la directory ' test ': autorizzazione negata|
|**4.**|Inserimento di archiviazione BLOB|Quando si usa AzCopy versione 10 per l'inserimento dell'archiviazione BLOB, eseguire AzCopy con l'argomento seguente: `Azcopy <other arguments> --cap-mbps 2000`| Se questi limiti non vengono forniti per AzCopy, potrebbe inviare un numero elevato di richieste al dispositivo e causare problemi con il servizio.|
|**5.**|Account di archiviazione a livelli|Quando si usano gli account di archiviazione a livelli, si applica quanto segue:<ul><li> Sono supportati solo i BLOB in blocchi. I BLOB di pagine non sono supportati.</li><li>Non è disponibile alcun supporto per snapshot o copia API.</li><li> L'inserimento del carico di lavoro Hadoop tramite `distcp` non è supportato perché usa l'operazione di copia.</li></ul>||
|**6.**|Connessione NFS share|Se più processi eseguono la copia nella stessa condivisione e l' `nolock` attributo non viene utilizzato, è possibile che vengano visualizzati errori durante la copia.|L' `nolock` attributo deve essere passato al comando mount per copiare i file nella condivisione NFS. Ad esempio: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**7.**|Cluster Kubernetes|Quando si applica un aggiornamento sul dispositivo che esegue un cluster kubernetes, le macchine virtuali kubernetes vengono riavviate e riavviate. In questo caso, solo i pod distribuiti con le repliche specificate vengono ripristinati automaticamente dopo un aggiornamento.  |Se sono stati creati singoli pod all'esterno di un controller di replica senza specificare un set di repliche, questi Pod non verranno ripristinati automaticamente dopo l'aggiornamento del dispositivo. Sarà necessario ripristinare i pod.<br>Un set di repliche sostituisce i pod eliminati o interrotti per qualsiasi motivo, ad esempio un errore del nodo o un aggiornamento del nodo di interruzione. Per questo motivo, è consigliabile usare un set di repliche anche se l'applicazione richiede un singolo POD.|
|**8.**|Cluster Kubernetes|Kubernetes su Azure Stack Edge Pro è supportato solo con Helm V3 o versione successiva. Per altre informazioni, vedere [domande frequenti: rimozione di un timone](https://v3.helm.sh/docs/faq/).|
|**9.**|Azure Arc + Azure Stack Edge Pro|Le distribuzioni di Azure Arc non sono supportate se il proxy Web è configurato sul dispositivo Azure Stack Edge Pro.||
|**10.**|Kubernetes |La porta 31000 è riservata per il dashboard di Kubernetes. Analogamente, nella configurazione predefinita, gli indirizzi IP 172.28.0.1 e 172.28.0.10 sono riservati rispettivamente per il servizio Kubernetes e per il servizio DNS di base.|Non usare IP riservati.|
|**11.**|Kubernetes |Kubernetes attualmente non consente i servizi LoadBalancer multiprotocollo. Ad esempio, un servizio DNS che avrebbe dovuto rimanere in attesa su TCP e UDP. |Per ovviare a questa limitazione di Kubernetes con MetalLB, è possibile creare due servizi (uno per TCP, uno per UDP) nello stesso selettore di Pod. Questi servizi usano la stessa chiave di condivisione e spec. loadBalancerIP per condividere lo stesso indirizzo IP. Gli IP possono anche essere condivisi se si dispone di più servizi rispetto agli indirizzi IP disponibili. <br> Per altre informazioni, vedere [condivisione degli indirizzi IP](https://metallb.universe.tf/usage/#ip-address-sharing).|
|**12.**|Cluster Kubernetes|I moduli esistenti di Azure IoT Edge Marketplace non vengono eseguiti nel cluster Kubernetes come piattaforma di hosting per IoT Edge sul dispositivo Azure Stack Edge.|I moduli dovranno essere modificati prima di essere distribuiti nel dispositivo Azure Stack Edge. Per altre informazioni, vedere Modificare Azure IoT Edge moduli da Marketplace per l'esecuzione nel dispositivo Azure Stack Edge.<!-- insert link-->|
|**13.**|Kubernetes |I montaggi con binding basato su file non sono supportati con Azure IoT Edge in Kubernetes sul dispositivo Azure Stack Edge.|IoT Edge usa un livello di conversione per tradurre le `ContainerCreate` Opzioni nei costrutti Kubernetes. La creazione di `Binds` mappe per la directory hostPath o la creazione e quindi il montaggio di binding basati su file non possono essere associati a percorsi in IOT Edge contenitori.|
|**14.**|Kubernetes |Se si importano certificati personalizzati per IoT Edge e si aggiungono quelli sul dispositivo Azure Stack Edge, i nuovi certificati non vengono selezionati come parte dell'aggiornamento dei grafici Helm.|Per aggirare questo problema, [connettersi all'interfaccia di PowerShell del dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md). Riavviare `iotedged` e `edgehub` pod.|
|**15.**|Certificati |In alcuni casi, lo stato del certificato nell'interfaccia utente locale può richiedere alcuni secondi per l'aggiornamento. |Potrebbero essere interessati gli scenari seguenti nell'interfaccia utente locale.<ul><li>Colonna **stato** nella pagina **certificati** .</li><li>Riquadro **sicurezza** nella pagina attività **iniziali** .</li><li>Riquadro **configurazione** nella pagina **Panoramica** .</li></ul>  |

## <a name="next-steps"></a>Passaggi successivi

- [Preparare la distribuzione di Azure Stack dispositivo Edge Pro con GPU](azure-stack-edge-gpu-deploy-prep.md)