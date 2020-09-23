---
title: Distribuire Azure SQL Edge usando il portale di Azure
description: Informazioni su come distribuire Azure SQL Edge usando il portale di Azure
keywords: distribuire SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: ffd967797a4e586387a0385169672220727f78a7
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90886759"
---
# <a name="deploy-azure-sql-edge"></a>Distribuire SQL Edge di Azure 

Azure SQL Edge è un motore di database relazionale ottimizzato per le distribuzioni di Azure IoT Edge e Internet. Fornisce funzionalità per la creazione di un livello di elaborazione e archiviazione dei dati a prestazioni elevate per le applicazioni e le soluzioni IoT. Questo argomento di avvio rapido illustra come iniziare a creare un modulo SQL Edge di Azure mediante Azure IoT Edge usando il portale di Azure.

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

4. Nella pagina **imposta moduli nel dispositivo:** fare clic sul modulo Azure SQL Edge in **moduli IOT Edge**. Il nome del modulo predefinito è impostato su *AzureSQLEdge*. 

5. Nella sezione *Impostazioni modulo* del pannello **Aggiorna IOT Edge modulo** specificare i valori desiderati per il nome del *modulo di IOT Edge*, il *criterio di riavvio* e *lo stato desiderato*. 

   > [!IMPORTANT]    
   > Non modificare o aggiornare le impostazioni dell' **URI dell'immagine** nel modulo.

