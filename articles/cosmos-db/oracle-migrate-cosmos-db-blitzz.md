---
title: Eseguire la migrazione dei dati da Oracle ad Azure Cosmos DB Cassandra API usando BlitzzMigrate data from Oracle to Azure Cosmos DB Cassandra API using Blitzz
description: Informazioni su come eseguire la migrazione dei dati dal database Oracle all'API Cassandra DB di Azure con Blitzz.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 43d15a7252819a3e4f7635e37458b75e9b7ecca7
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546281"
---
# <a name="migrate-data-from-oracle-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>Eseguire la migrazione dei dati da Oracle all'account API Cassandra DB Cosmos di Azure usando BlitzzMigrate data from Oracle to Azure Cosmos DB Cassandra API account using Blitzz

L'API Cassandra in Azure Cosmos DB è diventata un'ottima scelta per i carichi di lavoro aziendali in esecuzione su Oracle per una serie di motivi, ad esempio:Cassandra API in Azure Cosmos DB has become a great choice for enterprise workloads that are running on Oracle for a variety reasons such as:

* **Migliore scalabilità e disponibilità:** Elimina singoli punti di errore, scalabilità migliore e disponibilità per le applicazioni.

* **Notevoli risparmi sui costi:** È possibile risparmiare sui costi con Azure Cosmos DB, che include il costo delle macchine virtuali, della larghezza di banda e di tutte le licenze Oracle applicabili. Inoltre, non è necessario gestire i data center, i server, l'archiviazione SSD, la rete e i costi dell'elettricità.

* **Nessun sovraccarico di gestione e monitoraggio:** Come servizio cloud completamente gestito, Azure Cosmos DB elimina l'overhead della gestione e del monitoraggio di una miriade di impostazioni.

Esistono vari modi per eseguire la migrazione dei carichi di lavoro del database da una piattaforma a un'altra. [Blitzz](https://www.blitzz.io) è uno strumento che offre un modo sicuro e affidabile per eseguire zero operazioni di downtime migrazione da una varietà di database a Azure Cosmos DB. Questo articolo descrive i passaggi necessari per eseguire la migrazione dei dati dal database Oracle all'API Cassandra DB di Azure Cosmos usando Blitzz.This article describes the steps required to migrate data from Oracle database to Azure Cosmos DB Cassandra API using Blitzz.

## <a name="benefits-using-blitzz-for-migration"></a>Vantaggi dell'utilizzo di Blitzz per la migrazione

La soluzione di migrazione di Blitzz segue un approccio graduale per eseguire la migrazione di carichi di lavoro operativi complessi. Di seguito sono riportati alcuni degli aspetti chiave del piano di migrazione a tempo inattività zero di Blitzz:

* Offre la migrazione automatica della logica di business (tabelle, indici, viste) dal database Oracle al database Cosmos di Azure.It offers automatic migration of business logic (tables, indexes, views) from Oracle database to Azure Cosmos DB. Non è necessario creare gli schemi manualmente.

* Blitzz offre la replica di database di grandi volumi e paralleli. Consente a entrambe le piattaforme di origine e di destinazione di essere sincronizzate durante la migrazione utilizzando una tecnica denominata Change-Data-Capture (CDC). Utilizzando CDC, Blitzz estrae continuamente un flusso di modifiche dal database di origine (Oracle) e lo applica al database di destinazione (Azure Cosmos DB).

* È a tolleranza d'errore e garantisce esattamente una volta che la consegna dei dati anche durante un guasto hardware o software nel sistema.

* Protegge i dati durante il transito utilizzando una varietà di metodologie di sicurezza come TLS/SSL, crittografia.

* Offre servizi per convertire la logica di business complessa scritta in PL/SQL in logica di business equivalente in Azure Cosmos DB.

## <a name="steps-to-migrate-data"></a>Passaggi per la migrazione dei datiSteps to migrate data

Questa sezione descrive i passaggi necessari per configurare Blitzz ed esegue la migrazione dei dati dal database Oracle al database Cosmos di Azure.This section describes the steps required to setup Blitzz and migrates data from Oracle database to Azure Cosmos DB.

1. Dal computer in cui si prevede di installare il replicante Blitzz, aggiungere un certificato di sicurezza. Questo certificato è richiesto dal replicante Blitzz per stabilire una connessione TLS con l'account Azure Cosmos DB specificato. È possibile aggiungere il certificato attenendosi alla seguente procedura:

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. ou può ottenere l'installazione Blitzz e i file binari sia richiedendo una demo sul [sito Blitzz](https://www.blitzz.io). In alternativa, è anche possibile inviare un messaggio di [posta elettronica](mailto:success@blitzz.io) al team.

   ![Download dello strumento replicante Blitzz](./media/oracle-migrate-cosmos-db-blitzz/blitzz-replicant-download.png)

   ![File replicanti Blitzz](./media/oracle-migrate-cosmos-db-blitzz/replicant-files.png)

