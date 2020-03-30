---
title: 'Esercitazione: Come eseguire la migrazione del database SQLite al server di database SQL di AzureTutorial: How to migrate your SQLite database to Azure SQL Database Serverless'
description: Informazioni su come eseguire una migrazione offline da SQLite ad Azure SQL Database Serverless tramite Azure Data Factory.Learn to perform an offline migration from SQLite to Azure SQL Database Serverless by using Azure Data Factory.
services: sql-database
author: joplum
ms.author: joplum
manager: prda
ms.service: sql-database
ms.workload: data-services
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: c718daa4bc99bffd6fcfeb084299bed6682fe884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75780509"
---
# <a name="how-to-migrate-your-sqlite-database-to-azure-sql-database-serverless"></a>Come eseguire la migrazione del database SQLite al server di database SQL di AzureHow to migrate your SQLite database to Azure SQL DatabaseServerless
Per molte persone, SQLite offre la prima esperienza di database e programmazione SQL. E 'l'inclusione in molti sistemi operativi e applicazioni popolari rende SQLite uno dei motori di database più ampiamente distribuiti e utilizzati nel mondo. E poiché è probabile che il primo motore di database molte persone usano, spesso può finire come una parte centrale di progetti o applicazioni. In questi casi in cui il progetto o l'applicazione supera l'implementazione iniziale di SQLite, gli sviluppatori potrebbero dover eseguire la migrazione dei dati in un archivio dati affidabile e centralizzato.

Il server del database SQL di Azure è un livello di calcolo per singoli database che ridimensiona automaticamente il calcolo in base alla domanda del carico di lavoro e le fatture per la quantità di calcolo usata al secondo. Il livello di calcolo senza server sospende automaticamente i database durante i periodi di inattività quando viene fatturata solo l'archiviazione e riprende automaticamente i database al reso in corso.

Dopo aver eseguito i passaggi seguenti, verrà eseguita la migrazione del database in Azure SQL Database Serverless, consentendo di rendere il database disponibile ad altri utenti o applicazioni nel cloud e pagare solo per ciò che si usa, con modifiche minime al codice dell'applicazione.

## <a name="prerequisites"></a>Prerequisiti
- Una sottoscrizione di AzureAn Azure Subscription
- Database SQLite2 o SQLite3 di cui si desidera eseguire la migrazione
- Un ambiente Windows
  - Se non si dispone di un ambiente Windows locale, è possibile usare una macchina virtuale Windows in Azure per la migrazione. Spostare e rendere il file di database SQLite disponibile nella macchina virtuale usando File di Azure ed Esplora archivi.

## <a name="steps"></a>Passaggi

