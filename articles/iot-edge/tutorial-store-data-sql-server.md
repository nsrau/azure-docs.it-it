---
title: Archiviare i dati con il modulo SQL per Azure IoT Edge | Microsoft Docs
description: Informazioni su come archiviare i dati in locale nel dispositivo IoT Edge con un modulo SQL Server
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/19/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 95041ca77930d87bff6ea31e2eab89a6634cfcf5
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52442965"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Esercitazione: Archiviare dati nei dispositivi perimetrali con database di SQL Server

Usare Azure IoT Edge e SQL Server per archiviare i dati ed eseguirne query nei dispositivi perimetrali. Azure IoT Edge offre funzionalità di archiviazione di base che memorizzano nella cache i messaggi se un dispositivo passa alla modalità offline e quindi li inoltrano quando viene ristabilita la connessione. È tuttavia possibile che siano necessarie funzionalità di archiviazione più avanzate, ad esempio la possibilità di eseguire query sui dati in locale. Incorporando i database locali, i dispositivi IoT Edge possono eseguire elaborazioni più complesse senza la necessità di mantenere una connessione all'hub IoT. Ad esempio, un sensore in un computer carica i dati nel cloud una volta al mese per la creazione di report e il miglioramento di un modulo di apprendimento automatico. Tuttavia, se un tecnico sul campo sta lavorando sulla macchina, può accedere agli ultimi giorni di dati del sensore localmente.

Questo articolo contiene istruzioni per la distribuzione di un database di SQL Server a un dispositivo IoT Edge. Funzioni di Azure, in esecuzione sul dispositivo IoT Edge, struttura i dati in ingresso e quindi li invia al database. I passaggi descritti in questo articolo sono applicabili anche ad altri database che operano in contenitori, ad esempio MySQL o PostgreSQL.

In questa esercitazione si apprenderà come: 

> [!div class="checklist"]
> * Usare Visual Studio Code per creare una funzione di Azure
> * Distribuire un database SQL nel dispositivo IoT Edge
> * Usare Visual Studio Code per compilare i moduli e distribuirli nel dispositivo IoT Edge
> * Visualizzare i dati generati

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Un dispositivo Azure IoT Edge:

* È possibile usare il computer per lo sviluppo o una macchina virtuale come dispositivo perimetrale seguendo la procedura illustrata nella guida introduttiva per dispositivi [Linux](quickstart-linux.md) o [Windows](quickstart.md).

Risorse cloud:

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Gratuito o Standard in Azure. 

