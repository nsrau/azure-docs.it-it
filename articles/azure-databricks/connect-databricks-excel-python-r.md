---
title: Connettersi ad Azure Databricks da Excel, Python o R | Microsoft Docs
description: Informazioni su come usare il driver Simba per connettere Azure Databricks a Excel, Python o R.
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2018
ms.author: nitinme
ms.openlocfilehash: 9daa7d30036d0a0f98d079e03a69c29d11e49664
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Connettersi ad Azure Databricks da Excel, Python o R

In questo articolo viene spiegato come usare il driver ODBC Databricks per connettere Azure Databricks con il linguaggio R, Python o Microsoft Excel. Dopo aver stabilito la connessione, è possibile accedere ai dati in Azure Databricks da client Excel, Python o R. È anche possibile usare i client per analizzare ulteriormente i dati. 

## <a name="prerequisites"></a>prerequisiti

* È necessario disporre di un'area di lavoro di Azure Databricks, un cluster Spark e dati di esempio associati al cluster. Se questi prerequisiti non sono già disponibili, completare l'esercitazione della guida introduttiva [Eseguire un processo Spark in Azure Databricks mediante il portale di Azure](quickstart-create-databricks-workspace-portal.md).

* Scaricare il driver ODBC di Databricks dalla [pagina di download dei driver Databricks](https://databricks.com/spark/odbc-driver-download). Installare la versione a 32 o 64 bit in base all'applicazione da cui si vuole eseguire la connessione ad Azure Databricks. Ad esempio, per connettersi da Excel, installare la versione a 32 bit del driver. Per connettersi da R e Python, installare la versione a 64 bit del driver.

* Configurare un token di accesso personale in Databricks. Per istruzioni, vedere [Token management](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management) (Gestione di token).

## <a name="set-up-a-dsn"></a>Configurare un DSN

Un nome dell'origine dati (DSN) contiene le informazioni relative a una specifica origine dati. Un driver ODBC ha bisogno del DSN per connettersi a un'origine dati. In questa sezione si configura un DSN che è possibile usare con il driver ODBC di Databricks per connettersi ad Azure Databricks da client come Microsoft Excel, Python o R.

1. Dall'area di lavoro di Azure Databricks passare al cluster Databricks.

    ![Aprire il cluster Databricks](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "Aprire il cluster Databricks")

2. Nella scheda **Configurazione** fare clic sulla scheda **JDBC/ODBC** e copiare i valori per **Server Hostname** (Nome host server) e **HTTP Path** (Percorso HTTP). Questi valori sono necessari per seguire la procedura descritta in questo articolo.

    ![Ottenere la configurazione di Databricks](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "Ottenere la configurazione di Databricks")

3. Nel computer avviare l'applicazione **Origine dati ODBC** a 32 bit o 64 bit a seconda dell'applicazione. Per connettersi da Excel, usare la versione a 32 bit. Per connettersi da R e Python, usare la versione a 64 bit.

    ![Avviare ODBC](./media/connect-databricks-excel-python-r/launch-odbc-app.png "Avviare l'app ODBC")

4. Nella scheda **DSN utente** fare clic su **Aggiungi**. Nella finestra di dialogo **Crea nuova origine dati** selezionare **Simba Spark ODBC Driver** (Driver ODBC Spark di Simba) e fare clic su **Fine**.

    ![Avviare ODBC](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "Avviare l'app ODBC")

5. Nella finestra di dialogo di configurazione del **driver ODBC Spark di Simba**:

    ![Configurare il DSN](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "Configurare il DSN")

    La tabella seguente fornisce informazioni sui valori da inserire nella finestra di dialogo.
    
    |Campo  | Valore  |
    |---------|---------|
    |**Nome origine dati**     | Immettere un nome per l'origine dati.        |
    |**Host**     | Immettere il valore copiato dall'area di lavoro di Databricks per il *nome host del server*.        |
    |**Porta**     | Immettere *443*.        |
    |**Autenticazione** > **Meccanismo**     | Selezionare *Nome utente e password*.        |
    |**Nome utente**     | Immettere *token*.        |
    |**Password**     | Immettere il valore del token copiato dall'area di lavoro di Databricks. |
    
    Eseguire i passaggi aggiuntivi seguenti nella finestra di dialogo di configurazione del DSN.
    
    * Fare clic su **HTTP Options** (Opzioni HTTP). Nella finestra di dialogo visualizzata incollare il valore del *percorso HTTP* copiato dall'area di lavoro di Databricks. Fare clic su **OK**.
    * Fare clic su **SSL Options** (Opzioni SSL). Nella finestra di dialogo visualizzata selezionare la casella di controllo **Abilita SSL**. Fare clic su **OK**.
    * Fare clic su **Test** per testare la connessione ad Azure Databricks. Fare clic su **OK** per salvare la configurazione.
    * Nella finestra di dialogo **Amministrazione origine dati ODBC** fare clic su **OK**.

Il DSN è stato configurato. Nelle sezioni successive questo DSN viene usato per stabilire una connessione ad Azure Databricks da Excel, Python o R.

## <a name="connect-from-microsoft-excel"></a>Connettersi da Microsoft Excel

In questa sezione si esegue il pull dei dati da Azure Databricks in Microsoft Excel usando il DSN creato in precedenza. Prima di iniziare, verificare che Microsoft Excel sia installato nel computer in uso. È possibile usare una versione di valutazione di Excel da [collegamento alla versione di valutazione di Microsoft Excel](https://products.office.com/excel).

1. Aprire una cartella di lavoro vuota in Microsoft Excel. Nella barra multifunzione **Dati** fare clic su **Recupera dati**. Fare clic su **Da altre origini** e quindi su **Da ODBC**.

    ![Avviare ODBC da Excel](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "Avviare ODBC da Excel")

2. Nella finestra di dialogo **Da ODBC** selezionare il DSN creato in precedenza e quindi fare clic su **OK**.

    ![Selezionare il DSN](./media/connect-databricks-excel-python-r/excel-select-dsn.png "Selezionare il DSN")

3. Se viene chiesto di immettere le credenziali, per nome utente immettere **token**. Come password, fornire il valore del token recuperato dall'area di lavoro di Databricks.

    ![Inserire le credenziali per Databricks](./media/connect-databricks-excel-python-r/excel-databricks-token.png "Selezionare il DSN")

4. Nella finestra dello strumento di navigazione selezionare la tabella in Databricks che si vuole caricare in Excel e quindi fare clic su **Carica**. 

    ![Caricare i dati in Excel](./media/connect-databricks-excel-python-r/excel-load-data.png "Caricare i dati in Excel")

Dopo aver inserito i dati nella cartella di lavoro di Excel, è possibile eseguire su di essi operazioni di analisi.

## <a name="connect-from-r"></a>Connettersi da R

In questa sezione si usa un IDE per il linguaggio R per fare riferimento a dati disponibili in Azure Databricks. Prima di iniziare, è necessario installare nel computer in uso gli elementi seguenti.

* Un IDE per il linguaggio R. In questo articolo si usa RStudio per il desktop. È possibile installarlo dalla pagina di [download di R Studio](https://www.rstudio.com/products/rstudio/download/).
* Se si usa l'IDE RStudio per il desktop, installare anche Microsoft R Client da [http://aka.ms/rclient/](http://aka.ms/rclient/). 

Avviare RStudio e seguire questa procedura:

- Fare riferimento al pacchetto `RODBC`. Questo consente di stabilire la connessione ad Azure Databricks usando il DSN creato in precedenza.
- Stabilire una connessione usando il DSN.
- Eseguire una query SQL sui dati in Azure Databricks. Nel frammento di codice seguente, *radio_sample_data* è una tabella già esistente in Azure Databricks.
- Eseguire alcune operazioni sulla query per verificare l'output. 

Il frammento di codice seguente esegue queste attività:

    # reference the 'RODBC' package
    require(RODBC)
    
    # establish a connection using the DSN you created earlier
    conn <- odbcConnect("<ENTER DSN NAME HERE>")
    
    # run a SQL query using the connection you created
    res <- sqlQuery(conn, "SELECT * FROM radio_sample_data")
    
    # print out the column names in the query output
    names(res) 
        
    # print out the number of rows in the query output
    nrow (res)

## <a name="connect-from-python"></a>Connettersi da Python

In questa sezione si usa un IDE Python (ad esempio IDLE) fare riferimento a dati disponibili in Azure Databricks. Prima di iniziare, completare i prerequisiti seguenti:

* Installare Python da [qui](https://www.python.org/downloads/). Installando Python da questo collegamento viene installato anche IDLE.

* Da un prompt dei comandi nel computer installare il pacchetto `pyodbc`. Eseguire il comando seguente:

      pip install pyodbc

Aprire IDLE e seguire questa procedura:

- Importare il pacchetto `pyodbc`. Questo consente di stabilire la connessione ad Azure Databricks usando il DSN creato in precedenza.
- Stabilire una connessione usando il DSN creato in precedenza.
-  Eseguire una query SQL usando la connessione creata. Nel frammento di codice seguente, *radio_sample_data* è una tabella già esistente in Azure Databricks.
- Eseguire operazioni sulla query per verificare l'output.

Il frammento di codice seguente esegue queste attività:

```python
# import the `pyodbc` package:
import pyodbc

# establish a connection using the DSN you created earlier
conn = pyodbc.connect("DSN=<ENTER DSN NAME HERE>", autocommit = True)

# run a SQL query using the connection you created
cursor = conn.cursor()
cursor.execute("SELECT * FROM radio_sample_data")

# print the rows retrieved by the query.
for row in cursor.fetchall():
    print(row)

```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle origini da cui è possibile importare dati in Azure Databricks, vedere [Data sources for Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#) (Origini dati per Azure Databricks)


