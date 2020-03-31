---
title: Query a SQL Server Linux Docker container with Azure Databricks
description: Questo articolo descrive come distribuire Azure Databricks nella rete virtuale, nota anche come inserimento della rete virtuale.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 460079248e6cbd939c36b84f94cac41dce4dda2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73747657"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>Esercitazione: Eseguire una query su un contenitore Docker Linux di SQL Server in una rete virtuale da un blocco appunti di Azure DatabricksTutorial: Query a SQL Server Linux Docker container in a virtual network from an Azure Databricks notebook

Questa esercitazione illustra come integrare Azure Databricks con un contenitore Docker Linux di SQL Server in una rete virtuale. 

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Distribuire un'area di lavoro di Azure Databricks in una rete virtualeDeploy an Azure Databricks workspace to a virtual network
> * Installare una macchina virtuale Linux in una rete pubblicaInstall a Linux virtual machine in a public network
> * Installare Docker
> * Installare Microsoft SQL Server nel contenitore docker LinuxInstall Microsoft SQL Server on Linux docker container
> * Eseguire query su SQL Server utilizzando JDBC da un blocco appunti di Databricks

## <a name="prerequisites"></a>Prerequisiti

* Creare [un'area di lavoro Databricks in una rete virtuale.](quickstart-create-databricks-workspace-vnet-injection.md)

* Installare [Ubuntu per Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab).

* Scaricare [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="create-a-linux-virtual-machine"></a>Creare una macchina virtuale Linux

1. Nel portale di Azure selezionare l'icona Macchine **virtuali**. Quindi selezionare **+ Aggiungi**.

    ![Aggiungere una nuova macchina virtuale di AzureAdd new Azure virtual machine](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. Nella scheda **Nozioni di base** scegliere Ubuntu Server 18.04 LTS e modificare le dimensioni della macchina virtuale in B2s. Scegliere un nome utente e una password di amministratore.

    ![Scheda Nozioni di base sulla configurazione della nuova macchina virtuale](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. Passare alla scheda **Rete.** Scegliere la rete virtuale e la subnet pubblica che include il cluster Azure Databricks.Navigate to the Networking tab. Choose the virtual network and the public subnet that includes your Azure Databricks cluster. Selezionare **Revisione e creazione**, quindi **Crea** per distribuire la macchina virtuale.

    ![Scheda Rete della configurazione della nuova macchina virtuale](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. Al termine della distribuzione, passare alla macchina virtuale. Si noti l'indirizzo IP pubblico e la rete/subnet virtuale in **Panoramica**. Selezionare **l'indirizzo IP pubblico**

    ![Panoramica delle macchine virtuali](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. Modificare **L'assegnazione** in **Statico** e immettere **un'etichetta**di nome DNS . Selezionare **Salva**e riavviare la macchina virtuale.

    ![Configurazione dell'indirizzo IP pubblico](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. Selezionare la scheda **Rete** in **Impostazioni**. Si noti che il gruppo di sicurezza di rete creato durante la distribuzione di Azure Databricks è associato alla macchina virtuale. Selezionare **Aggiungi regola porta in ingresso**.

7. Aggiungere una regola per aprire la porta 22 per SSH. Usare le seguenti impostazioni:
    
    |Impostazione|Valore consigliato|Descrizione|
    |-------|---------------|-----------|
    |Source (Sorgente)|Indirizzi IP|Indirizzi IP specifica che il traffico in ingresso proveniente da un indirizzo IP di origine specifico verrà consentito o negato da questa regola.|
    |Indirizzi IP di origine|<il tuo indirizzo IP pubblico\>|Immettere l'indirizzo IP pubblico. Puoi trovare il tuo indirizzo IP pubblico visitando [bing.com](https://www.bing.com/) e cercando **"il mio IP".**|
    |Intervalli di porte di origine|*|Consentire il traffico da qualsiasi porta.|
    |Destination|Indirizzi IP|Indirizzi IP specifica che il traffico in uscita per un indirizzo IP di origine specifico verrà consentito o negato da questa regola.|
    |Indirizzi IP di destinazione|<l'ip pubblico della macchina virtuale\>|Immettere l'indirizzo IP pubblico della macchina virtuale. È possibile trovarlo nella pagina **Panoramica** della macchina virtuale.|
    |Intervalli di porte di destinazione|22|Aprire la porta 22 per SSH.|
    |Priorità|290|Assegnare una priorità alla regola.|
    |Nome|ssh-databricks-tutorial-vm|Assegnare un nome alla regola.|


    ![Aggiungere la regola di sicurezza in ingresso per la porta 22Add inbound security rule for port 22](./media/vnet-injection-sql-server/open-port.png)

8. Aggiungere una regola per aprire la porta 1433 per SQL con le impostazioni seguenti:Add a rule to open port 1433 for SQL with the following settings:

    |Impostazione|Valore consigliato|Descrizione|
    |-------|---------------|-----------|
    |Source (Sorgente)|Qualsiasi|Origine specifica che il traffico in ingresso da un indirizzo IP di origine specifico verrà consentito o negato da questa regola.|
    |Intervalli di porte di origine|*|Consentire il traffico da qualsiasi porta.|
    |Destination|Indirizzi IP|Indirizzi IP specifica che il traffico in uscita per un indirizzo IP di origine specifico verrà consentito o negato da questa regola.|
    |Indirizzi IP di destinazione|<l'ip pubblico della macchina virtuale\>|Immettere l'indirizzo IP pubblico della macchina virtuale. È possibile trovarlo nella pagina **Panoramica** della macchina virtuale.|
    |Intervalli di porte di destinazione|1433|Aprire la porta 22 per SQL Server.Open port 22 for SQL Server.|
    |Priorità|300|Assegnare una priorità alla regola.|
    |Nome|sql-databricks-tutorial-vm|Assegnare un nome alla regola.|

    ![Aggiungere la regola di sicurezza in ingresso per la porta 1433Add inbound security rule for port 1433](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>Eseguire SQL Server in un contenitore DockerRun SQL Server in a Docker container

1. Aprire [Ubuntu per Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)o qualsiasi altro strumento che consenta di SSH nella macchina virtuale. Passare alla macchina virtuale nel portale di Azure e selezionare **Connetti** per ottenere il comando SSH da connettere.

    ![Connettersi alla macchina virtuale](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Immettere il comando nel terminale Ubuntu e immettere la password di amministratore creata durante la configurazione della macchina virtuale.

    ![Accesso SSH del terminale Ubuntu](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. Utilizzare il comando seguente per installare Docker nella macchina virtuale.

    ```bash
    sudo apt-get install docker.io
    ```

    Verificare l'installazione di Docker con il seguente comando:

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

## <a name="create-a-sql-database"></a>Creare un database SQL

1. Aprire SQL Server Management Studio e connettersi al server utilizzando il nome del server e l'autenticazione SQL. Il nome utente di accesso è **SA** e la password è la password impostata nel comando Docker. La password nel comando `Password1234`di esempio è .

    ![Connettersi a SQL Server tramite SQL Server Management StudioConnect to SQL Server using SQL Server Management Studio](./media/vnet-injection-sql-server/ssms-login.png)

2. Dopo aver eseguito correttamente la connessione, selezionare **Nuova query** e immettere il frammento di codice seguente per creare un database, una tabella e inserire alcuni record nella tabella.

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

    ![Query per creare un database di SQL ServerQuery to create a SQL Server database](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>Query SQL Server from Azure Databricks

1. Passare all'area di lavoro di Azure Databricks e verificare di aver creato un cluster come parte dei prerequisiti. Selezionare **quindi Crea un blocco appunti**. Assegnare un nome al blocco appunti, selezionare *Python* come lingua e selezionare il cluster creato.

    ![Nuove impostazioni del notebook Databricks](./media/vnet-injection-sql-server/create-notebook.png)

2. Utilizzare il comando seguente per eseguire il ping dell'indirizzo IP interno della macchina virtuale di SQL Server. Il ping dovrebbe avere esito positivo. In caso contrario, verificare che il contenitore sia in esecuzione ed esaminare la configurazione del gruppo di sicurezza di rete .NETM.

    ```python
    %sh
    ping 10.179.64.4
    ```

    È inoltre possibile utilizzare il comando nslookup per esaminare.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. Dopo aver eseguito correttamente il ping di SQL Server, è possibile eseguire query sul database e sulle tabelle. Eseguire il seguente codice pitone:

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

Passare all'articolo successivo per informazioni su come estrarre, trasformare e caricare i dati usando Azure Databricks.Advance to the next article to learn how to extract, transform, and load data using Azure Databricks.
> [!div class="nextstepaction"]
> [Esercitazione: Estrarre, trasformare e caricare dati usando Azure DatabricksTutorial: Extract, transform, and load data by using Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
