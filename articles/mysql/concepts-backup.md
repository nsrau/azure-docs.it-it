---
title: Backup e ripristino - Database di Azure per MySQL
description: Informazioni su backup automatici e ripristino del server Database di Azure per MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: 3a6162bb381f4e54114e3cabbf138f5b1c6aaae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373023"
---
# <a name="backup-and-restore-in-azure-database-for-mysql"></a>Eseguire il backup e il ripristino in Database di Azure per MySQL

Database di Azure per MySQL crea automaticamente backup del server e li archivia in un archivio con ridondanza locale o geografica configurato dall'utente. I backup possono essere usati per ripristinare il server a un momento specifico. Il backup e il ripristino sono una parte essenziale di qualsiasi strategia di continuità aziendale, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale.

## <a name="backups"></a>Backup

Il database di Azure per MySQL esegue il backup dei file di dati e del log delle transazioni. A seconda delle dimensioni massime di archiviazione supportate, vengono eseguiti backup completi e differenziali (server di archiviazione max 4 TB) o backup snapshot (fino a 16 TB di server di archiviazione max). Questi backup consentono di ripristinare un server a qualsiasi momento specifico all'interno del periodo di conservazione dei backup configurato. Il periodo di conservazione dei backup predefinito è di sette giorni. [Facoltativamente,](howto-restore-server-portal.md#set-backup-configuration) è possibile configurarlo fino a 35 giorni. Tutti i backup vengono crittografati con crittografia AES a 256 bit.

Questi file di backup non sono esposti all'utente e non possono essere esportati. Questi backup possono essere usati solo per le operazioni di ripristino nel database di Azure per MySQL.These backups can only be used for restore operations in Azure Database for MySQL. È possibile utilizzare [mysqldump](concepts-migrate-dump-restore.md) per copiare un database.

### <a name="backup-frequency"></a>Frequenza di backup

In genere, i backup completi vengono eseguiti settimanalmente, i backup differenziali vengono eseguiti due volte al giorno per i server con uno spazio di archiviazione supportato al massimo di 4 TB. I backup di snapshot vengono eseguiti almeno una volta al giorno per i server che supportano fino a 16 TB di spazio di archiviazione. I backup del log delle transazioni si verificano in entrambi i casi ogni cinque minuti. Il primo snapshot del backup completo viene pianificato immediatamente dopo la creazione di un server. Il backup completo iniziale può richiedere più tempo su un server ripristinato di grandi dimensioni. Il momento meno recente a cui può essere ripristinato un nuovo server è quello del completamento del backup completo iniziale. Poiché le istantanee sono istantanee, i server con supporto fino a 16 TB di storage possono essere ripristinati fino al tempo di creazione.

### <a name="backup-redundancy-options"></a>Opzioni di ridondanza per il backup

Nei livelli Utilizzo generico e Con ottimizzazione per la memoria, Database di Azure per MySQL offre la possibilità di scegliere tra archiviazione dei backup con ridondanza locale o con ridondanza geografica. Quando vengono archiviati in un archivio di backup con ridondanza geografica, i backup non solo vengono archiviati nell'area in cui è ospitato il server, ma vengono anche replicati in un [data center abbinato](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Questo garantisce una migliore protezione e la possibilità di ripristinare il server in un'area diversa in caso di emergenza. Il livello Basic offre solo l'archiviazione dei backup con ridondanza locale.

> [!IMPORTANT]
> La configurazione dell'archiviazione con ridondanza locale o geografica per il backup è consentita solo durante la creazione del server. Dopo il provisioning del server, non è possibile modificare l'opzione di ridondanza per l'archivio di backup.

### <a name="backup-storage-cost"></a>Costo dell'archiviazione dei backup

Database di Azure per MySQL offre fino al 100% delle risorse di archiviazione del server di cui è stato effettuato il provisioning come archivio di backup senza costi aggiuntivi. Questo in genere è idoneo per un periodo di conservazione dei backup di sette giorni. Per lo spazio di archiviazione aggiuntivo usato per i backup è previsto un addebito in GB al mese.

Se è stato effettuato il provisioning di un server con 250 GB, ad esempio, sono disponibili 250 GB per l'archiviazione dei backup senza costi aggiuntivi. Lo spazio di archiviazione oltre 250 GB viene addebitato.

## <a name="restore"></a>Restore

In Database di Azure per MySQL, l'esecuzione di un ripristino crea un nuovo server dai backup del server originale e ripristina tutti i database contenuti nel server.

Sono disponibili due tipi di ripristino:

