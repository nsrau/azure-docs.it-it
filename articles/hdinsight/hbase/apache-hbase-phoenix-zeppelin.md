---
title: Eseguire query Apache di base in Azure HDInsight con Apache Phoenix
description: Informazioni su come usare Apache Zeppelin per eseguire query Apache di base con Phoenix.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: 28eeb446e55213f1ffa0a638878f6432fd15a05a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392241"
---
# <a name="use-apache-zeppelin-to-run-apache-phoenix-queries-over-apache-hbase-in-azure-hdinsight"></a>Usare Apache Zeppelin per eseguire query Apache Phoenix su Apache HBase in Azure HDInsight

Apache Phoenix è un livello di database relazionale open source altamente parallelo basato su HBase. Phoenix consente di usare query simili a SQL su HBase. Phoenix usa i driver JDBC sottostanti per consentire la creazione, l'eliminazione, la modifica di tabelle, indici, viste e sequenze SQL.  È anche possibile usare Phoenix per aggiornare le righe singolarmente e in blocco. Phoenix usa una compilazione nativa NOSQL anziché MapReduce per compilare query, consentendo la creazione di applicazioni a bassa latenza su HBase.

Apache Zeppelin è un notebook basato sul web open source che consente di creare documenti basati sui dati e collaborativi usando i linguaggi e l'analisi interattiva dei dati, ad esempio SQL e scala. Consente agli sviluppatori di dati & data scientist di sviluppare, organizzare, eseguire e condividere codice per la manipolazione dei dati. Consente di visualizzare i risultati senza fare riferimento alla riga di comando o se sono necessari i dettagli del cluster.

Gli utenti di HDInsight possono usare Apache Zeppelin per eseguire query sulle tabelle Phoenix. Apache Zeppelin è integrato con il cluster HDInsight e non ci sono altri passaggi da usare. È sufficiente creare un notebook Zeppelin con l'interprete JDBC e iniziare a scrivere le query SQL Phoenix

## <a name="prerequisites"></a>Prerequisiti

Un cluster Apache HBase in HDInsight. Vedere [Introduzione ad Apache HBase](./apache-hbase-tutorial-get-started-linux.md).

## <a name="create-an-apache-zeppelin-note"></a>Creare una nota di Apache Zeppelin

1. Nell'URL `https://CLUSTERNAME.azurehdinsight.net/zeppelin` seguente sostituire `CLUSTERNAME` con il nome del cluster. Immettere quindi l'URL in un Web browser. Immettere il nome utente e la password dell'account di accesso del cluster.

1. Dalla pagina Zeppelin selezionare **Crea nuova nota**.

    ![Zeppelin Interactive Query HDInsight](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-create-note.png)

1. Nella finestra di dialogo **Create new note** (Crea una nuova nota) digitare o selezionare i valori seguenti:

    - Nome Nota: immettere un nome per la nota.
    - Interprete predefinito: selezionare **JDBC** dall'elenco a discesa.

    Quindi selezionare **Crea nota**.

1. Verificare che l'intestazione del notebook mostri uno stato connesso. È indicato da un punto verde nell'angolo superiore destro.

    ![Stato notebook Zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-connected.png "Stato di notebook Zeppelin")

1. Creare una tabella HBase. Immettere il comando seguente e quindi premere **MAIUSC + INVIO**:

    ```sql
    %jdbc(phoenix)
    CREATE TABLE Company (
        company_id INTEGER PRIMARY KEY,
        name VARCHAR(225)
    );
    ```

    L'istruzione **% JDBC (Phoenix)** nella prima riga indica al notebook di usare l'interprete JDBC per Phoenix.

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

- [Apache Phoenix ora supporta Zeppelin in Azure HDInsight](https://blogs.msdn.microsoft.com/ashish/2018/08/17/apache-phoenix-now-supports-zeppelin-in-azure-hdinsight/)
- [Grammatica Apache Phoenix](https://phoenix.apache.org/language/index.html)
