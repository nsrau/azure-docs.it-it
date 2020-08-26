---
title: 'Avvio rapido: Creare un cluster ML Services usando un modello - Azure HDInsight'
description: Questa guida di avvio rapido illustra come usare un modello di Resource Manager per creare un cluster ML Services in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: 7f86d0a090409385497715b70e8199bdf48c6995
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88640328"
---
# <a name="quickstart-create-ml-services-cluster-in-azure-hdinsight-using-arm-template"></a>Avvio rapido: Creare un cluster ML Services in Azure HDInsight con un modello di Resource Manager

Questa guida di avvio rapido illustra come usare un modello di Azure Resource Manager per creare un cluster [ML Services](./r-server-overview.md) in Azure HDInsight. Microsoft Machine Learning Server è disponibile come opzione di distribuzione quando si creano i cluster HDInsight in Azure. Il tipo di cluster che offre questa opzione è denominato ML Services. Questa funzionalità offre ai data scientist, agli statistici e ai programmatori in R l'accesso su richiesta a metodi di analisi scalabili e distribuiti su HDInsight.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-rserver%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/).

:::code language="json" source="~/quickstart-templates/101-hdinsight-rserver/azuredeploy.json":::

Nel modello sono definite due risorse di Azure:

* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) per creare un account di archiviazione di Azure.
* [Microsoft.HDInsight/cluster](/azure/templates/microsoft.hdinsight/clusters): creare un cluster HDInsight.

## <a name="deploy-the-template"></a>Distribuire il modello

1. Selezionare il pulsante **Distribuisci in Azure** sotto per accedere ad Azure e aprire il modello di Resource Manager.

    [![Distribuzione in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-rserver%2Fazuredeploy.json)

1. Immettere o selezionare i valori seguenti:

    |Proprietà |Descrizione |
    |---|---|
    |Subscription|Nell'elenco a discesa selezionare la sottoscrizione di Azure che viene usata per il cluster.|
    |Resource group|Nell'elenco a discesa selezionare il gruppo di risorse esistente oppure selezionare **Crea nuovo**.|
    |Location|Come valore verrà inserita automaticamente la località usata per il gruppo di risorse.|
    |Cluster Name|Immettere un nome univoco globale. Per questo modello usare solo lettere minuscole e numeri.|
    |Nome utente dell'account di accesso del cluster|Specificare il nome utente. Il valore predefinito è **admin**.|
    |Password di accesso al cluster|Specificare una password. La password deve avere una lunghezza minima di 10 caratteri e contenere almeno una cifra, una lettera maiuscola, una lettera minuscola e un carattere non alfanumerico, ad eccezione di ' " `. |
    |Nome utente SSH|Specificare il nome utente. Il valore predefinito è sshuser.|
    |Password SSH|Specificare la password.|

    ![Modello di Resource Manager per HBase](./media/quickstart-resource-manager-template/resource-manager-template-rserver.png)

1. Leggere quanto riportato in **CONDIZIONI**. Selezionare quindi **Accetto le condizioni riportate sopra** e infine **Acquista**. Si riceverà una notifica che informa che la distribuzione è in corso. La creazione di un cluster richiede circa 20 minuti.

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

Al termine della creazione del cluster, si riceverà una notifica con il messaggio **La distribuzione è riuscita** e un collegamento **Vai alla risorsa**. Nella pagina del gruppo di risorse saranno presenti il nuovo cluster HDInsight e l'account di archiviazione predefinito associato. Ogni cluster ha una dipendenza da un [account di archiviazione di Azure](../hdinsight-hadoop-use-blob-storage.md) o un [account Azure Data Lake Storage](../hdinsight-hadoop-use-data-lake-store.md). Viene indicato come account di archiviazione predefinito. Il cluster HDInsight e il relativo account di archiviazione predefinito devono avere un percorso condiviso nella stessa area di Azure. L'eliminazione dei cluster non comporta l'eliminazione dell'account di archiviazione.

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'argomento di avvio rapido, può essere opportuno eliminare il cluster. Con HDInsight, i dati vengono archiviati in Archiviazione di Azure ed è possibile eliminare tranquillamente un cluster quando non è in uso. Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, eliminare i cluster quando non vengono usati è una scelta economicamente conveniente.

Nel portale di Azure passare al cluster e selezionare **Elimina**.

![Modello di Resource Manager per HBase](./media/quickstart-resource-manager-template/azure-portal-delete-rserver.png)

È anche possibile selezionare il nome del gruppo di risorse per aprire la pagina del gruppo di risorse e quindi selezionare **Elimina gruppo di risorse**. Eliminando il gruppo di risorse, si elimina sia il cluster HDInsight che l'account di archiviazione predefinito.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato creato un cluster ML Services in HDInsight tramite un modello di Resource Manager. L'articolo successivo illustra come eseguire uno script R con RStudio Server per una dimostrazione dell'uso di Spark per calcoli R distribuiti.

> [!div class="nextstepaction"]
> [Eseguire uno script R in un cluster ML Services in Azure HDInsight con RStudio Server](./machine-learning-services-quickstart-job-rstudio.md)