- **Il ripristino temporizzato** è disponibile con l'opzione di ridondanza del backup e crea un nuovo server nella stessa area del server originale utilizzando la combinazione di backup completi e del log delle transazioni.
- **Il ripristino geografico** è disponibile solo se il server è stato configurato per l'archiviazione con ridondanza geografica e consente di ripristinare il server in un'area diversa utilizzando il backup più recente eseguito.

Il tempo stimato per il ripristino dipende da diversi fattori, tra cui le dimensioni dei database, le dimensioni dei log delle transazioni, la larghezza di banda di rete e il numero totale di database ripristinati contemporaneamente nella stessa area. Il tempo di recupero di solito è inferiore a 12 ore.

> [!IMPORTANT]
> **Non è possibile** ripristinare i server eliminati. Se si elimina il server, vengono eliminati anche tutti i database appartenenti al server e non sarà possibile recuperarli. Per proteggere le risorse del server, post-distribuzione, da eliminazioni accidentali o modifiche impreviste, gli amministratori possono sfruttare [blocchi di gestione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

### <a name="point-in-time-restore"></a>Ripristino temporizzato

Indipendentemente dall'opzione di ridondanza per il backup scelta, è possibile eseguire il ripristino a qualsiasi momento specifico all'interno del periodo di conservazione dei backup. Verrà creato un nuovo server nella stessa area di Azure del server originale, nonché con la stessa configurazione in termini di piano tariffario, generazione di calcolo, numero di vCore, dimensioni di archiviazione, periodo di conservazione dei backup e opzione di ridondanza per il backup.

Il ripristino temporizzato è utile in più scenari, ad esempio quando un utente per errore elimina dati o rimuove un database o una tabella importante oppure se un'applicazione sovrascrive accidentalmente dati corretti con dati non validi a causa di un difetto dell'applicazione.

Per poter eseguire il ripristino a un momento specifico negli ultimi cinque minuti, potrebbe essere prima necessario attendere l'esecuzione del backup del log delle transazioni successivo.

### <a name="geo-restore"></a>Ripristino geografico

Se il server è stato configurato per backup con ridondanza geografica, è possibile ripristinare un server in un'altra area di Azure in cui il servizio è disponibile. I server che supportano fino a 4 TB di spazio di archiviazione possono essere ripristinati nell'area con associazione geografica o in qualsiasi area che supporta fino a 16 TB di spazio di archiviazione. Per i server che supportano fino a 16 TB di spazio di archiviazione, i backup geografici possono essere ripristinati anche in qualsiasi area che supporti server da 16 TB. Esaminare [Database di Azure per i livelli dei prezzi MySQL](concepts-pricing-tiers.md) per l'elenco delle aree supportate.

Il ripristino geografico è l'opzione di ripristino predefinita quando il server non è disponibile a causa di un evento imprevisto nell'area in cui è ospitato. Se un evento imprevisto su larga scala determina la mancata disponibilità dell'applicazione di database, è possibile ripristinare un server dai backup con ridondanza geografica in un server in un'altra area. Il ripristino geografico utilizza il backup più recente del server. Esiste un ritardo tra il momento in cui un backup viene creato e quando ne viene eseguita la replica in un'area diversa. Questo ritardo può essere al massimo di un'ora, quindi, in caso di emergenza, può verificarsi una perdita massima di un'ora di dati.

Durante il ripristino geografico è possibile modificare le seguenti opzioni relative alle configurazioni del server: generazione delle risorse di calcolo, vCore, periodo di conservazione dei backup e ridondanza per il backup. La modifica del piano tariffario (Basic, Utilizzo generico o Con ottimizzazione per la memoria) o delle dimensioni della risorsa di archiviazione non è supportata durante il ripristino geografico.

### <a name="perform-post-restore-tasks"></a>Eseguire le attività post-ripristino

Dopo il ripristino con uno dei due meccanismi, per rendere nuovamente operativi gli utenti e le applicazioni è consigliabile eseguire queste attività:

- Se il nuovo server è destinato a sostituire il server originale, reindirizzare i client e le applicazioni client al nuovo server
- Assicurarsi che siano disponibili regole di rete virtuale appropriate per la connessione degli utenti. Queste regole non vengono copiate dal server originale.
- Verificare che siano presenti gli account di accesso e le autorizzazioni a livello di database appropriati
- Configurare gli avvisi in base alle proprie esigenze.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla continuità aziendale, vedere la relativa  [panoramica](concepts-business-continuity.md).
- Per eseguire il ripristino in un momento base tramite il portale di Azure, vedere Ripristinare il server in un momento base [tramite il portale](howto-restore-server-portal.md)di Azure.
- Per eseguire il ripristino a un punto nel tempo usando l'interfaccia della riga di comando di Azure, vedere Ripristinare il server in un momento [nel tempo usando l'interfaccia della riga di comando.](howto-restore-server-cli.md)
