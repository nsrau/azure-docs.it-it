---
title: Importazione ed esportazione-database di Azure per MySQL
description: Questo articolo illustra i metodi comuni per importare ed esportare database nel database di Azure per MySQL mediante strumenti come MySQL Workbench.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 10/30/2020
ms.openlocfilehash: 1b4959cbf082a589c90034f48d597907c9b7e6cc
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128930"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrare il database MySQL mediante l'importazione ed esportazione
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]
In questo articolo vengono illustrati due approcci comuni per importare ed esportare i dati in un database di Azure per il server MySQL con MySQL Workbench.

È anche possibile fare riferimento alla [Guida alla migrazione del database](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide) per informazioni dettagliate e casi d'uso sulla migrazione di database al database di Azure per MySQL. Questa guida fornisce indicazioni che comporteranno la corretta pianificazione e l'esecuzione di una migrazione di MySQL in Azure.

## <a name="before-you-begin"></a>Prima di iniziare
Per proseguire con questa guida, si richiedono:
- Un database di Azure per il server MySQL, seguendo la procedura descritta in [Creare un database di Azure per il server MySQL tramite il portale di Azure](quickstart-create-mysql-server-database-using-azure-portal.md).
- [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) o un altro strumento MySQL di terze parti per eseguire l'importazione/esportazione.

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Creare un database nel database di Azure per il server MySQL
Creare un database vuoto nel database di Azure per il server MySQL tramite MySQL Workbench, Toad o Navicat per creare il database. Il database può avere lo stesso nome del database che contiene i dati di dump; in alternativa, è possibile creare un database con un nome diverso.

Per la connessione, individuare le informazioni di connessione nella pagina **Panoramica** del database di Azure per MySQL.

:::image type="content" source="./media/concepts-migrate-import-export/1_server-overview-name-login.png" alt-text="Trovare le informazioni di connessione nel portale di Azure":::

Aggiungere le informazioni di connessione a MySQL Workbench.

:::image type="content" source="./media/concepts-migrate-import-export/2_setup-new-connection.png" alt-text="Trovare le informazioni di connessione nel portale di Azure":::

## <a name="determine-when-to-use-import-and-export-techniques"></a>Determinare quando utilizzare le tecniche di importazione ed esportazione

> [!TIP]
> Per gli scenari in cui si desidera eseguire il dump e il ripristino dell'intero database, è necessario utilizzare invece l'approccio [dump e Restore](concepts-migrate-dump-restore.md) .

Usare gli strumenti di MySQL per importare ed esportare database nel database MySQL di Azure negli scenari seguenti.