1. Effettuare il provisioning di un nuovo database SQL di Azure nel livello di calcolo Senza server.

    ![screenshot del portale di Azure che mostra l'esempio di provisioning per azure sql database serverless](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/provision-serverless.png)

2. Assicurarsi di avere il file di database SQLite disponibile nell'ambiente Windows. Installare un driver ODBC SQLite se non si dispone già di uno http://www.ch-werner.de/sqliteodbc/)(sono presenti molti disponibili in Open Source, ad esempio, .

3. Creare un DSN di sistema per il database. Assicurarsi di utilizzare l'applicazione Amministratore origine dati che corrisponde all'architettura del sistema (32 bit e 64 bit). È possibile trovare la versione in esecuzione nelle impostazioni di sistema.

    - Aprire Amministratore origine dati ODBC nell'ambiente.
    - Fare clic sulla scheda DSN di sistema e fare clic su "Aggiungi"
    - Selezionare il connettore ODBC SQLite installato e assegnare alla connessione un nome significativo, ad esempio sqlitemigrationsource
    - Impostare il nome del database sul file con estensione db
    - Salvare e uscire.

4. Scaricare e installare il runtime di integrazione self-hosted. Il modo più semplice per eseguire questa operazione è l'opzione di installazione rapida, come descritto nella documentazione. Se si opta per un'installazione manuale, sarà necessario fornire all'applicazione una chiave di autenticazione, che può trovarsi nell'istanza di Data Factory:

    - Avvio di ADF (Author e Monitor dal servizio nel portale di Azure)Starting up ADF (Author and Monitor from the service in the Azure portal)
    - Fai clic sulla scheda "Autore" (matita blu) a sinistra
    - Fare clic su Connessioni (in basso a sinistra), quindi su Runtime di integrazione
    - Aggiungere il nuovo runtime di integrazione self-hosted, assegnargli un nome, selezionare *l'opzione 2*.

5. Creare un nuovo servizio collegato per il database SQLite di origine in Data Factory.Create a new linked service for the source SQLite database in your Data Factory.

    ![Schermata che mostra il pannello dei servizi collegati vuoti in Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create.png)

6. In Connessioni, in Servizio collegato, fare clic su Nuovo

7. Cercare e selezionare il connettore "ODBC"


    ![Schermata che mostra il logo del connettore ODBC nel pannello dei servizi collegati in Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-odbc.png)

8. Assegnare al servizio collegato un nome significativo, ad esempio "sqlite_odbc". Selezionare il runtime di integrazione dall'elenco a discesa "Connetti tramite runtime di integrazione". Immettere quanto segue nella stringa di connessione, sostituendo la variabile Initial Catalog con il percorso del file .db e il DSN con il nome della connessione DSN di sistema: 

    ```
    Connection string: Provider=MSDASQL.1;Persist Security Info=False;Mode=ReadWrite;Initial Catalog=C:\sqlitemigrationsource.db;DSN=sqlitemigrationsource
    ```

9. Impostare il tipo di autenticazione su Anonimo

10. Testare la connessione

    ![Schermata che mostra la connessione corretta in Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-test-successful.png)

11. Creare un altro servizio collegato per la destinazione SQL senza server. Selezionare il database utilizzando la procedura guidata del servizio collegato e fornire le credenziali di autenticazione SQL.

    ![Schermata che mostra il database SQL di Azure selezionato in Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create-target.png)

12. Estrarre le istruzioni CREATE TABLE dal database SQLite. È possibile farlo eseguendo lo script Python seguente sul file di database.

    ```
    #!/usr/bin/python
    import sqlite3
    conn = sqlite3.connect("sqlitemigrationsource.db")
    c = conn.cursor()

    print("Starting extract job..")
    with open('CreateTables.sql', 'w') as f:
        for tabledetails in c.execute("SELECT * FROM sqlite_master WHERE type='table'"):
            print("Extracting CREATE statement for " + (str(tabledetails[1])))
            print(tabledetails)
            f.write(str(tabledetails[4].replace('\n','') + ';\n'))
    c.close()
    ```

13. Creare le tabelle di destinazione nell'ambiente di destinazione SQL senza server copiando le istruzioni create table dal file CreateTables.sql ed eseguendo le istruzioni SQL nell'editor di query nel portale di Azure.Create the landing tables in your Serverless SQL target environment by copying the CREATE table statements from the CreateTables.sql file and running the SQL statements in the Query Editor in the Azure portal.

14. Tornare alla schermata iniziale di Data Factory e fare clic su "Copia dati" per eseguire la procedura guidata di creazione del processo.

    ![Schermata che mostra il logo della procedura guidata Copia dati in Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/copy-data.png)

15. Selezionare tutte le tabelle dal database SQLite di origine usando le caselle di controllo ed eseguirne il mapping alle tabelle di destinazione in SQL di Azure.Select all tables from the source SQLite database using the check boxes, and map them to the target tables in Azure SQL. Dopo l'esecuzione del processo, è stata eseguita correttamente la migrazione dei dati da SQLite ad Azure SQL.Once the job has run, you have successfully migrated your data from SQLite to Azure SQL!

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere Guida introduttiva: Creare un singolo database nel database SQL di Azure usando il portale di Azure.To get started, see [Quickstart: Create a single database in Azure SQL Database using the Azure portal](sql-database-single-database-get-started.md).
- Per i limiti delle risorse, vedere [Limiti delle risorse del livello di calcolo serverless](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).
