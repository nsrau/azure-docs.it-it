---
title: Modulo SQL per Azure IoT Edge | Microsoft Docs
description: Archiviare dati sul perimetro usando i moduli Microsoft SQL, con Funzioni di Azure per formattare i dati.
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban, ebertrams
ms.date: 02/21/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: ce3c3abd00dba23887b5f811af6cab8d2c83323d
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="store-data-at-the-edge-with-sql-server-databases"></a>Archiviare dati sul perimetro con database di SQL Server

Usare dispositivi Azure IoT Edge per archiviare i dati generati sul perimetro. I dispositivi con connessioni Internet intermittenti possono gestire database propri e segnalare le modifiche al cloud solo quando sono connessi. I dispositivi programmati per inviare al cloud solo i dati critici possono salvare il resto dei dati per regolari caricamenti di tipo bulk. Una volta nel cloud, i dati strutturati possono essere condivisi con altri servizi di Azure, ad esempio per compilare un modello di Machine Learning. 

Questo articolo contiene istruzioni per la distribuzione di un database di SQL Server a un dispositivo IoT Edge. Funzioni di Azure, in esecuzione sul dispositivo IoT Edge, struttura i dati in ingresso e quindi li invia al database. I passaggi descritti in questo articolo sono applicabili anche ad altri database che operano in contenitori, ad esempio MySQL o PostgreSQL. 

## <a name="prerequisites"></a>prerequisiti 

Prima di seguire le istruzioni di questo articolo, completare le esercitazioni seguenti:
* Distribuire Azure IoT Edge su un dispositivo simulato in [Windows](tutorial-simulate-device-windows.md) o [Linux](tutorial-simulate-device-linux.md)
* [Distribuire Funzioni di Azure come modulo IoT Edge](tutorial-deploy-function.md)

