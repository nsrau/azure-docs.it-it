---
title: Eseguire una query su un contenitore Docker SQL Server Linux con Azure Databricks
description: Questo articolo descrive come distribuire Azure Databricks alla rete virtuale, nota anche come VNet injection.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 773ffe264446e6a4d9ef2e88634e4f2c9b8aeb45
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72273974"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>Esercitazione: Eseguire query in un contenitore Docker di SQL Server Linux in una rete virtuale da un notebook di Azure Databricks

Questa esercitazione illustra come integrare Azure Databricks con un contenitore Docker Linux SQL Server in una rete virtuale. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Distribuire un'area di lavoro Azure Databricks a una rete virtuale
> * Installare una macchina virtuale Linux in una rete pubblica
> * Installare Docker
> * Installare Microsoft SQL Server in Linux contenitore Docker
> * Eseguire query sul SQL Server usando JDBC da un notebook di databricks

## <a name="prerequisites"></a>Prerequisiti

* Creare un' [area di lavoro di databricks in una rete virtuale](quickstart-create-databricks-workspace-vnet-injection.md).

* Installare [Ubuntu per Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab).

* Scaricare [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="create-a-linux-virtual-machine"></a>Creare una macchina virtuale Linux

1. Nella portale di Azure selezionare l'icona per le **macchine virtuali**. Quindi selezionare **+ Aggiungi**.

    ![Aggiungi nuova macchina virtuale di Azure](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. Nella scheda **nozioni di base** scegliere Ubuntu Server 16,04 LTS. Modificare le dimensioni della macchina virtuale in B1ms, che include un vCPU e 2 GB di RAM. Il requisito minimo per un contenitore Docker di Linux SQL Server è 2 GB. Scegliere un nome utente e una password di amministratore.

    ![Scheda nozioni di base della nuova configurazione della macchina virtuale](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. Passare alla scheda **rete** . Scegliere la rete virtuale e la subnet pubblica che include il cluster Azure Databricks. Selezionare **Verifica + crea**, quindi **Crea** per distribuire la macchina virtuale.

    ![Scheda rete della nuova configurazione della macchina virtuale](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. Al termine della distribuzione, passare alla macchina virtuale. Si notino l'indirizzo IP pubblico e la rete virtuale/subnet nella **Panoramica**. Selezionare l' **indirizzo IP pubblico**

    ![Panoramica delle macchine virtuali](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. Modificare l' **assegnazione** in **static** e immettere un' **etichetta del nome DNS**. Selezionare **Salva**e riavviare la macchina virtuale.

    ![Configurazione degli indirizzi IP pubblici](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. Selezionare la scheda **rete** in **Impostazioni**. Si noti che il gruppo di sicurezza di rete creato durante la distribuzione del Azure Databricks è associato alla macchina virtuale. Selezionare **Aggiungi regola porta in ingresso**.

7. Aggiungere una regola per aprire la porta 22 per SSH. Usare le seguenti impostazioni:
    
    |Impostazione|Valore suggerito|Descrizione|
    |-------|---------------|-----------|
    |Source|Indirizzi IP|Indirizzi IP specifica che il traffico in ingresso da un indirizzo IP di origine specifico verrà consentito o negato da questa regola.|
    |Indirizzi IP di origine|< IP pubblico @ no__t-0|Immettere l'indirizzo IP pubblico. È possibile trovare l'indirizzo IP pubblico visitando [Bing.com](https://www.bing.com/) e cercando **"My IP"** .|
    |Source port ranges|*|Consentire il traffico da qualsiasi porta.|
    |Destination|Indirizzi IP|Indirizzi IP specifica che il traffico in uscita per un indirizzo IP di origine specifico verrà consentito o negato da questa regola.|
    |Indirizzi IP di destinazione|< l'IP pubblico della VM @ no__t-0|Immettere l'indirizzo IP pubblico della macchina virtuale. È possibile trovarlo nella pagina **Panoramica** della macchina virtuale.|
    |Intervalli di porte di destinazione|22|Aprire la porta 22 per SSH.|
    |Priority|290|Assegnare una priorità alla regola.|
    |NOME|SSH-databricks-esercitazione-VM|Assegnare un nome alla regola.|


    ![Aggiungere una regola di sicurezza in ingresso per la porta 22](./media/vnet-injection-sql-server/open-port.png)

8. Aggiungere una regola per aprire la porta 1433 per SQL con le impostazioni seguenti:

    |Impostazione|Valore suggerito|Descrizione|
    |-------|---------------|-----------|
    |Source|Indirizzi IP|Indirizzi IP specifica che il traffico in ingresso da un indirizzo IP di origine specifico verrà consentito o negato da questa regola.|
    |Indirizzi IP di origine|10.179.0.0/16|Immettere l'intervallo di indirizzi per la rete virtuale.|
    |Source port ranges|*|Consentire il traffico da qualsiasi porta.|
    |Destination|Indirizzi IP|Indirizzi IP specifica che il traffico in uscita per un indirizzo IP di origine specifico verrà consentito o negato da questa regola.|
    |Indirizzi IP di destinazione|< l'IP pubblico della VM @ no__t-0|Immettere l'indirizzo IP pubblico della macchina virtuale. È possibile trovarlo nella pagina **Panoramica** della macchina virtuale.|
    |Intervalli di porte di destinazione|1433|Aprire la porta 22 per SQL Server.|
    |Priority|300|Assegnare una priorità alla regola.|
    |NOME|sql-databricks-tutorial-vm|Assegnare un nome alla regola.|

    ![Aggiungere una regola di sicurezza in ingresso per la porta 1433](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>Eseguire SQL Server in un contenitore Docker

1. Aprire [Ubuntu per Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)o qualsiasi altro strumento che consentirà di connettersi tramite SSH alla macchina virtuale. Passare alla macchina virtuale nel portale di Azure e selezionare **Connetti** per ottenere il comando SSH che è necessario connettere.

    ![Connettersi alla macchina virtuale](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Immettere il comando nel terminale di Ubuntu e immettere la password amministratore creata durante la configurazione della macchina virtuale.

    ![Accesso SSH al terminale Ubuntu](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. Usare il comando seguente per installare Docker nella macchina virtuale.

    ```bash
    sudo apt-get install docker.io
    ```

    Verificare l'installazione di Docker con il comando seguente:

    ```bash
    sudo docker --version
    ```

4. Installare l'immagine.

    ```bash
    sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
    ```

    Controllare le immagini.

    ```bash
    sudo docker images
    ```

5. Eseguire il contenitore dall'immagine.

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    Verificare che il contenitore sia in esecuzione.

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>Creazione di un database SQL

1. Aprire SQL Server Management Studio e connettersi al server utilizzando il nome del server e l'autenticazione SQL. Il nome utente di accesso è **sa** e la password è la password impostata nel comando di Docker. La password nel comando di esempio è `Password1234`.

    ![Connettersi a SQL Server tramite SQL Server Management Studio](./media/vnet-injection-sql-server/ssms-login.png)

2. Una volta stabilita la connessione, selezionare **nuova query** e immettere il frammento di codice seguente per creare un database, una tabella e inserire alcuni record nella tabella.

    ```SQL
    CREATE DATABASE MYDB;
    GO
    USE MYDB;
    CREATE TABLE states(Name VARCHAR(20), Capitol VARCHAR(20));
    INSERT INTO states VALUES ('Delaware','Dover');
    INSERT INTO states VALUES ('South Carolina','Columbia');
    INSERT INTO states VALUES ('Texas','Austin');
    SELECT * FROM states
    GO
    ```

    ![Query per creare un database di SQL Server](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>SQL Server di query da Azure Databricks

1. Passare all'area di lavoro Azure Databricks e verificare che sia stato creato un cluster come parte dei prerequisiti. Selezionare quindi **creare un notebook**. Assegnare un nome al notebook, selezionare *Python* come lingua e selezionare il cluster creato.

    ![Nuove impostazioni del notebook di databricks](./media/vnet-injection-sql-server/create-notebook.png)

2. Usare il comando seguente per effettuare il ping dell'indirizzo IP interno della macchina virtuale SQL Server. Questo ping dovrebbe avere esito positivo. In caso contrario, verificare che il contenitore sia in esecuzione e controllare la configurazione del gruppo di sicurezza di rete (NSG).

    ```python
    %sh
    ping 10.179.64.4
    ```

    È inoltre possibile utilizzare il comando nslookup per verificare.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. Una volta eseguito il ping del SQL Server, è possibile eseguire una query sul database e sulle tabelle. Eseguire il codice Python seguente:

    ```python
    jdbcHostname = "10.179.64.4"
    jdbcDatabase = "MYDB"
    userName = 'SA'
    password = 'Password1234'
    jdbcPort = 1433
    jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, userName, password)

    df = spark.read.jdbc(url=jdbcUrl, table='states')
    display(df)
    ```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, l'area di lavoro di Azure Databricks e tutte le risorse correlate. L'eliminazione del processo consente di evitare la fatturazione non necessaria. Se si prevede di usare l'area di lavoro di Azure Databricks in futuro, è possibile arrestare il cluster e riavviarlo in un secondo momento. Se non si intende continuare a usare quest'area di lavoro di Azure Databricks, eliminare tutte le risorse create in questa esercitazione seguendo questa procedura:

1. Scegliere **Gruppi di risorse** dal menu a sinistra del portale di Azure e quindi fare clic sul nome del gruppo di risorse creato.

2. Nella pagina del gruppo di risorse selezionare **Elimina**, digitare il nome della risorsa da eliminare nella casella di testo e quindi selezionare di nuovo **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo per informazioni su come estrarre, trasformare e caricare i dati usando Azure Databricks.
> [!div class="nextstepaction"]
> [Esercitazione: Estrarre, trasformare e caricare dati con Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
