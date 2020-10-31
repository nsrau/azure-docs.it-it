---
title: Backup e ripristino-database di Azure per MySQL
description: Informazioni su backup automatici e ripristino del server Database di Azure per MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: bbeb1248fef846afbd1641a668c6db3be4870ca6
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082097"
---
# <a name="backup-and-restore-in-azure-database-for-mysql"></a>Eseguire il backup e il ripristino in Database di Azure per MySQL

Database di Azure per MySQL crea automaticamente backup del server e li archivia in un archivio con ridondanza locale o geografica configurato dall'utente. I backup possono essere usati per ripristinare il server a un momento specifico. Il backup e il ripristino sono una parte essenziale di qualsiasi strategia di continuità aziendale, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale.

## <a name="backups"></a>Backup

Database di Azure per MySQL esegue i backup dei file di dati e del log delle transazioni. Questi backup consentono di ripristinare un server a qualsiasi momento specifico all'interno del periodo di conservazione dei backup configurato. Il periodo di conservazione dei backup predefinito è di sette giorni. [Facoltativamente, è possibile configurarlo](howto-restore-server-portal.md#set-backup-configuration) fino a 35 giorni. Tutti i backup vengono crittografati con crittografia AES a 256 bit.

Questi file di backup non sono esposti dall'utente e non possono essere esportati. Questi backup possono essere usati solo per le operazioni di ripristino nel database di Azure per MySQL. Per copiare un database, è possibile usare [mysqldump](concepts-migrate-dump-restore.md) .

Il tipo e la frequenza di backup variano a seconda dell'archiviazione back-end per i server.

### <a name="backup-type-and-frequency"></a>Tipo di backup e frequenza

#### <a name="basic-storage-servers"></a>Server di archiviazione Basic

L'archiviazione di base è l'archivio back-end che supporta i [server di livello Basic](concepts-pricing-tiers.md). I backup nei server di archiviazione Basic sono basati su snapshot. Ogni giorno viene eseguito uno snapshot completo del database. Per i server di archiviazione di base non sono stati eseguiti backup differenziali e tutti i backup di snapshot sono solo backup completi del database.

I backup del log delle transazioni vengono eseguiti ogni cinque minuti.

#### <a name="general-purpose-storage-servers-with-up-to-4-tb-storage"></a>Server di archiviazione per utilizzo generico con archiviazione fino a 4 TB

L'archiviazione per utilizzo generico è l'archiviazione back-end che supporta [per utilizzo generico](concepts-pricing-tiers.md) e il server di [livello con ottimizzazione](concepts-pricing-tiers.md) per la memoria Per i server con archiviazione per utilizzo generico fino a 4 TB, i backup completi si verificano una volta alla settimana. I backup differenziali si verificano due volte al giorno. I backup del log delle transazioni vengono eseguiti ogni cinque minuti. I backup nello spazio di archiviazione per utilizzo generico fino a un massimo di 4 TB non sono basati su snapshot e utilizzano la larghezza di banda IO al momento del backup. Per i database di grandi dimensioni (> 1 TB) nell'archiviazione da 4 TB, è consigliabile prendere in considerazione

- Provisioning di più IOPs per tenere conto del backup di IOs o
- In alternativa, eseguire la migrazione a una risorsa di archiviazione per utilizzo generico che supporta fino a 16 TB di archiviazione se l'infrastruttura di archiviazione sottostante è disponibile nelle [aree di Azure](/azure/mysql/concepts-pricing-tiers#storage)preferite. Non sono previsti costi aggiuntivi per l'archiviazione per utilizzo generico che supporta fino a 16 TB di archiviazione. Per assistenza sulla migrazione a una risorsa di archiviazione da 16 TB, aprire un ticket di supporto da portale di Azure.

#### <a name="general-purpose-storage-servers-with-up-to-16-tb-storage"></a>Server di archiviazione per utilizzo generico con archiviazione fino a 16 TB

