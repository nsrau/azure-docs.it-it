---
title: 'Esercitazione: Archiviare i dati con il modulo SQL - Azure IoT Edge | Microsoft Docs'
description: Informazioni su come archiviare i dati in locale nel dispositivo IoT Edge con un modulo SQL Server
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: b77b44856e9623235051470bc087885765ee12c9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080439"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Esercitazione: Archiviare dati sul perimetro con database di SQL Server

Distribuire un modulo di SQL Server per archiviare dati in un dispositivo Linux che esegue Azure IoT Edge.

Usare Azure IoT Edge e SQL Server per archiviare i dati ed eseguirne query nei dispositivi perimetrali. Azure IoT Edge offre funzionalità di archiviazione di base che memorizzano nella cache i messaggi se un dispositivo passa alla modalità offline e quindi li inoltrano quando viene ristabilita la connessione. È tuttavia possibile che siano necessarie funzionalità di archiviazione più avanzate, ad esempio la possibilità di eseguire query sui dati in locale. I dispositivi IoT Edge possono usare database locale per eseguire elaborazioni più complesse senza la necessità di mantenere una connessione all'hub IoT. 

Questo articolo contiene istruzioni per la distribuzione di un database di SQL Server a un dispositivo IoT Edge. Funzioni di Azure, in esecuzione sul dispositivo IoT Edge, struttura i dati in ingresso e quindi li invia al database. I passaggi descritti in questo articolo sono applicabili anche ad altri database che operano in contenitori, ad esempio MySQL o PostgreSQL.

In questa esercitazione si apprenderà come: 

