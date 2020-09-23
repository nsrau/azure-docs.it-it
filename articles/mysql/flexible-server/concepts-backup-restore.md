---
title: Eseguire il backup e il ripristino nel server flessibile database di Azure per MySQL
description: Informazioni sui concetti di backup e ripristino con il server flessibile database di Azure per MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: a5f11a6054358584a8511cb967860c89f6491beb
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939305"
---
# <a name="backup-and-restore-in-azure-database-for-mysql-flexible-server-preview"></a>Backup e ripristino nel database di Azure per il server flessibile MySQL (anteprima)

> [!IMPORTANT] 
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica.

Database di Azure per MySQL server flessibile, crea automaticamente backup del server e li archivia in modo sicuro nell'archiviazione con ridondanza locale all'interno dell'area. I backup possono essere usati per ripristinare il server a un momento specifico. Il backup e il ripristino sono una parte essenziale di qualsiasi strategia di continuità aziendale, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale.

## <a name="backup-overview"></a>Panoramica del servizio Backup

Il server flessibile esegue backup di snapshot dei file di dati e li archivia in una risorsa di archiviazione con ridondanza locale. Il server esegue anche il backup dei log delle transazioni e li archivia nell'archiviazione con ridondanza locale. Questi backup consentono di ripristinare un server a qualsiasi momento specifico all'interno del periodo di conservazione dei backup configurato. Il periodo di conservazione dei backup predefinito è di sette giorni. Facoltativamente, è possibile configurare il backup del database da 1 a 35 giorni. Tutti i backup vengono crittografati usando la crittografia AES a 256 bit per i dati archiviati inattivi.