- Quando è necessario scegliere in modo selettivo alcune tabelle da importare da un database MySQL esistente nel database MySQL di Azure, è consigliabile usare la tecnica di importazione ed esportazione.  In questo modo, è possibile omettere tutte le tabelle non necessarie dalla migrazione per risparmiare tempo e risorse. Ad esempio, usare l'istruzione `--include-tables` o `--exclude-tables` con [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) e l'istruzione `--tables` con [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Quando si spostano oggetti del database diversi dalle tabelle, crearli in modo esplicito. Includere vincoli (chiave primaria, chiave esterna, indici), viste, funzioni, procedure, trigger e altri oggetti di database per i quali si intende eseguire la migrazione.
- Quando si esegue la migrazione di dati da origini dati esterne diverse da un database MySQL, creare un file flat e importarli usando [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

> [!Important]
> Sia il server singolo che il server flessibile supportano **solo il motore di archiviazione InnoDB** . Verificare che tutte le tabelle nel database usino il motore di archiviazione InnoDB quando si caricano dati nel database di Azure per MySQL.
> Se il database di origine usa un altro motore di archiviazione, convertirlo nel motore InnoDB prima di eseguire la migrazione del database. Se ad esempio si possiede un'app Web o WordPress che usa il motore MyISAM, convertire prima le tabelle eseguendo la migrazione dei dati in tabelle InnoDB. Usare la clausola `ENGINE=INNODB` per impostare il motore per la creazione di una tabella e trasferire i dati nella tabella compatibile prima della migrazione.

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

> [!NOTE]
> Se si aggiunge una connessione a un server MySQL singolo o flessibile (anteprima) in MySQL Workbench, verificare che:
> - Per MySQL Single Server, il nome utente deve avere il formato ' username@servername '
> - Per MySQL flexible server è possibile usare solo ' username ' se si usa ' username@servername ' per la connessione, la connessione avrà esito negativo.

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>Procedure guidate di esportazione e importazione dei dati di una tabella tramite il menu di scelta rapida del visualizzatore oggetti
:::image type="content" source="./media/concepts-migrate-import-export/p1.png" alt-text="Trovare le informazioni di connessione nel portale di Azure":::

Le procedure guidate per i dati delle tabelle supportano le operazioni di importazione ed esportazione tramite i file CSV e JSON. Includono diverse opzioni di configurazione, ad esempio separatori, selezione colonne e selezione di codifica. È possibile eseguire ogni procedura guidata su server MySQL connessi in modalità remota o locale. L'operazione di importazione include tabelle, colonne e mapping dei tipi.

È possibile accedere a queste procedure guidate dal menu di scelta rapida del visualizzatore oggetti facendo clic con il pulsante destro del mouse su una tabella. Scegliere quindi **Table Data Export Wizard** (Esportazione guidata di tabelle) o **Table Data Import Wizard** (Importazione guidata di tabelle).

#### <a name="table-data-export-wizard"></a>Esportazione guidata di tabelle
L'esempio seguente illustra l'esportazione di una tabella in un file CSV:
1. Fare clic con il pulsante destro del mouse sulla tabella del database da esportare.
2. Selezionare **Table Data Export Wizard** (Esportazione guidata di tabelle). Selezionare le colonne da esportare, l'offset di riga (se presente) e il conteggio (se presente).
3. Fare clic su **Avanti** nella pagina **Selezionare i dati da esportare** . Selezionare il percorso del file, il tipo di file CSV o JSON. Selezionare anche separatore di riga, metodo di inclusione delle stringhe e separatore di campo.
4. Fare clic su **Avanti** nella pagina **Select output file location** (Selezionare la posizione del file di output).
5. Fare clic su **Avanti** nella pagina **Esporta dati** .

#### <a name="table-data-import-wizard"></a>Importazione guidata di tabelle
L'esempio seguente illustra l'importazione di una tabella da un file CSV:
1. Fare clic con il pulsante destro del mouse sulla tabella del database da importare.
2. Individuare e selezionare il file CSV da importare e quindi fare clic su **Avanti** .
3. Selezionare la tabella di destinazione (nuova o esistente) e selezionare o deselezionare la casella di controllo **Truncate table before import** (Tronca tabella prima dell'importazione). Fare clic su **Avanti** .
4. Selezionare la codifica e le colonne da importare e quindi fare clic su **Avanti** .
5. Fare clic su **Avanti** nella pagina **Importa dati** . La procedura guidata importa i dati di conseguenza.

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Esportazione e importazione guidata di dati SQL dal riquadro Strumento di navigazione
Usare una procedura guidata per esportare o importare SQL generati da MySQL Workbench o tramite il comando mysqldump. Accedere a queste procedure guidate dal riquadro **Strumento di navigazione** oppure selezionando **Server** dal menu principale. Selezionare quindi **Esporta dati** o **Importa dati** .

#### <a name="data-export"></a>Esportazione dati
:::image type="content" source="./media/concepts-migrate-import-export/p2.png" alt-text="Trovare le informazioni di connessione nel portale di Azure" e segnalare gli identificatori con l'apice inverso.
6. Fare clic su **Avvia esportazione** per avviare il processo di esportazione.


#### <a name="data-import"></a>Importazione dati
:::image type="content" source="./media/concepts-migrate-import-export/p3.png" alt-text="Trovare le informazioni di connessione nel portale di Azure":::

È possibile usare la scheda **Importa dati** per importare o ripristinare i dati esportati tramite l'operazione di esportazione dati o con il comando mysqldump.
1. Scegliere la cartella di progetto o il file SQL autonomo, scegliere lo schema in cui eseguire l'importazione o scegliere **Nuovo** per definire un nuovo schema.
2. Fare clic su **Inizia importazione** per avviare il processo di importazione.

## <a name="next-steps"></a>Passaggi successivi
- Per un altro approccio di migrazione, vedere [Eseguire la migrazione del database MySQL mediante dump e ripristino nel database di Azure per MySQL](concepts-migrate-dump-restore.md).
- Per altre informazioni sulla migrazione dei database in Database di Azure per MySQL, vedere [Database Migration Guide](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide) (Guida alla migrazione di database).