---
title: Configurare criteri Hive in HDInsight aggiunto al dominio - Azure | Microsoft Docs
description: Informazioni su...
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 3fade1e5-c2e1-4ad5-b371-f95caea23f6d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/25/2016
ms.author: saurinsh
ms.openlocfilehash: 9523c008d509647c6e578bc9ca9bb88b348d6ebf
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2017
---
# <a name="configure-hive-policies-in-domain-joined-hdinsight-preview"></a>Configurare criteri Hive in HDInsight aggiunto al dominio (anteprima)
Informazioni su come configurare i criteri di Apache Ranger per Hive. In questo articolo vengono creati due criteri di Ranger per limitare l'accesso a hivesampletable. La tabella hivesampletable è disponibile con i cluster HDInsight. Dopo aver configurato i criteri, usare Excel e il driver ODBC per connettersi alle tabelle Hive in HDInsight.

## <a name="prerequisites"></a>Prerequisiti
* Un cluster HDInsight aggiunto al dominio. Vedere [Configure Domain-joined HDInsight clusters](apache-domain-joined-configure.md) (Configurare i cluster HDInsight aggiunti al dominio).
* Una workstation con Office 2016, Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone oppure Office 2010 Professional Plus.

## <a name="connect-to-apache-ranger-admin-ui"></a>Connettersi all'interfaccia utente di amministrazione di Apache Ranger
**Per connettersi all'interfaccia utente di amministrazione di Ranger**

1. Da un browser, connettersi all'interfaccia utente di amministrazione di Ranger. L'URL è https://&lt;ClusterName>.azurehdinsight.net/Ranger/.

   > [!NOTE]
   > Ranger usa credenziali diverse da quelle del cluster Hadoop. Per evitare che i browser usino credenziali memorizzate nella cache di Hadoop, connettersi all'interfaccia utente di amministrazione di Ranger da una nuova finestra del browser anonima.
   >
   >
