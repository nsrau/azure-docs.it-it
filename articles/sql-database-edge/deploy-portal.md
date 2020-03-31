---
title: Distribuire l'anteprima di SQL Database Edge tramite il portale di Azure Documenti Microsoft
description: Informazioni su come distribuire Azure SQL Database Edge usando il portale di Azure
keywords: distribuire il perimetro del database sql
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 9da922de38d820864b3f83de80fe64eb3ac792e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246722"
---
# <a name="deploy-azure-sql-database-edge-preview"></a>Distribuire L'anteprima di Azure SQL Database EdgeDeploy Azure SQL Database Edge Preview

Azure SQL Database Edge Preview è un motore di database relazionale ottimizzato per le distribuzioni IoT e Azure IoT Edge.Azure SQL Database Edge Preview is a relational database engine optimized for IoT and Azure IoT Edge deployments. Fornisce funzionalità per creare un livello di elaborazione e archiviazione dati ad alte prestazioni per le applicazioni e le soluzioni IoT.Provides capabilities to create a high-performance data storage and processing layer for IoT applications and solutions. Questa guida introduttiva illustra come iniziare a creare un modulo Edge del database SQL di Azure tramite Azure IoT Edge usando il portale di Azure.This quickstart shows you how to get started with creating an Azure SQL Database Edge module through Azure IoT Edge using the Azure portal.

## <a name="before-you-begin"></a>Prima di iniziare

* Se non si dispone di una sottoscrizione di Azure, creare un [account gratuito.](https://azure.microsoft.com/free/)
* Accedere al [portale](https://portal.azure.com/)di Azure .
* Inviare una richiesta [qui](https://azure.microsoft.com/services/sql-database-edge/#contact)per abilitare la sottoscrizione per la distribuzione di SQL Database Edge.
* Creare un [hub IoT di Azure.](../iot-hub/iot-hub-create-through-portal.md)
* Registrare un [dispositivo perimetrale IoT dal portale](../iot-edge/how-to-register-device-portal.md)di Azure.Register an IoT Edge Device from the Azure portal .
* Preparare il dispositivo IoT Edge per [distribuire il modulo IoT Edge dal portale](../iot-edge/how-to-deploy-modules-portal.md)di Azure.

> [!NOTE]
> Per distribuire una macchina virtuale Linux di Azure come dispositivo Perimetrale IoT, vedere questa [guida introduttiva.](../iot-edge/quickstart-linux.md)

## <a name="deploy-sql-database-edge-module-from-azure-marketplace"></a>Distribuire il modulo Edge del database SQL da Azure MarketplaceDeploy SQL Database Edge Module from Azure Marketplace

Azure Marketplace è un marketplace online di servizi e applicazioni dove è possibile trovare un'ampia gamma di soluzioni e applicazioni aziendali certificate e ottimizzate per l'esecuzione in Azure, tra cui i [moduli IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Il perimetro del database SQL di Azure può essere distribuito in un dispositivo perimetrale tramite il marketplace.

1. Trovare il modulo Edge del database SQL di Azure in Azure Marketplace.Find the Azure SQL Database Edge module on the Azure Marketplace.<br><br>

   ![Bordo database SQL in MarketPlace](media/deploy-portal/find-offer-marketplace.png)

2. Selezionare il piano software più adatto alle proprie esigenze e fare clic su **Crea**. <br><br>

   ![Selezionare il piano software corretto](media/deploy-portal/pick-correct-plan.png)

3. Nella pagina Modulo spigoli IoT di destinazione specificare i dettagli seguenti e quindi fare clic su **Crea**

   |**Campo**  |**Descrizione**  |
   |---------|---------|
   |Subscription  |  Sottoscrizione di Azure in base alla quale è stato creato l'hub IoT |
   |Hub IoT   |  Nome dell'hub IoT in cui è registrato il dispositivo IoT Edge e quindi selezionare l'opzione "Distribuisci in un dispositivo"|
   |Nome dispositivo Perimetrale IoT  |  Nome del dispositivo IoT Edge in cui verrebbe distribuito SQL Database Edge |

4. Nella pagina **Imposta moduli** passare alla sezione nei moduli di distribuzione e fare clic su **Configura nel** modulo SQL Database Edge. 

5. Nel **riquadro Moduli personalizzati ioT Edge** specificare i valori desiderati per le variabili di ambiente e/o personalizzare le opzioni di creazione e le proprietà desiderate per il modulo. Per un elenco completo delle variabili di ambiente supportate, fare riferimento alle [variabili di ambiente del contenitore](/sql/linux/sql-server-linux-configure-environment-variables/)di SQL Server .

   |**Parametro**  |**Descrizione**|
   |---------|---------|
   | Nome | Nome del modulo. |
   |SA_PASSWORD  | Specificare una password complessa per l'account amministratore di SQL Database Edge. |
   |MSSQL_LCID   | Imposta l'ID della lingua da usare per SQL Server. Ad esempio, 1036 è il francese. |
   |MSSQL_COLLATION | Imposta le regole di confronto per SQL Server. Questa impostazione sostituisce il mapping predefinito dell'ID lingua (LCID) alle regole di confronto. |

   > [!NOTE]
   > Non modificare o aggiornare **l'URI dell'immagine** o le impostazioni **ACCEPT_EULA** nel modulo.

6. Nel **riquadro Moduli personalizzati IoT Edge** aggiornare il valore di creazione delle opzioni di creazione del contenitore per la **porta host**. Se è necessario distribuire più di un modulo SQL DB Edge, assicurarsi di aggiornare l'opzione mounts per creare una nuova coppia di origine & destinazione per il volume persistente. Per ulteriori informazioni sui montaggi e sul volume, fare riferimento [utilizzare i volumi](https://docs.docker.com/storage/volumes/) nella documentazione della finestra mobile. 

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

7. Nel **riquadro Moduli personalizzati ioT Edge** aggiornare le *proprietà desiderate del modulo gemello Set* per includere il percorso del pacchetto SQL e le informazioni sul processo di analisi del flusso. Questi due campi sono facoltativi e devono essere utilizzati se si desidera distribuire il modulo SQL Database Edge con un database e un processo di streaming.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. Nel **riquadro Moduli personalizzati di IoT Edge** impostare Criterio di *riavvio* su Sempre e *Stato desiderato su* In esecuzione.
9. Nel **riquadro Moduli personalizzati IoT Edge** fare clic su **Salva**.
10. Nella pagina **Imposta moduli** fare clic su **Avanti**.
11. Nella pagina **Specifica percorso (facoltativo)** della pagina **Imposta moduli** specificare le route da modulo a modulo o modulo per la comunicazione IoT Edge Hub, vedere Distribuire moduli e stabilire route [in IoT Edge](../iot-edge/module-composition.md).
12. Fare clic su **Avanti**.
13. Fare clic su **Submit**.

In questa guida introduttiva è stato distribuito un modulo perimetrale del database SQL in un dispositivo Perimetrale IoT.In this quickstart, you deployed a SQL Database Edge Module on an IoT Edge device.

## <a name="next-steps"></a>Passaggi successivi

- [Machine Learning e Intelligenza Artificiale con ONNX in SQL Database Edge](onnx-overview.md).
- Creazione di una soluzione IoT end-to-end con SQL Database Edge tramite Edge IoT.Building an end to end IoT Solution with SQL Database Edge using IoT Edge.
