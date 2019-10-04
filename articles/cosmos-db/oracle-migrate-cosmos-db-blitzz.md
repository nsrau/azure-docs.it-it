---
title: Eseguire la migrazione dei dati da Oracle a Azure Cosmos DB API Cassandra mediante blitzz
description: Informazioni su come eseguire la migrazione dei dati da un database Oracle a Azure Cosmos DB API Cassandra con blitzz.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: fe132ee6ab90fdae99463f11ecf46f352690b810
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69984355"
---
# <a name="migrate-data-from-oracle-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>Eseguire la migrazione dei dati da Oracle a Azure Cosmos DB account di API Cassandra tramite blitzz

API Cassandra in Azure Cosmos DB è diventata un'ottima scelta per i carichi di lavoro aziendali in esecuzione in Oracle per diversi motivi, ad esempio:

* **Maggiore scalabilità e disponibilità:** Elimina i singoli punti di errore, una migliore scalabilità e la disponibilità per le applicazioni.

* **Risparmi significativi sui costi:** È possibile risparmiare sui costi con Azure Cosmos DB, che include il costo di macchine virtuali, larghezza di banda ed eventuali licenze Oracle applicabili. Inoltre, non è necessario gestire i Data Center, i server, l'archiviazione SSD, la rete e i costi elettrici.

* **Nessun sovraccarico di gestione e monitoraggio:** Come servizio cloud completamente gestito, Azure Cosmos DB Elimina il sovraccarico di gestione e monitoraggio di una miriade di impostazioni.

Esistono diversi modi per eseguire la migrazione dei carichi di lavoro del database da una piattaforma a un'altra. [Blitzz](https://www.blitzz.io) è uno strumento che offre un modo sicuro e affidabile per eseguire la migrazione senza tempi di inattività da diversi database a Azure Cosmos DB. Questo articolo descrive i passaggi necessari per eseguire la migrazione dei dati dal database Oracle al Azure Cosmos DB API Cassandra con blitzz.

## <a name="benefits-using-blitzz-for-migration"></a>Vantaggi dell'uso di blitzz per la migrazione

La soluzione di migrazione di blitzz segue un approccio dettagliato per la migrazione di carichi di lavoro operativi complessi. Di seguito sono riportati alcuni aspetti chiave del piano di migrazione con zero inattività di blitzz:

* Offre la migrazione automatica della logica di business (tabelle, indici, viste) dal database Oracle al Azure Cosmos DB. Non è necessario creare schemi manualmente.

* Blitzz offre la replica di database con volumi elevati e paralleli. Consente di sincronizzare le piattaforme di origine e di destinazione durante la migrazione utilizzando una tecnica denominata Change-Data-Capture (CDC). Utilizzando CDC, blitzz esegue continuamente il pull di un flusso di modifiche dal database di origine (Oracle) e lo applica al database di destinazione (Azure Cosmos DB).

* È a tolleranza di errore e garantisce esattamente una volta il recapito dei dati anche durante un errore hardware o software nel sistema.

* Protegge i dati durante il transito usando un'ampia gamma di metodologie di sicurezza, ad esempio SSL, crittografia.

* Offre servizi per la conversione di logica di business complessa scritta in PL/SQL alla logica di business equivalente in Azure Cosmos DB.

## <a name="steps-to-migrate-data"></a>Passaggi per la migrazione dei dati

In questa sezione vengono descritti i passaggi necessari per configurare blitzz ed eseguire la migrazione dei dati dal database Oracle al Azure Cosmos DB.

1. Dal computer in cui si intende installare il replicante Blitzz, aggiungere un certificato di sicurezza. Questo certificato è richiesto dal replicante blitzz per stabilire una connessione SSL con l'account di Azure Cosmos DB specificato. È possibile aggiungere il certificato con i passaggi seguenti:

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. ou può ottenere l'installazione di blitzz e i file binari richiedendo una demo nel [sito Web blitzz](https://www.blitzz.io). In alternativa, è anche possibile inviare un [messaggio di posta elettronica](mailto:success@blitzz.io) al team.

   ![Download dello strumento blitzz replicante](./media/oracle-migrate-cosmos-db-blitzz/blitzz-replicant-download.png)

   ![File replicante di blitzz](./media/oracle-migrate-cosmos-db-blitzz/replicant-files.png)

1. Dal terminale dell'interfaccia della riga di comando configurare la configurazione del database di origine. Aprire il file di configurazione **`vi conf/conn/oracle.yml`** usando il comando e aggiungere un elenco delimitato da virgole degli indirizzi IP dei nodi Oracle, il numero di porta, il nome utente, la password e tutti gli altri dettagli necessari. Il codice seguente mostra un esempio di file di configurazione:

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

   ![Apri editor connessione Oracle](./media/oracle-migrate-cosmos-db-blitzz/open-connection-editor-oracle.png)

   ![Configurazione della connessione Oracle](./media/oracle-migrate-cosmos-db-blitzz/oracle-connection-configuration.png)

   Dopo aver compilato i dettagli di configurazione, salvare e chiudere il file.

