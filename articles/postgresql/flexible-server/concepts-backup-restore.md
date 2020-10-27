---
title: Backup e ripristino nel database di Azure per PostgreSQL-server flessibile
description: Informazioni sui concetti di backup e ripristino con database di Azure per PostgreSQL-server flessibile
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: d0e79e42c7c004638336ada23de663bbe74b7e48
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92532646"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---flexible-server"></a>Backup e ripristino nel database di Azure per PostgreSQL-server flessibile

> [!IMPORTANT]
> Il server flessibile di Database di Azure per PostgreSQL è disponibile in anteprima

I backup costituiscono una parte essenziale di qualsiasi strategia di continuità aziendale. Consentono di proteggere i dati dal danneggiamento o dall'eliminazione accidentale. Database di Azure per PostgreSQL: il server flessibile esegue automaticamente il backup del server e mantiene i backup per la durata di un massimo di 35 giorni. Durante il ripristino, è possibile specificare la data e l'ora in cui si desidera eseguire il ripristino entro il periodo di memorizzazione. Il tempo complessivo per il ripristino e il recupero dipende dalle dimensioni dei file di database e dalla quantità di ripristino da eseguire. 

### <a name="backup-process-in-flexible-server"></a>Processo di backup in un server flessibile
Il primo backup degli snapshot viene pianificato subito dopo la creazione del server flessibile. Successivamente, viene eseguito un backup giornaliero degli snapshot dei file di dati. I backup vengono archiviati in una risorsa di archiviazione con ridondanza della zona all'interno di un'area. Anche i log delle transazioni (write ahead logs-WAL) vengono archiviati in modo continuo, in modo che sia possibile eseguire il ripristino fino all'ultima transazione di cui è stato eseguito il commit. Questi backup dei dati e dei log consentono di ripristinare un server a qualsiasi momento specifico all'interno del periodo di conservazione dei backup configurato. Tutti i backup vengono crittografati con crittografia AES a 256 bit.

Se il database è configurato con disponibilità elevata, gli snapshot giornalieri vengono eseguiti dal database primario e i backup del log continui vengono eseguiti dalla modalità standby.

> [!IMPORTANT]
>I backup non vengono eseguiti su server interrotti. Tuttavia, i backup vengono ripresi quando il database viene avviato automaticamente dopo 7 giorni o avviato dall'utente.

I backup possono essere utilizzati solo per le operazioni di ripristino all'interno del server flessibile. Se si desidera esportare o importare dati nel server flessibile, utilizzare la metodologia di [dump e ripristino](../howto-migrate-using-dump-and-restore.md) .


### <a name="backup-retention"></a>Conservazione dei backup

I backup vengono conservati in base all'impostazione del periodo di memorizzazione dei backup per il server. È possibile selezionare un periodo di conservazione compreso tra 7 e 35 giorni. Il periodo di conservazione predefinito è di sette giorni. È possibile impostare il periodo di conservazione durante la creazione del server oppure è possibile aggiornarlo in un secondo momento. I backup vengono conservati anche per i server arrestati.

Il periodo di conservazione dei backup determina la distanza del tempo di recupero di un ripristino temporizzato, poiché si \' basa su backup disponibili. Il periodo di conservazione dei backup può essere trattato anche come una finestra di ripristino da una prospettiva di ripristino. Tutti i backup necessari per eseguire un ripristino temporizzato entro il periodo di conservazione dei backup vengono conservati nell'archivio di backup. Ad esempio, se il periodo di conservazione dei backup è impostato su sette giorni, la finestra di ripristino viene considerata come gli ultimi sette giorni. In questo scenario vengono conservati tutti i dati e i log necessari per il ripristino e il ripristino del server negli ultimi sette giorni. 


### <a name="backup-storage-cost"></a>Costo dell'archiviazione dei backup