Risorse per lo sviluppo:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Estensione C# per Visual Studio Code (con tecnologia OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Estensione Azure IoT Edge per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download). 
* [Docker CE](https://docs.docker.com/install/). 

## <a name="create-a-container-registry"></a>Creare un registro di contenitori

In questa esercitazione si usa l'estensione Azure IoT Edge per Visual Studio Code per creare un modulo e un'**immagine del contenitore** dai file. Eseguire quindi il push dell'immagine in un **registro** che archivia e gestisce le immagini. Distribuire infine l'immagine dal registro nel dispositivo IoT Edge.  

È possibile usare qualsiasi registro compatibile con Docker per inserire le immagini dei contenitori. Due servizi di registro Docker molto diffusi sono [Registro contenitori di Azure](https://docs.microsoft.com/azure/container-registry/) e [Hub Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Questa esercitazione usa il Registro contenitori di Azure. 

Se non è ancora disponibile alcun registro contenitori, seguire questa procedura per crearne uno nuovo in Azure:

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa** > **Contenitori** > **Registro contenitori**.

2. Specificare i valori seguenti per creare il registro contenitori:

   | Campo | Valore | 
   | ----- | ----- |
   | Nome registro | Specificare un nome univoco. |
   | Sottoscrizione | Selezionare una sottoscrizione nell'elenco a discesa. |
   | Gruppo di risorse | È consigliabile usare lo stesso gruppo di risorse per tutte le risorse di test create durante le esercitazioni e le guide introduttive di IoT Edge. Ad esempio, **IoTEdgeResources**. |
   | Località | Scegliere una località vicina. |
   | Utente amministratore | Impostare su **Abilita**. |
   | SKU | Selezionare **Basic**. | 

5. Selezionare **Create**.

6. Dopo aver creato il registro contenitori, passare al registro e quindi selezionare **Chiavi di accesso**. 

7. Copiare i valori nei campi **Server di accesso**, **Nome utente** e **Password**. Usare questi valori più avanti nell'esercitazione per fornire l'accesso al registro contenitori.  

## <a name="create-a-function-project"></a>Creare un progetto per le funzioni

Per inviare dati in un database, è necessario un modulo con cui strutturare i dati in modo corretto e quindi archiviarli in una tabella. 

La procedura seguente illustra come creare una funzione di IoT Edge tramite Visual Studio Code e l'estensione Azure IoT Edge.

1. Aprire Visual Studio Code.

2. Aprire il riquadro comandi di VS Code selezionando **Visualizza** > **Riquadro comandi**.

3. Nel riquadro comandi digitare ed eseguire il comando **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nuova soluzione IoT Edge). Nel riquadro comandi immettere le informazioni seguenti per creare la soluzione: 

   | Campo | Valore |
   | ----- | ----- |
   | Selezionare la cartella | Nel computer di sviluppo scegliere la posizione in cui Visual Studio Code dovrà creare i file della soluzione. |
   | Provide a solution name (Specificare un nome per la soluzione) | Immettere un nome descrittivo per la soluzione, ad esempio **SqlSolution**, oppure accettare l'impostazione predefinita. |
   | Select module template (Selezionare un modello di modulo) | Scegliere **Azure Functions - C#** (Funzioni di Azure - C#). |
   | Provide a module name (Specificare un nome per il modulo) | Assegnare al modulo il nome **sqlFunction**. |
   | Provide Docker image repository for the module (Specificare il repository di immagini Docker per il modulo) | Un repository di immagini include il nome del registro contenitori e il nome dell'immagine del contenitore. L'immagine del contenitore è prepopolata dall'ultimo passaggio. Sostituire **localhost:5000** con il valore del server di accesso nel registro contenitori di Azure. È possibile recuperare il server di accesso dalla pagina Panoramica del registro contenitori nel portale di Azure. La stringa finale è simile a \<nome registro\>.azurecr.io/sqlFunction. |

   La finestra di Visual Studio Code carica l'area di lavoro della soluzione IoT Edge: una cartella \.vscode, una cartella dei moduli, un file del manifesto della distribuzione e un file con estensione \.env. 
   
4. Quando si crea una nuova soluzione IoT Edge, Visual Studio Code chiede di specificare le credenziali del registro nel file \.env. Questo file viene ignorato da Git e l'estensione IoT Edge lo usa in un secondo momento per fornire l'accesso al registro al dispositivo IoT Edge. Aprire il file \.env. 

5. Nel file con estensione env assegnare al runtime IoT Edge le credenziali del registro in modo che possa accedere alle immagini di modulo. Trovare le sezioni **CONTAINER_REGISTRY_USERNAME** e **CONTAINER_REGISTRY_PASSWORD** e inserire le credenziali dopo il segno di uguale: 

   ```env
   CONTAINER_REGISTRY_USERNAME_yourregistry=<username>
   CONTAINER_REGISTRY_PASSWORD_yourregistry=<password>
   ```

6. Salvare il file con estensione env.

7. Nello strumento di esplorazione di Visual Studio Code aprire **modules** > **sqlFunction** > **sqlFunction.cs**.

8. Sostituire il contenuto del file con il codice seguente:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;

   namespace Functions.Samples
   {
       public static class sqlFunction
       {
           [FunctionName("sqlFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   //Store the data in SQL db
                   const string str = "<sql connection string>";
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
                       // Send the message to the output as the temperature value is greater than the threashold.
                       var filteredMessage = new Message(messageBytes);
                       // Copy the properties of the original message into the new Message object.
                       foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                       {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                       // Add a new property to the message to indicate it is an alert.
                       filteredMessage.Properties.Add("MessageType", "Alert");
                       // Send the message.       
                       await output.AddAsync(filteredMessage);
                       logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
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
   }
   ```

6. Nella riga 35 sostituire la stringa **\<sql connection string\>** con la stringa seguente. La proprietà **Data Source** fa riferimento al nome del contenitore di SQL Server, che verrà creato con il nome **SQL** nella sezione successiva. 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

7. Salvare il file **sqlFunction.cs**. 

## <a name="add-a-sql-server-container"></a>Aggiungere un contenitore di SQL Server

Un [manifesto della distribuzione](module-composition.md) dichiara i moduli che il runtime IoT Edge installerà nel dispositivo IoT Edge. Nella sezione precedente è stato specificato il codice per creare un modulo di funzione personalizzato, ma il modulo di SQL Server è già compilato. È sufficiente indicare al runtime IoT Edge di includerlo, quindi configurarlo nel dispositivo. 

1. Nello strumento di esplorazione di Visual Studio Code aprire il file **deployment.template.json**. 

2. Individuare la sezione **modules**. Dovrebbero essere elencati due moduli: **tempSensor**, che genera i dati simulati, e il modulo **sqlFunction**.

3. Se si usano contenitori Windows, modificare la sezione **sqlFunction.settings.image**.

   ```json
   "image": "${MODULES.sqlFunction.windows-amd64}"
   ```

4. Aggiungere il codice seguente per dichiarare un terzo modulo. Aggiungere una virgola dopo la sezione sqlFunction e inserire:

   ```json
   "sql": {
       "version": "1.0",
       "type": "docker",
       "status": "running",
       "restartPolicy": "always",
       "env":{},
       "settings": {
           "image": "",
           "createOptions": ""
       }
   }
   ```

   ![Aggiungere un contenitore di SQL Server](./media/tutorial-store-data-sql-server/view_json_sql.png)

5. A seconda del tipo di contenitori Docker presenti nel dispositivo IoT Edge, aggiornare i parametri del modulo **sql** con il codice seguente:
   * Contenitori Windows:

      ```json
      "env": {
         "ACCEPT_EULA": {"value": "Y"},
         "SA_PASSWORD": {"value": "Strong!Passw0rd"}
       },
       "settings": {
          "image": "microsoft/mssql-server-windows-developer",
          "createOptions": {
              "HostConfig": {
                  "Mounts": [{"Target": "C:\\\\mssql","Source": "sqlVolume","Type": "volume"}],
                  "PortBindings": {
                      "1433/tcp": [{"HostPort": "1401"}]
                  }
              }
          }
      }
      ```

   * Contenitori Linux:

      ```json
      "env": {
         "ACCEPT_EULA": {"value": "Y"},
         "SA_PASSWORD": {"value": "Strong!Passw0rd"}
       },
       "settings": {
          "image": "mcr.microsoft.com/mssql/server:latest",
          "createOptions": {
              "HostConfig": {
                  "Mounts": [{"Target": "/var/opt/mssql","Source": "sqlVolume","Type": "volume"}],
                  "PortBindings": {
                      "1433/tcp": [{"HostPort": "1401"}]
                  }
              }
          }
      }
      ```

   >[!Tip]
   >Ogni volta che si crea un contenitore SQL Server in un ambiente di produzione, è necessario [modificare la password dell'amministratore di sistema predefinita](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker#change-the-sa-password).

6. Salvare il file **deployment.template.json**.

## <a name="build-your-iot-edge-solution"></a>Compilare la soluzione IoT Edge

Nelle sezioni precedenti è stata creata una soluzione con un modulo e quindi ne è stata aggiunta un'altra al modello di manifesto della distribuzione. È ora necessario compilare la soluzione, creare le immagini del contenitore per i moduli ed eseguire il push delle immagini nel registro contenitori. 

1. Accedere al registro contenitori in Visual Studio Code per poter eseguire il push delle immagini nel registro. Usare le stesse credenziali aggiunte al file con estensione env. Immettere il comando seguente nel terminale integrato:

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    
    Verrà richiesto di specificare la password. Incollare la password nel prompt (la password è nascosta per sicurezza) e premere **INVIO**. 

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. Nello strumento di esplorazione di Visual Studio Code fare clic con il pulsante destro del mouse sul file **deployment.template.json** e scegliere **Build and Push IoT Edge solution** (Compila ed esegui il push della soluzione IoT Edge). 

Quando si comunica a Visual Studio Code di compilare la soluzione, prima di tutto con le informazioni del modello di distribuzione viene generato un file deployment.json in una nuova cartella denominata **config**. Vengono quindi eseguiti due comandi nel terminale integrato: `docker build` e `docker push`. Questi due comandi compilano il codice, includono il modulo in un contenitore ed eseguono il push del codice nel registro contenitori specificato quando è stata inizializzata la soluzione. 

## <a name="deploy-the-solution-to-a-device"></a>Distribuire la soluzione in un dispositivo

È possibile impostare i moduli in un dispositivo tramite l'hub IoT, ma è anche possibile accedere all'hub IoT e ai dispositivi tramite Visual Studio Code. In questa sezione si configura l'accesso all'hub IoT e quindi si usa VS Code per distribuire la soluzione nel dispositivo IoT Edge. 

1. Nel riquadro comandi di VS Code selezionare **Azure IoT Hub: Select IoT Hub** (Hub IoT di Azure: Seleziona l'hub IoT).

2. Seguire le istruzioni per accedere all'account Azure. 

3. Nel riquadro comandi selezionare la sottoscrizione di Azure, quindi selezionare l'hub IoT. 

4. Nello strumento di esplorazione di VS Code espandere la sezione **Azure IoT Hub dispositivi** (Dispositivi dell'hub IoT di Azure). 

5. Fare clic con il pulsante destro del mouse sul dispositivo che si vuole specificare come destinazione della distribuzione e scegliere **Create deployment for single device** (Crea la distribuzione per un unico dispositivo). 

   ![Create deployment for single device (Crea la distribuzione per un unico dispositivo)](./media/tutorial-store-data-sql-server/create-deployment.png)

6. In Esplora file passare alla cartella **config** nella soluzione e scegliere **deployment.json**. Fare clic su **Select Edge deployment manifest** (Seleziona il manifesto della distribuzione di Edge). 

Se la distribuzione ha esito positivo, nell'output di VS Code viene visualizzato un messaggio di conferma. 

È anche possibile verificare che tutti i moduli siano operativi nel dispositivo. Nel dispositivo IoT Edge eseguire questo comando per visualizzare lo stato dei moduli. L'operazione potrebbe richiedere alcuni minuti.

   ```cmd/sh
   iotedge list
   ```

## <a name="create-the-sql-database"></a>Creare il database SQL

Quando si applica il manifesto della distribuzione al dispositivo, si ottengono tre moduli in esecuzione. Il modulo tempSensor genera i dati dell'ambiente simulato. Il modulo sqlFunction acquisisce i dati e li formatta per un database. 

Questa sezione descrive come configurare il database SQL per l'archiviazione dei dati di temperatura. 

1. In uno strumento da riga di comando connettersi al database. 
   * Contenitore Windows:
   
      ```cmd
      docker exec -it sql cmd
      ```
    
   * Contenitore Linux: 

      ```bash
      sudo docker exec -it sql bash
      ```

2. Aprire lo strumento di comando SQL.
   * Contenitore Windows:

      ```cmd
      sqlcmd -S localhost -U SA -P "Strong!Passw0rd"
      ```

   * Contenitore Linux: 

      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

3. Creare il database: 

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

4. Definire la tabella.

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

È possibile personalizzare il file Docker di SQL Server in modo da impostare automaticamente la distribuzione di SQL Server in più dispositivi IoT Edge. Per altre informazioni, vedere il [progetto demo di contenitore Microsoft SQL Server](https://github.com/twright-msft/mssql-node-docker-demo-app). 

## <a name="view-the-local-data"></a>Visualizzare i dati locali

Dopo che la tabella è stata creata, il modulo sqlFunction avvia l'archiviazione dei dati in un database di SQL Server 2017 locale nel dispositivo IoT Edge. 

Dallo strumento di comando SQL eseguire il comando seguente per visualizzare i dati della tabella formattati: 

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

   ![Visualizzare i dati locali](./media/tutorial-store-data-sql-server/view-data.png)



## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende continuare con il prossimo articolo consigliato, è possibile conservare le risorse e le configurazioni create e riutilizzarle. È anche possibile continuare a usare lo stesso dispositivo IoT Edge come dispositivo di test. 

In caso contrario, è possibile eliminare le risorse di Azure e le configurazioni locali create in questo articolo per evitare addebiti. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]



## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un modulo di Funzioni di Azure contenente il codice per filtrare i dati non elaborati generati dal dispositivo IoT Edge. Quando si è pronti per compilare moduli personalizzati, sono disponibili altre informazioni sullo [sviluppo di Funzioni di Azure con Azure IoT Edge per Visual Studio Code](how-to-develop-csharp-function.md). 

Continuare con le esercitazioni successive per ottenere informazioni sugli altri modi in cui Azure IoT Edge può contribuire alla trasformazione dei dati in informazioni dettagliate aziendali nei dispositivi perimetrali.

> [!div class="nextstepaction"]
> [Filtrare i dati del sensore usando il codice C#](tutorial-csharp-module.md)