In un sottoinsieme di [aree di Azure](/azure/mysql/concepts-pricing-tiers#storage), tutti i server di cui è stato effettuato il provisioning sono in grado di supportare l'archiviazione per utilizzo generico fino a 16 TB. In altre parole, l'archiviazione per utilizzo generico predefinito per tutte le [aree](/azure/mysql/concepts-pricing-tiers#storage) in cui è supportata è l'archiviazione fino a 16 TB. I backup in questi server di archiviazione da 16 TB sono basati su snapshot. Il primo backup completo dello snapshot viene pianificato subito dopo la creazione di un server. Il primo backup completo dello snapshot viene mantenuto come backup di base del server. I backup dello snapshot successivi sono solo backup differenziali.

In un sottoinsieme di [aree di Azure](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage), tutti i server di cui è stato effettuato il provisioning sono in grado di supportare l'archiviazione per utilizzo generico fino a 16 TB. In altre parole, l'archiviazione per utilizzo generico predefinito per tutte le [aree](/concepts-pricing-tiers.md#storage) in cui è supportata è l'archiviazione fino a 16 TB. I backup in questi server di archiviazione da 16 TB sono basati su snapshot. Il primo backup completo dello snapshot viene pianificato subito dopo la creazione di un server. Il primo backup completo dello snapshot viene mantenuto come backup di base del server. I backup dello snapshot successivi sono solo backup differenziali.

I backup differenziali degli snapshot vengono eseguiti almeno una volta al giorno. I backup differenziali degli snapshot non vengono eseguiti in base a una pianificazione fissa. I backup differenziali degli snapshot si verificano ogni 24 ore, a meno che il log delle transazioni (binlog in MySQL) superi 50 GB dall'ultimo backup differenziale. In un giorno sono consentiti al massimo sei snapshot differenziali.

I backup del log delle transazioni vengono eseguiti ogni cinque minuti.

### <a name="backup-retention"></a>Conservazione dei backup

I backup vengono conservati in base all'impostazione del periodo di conservazione dei backup nel server. È possibile selezionare un periodo di conservazione compreso tra 7 e 35 giorni. Il periodo di memorizzazione predefinito è 7 giorni. È possibile impostare il periodo di conservazione durante la creazione del server o in un secondo momento aggiornando la configurazione di backup usando [portale di Azure](./howto-restore-server-portal.md#set-backup-configuration) o l' [interfaccia](./howto-restore-server-cli.md#set-backup-configuration)della riga di comando

Il periodo di conservazione dei backup determina quanto è possibile tornare indietro nel tempo con un ripristino temporizzato, essendo il ripristino basato sui backup disponibili. Il periodo di conservazione dei backup può essere trattato anche come una finestra di ripristino da una prospettiva di ripristino. Tutti i backup necessari per eseguire un ripristino temporizzato entro il periodo di conservazione dei backup vengono conservati nell'archivio di backup. Se, ad esempio, il periodo di conservazione dei backup è impostato su 7 giorni, la finestra di ripristino viene considerata gli ultimi 7 giorni. In questo scenario vengono conservati tutti i backup necessari per ripristinare il server negli ultimi 7 giorni. Con un intervallo di conservazione dei backup di sette giorni:

- I server con archiviazione fino a 4 TB manterranno fino a 2 backup completi del database, tutti i backup differenziali e i backup del log delle transazioni eseguiti dopo il primo backup completo del database.
- I server con archiviazione fino a 16 TB manterranno lo snapshot completo del database, tutti gli snapshot differenziali e i backup del log delle transazioni negli ultimi 8 giorni.

#### <a name="long-term-retention"></a>Conservazione a lungo termine

La conservazione a lungo termine dei backup oltre 35 giorni non è ancora supportata in modo nativo dal servizio. È possibile usare mysqldump per eseguire i backup e archiviarli per la conservazione a lungo termine. Il team di supporto ha bloggato un [articolo dettagliato](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/automate-backups-of-your-azure-database-for-mysql-server-to/ba-p/1791157) per condividere il modo in cui è possibile ottenerlo.

### <a name="backup-redundancy-options"></a>Opzioni di ridondanza per il backup

Nei livelli Utilizzo generico e Con ottimizzazione per la memoria, Database di Azure per MySQL offre la possibilità di scegliere tra archiviazione dei backup con ridondanza locale o con ridondanza geografica. Quando vengono archiviati in un archivio di backup con ridondanza geografica, i backup non solo vengono archiviati nell'area in cui è ospitato il server, ma vengono anche replicati in un [data center abbinato](../best-practices-availability-paired-regions.md). Questa ridondanza geografica offre una migliore protezione e la possibilità di ripristinare il server in un'area diversa in caso di emergenza. Il livello Basic offre solo l'archiviazione dei backup con ridondanza locale.

#### <a name="moving-from-locally-redundant-to-geo-redundant-backup-storage"></a>Passaggio dall'archiviazione con ridondanza locale al backup con ridondanza geografica

La configurazione dell'archiviazione con ridondanza locale o geografica per il backup è consentita solo durante la creazione del server. Dopo il provisioning del server, non è possibile modificare l'opzione di ridondanza per l'archivio di backup. Per spostare l'archivio di backup dall'archiviazione con ridondanza locale all'archiviazione con ridondanza geografica, la creazione di un nuovo server e la migrazione dei dati tramite [dump e Restore](concepts-migrate-dump-restore.md) sono l'unica opzione supportata.

### <a name="backup-storage-cost"></a>Costo dell'archiviazione dei backup

Database di Azure per MySQL offre fino al 100% delle risorse di archiviazione del server di cui è stato effettuato il provisioning come archivio di backup senza costi aggiuntivi. Ogni ulteriore spazio di archiviazione di backup utilizzato viene addebitato in GB al mese. Se, ad esempio, è stato effettuato il provisioning di un server con 250 GB di spazio di archiviazione, sono disponibili 250 GB di spazio di archiviazione aggiuntivo per i backup del server senza costi aggiuntivi. Lo spazio di archiviazione utilizzato per i backup con più di 250 GB viene addebitato in base al [modello di determinazione prezzi](https://azure.microsoft.com/pricing/details/mysql/).

È possibile usare la metrica di [archiviazione di backup utilizzata](concepts-monitoring.md) in monitoraggio di Azure disponibile tramite il portale di Azure per monitorare l'archiviazione di backup utilizzata da un server. La metrica di archiviazione di backup utilizzata rappresenta la somma dello spazio di archiviazione utilizzato da tutti i backup completi del database, backup differenziali e backup del log mantenuti in base al periodo di conservazione dei backup impostato per il server. La frequenza dei backup è gestita dal servizio e illustrata in precedenza. Un'intensa attività transazionale sul server può causare un aumento dell'uso dell'archivio di backup indipendentemente dalle dimensioni totali del database. Per l'archiviazione con ridondanza geografica, l'utilizzo dell'archiviazione di backup è due volte quello dell'archiviazione con ridondanza locale.

Il modo principale per controllare i costi di archiviazione dei backup consiste nell'impostare il periodo di conservazione dei backup appropriato e scegliere le opzioni di ridondanza di backup corrette per soddisfare gli obiettivi di ripristino desiderati. È possibile selezionare un periodo di conservazione compreso tra 7 e 35 giorni. I server per utilizzo generico e con ottimizzazione per la memoria possono scegliere di disporre di archiviazione con ridondanza geografica per i backup.

## <a name="restore"></a>Restore

In database di Azure per MySQL l'esecuzione di un ripristino crea un nuovo server dai backup del server originale e ripristina tutti i database contenuti nel server.

Sono disponibili due tipi di ripristino:

- Il **ripristino temporizzato** è disponibile con l'opzione di ridondanza dei backup e crea un nuovo server nella stessa area del server originale utilizzando la combinazione di backup completi e del log delle transazioni.
- Il **ripristino geografico** è disponibile solo se il server è stato configurato per l'archiviazione con ridondanza geografica e consente di ripristinare il server in un'area diversa usando il backup più recente adottato.

Il tempo stimato per il ripristino dipende da diversi fattori, tra cui le dimensioni dei database, le dimensioni dei log delle transazioni, la larghezza di banda di rete e il numero totale di database ripristinati contemporaneamente nella stessa area. Il tempo di recupero di solito è inferiore a 12 ore.

> [!IMPORTANT]
> I server eliminati possono essere ripristinati solo entro **cinque giorni** dall'eliminazione dopo il quale vengono eliminati i backup. È possibile accedere al backup del database e ripristinarlo solo dalla sottoscrizione di Azure che ospita il server. Per ripristinare un server eliminato, vedere la [procedura documentata](howto-restore-dropped-server.md). Per proteggere le risorse del server, post-distribuzione, da eliminazioni accidentali o modifiche impreviste, gli amministratori possono sfruttare [blocchi di gestione](../azure-resource-manager/management/lock-resources.md).

### <a name="point-in-time-restore"></a>Ripristino temporizzato

Indipendentemente dall'opzione di ridondanza per il backup scelta, è possibile eseguire il ripristino a qualsiasi momento specifico all'interno del periodo di conservazione dei backup. Verrà creato un nuovo server nella stessa area di Azure del server originale, nonché con la stessa configurazione in termini di piano tariffario, generazione di calcolo, numero di vCore, dimensioni di archiviazione, periodo di conservazione dei backup e opzione di ridondanza per il backup.

> [!NOTE]
> Sono disponibili due parametri del server che vengono reimpostati sui valori predefiniti (e non vengono copiati dal server primario) dopo l'operazione di ripristino
>
> - time_zone: questo valore per impostare il valore predefinito del **sistema**
> - event_scheduler: il event_scheduler è impostato su **off** nel server ripristinato
>
> È necessario impostare questi parametri del server riconfigurando il [parametro Server](howto-server-parameters.md)

Il ripristino temporizzato è utile in più scenari, ad esempio quando un utente per errore elimina dati o rimuove un database o una tabella importante oppure se un'applicazione sovrascrive accidentalmente dati corretti con dati non validi a causa di un difetto dell'applicazione.

Per poter eseguire il ripristino a un momento specifico negli ultimi cinque minuti, potrebbe essere prima necessario attendere l'esecuzione del backup del log delle transazioni successivo.

### <a name="geo-restore"></a>Ripristino geografico

Se il server è stato configurato per backup con ridondanza geografica, è possibile ripristinare un server in un'altra area di Azure in cui il servizio è disponibile. I server che supportano fino a 4 TB di spazio di archiviazione possono essere ripristinati nell'area geografica abbinata o in qualsiasi area che supporta fino a 16 TB di spazio di archiviazione. Per i server che supportano fino a 16 TB di archiviazione, è possibile ripristinare i backup geografici in qualsiasi area che supporti i server da 16 TB. Esaminare i [piani tariffari di database di Azure per MySQL](concepts-pricing-tiers.md) per l'elenco delle aree supportate.

Il ripristino geografico è l'opzione di ripristino predefinita quando il server non è disponibile a causa di un evento imprevisto nell'area in cui è ospitato. Se un evento imprevisto su larga scala determina la mancata disponibilità dell'applicazione di database, è possibile ripristinare un server dai backup con ridondanza geografica in un server in un'altra area. Il ripristino geografico usa il backup più recente del server. Esiste un ritardo tra il momento in cui un backup viene creato e quando ne viene eseguita la replica in un'area diversa. Questo ritardo può essere al massimo di un'ora, quindi, in caso di emergenza, può verificarsi una perdita massima di un'ora di dati.

Durante il ripristino geografico è possibile modificare le seguenti opzioni relative alle configurazioni del server: generazione delle risorse di calcolo, vCore, periodo di conservazione dei backup e ridondanza per il backup. La modifica del piano tariffario (Basic, Utilizzo generico o Con ottimizzazione per la memoria) o delle dimensioni della risorsa di archiviazione non è supportata durante il ripristino geografico.

### <a name="perform-post-restore-tasks"></a>Eseguire le attività post-ripristino

Dopo il ripristino con uno dei due meccanismi, per rendere nuovamente operativi gli utenti e le applicazioni è consigliabile eseguire queste attività:

- Se il nuovo server è destinato a sostituire il server originale, reindirizzare i client e le applicazioni client al nuovo server
- Verificare che siano disponibili regole VNet appropriate per la connessione degli utenti. Queste regole non vengono copiate dal server originale.
- Verificare che siano presenti gli account di accesso e le autorizzazioni a livello di database appropriati
- Configurare gli avvisi in base alle proprie esigenze.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla continuità aziendale, vedere la relativa  [panoramica](concepts-business-continuity.md).
- Per eseguire il ripristino temporizzato usando il portale di Azure, vedere [ripristinare il server a un punto nel tempo usando il portale di Azure](howto-restore-server-portal.md).
- Per eseguire il ripristino temporizzato usando l'interfaccia della riga di comando di Azure, vedere [ripristinare un server a un punto nel tempo usando l'interfaccia della](howto-restore-server-cli.md)riga di comando.