Il server flessibile offre fino al 100% dell'archiviazione del server di cui è stato effettuato il provisioning come archivio di backup senza costi aggiuntivi. Ogni ulteriore spazio di archiviazione di backup utilizzato viene addebitato in GB al mese. Se, ad esempio, è stato effettuato il provisioning di un server con 250 GiB di spazio di archiviazione, si avrà 250 GiB della capacità di archiviazione di backup senza costi aggiuntivi. Se l'utilizzo del backup giornaliero è 25 GiB, è possibile disporre di un massimo di 10 giorni di archiviazione di backup gratuita. Il consumo di archiviazione di backup che supera 250 GiB viene addebitato in base al [modello di determinazione prezzi](https://azure.microsoft.com/pricing/details/postgresql/).

È possibile usare la metrica di [archiviazione di backup usata](../concepts-monitoring.md) nel portale di Azure per monitorare l'archivio di backup utilizzato da un server. La metrica di archiviazione di backup usata rappresenta la somma dello spazio di archiviazione utilizzato da tutti i backup del database e dei backup del log mantenuti in base al periodo di conservazione dei backup impostato per il server.  Un'intensa attività transazionale sul server può causare un aumento dell'uso dell'archivio di backup indipendentemente dalle dimensioni totali del database.

Il modo principale per controllare i costi di archiviazione dei backup consiste nell'impostare il periodo di conservazione dei backup appropriato e scegliere le opzioni di ridondanza di backup corrette per soddisfare gli obiettivi di ripristino desiderati.

> [!IMPORTANT]
> I backup con ridondanza geografica non sono attualmente supportati con il server flessibile.

## <a name="point-in-time-restore-overview"></a>Panoramica sul ripristino temporizzato

In server flessibili, l'esecuzione di un ripristino temporizzato consente di creare un nuovo server dai backup flessibili del server \' nella stessa area del server di origine. Viene creata con la configurazione del server di origine per il piano tariffario, la generazione di calcolo, il numero di Vcore, le dimensioni di archiviazione, il periodo di conservazione dei backup e l'opzione di ridondanza dei backup. I tag e le impostazioni, come ad esempio le impostazioni della rete virtuale e del firewall, vengono anche ereditati dal server di origine. 

 > [!IMPORTANT]
> Se si ripristina un server flessibile configurato con disponibilità elevata con ridondanza della zona, il server ripristinato verrà configurato senza disponibilità elevata e nella stessa area del server primario. 

 ### <a name="restore-process"></a>Processo di ripristino

I file di database fisici vengono innanzitutto ripristinati dai backup di snapshot al percorso dei dati del server. Il backup appropriato che è stato effettuato prima del momento desiderato viene scelto e ripristinato automaticamente. Viene quindi avviato un processo di ripristino usando i file WAL per portare il database in uno stato coerente. 

 Si supponga, ad esempio, che i backup vengano eseguiti alle 23.00 ogni notte. Se il punto di ripristino è per il 15 agosto 2020 alle 10:00 AM, viene ripristinato il backup giornaliero del 14 agosto 2020. Il database verrà recuperato fino al 10 agosto 2020 usando il backup dei log delle transazioni compreso tra il 24 agosto e il 25 agosto. 

 Per ripristinare il server di database, vedere la [procedura riportata](./how-to-restore-server-portal.md) di seguito.

> [!IMPORTANT]
> Le operazioni di ripristino in server flessibili creano un nuovo server di database e non sovrascrivono il server di database esistente.

Il ripristino temporizzato è utile in più scenari, ad esempio quando un utente per errore elimina dati o rimuove un database o una tabella importante oppure se un'applicazione sovrascrive accidentalmente dati corretti con dati non validi a causa di un difetto dell'applicazione. Sarà possibile eseguire il ripristino fino all'ultima transazione a causa del backup continuo dei log delle transazioni.

È possibile scegliere tra un punto di ripristino meno recente e un punto di ripristino personalizzato.

-   **Punto di ripristino meno recente** : a seconda del periodo di memorizzazione, sarà il momento più recente che è possibile ripristinare. Il tempo di backup meno recente verrà selezionato automaticamente e verrà visualizzato nel portale. Questa opzione è utile se si desidera analizzare o eseguire alcuni test a partire da tale punto nel tempo.

-   **Punto di ripristino personalizzato** : questa opzione consente di scegliere qualsiasi punto nel tempo entro il periodo di memorizzazione definito per questo server flessibile. Per impostazione predefinita, l'ora più recente in UTC viene selezionata automaticamente e utile se si desidera eseguire il ripristino fino all'ultima transazione di cui è stato eseguito il commit a scopo di test. Facoltativamente, è possibile scegliere altri giorni e ora. 

Il tempo stimato per il ripristino dipende da diversi fattori, tra cui le dimensioni del database, il volume dei log delle transazioni da elaborare, la larghezza di banda di rete e il numero totale di database ripristinati contemporaneamente nella stessa area. Il tempo di recupero complessivo richiede in genere da alcuni minuti fino a poche ore.


> [!IMPORTANT]
> **Non è possibile** ripristinare i server eliminati. Se si elimina il server, vengono eliminati anche tutti i database appartenenti al server e non sarà possibile recuperarli. Per proteggere le risorse del server, post-distribuzione, da eliminazioni accidentali o modifiche impreviste, gli amministratori possono sfruttare [blocchi di gestione](../../azure-resource-manager/management/lock-resources.md).

## <a name="perform-post-restore-tasks"></a>Eseguire le attività post-ripristino

Dopo avere ripristinato il database, è possibile eseguire le attività seguenti per eseguire il backup e l'esecuzione di utenti e applicazioni:

-   Se il nuovo server ha lo scopo di sostituire il server originale, reindirizzare i client e le applicazioni client al nuovo server.

-   Assicurarsi che le regole del firewall a livello di server e del VNet siano appropriate per consentire agli utenti di connettersi. Queste regole non vengono copiate dal server originale.
  
-   Il calcolo del server ripristinato può essere ridimensionato verso l'alto o verso il basso in base alle esigenze.

-   Verificare che siano presenti gli account di accesso e le autorizzazioni a livello di database appropriati.

-   Configurare gli avvisi in base alle esigenze.
  
-  Se è stato ripristinato il database configurato con la disponibilità elevata e si desidera configurare il server ripristinato con disponibilità elevata, è possibile seguire [la procedura](./how-to-manage-high-availability-portal.md).


## <a name="next-steps"></a>Passaggi successivi

-   Scopri di più sulla [continuità aziendale](./concepts-business-continuity.md)
-   Informazioni sulla [disponibilità elevata con ridondanza della zona](./concepts-high-availability.md)
-   Informazioni [su come eseguire il ripristino](./how-to-restore-server-portal.md)