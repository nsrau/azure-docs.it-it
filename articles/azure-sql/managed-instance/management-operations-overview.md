---
title: Panoramica delle operazioni di gestione
titleSuffix: Azure SQL Managed Instance
description: Informazioni sulla durata e sulle procedure consigliate per le operazioni di gestione di Istanza gestita di SQL di Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: overview
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, MashaMSFT
ms.date: 07/10/2020
ms.openlocfilehash: 2da7311e61aa39be69a6a0a29eff686baaad7ebf
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91323193"
---
# <a name="overview-of-azure-sql-managed-instance-management-operations"></a>Panoramica delle operazioni di gestione di Istanza gestita di SQL di Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Istanza gestita di SQL di Azure include operazioni di gestione che è possibile usare per distribuire automaticamente nuove istanze gestite, aggiornare le proprietà delle istanze ed eliminare le istanze quando non sono più necessarie.

## <a name="what-are-management-operations"></a>Che cosa sono le operazioni di gestione?

Tutte le operazioni di gestione possono essere suddivise per categoria, come indicato di seguito:

- Distribuzione di un'istanza (creazione di una nuova istanza)
- Aggiornamento di un'istanza (modifica delle proprietà di un'istanza, ad esempio il numero di vCore o l'archiviazione riservata)
- Eliminazione di un'istanza

