---
title: Importare ed esportare in Database di Azure per MySQL
description: Questo articolo illustra i metodi comuni per importare ed esportare database nel database di Azure per MySQL mediante strumenti come MySQL Workbench.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: f15811cd44e405ee33d9ba3d565c2982e71a80c0
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrare il database MySQL mediante l'importazione ed esportazione
In questo articolo vengono illustrati due approcci comuni per importare ed esportare i dati in un database di Azure per il server MySQL con MySQL Workbench. 

## <a name="before-you-begin"></a>Prima di iniziare
Per proseguire con questa guida, si richiedono:
- Un database di Azure per il server MySQL, seguendo la procedura descritta in [Creare un database di Azure per il server MySQL tramite il portale di Azure](quickstart-create-mysql-server-database-using-azure-portal.md).
- MySQL Workbench [scaricato](https://dev.mysql.com/downloads/workbench/) o un altro strumento di MySQL per l'importazione e l'esportazione.

## <a name="use-common-tools"></a>Usare strumenti comuni
Usare strumenti comuni, come ad esempio MySQL Workbench, Toad o Navicat per connettersi in modalità remota e importare o esportare dati nel database di Azure per MySQL. 

Usare tali strumenti sul computer client con una connessione Internet per connettersi al database di Azure per MySQL. Usare una connessione SSL crittografata per le procedure di sicurezza consigliate, come descritto in [Configurare la connettività SSL nel database di Azure per MySQL](concepts-ssl-connection-security.md).

Durante la migrazione al database di Azure per MySQL non è necessario spostare i file di importazione ed esportazione in alcun percorso cloud speciale. 

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Creare un database nel database di Azure per il server MySQL
Creare un database vuoto nel database di Azure per il server MySQL in cui si vuole eseguire la migrazione dei dati. Usare uno strumento come MySQL Workbench, Toad o Navicat per creare il database. Il database può avere lo stesso nome del database che contiene i dati di dump; in alternativa, è possibile creare un database con un nome diverso.

Per la connessione, individuare le informazioni di connessione nel riquadro **Proprietà** nel database di Azure per MySQL.

![Trovare le informazioni di connessione nel portale di Azure](./media/concepts-migrate-import-export/1_server-properties-name-login.png)

Aggiungere le informazioni di connessione a MySQL Workbench.

![Stringa di connessione MySQL Workbench](./media/concepts-migrate-import-export/2_setup-new-connection.png)

## <a name="determine-when-to-use-import-and-export-techniques-instead-of-a-dump-and-restore"></a>Determinare quando usare le tecniche di importazione ed esportazione anziché un dump e ripristino
Usare gli strumenti di MySQL per importare ed esportare database nel database MySQL di Azure negli scenari seguenti. In altri scenari, è invece possibile trarre vantaggio dall'approccio di [dump e ripristino](concepts-migrate-dump-restore.md). 

- Quando è necessario scegliere in modo selettivo alcune tabelle da importare da un database MySQL esistente nel database MySQL di Azure, è consigliabile usare la tecnica di importazione ed esportazione.  In questo modo, è possibile omettere tutte le tabelle non necessarie dalla migrazione per risparmiare tempo e risorse. Ad esempio, usare l'istruzione `--include-tables` o `--exclude-tables` con [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) e l'istruzione `--tables` con [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Quando si spostano oggetti del database diversi dalle tabelle, crearli in modo esplicito. Includere vincoli (chiave primaria, chiave esterna, indici), viste, funzioni, procedure, trigger e altri oggetti di database per i quali si intende eseguire la migrazione.
- Quando si esegue la migrazione di dati da origini dati esterne diverse da un database MySQL, creare un file flat e importarli usando [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

Verificare che tutte le tabelle nel database usino il motore di archiviazione InnoDB quando si caricano dati nel database di Azure per MySQL. Il database di Azure per MySQL supporta solo il motore di archiviazione InnoDB, pertanto non sono supportati motori di archiviazione alternativi. Se le tabelle richiedono motori di archiviazione alternativi, assicurarsi di convertirli in modo che usino il formato di motore InnoDB prima della migrazione al database di Azure per MySQL. 

Se ad esempio si possiede un'app Web o WordPress che usa il motore MyISAM, convertire prima le tabelle eseguendo la migrazione dei dati in tabelle InnoDB. Eseguire quindi il ripristino al database di Azure per MySQL. Usare la clausola `ENGINE=INNODB` per impostare il motore per la creazione di una tabella e trasferire i dati nella tabella compatibile prima della migrazione. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Consigli relativi alle prestazioni per l'importazione ed esportazione
-   Creare indici cluster e chiavi primarie prima di caricare dati. Caricare i dati nell'ordine delle chiavi primarie. 
-   Ritardare la creazione di indici secondari fino al termine del caricamento dei dati. Creare tutti gli indici secondari dopo il caricamento. 
-   Disabilitare i vincoli di chiave esterna prima del caricamento. La disabilitazione dei controlli della chiave esterna offre miglioramenti significativi delle prestazioni. Abilitare i vincoli e verificare i dati dopo il caricamento per garantire l'integrità referenziale.
-   Caricare i dati in parallelo. Evitare un eccessivo parallelismo che comporterebbe il raggiungimento del limite di risorse e monitorare le risorse con le metriche offerte nel portale di Azure. 
-   Usare le tabelle partizionate quando appropriato.

## <a name="import-and-export-by-using-mysql-workbench"></a>Importare ed esportare con MySQL Workbench
Esistono due modi per esportare e importare dati in MySQL Workbench, ognuno destinato a uno scopo diverso. 

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>Procedure guidate di esportazione e importazione dei dati di una tabella tramite il menu di scelta rapida del visualizzatore oggetti
![Procedure guidate di MySQL Workbench nel menu di scelta rapida del visualizzatore oggetti](./media/concepts-migrate-import-export/p1.png)

Le procedure guidate per i dati delle tabelle supportano le operazioni di importazione ed esportazione tramite i file CSV e JSON. Includono diverse opzioni di configurazione, ad esempio separatori, selezione colonne e selezione di codifica. È possibile eseguire ogni procedura guidata su server MySQL connessi in modalità remota o locale. L'operazione di importazione include tabelle, colonne e mapping dei tipi. 

È possibile accedere a queste procedure guidate dal menu di scelta rapida del visualizzatore oggetti facendo clic con il pulsante destro del mouse su una tabella. Scegliere quindi **Table Data Export Wizard** (Esportazione guidata di tabelle) o **Table Data Import Wizard** (Importazione guidata di tabelle). 

#### <a name="table-data-export-wizard"></a>Esportazione guidata di tabelle
L'esempio seguente illustra l'esportazione di una tabella in un file CSV: 
1. Fare clic con il pulsante destro del mouse sulla tabella del database da esportare. 
2. Selezionare **Table Data Export Wizard** (Esportazione guidata di tabelle). Selezionare le colonne da esportare, l'offset di riga (se presente) e il conteggio (se presente). 
3. Fare clic su **Avanti** nella pagina **Selezionare i dati da esportare**. Selezionare il percorso del file, il tipo di file CSV o JSON. Selezionare anche separatore di riga, metodo di inclusione delle stringhe e separatore di campo. 
4. Fare clic su **Avanti** nella pagina **Select output file location** (Selezionare la posizione del file di output). 
5. Fare clic su **Avanti** nella pagina **Esporta dati**.

#### <a name="table-data-import-wizard"></a>Importazione guidata di tabelle
L'esempio seguente illustra l'importazione di una tabella da un file CSV:
1. Fare clic con il pulsante destro del mouse sulla tabella del database da importare. 
2. Individuare e selezionare il file CSV da importare e quindi fare clic su **Avanti**. 
3. Selezionare la tabella di destinazione (nuova o esistente) e selezionare o deselezionare la casella di controllo **Truncate table before import** (Tronca tabella prima dell'importazione). Fare clic su **Avanti**.
4. Selezionare la codifica e le colonne da importare e quindi fare clic su **Avanti**. 
5. Fare clic su **Avanti** nella pagina **Importa dati**. La procedura guidata importa i dati di conseguenza.

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Esportazione e importazione guidata di dati SQL dal riquadro Strumento di navigazione
Usare una procedura guidata per esportare o importare SQL generati da MySQL Workbench o tramite il comando mysqldump. Accedere a queste procedure guidate dal riquadro **Strumento di navigazione** oppure selezionando **Server** dal menu principale. Selezionare quindi **Esporta dati** o **Importa dati**. 

#### <a name="data-export"></a>Esportazione dati
![Esportazione dati da MySQL Workbench tramite il riquadro Strumento di navigazione](./media/concepts-migrate-import-export/p2.png)

È possibile usare la scheda **Esporta dati** per esportare i dati di MySQL. 
1. Selezionare ogni schema da esportare, se si vuole scegliere tabelle/oggetti specifici dello schema da ogni schema e generare l'esportazione. Le opzioni di configurazione includono l'esportazione in una cartella di progetto o in un file SQL autonomo, eventi e routine di dump archiviati o consentono di ignorare i dati della tabella. 
 
   In alternativa, usare **Export a Result Set** (Esporta un set di risultati) per esportare uno specifico set di risultati nell'editor SQL in un altro formato, come ad esempio CSV, JSON, HTML e XML. 
3. Selezionare gli oggetti del database da esportare e configurare le opzioni correlate.
4. Fare clic su **Aggiorna** per caricare gli oggetti correnti.
5. Se si preferisce, aprire la scheda **Opzioni avanzate** per definire l'operazione di esportazione. Ad esempio, aggiungere blocchi di tabella, usare istruzioni "replace" anziché "insert" e segnalare gli identificatori con l'apice inverso.
6. Fare clic su **Avvia esportazione** per avviare il processo di esportazione.


#### <a name="data-import"></a>Importazione dati
![Importazione di dati da MySQL Workbench tramite Management Navigator](./media/concepts-migrate-import-export/p3.png)

È possibile usare la scheda **Importa dati** per importare o ripristinare i dati esportati tramite l'operazione di esportazione dati o con il comando mysqldump. 
1. Scegliere la cartella di progetto o il file SQL autonomo, scegliere lo schema in cui eseguire l'importazione o scegliere **Nuovo** per definire un nuovo schema. 
2. Fare clic su **Inizia importazione** per avviare il processo di importazione.

## <a name="next-steps"></a>Passaggi successivi
Per un altro approccio di migrazione, vedere [Eseguire la migrazione del database MySQL mediante dump e ripristino nel database di Azure per MySQL](concepts-migrate-dump-restore.md). 
