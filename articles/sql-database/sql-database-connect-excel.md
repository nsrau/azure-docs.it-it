---
title: Connettere Excel a un database singolo in database SQL di Azure | Microsoft Docs
description: Informazioni su come connettere Microsoft Excel a un database singolo in database SQL di Azure. Importare i dati in Excel per creare report ed esplorare i dati.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: joseidz
ms.author: craigg
ms.reviewer: ''
manager: craigg
ms.date: 02/12/2019
ms.openlocfilehash: c456954c3bf68fd87e51162ea6aab1d0a54b86ad
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237013"
---
# <a name="connect-excel-to-a-single-database-in-azure-sql-database-and-create-a-report"></a>Connettere Excel a un database singolo in database SQL di Azure e creare un report

Connettere Excel a un database singolo in database SQL di Azure e quindi importare dati e creare tabelle e grafici in base ai valori nel database. In questa esercitazione sarà necessario configurare la connessione tra Excel e una tabella di database, salvare il file che archivia i dati e le informazioni di connessione per Excel e quindi creare un grafico pivot dai valori del database.

Prima di iniziare, è necessario avere un database singolo. Se non è stato ancora creato, vedere [Creare un database singolo](sql-database-single-database-get-started.md) e [Creare un firewall IP a livello di server](sql-database-server-level-firewall-rule.md) per avere in pochi minuti un database singolo funzionante con dati di esempio.

Questo articolo descrive come importare i dati di esempio in Excel, ma è anche possibile seguire la procedura con dati personalizzati.

