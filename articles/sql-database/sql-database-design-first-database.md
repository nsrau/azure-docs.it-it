---
title: 'Esercitazione: Progettare il primo database SQL di Azure con SSMS | Microsoft Docs'
description: Informazioni su come progettare il primo database SQL di Azure con SQL Server Management Studio.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 9fa36b9b87a8e9591b0c863826cd2278a29ba28e
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956058"
---
# <a name="tutorial-design-your-first-azure-sql-database-using-ssms"></a>Esercitazione: Progettare il primo database SQL di Azure con SSMS

Il database SQL di Azure è un database relazionale distribuito come servizio in Microsoft Cloud (Azure). Questa esercitazione illustra come usare il portale di Azure e [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) per le operazioni seguenti:

> [!div class="checklist"]
> * Creare un database nel portale di Azure*
> * Impostare una regola del firewall a livello di server nel portale di Azure
> * Connettersi al database con SSMS
> * Creare tabelle con SQL Server Management Studio
> * Eseguire il caricamento bulk dei dati con BCP
> * Eseguire query sui dati con SSMS

*Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

> [!NOTE]
> Ai fini di questa esercitazione verrà usato il [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md), ma è possibile scegliere il [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, assicurarsi di aver installato quanto segue:

- [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (ultima versione)
- [BCP e SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433) (ultima versione)

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-a-blank-database"></a>Creazione di un database vuoto

Un database SQL di Azure viene creato con un set definito di [risorse di calcolo e di archiviazione](sql-database-service-tiers-dtu.md). Il database viene creato in un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) e in un [server logico di database SQL di Azure](sql-database-features.md).

Per creare un database SQL vuoto, attenersi alla procedura seguente.

1. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.

1. Nella pagina **Nuovo**, selezionare **Database** nella sezione Azure Marketplace e quindi fare clic su **Database SQL** nella sezione **In primo piano**.

   ![creare database vuoto](./media/sql-database-design-first-database/create-empty-database.png)

   1. Compilare il modulo **Database SQL** con le informazioni seguenti, come illustrato nell'immagine precedente:

      | Impostazione       | Valore consigliato | Descrizione |
      | ------------ | ------------------ | ------------------------------------------------- |
      | **Nome database** | *yourDatabase* | Per informazioni sui nomi di database validi, vedere [Identificatori del database](/sql/relational-databases/databases/database-identifiers). |
      | **Sottoscrizione** | *yourSubscription*  | Per informazioni dettagliate sulle sottoscrizioni, vedere [Sottoscrizioni](https://account.windowsazure.com/Subscriptions). |
      | **Gruppo di risorse** | *yourResourceGroup* | Per i nomi di gruppi di risorse validi, vedere [Regole di denominazione e restrizioni](/azure/architecture/best-practices/naming-conventions). |
      | **Select source** (Seleziona origine) | Database vuoto | Indica che deve essere creato un database vuoto. |

   1. Fare clic su **Server** per usare un server esistente o crearne e configurarne uno nuovo per il database. Selezionare il server oppure fare clic su **Crea un nuovo server** e compilare il modulo **Nuovo server** con le informazioni seguenti:

      | Impostazione       | Valore consigliato | Descrizione |
      | ------------ | ------------------ | ------------------------------------------------- |
      | **Nome server** | Qualsiasi nome globalmente univoco | Per i nomi di server validi, vedere [Regole di denominazione e restrizioni](/azure/architecture/best-practices/naming-conventions). |
      | **Accesso amministratore server** | Qualsiasi nome valido | Per informazioni sui nomi di accesso validi, vedere [Identificatori del database](/sql/relational-databases/databases/database-identifiers). |
      | **Password** | Qualsiasi password valida | La password deve contenere almeno otto caratteri delle tre categorie seguenti: maiuscole, minuscole, numeri e caratteri non alfanumerici. |
      | **Posizione** | Qualsiasi località valida | Per informazioni sulle aree, vedere [Aree di Azure](https://azure.microsoft.com/regions/). |

      ![Creare il server di database](./media/sql-database-design-first-database/create-database-server.png)

      Fare clic su **Seleziona**.

   1. Fare clic su **Piano tariffario** per specificare il livello di servizio, il numero di DTU o vCores e la quantità di risorse di archiviazione. Esplorare le opzioni relative al numero di DTU/vCore e di risorse di archiviazione disponibile per ogni livello di servizio. Per impostazione predefinita viene selezionato il [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md) **Standard**, ma è possibile scegliere il [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md).

      > [!IMPORTANT]
      > Nel livello Premium è attualmente disponibile uno spazio di archiviazione superiore a 1 TB in tutte le aree tranne le seguenti: Regno Unito settentrionale, Stati Uniti centro-occidentali, Regno Unito meridionale 2, Cina orientale, US DoD (area centrale), Germania centrale, US DoD (area orientale), US Gov (area sudoccidentale), US Gov (area centro-meridionale), Germania nord-orientale, Cina settentrionale, US Gov (area orientale). Nelle altre aree la quantità massima di risorse di archiviazione nel livello Premium è limitata a 1 TB. Vedere [Limitazioni correnti per P11-P15]( sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).

      Dopo aver selezionato il livello di servizio, il numero di DTU e la quantità di spazio di archiviazione, fare clic su **Applica**.

   1. Immettere le **regole di confronto** per il database vuoto. Per questa esercitazione usare il valore predefinito. Per altre informazioni sulle regole di confronto, vedere [Collations](/sql/t-sql/statements/collations) (Regole di confronto)

1. Dopo aver completato il modulo **Database SQL**, fare clic su **Crea** per effettuare il provisioning del database. Questo passaggio potrebbe richiedere alcuni minuti.

1. Sulla barra degli strumenti fare clic su **Notifiche** per monitorare il processo di distribuzione.

     ![notifica](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-firewall-rule"></a>Creare una regola del firewall

Il servizio di database SQL crea un firewall a livello di server. Il firewall impedisce ad applicazioni e strumenti esterni di connettersi al server e a qualsiasi database nel server. Per abilitare la connettività esterna al database, è prima di tutto necessario aggiungere una regola per l'indirizzo IP al firewall. Per creare una [regola del firewall a livello di server per il database SQL](sql-database-firewall-configure.md), seguire questa procedura.

> [!NOTE]
> Il database SQL comunica sulla porta 1433. Se si sta tentando di connettersi da una rete aziendale, il traffico in uscita attraverso la porta 1433 potrebbe non essere autorizzato dal firewall della rete. In questo caso, non è possibile connettersi al server di database SQL di Azure a meno che l'amministratore non apra la porta 1433.

1. Al termine della distribuzione, fare clic su **Database SQL** nel menu a sinistra e quindi su *yourDatabase* nella pagina **Database SQL**. Verrà visualizzata la pagina di panoramica del database, che mostra il **nome server** completo, ad esempio *yourserver.database.windows.net*, e offre opzioni per operazioni di configurazione aggiuntive.

1. Copiare il nome completo del server per usarlo per la connessione al server e ai database da SQL Server Management Studio.

   ![Nome del server](./media/sql-database-design-first-database/server-name.png)

1. Fare clic su **Imposta firewall server** sulla barra degli strumenti. Verrà visualizzata la pagina **Impostazioni del firewall** per il server di database SQL.

   ![Regola del firewall del server](./media/sql-database-design-first-database/server-firewall-rule.png)

   1. Fare clic su **Aggiungi IP client** sulla barra degli strumenti per aggiungere l'indirizzo IP corrente a una nuova regola del firewall. Una regola del firewall può aprire la porta 1433 per un indirizzo IP singolo o un intervallo di indirizzi IP.

   1. Fare clic su **Salva**. Viene creata una regola del firewall a livello di server per l'indirizzo IP corrente, che apre la porta 1433 nel server logico.

   1. Fare clic su **OK** e quindi chiudere la pagina **Impostazioni del firewall**.

L'indirizzo IP può ora superare il firewall. È ora possibile connettersi al server di database SQL e ai relativi database usando SQL Server Management Studio o un altro strumento di propria scelta. Assicurarsi di usare l'account amministratore del server creato in precedenza.

> [!IMPORTANT]
> Per impostazione predefinita, l'accesso attraverso il firewall del database SQL è abilitato per tutti i servizi di Azure. Selezionando **NO** in questa pagina permette di disabilitare tutti i servizi di Azure.

## <a name="connect-to-the-database"></a>Connettersi al database

Usare [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) per stabilire una connessione al server di database SQL di Azure.

1. Aprire SQL Server Management Studio.

1. Nella finestra di dialogo **Connetti al server** immettere le informazioni seguenti:

   | Impostazione       | Valore consigliato | DESCRIZIONE |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Tipo di server** | Motore di database | Questo valore è obbligatorio. |
   | **Nome server** | Nome completo del server | Ad esempio, *yourserver.database.windows.net*. |
   | **autenticazione** | Autenticazione di SQL Server | L'autenticazione SQL è l'unico tipo di autenticazione configurato in questa esercitazione. |
   | **Accesso** | Account amministratore del server | Account specificato quando è stato creato il server. |
   | **Password** | Password per l'account amministratore del server | Password specificata quando è stato creato il server. |

   ![connetti al server](./media/sql-database-design-first-database/connect.png)

   1. Fare clic su **Opzioni** nella finestra di dialogo **Connetti al server**. Nella sezione **Connetti al database** immettere *yourDatabase* per connettersi a tale database.

      ![connettersi al database nel server](./media/sql-database-design-first-database/options-connect-to-db.png)  

   1. Fare clic su **Connetti**. In SSMS verrà visualizzata la finestra **Esplora oggetti**.

1. In **Esplora oggetti** espandere **Database** e quindi *yourDatabase* per visualizzare gli oggetti presenti nel database di esempio.

   ![oggetti di database](./media/sql-database-design-first-database/connected.png)  

## <a name="create-tables-in-the-database"></a>Creare tabelle nel database

Creare uno schema di database con quattro tabelle che modellano un sistema di gestione degli studenti per le università tramite [Transact-SQL](/sql/t-sql/language-reference):

- Person
- Corso
- Studente
- Riconoscimenti

Nel diagramma seguente viene illustrato come queste tabelle sono correlate tra loro. Alcune di queste tabelle fanno riferimento a delle colonne in altre tabelle. La tabella *Student*, ad esempio, fa riferimento alla colonna *PersonId* della tabella *Person*. Studiare il diagramma per comprendere come sono correlate tra loro le tabelle in questa esercitazione. Per un esame approfondito su come creare tabelle di database efficaci, vedere [Create effective database tables](https://msdn.microsoft.com/library/cc505842.aspx) (Creare tabelle di database efficaci). Per informazioni sulla scelta dei tipi di dati, vedere [Tipi di dati](/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> È anche possibile usare la [Progettazione delle tabelle in SQL Server Management Studio](/sql/ssms/visual-db-tools/design-database-diagrams-visual-database-tools) per creare e progettare le tabelle.

![Relazioni tra tabelle](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. In **Esplora oggetti** fare clic con il pulsante destro del mouse su *yourDatabase* e scegliere **Nuova query**. Viene visualizzata una finestra di query vuota, connessa al database.

1. Nella finestra di query, eseguire la query seguente per creare quattro tabelle nel database:

   ```sql
   -- Create Person table
   CREATE TABLE Person
   (
       PersonId INT IDENTITY PRIMARY KEY,
       FirstName NVARCHAR(128) NOT NULL,
       MiddelInitial NVARCHAR(10),
       LastName NVARCHAR(128) NOT NULL,
       DateOfBirth DATE NOT NULL
   )

   -- Create Student table
   CREATE TABLE Student
   (
       StudentId INT IDENTITY PRIMARY KEY,
       PersonId INT REFERENCES Person (PersonId),
       Email NVARCHAR(256)
   )

   -- Create Course table
   CREATE TABLE Course
   (
       CourseId INT IDENTITY PRIMARY KEY,
       Name NVARCHAR(50) NOT NULL,
       Teacher NVARCHAR(256) NOT NULL
   )

   -- Create Credit table
   CREATE TABLE Credit
   (
       StudentId INT REFERENCES Student (StudentId),
       CourseId INT REFERENCES Course (CourseId),
       Grade DECIMAL(5,2) CHECK (Grade <= 100.00),
       Attempt TINYINT,
       CONSTRAINT [UQ_studentgrades] UNIQUE CLUSTERED
       (
           StudentId, CourseId, Grade, Attempt
       )
   )
   ```

   ![Creare tabelle](./media/sql-database-design-first-database/create-tables.png)

1. In **Esplora oggetti** espandere il nodo **Tabelle** in *yourDatabase* per visualizzare le tabelle create.

   ![tabelle create con SQL Server Management Studio](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Caricare i dati nelle tabelle

1. Creare una cartella denominata *sampleData* nella cartella Download per archiviare dati di esempio per il database.

1. Fare clic con il pulsante destro del mouse sui collegamenti seguenti e salvare i dati nella cartella *sampleData*.

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

1. Aprire una finestra del prompt dei comandi e passare alla cartella *sampleData*.

1. Eseguire questi comandi per inserire i dati di esempio nelle tabelle, sostituendo i valori *server*, *database*, *user* e *password* con i valori dell'ambiente in uso.
  
   ```cmd
   bcp Course in SampleCourseData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   ```

A questo punto, sono stati caricati i dati di esempio nelle tabelle create in precedenza.

## <a name="query-data"></a>Eseguire query sui dati

Eseguire le query seguenti per recuperare informazioni dalle tabelle del database. Per altre informazioni sulla scrittura di query SQL, vedere l'articolo su come [scrivere query SQL](https://technet.microsoft.com/library/bb264565.aspx). La prima query crea un join di tutte le quattro tabelle per trovare tutti gli studenti con "Dominick Pope" come insegnante e un voto superiore al 75%. La seconda query crea un join di tutte le quattro tabelle e trova i corsi a cui è stato iscritto "Noe Coleman".

1. In una finestra di query di SQL Server Management Studio, eseguire la query seguente:

   ```sql
   -- Find the students taught by Dominick Pope who have a grade higher than 75%
   SELECT  person.FirstName, person.LastName, course.Name, credit.Grade
   FROM  Person AS person
       INNER JOIN Student AS student ON person.PersonId = student.PersonId
       INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
       INNER JOIN Course AS course ON credit.CourseId = course.courseId
   WHERE course.Teacher = 'Dominick Pope'
       AND Grade > 75
   ```

1. In una finestra di query eseguire questa query:

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled
   SELECT  course.Name, course.Teacher, credit.Grade
   FROM  Course AS course
       INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
       INNER JOIN Student AS student ON student.StudentId = credit.StudentId
       INNER JOIN Person AS person ON person.PersonId = student.PersonId
   WHERE person.FirstName = 'Noe'
       AND person.LastName = 'Coleman'
   ```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate molte attività di base sui database. Si è appreso come:

> [!div class="checklist"]
> * Creare un database
> * Configurare una regola del firewall
> * Connettersi al database con [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS)
> * Creare tabelle
> * Eseguire il caricamento bulk dei dati
> * Eseguire query sui dati

Per informazioni sulla progettazione di un database con Visual Studio e C#, passare all'esercitazione successiva.

> [!div class="nextstepaction"]
> [Progettare un database SQL di Azure e connettersi con C# e ADO.NET](sql-database-design-first-database-csharp.md)
