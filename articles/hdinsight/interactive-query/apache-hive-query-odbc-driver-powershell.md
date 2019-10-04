---
title: Eseguire query in Apache Hive con ODBC Driver e PowerShell - Azure HDInsight
description: Usare Microsoft Hive ODBC Driver e PowerShell per eseguire query su cluster Apache Hive in Azure HDInsight.
keywords: hive, odbc hive, powershell
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 06/27/2019
ms.openlocfilehash: 04771ddc633c210ce8c7b3c42a9e46cb2f1ed349
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122164"
---
# <a name="tutorial-query-apache-hive-with-odbc-and-powershell"></a>Esercitazione: Eseguire query in Apache Hive con ODBC e PowerShell

I driver ODBC di Microsoft rappresentano un modo flessibile per interagire con tipi diversi di origini dati, tra cui Apache Hive. È possibile scrivere codice in linguaggi di script, ad esempio PowerShell, che usano i driver ODBC per stabilire una connessione al cluster Hive, passare la query desiderata e visualizzare i risultati.

In questa esercitazione si eseguiranno le attività seguenti:

> [!div class="checklist"]
> * Scaricare e installare Microsoft Hive ODBC Driver
> * Creare un'origine dati ODBC Apache Hive collegata al cluster
> * Eseguire query su informazioni di esempio dal cluster tramite PowerShell

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione sono necessari gli elementi seguenti:

* Un cluster Interactive Query in HDInsight. Per crearne uno, vedere [Introduzione all'uso di Hadoop in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Selezionare **Interactive Query** come tipo di cluster.

## <a name="install-microsoft-hive-odbc-driver"></a>Installare Microsoft Hive ODBC driver

Scaricare e installare [Microsoft Hive ODBC driver](https://go.microsoft.com/fwlink/?LinkID=286698).

## <a name="create-apache-hive-odbc-data-source"></a>Creare un'origine dati Apache Hive ODBC

I passaggi seguenti descrivono come creare un'origine dati ODBC Apache Hive.

1. In Windows scegliere **Start** > **Strumenti di amministrazione Windows** > **Configura origini dati ODBC (32 bit)/(64 bit)** .  Verrà aperta la finestra **Amministrazione origine dati ODBC**.

    ![Amministrazione origine dati ODBC](./media/apache-hive-query-odbc-driver-powershell/hive-odbc-driver-dsn-setup.png "Configurare un DSN usando l'amministrazione origine dati ODBC")

1. Nella scheda **DSN utente** selezionare **Aggiungi** per aprire la finestra **Crea nuova origine dati**.

1. Selezionare **Microsoft Hive ODBC Driver** e quindi selezionare **Fine** per aprire la finestra di configurazione del **DSN per Microsoft Hive ODBC Driver.**

1. Digitare o selezionare i valori seguenti:

   | Proprietà | DESCRIZIONE |
   | --- | --- |
   |  Data Source Name |Assegnare un nome all'origine dati |
   |  Host |Immettere `CLUSTERNAME.azurehdinsight.net`. Ad esempio: `myHDICluster.azurehdinsight.net` |
   |  Porta |Utilizzare **443**.|
   |  Database |Usare l'**impostazione predefinita**. |
   |  Mechanism |Selezionare **Servizio HDInsight di Microsoft Azure** |
   |  User Name |Immettere il nome utente HTTP del cluster HDInsight. Il nome utente predefinito è **admin**. |
   |  Password |Immettere la password utente del cluster HDInsight. Selezionare la casella di controllo **Save Password (Encrypted)** (Salva password (crittografata)).|

1. Facoltativo: selezionare **Opzioni avanzate**.  

   | Parametro | DESCRIZIONE |
   | --- | --- |
   |  Use Native Query |Quando è selezionato, il driver ODBC NON cerca di convertire TSQL in HiveQL. Usare questa opzione solo se sussiste la massima sicurezza che si stanno inviando istruzioni HiveQL pure. Quando ci si connette al database SQL di Azure o SQL Server, è consigliabile lasciarlo deselezionato. |
   |  Rows fetched per block |Quando si recupera un numero elevato di record, potrebbe essere necessario ottimizzare questo parametro per assicurare prestazioni ottimali. |
   |  Default string column length, Binary column length, Decimal column scale |Le lunghezze e le precisioni del tipo di dati potrebbero avere effetto sulla visualizzazione dei dati. In questo caso verranno restituite informazioni non corrette a causa della perdita di precisione e dei troncamenti. |

    ![Opzioni di configurazione DNS avanzate](./media/apache-hive-query-odbc-driver-powershell/odbc-data-source-advanced-options.png "Opzioni di configurazione DSN avanzate")

1. Selezionare **Test** per testare l'origine dati. Se l'origine dati è configurata correttamente, come risultato del test è indicato **OPERAZIONE RIUSCITA**.  

1. Selezionare **OK** per chiudere la finestra Test.  

1. Selezionare **OK** per chiudere la finestra di configurazione del **** DSN per Microsoft Hive ODBC Driver.  

1. Selezionare **OK** per chiudere la finestra **Amministratore origine dati ODBC**.  

## <a name="query-data-with-powershell"></a>Eseguire query sui dati con PowerShell

Lo script PowerShell seguente è una funzione che usa ODBC per eseguire query in un cluster Hive.

```powershell
function Get-ODBC-Data {

   param(
   [string]$query=$(throw 'query is required.'),
   [string]$dsn,  
   [PSCredential] $cred = (Get-Credential)  
   )

   $conn = New-Object System.Data.Odbc.OdbcConnection
   $uname = $cred.UserName

   $pswd = (New-Object System.Net.NetworkCredential -ArgumentList "", $cred.Password).Password
   $conn.ConnectionString = "DSN=$dsn;Uid=$uname;Pwd=$pswd;"
   $conn.open()
   $cmd = New-object System.Data.Odbc.OdbcCommand($query,$conn)

   $ds = New-Object system.Data.DataSet

   (New-Object system.Data.odbc.odbcDataAdapter($cmd)).fill($ds) #| out-null
   $conn.close()
   $ds.Tables
}
```

Il frammento di codice seguente usa la funzione precedente per eseguire una query sul cluster Interactive Query creato all'inizio dell'esercitazione. Sostituire `DATASOURCENAME` con il valore di **Nome origine dati** specificato nella schermata **Microsoft Hive ODBC Driver DSN Setup** (Configurazione DSN Microsoft Hive ODBC Driver). Quando vengono richieste le credenziali, immettere il nome utente e la password specificati in **Nome utente dell'account di accesso del cluster** e in **Password dell'account di accesso del cluster** al momento della creazione del cluster.

```powershell

$dsn = "DATASOURCENAME"

$query = "select count(distinct clientid) AS total_clients from hivesampletable"

Get-ODBC-Data -query $query -dsn $dsn
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, il cluster HDInsight e l'account di archiviazione. A tale scopo, selezionare il gruppo di risorse in cui è stato creato il cluster e fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare Microsoft Hive ODBC Driver e PowerShell per recuperare dati dal cluster Interactive Query di Azure HDInsight.

> [!div class="nextstepaction"]
> [Connettere Excel ad Apache Hive tramite ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