Sarà necessaria anche una copia di Excel. In questa esercitazione viene usato [Microsoft Excel 2016](https://products.office.com/).

## <a name="connect-excel-to-a-sql-database-and-load-data"></a>Connettere Excel a un database SQL e caricare i dati

1. Per connettere Excel a un database SQL, aprire Excel e quindi creare una nuova cartella di lavoro o aprirne una esistente.
2. Sulla barra dei menu nella parte superiore della pagina selezionare la scheda **Dati**, selezionare **Recupera dati**, selezionare Da Azure e quindi selezionare **Dal database SQL di Azure**. 

   ![Selezionare l'origine dati: Connettere Excel al database SQL.](./media/sql-database-connect-excel/excel_data_source.png)

   Si apre la Connessione guidata dati.
3. Nella finestra di dialogo **Connessione al server di database** digitare il **Nome del server** per il database SQL a cui si vuole stabilire la connessione nel formato <*nomeserver*>**.database.windows.net**. Ad esempio, **msftestserver.database.windows.net**. Se si vuole, immettere il nome del database. Scegliere **OK** per aprire la finestra delle credenziali. 

   ![server-name.png](media/sql-database-connect-excel/server-name.png)

4. Nella finestra di dialogo **Database SQL Server** selezionare **Database** sul lato sinistro e quindi immettere il **nome utente** e la **password** per il server di database SQL a cui ci si vuole connettere. Selezionare **Connetti** per aprire lo **Strumento di navigazione**. 

  ![Digitare il nome del server e le credenziali di accesso](./media/sql-database-connect-excel/connect-to-server.png)

  > [!TIP]
  > A seconda dell'ambiente di rete, è possibile che non si riesca a connettersi o che si perda la connessione se il server di database SQL non consente il traffico dall'indirizzo IP client dell'utente. Accedere al [portale di Azure](https://portal.azure.com/), fare clic su SQL Server, fare clic sul server, selezionare il firewall nelle impostazioni e aggiungere l'indirizzo IP del client. Per altre informazioni, vedere [Procedura: Configurare le impostazioni del firewall nel database SQL](sql-database-configure-firewall-settings.md) .

5. Nello **Strumento di navigazione** selezionare il database che si vuole usare dall'elenco, selezionare le tabelle o le viste desiderate (in questo esempio **vGetAllCategories**), quindi selezionare **Carica** per spostare i dati dal database al foglio di calcolo di Excel.

    ![Selezionare un database e una tabella.](./media/sql-database-connect-excel/select-database-and-table.png)

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Importare i dati in Excel e creare un grafico pivot

Ora che è stata stabilita la connessione, è possibile caricare i dati in diversi modi. Ad esempio, la procedura seguente crea un grafico pivot basato sui dati trovati nel database SQL. 

1. Seguire la procedura descritta nella sezione precedente, ma questa volta, invece di selezionare **Carica**, selezionare **Carica in** nell'elenco a discesa **Carica**.
2. Specificare quindi come visualizzare i dati nella cartella di lavoro. In questo caso è stato scelto **Grafico pivot**. È anche possibile scegliere di creare un **Nuovo foglio di lavoro** o **Aggiungi questi dati al modello di dati**. Per altre informazioni sui modelli di dati, vedere [Creare un modello di dati in Excel](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B). 

    ![Scelta del formato per i dati in Excel](./media/sql-database-connect-excel/import-data.png)

    Il foglio di lavoro include ora una tabella e un grafico pivot vuoti.
3. In **Campi tabella pivot**, selezionare tutte le caselle di controllo per i campi da visualizzare.

    ![Configurare un report di database.](./media/sql-database-connect-excel/power-pivot-results.png)

> [!TIP]
> Per connettere altre cartelle di lavoro e altri fogli di lavoro di Excel al database, selezionare la scheda **Dati** e quindi selezionare **Origini recenti** per aprire la finestra di dialogo **Origini recenti**. In questa finestra selezionare la connessione creata dall'elenco e fare clic su **Apri**.
> ![Connessioni recenti](media/sql-database-connect-excel/recent-connections.png)

## <a name="create-a-permanent-connection-using-odc-file"></a>Creare una connessione permanente mediante un file con estensione odc

Per salvare i dettagli della connessione in modo permanente, è possibile creare un file con estensione odc e impostare questa connessione come opzione selezionabile nella finestra di dialogo **Connessioni esistenti**. 

1. Sulla barra dei menu nella parte superiore della pagina selezionare la scheda **Dati** e quindi selezionare **Connessioni esistenti** per aprire la finestra di dialogo **Connessioni esistenti**. 
    1. Selezionare **Sfoglia** per aprire la finestra di dialogo **Seleziona origine dati**.   
    2. Selezionare il file **+NewSqlServerConnection.odc** e quindi scegliere **Apri** per aprire la **Connessione guidata dati**.

    ![Nuova connessione](media/sql-database-connect-excel/new-connection.png)

2. In **Connessione guidata dati** digitare il nome del server e le credenziali del database SQL. Selezionare **Avanti**. 
    1. Nell'elenco a discesa selezionare il database che contiene i dati. 
    2. Selezionare la tabella o la vista desiderata. In questo esempio abbiamo selezionato vGetAllCategories.
    3. Selezionare **Avanti**. 

    ![Connessione guidata dati](media/sql-database-connect-excel/data-connection-wizard.png) 

3. Nella schermata successiva della Connessione guidata dati selezionare il percorso del file, il **nome file** e il **nome descrittivo**. Si può anche scegliere di salvare la password nel file, ma in questo modo si potrebbero esporre i dati ad accessi indesiderati. Al termine scegliere **Fine**. 

    ![Salvataggio della connessione dati](media/sql-database-connect-excel/save-data-connection.png)

4. Selezionare la modalità di importazione dei dati. In questo esempio abbiamo scelto di creare una tabella pivot. È anche possibile modificare le proprietà della connessione scegliendo **Proprietà**. Al termine scegliere **OK**. Se non si è scelto di salvare la password con il file, verrà chiesto di immettere le credenziali. 

    ![Importazione dei dati](media/sql-database-connect-excel/import-data2.png)

5. Verificare che la connessione sia stata salvata espandendo la scheda **Dati** e selezionando **Connessioni esistenti**. 

    ![Connessione esistente](media/sql-database-connect-excel/existing-connection.png)

## <a name="next-steps"></a>Passaggi successivi

* Per query e analisi avanzate, vedere [Connettersi al database SQL con SQL Server Management Studio](sql-database-connect-query-ssms.md) .
* Informazioni sui vantaggi dei [pool elastici](sql-database-elastic-pool.md).
* Informazioni su come [creare un'app Web che si connette al database SQL nel back-end](../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).
