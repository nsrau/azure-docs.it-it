---
title: Eseguire una query un contenitore Linux Docker di SQL Server in una rete virtuale da un notebook di Azure Databricks
description: Questo articolo descrive come distribuire Azure Databricks per la rete virtuale, noto anche come inserimento di rete virtuale.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 345e07fac30f4ad0c8e9918cb8a1ff0fb8aeb811
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60770790"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>Esercitazione: Eseguire una query un contenitore Linux Docker di SQL Server in una rete virtuale da un notebook di Azure Databricks

Questa esercitazione illustra come integrare Azure Databricks con un contenitore Linux Docker di SQL Server in una rete virtuale. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Distribuire un'area di lavoro Azure Databricks in una rete virtuale
> * Installare una macchina virtuale Linux in una rete pubblica
> * Installare Docker
> * Installare Microsoft SQL Server nel contenitore docker di Linux
> * Query sul Server SQL mediante JDBC da un notebook di Databricks

## <a name="prerequisites"></a>Prerequisiti

* Creare un [area di lavoro di Databricks in una rete virtuale](quickstart-create-databricks-workspace-vnet-injection.md).

* Installare [Ubuntu per Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab).

* Scaricare [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="create-a-linux-virtual-machine"></a>Creare una macchina virtuale Linux

1. Nel portale di Azure, selezionare l'icona **macchine virtuali**. Quindi, selezionare **+ Aggiungi**.

    ![Aggiungere una nuova macchina virtuale di Azure](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. Nel **nozioni di base** scheda, scegliere Ubuntu Server 16.04 LTS. Modificare le dimensioni VM in B1ms, che include una VCPU e 2 GB di RAM. Il requisito minimo per un contenitore Docker per Linux SQL Server è 2 GB. Scegliere un amministratore di nome utente e password.

    ![Scheda elementi di base di configurazione della nuova macchina virtuale](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. Passare il **Networking** scheda. Scegliere la rete virtuale e subnet public che include il cluster Azure Databricks. Selezionare **revisione + Crea**, quindi **crea** per distribuire la macchina virtuale.

    ![Scheda di rete della nuova configurazione della macchina virtuale](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. Una volta completata la distribuzione, passare alla macchina virtuale. Si noti che l'indirizzo IP pubblico e virtuale/subnet di rete nel **Panoramica**. Selezionare il **indirizzo IP pubblico**

    ![Panoramica delle macchine virtuali](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. Modifica il **assegnazione** al **statici** e immettere un **etichetta del nome DNS**. Selezionare **salvare**e riavviare la macchina virtuale.

    ![Configurazione dell'indirizzo IP pubblico](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. Selezionare il **Networking** disponibile nella scheda **impostazioni**. Si noti che il gruppo di sicurezza di rete che è stato creato durante la distribuzione di Azure Databricks è associato alla macchina virtuale. Selezionare **Aggiungi regola porta in ingresso**.

7. Aggiungere una regola per aprire la porta 22 per SSH. Usare le seguenti impostazioni:
    
    |Impostazione|Valore consigliato|Descrizione|
    |-------|---------------|-----------|
    |`Source`|Indirizzi IP|Gli indirizzi IP consente di specificare che il traffico in ingresso da un'origine Specifica indirizzo IP verrà consentito o negato da questa regola.|
    |Indirizzi IP di origine|< l'indirizzo ip pubblico\>|Immettere il proprio indirizzo IP pubblico. È possibile trovare l'indirizzo IP pubblico visitando [bing.com](https://www.bing.com/) e cercando **"my IP"** .|
    |Intervalli di porte di origine|*|Consentire il traffico da qualsiasi porta.|
    |Destination|Indirizzi IP|Gli indirizzi IP consente di specificare che il traffico in uscita per una specifica origine, indirizzo IP verrà consentito o negato da questa regola.|
    |Indirizzi IP di destinazione|< l'indirizzo ip pubblico della macchina virtuale\>|Immettere l'indirizzo IP pubblico della macchina virtuale. È possibile trovarlo nel **Panoramica** pagina della macchina virtuale.|
    |Intervalli di porte di destinazione|22|Aprire la porta 22 per SSH.|
    |Priorità|290|Assegnare una priorità alla regola.|
    |NOME|ssh-databricks-tutorial-vm|Assegnare un nome alla regola.|


    ![Aggiungi regola di sicurezza in ingresso per la porta 22](./media/vnet-injection-sql-server/open-port.png)

8. Aggiungere una regola per aprire la porta 1433 per SQL con le impostazioni seguenti:

    |Impostazione|Valore consigliato|Descrizione|
    |-------|---------------|-----------|
    |`Source`|Indirizzi IP|Gli indirizzi IP consente di specificare che il traffico in ingresso da un'origine Specifica indirizzo IP verrà consentito o negato da questa regola.|
    |Indirizzi IP di origine|10.179.0.0/16|Immettere l'intervallo di indirizzi della rete virtuale.|
    |Intervalli di porte di origine|*|Consentire il traffico da qualsiasi porta.|
    |Destination|Indirizzi IP|Gli indirizzi IP consente di specificare che il traffico in uscita per una specifica origine, indirizzo IP verrà consentito o negato da questa regola.|
    |Indirizzi IP di destinazione|< l'indirizzo ip pubblico della macchina virtuale\>|Immettere l'indirizzo IP pubblico della macchina virtuale. È possibile trovarlo nel **Panoramica** pagina della macchina virtuale.|
    |Intervalli di porte di destinazione|1433|Aprire la porta 22 per SQL Server.|
    |Priorità|300|Assegnare una priorità alla regola.|
    |Name|sql-databricks-tutorial-vm|Assegnare un nome alla regola.|

    ![Aggiungi regola di sicurezza in ingresso per la porta 1433](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>Eseguire SQL Server in un contenitore Docker

1. Aprire [Ubuntu per Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab), o qualsiasi altro strumento che ti permetterà per la connessione SSH alla macchina virtuale. Passare alla macchina virtuale nel portale di Azure e seleziona **Connect** per ottenere il comando SSH è necessario connettersi.

    ![Connettersi alla macchina virtuale](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Immettere il comando nel terminale di Ubuntu e immettere la password dell'amministratore creato durante la configurazione della macchina virtuale.

    ![Ubuntu terminale eseguire l'accesso SSH](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. Usare il comando seguente per installare Docker nella macchina virtuale.

    ```bash
    sudo apt-get install docker.io
    ```

    Per verificare l'installazione di Docker con il comando seguente:

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

1. Aprire SQL Server Management Studio e connettersi al server usando il nome del server e autenticazione di SQL Server. Il nome utente di accesso **SA** e la password è la password impostata nel comando Docker. La password nell'esempio di comando è `Password1234`.

    ![Connettersi a SQL Server usando SQL Server Management Studio](./media/vnet-injection-sql-server/ssms-login.png)

2. Una volta completata la connessione, selezionare **nuova Query** e immettere il seguente frammento di codice per creare un database, una tabella e inserire alcuni record della tabella.

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

    ![Eseguire una query per creare un database di SQL Server](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>Query SQL Server da Azure Databricks

1. Passare all'area di lavoro Azure Databricks e verificare di avere creato un cluster come parte dei prerequisiti. Quindi, selezionare **creare un Notebook**. Assegnare un nome, selezionare il notebook *Python* come il linguaggio e selezionare il cluster creato.

    ![Nuove impostazioni di notebook di Databricks](./media/vnet-injection-sql-server/create-notebook.png)

2. Usare il comando seguente per il ping dell'indirizzo IP interno della macchina virtuale SQL Server. Questo ping dovrebbe essere completato. In caso contrario, verificare che il contenitore è in esecuzione e rivedere la configurazione di gruppo (NSG) di sicurezza di rete.

    ```python
    %sh
    ping 10.179.64.4
    ```

    È anche possibile usare il comando nslookup per esaminare.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. Dopo aver si risponde al ping SQL Server, è possibile eseguire una query di database e tabelle. Eseguire il codice python seguente:

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
