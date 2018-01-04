---
title: Proteggere un file server usando Azure Site Recovery
description: Questo articolo descrive come proteggere un file server usando Azure Site Recovery
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: ac734ffc6cb57188b7b0959cbe7655949b2853de
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="protect-a-file-server-using-azure-site-recovery"></a>Proteggere un file server usando Azure Site Recovery 

Il servizio [Azure Site Recovery](site-recovery-overview.md) contribuisce a realizzare la strategia di continuità aziendale e ripristino di emergenza (BCDR) mantenendo disponibili e operative le app aziendali durante interruzioni pianificate e non pianificate. Site Recovery gestisce e orchestra il ripristino di emergenza di computer locali e macchine virtuali di Azure, incluse le operazioni di replica, failover e ripristino di vari carichi di lavoro.

Questo articolo descrive come proteggere un file server usando Azure Site Recovery e include altre raccomandazioni specifiche per i vari ambienti.     

- [Replicare macchine virtuali file server IaaS di Azure](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [Replicare un file server locale usando Azure Site Recovery](#replicate-an-onpremises-file-server-using-azure-site-recovery)

## <a name="file-server-architecture"></a>Architettura del file server
L'obiettivo di un sistema di condivisione distribuito aperto consiste nel fornire un ambiente in cui un gruppo di utenti distribuiti geograficamente può collaborare per lavorare sui file in modo efficiente e garantire che vengano applicati i requisiti di integrità. Un ecosistema di file server locale tipico che supporta un numero elevato di utenti simultanei e un numero elevato di elementi di contenuto usa la replica DFS (file system distribuito) per pianificare le repliche e limitare la larghezza di banda. La replica DFS usa un algoritmo di compressione, noto come compressione RDC (Remote Differential Compression), che consente di aggiornare in modo efficiente i file in una rete con larghezza di banda limitata. Rileva gli inserimenti, le rimozioni e le riorganizzazioni di dati nei file consentendo di replicare solo i blocchi di file modificati quando vengono aggiornati i file. Esistono anche ambienti di file server, in cui i backup giornalieri vengono eseguiti a intervalli non di punta, che soddisfano le esigenze di emergenza e non prevedono l'implementazione della replica DFS.

La topologia seguente illustra l'ambiente di file server in cui è implementata la replica DFS.
                
![Architettura con replica DFS](media/site-recovery-file-server/dfsr-architecture.JPG)

Nel riferimento precedente più file server, a cui viene fatto riferimento come membri, partecipano attivamente alla replica di file in un gruppo di replica. Il contenuto nella cartella replicata sarà disponibile a tutti i client inviando richieste a uno qualsiasi dei membri, anche in caso di disconnessione di uno dei membri.

## <a name="disaster-recovery-recommendation-for-file-servers"></a>Raccomandazione sul ripristino di emergenza per i file server:

1.  Replicare un file server usando Azure Site Recovery: i file server possono essere replicati in Azure usando Azure Site Recovery. Quando non è possibile accedere a uno o più file server in locale, è possibile attivare le macchine virtuali di ripristino in Azure che possono soddisfare le richieste dei client in locale, purché sia disponibile la connettività VPN da sito a sito e Active Directory sia configurato in Azure. Questa operazione può essere eseguita in un ambiente configurato con la replica DFS o in un ambiente di file server semplice senza la replica DFS. 

2.  Estendere la replica DFS a una macchina virtuale Iaas di Azure: in un ambiente di file server in cluster con la replica DFS implementata, un approccio suggerito consiste nell'estendere la replica DFS locale in Azure. Una macchina virtuale di Azure viene quindi abilitata per eseguire il ruolo di file server. 

    Dopo che sono state gestite le dipendenze della connettività VPN da sito a sito e di Active Directory e che è stata applicata la replica DFS, quando non è possibile accedere a uno o più file server locali, i client possono connettersi alla macchina virtuale di Azure che soddisferà le richieste.

    Questo approccio è consigliabile nel caso in cui le macchine virtuali abbiano configurazioni non supportate da Azure Site Recovery, ad esempio un disco cluster condiviso che viene in alcuni casi usato comunemente negli ambienti di file server.  La replica DFS funziona correttamente anche negli ambienti con larghezza di banda ridotta con varianza media. Deve essere tenuto in considerazione anche il costo aggiuntivo dovuto a una macchina virtuale di Azure sempre in esecuzione.  

3.  Usare il servizio Sincronizzazione file di Azure per replicare i file: se si sta preparando il passaggio al cloud o si sta già usando una macchina virtuale di Azure, è consigliabile usare il servizio Sincronizzazione file di Azure che offre la sincronizzazione delle condivisioni file completamente gestite nel cloud accessibili tramite il protocollo [Server Message Block](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) (SMB) standard di settore. Le condivisioni file di Azure possono essere montate simultaneamente da distribuzioni cloud o locali di Windows, macOS e Linux. 

Il diagramma seguente offre una rappresentazione grafica per facilitare la scelta della strategia da usare per l'ambiente di file server.

![decisiontree](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-while-making-decision-of-disaster-recovery-to-azure"></a>Fattori da considerare per la decisione di un ripristino di emergenza in Azure

|Environment  |Raccomandazione  |Elementi da considerare: |
|---------|---------|---------|
|Ambiente di file server con/senza replica DFS|   [Usare Azure Site Recovery per la replica](#replicate-an-onpremises-file-servers-using-azure-site-recovery)   |    Azure Site Recovery non supporta il cluster di dischi condivisi, il dispositivo NAS. Se nell'ambiente è presente una di queste configurazioni, usare uno degli altri approcci in base alle esigenze. <br> Azure Site Recovery non supporta il protocollo SMB 3.0, il che significa che solo quando le modifiche apportate ai file vengono aggiornate nella posizione originale del file, la macchina virtuale replicata incorporerà le modifiche.
|Ambiente di file server con replica DFS     |  [Estendere la replica DFS a una macchina virtuale IaaS di Azure:](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |     La replica DFS funziona correttamente in ambienti di larghezza di banda estremamente ridotta, ma questo approccio richiede la presenza di una macchina virtuale di Azure sempre in esecuzione. Il costo della macchina virtuale deve essere tenuto in considerazione nella pianificazione.         |
|Macchina virtuale IaaS di Azure     |     [Sincronizzazione file di Azure](#use-azure-file-sync-service-to-replicate-your-files)   |     In uno scenario di ripristino di emergenza, se si usa Sincronizzazione file di Azure, durante il failover manuale è necessario eseguire azioni per garantire che le condivisioni file siano accessibili in modo trasparente al computer client. AFS richiede che la porta 445 sia aperta dal computer client.     |


### <a name="site-recovery-support"></a>Supporto di Site Recovery
Poiché la replica di Site Recovery è indipendente dall'applicazione, le raccomandazioni incluse in questo articolo saranno valide anche per gli scenari seguenti:
| Sorgente    |In un sito secondario    |In Azure
|---------|---------|---------|
|Azure| -|Sì|
|Hyper-V|   Sì |Sì
|VMware |Sì|   Sì
|Server fisico|   Sì |Sì
 

> [!IMPORTANT]
> Prima di procedere con uno qualsiasi dei tre approcci di seguito, assicurarsi che siano state gestite le seguenti dipendenze:

**Connettività da sito a sito**: è necessario stabilire la connessione diretta tra il sito locale e la rete di Azure per consentire la comunicazione tra i server.  Questa condizione può essere garantita da una connessione VPN da sito a sito protetta a una rete virtuale di Microsoft Azure che verrà usata come sito di ripristino di emergenza.  
Vedere [Connessione VPN da sito a sito tra sito locale e rete Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)

**Active Directory**: la replica DFS dipende da Active Directory.  Ciò significa che la foresta Active Directory con i controller di dominio locali viene estesa al sito di ripristino di emergenza in Azure. Anche se non si usa la replica DFS occorre eseguire questi passaggi se è necessario assegnare/verificare l'accesso agli utenti desiderati come nella maggior parte delle organizzazioni.
Fare riferimento: [Estendere Active Directory locale ad Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-active-directory).

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Raccomandazioni sul ripristino di emergenza per le macchine virtuali IaaS di Azure

Se si configura e gestisce il ripristino di emergenza di file server in hosting nelle macchine virtuali IaaS di Azure, è possibile scegliere tra due opzioni, a seconda che si voglia o meno passare a [File di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

1. [Usare Sincronizzazione file di Azure](#use-azure-file-sync-service-to-replicate-files-hosted-on-iaas-virtual-machine)
2. [Usare Azure Site Recovery](#replicate-an-iaas-file-server-virtual-machine-using-azure-site-recovery)

## <a name="use-azure-file-sync-service-to-replicate-files-hosted-on-iaas-virtual-machine"></a>Usare il servizio Sincronizzazione file di Azure per replicare file ospitati nella macchina virtuale IaaS

È possibile usare **File di Azure** per sostituire completamente o integrare i dispositivi NAS o i file server locali tradizionali. Le condivisioni di File di Azure possono anche essere replicate nei server Windows con Sincronizzazione file di Azure, in locale o nel cloud, per il caching efficiente e distribuito dei dati lì dove vengono usati. I passaggi seguenti descrivono in dettaglio la raccomandazione sul ripristino di emergenza per le macchine virtuali di Azure che eseguono la stessa funzionalità dei file server tradizionali:
1.  Proteggere le macchine con Azure Site Recovery usando i passaggi indicati [qui](azure-to-azure-quickstart.md).
2.  Usare Sincronizzazione file di Azure per replicare i file dalla macchina virtuale che funge da file server al cloud.
3.  Usare la funzionalità di [pianificazione del ripristino](site-recovery-create-recovery-plans.md) di Azure Site Recovery per aggiungere gli script per [montare la condivisione file di Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) e accedere alla condivisione nella macchina virtuale.

I passaggi seguenti descrivono brevemente come usare il servizio Sincronizzazione file di Azure:

1. [Creare un account di archiviazione in Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Se si è scelto Archiviazione con ridondanza geografica e accesso in lettura per gli account di archiviazione, si ottiene l'accesso in lettura ai dati dall'area secondaria in caso di emergenza. Per altre informazioni, consultare le [strategie di ripristino di emergenza della condivisione file di Azure](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
2. [Creare una condivisione file](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Distribuire Sincronizzazione file di Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) sul file server di Azure.
4. Creare un gruppo di sincronizzazione: gli endpoint all'interno di un gruppo di sincronizzazione vengono mantenuti sincronizzati tra loro. Un gruppo di sincronizzazione deve contenere almeno un endpoint cloud, che rappresenta una condivisione file di Azure, e un endpoint server, che rappresenta un percorso in Windows Server.
5.  I file ora verranno mantenuti sincronizzati tra la condivisione file di Azure e il server locale.
6.  In caso di emergenza nell'ambiente locale, procedere all'esecuzione come failover usando un [piano di ripristino](site-recovery-create-recovery-plans.md) e aggiungere lo script per [montare la condivisione file di Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) e accedere alla condivisione nella macchina virtuale.

### <a name="replicate-an-iaas-file-server-virtual-machine-using-azure-site-recovery"></a>Replicare una macchina virtuale che funge da file server IaaS usando Azure Site Recovery

Se sono disponibili client locali che accedono alla macchina virtuale file server IaaS eseguire anche i primi due passaggi, altrimenti proseguire con il passaggio 3.

1. Stabilire la connessione VPN da sito a sito tra il sito locale e la rete Azure.
1. Estendere Active Directory locale.
1. [Configurare il ripristino di emergenza](azure-to-azure-tutorial-enable-replication.md) per il computer di file server IaaS in un'area secondaria.


Per altre informazioni sul ripristino di emergenza in un'area secondaria, fare riferimento [qui](concepts-azure-to-azure-architecture.md).


## <a name="replicate-an-on-premises-file-server-using-azure-site-recovery"></a>Replicare un file server locale usando Azure Site Recovery

La procedura seguente descrive in dettaglio la replica per una macchina virtuale VMware. Per i passaggi da seguire per replicare una macchina virtuale Hyper-V, vedere [qui](tutorial-hyper-v-to-azure.md).

1.  [Preparare le risorse di Azure](tutorial-prepare-azure.md) per la replica dei computer locali.
2.  Stabilire la connessione VPN da sito a sito tra il sito locale e la rete Azure.  
3. Estendere Active Directory locale.
4.  [Preparare i server VMware locali](tutorial-prepare-on-premises-vmware.md).
5.  [Configurare il ripristino di emergenza](tutorial-vmware-to-azure.md) in Azure per le macchine virtuali locali.

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>Estendere la replica DFS a una macchina virtuale IaaS di Azure:

1.  Stabilire la connessione VPN da sito a sito tra il sito locale e la rete Azure. 
2.  Estendere Active Directory locale.
3.  [Creare ed eseguire il provisioning di un file server di VM](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) nella rete virtuale di Microsoft Azure.
Verificare che la macchina virtuale sia stata aggiunta alla stessa rete virtuale di Microsoft Azure, che dispone della Cross Connection con l'ambiente locale. 
4.  Installare e [configurare la replica DFS](https://blogs.technet.microsoft.com/b/filecab/archive/2013/08/21/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of-the-clones.aspx) in Windows Server.
5.  [Implementare uno spazio dei nomi DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces).
6.  Con lo spazio dei nomi DFS implementato, è possibile eseguire il failover delle cartelle condivise dalla produzione ai siti di ripristino di emergenza aggiornando le destinazioni di cartelle dello spazio dei nomi DFS.  Dopo che queste modifiche dello spazio dei nomi DFS sono state replicate tramite Active Directory, gli utenti vengono connessi alle destinazioni di cartelle appropriate in modo trasparente.

## <a name="use-azure-file-sync-service-to-replicate-your-on-premises-files"></a>Usare il servizio Sincronizzazione file di Azure per replicare i file locali:
Tramite il servizio Sincronizzazione file di Azure è possibile replicare i file desiderati nel cloud; in caso di emergenza e non disponibilità del file server locale è quindi possibile montare i percorsi di file desiderati dal cloud e continuare a soddisfare le richieste del servizio dalle macchine client.
L'approccio consigliato per l'integrazione di Sincronizzazione file di Azure è il seguente:
1.  Proteggere le macchine virtuali file server tramite Azure Site Recovery seguendo i passaggi indicati [qui](tutorial-vmware-to-azure.md).
2.  Usare Sincronizzazione file di Azure per replicare i file dal computer che funge da file server, nel cloud.
3.  Usare la funzionalità di pianificazione di ripristino di Azure Site Recovery per aggiungere script per montare la condivisione file di Azure sulla macchina virtuale file server in cui è stato eseguito il failover in Azure.

I passaggi di seguito descrivono in dettaglio l'utilizzo del servizio Sincronizzazione file di Azure:

1. [Creare un account di archiviazione in Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Se si è scelto Archiviazione con ridondanza geografica e accesso in lettura (consigliato) per gli account di archiviazione, si ha l'accesso in lettura ai dati dall'area secondaria in caso di emergenza. Per altre informazioni, consultare le [strategie di ripristino di emergenza della condivisione file di Azure](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
2. [Creare una condivisione file](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Distribuire Sincronizzazione file di Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) nel file server locale.
4. Creare un gruppo di sincronizzazione: gli endpoint all'interno di un gruppo di sincronizzazione vengono mantenuti sincronizzati tra loro. Un gruppo di sincronizzazione deve contenere almeno un endpoint cloud, che rappresenta una condivisione file di Azure, e un endpoint server, che rappresenta un percorso in Windows Server locale.
1. I file ora verranno mantenuti sincronizzati tra la condivisione file di Azure e il server locale.
6.  In caso di emergenza nell'ambiente locale procedere all'esecuzione come failover usando un [piano di ripristino](site-recovery-create-recovery-plans.md) e aggiungere lo script per montare la condivisione file di Azure e accedere alla condivisione nella macchina virtuale.

> [!NOTE]
> Assicurarsi che la porta 445 sia aperta: File di Azure usa il protocollo SMB. SMB comunica tramite la porta TCP 445: verificare che il firewall non blocchi le porte TCP 445 dal computer client.


## <a name="doing-a-test-failover"></a>Esecuzione di un failover di test

1.  Accedere al portale di Azure e selezionare l'insieme di credenziali di Servizi di ripristino.
2.  Fare clic sul piano di ripristino creato per l'ambiente di file server.
3.  Fare clic su 'Failover di test'.
4.  Selezionare il punto di recupero e la rete virtuale di Azure per avviare il processo di failover di test.
5.  Quando l'ambiente secondario è disponibile, è possibile eseguire le convalide.
6.  Al termine delle convalide, è possibile fare clic su "Pulizia failover di test" nel piano di ripristino perché l'ambiente di failover di test venga pulito.

Per altre informazioni sull'esecuzione del failover di test, vedere [qui](site-recovery-test-failover-to-azure.md).

Per le linee guida su come eseguire il failover di test per Active Directory e DNS, fare riferimento a [Considerazioni sul failover di test per Active Directory e DNS](site-recovery-active-directory.md).

## <a name="doing-a-failover"></a>Esecuzione di un failover

1.  Accedere al portale di Azure e selezionare l'insieme di credenziali di Servizi di ripristino.
2.  Fare clic sul piano di ripristino creato per l'ambiente di file server.
3.  Fare clic su 'Failover'.
4.  Selezionare il punto di recupero per avviare il processo di failover.

Per altre informazioni sull'esecuzione di un failover di test, vedere [qui](site-recovery-failover.md).