Per supportare[le distribuzioni all'interno delle reti virtuali di Azure ](../../virtual-network/virtual-network-for-azure-services.md)e garantire l'isolamento e la sicurezza per i clienti, Istanza gestita di SQL si basa su [cluster virtuali](connectivity-architecture-overview.md#high-level-connectivity-architecture). Il cluster virtuale rappresenta un set dedicato di macchine virtuali isolate distribuite all'interno di una subnet della rete virtuale del cliente. In pratica, ogni istanza gestita distribuita in una subnet vuota genera l'implementazione di un nuovo cluster virtuale.

Le successive operazioni di gestione sulle istanze gestite possono avere effetto sul cluster virtuale sottostante. Le modifiche che hanno effetto sul cluster virtuale sottostante possono influire sulla durata delle operazioni di gestione, perché la distribuzione di macchine virtuali aggiuntive comporta un sovraccarico che è necessario considerare quando si pianificano nuove distribuzioni o aggiornamenti delle istanze gestite esistenti.


## <a name="duration"></a>Duration

La durata delle operazioni nel cluster virtuale può variare, ma in genere è molto lunga. 

Di seguito sono riportati i valori tipici che è possibile prevedere, in base ai dati di telemetria del servizio esistenti:

- **Creazione di un cluster virtuale**:  la creazione è un passaggio sincrono nelle operazioni di gestione delle istanze. <br/> **Il 90% delle operazioni si completa in 4 ore**.
- **Ridimensionamento del cluster virtuale (espansione o riduzione)** : L'espansione è un passaggio sincrono, mentre la compattazione viene eseguita in modo asincrono (senza alcun effetto sulla durata delle operazioni di gestione dell'istanza). <br/>**Il 90% delle espansioni del cluster si completa in meno di 2,5 ore**.
- **Eliminazione del cluster virtuale**: L'eliminazione è un passaggio asincrono, ma può anche essere [avviata manualmente](virtual-cluster-delete.md) in un cluster virtuale vuoto. In tal caso, si tratta ti un'operazione sincrona. <br/>**Il 90% delle eliminazioni di cluster virtuali si completa in 1,5 ore**.

Inoltre, la gestione delle istanze può includere anche una delle operazioni sui database ospitati, il che comporta durate più lunghe:

- **Collegamento dei file di database da Archiviazione di Azure**:  si tratta di un passaggio sincrono, ad esempio il dimensionamento delle risorse di calcolo (vCore) oppure l'aumento o la riduzione delle risorse di archiviazione nel livello di servizio per utilizzo generico. <br/>**Il 90% di queste operazioni si completa in 5 minuti**.
- **Seeding dei gruppi di disponibilità AlwaysOn**: si tratta di un passaggio sincrono, ad esempio il dimensionamento delle risorse di calcolo (vCore) o di archiviazione nel livello di servizio business critical e il passaggio dal livello di servizio per utilizzo generico a quello business critical o viceversa. La durata dell'operazione è proporzionale alle dimensioni totali del database e all'attività del database corrente (numero di transazioni attive). L'attività del database durante l'aggiornamento di un'istanza può introdurre una varianza significativa per la durata totale. <br/>**Il 90% di queste operazioni viene eseguito a 220 GB/ora o superiore**.

Le tabelle seguenti riepilogano le operazioni e le durate complessive tipiche, in base alla categoria:

**Categoria: Distribuzione**

|Operazione  |Segmento a esecuzione prolungata  |Durata stimata  |
|---------|---------|---------|
|Prima istanza in una subnet vuota|Creazione del cluster virtuale|Il 90% delle operazioni viene completato in 4 ore.|
|Prima istanza di un'altra generazione di hardware in una subnet non vuota (ad esempio, la prima istanza di generazione 5 in una subnet con istanze di generazione 4)|Creazione di un cluster virtuale<sup>1</sup>|Il 90% delle operazioni viene completato in 4 ore.|
|Creazione dell'istanza successiva all'interno della subnet non vuota (seconda, terza istanza e così via)|Ridimensionamento del cluster virtuale|Il 90% delle operazioni viene completato in 2,5 ore.|
| | | 

<sup>1</sup> Il cluster virtuale viene creato in base alla generazione hardware.

**Categoria: Aggiornamento**

|Operazione  |Segmento a esecuzione prolungata  |Durata stimata  |
|---------|---------|---------|
|Modifica delle proprietà dell'istanza (password amministratore, accesso di Azure AD, flag di Vantaggio Azure Hybrid)|N/D|Fino a 1 minuto.|
|Aumento/Riduzione dell'archiviazione delle istanze (livello di servizio per utilizzo generico)|Collegamento di file di database|Il 90% delle operazioni viene completato in 5 minuti.|
|Aumento/Riduzione dell'archiviazione delle istanze (livello di servizio business critical)|- Ridimensionamento del cluster virtuale<br>- Seeding dei gruppi di disponibilità AlwaysOn|Il 90% delle operazioni viene completato in 2,5 ore + il tempo per il seeding di tutti i database (220 GB/ora).|
|Aumento e riduzione del calcolo (vCore) delle istanze (utilizzo generico)|- Ridimensionamento del cluster virtuale<br>- Collegamento di file di database|Il 90% delle operazioni viene completato in 2,5 ore.|
|Aumento e riduzione del calcolo (vCore) delle istanze (business critical)|- Ridimensionamento del cluster virtuale<br>- Seeding dei gruppi di disponibilità AlwaysOn|Il 90% delle operazioni viene completato in 2,5 ore + il tempo per il seeding di tutti i database (220 GB/ora).|
|Modifica del livello di servizio dell'istanza (da utilizzo generico a business critical e viceversa)|- Ridimensionamento del cluster virtuale<br>- Seeding dei gruppi di disponibilità AlwaysOn|Il 90% delle operazioni viene completato in 2,5 ore + il tempo per il seeding di tutti i database (220 GB/ora).|
| | | 

**Categoria: Eliminazione**

|Operazione  |Segmento a esecuzione prolungata  |Durata stimata  |
|---------|---------|---------|
|Eliminazione di un'istanza|Backup della parte finale del log per tutti i database|90% delle operazioni entro 1 minuto.<br>Nota: se viene eliminata l'ultima istanza della subnet, questa operazione pianifica l'eliminazione del cluster virtuale dopo 12 ore.<sup>1</sup>|
|Eliminazione di un cluster virtuale (operazione avviata dall'utente)|Eliminazione del cluster virtuale|Il 90% delle operazioni viene completato entro 1,5 ore.|
| | | 

<sup>1</sup>12 ore è la configurazione corrente, ma è soggetta a modifiche in futuro. Se è necessario eliminare un cluster virtuale in anticipo (per rilasciare la subnet, ad esempio), vedere [Eliminare una subnet dopo l'eliminazione di un'istanza gestita](virtual-cluster-delete.md).

## <a name="instance-availability"></a>Disponibilità dell'istanza

Istanza gestita di SQL **rimane disponibile durante le operazioni di aggiornamento**, ad eccezione di un breve tempo di inattività causato dal failover che si verifica alla fine dell'aggiornamento. In genere, si tratta di un massimo di 10 secondi anche nel caso di transazioni a esecuzione prolungata interrotte, grazie al [recupero del database accelerato](../accelerated-database-recovery.md).

Istanza gestita di SQL non è disponibile per le applicazioni client durante le operazioni di distribuzione ed eliminazione.

> [!IMPORTANT]
> Non è consigliabile dimensionare le risorse di calcolo o di archiviazione di Istanza gestita di SQL di Azure o cambiare il livello di servizio contemporaneamente alle transazioni a esecuzione prolungata (importazione di dati, processi di elaborazione dei dati, ricompilazione dell'indice e così via). Il failover del database alla fine dell'operazione annulla tutte le transazioni in corso. 

## <a name="management-operations-steps"></a>Passaggi delle operazioni di gestione

Le operazioni di gestione sono costituite da più passaggi. Con l'[API per le operazioni introdotta](management-operations-monitor.md), questi passaggi sono esposti per un sottoinsieme di operazioni (distribuzione e aggiornamento). L'operazione di distribuzione è costituita da tre passaggi, mentre l'operazione di aggiornamento viene eseguita in sei passaggi. Per informazioni dettagliate sulla durata delle operazioni, vedere la sezione [Durata delle operazioni di gestione](#duration). I passaggi sono elencati in base all'ordine di esecuzione.

### <a name="managed-instance-deployment-steps"></a>Passaggi per la distribuzione di istanze gestite

|Nome passaggio  |Descrizione passaggio  |
|----|---------|
|Convalida delle richieste |I parametri inviati vengono convalidati. In caso di errore di configurazione, l'operazione avrà esito negativo con un errore. |
|Ridimensionamento/creazione del cluster virtuale |A seconda dello stato della subnet, il cluster virtuale viene creato o ridimensionato. |
|Avvio di una nuova istanza di SQL |Il processo SQL viene avviato nel cluster virtuale distribuito. |

### <a name="managed-instance-update-steps"></a>Passaggi per l'aggiornamento di istanze gestite

|Nome passaggio  |Descrizione passaggio  |
|----|---------|
|Convalida delle richieste | I parametri inviati vengono convalidati. In caso di errore di configurazione, l'operazione avrà esito negativo con un errore. |
|Ridimensionamento/creazione del cluster virtuale |A seconda dello stato della subnet, il cluster virtuale viene creato o ridimensionato. |
|Avvio di una nuova istanza di SQL | Il processo SQL viene avviato nel cluster virtuale distribuito. |
|Seeding/collegamento dei file di database |A seconda del tipo di operazione di aggiornamento, viene eseguito il seeding o il collegamento dei file di database. |
|Preparazione ed esecuzione del failover |Dopo il seeding dei dati e una volta ricollegati i file di database, il sistema viene preparato per il failover. Quando vengono configurate tutte le impostazioni, il failover viene eseguito **con un breve tempo di inattività**. |
|Pulizia delle istanze di SQL |Rimozione del vecchio processo SQL dal cluster virtuale |

> [!NOTE]
> Come conseguenza del dimensionamento delle istanze, nel cluster virtuale sottostante viene eseguito il processo di rilascio della capacità inutilizzata e di possibile deframmentazione della capacità, il che potrebbe influire sulle istanze non coinvolte nelle operazioni di creazione/dimensionamento. 


## <a name="management-operations-cross-impact"></a>Effetti incrociati sulle operazioni di gestione

Le operazioni di gestione eseguite in un'istanza gestita possono influire su altre operazioni di gestione delle istanze situate all'interno dello stesso cluster virtuale:

- Le **operazioni di ripristino a esecuzione prolungata** in un cluster virtuale metteranno in attesa le altre operazioni di creazione o dimensionamento di istanze nella stessa subnet.<br/>**Esempio:** se è in corso un'operazione di ripristino a esecuzione prolungata e nella stessa subnet viene effettuata una richiesta di creazione o dimensionamento, l'operazione impiegherà più tempo perché bisognerà aspettare che venga completata l'operazione di ripristino prima di continuare.

- Una **successiva operazione di creazione o dimensionamento di istanze** viene messa in attesa da un'operazione di creazione o dimensionamento di istanze avviata in precedenza che ha comportato il ridimensionamento del cluster virtuale.<br/>**Esempio:** se sono presenti più richieste di creazione e/o dimensionamento nella stessa subnet all'interno dello stesso cluster virtuale e una di esse avvia il ridimensionamento del cluster virtuale, tutte le operazioni richieste 5 minuti dopo quella iniziale impiegheranno più tempo del previsto, perché bisognerà aspettare che venga completato il ridimensionamento prima che vengano riprese.

- **Le operazioni di creazione/dimensionamento inviate entro l'intervallo di 5 minuti** verranno organizzate in batch ed eseguite in parallelo.<br/>**Esempio:** verrà eseguito un solo ridimensionamento del cluster virtuale per tutte le operazioni inviate entro un intervallo di 5 minuti (a partire dal momento dell'esecuzione della prima richiesta di operazione). L'esecuzione di un'eventuale altra operazione richiesta più di 5 minuti dopo la prima verrà avviata solo una volta completato il ridimensionamento del cluster virtuale.

> [!IMPORTANT]
> Le operazioni di gestione messe in attesa a causa di un'altra operazione in corso verranno riprese automaticamente al verificarsi delle condizioni adatte. Non è necessaria alcuna azione da parte dell'utente per riprendere le operazioni di gestione temporaneamente sospese.

## <a name="monitoring-management-operations"></a>Monitoraggio delle operazioni di gestione

Per informazioni su come monitorare l'avanzamento e lo stato delle operazioni di gestione, vedere [Monitoraggio delle operazioni di gestione](management-operations-monitor.md).

## <a name="canceling-management-operations"></a>Annullamento delle operazioni di gestione

Per informazioni su come annullare un'operazione di gestione, vedere [Annullamento delle operazioni di gestione](management-operations-cancel.md).


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come creare la prima istanza gestita, vedere [Guida di avvio rapido](instance-create-quickstart.md).
- Per un elenco di funzionalità e di confronto, vedere [Funzionalità comuni di SQL](../database/features-comparison.md).
- Per altre informazioni sulla configurazione della rete virtuale, vedere [Configurazione di una rete virtuale per Istanza gestita di SQL](connectivity-architecture-overview.md).
- Per una guida di avvio rapido sulla creazione di un'istanza gestita e il ripristino di un database da un file di backup, vedere [Creare un'istanza gestita](instance-create-quickstart.md).
- Per un'esercitazione sull'uso del Servizio Migrazione del database di Azure, vedere [Migrazione a Istanza gestita di SQL con il Servizio Migrazione del database](../../dms/tutorial-sql-server-to-managed-instance.md).
