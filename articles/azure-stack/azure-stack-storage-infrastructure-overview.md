---
title: Gestire l'infrastruttura di archiviazione per Azure Stack | Microsoft Docs
description: Gestire l'infrastruttura di archiviazione per Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: ''
ms.date: 03/11/2019
ms.author: mabrigg
ms.lastreviewed: 03/11/2019
ms.reviewer: jiahan
ms.openlocfilehash: 4a8287d7ca4da380ad7c2b1e039ab3058ca07a96
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57760274"
---
# <a name="manage-storage-infrastructure-for-azure-stack"></a>Gestire l'infrastruttura di archiviazione per Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Questo articolo descrive l'integrità e lo stato operativo delle risorse di infrastruttura di archiviazione di Azure Stack. Queste risorse includono le unità di archiviazione e volumi. Le informazioni contenute in questo argomento possono essere estremamente utile quando si tenta di risolvere i problemi vari, ad esempio un'unità non è possibile aggiungere a un pool.

## <a name="understand-drives-and-volumes"></a>Comprendere le unità e i volumi

### <a name="drives"></a>Unità

Azure Stack con tecnologia software Windows Server definita funzionalità di archiviazione, con una combinazione di spazi di archiviazione è diretta (S2D) e Windows Server Failover Clustering, per fornire un servizio di archiviazione scalabile e resiliente e ad alte prestazioni.

Partner di Azure Stack integrato system offrono numerose variazioni di soluzioni, tra cui una vasta gamma di flessibilità di archiviazione. È attualmente possibile selezionare una combinazione dei tre tipi di unità: NVMe (memoria Non Volatile Express), unità SSD (unità SSD), SATA o SAS HDD (unità disco rigido).

Spazi di archiviazione diretta è dotato di una cache per ottimizzare le prestazioni di archiviazione. Nel dispositivo di Azure Stack con uno o più tipi di unità, spazi di archiviazione diretta usano automaticamente tutte le unità di "più rapidamente" (NVMe &gt; SSD &gt; HDD) tipo per la memorizzazione nella cache. Le unità rimanenti vengono usate per la capacità. Le unità potrebbero essere raggruppate in "all-flash" o "ibrido" distribuzione:

![Infrastruttura di archiviazione di Azure Stack](media/azure-stack-storage-infrastructure-overview/image1.png)

All-flash mira le distribuzioni per ottimizzare le prestazioni di archiviazione e non includono le unità disco rigido rotazionale (HDD).

![Infrastruttura di archiviazione di Azure Stack](media/azure-stack-storage-infrastructure-overview/image2.png)

Le distribuzioni ibride hanno lo scopo di bilanciare prestazioni e la capacità o per ottimizzare capacità e includono le unità disco rigido rotazionale (HDD).

Il comportamento della cache è determinato automaticamente in base i tipi di unità che sono la memorizzazione nella cache per. Quando la memorizzazione nella cache per unità SSD (ad esempio NVMe la memorizzazione nella cache per le unità SSD), solo operazioni di scrittura vengono memorizzati nella cache. In questo modo si riduce usura delle unità di capacità, riducendo il traffico cumulativo per le unità di capacità e l'estensione relativa durata. Nel frattempo, poiché le letture non influiscono in modo significativo la durata della memoria flash e poiché le unità SSD universalmente offrono bassa latenza di lettura, letture non vengono memorizzate. Quando la memorizzazione nella cache per unità disco rigido (ad esempio unità SSD la memorizzazione nella cache per dischi HDD), operazioni di lettura e scrittura viene memorizzati nella cache, per garantire una latenza simile a flash (spesso ~ 10 volte migliore) per entrambi.

![Infrastruttura di archiviazione di Azure Stack](media/azure-stack-storage-infrastructure-overview/image3.png)