1. Dal terminale CLI, impostare la configurazione del database di origine. Aprire il file **`vi conf/conn/oracle.yml`** di configurazione utilizzando il comando e aggiungere un elenco separato da virgole di indirizzi IP dei nodi dell'oracolo, numero di porta, nome utente, password e qualsiasi altro dettaglio richiesto. Il codice seguente mostra un file di configurazione di esempio:The following code shows an example configuration file:

   ```bash
   type: ORACLE

   host: localhost
   port: 53546

   service-name: IO

   username: '<Username of your Oracle database>'
   password: '<Password of your Oracle database>'

   conn-cnt: 30
   use-ssl: false
   ```

   ![Aprire l'editor delle connessioni OracleOpen Oracle connection editor](./media/oracle-migrate-cosmos-db-blitzz/open-connection-editor-oracle.png)

   ![Configurazione della connessione Oracle](./media/oracle-migrate-cosmos-db-blitzz/oracle-connection-configuration.png)

   Dopo aver compilato i dettagli di configurazione, salvare e chiudere il file.

1. Facoltativamente, è possibile impostare il file del filtro del database di origine. Il file di filtro specifica gli schemi o le tabelle di cui eseguire la migrazione. Aprire il file **`vi filter/oracle_filter.yml`** di configurazione utilizzando il comando e immettere i seguenti dettagli di configurazione:

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```
 
   Dopo aver compilato i dettagli del filtro del database, salvare e chiudere il file.

1. Successivamente si imposterà la configurazione del database di destinazione. Prima di definire la configurazione, [creare un account API Cassandra db di Azure.](create-cassandra-dotnet.md#create-a-database-account) [Scegliere la chiave di partizione corretta](partitioning-overview.md#choose-partitionkey) dai dati e quindi creare uno spazio delle chiavi e una tabella per archiviare i dati migrati.

1. Prima di eseguire la migrazione dei dati, aumentare la velocità effettiva del contenitore alla quantità necessaria per la migrazione rapida dell'applicazione. Ad esempio, è possibile aumentare la velocità effettiva a 100000 RU. La scalabilità della velocità effettiva prima di avviare la migrazione consente di eseguire la migrazione dei dati in meno tempo. 

   ![Ridimensionare il contenitore Azure Cosmos in tuttoScale Azure Cosmos container throughout](./media/oracle-migrate-cosmos-db-blitzz/scale-throughput.png)

   È necessario ridurre la velocità effettiva al termine della migrazione. In base alla quantità di dati archiviati e ru e RU necessari per ogni operazione, è possibile stimare la velocità effettiva richiesta dopo la migrazione dei dati. Per altre informazioni su come stimare le RU necessarie, vedere Effettuare il [provisioning della velocità effettiva in contenitori e database](set-throughput.md) e Stima RU/s usando gli articoli di Azure [Cosmos DB capacity planner.](estimate-ru-with-capacity-planner.md)

1. Ottenere il punto di contatto, la **porta, il nome utente**e la password **primaria** dell'account Azure Cosmos dal riquadro Stringa di **connessione.** Questi valori verranno utilizzati nel file di configurazione.

1. Dal terminale CLI, impostare la configurazione del database di destinazione. Aprire il file **`vi conf/conn/cosmosdb.yml`** di configurazione utilizzando il comando e aggiungere un elenco separato da virgole di URI host, numero di porta, nome utente, password e altri parametri obbligatori. Di seguito è riportato un esempio di contenuto nel file di configurazione:

   ```bash
   type: COSMOSDB

   host: `<Azure Cosmos account’s Contact point>`
   port: 10350

   username: 'blitzzdemo'
   password: `<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   use-ssl: false
   ```

1. Eseguire quindi la migrazione dei dati utilizzando Blitzz. È possibile eseguire il replicante Blizz in modalità completa o **snapshot:You** can run the Blizz replicant in **full** or snapshot mode:

   * **Modalità completa:** in questa modalità, il replicante continua a essere eseguito dopo la migrazione e rimane in ascolto di eventuali modifiche nel sistema Oracle di origine. Se rileva eventuali modifiche, vengono replicate nell'account Azure Cosmos di destinazione in tempo reale.

   * **Modalità snapshot:** in questa modalità è possibile eseguire la migrazione dello schema e la replica dei dati una tantera. La replica in tempo reale non è supportata con questa opzione.


   Utilizzando le due modalità precedenti, la migrazione può essere eseguita senza tempi di inattività.

1. Per eseguire la migrazione dei dati, dal terminale CLI replicante Blitzz, eseguire il comando seguente:

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing
   ```

   L'interfaccia utente di replica mostra lo stato di avanzamento della replica. Al termine dell'operazione di migrazione e snapshot dello schema, lo stato di avanzamento mostra 100%. Al termine della migrazione, è possibile convalidare i dati nel database di Azure Cosmos di destinazione.

   ![Output di migrazione dei dati Oracle](./media/oracle-migrate-cosmos-db-blitzz/oracle-data-migration-output.png)

1. Poiché per la migrazione è stata utilizzata la modalità completa, è possibile eseguire operazioni quali l'inserimento, l'aggiornamento o l'eliminazione di dati nel database Oracle di origine. Successivamente è possibile verificare che vengano replicati in tempo reale nel database di Azure Cosmos di destinazione. Dopo la migrazione, assicurarsi di ridurre la velocità effettiva configurata per il contenitore Azure Cosmos.After the migration, make sure to decrease the throughput configured for your Azure Cosmos container.

1. È possibile arrestare il replicante in qualsiasi punto e riavviarlo con **l'opzione --resume.** La replica riprende dal punto in cui si è arrestata senza compromettere la coerenza dei dati. Il comando seguente mostra come utilizzare l'opzione resume.

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing --resume
   ```

Per ulteriori informazioni sulla migrazione dei dati alla destinazione, vedere la [demo Blitzz replicant](https://www.youtube.com/watch?v=y5ZeRK5A-MI).

## <a name="next-steps"></a>Passaggi successivi

* [Effettuare il provisioning della velocità effettiva per contenitori e database](set-throughput.md) 
* [Procedure consigliate per le chiavi di partizionePartition key best practices](partitioning-overview.md#choose-partitionkey)
* [Stimare RU/s usando gli](estimate-ru-with-capacity-planner.md) articoli di Azure Cosmos DB capacity planner