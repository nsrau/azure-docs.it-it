---
title: Configurare i moduli IoT Edge in SQL Edge di Azure
description: Nella seconda delle tre parti di questa esercitazione di SQL Edge di Azure sulla stima delle impurità del minerale di ferro, si configureranno i moduli e le connessioni IoT Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sourabha, sstein
ms.date: 09/22/2020
ms.openlocfilehash: 75e6ebaea4c5ba883820d2309212b35fed128142
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422128"
---
# <a name="set-up-iot-edge-modules-and-connections"></a>Configurare i moduli e le connessioni IoT Edge

Nella seconda delle tre parti di questa esercitazione sulla stima delle impurità del minerale di ferro in SQL Edge di Azure si configureranno i moduli IoT Edge seguenti:

- SQL Edge di Azure
- Modulo IoT Edge per la generazione di dati

## <a name="specify-container-registry-credentials"></a>Specificare le credenziali del registro contenitori

È necessario specificare le credenziali per i registri contenitori che ospitano le immagini dei moduli. Queste credenziali si trovano nel registro contenitori creato nel gruppo di risorse. Passare alla sezione **Chiavi di accesso**. Prendere nota dei campi seguenti:

- Nome registro
- Server di accesso
- Username
- Password

A questo punto, specificare le credenziali del contenitore nel modulo IoT Edge.

1. Passare all'hub IoT creato nel gruppo di risorse.

2. Nella sezione **IoT Edge** sotto **Gestione dispositivi automatica** fare clic su **ID dispositivo**. Per questa esercitazione l'ID è `IronOrePredictionDevice`.

3. Selezionare la sezione **Imposta moduli**.

4. In **Credenziali del Registro Container** immettere i valori seguenti:

   _Campo_|_Valore_
   -------|-------
   Nome|Nome registro
   Indirizzo|Server di accesso
   Nome utente|Username
   Password|Password
  
## <a name="build-push-and-deploy-the-data-generator-module"></a>Compilare, eseguire il push e distribuire il modulo Generatore di dati

