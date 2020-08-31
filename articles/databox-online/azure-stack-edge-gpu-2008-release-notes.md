---
title: Note sulla versione di Azure Stack Edge Preview | Microsoft Docs
description: Vengono descritti i problemi critici aperti e le risoluzioni per la Azure Data Box Gateway esecuzione della versione di disponibilità generale.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/27/2020
ms.author: alkohli
ms.openlocfilehash: 6c29240aa3267cd93ba0c3de1f0c797ce1a1483c
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89086162"
---
# <a name="azure-stack-edge-with-gpu-preview-release-notes"></a>Note sulla versione di Azure Stack Edge con GPU Preview

Le note sulla versione seguenti identificano i problemi critici aperti e i problemi risolti per la versione di anteprima 2008 per i dispositivi Azure Stack Edge con GPU.

Le note sulla versione vengono aggiornate continuamente e i problemi che richiedono una soluzione alternativa vengono aggiunti man mano che vengono individuati. Prima di distribuire il dispositivo Azure Stack Edge, esaminare attentamente le informazioni contenute nelle note sulla versione.

Questa versione di **Azure stack Edge 2008** corrisponde alla versione del software seguente:

- **2.1.1328.1904**



## <a name="known-issues-in-ga-release"></a>Problemi noti nella versione GA

La tabella seguente fornisce un riepilogo dei problemi noti per il dispositivo Azure Stack Edge.