Non è possibile esportare i file di backup. I backup possono essere utilizzati solo per operazioni di ripristino in un server flessibile. È anche possibile usare [mysqldump](https://docs.microsoft.com/azure/MySQL/howto-migrate-using-dump-and-restore)   da un client MySQL per copiare un database.

## <a name="backup-frequency"></a>Frequenza di backup

I backup nei server flessibili sono basati su snapshot. Il primo backup completo dello snapshot viene pianificato subito dopo la creazione di un server. Il primo backup completo dello snapshot viene mantenuto come backup di base del server. I backup dello snapshot successivi sono solo backup differenziali.

I backup differenziali degli snapshot vengono eseguiti almeno una volta al giorno. I backup differenziali degli snapshot non vengono eseguiti in base a una pianificazione fissa. I backup differenziali degli snapshot vengono eseguiti ogni 24 ore, a meno che i log binari in MySQL non superino 50 GB dall'ultimo backup differenziale. In un giorno sono consentiti al massimo sei snapshot differenziali. I backup del log delle transazioni vengono eseguiti ogni cinque minuti.

## <a name="backup-retention"></a>Conservazione dei backup

I backup del database vengono archiviati in una risorsa di archiviazione con ridondanza locale (con ridondanza locale), archiviata in tre copie all'interno di un'area. I backup vengono conservati in base all'impostazione del periodo di conservazione dei backup nel server. È possibile selezionare un periodo di conservazione compreso tra 1 e 35 giorni e il periodo di conservazione predefinito è di sette giorni. È possibile impostare il periodo di conservazione durante la creazione del server o in un secondo momento aggiornando la configurazione del backup utilizzando portale di Azure.

Il periodo di conservazione dei backup controlla il tempo di esecuzione di un'operazione di ripristino temporizzato, perché si basa su backup disponibili. Il periodo di conservazione dei backup può essere trattato anche come una finestra di ripristino da una prospettiva di ripristino. Tutti i backup necessari per eseguire un ripristino temporizzato entro il periodo di conservazione dei backup vengono conservati nell'archivio di backup. Ad esempio, se il periodo di conservazione dei backup è impostato su sette giorni, la finestra di ripristino viene considerata negli ultimi sette giorni. In questo scenario vengono conservati tutti i backup necessari per ripristinare il server negli ultimi sette giorni. Con un intervallo di conservazione dei backup di sette giorni, gli snapshot del database e i backup del log delle transazioni vengono archiviati negli ultimi otto giorni (1 giorno prima della finestra).

## <a name="backup-storage-cost"></a>Costo dell'archiviazione dei backup

Il server flessibile offre fino al 100% dell'archiviazione del server di cui è stato effettuato il provisioning come archivio di backup senza costi aggiuntivi. Ogni ulteriore spazio di archiviazione di backup utilizzato viene addebitato in GB al mese. Se, ad esempio, è stato effettuato il provisioning di un server con 250 GB di spazio di archiviazione, sono disponibili 250 GB di spazio di archiviazione per i backup del server senza costi aggiuntivi. Se l'utilizzo del backup giornaliero è 25GB, è possibile disporre di un massimo di 10 giorni di archiviazione di backup gratuita. Lo spazio di archiviazione utilizzato per i backup con più di 250 GB viene addebitato in base al [modello di determinazione prezzi](https://azure.microsoft.com/pricing/details/mysql/).

È possibile usare la metrica di [archiviazione di backup utilizzata](https://docs.microsoft.com/azure/mysql/concepts-monitoring)   in monitoraggio di Azure disponibile nel portale di Azure per monitorare l'archiviazione di backup utilizzata da un server. La metrica di **archiviazione di backup** utilizzata rappresenta la somma dello spazio di archiviazione utilizzato da tutti i backup del database e dei backup del log mantenuti in base al periodo di conservazione dei backup impostato per il server. Un'intensa attività transazionale sul server può causare un aumento dell'uso dell'archivio di backup indipendentemente dalle dimensioni totali del database.

Il modo principale per controllare i costi di archiviazione dei backup consiste nell'impostare il periodo di conservazione dei backup appropriato. È possibile selezionare un periodo di conservazione compreso tra 1 e 35 giorni.

> [!IMPORTANT]
> I backup da un server di database configurato in una configurazione a disponibilità elevata con ridondanza della zona si verificano dal server di database primario perché l'overhead è minimo con i backup di snapshot.

> [!IMPORTANT]
> I backup con ridondanza geografica non sono attualmente supportati con il server flessibile.

## <a name="point-in-time-restore"></a>Ripristino temporizzato

Nel database di Azure per MySQL server flessibile, l'esecuzione di un ripristino temporizzato consente di creare un nuovo server dai backup del server flessibili nella stessa area del server di origine. Viene creata con la configurazione del server originale per il livello di calcolo, il numero di Vcore, le dimensioni di archiviazione, il periodo di conservazione dei backup e l'opzione di ridondanza dei backup. Inoltre, i tag e le impostazioni come la rete virtuale e il firewall vengono ereditati dal server di origine. Il livello di calcolo e di archiviazione del server ripristinato, le impostazioni di configurazione e sicurezza possono essere modificate dopo il completamento del ripristino.

> [!NOTE]
> Sono disponibili due parametri del server che vengono reimpostati sui valori predefiniti (e non vengono copiati dal server primario) dopo l'operazione di ripristino
> *   time_zone: questo valore per impostare il valore predefinito del sistema
> *   event_scheduler: il event_scheduler è impostato su OFF nel server ripristinato

Il ripristino temporizzato è utile in più scenari, Alcuni dei casi d'uso comuni includono-
-   Quando un utente elimina accidentalmente i dati nel database
-   L'utente elimina una tabella o un database importante
-   L'applicazione utente sovrascrive accidentalmente dati validi con dati errati a causa di un difetto dell'applicazione.

È possibile scegliere tra un punto di ripristino più recente e un punto di ripristino personalizzato tramite [portale di Azure](how-to-restore-server-portal.md).

-   **Punto di ripristino più recente**: il punto di ripristino più recente consente di ripristinare il server per l'ultimo backup eseguito nel server di origine. Il timestamp per il ripristino verrà visualizzato anche nel portale. Questa opzione è utile per ripristinare rapidamente il server allo stato più aggiornato.
-   **Punto di ripristino personalizzato**: consente di scegliere qualsiasi punto nel tempo entro il periodo di memorizzazione definito per questo server flessibile. Questa opzione è utile per ripristinare il server nel momento esatto in cui eseguire il recupero da un errore dell'utente.

Il tempo stimato per il ripristino dipende da diversi fattori, tra cui le dimensioni del database, le dimensioni del backup del log delle transazioni, le dimensioni di calcolo dello SKU e anche l'ora del ripristino. Il recupero del log delle transazioni è la maggior parte del tempo di utilizzo del processo di ripristino. Se il tempo di ripristino viene scelto più vicino alla pianificazione del backup completo o differenziale dello snapshot, i ripristini sono più veloci perché l'applicazione del log delle transazioni è minima. Per stimare il tempo di ripristino esatto per il server, è consigliabile eseguirne il test nel proprio ambiente perché contiene troppe variabili specifiche dell'ambiente.

> [!IMPORTANT]
> Se si ripristina un server flessibile configurato con disponibilità elevata con ridondanza della zona, il server ripristinato verrà configurato nella stessa area e nella stessa zona del server primario e distribuito come un singolo server flessibile in una modalità non a disponibilità elevata. Per un server flessibile, vedere [disponibilità elevata con ridondanza della zona](concepts-high-availability.md) .

> [!IMPORTANT]
>  **Non è possibile**ripristinare i server eliminati   . Se si elimina il server, vengono eliminati anche tutti i database appartenenti al server e non sarà possibile recuperarli. Per proteggere le risorse del server, post-distribuzione, da eliminazioni accidentali o modifiche impreviste, gli amministratori possono sfruttare i [blocchi di gestione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

## <a name="perform-post-restore-tasks"></a>Eseguire le attività post-ripristino

Dopo un ripristino da un **punto di ripristino più recente** o da un meccanismo di ripristino del **punto di ripristino personalizzato** , è necessario eseguire le attività seguenti per eseguire il backup e l'esecuzione di utenti e applicazioni:

-   Se il nuovo server ha lo scopo di sostituire il server originale, reindirizzare i client e le applicazioni client al nuovo server.
-   Assicurarsi che le regole appropriate per il firewall a livello di server e la rete virtuale siano disponibili per la connessione degli utenti.
-   Verificare che siano presenti gli account di accesso e le autorizzazioni a livello di database appropriati.
-   Configurare gli avvisi in base alle esigenze.

## <a name="next-steps"></a>Passaggi successivi

-   Scopri di più sulla [continuità aziendale](./concepts-business-continuity.md)
-   Informazioni sulla [disponibilità elevata con ridondanza della zona](./concepts-high-availability.md)
-   Informazioni su [backup e ripristino](./concepts-backup-restore.md)