> [!div class="checklist"]
> * Usare Visual Studio Code per creare una funzione di Azure
> * Distribuire un database SQL nel dispositivo IoT Edge
> * Usare Visual Studio Code per compilare i moduli e distribuirli nel dispositivo IoT Edge
> * Visualizzare i dati generati

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione è necessario aver completato l'esercitazione precedente per configurare l'ambiente di sviluppo per i contenitori Linux: [Sviluppare moduli IoT Edge per i dispositivi Linux](tutorial-develop-for-linux.md). Completando tale esercitazione, saranno soddisfatti i prerequisiti seguenti: 

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Gratuito o Standard in Azure.
* Un [dispositivo Linux che esegue Azure IoT Edge](quickstart-linux.md)
* Un registro contenitori, ad esempio [Registro Azure Container](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) configurato con [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configurato per eseguire i contenitori Linux.

In questa esercitazione si usa un modulo di Funzioni di Azure per inviare dati a SQL Server. Per sviluppare un modulo IoT Edge con Funzioni di Azure, installare i prerequisiti aggiuntivi seguenti nel computer di sviluppo: 

* [Estensione C# per Visual Studio Code (con tecnologia OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download). 

## <a name="create-a-function-project"></a>Creare un progetto per le funzioni

Per inviare dati in un database, è necessario un modulo con cui strutturare i dati in modo corretto e quindi archiviarli in una tabella. 

### <a name="create-a-new-project"></a>Creare un nuovo progetto

La procedura seguente illustra come creare una funzione di IoT Edge tramite Visual Studio Code e gli strumenti di Azure IoT.

1. Aprire Visual Studio Code.

2. Aprire il riquadro comandi di VS Code selezionando **Visualizza** > **Riquadro comandi**.

3. Nel riquadro comandi digitare ed eseguire il comando **Azure IoT Edge: Nuova soluzione IoT Edge**. Nel riquadro comandi immettere le informazioni seguenti per creare la soluzione: 

   | Campo | Valore |
   | ----- | ----- |
   | Selezionare la cartella | Nel computer di sviluppo scegliere la posizione in cui Visual Studio Code dovrà creare i file della soluzione. |
   | Provide a solution name (Specificare un nome per la soluzione) | Immettere un nome descrittivo per la soluzione, ad esempio **SqlSolution**, oppure accettare l'impostazione predefinita. |
   | Select module template (Selezionare un modello di modulo) | Scegliere **Azure Functions - C#** (Funzioni di Azure - C#). |
   | Provide a module name (Specificare un nome per il modulo) | Assegnare al modulo il nome **sqlFunction**. |
   | Provide Docker image repository for the module (Specificare il repository di immagini Docker per il modulo) | Un repository di immagini include il nome del registro contenitori e il nome dell'immagine del contenitore. L'immagine del contenitore è prepopolata dall'ultimo passaggio. Sostituire **localhost:5000** con il valore del server di accesso in Registro Azure Container. È possibile recuperare il server di accesso dalla pagina Panoramica del registro contenitori nel portale di Azure. <br><br>La stringa finale è simile a \<nome registro\>.azurecr.io/sqlfunction. |

   La finestra di VS Code carica l'area di lavoro della soluzione IoT Edge. 
   
### <a name="add-your-registry-credentials"></a>Aggiungere le credenziali del registro

Il file dell'ambiente archivia le credenziali per il registro contenitori e le condivide con il runtime IoT Edge. Queste credenziali sono necessarie al runtime per eseguire il pull delle immagini private nel dispositivo IoT Edge.

1. Nello strumento di esplorazione di Visual Studio Code aprire il file con estensione env.
2. Aggiornare i campi con i valori di **nome utente** e **password** copiati dal registro contenitori di Azure.
3. Salvare questo file.

### <a name="select-your-target-architecture"></a>Selezionare l'architettura di destinazione

Attualmente, Visual Studio Code può sviluppare moduli C per dispositivi Linux AMD64 e Linux ARM32v7. È necessario selezionare l'architettura di destinazione per ogni soluzione, perché il contenitore viene creato ed eseguito in modo diverso in base al tipo di architettura. L'impostazione predefinita è Linux AMD64. 

1. Aprire il riquadro comandi e cercare **Azure IoT Edge: Set Default Target Platform for Edge Solution** (Azure IoT Edge: Imposta la piattaforma di destinazione predefinita per la soluzione Edge) oppure selezionare l'icona del collegamento sulla barra laterale nella parte inferiore della finestra. 

2. Nel riquadro comandi selezionare l'architettura di destinazione nell'elenco di opzioni. Per questa esercitazione si usa una macchina virtuale Ubuntu come dispositivo IoT Edge, quindi si manterrà il valore predefinito **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Aggiornare il modulo con il codice personalizzato

1. Nello strumento di esplorazione di Visual Studio Code aprire **modules** > **sqlFunction** > **sqlFunction.cs**.

2. Sostituire l'intero contenuto del file con il codice seguente:

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
                           logger.LogInformation($"{rows} rows were updated");
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

3. Nella riga 35 sostituire la stringa **\<sql connection string\>** con la stringa seguente. La proprietà **Data Source** fa riferimento al nome del contenitore di SQL Server, che non esiste ma verrà creato con il nome **SQL** nella sezione successiva. 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

4. Salvare il file **sqlFunction.cs**. 

5. Aprire il file **sqlFunction.csproj**.

6. Trovare il gruppo di riferimenti ai pacchetti e aggiungerne uno nuovo per includere SqlClient. 

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

7. Salvare il file **sqlFunction.csproj**.

## <a name="add-the-sql-server-container"></a>Aggiungere il contenitore di SQL Server

Un [manifesto della distribuzione](module-composition.md) dichiara i moduli che il runtime IoT Edge installerà nel dispositivo IoT Edge. Nella sezione precedente è stato specificato il codice per creare un modulo di funzione personalizzato, ma il modulo di SQL Server è già compilato e disponibile in Azure Marketplace. È sufficiente indicare al runtime IoT Edge di includerlo, quindi configurarlo nel dispositivo. 

1. In Visual Studio Code aprire il riquadro comandi selezionando **Visualizza** > **Riquadro comandi**.

2. Nel riquadro comandi digitare ed eseguire il comando **Azure IoT Edge: Aggiungi modulo IoT Edge**. Nel riquadro comandi immettere le informazioni seguenti per aggiungere un nuovo modulo: 

   | Campo | Valore | 
   | ----- | ----- |
   | Select deployment template file (Selezionare il file del modello di distribuzione) | Il riquadro comandi evidenzia il file deployment.template.json nella cartella della soluzione corrente. Selezionare questo file.  |
   | Select module template (Selezionare un modello di modulo) | Selezionare **Module from Azure Marketplace** (Modulo da Azure Marketplace). |

3. Nel marketplace per il modulo Azure IoT Edge cercare e selezionare **SQL Server Module** (Modulo di SQL Server). 

4. Cambiare il nome del modulo in **sql**, tutto in minuscolo. Questo nome corrisponde al nome del contenitore dichiarato nella stringa di connessione nel file sqlFunction.cs. 

5. Selezionare **Importa** per aggiungere il modulo alla soluzione. 

6. Nella cartella della soluzione aprire il file **deployment.template.json**. 

7. Individuare la sezione **modules**. Dovrebbero essere visualizzati tre moduli. Il modulo *tempSensor* è incluso per impostazione predefinita nelle nuove soluzioni e fornisce i dati di test da usare con gli altri moduli. Il modulo *sqlFunction* è quello che è stato creato inizialmente e aggiornato con il nuovo codice. Infine, il modulo *sql* è stato importato da Azure Marketplace. 

   >[!Tip]
   >Il modulo di SQL Server include una password predefinita impostata nelle variabili di ambiente del manifesto della distribuzione. Ogni volta che si crea un contenitore SQL Server in un ambiente di produzione, è necessario [modificare la password dell'amministratore di sistema predefinita](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker).

8. Chiudere il file **deployment.template.json**.

## <a name="build-your-iot-edge-solution"></a>Compilare la soluzione IoT Edge

Nelle sezioni precedenti è stata creata una soluzione con un modulo e quindi ne è stata aggiunta un'altra al modello di manifesto della distribuzione. Il modulo di SQL Server viene ospitato pubblicamente da Microsoft, ma è necessario aggiungere il codice a un contenitore nel modulo Functions. In questa sezione viene compilata la soluzione, vengono create le immagini del contenitore per il modulo sqlFunction e ne viene eseguito il push nel registro contenitori. 

1. In Visual Studio Code aprire il terminale integrato selezionando **Visualizza** > **Terminale**.  

1. Accedere al registro contenitori in Visual Studio Code per poter eseguire il push delle immagini nel registro. Usare le stesse credenziali di Registro Azure Container aggiunte al file con estensione env. Immettere il comando seguente nel terminale integrato:

    ```csh/sh
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```
    
    È possibile che venga visualizzato un avviso di sicurezza in cui si consiglia l'uso del parametro --password-stdin. Sebbene il suo utilizzo non rientri nell'ambito di questo articolo, si raccomanda di seguire questa procedura consigliata. Per altre informazioni, vedere la guida comandi di [accesso di Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin). 

2. Nello strumento di esplorazione di Visual Studio Code fare clic con il pulsante destro del mouse sul file **deployment.template.json** e scegliere **Build and Push IoT Edge solution** (Compila ed esegui il push della soluzione IoT Edge). 

Quando si comunica a Visual Studio Code di compilare la soluzione, prima di tutto con le informazioni del modello di distribuzione viene generato un file deployment.json in una nuova cartella denominata **config**. Vengono quindi eseguiti due comandi nel terminale integrato: `docker build` e `docker push`. Questi due comandi compilano il codice, includono il modulo in un contenitore ed eseguono il push del codice nel registro contenitori specificato quando è stata inizializzata la soluzione. 

È possibile verificare se il push del modulo sqlFunction nel registro contenitori è stato eseguito correttamente. Nel portale di Azure passare al registro contenitori. Selezionare **repository** e cercare **sqlFunction**. Il push degli altri due moduli, tempSensor e sql, nel registro contenitori non verrà eseguito perché si punta già ai relativi repository nei registri Microsoft.

## <a name="deploy-the-solution-to-a-device"></a>Distribuire la soluzione in un dispositivo

È possibile impostare i moduli in un dispositivo tramite l'hub IoT, ma è anche possibile accedere all'hub IoT e ai dispositivi tramite Visual Studio Code. In questa sezione si configura l'accesso all'hub IoT e quindi si usa VS Code per distribuire la soluzione nel dispositivo IoT Edge. 

1. Nello strumento di esplorazione di VS Code espandere la sezione **Azure IoT Hub dispositivi** (Dispositivi dell'hub IoT di Azure). 

2. Fare clic con il pulsante destro del mouse sul dispositivo che si vuole specificare come destinazione della distribuzione e scegliere **Create deployment for single device** (Crea la distribuzione per un unico dispositivo). 

3. In Esplora file passare alla cartella **config** nella soluzione e scegliere **deployment.amd64**. Fare clic su **Select Edge deployment manifest** (Seleziona il manifesto della distribuzione di Edge). 

   Non usare il file deployment.template.json come manifesto della distribuzione.

Se la distribuzione ha esito positivo, nell'output di Visual Studio Code viene visualizzato un messaggio di conferma. 

Aggiornare lo stato del dispositivo nella sezione Azure IoT Hub Devices (Dispositivi dell'hub IoT di Azure) di VS Code. I nuovi moduli sono inclusi nell'elenco e risulteranno in esecuzione entro pochi minuti, quando i contenitori saranno installati e avviati. È anche possibile verificare che tutti i moduli siano operativi nel dispositivo. Nel dispositivo IoT Edge eseguire questo comando per visualizzare lo stato dei moduli. 

   ```cmd/sh
   iotedge list
   ```

## <a name="create-the-sql-database"></a>Creare il database SQL

Quando si applica il manifesto della distribuzione al dispositivo, si ottengono tre moduli in esecuzione. Il modulo tempSensor genera i dati dell'ambiente simulato. Il modulo sqlFunction acquisisce i dati e li formatta per un database. Questa sezione descrive come configurare il database SQL per l'archiviazione dei dati di temperatura. 

Eseguire i comandi seguenti nel dispositivo IoT Edge. Questi comandi stabiliscono una connessione al modulo **sql** in esecuzione nel dispositivo e creano un database e una tabella per i dati di temperatura inviati al modulo. 

1. In uno strumento da riga di comando nel dispositivo IoT Edge connettersi al database. 
      ```bash
      sudo docker exec -it sql bash
      ```

2. Aprire lo strumento di comando SQL.
      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

3. Creare il database: 
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

   ![Visualizzare il contenuto del database locale](./media/tutorial-store-data-sql-server/view-data.png)



## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende continuare con il prossimo articolo consigliato, è possibile conservare le risorse e le configurazioni create e riutilizzarle. È anche possibile continuare a usare lo stesso dispositivo IoT Edge come dispositivo di test. 

In caso contrario, è possibile eliminare le risorse di Azure e le configurazioni locali create in questo articolo per evitare addebiti. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un modulo di Funzioni di Azure contenente il codice per filtrare i dati non elaborati generati dal dispositivo IoT Edge. Quando si è pronti per compilare moduli personalizzati, sono disponibili altre informazioni sullo [sviluppo di Funzioni di Azure con Azure IoT Edge per Visual Studio Code](how-to-develop-csharp-function.md). 

Se si vuole provare un altro metodo di archiviazione nei dispositivi perimetrali, leggere l'articolo su come usare Archiviazione BLOB di Azure in IoT Edge. 

> [!div class="nextstepaction"]
> [Archiviare i dati sui dispositivi perimetrali con l'archiviazione BLOB di Azure in IoT Edge](how-to-store-data-blob.md)