Per la configurazione a disponibilità di spazio di archiviazione, è possibile controllare i partner di Azure Stack OEM (https://azure.microsoft.com/overview/azure-stack/partners/) per specifiche dettagliate.

> [!Note]  
> Appliance Azure Stack può essere distribuito in una distribuzione ibrida, con le unità HDD e SSD (sia NVMe). Ma le unità del tipo più veloce verrà usate come unità di cache, e tutte le unità rimanenti vengono usate come unità di capacità come un pool. I dati dei tenant (BLOB, tabelle, code e dischi) verranno inseriti in unità di capacità. Pertanto, il provisioning di dischi premium o la selezione account di archiviazione premium non significa che gli oggetti di tipo è garantita da allocare in unità SSD o NVMe, unità e ottenere prestazioni migliori.

### <a name="volumes"></a>Volumi

Il *servizio di archiviazione* suddivide lo spazio di archiviazione disponibile in volumi distinti che vengono allocati per contenere i dati di sistema e del tenant. Volumi di combinano le unità nel pool di archiviazione per introdurre la tolleranza di errore, scalabilità e prestazioni di spazi di archiviazione diretta.

![Infrastruttura di archiviazione di Azure Stack](media/azure-stack-storage-infrastructure-overview/image4.png)

Esistono tre tipi di volumi creati nel pool di archiviazione di Azure Stack:

-   Infrastruttura: file di host usati da servizi di base e le macchine virtuali dell'infrastruttura di Azure Stack.

-   Temp di macchina virtuale: host i dischi temporanei collegati alle macchine virtuali tenant e che i dati vengono archiviati in tali dischi.

-   Oggetto Store: host i dati del tenant per la manutenzione di BLOB, tabelle, code e dischi delle macchine Virtuali.

In una distribuzione a più nodi, si vedrebbe tre volumi di infrastruttura, mentre il numero di volumi di macchina virtuale temporanea e oggetto Store è uguale al numero dei nodi nella distribuzione di Azure Stack:

-   In una distribuzione di quattro nodi, sono disponibili quattro volumi della macchina virtuale temporanea uguale e quattro i volumi di oggetti Store uguale.

-   Se si aggiunge un nuovo nodo al cluster, non vi sarà un nuovo volume per entrambi i tipi creata.

-   Il numero di volumi rimane la stessa anche se un nodo funziona correttamente o viene rimosso.

-   Se si utilizza il Kit per sviluppatori di Azure Stack, è presente un singolo volume con più condivisioni.

I volumi in spazi di archiviazione diretta forniscono la resilienza contro i problemi hardware, ad esempio errori di unità o server e per abilitare la disponibilità continua in tutta la manutenzione di server, ad esempio gli aggiornamenti software. Distribuzione di Azure Stack è con mirroring a tre vie per garantire resilienza dei dati. Tre copie dei dati dei tenant vengono scritti in server diversi, in cui vengono inserite nella cache:

![Infrastruttura di archiviazione di Azure Stack](media/azure-stack-storage-infrastructure-overview/image5.png)

Il mirroring fornisce tolleranza d'errore mantenendo più copie di tutti i dati. Come tali dati vengono archiviati con striping e inseriti è non semplice (vedere questo blog per saperne di più), ma è assolutamente true per quella di tutti i dati archiviati usando il mirroring viene scritto, nella sua interezza, più volte. Ogni copia viene scritta fisici diversi hardware (unità differenti in server diversi) che si presuppone di avere esito negativo in modo indipendente. Il mirroring a tre vie, in modo sicuro può tollerare almeno due problemi hardware (unità o server) alla volta. Ad esempio, se si esegue il riavvio di un server quando improvvisamente un'altra unità o server non riesce, tutti i dati rimangono sicuri e accessibili in modo continuo.

## <a name="volume-states"></a>Stati del volume

Per scoprire quali sono in stato volumess, usare i comandi di PowerShell seguenti:

\$scaleunit\_name = (Get-AzsScaleUnit)\[0\].name

\$subsystem\_name = (Get-AzsStorageSubSystem -ScaleUnit \$scaleunit\_name)\[0\].name

Get-AzsVolume - ScaleUnit \$scaleunit\_nome - StorageSubSystem \$sottosistema\_nome | Select-Object VolumeLabel HealthStatus, OperationalStatus, RepairStatus, descrizione, azione, TotalCapacityGB, RemainingCapacityGB

Ecco un esempio di output che mostra un volume danneggiato o incompleto e un volume scollegato:

| VolumeLabel | HealthStatus | OperationalStatus |
|-------------|--------------|------------------------|
| ObjStore_1 | Sconosciuto | Scollegato |
| ObjStore_2 | Avviso | {Con funzionalità ridotte, incompleta} |

Le sezioni seguenti elencano l'integrità e gli stati operativi.

### <a name="volume-health-state-healthy"></a>Stato di integrità volume: Healthy

| Stato operativo | DESCRIZIONE |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| OK | Il volume è integro. |
| Non ottimale | I dati non viene scritto in modo uniforme tra le unità.<br> <br>**Azione:** Contattare il supporto per ottimizzare l'uso del disco nel pool di archiviazione. Prima di iniziare, il processo di raccolta di file di log usando le indicazioni fornite dalla https://aka.ms/azurestacklogfiles. È possibile ripristinare da backup dopo la connessione non riuscita viene ripristinata. |


### <a name="volume-health-state-warning"></a>Stato di integrità volume: Avviso

Quando il volume è in uno stato di integrità di avviso, significa che non sono disponibili uno o più copie dei dati, ma Azure Stack può leggere almeno una copia dei dati.

| Stato operativo | DESCRIZIONE |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nel servizio | Azure Stack è ripristinare il volume, ad esempio dopo l'aggiunta o rimozione di un'unità. Una volta completato il ripristino, il volume deve restituire lo stato di integrità OK.<br> <br>**Azione:** Attendere che Azure Stack per completare il ripristino del volume e controllare lo stato in un secondo momento. |
| Incompleta | La resilienza del volume viene ridotto perché una o più unità non è riuscita o non sono presenti. Tuttavia, le unità mancante contengono copie aggiornate dei dati.<br> <br>**Azione:** Riconnettersi a qualsiasi unità mancante, sostituire tutte le unità non riuscite e portare online tutti i server che sono offline. |
| Degraded | La resilienza del volume viene ridotto perché una o più unità mancano o non è riuscita e non esistono copie obsolete dei dati su tali unità.<br> <br>**Azione:** Riconnettersi a qualsiasi unità mancante, sostituire tutte le unità non riuscite e portare online tutti i server che sono offline. |

 

### <a name="volume-health-state-unhealthy"></a>Stato di integrità volume: Non integro

Quando un volume è in uno stato di integrità integro, alcuni o tutti i dati nel volume è attualmente inaccessibili.

| Stato operativo | DESCRIZIONE |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Senza ridondanza | Il volume ha perso dati poiché troppe unità non è riuscita.<br> <br>**Azione:** Contattare il supporto. Prima di iniziare, il processo di raccolta di file di log usando le indicazioni fornite dalla https://aka.ms/azurestacklogfiles. |


### <a name="volume-health-state-unknown"></a>Stato di integrità volume: Sconosciuto

Il volume può essere anche nello stato di integrità sconosciuto se il disco virtuale è diventano scollegato.

| Stato operativo | DESCRIZIONE |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Scollegato | Si è verificato un errore di dispositivo di archiviazione, che potrebbe causare il volume sia accessibile. Alcuni dati potrebbero essere persi.<br> <br>**Azione:** <br>1. Controllare la fisica e la connettività di tutti i dispositivi di archiviazione di rete.<br>2. Se tutti i dispositivi sono connessi in modo corretto, contattare il supporto. Prima di iniziare, il processo di raccolta di file di log usando le indicazioni fornite dalla https://aka.ms/azurestacklogfiles. È possibile ripristinare da backup dopo la connessione non riuscita viene ripristinata. |

## <a name="drive-states"></a>Stati dell'unità

Usare i comandi PowerShell seguenti per monitorare lo stato delle unità:

 

\$scaleunit\_name = (Get-AzsScaleUnit)\[0\].name

\$subsystem\_name = (Get-AzsStorageSubSystem -ScaleUnit \$scaleunit\_name)\[0\].name

, SerialNumber

 

Get-AzsDrive - ScaleUnit \$scaleunit\_nome - StorageSubSystem \$sottosistema\_nome | StorageNode Select-Object, PhysicalLocation, HealthStatus, OperationalStatus, descrizione, azione, utilizzo, CanPool, CannotPoolReason, numero di serie, modello, MediaType, CapacityGB

Le sezioni seguenti descrivono gli stati di integrità in cui può essere un'unità.

### <a name="drive-health-state-healthy"></a>Stato di integrità unità: Healthy

| Stato operativo | DESCRIZIONE |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| OK | Il volume è integro. |
| Nel servizio | L'unità sta eseguendo alcune operazioni di manutenzione interna. Una volta completata l'azione, l'unità deve restituire lo stato di integrità OK. |

### <a name="drive-health-state-healthy"></a>Stato di integrità unità: Healthy

Un'unità, è possibile stato avviso leggere e scrivere i dati correttamente ma si è verificato un problema.

| Stato operativo | DESCRIZIONE |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Comunicazione perduta | Connettività persa nell'unità.<br> <br>**Azione:** Portare online tutti i server. Se non viene risolto, riconnettere l'unità. Se si continua a verificarsi, sostituire il disco per garantire la resilienza completa. |
| Errore prevedibile | Un errore dell'unità, secondo la stima a breve si verificano.<br> <br>**Azione:** Sostituire il disco appena possibile per garantire la resilienza completa. |
| Errore dei / o | Si è verificato un errore temporaneo durante l'accesso dell'unità.<br> <br>**Azione:** Se si continua a verificarsi, sostituire il disco per garantire la resilienza completa. |
| Errore temporaneo | Si è verificato un errore temporaneo con l'unità. Ciò significa in genere l'unità stato non risponda, ma può anche indicare che spazi di archiviazione diretta protettivo partizione in modo non appropriato è stato rimosso dall'unità. <br> <br>**Azione:** Se si continua a verificarsi, sostituire il disco per garantire la resilienza completa. |
| Latenza anomala | L'unità a volte non risponde e verrà visualizzati segni di errore.<br> <br>**Azione:** Se si continua a verificarsi, sostituire il disco per garantire la resilienza completa. |
| Rimozione dal pool | Azure Stack è in corso la rimozione dal relativo pool di archiviazione.<br> <br>**Azione:** Attendere che Azure Stack per completare la rimozione dell'unità e controllare lo stato in un secondo momento.<br>Se lo stato persiste, contattare il supporto. Prima di iniziare, il processo di raccolta di file di log usando le indicazioni fornite dalla https://aka.ms/azurestacklogfiles. |
|  |  |
| Inizio modalità manutenzione | Azure Stack è in corso di inserire l'unità in modalità di manutenzione. Questo è un stato temporaneo: l'unità a breve deve essere nello stato di modalità di manutenzione In.<br> <br>**Azione:** Attendere per Azure Stack completare il processo e controllare lo stato in un secondo momento. |
| In modalità di manutenzione | L'unità è in modalità di manutenzione, l'arresto di operazioni di lettura e scrittura dall'unità. Ciò significa in genere le attività di amministrazione di Azure Stack, ad esempio PNU o FRU funziona l'unità. Ma l'amministratore può inoltre inserire l'unità in modalità di manutenzione.<br> <br>**Azione:** Attendere per Azure Stack completare l'attività di amministrazione e verificare lo stato in un secondo momento.<br>Se lo stato persiste, contattare il supporto. Prima di iniziare, il processo di raccolta di file di log usando le indicazioni fornite dalla https://aka.ms/azurestacklogfiles. |
|  |  |
| Arrestare la modalità di manutenzione | Azure Stack è in corso di riportare online l'unità. Questo è un stato temporaneo: l'unità deve presto disponibile in un altro stato - integro in una situazione ideale.<br> <br>**Azione:** Attendere per Azure Stack completare il processo e controllare lo stato in un secondo momento. |

 

### <a name="drive-health-state-unhealthy"></a>Stato di integrità unità: Non integro

Un'unità in stato non integro non può attualmente essere scritto o si accede.

| Stato operativo | DESCRIZIONE |
|-------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Split | L'unità è diventano separato dal pool.<br> <br>**Azione:** Sostituire l'unità con un nuovo disco. Se è necessario usare il disco, rimuovere il disco del sistema, assicurarsi che non sono presenti dati utili sul disco, cancellare il disco e quindi ricollegare il disco. |
| Non è utilizzabile | Il disco fisico viene messo in quarantena perché non è supportata per il fornitore della soluzione. Sono supportati solo i dischi che sono approvati per la soluzione e hanno il firmware del disco corretto.<br> <br>**Azione:** Sostituire l'unità con un disco che dispone di un produttore approvati e numero di modello per la soluzione. |
| Metadati obsoleti | Il disco sostitutivo utilizzato in precedenza e può contenere i dati da un sistema di archiviazione sconosciuto. Il disco viene messo in quarantena.        <br> <br>**Azione:** Sostituire l'unità con un nuovo disco. Se è necessario usare il disco, rimuovere il disco del sistema, assicurarsi che non sono presenti dati utili sul disco, cancellare il disco e quindi ricollegare il disco. |
| Metadati non riconosciuti | Metadati non riconosciuti trovati nell'unità, questo in genere significa che l'unità sia i metadati da un pool diverso su di esso.<br> <br>**Azione:** Sostituire l'unità con un nuovo disco. Se è necessario usare il disco, rimuovere il disco del sistema, assicurarsi che non sono presenti dati utili sul disco, cancellare il disco e quindi ricollegare il disco. |
| Supporto con errori | L'unità non è riuscita e non sarà più essere usato da spazi di archiviazione.<br> <br>**Azione:** Sostituire il disco appena possibile per garantire la resilienza completa. |
| Errore hardware del dispositivo | Si è verificato un errore hardware in questa unità. <br> <br>**Azione:** Sostituire il disco appena possibile per garantire la resilienza completa. |
| Aggiornamento del firmware | Azure Stack è l'aggiornamento del firmware nell'unità. Questo è un stato temporaneo che in genere dura meno di un minuto e durante il quale ora che altre unità nel pool di gestire tutte le letture e scritture.<br> <br>**Azione:** Attendere per Azure Stack completare l'aggiornamento e controllare lo stato in un secondo momento. |
| Avvio in corso | L'unità si sta preparando per l'operazione. Deve trattarsi di un stato temporaneo - al termine dell'operazione, che l'unità deve eseguire la transizione a uno stato operativo diverso.<br> <br>**Azione:** Attendere per Azure Stack completare l'operazione e verificare lo stato in un secondo momento. |
 

## <a name="reasons-a-drive-cant-be-pooled"></a>Motivi di che un'unità non è possibile raggruppare in pool

Alcune unità non appena sono pronte essere in pool di archiviazione di Azure Stack. È possibile scoprire il motivo per cui un'unità non è idonea per il pool, esaminando la proprietà CannotPoolReason di un'unità. Nella tabella seguente fornisce altri dettagli su ciascuno dei motivi.

| Motivo | DESCRIZIONE |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Hardware non conforme | L'unità non è nell'elenco dei modelli di archiviazione approvati specificati usando il servizio integrità.<br> <br>**Azione:** Sostituire l'unità con un nuovo disco. |
| Firmware non conforme | Il firmware del disco fisico non è nell'elenco delle revisioni del firmware approvata tramite il servizio integrità.<br> <br>**Azione:** Sostituire l'unità con un nuovo disco. |
| In uso dal cluster | L'unità viene attualmente usata da un Cluster di Failover.<br> <br>**Azione:** Sostituire l'unità con un nuovo disco. |
| Supporti rimovibili | L'unità viene classificato come un'unità rimovibile. <br> <br>**Azione:** Sostituire l'unità con un nuovo disco. |
| Non integri | L'unità non è in uno stato integro e potrebbe essere necessario sostituire.<br> <br>**Azione:** Sostituire l'unità con un nuovo disco. |
| Capacità insufficiente | Sono presenti partizioni occupare lo spazio disponibile sull'unità.<br> <br>**Azione:** Sostituire l'unità con un nuovo disco. Se è necessario usare il disco, rimuovere il disco del sistema, assicurarsi che non sono presenti dati utili sul disco, cancellare il disco e quindi ricollegare il disco. |
| Verifica in corso. | Il servizio integrità controlla per verificare se l'unità o firmware l'unità è approvato per l'uso.<br> <br>**Azione:** Attendere per Azure Stack completare il processo e controllare lo stato in un secondo momento. |
| Verifica non riuscita | Il servizio integrità non è stato possibile verificare se l'unità o firmware l'unità è approvato per l'uso.<br> <br>**Azione:** Contattare il supporto. Prima di iniziare, il processo di raccolta di file di log usando le indicazioni fornite dalla https://aka.ms/azurestacklogfiles. |
| Offline | L'unità è offline. <br> <br>**Azione:** Contattare il supporto. Prima di iniziare, il processo di raccolta di file di log usando le indicazioni fornite dalla https://aka.ms/azurestacklogfiles. |

## <a name="next-step"></a>Passaggio successivo

[Gestire la capacità di archiviazione](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-storage-shares) 