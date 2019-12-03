---
title: Criteri di Apache Hive in Apache Ranger-Azure HDInsight
description: Informazioni su come configurare i criteri di Apache Ranger per Hive in un servizio HDInsight di Azure con Enterprise Security Package.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 9005b2e01cdb17d6aa6c630ec8be3d702d5b138c
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688098"
---
# <a name="configure-apache-hive-policies-in-hdinsight-with-enterprise-security-package"></a>Configurare i criteri per Apache Hive in HDInsight con Enterprise Security Package

Informazioni su come configurare i criteri di Apache Ranger per Apache Hive. In questo articolo vengono creati due criteri di Ranger per limitare l'accesso a hivesampletable. La tabella hivesampletable è disponibile con i cluster HDInsight. Dopo aver configurato i criteri, è possibile usare Excel e il driver ODBC per connettersi alle tabelle hive in HDInsight.

## <a name="prerequisites"></a>Prerequisiti

* Un cluster HDInsight con Enterprise Security Package. Vedere [Configurare i cluster HDInsight con ESP](apache-domain-joined-configure.md).
* Una workstation con Office 2016, Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone oppure Office 2010 Professional Plus.

## <a name="connect-to-apache-ranger-admin-ui"></a>Connettersi all'interfaccia utente di amministrazione di Apache Ranger
**Per connettersi all'interfaccia utente di amministrazione di Ranger**

1. Da un browser, passare all'interfaccia utente di amministrazione di Ranger in `https://CLUSTERNAME.azurehdinsight.net/Ranger/` dove CLUSTERname è il nome del cluster.

   > [!NOTE]  
   > Ranger usa credenziali diverse da quelle del cluster Apache Hadoop. Per evitare che i browser usino credenziali memorizzate nella cache di Hadoop, connettersi all'interfaccia utente di amministrazione di Ranger da una nuova finestra del browser InPrivate.

