---
title: 'Esercitazione: Come eseguire la migrazione di un database SQLite al database SQL di Azure serverless'
description: Informazioni su come eseguire una migrazione offline da SQLite al database SQL di Azure serverless usando Azure Data Factory.
services: sql-database
author: joplum
ms.author: joplum
ms.service: sql-database
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/08/2020
ms.custom: sqldbrb=1
ms.openlocfilehash: 6e60403344a0341b4aee74a001287c09ba67e114
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448902"
---
# <a name="how-to-migrate-your-sqlite-database-to-azure-sql-database-serverless"></a>Come eseguire la migrazione di un database SQLite al database SQL di Azure serverless
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

SQLite rappresenta per molte persone la prima esperienza di database e programmazione SQL. Incluso in molti sistemi operativi e nelle applicazioni più diffuse, SQLite è uno dei motori di database più ampiamente diffusi e usati nel mondo. Ed essendo probabilmente il primo motore di database usato da molte persone, spesso diventa una parte centrale di progetti o applicazioni. Nei casi in cui il progetto o l'applicazione supera l'implementazione iniziale di SQLite, gli sviluppatori potrebbero dover eseguire la migrazione dei dati in un archivio dati affidabile e centralizzato.

Database SQL di Azure serverless è un livello di calcolo per database singoli che ridimensiona automaticamente le risorse di calcolo in base alle esigenze dei carichi di lavoro, addebitando la quantità di risorse di calcolo usata al secondo. Il livello di calcolo serverless inoltre sospende automaticamente i database durante i periodi di inattività, in cui viene addebitata solo l'archiviazione, e li riprende automaticamente quando necessario.

Dopo aver completato i passaggi seguenti, verrà eseguita la migrazione del database in Database SQL di Azure serverless, consentendo di rendere disponibile il database per altri utenti o applicazioni nel cloud e di pagare solo per le risorse usate, con modifiche minime al codice dell'applicazione.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure
- Il database SQLite2 o SQLite3 di cui si vuole eseguire la migrazione
- Un ambiente Windows
  - Se non si ha un ambiente Windows locale, è possibile usare una macchina virtuale Windows in Azure per eseguire la migrazione. Spostare il file di database SQLite e renderlo disponibile nella VM usando File di Azure e Storage Explorer.

## <a name="steps"></a>Passaggi

1. Effettuare il provisioning di un nuovo database SQL di Azure nel livello di calcolo serverless.

    ![Screenshot del portale di Azure che mostra un esempio di provisioning di Database SQL di Azure serverless](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/provision-serverless.png)

2. Verificare che il file di database SQLite sia disponibile nell'ambiente Windows. Se non ne è già disponibile uno, installare un driver ODBC SQLite (ne sono disponibili molti in open source, ad esempio http://www.ch-werner.de/sqliteodbc/).

3. Creare un DSN di sistema per il database. Assicurarsi di usare l'applicazione Amministrazione origine dati ODBC corrispondente all'architettura del sistema (32 bit o 64 bit). È possibile verificare la versione in esecuzione nelle impostazioni di sistema.

    - Aprire Amministrazione origine dati ODBC nell'ambiente.
    - Fare clic sulla scheda DSN di sistema e quindi su "Aggiungi".
    - Selezionare il connettore ODBC SQLite installato e assegnare alla connessione un nome significativo, ad esempio sqlitemigrationsource.
    - Impostare il nome del database sul file con estensione db.
    - Salvare e uscire.

4. Scaricare e installare il runtime di integrazione self-hosted. Il modo più facile per farlo è tramite l'opzione di installazione rapida, come descritto in dettaglio nella documentazione. Se si opta per un'installazione manuale, è necessario fornire all'applicazione una chiave di autenticazione, che può essere trovata nell'istanza di Data Factory nel modo seguente:

    - Avviare Azure Data Factory (Autore e Monitoraggio dal servizio nel portale di Azure)
    - Fare clic sulla scheda "Autore" (matita blu) a sinistra.
    - Fare clic su Connessioni (in basso a sinistra) e quindi su Runtime di integrazione.
    - Aggiungere un nuovo runtime di integrazione self-hosted, assegnargli un nome e selezionare *Opzione 2*.

5. Creare un nuovo servizio collegato per il database SQLite di origine nel data factory.

    ![Screenshot che mostra il pannello Servizi collegati vuoto in Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-create.png)

6. In **Connessioni** fare clic su **Nuovo** in **Servizio collegato**.

7. Cercare e selezionare il connettore "ODBC".

   ![Screenshot che mostra il logo del connettore ODBC in Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-odbc.png)

8. Assegnare al servizio collegato un nome significativo, ad esempio "sqlite_odbc". Selezionare il runtime di integrazione dall'elenco a discesa "Connect via integration runtime" (Connetti tramite il runtime di integrazione). Immettere il codice seguente nella stringa di connessione, sostituendo la variabile Initial Catalog con il percorso del file con estensione db e la variabile DSN con il nome della connessione DSN di sistema:

   ```
   Connection string: Provider=MSDASQL.1;Persist Security Info=False;Mode=ReadWrite;Initial Catalog=C:\sqlitemigrationsource.db;DSN=sqlitemigrationsource
    ```

9. Impostare il tipo di autenticazione su Anonima.

10. Testare la connessione

    ![Screenshot che mostra la connessione riuscita in Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-test-successful.png)

11. Creare un altro servizio collegato per la destinazione SQL serverless. Selezionare il database usando la procedura guidata per la creazione del servizio collegato e specificare le credenziali di autenticazione SQL.

    ![Screenshot che mostra il database SQL di Azure selezionato in Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-create-target.png)

12. Estrarre le istruzioni CREATE TABLE dal database SQLite eseguendo lo script Python seguente sul file di database.

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

13. Creare le tabelle di destinazione nell'ambiente di destinazione SQL serverless copiando le istruzioni CREATE TABLE dal file CreateTables.sql ed eseguendo le istruzioni SQL nell'editor di query nel portale di Azure.

14. Tornare alla schermata iniziale di Data Factory e fare clic su "Copia dati" per eseguire la procedura guidata di creazione del processo.

    ![Screenshot che mostra il logo della procedura guidata Copia dati in Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/copy-data.png)

15. Selezionare tutte le tabelle del database SQLite di origine usando le caselle di controllo ed eseguirne il mapping alle tabelle di destinazione in Azure SQL. Al termine dell'esecuzione del processo, la migrazione dei dati da SQLite ad Azure SQL è completata.

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere [Avvio rapido: Creare un database singolo nel database SQL di Azure usando il portale di Azure](single-database-create-quickstart.md).
- Per i limiti delle risorse, vedere [Limiti delle risorse del livello di calcolo serverless](../../sql-database/sql-database-vcore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).
