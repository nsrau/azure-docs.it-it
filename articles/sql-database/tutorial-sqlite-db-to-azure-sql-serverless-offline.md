---
title: 'Esercitazione: come eseguire la migrazione del database SQLite al database SQL di Azure senza server'
description: Informazioni su come eseguire una migrazione offline da SQLite al database SQL di Azure senza server usando Azure Data Factory.
services: sql-database
author: joplum
ms.author: joplum
manager: prda
ms.service: sql-database
ms.workload: data-services
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: c718daa4bc99bffd6fcfeb084299bed6682fe884
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75780509"
---
# <a name="how-to-migrate-your-sqlite-database-to-azure-sql-database-serverless"></a>Come eseguire la migrazione del database SQLite al database SQL di Azure senza server
Per molte persone, SQLite offre la prima esperienza dei database e della programmazione SQL. L'inclusione in molti sistemi operativi e nelle applicazioni più diffuse rende SQLite uno dei motori di database più diffusi e usati nel mondo. Poiché è probabile che il primo motore di database utilizzi molti utenti, spesso può finire come parte centrale dei progetti o delle applicazioni. In casi in cui il progetto o l'applicazione superino l'implementazione iniziale di SQLite, gli sviluppatori potrebbero dover eseguire la migrazione dei dati a un archivio dati affidabile e centralizzato.

Il database SQL di Azure senza server è un livello di calcolo per database singoli che consente di ridimensionare automaticamente le risorse di calcolo in base alla domanda del carico di lavoro e le fatture per la quantità di calcolo usata al secondo. Il livello di calcolo senza server inoltre sospende automaticamente i database durante i periodi di inattività quando viene addebitata solo l'archiviazione e riprende automaticamente i database quando l'attività restituisce.

Dopo aver seguito i passaggi seguenti, verrà eseguita la migrazione del database nel database SQL di Azure senza server, consentendo di rendere disponibile il database per altri utenti o applicazioni nel cloud e di pagare solo per le risorse usate, con modifiche minime al codice dell'applicazione.

## <a name="prerequisites"></a>Prerequisiti
- Una sottoscrizione di Azure
- Database SQLite2 o SQLite3 di cui si vuole eseguire la migrazione
- Ambiente Windows
  - Se non si dispone di un ambiente Windows locale, è possibile usare una macchina virtuale Windows in Azure per la migrazione. Spostare e rendere disponibile il file di database SQLite nella macchina virtuale usando File di Azure e Storage Explorer.

## <a name="steps"></a>Passaggi

1. Effettuare il provisioning di un nuovo database SQL di Azure nel livello di calcolo senza server.

    ![Screenshot di portale di Azure che mostra l'esempio di provisioning per il database SQL di Azure senza server](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/provision-serverless.png)

2. Assicurarsi che il file di database SQLite sia disponibile nell'ambiente Windows. Installare un driver ODBC SQLite se non ne è già disponibile uno (sono disponibili molti in open source, ad esempio http://www.ch-werner.de/sqliteodbc/).

3. Creare un DSN di sistema per il database. Assicurarsi di usare l'applicazione di amministrazione dell'origine dati corrispondente all'architettura di sistema (32 bit rispetto a 64 bit). È possibile trovare la versione in esecuzione nelle impostazioni di sistema.

    - Aprire Amministrazione origine dati ODBC nell'ambiente in uso.
    - Fare clic sulla scheda DSN di sistema e fare clic su "Aggiungi"
    - Selezionare il connettore ODBC SQLite installato e fornire alla connessione un nome significativo, ad esempio sqlitemigrationsource
    - Impostare il nome del database sul file con estensione DB
    - Salvare e uscire.

4. Scaricare e installare il runtime di integrazione self-hosted. Il modo più semplice per eseguire questa operazione è l'opzione di installazione rapida, come descritto in dettaglio nella documentazione di. Se si sceglie un'installazione manuale, è necessario fornire all'applicazione una chiave di autenticazione, che può trovarsi nell'istanza di Data Factory per:

    - Avvio di ADF (autore e monitoraggio dal servizio nel portale di Azure)
    - Fare clic sulla scheda "autore" (matita blu) a sinistra.
    - Fare clic su connessioni (in basso a sinistra), quindi su runtime di integrazione
    - Aggiungere una nuova Integration Runtime self-hosted, assegnarle un nome e selezionare l' *opzione 2*.

5. Creare un nuovo servizio collegato per il database SQLite di origine nel Data Factory.

    ![screenshot che mostra il pannello servizi collegati vuoti in Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create.png)

6. In connessioni, in servizio collegato, fare clic su nuovo

7. Cercare e selezionare il connettore "ODBC"


    ![screenshot che mostra il logo del connettore ODBC nel pannello dei servizi collegati in Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-odbc.png)

8. Assegnare al servizio collegato un nome significativo, ad esempio "sqlite_odbc". Selezionare il runtime di integrazione dall'elenco a discesa "Connetti tramite il runtime di integrazione". Immettere quanto segue nella stringa di connessione, sostituendo la variabile del catalogo iniziale con il FilePath per il file con estensione DB e il DSN con il nome della connessione DSN di sistema: 

    ```
    Connection string: Provider=MSDASQL.1;Persist Security Info=False;Mode=ReadWrite;Initial Catalog=C:\sqlitemigrationsource.db;DSN=sqlitemigrationsource
    ```

9. Impostare il tipo di autenticazione su Anonymous

10. Testare la connessione

    ![screenshot che mostra la connessione riuscita in Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-test-successful.png)

11. Creare un altro servizio collegato per la destinazione SQL senza server. Selezionare il database usando la creazione guidata servizio collegato e specificare le credenziali di autenticazione SQL.

    ![screenshot che mostra il database SQL di Azure selezionato in Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create-target.png)

12. Estrarre le istruzioni CREATE TABLE dal database SQLite. È possibile eseguire questa operazione eseguendo lo script Python seguente nel file di database.

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

13. Creare le tabelle di destinazione nell'ambiente di destinazione SQL senza server copiando le istruzioni CREATE TABLE dal file CreateTables. SQL ed eseguendo le istruzioni SQL nell'editor di query nel portale di Azure.

14. Tornare alla schermata iniziale del Data Factory e fare clic su "Copia dati" per eseguire la procedura guidata di creazione del processo.

    ![screenshot che illustra il logo della procedura guidata Copia dati in Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/copy-data.png)

15. Selezionare tutte le tabelle dal database SQLite di origine usando le caselle di controllo ed eseguirne il mapping alle tabelle di destinazione in Azure SQL. Una volta eseguito il processo, la migrazione dei dati da SQLite ad Azure SQL è stata completata.

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere [Guida introduttiva: creare un database singolo nel database SQL di Azure usando il portale di Azure](sql-database-single-database-get-started.md).
- Per i limiti delle risorse, vedere [Limiti delle risorse del livello di calcolo serverless](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).