2. Eseguire l'accesso usando il nome utente e la password di amministratore cluster:

    ![Home page di HDInsight ESP Ranger](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    Attualmente, Ranger è compatibile solo con Yarn e Hive.

## <a name="create-domain-users"></a>Creazione di utenti del dominio

Per informazioni su come creare hiveruser1 e hiveuser2, vedere [Creare un cluster HDInsight con ESP](apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp). In questo articolo vengono usati i due account utente.

## <a name="create-ranger-policies"></a>Creazione dei criteri di Ranger

In questa sezione vengono creati due criteri di Ranger per accedere a hivesampletable. Vengono concesse autorizzazioni di selezione su diversi set di colonne. Entrambi gli utenti sono stati creati tramite la procedura descritta in [Creare un cluster HDInsight con ESP](apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp). Nella sezione successiva verranno testati i due criteri in Excel.

**Per creare criteri di Ranger**

1. Aprire l'interfaccia utente di amministrazione di Ranger. Consultare la sezione Connettersi all'interfaccia utente di amministrazione di Apache Ranger.
2. Selezionare **CLUSTERNAME_Hive**, in **hive**. Verranno visualizzati due criteri preconfigurati.
3. Selezionare **Aggiungi nuovo criterio**, quindi immettere i valori seguenti:

    |Proprietà |Value |
    |---|---|
    |Nome criterio|Read-hivesampletable-all|
    |Database hive|default|
    |table|hivesampletable|
    |Colonna hive|*|
    |Seleziona utente|hiveuser1|
    |autorizzazioni|seleziona|

    ![Configurazione di HDInsight ESP Ranger hive Policy](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

    > [!NOTE]  
    > Se l'utente di un dominio non compare in Seleziona utente, attendere alcuni istanti per la sincronizzazione di Ranger con AAD.

4. Selezionare **Aggiungi** per salvare il criterio.

5. Ripetere gli ultimi due passaggi per creare un altro criterio con le proprietà seguenti:

    |Proprietà |Value |
    |---|---|
    |Nome criterio|Read-hivesampletable-devicemake|
    |Database hive|default|
    |table|hivesampletable|
    |Colonna hive|ClientID, devicemake|
    |Seleziona utente|hiveuser2|
    |autorizzazioni|seleziona|

## <a name="create-hive-odbc-data-source"></a>Creare un'origine dati Hive ODBC

Le istruzioni sono disponibili in [Creare un'origine dati Hive ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).  

 | Proprietà  |Description |
 | --- | --- |
 | Data Source Name | Assegnare un nome all'origine dati |
 | Host | Immettere CLUSTERNAME.azurehdinsight.net. Ad esempio, myHDICluster.azurehdinsight.net |
 | Porta | Utilizzare **443**. Questa porta è passata da 563 a 443. |
 | Database | Usare l'**impostazione predefinita**. |
 | Hive Server Type | Selezionare **Hive Server 2** |
 | Mechanism | Selezionare **Azure HDInsight Service** |
 | HTTP Path | Lasciare vuoto. |
 | User Name | Immettere hiveuser1@contoso158.onmicrosoft.com. Se è diverso, aggiornare il nome di dominio. |
 | Password | Immettere la password per hiveuser1. |

Assicurarsi di fare clic su **Test** prima di salvare l'origine dati.

## <a name="import-data-into-excel-from-hdinsight"></a>Importazione di dati in Excel da HDInsight

Nell'ultima sezione sono stati configurati due criteri.  hiveuser1 dispone dell'autorizzazione di selezione in tutte le colonne, mentre hiveuser2 solo in due di loro. In questa sezione vengono rappresentati due utenti che importano dati in Excel.

1. Aprire una cartella di lavoro nuova o esistente in Excel.

1. Nella scheda **Dati** passare a **Carica dati** > **Da altre origini** > **Da ODBC** per aprire la finestra **Da ODBC**.

    ![Aprire la Connessione guidata dati](./media/apache-domain-joined-run-hive/simbahiveodbc-excel-dataconnection1.png)

1. Nell'elenco a discesa selezionare il nome dell'origine dati creato nell'ultima sezione e quindi fare clic su **OK**.

1. Per il primo utilizzo, viene visualizzata una finestra di dialogo del **driver ODBC** . Selezionare **Windows** dal menu a sinistra. Selezionare quindi **Connetti** per aprire la finestra **strumento di navigazione** .

1. Attendere l'apertura della finestra di dialogo **Seleziona database e tabella** . Questa operazione potrebbe richiedere alcuni secondi.

1. Selezionare **hivesampletable**e quindi fare clic su **Avanti**.

1. Selezionare **Fine**.

1. Nella finestra di dialogo **Importa dati** è possibile modificare o specificare la query. A tale scopo, selezionare **Proprietà**. Questa operazione potrebbe richiedere alcuni secondi.

1. Selezionare la scheda **definizione** . Il testo del comando è:

       SELECT * FROM "HIVE"."default"."hivesampletable"

   In base ai criteri di Ranger che sono stati definiti, hiveuser1 dispone dell'autorizzazione di selezione su tutte le colonne.  Quindi, questa query funziona con le credenziali di hiveuser1, ma questa query non funziona con le credenziali di quelle.

1. Selezionare **OK** per chiudere la finestra di dialogo Proprietà connessione.

1. Selezionare **OK** per chiudere la finestra di dialogo **Importa dati** .  

1. Reimmettere la password per hiveuser1, quindi fare clic su **OK**. L'importazione dei dati in Excel potrebbe richiedere alcuni secondi. Al termine, verranno visualizzate 11 colonne di dati.

Per il test del secondo criterio (read-hivesampletable-devicemake) creato nell'ultima sezione

1. Aggiungere un nuovo foglio in Excel.
2. Seguire l'ultima procedura per importare i dati.  L'unica differenza è che vengono usate le credenziali di hiveuser2 anziché quelle di hiveuser1. L'esito è negativo perché hiveuser2 ha solo l'autorizzazione per visualizzare due colonne. Verrà visualizzato l'errore seguente:

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
        
3. Seguire la stessa procedura per importare i dati. Questa volta usare le credenziali di hiveuser2 e modificare l'istruzione di selezione da:

        SELECT * FROM "HIVE"."default"."hivesampletable"

    in:

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    Al termine, verranno visualizzate due colonne di dati importati.

## <a name="next-steps"></a>Passaggi successivi

* Per configurare un cluster HDInsight con Enterprise Security Package, vedere [Configurare i cluster HDInsight con ESP](apache-domain-joined-configure.md).
* Per gestire un cluster HDInsight con ESP, vedere [Gestire i cluster HDInsight con ESP](apache-domain-joined-manage.md).
* Per eseguire query Hive usando SSH nei cluster HDInsight con ESP, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
* Per connettere Hive usando Hive JDBC, vedere [Connettersi ad Apache Hive in Azure HDInsight con il driver Hive JDBC](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md)
* Per connettere Excel a Hadoop usando ODBC, vedere [Connettere Excel ad Apache Hadoop mediante Microsoft Hive ODBC Driver](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* Per connettere Excel a Hadoop usando Power Query, vedere [Connettere Excel ad Apache Hadoop mediante Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md)