1. Clonare i [file di progetto](https://github.com/microsoft/sqlsourabh/tree/main/SQLEdgeSamples/IoTEdgeSamples/IronOreSilica) nel computer.
2. Aprire il file **IronOre_Silica_Predict.sln** con Visual Studio 2019
3. Aggiornare i dettagli del registro contenitori nel file **deployment.template.json** 
   ```json
   "registryCredentials":{
        "RegistryName":{
            "username":"",
            "password":""
            "address":""
        }
    }
   ```
4. Aggiornare il file **modules.json** per specificare il registro contenitori di destinazione (o il repository per il modulo)
   ```json
   "image":{
        "repository":"samplerepo.azurecr.io/ironoresilicapercent",
        "tag":
    }
   ```
5. Eseguire il progetto in modalità di debug o versione per assicurarsi che il progetto venga eseguito senza problemi 
6. Eseguire il push del progetto nel registro contenitori facendo clic con il pulsante destro del mouse sul nome del progetto e quindi scegliendo **Build and Push IoT Edge Modules** (Compila ed esegui il push dei moduli IoT Edge).
7. Distribuire il modulo Generatore di dati come modulo IoT Edge nel dispositivo perimetrale. 

## <a name="deploy-the-azure-sql-edge-module"></a>Distribuire il modulo SQL Edge di Azure

1. Distribuire il modulo SQL Edge di Azure facendo clic su **+ Aggiungi** e quindi su **Modulo del Marketplace**. 

2. Nel pannello **Marketplace dei moduli IoT Edge** cercare *SQL Edge di Azure* e selezionare *SQL Edge di Azure Edge Developer*. 

3. Fare clic sul modulo *SQL Edge di Azure* appena aggiunto in **Moduli IoT Edge** per configurarlo. Per altre informazioni sulle opzioni di configurazione, vedere [Distribuire SQL Edge di Azure](./deploy-portal.md).

4. Aggiungere la variabile di ambiente `MSSQL_PACKAGE` alla distribuzione del modulo *SQL Edge di Azure* e specificare l'URL di firma di accesso condiviso del file dacpac del database creato nel passaggio 8 della [prima parte](tutorial-deploy-azure-resources.md) di questa esercitazione.

5. Fare clic su **Update** (Aggiorna).

6. Nella pagina **Imposta moduli nel dispositivo** fare clic su **Avanti: Route >** .

7. Nel riquadro Route della pagina **Imposta moduli nel dispositivo** specificare le route per la comunicazione tra il modulo e l'hub di IoT Edge, come descritto di seguito. Assicurarsi di aggiornare i nomi dei moduli nelle definizioni delle route riportate di seguito.

   ```
   FROM /messages/modules/<your_data_generator_module>/outputs/IronOreMeasures INTO
   BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/IronOreMeasures")
   ```

   Ad esempio:

   ```
   FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/IronOreMeasures")
   ```


7. Nella pagina **Imposta moduli nel dispositivo** fare clic su **Avanti: Rivedi e crea >**

8. Nella pagina **Imposta moduli nel dispositivo** fare clic su **Crea**

## <a name="create-and-start-the-t-sql-streaming-job-in-azure-sql-edge"></a>Creare e avviare il processo Streaming T-SQL in SQL Edge di Azure.

1. Aprire Azure Data Studio.

2. Nella scheda di **benvenuto** avviare una nuova connessione con i dettagli seguenti:

   |_Campo_|_Valore_|
   |-------|-------|
   |Tipo di connessione| Microsoft SQL Server|
   |Server|Indirizzo IP pubblico indicato nella macchina virtuale creata per questa demo|
   |Username|sa|
   |Password|Password complessa usata durante la creazione dell'istanza di SQL Edge di Azure|
   |Database|Predefinito|
   |Gruppo di server|Predefinito|
   |Nome (facoltativo)|Specificare un nome facoltativo|

3. Fare clic su **Connetti**

4. Nella scheda del menu **File** aprire un nuovo notebook oppure usare i tasti di scelta rapida CTRL+N.

5. Nella finestra Nuova query eseguire lo script seguente per creare il processo Streaming T-SQL. Prima di eseguire lo script, assicurarsi di cambiare le variabili seguenti. 
   - *SQL_SA_Password:* il valore MSSQL_SA_PASSWORD specificato durante la distribuzione del modulo SQL Edge di Azure. 
   
   ```sql
   Use IronOreSilicaPrediction
   Go

   Declare @SQL_SA_Password varchar(200) = '<SQL_SA_Password>'
   declare @query varchar(max) 

   /*
   Create Objects Required for Streaming
   */

   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'MyStr0ng3stP@ssw0rd';

   If NOT Exists (select name from sys.external_file_formats where name = 'JSONFormat')
   Begin
      CREATE EXTERNAL FILE FORMAT [JSONFormat]  
      WITH ( FORMAT_TYPE = JSON)
   End 


   If NOT Exists (select name from sys.external_data_sources where name = 'EdgeHub')
   Begin
      Create EXTERNAL DATA SOURCE [EdgeHub] 
      With(
         LOCATION = N'edgehub://'
      )
   End 

   If NOT Exists (select name from sys.external_streams where name = 'IronOreInput')
   Begin
      CREATE EXTERNAL STREAM IronOreInput WITH 
      (
         DATA_SOURCE = EdgeHub,
         FILE_FORMAT = JSONFormat,
         LOCATION = N'IronOreMeasures'
       )
   End


   If NOT Exists (select name from sys.database_scoped_credentials where name = 'SQLCredential')
   Begin
       set @query = 'CREATE DATABASE SCOPED CREDENTIAL SQLCredential
                 WITH IDENTITY = ''sa'', SECRET = ''' + @SQL_SA_Password + ''''
       Execute(@query)
   End 

   If NOT Exists (select name from sys.external_data_sources where name = 'LocalSQLOutput')
   Begin
      CREATE EXTERNAL DATA SOURCE LocalSQLOutput WITH (
      LOCATION = 'sqlserver://tcp:.,1433',CREDENTIAL = SQLCredential)
   End

   If NOT Exists (select name from sys.external_streams where name = 'IronOreOutput')
   Begin
      CREATE EXTERNAL STREAM IronOreOutput WITH 
      (
         DATA_SOURCE = LocalSQLOutput,
         LOCATION = N'IronOreSilicaPrediction.dbo.IronOreMeasurements'
      )
   End

   EXEC sys.sp_create_streaming_job @name=N'IronOreData',
   @statement= N'Select * INTO IronOreOutput from IronOreInput'

   exec sys.sp_start_streaming_job @name=N'IronOreData'
   ```

6. Usare la query seguente per verificare che i dati del modulo per la generazione di dati vengano trasmessi al database. 

   ```sql
   Select Top 10 * from dbo.IronOreMeasurements
   order by timestamp desc
   ```


In questa esercitazione sono stati distribuiti il modulo per la generazione di dati e il modulo SQL Edge. È stato quindi creato un processo di streaming per trasmettere i dati generati dal modulo per la generazione di dati a SQL. 

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire il modello di Machine Learning in SQL Edge di Azure usando ONNX](tutorial-run-ml-model-on-sql-edge.md)