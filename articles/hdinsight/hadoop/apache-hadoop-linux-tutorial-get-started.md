---
title: 'Avvio rapido: Creare cluster Apache Hadoop in Azure HDInsight con un modello di Resource Manager'
description: Questo argomento di avvio rapido illustra come creare cluster Apache Hadoop in Azure HDInsight con un modello di Resource Manager
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: b41283ea5ac16aaa6ea7f992d42775d3fe231b59
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88642300"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-arm-template"></a>Avvio rapido: Creare un cluster Apache Hadoop in Azure HDInsight usando un modello di Resource Manager

Questa guida di avvio rapido illustra come usare un modello di Azure Resource Manager per creare un cluster [Apache Hadoop](./apache-hadoop-introduction.md) in Azure HDInsight. Hadoop era il framework open source originale per l'elaborazione distribuita e l'analisi di set di Big Data in cluster. L'ecosistema Hadoop include software e utilità correlate, tra cui Apache Hive, Apache HBase, Spark, Kafka e molti altri.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]
  
HDInsight attualmente viene fornito con [sette diversi tipi di cluster](../hdinsight-overview.md#cluster-types-in-hdinsight). Ogni tipo di cluster supporta un set diverso di componenti. Tutti i tipi di cluster supportano Hive. Per un elenco dei componenti supportati in HDInsight, vedere [Novità delle versioni cluster di Hadoop incluse in HDInsight](../hdinsight-component-versioning.md)  

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/).

:::code language="json" source="~/quickstart-templates/101-hdinsight-linux-ssh-password/azuredeploy.json":::

Nel modello sono definite due risorse di Azure:

* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) per creare un account di archiviazione di Azure.
* [Microsoft.HDInsight/cluster](/azure/templates/microsoft.hdinsight/clusters): creare un cluster HDInsight.

## <a name="deploy-the-template"></a>Distribuire il modello

1. Selezionare il pulsante **Distribuisci in Azure** sotto per accedere ad Azure e aprire il modello di Resource Manager.

    [![Distribuzione in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json)

1. Immettere o selezionare i valori seguenti:

    |Proprietà  |Descrizione  |
    |---------|---------|
    |Subscription|Nell'elenco a discesa selezionare la sottoscrizione di Azure che viene usata per il cluster.|
    |Resource group|Nell'elenco a discesa selezionare il gruppo di risorse esistente oppure selezionare **Crea nuovo**.|
    |Location|Come valore verrà inserita automaticamente la località usata per il gruppo di risorse.|
    |Cluster Name|Immettere un nome univoco globale. Per questo modello usare solo lettere minuscole e numeri.|
    |Tipo di cluster | Selezionare **hadoop**. |
    |Nome utente dell'account di accesso del cluster|Specificare il nome utente. Il valore predefinito è **admin**.|
    |Password di accesso al cluster|Specificare una password. La password deve avere una lunghezza minima di 10 caratteri e contenere almeno una cifra, una lettera maiuscola, una lettera minuscola e un carattere non alfanumerico, ad eccezione di ' " `. |
    |Nome utente SSH|Specificare il nome utente. Il valore predefinito è **sshuser**.|
    |Password SSH|Specificare la password.|

    Alcune proprietà sono state impostate come hardcoded nel modello.  È possibile configurare questi valori dal modello. Per una spiegazione più approfondita di queste proprietà, vedere [Creare cluster Apache Hadoop in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

    > [!NOTE]  
    > I valori immessi devono essere univoci e devono seguire le linee guida sulle convenzioni di denominazione. Il modello non esegue controlli di convalida. Se i valori immessi sono già in uso o non seguono le linee guida, viene visualizzato un errore dopo l'invio del modello.  

    ![Modello iniziale di Resource Manager per HDInsight basato su Linux nel portale](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "Distribuzione del cluster Hadoop in HDInsight con il portale di Azure e un modello di gestione del gruppo di risorse")

1. Leggere quanto riportato in **CONDIZIONI**. Selezionare quindi **Accetto le condizioni riportate sopra** e infine **Acquista**. Si riceverà una notifica che informa che la distribuzione è in corso. La creazione di un cluster richiede circa 20 minuti.

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

Al termine della creazione del cluster, si riceverà una notifica con il messaggio **La distribuzione è riuscita** e un collegamento **Vai alla risorsa**. Nella pagina del gruppo di risorse saranno presenti il nuovo cluster HDInsight e l'account di archiviazione predefinito associato. Ogni cluster ha una dipendenza da un [account di archiviazione di Azure](../hdinsight-hadoop-use-blob-storage.md) o un [account Azure Data Lake Storage](../hdinsight-hadoop-use-data-lake-store.md). Viene indicato come account di archiviazione predefinito. Il cluster HDInsight e il relativo account di archiviazione predefinito devono avere un percorso condiviso nella stessa area di Azure. L'eliminazione dei cluster non comporta l'eliminazione dell'account di archiviazione.

> [!NOTE]  
> Per altri metodi di creazione di cluster e per informazioni sulle proprietà usate in questo argomento di avvio rapido, vedere [Creare cluster HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'argomento di avvio rapido, può essere opportuno eliminare il cluster. Con HDInsight, i dati vengono archiviati in Archiviazione di Azure ed è possibile eliminare tranquillamente un cluster quando non è in uso. Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, eliminare i cluster quando non vengono usati è una scelta economicamente conveniente.

> [!NOTE]  
> Se si procede *subito* con l'esercitazione successiva per imparare come eseguire le operazioni ETL mediante Hadoop in HDInsight, è possibile mantenere il cluster in esecuzione, poiché nell'esercitazione è necessario creare nuovamente un cluster Hadoop. Se invece non si prevede di passare subito all'esercitazione successiva, è necessario eliminare il cluster ora.

Nel portale di Azure passare al cluster e selezionare **Elimina**.

![Eliminazione del cluster HDInsight dal portale](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "Eliminazione del cluster HDInsight dal portale")

È anche possibile selezionare il nome del gruppo di risorse per aprire la pagina del gruppo di risorse e quindi selezionare **Elimina gruppo di risorse**. Eliminando il gruppo di risorse, si elimina sia il cluster HDInsight che l'account di archiviazione predefinito.

## <a name="next-steps"></a>Passaggi successivi

Questo argomento di avvio rapido illustra come creare un cluster Apache Hadoop in HDInsight tramite un modello di Resource Manager. Passare all'articolo successivo per informazioni su come eseguire un'operazione di estrazione, trasformazione e caricamento (ETL) usando Hadoop in HDInsight.

> [!div class="nextstepaction"]
> [Estrarre, trasformare e caricare dati usando Interactive Query su HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