2. Eseguire l'accesso usando il nome utente e la password di amministratore cluster:

    ![Home page di Ranger con dominio aggiunto a HDInsight](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    Attualmente, Ranger è compatibile solo con Yarn e Hive.

## <a name="create-domain-users"></a>Creazione di utenti del dominio
In [Configure Domain-joined HDInsight clusters](apache-domain-joined-configure.md#create-and-configure-azure-ad-ds-for-your-azure-ad) (Configurare i cluster HDInsight aggiunti al dominio) sono stati creati hiveruser1 e hiveuser2. In questa esercitazione verranno usati i due account utente.

## <a name="create-ranger-policies"></a>Creazione dei criteri di Ranger
In questa sezione verranno creati due criteri di Ranger per accedere a hivesampletable. Vengono concesse autorizzazioni di selezione su diversi set di colonne. Entrambi gli utenti sono stati creati nella procedura [Configure Domain-joined HDInsight clusters](apache-domain-joined-configure.md#create-and-configure-azure-ad-ds-for-your-azure-ad) (Configurare i cluster HDInsight aggiunti al dominio).  Nella sezione successiva verranno verificati i due criteri in Excel.

**Per creare criteri di Ranger**

1. Aprire l'interfaccia utente di amministrazione di Ranger. Vedere [Connettersi all'interfaccia utente di amministrazione di Apache Ranger](#connect-to-apache-ranager-admin-ui).
2. Fare clic su **&lt;ClusterName>_hive** in **Hive**. Verranno visualizzati due criteri preconfigurati.
3. Fare clic su **Aggiungi nuovo criterio**, quindi immettere i valori seguenti:

   * Nome criterio: read-hivesampletable-all
   * Database Hive: predefinito
   * Tabella: hivesampletable
   * Colonna Hive: *
   * Seleziona utente: hiveuser1
   * Autorizzazioni: selezionare

     ![Configurazione dei criteri di Hive Ranger con dominio aggiunto a HDInsight](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

     > [!NOTE]
     > Se l'utente di un dominio non compare in Seleziona utente, attendere alcuni istanti per la sincronizzazione di Ranger con AAD.
     >
     >
4. Fare clic su **Aggiungi** per salvare il criterio.
5. Ripetere gli ultimi due passaggi per creare un altro criterio con le proprietà seguenti:

   * Nome criterio: read-hivesampletable-devicemake
   * Database Hive: predefinito
   * Tabella: hivesampletable
   * Colonna Hive: clientid, devicemake
   * Seleziona utente: hiveuser2
   * Autorizzazioni: selezionare

## <a name="create-hive-odbc-data-source"></a>Creare un'origine dati Hive ODBC
Le istruzioni sono disponibili in [Creare un'origine dati Hive ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).  

 | Proprietà  |Descrizione |
 | --- | --- |
 | Data Source Name | Assegnare un nome all'origine dati |
 | Host | Immettere &lt;HDInsightClusterName>.azurehdinsight.net. Ad esempio, myHDICluster.azurehdinsight.net |
 | Porta | Utilizzare **443**. Questa porta è passata da 563 a 443. |
 | Database | Usare l'**impostazione predefinita**. |
 | Hive Server Type | Selezionare **Hive Server 2** |
 | Mechanism | Selezionare **Azure HDInsight Service** |
 | HTTP Path | Lasciare vuoto. |
 | User Name | Immettere hiveuser1@contoso158.onmicrosoft.com. Se diverso, aggiornare il nome di dominio. |
 | Password | Immettere la password per hiveuser1. |

Assicurarsi di fare clic su **Test** prima di salvare l'origine dati.

## <a name="import-data-into-excel-from-hdinsight"></a>Importazione di dati in Excel da HDInsight
Nell'ultima sezione, sono stati configurati due criteri.  hiveuser1 dispone dell'autorizzazione di selezione in tutte le colonne, mentre hiveuser2 solo in due di loro. In questa sezione vengono rappresentati due utenti che importano dati in Excel.

1. Aprire una cartella di lavoro nuova o esistente in Excel.
2. Nella scheda **Dati** fare clic su **From Other Data Sources** (Da altre origini dati) e quindi su **From Data Connection Wizard** (Da Connessione guidata dati) per avviare la **Connessione guidata dati**.

    ![Aprire Connessione guidata dati][img-hdi-simbahiveodbc.excel.dataconnection]
3. Selezionare **Nome origine dati ODBC DSN** come origine dati e quindi fare clic su **Avanti**.
4. Nelle origini dati ODBC selezionare il nome dell'origine dati creato nel passaggio precedente e quindi fare clic su **Avanti**.
5. Immettere nuovamente la password per il cluster nella procedura guidata e quindi fare clic su **OK**. Attendere l'apertura della finestra di dialogo **Seleziona database e tabella** . Questa operazione potrebbe richiedere alcuni secondi.
6. Selezionare **hivesampletable**, quindi fare clic su **Avanti**.
7. Fare clic su **Fine**.
8. Nella finestra di dialogo **Importa dati** è possibile modificare o specificare la query. A questo scopo, fare clic su **Proprietà**. Questa operazione potrebbe richiedere alcuni secondi.
9. Fare clic sulla scheda **Definizione**. Il testo del comando è:

       SELECT * FROM "HIVE"."default"."hivesampletable"

   In base ai criteri di Ranger che sono stati definiti, hiveuser1 dispone dell'autorizzazione di selezione su tutte le colonne.  Pertanto, questa query funziona con le credenziali di hiveuser1, ma non con quelle di hiveuser2.

   ![Proprietà di connessione][img-hdi-simbahiveodbc-excel-connectionproperties]
10. Fare clic su **OK** per chiudere la finestra di dialogo Proprietà connessione.
11. Fare clic su **OK** per chiudere la finestra di dialogo **Importa dati**.  
12. Reimmettere la password per hiveuser1, quindi fare clic su **OK**. L'importazione dei dati in Excel potrebbe richiedere alcuni secondi. Al termine, verranno visualizzate 11 colonne di dati.

Per il test del secondo criterio (read-hivesampletable-devicemake) creato nell'ultima sezione

1. Aggiungere un nuovo foglio in Excel.
2. Seguire l'ultima procedura per importare i dati.  L'unica differenza è che verranno usate le credenziali di hiveuser2 anziché quelle di hiveuser1. L'esito sarà negativo perché hiveuser2 dispone solo dell'autorizzazione per visualizzare due colonne. Verrà visualizzato l'errore seguente:

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
3. Seguire la stessa procedura per importare i dati. Questa volta usare le credenziali di hiveuser2 e modificare l'istruzione di selezione da:

        SELECT * FROM "HIVE"."default"."hivesampletable"

    in:

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    Al termine, verranno visualizzate due colonne di dati importati.

## <a name="next-steps"></a>Passaggi successivi
* Per configurare un cluster HDInsight aggiunto al dominio, vedere [Configure Domain-joined HDInsight clusters](apache-domain-joined-configure.md) (Configurare i cluster HDInsight aggiunti al dominio).
* Per gestire cluster HDInsight aggiunti al dominio, vedere [Manage Domain-joined HDInsight clusters](apache-domain-joined-manage.md) (Gestire i cluster HDInsight aggiunti al dominio).
* Per eseguire query Hive usando SSH nei cluster HDInsight aggiunti al dominio, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
* Per connettere Hive usando JDBC, vedere [Connettersi a Hive in Azure HDInsight con il driver Hive JDBC](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md).
* Per connettere Excel a Hadoop usando ODBC, vedere [Connettere Excel a Hadoop mediante Microsoft Hive ODBC Driver](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* Per connettere Excel a Hadoop usando Power Query, vedere [Connettere Excel a Hadoop mediante Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
