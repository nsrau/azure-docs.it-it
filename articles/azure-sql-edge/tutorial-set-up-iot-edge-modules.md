---
title: Configurare i moduli IoT Edge in SQL Edge di Azure
description: Nella seconda delle tre parti di questa esercitazione di SQL Edge di Azure sulla stima delle impurità del minerale di ferro, si configureranno i moduli e le connessioni IoT Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a4087ef56712e098443009bd0457029394ea7b51
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235030"
---
# <a name="set-up-iot-edge-modules-and-connections"></a>Configurare i moduli e le connessioni IoT Edge

Nella seconda delle tre parti di questa esercitazione sulla stima delle impurità del minerale di ferro in SQL Edge di Azure si configureranno i moduli IoT Edge seguenti:

- SQL Edge di Azure
- Modulo IoT Edge per la generazione di dati

## <a name="create-azure-stream-analytics-module"></a>Creare il modulo Analisi di flusso di Azure

Creare un modulo Analisi di flusso di Azure che verrà usato in questa esercitazione. Per altre informazioni sull'uso dei processi di streaming con SQL Edge, vedere [Uso dei processi di streaming con il SQL Edge](stream-analytics.md).

Dopo la creazione del processo di Analisi di flusso di Azure con l'ambiente di hosting impostato come Edge, configurare gli input e gli output per l'esercitazione.

1. Per creare l'**input**, fare clic su **+Aggiungi input del flusso**. Compilare la sezione dei dettagli usando le informazioni seguenti:

   Campo|valore
   -----|-----
   Formato di serializzazione eventi|JSON
   Codifica|UTF-8
   Tipo di compressione eventi|nessuno

2. Per creare l'**output**, fare clic su **+Aggiungi** e scegliere Database SQL. Compilare la sezione dei dettagli usando le informazioni seguenti.

   > [!NOTE]
   > La password specificata in questa sezione deve essere specificata come password dell'amministratore di sistema di SQL quando si distribuisce il modulo SQL Edge nella sezione **"Distribuire il modulo SQL Edge di Azure"** .

   Campo|valore
   -----|-----
   Database|IronOreSilicaPrediction
   Nome server|tcp:.,1433
   Username|sa
   Password|Specificare una password complessa
   Tabella|IronOreMeasurements1

3. Passare alla sezione **Query** e configurare la query come segue:

   `SELECT * INTO <name_of_your_output_stream> FROM <name_of_your_input_stream>`
   
4. In **Configura** selezionare **Pubblica** e quindi selezionare il pulsante **Pubblica**. Salvare l'URI di firma di accesso condiviso da usare con il modulo del database SQL Edge.

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
  
## <a name="deploy-the-data-generator-module"></a>Distribuire il modulo per la generazione di dati

1. Nella sezione **Moduli IoT Edge** fare clic su **+ AGGIUNGI** e selezionare **Modulo IoT Edge**.

2. Specificare un nome per il modulo IoT Edge e l'URI dell'immagine.
   È possibile trovare l'URI dell'immagine nel registro contenitori nel gruppo di risorse. Selezionare la sezione **Repository** in **Servizi**. Per questa esercitazione, scegliere il repository denominato `silicaprediction`. Selezionare il tag appropriato. Il formato dell'URI dell'immagine sarà il seguente:

   *server di accesso del registro contenitori*/*nome del repository*:*nome del tag*

   Ad esempio:

   ```
   ASEdemocontregistry.azurecr.io/silicaprediction:amd64
   ```

3. Fare clic su **Aggiungi**.

## <a name="deploy-the-azure-sql-edge-module"></a>Distribuire il modulo SQL Edge di Azure

1. Per distribuire il modulo SQL Edge di Azure, seguire i passaggi elencati in [Distribuire SQL Edge di Azure (anteprima)](https://docs.microsoft.com/azure/azure-sql-edge/deploy-portal).

2. In **Specifica route** nella pagina **Imposta moduli** specificare le route per il modulo relative alla comunicazione con l'hub di IoT Edge, come segue. 

   ```
   FROM /messages/modules/<your_data_generator_module>/outputs/<your_output_stream_name> INTO
   BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/<your_input_stream_name>")
   ```

   Ad esempio:

   ```
   FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/Input1")
   ```

3. Nelle impostazioni **Modulo gemello** assicurarsi che SQLPackage e ASAJonInfo vengano aggiornati con gli URL di firma di accesso condiviso pertinenti salvati in precedenza nell'esercitazione.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire il modello di Machine Learning in SQL Edge di Azure usando ONNX](tutorial-run-ml-model-on-sql-edge.md)
