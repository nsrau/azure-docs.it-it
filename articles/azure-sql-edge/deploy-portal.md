---
title: Distribuire SQL Edge di Azure (anteprima) usando il portale di Azure
description: Informazioni su come distribuire SQL Edge di Azure (anteprima) usando il portale di Azure
keywords: distribuire SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 43359b66ba747dba7b3294d022a2c1aa2a3e624c
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.contentlocale: it-IT
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233250"
---
# <a name="deploy-azure-sql-edge-preview"></a>Distribuire SQL Edge di Azure (anteprima) 

SQL Edge di Azure (anteprima) è un motore di database relazionale ottimizzato per distribuzioni IoT e Azure IoT Edge. Fornisce funzionalità per la creazione di un livello di elaborazione e archiviazione dei dati a prestazioni elevate per le applicazioni e le soluzioni IoT. Questo argomento di avvio rapido illustra come iniziare a creare un modulo SQL Edge di Azure mediante Azure IoT Edge usando il portale di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/).
* Accedere al [portale di Azure](https://portal.azure.com/).
* Creare un [hub IoT di Azure](../iot-hub/iot-hub-create-through-portal.md).
* Registrare un [dispositivo IoT Edge dal portale di Azure](../iot-edge/how-to-register-device-portal.md).
* Preparare il dispositivo IoT Edge per la [distribuzione del modulo IoT Edge dal portale di Azure](../iot-edge/how-to-deploy-modules-portal.md).

> [!NOTE]
> Per distribuire una macchina virtuale Azure Linux come dispositivo IoT Edge, consultare questa [guida introduttiva](../iot-edge/quickstart-linux.md).

## <a name="deploy-sql-edge-module-from-azure-marketplace"></a>Distribuire un modulo SQL Edge da Azure Marketplace

Azure Marketplace è un marketplace online di servizi e applicazioni dove è possibile trovare un'ampia gamma di soluzioni e applicazioni aziendali certificate e ottimizzate per l'esecuzione in Azure, tra cui i [moduli IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). SQL Edge di Azure può essere distribuito in un dispositivo perimetrale tramite il Marketplace.

1. Trovare il modulo SQL Edge di Azure in Azure Marketplace.<br><br>

   ![SQL Edge in Marketplace](media/deploy-portal/find-offer-marketplace.png)

2. Selezionare il piano software che meglio soddisfa i requisiti e fare clic su **Crea**. <br><br>

   ![Selezionare il piano software corretto](media/deploy-portal/pick-correct-plan.png)

3. Nella pagina Dispositivi di destinazione per modulo IoT Edge specificare i dettagli seguenti e quindi fare clic su **Crea**

   |**Campo**  |**Descrizione**  |
   |---------|---------|
   |Subscription  |  Sottoscrizione ad Azure in cui l'hub IoT è stato creato |
   |Hub IoT   |  Nome dell'hub IoT in cui è registrato il dispositivo IoT Edge e quindi selezionare l'opzione "Distribuisci in un dispositivo"|
   |Nome del dispositivo IoT Edge  |  Nome del dispositivo IoT Edge in cui verrebbe distribuito SQL Edge |

4. Nella pagina **Imposta moduli** passare alla sezione sui moduli di distribuzione e fare clic su **Configura** relativamente al modulo SQL Edge. 

5. Nel riquadro **Moduli personalizzati IoT Edge** specificare i valori desiderati per le variabili di ambiente e/o personalizzare le opzioni di creazione e le proprietà desiderate per il modulo. Per un elenco completo delle variabili di ambiente supportate, fare riferimento a [Variabili di ambiente del contenitore SQL Server](/sql/linux/sql-server-linux-configure-environment-variables/).

   |**Parametro**  |**Descrizione**|
   |---------|---------|
   | Nome | Nome per il modulo. |
   |SA_PASSWORD  | Specificare una password complessa per l'account amministratore di SQL Edge. |
   |MSSQL_LCID   | Imposta l'ID della lingua da usare per SQL Server. Ad esempio, 1036 corrisponde al francese. |
   |MSSQL_COLLATION | Imposta le regole di confronto per SQL Server. Questa impostazione sostituisce il mapping predefinito dell'ID lingua (LCID) per le regole di confronto. |

   > [!NOTE]
   > Non modificare o aggiornare l'**URI dell'immagine** o le impostazioni **ACCEPT_EULA** nel modulo.

6. Nel riquadro **Moduli personalizzati IoT Edge** aggiornare il valore desiderato delle opzioni di creazione del contenitore per la **Porta host**. Se è necessario distribuire più di un modulo database SQL Edge, assicurarsi di aggiornare l'opzione montaggi per creare una nuova coppia di origine e destinazione per il volume permanente. Per altre informazioni sui montaggi e sul volume, fare riferimento a [Usare volumi](https://docs.docker.com/storage/volumes/) nella documentazione di Docker. 

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

7. Nel riquadro **Moduli personalizzati IoT Edge** aggiornare *Imposta proprietà desiderate del modulo gemello* per includere il percorso del pacchetto SQL e le informazioni sul processo di analisi di flusso. Questi due campi sono facoltativi e devono essere usati se si vuole distribuire il modulo SQL Edge con un database e un processo di streaming.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. Nel riquadro **Moduli personalizzati IoT Edge** impostare *Criterio di riavvio* su sempre e *Stato desiderato* su in esecuzione.
9. Nel riquadro **Moduli personalizzati IoT Edge** fare clic su **Salva**.
10. Nella pagina **Imposta moduli** fare clic su **Avanti**.
11. In **Specificare route (facoltativo)** della pagina **Imposta moduli** specificare le route per da modulo a modulo a da modulo alla comunicazione dell'hub IoT Edge. Consultare [Distribuire moduli e stabilire route in IoT Edge](../iot-edge/module-composition.md).
12. Fare clic su **Avanti**.
13. Fare clic su **Submit** (Invia).

In questo argomento di avvio rapido è stato distribuito un modulo SQL Edge in un dispositivo IoT Edge.

## <a name="next-steps"></a>Passaggi successivi

- [Machine Learning e intelligenza artificiale con ONNX in SQL Edge](onnx-overview.md).
- [Creazione di una soluzione IoT end-to-end con SQL Edge usando IoT Edge](tutorial-deploy-azure-resources.md).