1. Facoltativamente, è possibile configurare il file di filtro del database di origine. Il file di filtro specifica gli schemi o le tabelle di cui eseguire la migrazione. Aprire il file di configurazione **`vi filter/oracle_filter.yml`** usando il comando e immettere i dettagli di configurazione seguenti:

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```
 
   Dopo aver compilato i dettagli del filtro del database, salvare e chiudere il file.

1. Successivamente verrà configurata la configurazione del database di destinazione. Prima di definire la configurazione, [creare un account di API Cassandra Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account). [Scegliere la chiave di partizione corretta](partitioning-overview.md#choose-partitionkey) dai dati, quindi creare uno spazio di chiavi e una tabella per archiviare i dati migrati.

1. Prima di eseguire la migrazione dei dati, aumentare la velocità effettiva del contenitore fino alla quantità necessaria per la migrazione rapida dell'applicazione. Ad esempio, è possibile aumentare la velocità effettiva a 100000 ur. Il ridimensionamento della velocità effettiva prima di avviare la migrazione consentirà di eseguire la migrazione dei dati in minor tempo. 

   ![Ridimensionare Azure Cosmos container in tutto](./media/oracle-migrate-cosmos-db-blitzz/scale-throughput.png)

   È necessario ridurre la velocità effettiva al termine della migrazione. In base alla quantità di dati archiviati e ur necessari per ogni operazione, è possibile stimare la velocità effettiva necessaria dopo la migrazione dei dati. Per altre informazioni su come stimare le UR richieste, vedere provisioning della [velocità effettiva su contenitori e database](set-throughput.md) e [stimare ur/sec usando gli articoli Azure Cosmos DB Capacity Planner](estimate-ru-with-capacity-planner.md) .

1. Ottenere il **punto di contatto, la porta, il nome utente**e la **password primaria** dell'account Azure Cosmos dal riquadro della **stringa di connessione** . Questi valori vengono utilizzati nel file di configurazione.

1. Dal terminale dell'interfaccia della riga di comando configurare la configurazione del database di destinazione. Aprire il file di configurazione **`vi conf/conn/cosmosdb.yml`** usando il comando e aggiungere un elenco delimitato da virgole di Uri host, numero di porta, nome utente, password e altri parametri obbligatori. Di seguito è riportato un esempio di contenuto nel file di configurazione:

   ```bash
   type: COSMOSDB

   host: `<Azure Cosmos account’s Contact point>`
   port: 10350

   username: 'blitzzdemo'
   password: `<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   use-ssl: false
   ```

1. Eseguire quindi la migrazione dei dati usando blitzz. È possibile eseguire il replicante di Blizz in modalità **completa** o **snapshot** :

   * **Modalità completa** : in questa modalità, il replicante continua a funzionare dopo la migrazione e resta in attesa di eventuali modifiche nel sistema Oracle di origine. Se vengono rilevate modifiche, vengono replicate nell'account Azure Cosmos di destinazione in tempo reale.

   * **Modalità snapshot** : in questa modalità è possibile eseguire la migrazione dello schema e la replica dei dati una sola volta. La replica in tempo reale non è supportata con questa opzione.


   Usando le due modalità precedenti, la migrazione può essere eseguita senza tempi di inattività.

1. Per eseguire la migrazione dei dati, eseguire il comando seguente dal terminale dell'interfaccia della riga di comando blitzz replicante:

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing
   ```

   L'interfaccia utente di replicante Mostra lo stato della replica. Una volta eseguita la migrazione dello schema e l'operazione snapshot, lo stato di avanzamento indica il 100%. Al termine della migrazione, è possibile convalidare i dati nel database Azure Cosmos di destinazione.

   ![Output migrazione dati Oracle](./media/oracle-migrate-cosmos-db-blitzz/oracle-data-migration-output.png)

1. Poiché è stata usata la modalità completa per la migrazione, è possibile eseguire operazioni quali l'inserimento, l'aggiornamento o l'eliminazione di dati nel database Oracle di origine. In seguito è possibile verificare che vengano replicate in tempo reale nel database di Azure Cosmos di destinazione. Dopo la migrazione, assicurarsi di diminuire la velocità effettiva configurata per il contenitore di Azure Cosmos.

1. È possibile arrestare il replicante in qualsiasi punto e riavviarlo con l'opzione **--Resume** . La replica riprende dal punto in cui è stata arrestata senza compromettere la coerenza dei dati. Il comando seguente mostra come usare l'opzione Resume.

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing --resume
   ```

Per altre informazioni sulla migrazione dei dati alla destinazione, migrazione in tempo reale, vedere la [demo di blitzz replicante](https://www.youtube.com/watch?v=y5ZeRK5A-MI).

## <a name="next-steps"></a>Passaggi successivi

* [Effettuare il provisioning della velocità effettiva per contenitori e database](set-throughput.md) 
* [Procedure consigliate per le chiavi di partizione](partitioning-overview.md#choose-partitionkey)
* [Stimare ur/sec usando gli articoli Azure Cosmos DB Capacity Planner](estimate-ru-with-capacity-planner.md)