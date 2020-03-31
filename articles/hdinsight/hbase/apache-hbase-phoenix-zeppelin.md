---
title: Eseguire query di base Apache in Azure HDInsight con Apache PhoenixRun Apache Base queries in Azure HDInsight with Apache Phoenix
description: Scopri come usare Apache zeppelin per eseguire query Apache Base con Phoenix.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: 28eeb446e55213f1ffa0a638878f6432fd15a05a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72392241"
---
# <a name="use-apache-zeppelin-to-run-apache-phoenix-queries-over-apache-hbase-in-azure-hdinsight"></a>Usare Apache per eseguire query Apache Phoenix su Apache HBase in Azure HDInsightUse Apache zeppelin to run Apache Phoenix queries over Apache HBase in Azure HDInsight

Apache Phoenix è un livello database relazionale open source altamente parallelo basato su HBase. Phoenix consente di utilizzare query SQL like su HBase. Phoenix utilizza i driver JDBC sottostanti per consentire di creare, eliminare, modificare tabelle SQL, indici, viste e sequenze.  È inoltre possibile utilizzare Phoenix per aggiornare le righe singolarmente e in blocco. Phoenix utilizza una compilazione nativa NOSQL anziché usare MapReduce per compilare query, consentendo la creazione di applicazioni a bassa latenza in HBase.

Apache è un notebook open source basato sul Web che consente di creare documenti collaborativi basati sui dati utilizzando l'analisi interattiva dei dati e linguaggi come SQL e Scala. Aiuta gli sviluppatori di dati & i data scientist a sviluppare, organizzare, eseguire e condividere codice per la manipolazione dei dati. Consente di visualizzare i risultati senza fare riferimento alla riga di comando o aver bisogno dei dettagli del cluster.

Gli utenti di HDInsight possono usare Apache per eseguire query sulle tabelle Phoenix. Con il cluster HDInsight è integrato con il cluster HDInsight e non sono disponibili ulteriori passaggi per usarlo. È sufficiente creare un notebook zeppelin con interprete JDBC e iniziare a scrivere le query Phoenix SQL

## <a name="prerequisites"></a>Prerequisiti

Un cluster Apache HBase in HDInsight.An Apache HBase cluster on HDInsight. Vedere [Introduzione a Apache HBase](./apache-hbase-tutorial-get-started-linux.md).

## <a name="create-an-apache-zeppelin-note"></a>Creare una nota di Apache Zeppelin

1. Nell'URL `https://CLUSTERNAME.azurehdinsight.net/zeppelin` seguente sostituire `CLUSTERNAME` con il nome del cluster. Immettere quindi l'URL in un Web browser. Immettere il nome utente e la password dell'account di accesso del cluster.

1. Nella pagina Eppelin, selezionare **Crea nuova nota**.

    ![Zeppelin Interactive Query HDInsight](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-create-note.png)

1. Nella finestra di dialogo **Create new note** (Crea una nuova nota) digitare o selezionare i valori seguenti:

    - Nome nota: immettere un nome per la nota.
    - Interprete predefinito: selezionare **jdbc** dall'elenco a discesa.

    Quindi selezionare **Crea nota**.

1. Verificare che l'intestazione del blocco appunti mostri uno stato connesso. È indicato da un punto verde nell'angolo in alto a destra.

    ![Stato di notebook Zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-connected.png "Stato di notebook Zeppelin")

1. Creare una tabella HBase. Immettere il comando seguente e quindi premere **Maiusc e INVIO:**

    ```sql
    %jdbc(phoenix)
    CREATE TABLE Company (
        company_id INTEGER PRIMARY KEY,
        name VARCHAR(225)
    );
    ```

    L'istruzione **%jdbc(phoenix)** nella prima riga indica al blocco appunti di utilizzare l'interprete JDBC Phoenix.

1. Visualizzare le tabelle create.

    ```sql
    %jdbc(phoenix)
    SELECT DISTINCT table_name
    FROM SYSTEM.CATALOG
    WHERE table_schem is null or table_schem <> 'SYSTEM';
    ```

1. Inserire i valori nella tabella.

    ```sql
    %jdbc(phoenix)
    UPSERT INTO dbo.Company VALUES(1, 'Microsoft');
    UPSERT INTO dbo.Company (name, company_id) VALUES('Apache', 2);
    ```

1. Eseguire una query sulla tabella.

    ```sql
    %jdbc(phoenix)
    SELECT * FROM dbo.Company;
    ```

1. Eliminare un record.

    ```sql
    %jdbc(phoenix)
    DELETE FROM dbo.Company WHERE COMPANY_ID=1;
    ```

1. Eliminare la tabella.

    ```sql
    %jdbc(phoenix)
    DROP TABLE dbo.Company;
    ```

## <a name="next-steps"></a>Passaggi successivi

- [Apache Phoenix ora supporta lo zeppelin in Azure HDInsight](https://blogs.msdn.microsoft.com/ashish/2018/08/17/apache-phoenix-now-supports-zeppelin-in-azure-hdinsight/)
- [Apache Phoenix grammatica](https://phoenix.apache.org/language/index.html)
