<properties
	pageTitle="Proteggere i dati sensibili nel database SQL con la crittografia del database | Microsoft Azure"
	description="Proteggere i dati sensibili nel database SQL in pochi minuti."
	keywords="database sql, crittografia sql, crittografia database, chiave crittografia, dati sensibili, crittografia sempre attiva"	
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor="cgronlun"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="sstein"/>

# Proteggere i dati sensibili nel database SQL con la crittografia del database e archiviare le chiavi di crittografia nell'archivio certificati di Windows

> [AZURE.SELECTOR]
- [Insieme di credenziali chiave Azure](sql-database-always-encrypted-azure-key-vault.md)
- [Archivio certificati di Windows](sql-database-always-encrypted.md)

Questo articolo illustra come proteggere i dati sensibili in un database SQL con la crittografia di database tramite la [procedura guidata per la crittografia sempre attiva](https://msdn.microsoft.com/library/mt459280.aspx) di [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx) e archiviare le chiavi di crittografia nell'archivio certificati di Windows.

La crittografia sempre attiva è una nuova tecnologia di crittografia del database SQL di Azure e di SQL Server, che protegge i dati sensibili inattivi sul server durante lo spostamento tra client e server e durante l'uso, assicurando che i dati sensibili non vengano mai visualizzati come testo non crittografato all'interno del sistema di database. Solo le applicazioni client o i server delle app, che hanno accesso alle chiavi, possono accedere ai dati di testo non crittografato. Per informazioni dettagliate, vedere l'articolo relativo alla [crittografia sempre attiva (motore di database)](https://msdn.microsoft.com/library/mt163865.aspx).


Dopo aver configurato il database per usare la crittografia sempre attiva, verrà creata un'applicazione client in C# con Visual Studio per lavorare con i dati crittografati.

Seguire i passaggi in questo articolo per imparare come configurare la crittografia sempre attiva per un database SQL di Azure. Questo articolo spiega come eseguire le attività seguenti:

- Usare la procedura guidata per la crittografia sempre attiva in SSMS per creare [chiavi di crittografia sempre attiva](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3)
    - Creare una [chiave master di colonna (CMK, Column Master Key)](https://msdn.microsoft.com/library/mt146393.aspx).
    - Creare una [chiave di crittografia di colonna (CEK, Column Encryption Key)](https://msdn.microsoft.com/library/mt146372.aspx).
- Creare una tabella di database e crittografare alcune colonne.
- Creare un'applicazione che inserisce, seleziona e visualizza i dati delle colonne crittografate.

> [AZURE.NOTE] La crittografia sempre attiva per il database SQL di Azure è attualmente in modalità di anteprima.


## Prerequisiti

Per questa esercitazione occorrono:

- Un account e una sottoscrizione di Azure prima di iniziare. Nel caso in cui non siano disponibili, è possibile usare una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) versione 13.0.700.242 o versioni successive.
- [.NET Framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) o versioni successive (nel computer client).
- [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)



## Creare un database SQL vuoto
1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **Nuovo** > **Dati + Archiviazione** > **Database SQL**.
3. Creare un database **vuoto** denominato **Clinic** in un server nuovo o esistente. Per istruzioni dettagliate per creare un database nel portale di Azure, vedere [Creare un database SQL in pochi minuti](sql-database-get-started.md).

	![creazione di un database vuoto](./media/sql-database-always-encrypted/create-database.png)

La stringa di connessione sarà necessaria più avanti nell'esercitazione, quindi, dopo aver creato il database, selezionare il nuovo database Clinic e copiare la stringa di connessione (è possibile ottenerla in qualsiasi momento, ma è più semplice copiarla ora dato che ci si trova già nel portale).

1. Fare clic su **Database SQL** > **Clinic** > **Mostra stringhe di connessione del database**.
2. Copiare la stringa di connessione per **ADO.NET**:

	![copia della stringa di connessione](./media/sql-database-always-encrypted/connection-strings.png)


## Connettersi al database con SSMS

Aprire SSMS e connettersi al server con il database Clinic.


1. Aprire SSMS (fare clic su **Connetti** > **Motore di database** per aprire la finestra **Connetti al server**, se non è già aperta).
2. Immettere il nome e le credenziali del server. Il nome del server è disponibile nel pannello del database SQL e nella stringa di connessione copiata in precedenza. Digitare il nome completo del server, compreso *database.windows.net*.

	![copia della stringa di connessione](./media/sql-database-always-encrypted/ssms-connect.png)

3. Se viene visualizzata la finestra **Nuova regola firewall**, accedere ad Azure e lasciare che SSMS crei una nuova regola firewall per l'utente.


## Creare una tabella

Creare prima una tabella per i dati dei pazienti (inizialmente solo una normale tabella, la crittografia verrà configurata nella sezione successiva).

1. Espandere **Database**.
1. Fare clic con il pulsante destro del mouse sul database **Clinic** e quindi fare clic su **Nuova query**.
2. Incollare il comando Transact-SQL (T-SQL) seguente nella finestra della nuova query ed **eseguirlo**:


        CREATE TABLE [dbo].[Patients](
         [PatientId] [int] IDENTITY(1,1), 
         [SSN] [char](11) NOT NULL,
         [FirstName] [nvarchar](50) NULL,
         [LastName] [nvarchar](50) NULL, 
         [MiddleName] [nvarchar](50) NULL,
         [StreetAddress] [nvarchar](50) NULL,
         [City] [nvarchar](50) NULL,
         [ZipCode] [char](5) NULL,
         [State] [char](2) NULL,
         [BirthDate] [date] NOT NULL
         PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
         GO


## Crittografare alcune colonne (configurare la crittografia sempre attiva)

SSMS offre una procedura guidata per configurare facilmente la crittografia sempre attiva impostando la chiave master di colonna, la chiave di crittografia di colonna e le colonne crittografate automaticamente.

1. Espandere **Database** > **Clinic** > **Tabelle**.
2. Fare clic con il pulsante destro del mouse sulla tabella **Patients** e selezionare **Crittografa colonne** per aprire la procedura guidata della crittografia sempre attiva:

    ![crittografia delle colonne](./media/sql-database-always-encrypted/encrypt-columns.png)

3. **Selezione colonne**

    Fare clic su **Avanti** nella pagina **Introduzione** per aprire la pagina **Selezione colonne** in cui selezionare le colonne da crittografare, [il tipo di crittografia e la chiave di crittografia di colonna](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) da usare.

    Per ogni paziente si vuole crittografare il **codice fiscale** e la **data di nascita**. La colonna relativa al codice fiscale usa la crittografia deterministica, che supporta ricerche di uguaglianza, join e raggruppamenti. La colonna relativa alla data di nascita usa la crittografia casuale, che non supporta le operazioni.

    Selezionare e impostare **Deterministica** per il **Tipo di crittografia** della colonna del codice fiscale e **Casuale** per la colonna della data di nascita e quindi fare clic su **Avanti**.

    ![crittografia delle colonne](./media/sql-database-always-encrypted/column-selection.png)

4. **Configurazione della chiave master**

    La pagina **Configurazione della chiave master** consente di impostare la chiave master di colonna e selezionare il provider dell'archivio chiavi in cui verrà archiviata la chiave master di colonna. Attualmente è possibile archiviare una chiave master di colonna nell'archivio certificati di Windows, nell'insieme di credenziali delle chiavi di Azure o in un modulo di protezione hardware. Questa esercitazione illustra come archiviare le chiavi nell'archivio certificati di Windows.

    Verificare che l'**archivio certificati di Windows** sia selezionato e fare clic su **Avanti**.

    ![configurazione della chiave master](./media/sql-database-always-encrypted/master-key-configuration.png)


5. **Convalida**

    È attualmente possibile crittografare le colonne o salvare uno script di PowerShell da eseguire in un secondo momento. Per questa esercitazione selezionare **Procedi per completare ora** e fare clic su **Avanti**.

6. **Riepilogo**

    Verificare che tutte le impostazioni siano corrette e fare clic su **Fine** per completare la configurazione della crittografia sempre attiva.


    ![riepilogo](./media/sql-database-always-encrypted/summary.png)


### Che cosa ha fatto esattamente la procedura guidata?

Al termine della procedura guidata, il database è configurato per la crittografia sempre attiva e sono state completate le operazioni seguenti:

- Creazione di una chiave master di colonna.
- Creazione di una chiave di crittografia di colonna.
- Configurazione delle colonne selezionate per la crittografia. La tabella Patients attualmente è vuota, ma i dati esistenti nelle colonne selezionate sono ora crittografati.

È possibile verificare la creazione di chiavi in SSMS espandendo **Clinic** > **Sicurezza** > **Sempre chiavi crittografate**. Sono ora visibili le nuove chiavi generate dalla procedura guidata.


## Creare un'applicazione client che funziona con i dati crittografati

Ora che la crittografia sempre attiva è configurata, è possibile creare un'applicazione che esegue alcuni inserimenti e selezioni nelle colonne crittografate. Per eseguire correttamente l'applicazione di esempio, è necessario avviarla nello stesso computer in cui è stata eseguita la procedura guidata della crittografia sempre attiva. Per eseguirla in un altro computer è necessario distribuire i certificati della crittografia sempre attiva nel computer che esegue l'applicazione client.

> [AZURE.IMPORTANT] L'applicazione deve usare oggetti [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) per trasferire dati di testo non crittografato al server con colonne con la crittografia sempre attiva. Il trasferimento di valori letterali senza usare oggetti SqlParameter genererà un'eccezione.


1. Aprire Visual Studio e creare un'applicazione console C#. Verificare che il progetto sia impostato su **.NET Framework 4.6** o versione successiva.
2. Denominare il progetto **AlwaysEncryptedConsoleApp** e fare clic su **OK**.


	![nuova applicazione console](./media/sql-database-always-encrypted/console-app.png)



## Modificare la stringa di connessione per abilitare la crittografia sempre attiva

Questa sezione descrive semplicemente come abilitare la crittografia sempre attiva nella stringa di connessione del database. Nella sezione successiva, **Applicazione console di esempio della crittografia sempre attiva**, si modificherà l'applicazione console appena creata.


Per abilitare la crittografia sempre attiva è necessario aggiungere la parola chiave **Column Encryption Setting** alla stringa di connessione e impostarla su **Abilitata**.

È possibile impostarla direttamente nella stringa di connessione o tramite [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). L'applicazione di esempio nella sezione successiva mostra come usare **SqlConnectionStringBuilder**.

> [AZURE.NOTE] Questa è l'unica modifica specifica della crittografia sempre attiva da apportare a un'applicazione client. Se è presente un'applicazione esistente che archivia la stringa di connessione esternamente (ad esempio in un file di configurazione), è possibile abilitare la crittografia sempre attiva senza modificare il codice.


### Abilitare la crittografia sempre attiva nella stringa di connessione

Aggiungere la parola chiave seguente alla stringa di connessione:

    Column Encryption Setting=Enabled


### Abilitare la crittografia sempre attiva con SqlConnectionStringBuilder

Il codice seguente mostra come abilitare la crittografia sempre attiva tramite l'impostazione di [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) su [Abilitata](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder = 
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting = 
       SqlConnectionColumnEncryptionSetting.Enabled;



## Applicazione console di esempio della crittografia sempre attiva

Questo esempio dimostra come:

- Modificare la stringa di connessione per abilitare la crittografia sempre attiva.
- Inserire dati nelle colonne crittografate.
- Selezionare un record filtrando in base a un valore specifico in una colonna crittografata.

Sostituire il contenuto del file **Program.cs** con il codice seguente. Sostituire la stringa di connessione per la variabile globale connectionString nella riga immediatamente sopra il metodo Main con la stringa di connessione valida del portale di Azure. Questa è l'unica modifica che è necessario apportare al codice.

Eseguire ora l'app per vedere in azione la crittografia sempre attiva.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Data;
    using System.Data.SqlClient;
    
    namespace AlwaysEncryptedConsoleApp
    {
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure Portal.
        static string connectionString = @"Replace with your connection string";

        static void Main(string[] args)
        {
            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted 
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();


            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;


            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            InsertPatient(new Patient() {
                SSN = "999-99-0001", FirstName = "Orlando", LastName = "Gee", BirthDate = DateTime.Parse("01/04/1964") });
            InsertPatient(new Patient() {
                SSN = "999-99-0002", FirstName = "Keith", LastName = "Harris", BirthDate = DateTime.Parse("06/20/1977") });
            InsertPatient(new Patient() {
                SSN = "999-99-0003", FirstName = "Donna", LastName = "Carreras", BirthDate = DateTime.Parse("02/09/1973") });
            InsertPatient(new Patient() {
                SSN = "999-99-0004", FirstName = "Janet", LastName = "Gates", BirthDate = DateTime.Parse("08/31/1985") });
            InsertPatient(new Patient() {
                SSN = "999-99-0005", FirstName = "Lucy", LastName = "Harrington", BirthDate = DateTime.Parse("05/06/1993") });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 123-45-6789):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null)
            {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else
            {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }


        static int InsertPatient(Patient newPatient)
        {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
         VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);


            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex)
                {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients()
        {
            List<Patient> patients = new List<Patient>();


            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));


            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patients.Add(new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }

            return patients;
        }


        static Patient SelectPatientBySSN(string ssn)
        {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);


            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patient = new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else
                    {
                        patient = null;
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }
            return patient;
        }


        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable()
        {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex)
                {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient
    {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
    }


## Verificare che i dati siano crittografati

Per verificare rapidamente che i dati del server siano crittografati, è possibile eseguire facilmente una query nei dati dei pazienti con SSMS (tramite la connessione corrente in cui l'impostazione Column Encryption Setting non è ancora abilitata).

Eseguire la query seguente nel database Clinic:

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

È possibile osservare che le colonne crittografate non contengono dati di testo non crittografato.

   ![nuova applicazione console](./media/sql-database-always-encrypted/ssms-encrypted.png)


Per usare SSMS per accedere ai dati di testo non crittografato, aggiungere il parametro **Column Encryption Setting=Enabled** alla connessione.

1. In SSMS fare clic con il pulsante destro del mouse sul server in **Esplora oggetti** e scegliere **Disconnetti**.
2. Fare clic su **Connetti** > **Motore di database** per aprire la finestra **Connetti al server** e quindi fare clic su **Opzioni**.
3. Fare clic su **Parametri aggiuntivi per la connessione** e digitare **Column Encryption Setting=Enabled**.

	![nuova applicazione console](./media/sql-database-always-encrypted/ssms-connection-parameter.png)

4. Eseguire la query seguente nel database Clinic:

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     È ora possibile visualizzare i dati non crittografati nelle colonne crittografate.


	![nuova applicazione console](./media/sql-database-always-encrypted/ssms-plaintext.png)



> [AZURE.NOTE] Se ci si connette a SSMS (o a un client qualsiasi) da un altro computer, il computer non avrà accesso alle chiavi di crittografia e quindi non sarà in grado di decrittografare i dati.



## Passaggi successivi
Dopo aver creato un database che usa la crittografia sempre attiva, è possibile eseguire le operazioni seguenti:

- Eseguire questo esempio da un altro computer. Non avrà accesso alle chiavi di crittografia e quindi non avrà accesso ai dati non crittografati e non verrà eseguito correttamente. 
- [Ruotare e pulire le chiavi](https://msdn.microsoft.com/library/mt607048.aspx).
- [Eseguire la migrazione dei dati già crittografati con la crittografia sempre attiva](https://msdn.microsoft.com/library/mt621539.aspx)
- Distribuire i certificati della crittografia sempre attiva ad altri computer client


## Informazioni correlate

- [Crittografia sempre attiva (sviluppo di client)](https://msdn.microsoft.com/library/mt147923.aspx)
- [Transparent Data Encryption](https://msdn.microsoft.com/library/bb934049.aspx)
- [Crittografia di SQL Server](https://msdn.microsoft.com/library/bb510663.aspx)
- [Procedura guidata della crittografia sempre attiva](https://msdn.microsoft.com/library/mt459280.aspx)
- [Blog della crittografia sempre attiva](http://blogs.msdn.com/b/sqlsecurity/archive/tags/always%20encrypted/)

<!---HONumber=AcomDC_0302_2016-->