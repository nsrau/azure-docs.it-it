<properties
	pageTitle="Linee guida per le soluzioni di archiviazione | Microsoft Azure"
	description="Informazioni sulle principali linee guida di progettazione e implementazione per la distribuzione di soluzioni di archiviazione nei servizi di infrastruttura di Azure."
	documentationCenter=""
	services="virtual-machines-windows"
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/08/2016"
	ms.author="iainfou"/>  

# Linee guida sull'infrastruttura di archiviazione

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

Questo articolo è incentrato sulla comprensione delle esigenze di archiviazione e sulle considerazioni di progettazione per il raggiungimento di livelli di prestazioni ottimali delle macchine virtuali (VM).


## Linee guida di implementazione per l'archiviazione

Decisioni:

- È necessario usare l'archiviazione standard o Premium per il carico di lavoro?
- È necessario lo striping del disco per creare dischi più grandi di 1023 TB?
- È necessario lo striping del disco per ottenere prestazioni di I/O ottimali per il carico di lavoro?
- Quali sono i set di account di archiviazione necessari per l’hosting dell’infrastruttura o del carico di lavoro IT?

Attività:

- Esaminare le richieste di I/O delle applicazioni da distribuire e pianificare il numero appropriato e il tipo di account di archiviazione.
- Creare il set di account di archiviazione usando la convenzione di denominazione scelta. È possibile usare Azure PowerShell o il portale.


## Archiviazione

Archiviazione di Azure è una parte fondamentale della distribuzione e della gestione di applicazioni e macchine virtuali. Archiviazione di Azure fornisce servizi per l'archiviazione di dati dei file, dati non strutturati e messaggi ed è anche parte dell'infrastruttura di supporto delle macchine virtuali.

Per il supporto delle macchine virtuali sono disponibili due tipi di archiviazione:

- Un account di archiviazione standard consente di accedere all'archivio BLOB (usato per archiviare i dischi delle macchine virtuali di Azure), all'archivio tabelle, all'archivio code e all'archivio file.
- Gli account di [archiviazione Premium](../storage/storage-premium-storage.md) offrono prestazioni elevate e supporto per dischi a bassa latenza per carichi di lavoro con uso intensivo di I/O, ad esempio istanze di SQL Server in un cluster AlwaysOn. Attualmente l'archiviazione Premium di Azure supporta solo i dischi di VM di Azure.

Azure consente di creare macchine virtuali con un disco del sistema operativo, un disco temporaneo e nessuno o più dischi dati facoltativi. Il disco del sistema operativo e i dischi dati sono BLOB di Azure, mentre il disco temporaneo è archiviato localmente sul nodo in cui si trova il computer. Prestare attenzione quando si progettano applicazioni in modo da usare solo questo disco temporaneo per i dati non persistenti, in quanto la macchina virtuale può eseguire la migrazione tra host durante un evento di manutenzione. Tutti i dati archiviati sul disco temporaneo andranno persi.

Per garantire che i dati rimangano protetti da eventi di manutenzione non pianificata o errori hardware, l'ambiente di Archiviazione di Azure sottostante offre durabilità ed elevata disponibilità. Quando si progetta l'ambiente di Archiviazione di Azure, è possibile scegliere di replicare l'archiviazione VM:

- in locale all'interno di un data center di Azure
- tra data center di Azure all'interno di una determinata area
- tra data center di Azure all'interno di aree diverse

Sono disponibili altre informazioni sulle [opzioni di replica per la disponibilità elevata](../storage/storage-introduction.md#replication-for-durability-and-high-availability).

I dischi dati e del sistema operativo hanno una dimensione massima di 1.023 gigabyte (GB). La dimensione massima di un BLOB è di 1.024 GB, compresi i metadati (piè di pagina) del file VHD (un GB corrisponde a 1.024<sup>3</sup> byte). È possibile usare gli spazi di archiviazione in Windows Server 2012 per superare questo limite tramite il pooling dei dischi dati per la presentazione di volumi logici superiori a 1023 GB per la VM.

Quando si progettano le distribuzioni di Archiviazione di Azure, esistono alcuni limiti di scalabilità; a questo proposito, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](azure-subscription-service-limits.md#storage-limits) per altri dettagli. Vedere anche [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../storage/storage-scalability-targets.md).

Quanto all'archiviazione delle applicazioni, è possibile archiviare dati oggetto non strutturati, ad esempio documenti, immagini, backup, dati di configurazione, log e così via tramite l'archiviazione BLOB. Anziché la scrittura in un disco virtuale collegato alla macchina virtuale, l'applicazione può scrivere direttamente nell'archiviazione BLOB di Azure. L'archiviazione BLOB offre anche la possibilità di [livelli di archiviazione per accesso frequente e accesso sporadico](../storage/storage-blob-storage-tiers.md) in base alle esigenze di disponibilità e ai vincoli di costo.


## Dischi con striping
Oltre a consentire di creare dischi di dimensioni superiori a 1023 GB, in molti casi l'uso dello striping per i dischi dati contribuisce a migliorare le prestazioni, consentendo a più BLOB di supportare l'archiviazione per un singolo volume. Con lo striping, le operazioni I/O necessarie per scrivere e leggere dati da un singolo disco logico procedono in parallelo.

Azure impone limiti riguardo al numero di dischi dati e alla quantità della larghezza di banda disponibile a seconda delle dimensioni della macchina virtuale. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali](virtual-machines-windows-sizes.md).

Se si usa lo striping del disco per i dischi dati di Azure, considerare le linee guida seguenti:

- I dischi dati devono essere sempre della massima dimensione (1023 GB).
- Collegare i dischi dati delle dimensioni massime consentite per le dimensioni della macchina virtuale.
- Usare gli spazi di archiviazione.
- Evitare di usare le opzioni di caching del disco di dati di Azure (criterio di caching = Nessuno).

Per altre informazioni, vedere l’articolo sugli [spazi di archiviazione - progettazione della prestazione](http://social.technet.microsoft.com/wiki/contents/articles/15200.storage-spaces-designing-for-performance.aspx).


## Account di archiviazione multipli

Quando si progetta l'ambiente di Archiviazione di Azure, è possibile usare più account di archiviazione con l'aumentare del numero di macchine virtuali distribuite. Ciò consente di distribuire i carichi di lavoro I/O nell'infrastruttura di Archiviazione di Azure sottostante al fine di garantire un livello di prestazioni ottimale per le macchine virtuali e le applicazioni. Durante la progettazione delle applicazioni da distribuire, considerare i requisiti di I/O di ogni VM e bilanciare queste ultime fra gli account di Archiviazione di Azure. Cercare di evitare di raggruppare tutte le VM con valori di I/O elevati in uno o due account di archiviazione soltanto.

Per altre informazioni sulle funzionalità di I/O delle diverse opzioni di Archiviazione di Azure e sui valori massimi consigliati, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../storage/storage-scalability-targets.md).


## Passaggi successivi

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]

<!---HONumber=AcomDC_0914_2016-->