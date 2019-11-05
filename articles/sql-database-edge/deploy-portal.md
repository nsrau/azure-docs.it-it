---
title: Distribuire l'anteprima del database perimetrale SQL usando il portale di Azure | Microsoft Docs
description: Informazioni su come distribuire Edge del database SQL di Azure usando il portale di Azure
keywords: distribuire Edge database SQL
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 9da756b702c994d69aae42ecef0e2da4d44eed39
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514095"
---
# <a name="deploy-azure-sql-database-edge-preview"></a>Distribuire l'anteprima perimetrale del database SQL di Azure

L'anteprima Edge del database SQL di Azure è un motore di database relazionale ottimizzato per le distribuzioni e Azure IoT Edge. Fornisce funzionalità per la creazione di un livello di elaborazione e archiviazione dei dati a prestazioni elevate per le applicazioni e le soluzioni Internet. Questa Guida introduttiva illustra come iniziare a creare un modulo perimetrale del database SQL di Azure tramite Azure IoT Edge usando il portale di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/).
* Accedere al [portale di Azure](https://portal.azure.com/).
* Inviare una richiesta [qui](https://azure.microsoft.com/services/sql-database-edge/#contact)per fare in modo che la sottoscrizione sia abilitata per la distribuzione del perimetro del database SQL.
* Creare un [Hub Azure](../iot-hub/iot-hub-create-through-portal.md).
* Registrare un [dispositivo IOT Edge dalla portale di Azure](../iot-edge/how-to-register-device-portal.md).
* Preparare il dispositivo IoT Edge per [distribuire IOT Edge modulo dal portale di Azure](../iot-edge/how-to-deploy-modules-portal.md).

> [!NOTE]
> Per distribuire una VM Linux di Azure come dispositivo IoT Edge, vedere questa [Guida introduttiva](../iot-edge/quickstart-linux.md).

## <a name="deploy-sql-database-edge-module-from-azure-marketplace"></a>Distribuire il modulo perimetrale del database SQL da Azure Marketplace

Azure Marketplace è un marketplace online di servizi e applicazioni dove è possibile trovare un'ampia gamma di soluzioni e applicazioni aziendali certificate e ottimizzate per l'esecuzione in Azure, tra cui i [moduli IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Il bordo del database SQL di Azure può essere distribuito in un dispositivo perimetrale tramite il Marketplace.

1. Trovare il modulo perimetrale del database SQL di Azure in Azure Marketplace.<br><br>

   ![Edge database SQL nel MarketPlace](media/deploy-portal/find-offer-marketplace.png)

2. Selezionare il piano software che meglio soddisfa i requisiti e fare clic su **Crea**. <br><br>

   ![Selezionare il piano software corretto](media/deploy-portal/pick-correct-plan.png)

3. Nella pagina dispositivi di destinazione per IoT Edge modulo specificare i dettagli seguenti e quindi fare clic su **Crea** .

   |**Campo**  |**Descrizione**  |
   |---------|---------|
   |Sottoscrizione  |  Sottoscrizione di Azure in cui è stato creato l'hub Internet |
   |Hub IoT   |  Nome dell'hub Internet delle cose in cui è registrato il dispositivo IoT Edge e quindi selezionare l'opzione "Distribuisci in un dispositivo"|
   |Nome dispositivo IoT Edge  |  Nome del dispositivo IoT Edge in cui verrebbe distribuito il database perimetrale SQL |

4. Nella pagina **imposta moduli** passare alla sezione dei moduli di distribuzione e fare clic su **Configura** per il modulo perimetrale del database SQL. 

5. Nel riquadro **IOT Edge moduli personalizzati** specificare i valori desiderati per le variabili di ambiente e/o personalizzare le opzioni di creazione e le proprietà desiderate per il modulo. Per un elenco completo delle variabili di ambiente supportate, vedere [SQL Server variabili di ambiente del contenitore](/sql/linux/sql-server-linux-configure-environment-variables/).

   |**Parametro**  |**Descrizione**|
   |---------|---------|
   | Name | Nome del modulo. |
   |SA_PASSWORD  | Specificare una password complessa per l'account amministratore perimetrale del database SQL. |
   |MSSQL_LCID   | Imposta l'ID lingua da utilizzare per SQL Server. Ad esempio, 1036 è francese. |
   |MSSQL_COLLATION | Imposta le regole di confronto predefinite per SQL Server. Questa impostazione esegue l'override del mapping predefinito dell'ID lingua (LCID) alle regole di confronto. |

   > [!NOTE]
   > Non modificare o aggiornare l'URI dell' **immagine** o le impostazioni **ACCEPT_EULA** del modulo.

6. Nel riquadro **IOT Edge moduli personalizzati** aggiornare il valore desiderato del contenitore crea opzioni per la **porta host** e la **destinazione** per i punti di montaggio. La destinazione del punto di montaggio è la posizione in cui verranno archiviati i file del database SQL nell'host IoT Edge dispositivo.

   ```json
       {
         "HostConfig": {
           "Binds": [
             "sqlvolume:/sqlvolume"
           ],
           "PortBindings": {
             "1433/tcp": [
               {
                 "HostPort": "1433"
               }
             ]
           },
           "Mounts": [
             {
               "Type": "volume",
               "Source": "sqlvolume",
               "Target": "/var/opt/mssql"
             }
           ]
         },
         "Env": [
           "MSSQL_AGENT_ENABLED=TRUE",
           "MSSQL_PID=Developer"
         ]
       }
   ```

7. Nel riquadro **IOT Edge moduli personalizzati** aggiornare le *proprietà desiderate del modulo set del dispositivo gemello* in modo da includere il percorso del pacchetto SQL e le informazioni sul processo di analisi di flusso. Questi due campi sono facoltativi e devono essere usati se si vuole distribuire il modulo perimetrale del database SQL con un database e un processo di streaming.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. Nel riquadro **IOT Edge Custom Modules** impostare il *criterio di riavvio* su Always and *desired status* su running.
9. Nel riquadro **IOT Edge moduli personalizzati** fare clic su **Salva**.
10. Nella pagina **imposta moduli** fare clic su **Avanti**.
11. Nella pagina **specifica route (facoltativo)** della pagina **set Modules (imposta moduli** ) specificare le route per il modulo o il modulo per la comunicazione dell'hub IOT Edge vedere [deploy modules e establish routes in IOT Edge](../iot-edge/module-composition.md).
12. Fare clic su **Avanti**.
13. Fare clic su **Submit**.

In questa Guida introduttiva è stato distribuito un modulo perimetrale del database SQL in un dispositivo IoT Edge.

## <a name="next-steps"></a>Passaggi successivi

- [Machine Learning e intelligenza artificiale con ONNX nel database SQL Edge](onnx-overview.md).
- Creazione di una soluzione end-to-end per il database SQL tramite IoT Edge.