6. Nella sezione *variabili di ambiente* del pannello **Aggiorna IOT Edge modulo** specificare i valori desiderati per le variabili di ambiente. Per un elenco completo delle variabili di ambiente di Azure SQL Edge, vedere [configurare usando le variabili di ambiente](configure.md#configure-by-using-environment-variables). Per il modulo sono definite le seguenti variabili di ambiente predefinite. 

   |**Parametro**  |**Descrizione**|
   |---------|---------|
   | Nome | Nome per il modulo. |
   | MSSQL_SA_PASSWORD  | Modificare il valore predefinito per specificare una password complessa per l'account amministratore di SQL Edge. |
   | MSSQL_LCID   | Modificare il valore predefinito per impostare l'ID lingua desiderato da usare per SQL Edge. Ad esempio, 1036 corrisponde al francese. |
   | MSSQL_COLLATION | Modificare il valore predefinito per impostare le regole di confronto predefinite per SQL Edge. Questa impostazione sostituisce il mapping predefinito dell'ID lingua (LCID) per le regole di confronto. |

   > [!IMPORTANT]    
   > Non modificare o aggiornare la variabile di ambiente **ACCEPT_EULA** per il modulo.

7. Nella sezione *contenitore crea opzioni* del pannello **Aggiorna IOT Edge modulo** aggiornare le opzioni seguenti in base ai requisiti. 
   - **Porta host:** Eseguire il mapping della porta host specificata alla porta 1433 (porta SQL predefinita) nel contenitore.
   - **Binding** e **montaggi:** se è necessario distribuire più di un modulo di SQL Edge, assicurarsi di aggiornare l'opzione di montaggio per creare una nuova coppia di destinazione & origine per il volume permanente. Per altre informazioni sui montaggi e sul volume, fare riferimento a [Usare volumi](https://docs.docker.com/storage/volumes/) nella documentazione di Docker. 

   ```json
   {
    "HostConfig": {
        "CapAdd": [
            "SYS_PTRACE"
        ],
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
        "ClientTransportType=AMQP_TCP_Only",
        "PlanId=asde-developer-on-iot-edge"
    ]
   }
   ```
   > [!IMPORTANT]    
   > Non modificare la `PlanId` variabile di ambiente definita nell'impostazione Crea configurazione. Se questo valore viene modificato, il contenitore Edge di Azure SQL non verrà avviato. 
   
8. Nel riquadro **aggiorna IOT Edge modulo** fare clic su **Aggiorna**.
9. Nella pagina **imposta moduli nel dispositivo** fare clic su **avanti: Route >** se è necessario definire le route per la distribuzione. In caso contrario, fare clic su **Verifica + crea**. Per ulteriori informazioni sulla configurazione delle route, vedere [deploy Modules and establish routes in IOT Edge](../iot-edge/module-composition.md).
11. Nel riquadro **aggiorna IOT Edge modulo** fare clic su **Crea**.

## <a name="connect-to-azure-sql-edge"></a>Connettersi a SQL Edge di Azure

La procedura seguente usa lo strumento da riga di comando di SQL Edge di Azure, **sqlcmd**, all'interno del contenitore per stabilire la connessione a SQL Edge di Azure.

> [!NOTE]      
> Gli strumenti da riga di comando di SQL (sqlcmd) non sono disponibili all'interno della versione ARM64 dei contenitori Edge di Azure SQL.

1. Usare il comando `docker exec -it` per avviare una shell Bash interattiva all'interno del contenitore in esecuzione. Nell'esempio seguente `azuresqledge` è il nome specificato dal `Name` parametro del modulo IOT Edge.

   ```bash
   sudo docker exec -it azuresqledge "bash"
   ```

2. Una volta all'interno del contenitore, eseguire la connessione in locale con sqlcmd. Sqlcmd non è incluso nel percorso per impostazione predefinita, quindi occorre specificare il percorso completo.

   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "<YourNewStrong@Passw0rd>"
   ```

   > [!TIP]    
   > È possibile omettere la password nella riga di comanda perché sia richiesto di essere immessa.

3. Se la connessione viene eseguita correttamente, il prompt dei comandi **sqlcmd** sarà: `1>`.

## <a name="create-and-query-data"></a>Creare i dati e recuperarli tramite query

Nelle sezioni seguenti viene descritto l'uso di **sqlcmd** e Transact-SQL per creare un nuovo database, aggiungere dati ed eseguire una query.

### <a name="create-a-new-database"></a>Creare un nuovo database

La seguente procedura consente di creare un nuovo database denominato `TestDB`.

1. Dal prompt dei comandi **sqlcmd** incollare il comando seguente di Transact-SQL per creare un database di test:

   ```sql
   CREATE DATABASE TestDB
   Go
   ```

2. Nella riga successiva scrivere una query perché vengano restituiti i nomi di tutti database nel server:

   ```sql
   SELECT Name from sys.Databases
   Go
   ```

### <a name="insert-data"></a>Inserire i dati

Creare poi una nuova tabella `Inventory` e inserire due nuove righe.

1. Dal prompt dei comandi **sqlcmd** spostare il contesto nel nuovo database `TestDB`:

   ```sql
   USE TestDB
   ```

2. Creare una nuova tabella denominata `Inventory`:

   ```sql
   CREATE TABLE Inventory (id INT, name NVARCHAR(50), quantity INT)
   ```

3. Inserire i dati nella nuova tabella:

   ```sql
   INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (2, 'orange', 154);
   ```

4. Digitare `GO` per eseguire i comandi precedenti:

   ```sql
   GO
   ```

### <a name="select-data"></a>Selezionare i dati

A questo punto, eseguire una query per restituire i dati dalla tabella `Inventory`.

1. Dal prompt dei comandi **sqlcmd** immettere una query che restituisca le righe dalla tabella `Inventory` che ne contiene oltre 152:

   ```sql
   SELECT * FROM Inventory WHERE quantity > 152;
   ```

2. Eseguire il comando seguente:

   ```sql
   GO
   ```

### <a name="exit-the-sqlcmd-command-prompt"></a>Uscire dal prompt dei comandi sqlcmd

1. Per terminare la sessione **sqlcmd**, digitare `QUIT`:

   ```sql
   QUIT
   ```

2. Per uscire dal prompt dei comandi interattivo nel contenitore, digitare `exit`. Dopo la chiusura della shell Bash interattiva, il contenitore continua l'esecuzione.

## <a name="connect-from-outside-the-container"></a> Eseguire la connessione dall'esterno del contenitore

È possibile connettersi ed eseguire query SQL sull'istanza di Azure SQL Edge da qualsiasi strumento Linux, Windows o macOS esterno che supporta le connessioni SQL. Per altre informazioni sulla connessione a un contenitore di SQL Edge dall'esterno, vedere [Connettersi ed eseguire query in SQL Edge di Azure](https://docs.microsoft.com/azure/azure-sql-edge/connect).

In questo argomento di avvio rapido è stato distribuito un modulo SQL Edge in un dispositivo IoT Edge. 

## <a name="next-steps"></a>Passaggi successivi

- [Machine Learning e intelligenza artificiale con ONNX in SQL Edge](onnx-overview.md)
- [Creazione di una soluzione end-to-end con SQL Edge usando IoT Edge](tutorial-deploy-azure-resources.md)
- [Streaming dei dati in SQL Edge di Azure](stream-data.md)
- [Risolvere gli errori relativi alla distribuzione](troubleshoot.md)