| No. | Funzionalità | Problema | Soluzione alternativa/commenti |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge e Azure SQL | Per la creazione del database SQL è necessario l'accesso amministratore.   |Eseguire i passaggi seguenti anziché i passaggi 1-2 in [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database) . <ul><li>Nell'interfaccia utente locale del dispositivo abilitare l'interfaccia di calcolo. Selezionare **calcolo > porta # > Abilita per calcolo > applica.**</li><li>In una finestra di PowerShell connessa al dispositivo eseguire `Add-HcsComputeNetwork` . </li><li>Scarica `sqlcmd` dal computer client da https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>Connettersi all'indirizzo IP dell'interfaccia di calcolo (la porta abilitata), aggiungendo ", 1401" alla fine dell'indirizzo.</li><li>Il comando finale sarà simile al seguente: sqlcmd-S {Interface IP}, 1401-U SA-P "Strong! Passw0rd ".</li>Al termine di questa operazione, i passaggi 3-4 dalla documentazione corrente devono essere identici. </li></ul> |
| **2.** |Aggiorna| Le modifiche incrementali apportate ai BLOB ripristinati tramite l' **aggiornamento** non sono supportate |Per gli endpoint BLOB, gli aggiornamenti parziali dei BLOB dopo un aggiornamento possono comportare il caricamento degli aggiornamenti nel cloud. Ad esempio, sequenza di azioni come:<ul><li>Creare un BLOB nel cloud. O eliminare un BLOB caricato in precedenza dal dispositivo.</li><li>Aggiornare il BLOB dal cloud al dispositivo usando la funzionalità di aggiornamento.</li><li>Aggiornare solo una parte del BLOB usando le API REST di Azure SDK.</li></ul>Queste azioni possono comportare l'aggiornamento delle sezioni aggiornate del BLOB nel cloud. <br>**Soluzione alternativa**: usare strumenti come Robocopy o una normale copia di file tramite Esplora risorse o la riga di comando per sostituire i BLOB interi.|
|**3.**|Limitazione|Durante la limitazione, se nel dispositivo non sono consentite nuove scritture, le scritture eseguite dal client NFS avranno esito negativo con l'errore "autorizzazione negata".| L'errore verrà visualizzato come riportato di seguito:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: non è possibile creare la directory ' test ': autorizzazione negata|
|**4.**|Inserimento di archiviazione BLOB|Quando si usa AzCopy versione 10 per l'inserimento dell'archiviazione BLOB, eseguire AzCopy con l'argomento seguente: `Azcopy <other arguments> --cap-mbps 2000`| Se questi limiti non vengono forniti per AzCopy, potrebbe inviare un numero elevato di richieste al dispositivo e causare problemi con il servizio.|
|**5.**|Account di archiviazione a livelli|Quando si usano gli account di archiviazione a livelli, si applica quanto segue:<ul><li> Sono supportati solo i BLOB in blocchi. I BLOB di pagine non sono supportati.</li><li>Non è disponibile alcun supporto per snapshot o copia API.</li><li> L'inserimento del carico di lavoro Hadoop tramite `distcp` non è supportato perché usa l'operazione di copia.</li></ul>||
|**6.**|Connessione NFS share|Se più processi eseguono la copia nella stessa condivisione e l' `nolock` attributo non viene utilizzato, è possibile che vengano visualizzati errori durante la copia.|L' `nolock` attributo deve essere passato al comando mount per copiare i file nella condivisione NFS. Ad esempio: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**7.**|Cluster Kubernetes|Quando si applica un aggiornamento sul dispositivo che esegue un cluster kubernetes, le macchine virtuali kubernetes vengono riavviate e riavviate. In questo caso, solo i pod distribuiti con le repliche specificate vengono ripristinati automaticamente dopo un aggiornamento.  |Se sono stati creati singoli pod all'esterno di un controller di replica senza specificare un set di repliche, questi Pod non verranno ripristinati automaticamente dopo l'aggiornamento del dispositivo. Sarà necessario ripristinare i pod.<br>Un set di repliche sostituisce i pod eliminati o interrotti per qualsiasi motivo, ad esempio un errore del nodo o un aggiornamento del nodo di interruzione. Per questo motivo, è consigliabile usare un set di repliche anche se l'applicazione richiede un singolo POD.|
|**8.**|Cluster Kubernetes|Kubernetes su Azure Stack Edge è supportato solo con Helm V3 o versione successiva. Per altre informazioni, vedere [domande frequenti: rimozione di un timone](https://v3.helm.sh/docs/faq/).|
|**9.**|Azure Arc + Azure Stack Edge|Le distribuzioni di Azure Arc non sono supportate se il proxy Web è configurato nel dispositivo Azure Stack Edge.||
|**10.**|Kubernetes |La porta 31000 è riservata per il dashboard di Kubernetes. Analogamente, nella configurazione predefinita, gli indirizzi IP 10.96.0.1 e 10.96.0.10 sono riservati rispettivamente per il servizio Kubernetes e per il servizio DNS di base.|Non usare IP riservati.|
|**11.**|Kubernetes |Kubernetes attualmente non consente i servizi LoadBalancer multiprotocollo. Ad esempio, un servizio DNS che avrebbe dovuto rimanere in attesa su TCP e UDP. |Per ovviare a questa limitazione di Kubernetes con MetalLB, è possibile creare due servizi (uno per TCP, uno per UDP) nello stesso selettore di Pod. Questi servizi usano la stessa chiave di condivisione e spec. loadBalancerIP per condividere lo stesso indirizzo IP. Gli IP possono anche essere condivisi se si dispone di più servizi rispetto agli indirizzi IP disponibili. <br> Per altre informazioni, vedere [condivisione degli indirizzi IP](https://metallb.universe.tf/usage/#ip-address-sharing).|
|**12.**|Cluster Kubernetes|I moduli esistenti di Azure IoT Edge Marketplace non vengono eseguiti nel cluster Kubernetes come piattaforma di hosting per IoT Edge sul dispositivo Azure Stack Edge.|I moduli dovranno essere modificati prima di essere distribuiti nel dispositivo Azure Stack Edge. Per altre informazioni, vedere Modificare Azure IoT Edge moduli da Marketplace per l'esecuzione nel dispositivo Azure Stack Edge.<!-- insert link-->|
|**13.**|Kubernetes |I montaggi con binding basato su file non sono supportati con Azure IoT Edge in Kubernetes sul dispositivo Azure Stack Edge.|IoT Edge usa un livello di conversione per tradurre le `ContainerCreate` Opzioni nei costrutti Kubernetes. La creazione di `Binds` mappe per la directory hostPath o la creazione e quindi il montaggio di binding basati su file non possono essere associati a percorsi in IOT Edge contenitori.|


## <a name="next-steps"></a>Passaggi successivi

- [Preparare la distribuzione di Azure Stack dispositivo perimetrale con GPU](azure-stack-edge-gpu-deploy-prep.md)