Gli articoli seguenti non sono necessari per completare questa esercitazione, ma possono fornire contesto utile:
* [Run the SQL Server 2017 container image with Docker](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker) (Eseguire l'immagine del contenitore SQL Server 2017 con Docker)
* [Usare Visual Studio Code per sviluppare e distribuire Funzioni di Azure con Azure IoT Edge](how-to-vscode-develop-azure-function.md)

Dopo aver completato le esercitazioni, i prerequisiti necessari dovrebbero essere disponibili nel computer in uso: 
* Un hub IoT di Azure attivo.
* Un dispositivo IoT Edge con almeno 2 GB di RAM e un'unità disco da 2 GB.
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Estensione Azure IoT Edge per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Estensione C# per Visual Studio Code con tecnologia OmniSharp](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Docker](https://docs.docker.com/engine/installation/)
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd). 
* [Python 2.7](https://www.python.org/downloads/)
* [Script di controllo IoT Edge](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
* Modello AzureIoTEdgeFunction (`dotnet new -i Microsoft.Azure.IoT.Edge.Function`)
* Un hub IoT attivo con almeno un dispositivo IoT Edge.

Per questa esercitazione sono adatti contenitori sia Windows che Linux su architetture di processore x64. SQL Server non supporta i processori ARM.

## <a name="deploy-a-sql-server-container"></a>Distribuire un contenitore SQL Server

In questa sezione si aggiunge un database MS-SQL al dispositivo IoT Edge simulato. Usare l'immagine del contenitore Docker SQL Server 2017, disponibile come contenitore [Windows](https://hub.docker.com/r/microsoft/mssql-server-windows-developer/) e [Linux](https://hub.docker.com/r/microsoft/mssql-server-linux/). 

### <a name="deploy-sql-server-2017"></a>Distribuire SQL Server 2017

Per impostazione predefinita, il codice in questa sezione crea un contenitore con l'edizione Developer gratuita di SQL Server 2017. Se si preferisce eseguire edizioni di produzione, vedere [Run production container images](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#production) (Eseguire immagini contenitore di produzione) per informazioni dettagliate. 

Nel passaggio 3 si aggiungono al contenitore SQL Server opzioni di creazione, importanti per la definizione delle variabili di ambiente e dell'archiviazione persistente. Le [variabili di ambiente](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-environment-variables) configurate accettano le condizioni di licenza e definiscono una password. L'[archiviazione persistente](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#persist) si configura mediante [montaggi](https://docs.docker.com/storage/volumes/). I montaggi creano il contenitore SQL Server 2017 con un contenitore del volume *sqlvolume* associato, in modo che i dati vengano salvati anche se il contenitore viene eliminato. 

1. Aprire il file `deployment.json` in Visual Studio Code. 
2. Sostituire la sezione **modules** con il codice seguente: 

   ```json
   "modules": {
          "filterFunction": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "localhost:5000/filterfunction:latest",
              "createOptions": "{}"
            }
          },
          "tempSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
              "createOptions": "{}"
            }
          },
          "sql": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "",
              "createOptions": ""
             }
          }
        }
   ```

3. A seconda del sistema operativo in esecuzione, aggiornare le impostazioni per il modulo SQL con il codice seguente: 

   * Windows:

      ```json
      "image": "microsoft/mssql-server-windows-developer",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"C:\\\\mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}"
      ```

   * Linux:

      ```json
      "image": "microsoft/mssql-server-linux:2017-latest",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"/var/opt/mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

4. Salvare il file. 
5. Nel riquadro comandi di Visual Studio Code selezionare **Edge: Create deployment for Edge device** (Edge: Crea distribuzione per dispositivo Edge). 
6. Selezionare l'ID del dispositivo IoT Edge.
7. Selezionare il file `deployment.json` aggiornato. Nella finestra di output è possibile visualizzare l'output corrispondente per la distribuzione. 
8. Per avviare il runtime di Edge, selezionare **Edge: Start Edge** (Edge: Avvia Edge) nel riquadro comandi.

>[!TIP]
>Ogni volta che si crea un contenitore SQL Server in un ambiente di produzione, è necessario [modificare la password dell'amministratore di sistema predefinita](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker#change-the-sa-password).

## <a name="create-the-sql-database"></a>Creare il database SQL

Questa sezione descrive come configurare il database SQL per l'archiviazione dei dati di temperatura ricevuti dai sensori connessi al dispositivo IoT Edge. Se si usa un dispositivo simulato, questi dati provengono dal contenitore *tempSensor*. 

In uno strumento da riga di comando connettersi al database: 

* Contenitore Windows
   ```cmd
   docker exec -it sql cmd
   ```

* Contenitore Linux
   ```bash
   docker exec -it sql bash
   ```

Aprire lo strumento di comando SQL: 

* Contenitore Windows
   ```cmd
   sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

* Contenitore Linux
   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

Creare il database: 

* Contenitore Windows
   ```sql
   CREATE DATABASE MeasurementsDB
   ON
   (NAME = MeasurementsDB, FILENAME = 'C:\mssql\measurementsdb.mdf')
   GO
   ```

* Contenitore Linux
   ```sql
   CREATE DATABASE MeasurementsDB
   ON
   (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
   GO
   ```

Definire la tabella: 

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

È possibile personalizzare il file Docker di SQL Server in modo da impostare automaticamente la distribuzione di SQL Server in più dispositivi IoT Edge. Per altre informazioni, vedere il [progetto demo di contenitore Microsoft SQL Server](https://github.com/twright-msft/mssql-node-docker-demo-app).

## <a name="understand-the-sql-connection"></a>Informazioni sulla connessione SQL

In altre esercitazioni vengono usare route per consentire di contenitori di comunicare pur restando isolati tra loro. Quando si lavora con un database di SQL Server, tuttavia, è necessaria una relazione più stretta. 

IoT Edge crea automaticamente una rete bridge (Linux) o NAT (Windows) all'avvio. La rete viene denominata **azure-iot-edge**. Se è necessario eseguire il debug di questa connessione, è possibile cercarne le proprietà nella riga di comando:

* Windows

   ```cmd
   docker network inspect azure-iot-edge
   ```

* Linux

   ```bash
   sudo docker network inspect azure-iot-edge
   ```

IoT Edge può anche risolvere il DNS di un nome contenitore tramite Docker, pertanto non è necessario fare riferimento al database di SQL Server tramite il relativo indirizzo IP. 

Ad esempio, ecco la stringa di connessione usata nella sezione successiva: 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

Come si può notare, la stringa di connessione fa riferimento al contenitore in base al nome, **sql**. Se il nome del modulo è stato modificato, aggiornare anche la stringa di connessione. In caso contrario, proseguire alla sezione successiva. 

## <a name="update-your-azure-function"></a>Aggiornare la funzione di Azure
Per inviare i dati al database, aggiornare la funzione di Azure FilterFunction creata nell'esercitazione precedente. Modificare il file in modo da strutturare i dati ricevuti dai sensori e quindi archiviarli in una tabella SQL. 

1. In Visual Studio Code aprire la cartella FilterFunction. 
2. Sostituire il file run.csx con il codice seguente, che include la stringa di connessione SQL creata nella sezione precedente: 

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"
   #r "System.Data.SqlClient"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;
   using System.Threading.Tasks;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
       const int temperatureThreshold = 25;
       byte[] messageBytes = messageReceived.GetBytes();
       var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

       if (!string.IsNullOrEmpty(messageString))
       {
           // Get the body of the message and deserialize it
           var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

           //Store the data in SQL db
           const string str = "Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;";
           using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
           {
           conn.Open();
           var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
           var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");"; 
               using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
               {
               //Execute the command and log the # rows affected.
               var rows = await cmd.ExecuteNonQueryAsync();
               log.Info($"{rows} rows were updated");
               }
           }

           if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
           {
               // Send the message to the output as the temperature value is greater than the threshold
               var filteredMessage = new Message(messageBytes);
               // Copy the properties of the original message into the new Message object
               foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
               {
                   filteredMessage.Properties.Add(prop.Key, prop.Value);
               }
               // Add a new property to the message to indicate it is an alert
               filteredMessage.Properties.Add("MessageType", "Alert");
               // Send the message        
               await output.AddAsync(filteredMessage);
               log.Info("Received and transferred a message with temperature above the threshold");
           }
       }
   }

   //Define the expected schema for the body of incoming messages
   class MessageBody
   {
       public Machine machine {get;set;}
       public Ambient ambient {get; set;}
       public string timeCreated {get; set;}
   }
   class Machine
   {
      public double temperature {get; set;}
      public double pressure {get; set;}         
   }
   class Ambient
   {
      public double temperature {get; set;}
      public int humidity {get; set;}         
   }
   ```

## <a name="update-your-container-image"></a>Aggiornare l'immagine del contenitore

Per applicare le modifiche apportate, aggiornare l'immagine del contenitore, pubblicarla e riavviare IoT Edge.

1. In Visual Studio Code espandere la cartella **Docker**. 
2. In base alla piattaforma in uso, espandere la cartella **windows-nano** o **linux-x64**. 
3. Fare clic con il pulsante destro del mouse sul file **Dockerfile** e selezionare **Build IoT Edge module Docker image** (Compila l'immagine Docker per il modulo di IoT Edge).
4. Passare alla cartella di progetto **FilterFunction** e fare clic su **Select folder as EXE_DIR** (Seleziona cartella come EXE_DIR).
5. Nella casella di testo popup nella parte superiore della finestra di Visual Studio Code immettere il nome dell'immagine. Ad esempio, `<your container registry address>/filterfunction:latest`. Se si esegue la distribuzione in un registro locale, il nome deve essere `<localhost:5000/filterfunction:latest>`.
6. Nel riquadro comandi di Visual Studio Code selezionare **Edge: Push IoT Edge module Docker image** (Edge: esegui il push dell'immagine Docker per il modulo IoT Edge). 
7. Nella casella di testo popup immettere lo stesso nome di immagine. 
8. Nel riquadro comandi di Visual Studio Code selezionare **Edge: Restart Edge** (Edge: Riavvia Edge).

## <a name="view-the-local-data"></a>Visualizzare i dati locali

Dopo il riavvio dei contenitori, i dati ricevuti dai sensori di temperatura vengono archiviati in un database locale di SQL Server 2017 nel dispositivo IoT Edge. 

In uno strumento da riga di comando connettersi al database: 

* Contenitore Windows
   ```cmd
   docker exec -it sql cmd
   ```

* Contenitore Linux
   ```bash
   docker exec -it sql bash
   ```

Aprire lo strumento di comando SQL: 

* Contenitore Windows
   ```cmd
   sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

* Contenitore Linux
   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

Visualizzare i dati: 

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [configurare immagini contenitore SQL Server 2017 in Docker](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker).

* Visitare il [repository GitHub mssql-docker](https://github.com/Microsoft/mssql-docker) per risorse, feedback e problemi noti.
