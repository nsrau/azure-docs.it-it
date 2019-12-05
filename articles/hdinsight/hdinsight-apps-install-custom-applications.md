---
title: Installare le applicazioni Apache Hadoop personalizzate in Azure HDInsight
description: Informazioni su come installare applicazioni HDInsight per cluster Apache Hadoop in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: c109f5309837de8c9b4bd3e4bc5a5da0a6da534e
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806865"
---
# <a name="install-custom-apache-hadoop-applications-on-azure-hdinsight"></a>Installare applicazioni Apache Hadoop personalizzate in Azure HDInsight

Questo articolo illustra come installare un'applicazione [Apache Hadoop](https://hadoop.apache.org/) in Azure HDInsight, che non è stata pubblicata nel portale di Azure. L'applicazione che verrà installata in questo articolo è [Hue](https://gethue.com/).

Un'applicazione HDInsight è un'applicazione che gli utenti possono installare in un cluster HDInsight.  Queste applicazioni possono essere sviluppate da Microsoft, da fornitori di software indipendenti (ISV) o dall'utente.  

## <a name="prerequisites"></a>Prerequisiti

Per installare applicazioni HDInsight in un cluster HDInsight esistente, è necessario un cluster HDInsight. Per crearne uno, vedere [Creare cluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). È anche possibile installare applicazioni HDInsight quando si crea un cluster HDInsight.

## <a name="install-hdinsight-applications"></a>Installare applicazioni HDInsight

Le applicazioni HDInsight possono essere installate quando si crea un cluster o in un cluster HDInsight esistente. Per definire i modelli di Azure Resource Manager, vedere [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN: Installare un'applicazione HDInsight).

File necessari per distribuire questa applicazione (Hue):

* [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): modello di Azure Resource Manager per installare l'applicazione HDInsight. Per sviluppare il proprio modello di Azure Resource Manager, vedere [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: Installare un'applicazione HDInsight).
* [hue-install_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh): azione script chiamata dal modello di Resource Manager per configurare il nodo perimetrale.
* [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): file binario hue chiamato da hui-install_v0.sh.
* [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): file binario hue chiamato da hui-install_v0.sh.
* [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz): applicazione Web di esempio (Tomcat) chiamata da hui-install_v0.sh.

### <a name="to-install-hue-to-an-existing-hdinsight-cluster"></a>Per installare Hue in un cluster HDInsight esistente

1. Selezionare l'immagine seguente per accedere ad Azure e aprire il modello di Gestione risorse nel portale di Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Il modello di Resource Manager è disponibile in [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue).  Per informazioni su come scrivere questo modello di Azure Resource Manager, vedere [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN: Installare un'applicazione HDInsight).

1. Selezionare il **gruppo di risorse** esistente che contiene il cluster dall'elenco a discesa. È necessario usare lo stesso gruppo di risorse del cluster.

1. immettere il nome del cluster in cui installare l'applicazione. Deve essere un cluster esistente.

1. Selezionare la casella di controllo per accetto **i termini e le condizioni indicati in precedenza**.

1. Selezionare **Acquisto**.

È possibile visualizzare lo stato dell'installazione dal riquadro aggiunto al dashboard del portale e dalla notifica del portale facendo clic sull'icona a forma di campana nella parte superiore del portale.  Sono necessari circa 10 minuti per installare l'applicazione.

### <a name="to-install-hue-while-creating-a-cluster"></a>Per installare Hue durante la creazione di un cluster

1. Selezionare l'immagine seguente per accedere ad Azure e aprire il modello di Gestione risorse nel portale di Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Il modello di Resource Manager è disponibile in [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json).  Per informazioni su come scrivere questo modello di Azure Resource Manager, vedere [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN: Installare un'applicazione HDInsight).

2. Seguire le istruzioni per creare il cluster e installare Hue. Per altre informazioni sulla creazione di cluster HDInsight, vedere [Creare cluster Hadoop basati su Linux in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

### <a name="other-installation-methods"></a>Altri metodi di installazione

Oltre al portale di Azure è anche possibile usare [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-powershell) e l'[interfaccia della riga di comando di Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-azure-cli) per chiamare modelli di Resource Manager.

## <a name="validate-the-installation"></a>Convalidare l'installazione

È possibile controllare lo stato dell'applicazione nel portale di Azure per convalidare l'installazione dell'applicazione. Inoltre, è possibile convalidare tutti gli endpoint HTTP come previsto e la pagina Web, se disponibile.

Per **Hue**è possibile seguire questa procedura:

### <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il cluster in cui è stata installata l'applicazione.
1. Scegliere **applicazioni**dal menu **Impostazioni** .
1. Selezionare **Hue** nell'elenco per visualizzare le proprietà.  
1. Selezionare il collegamento pagina Web per convalidare il sito Web.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Sostituire `CLUSTERNAME`e `RESOURCEGROUP` con i valori rilevanti, quindi immettere i comandi seguenti:

* Per elencare tutte le applicazioni per il cluster HDInsight.

    ```azurecli
    az hdinsight application list --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

* Per recuperare le proprietà dell'applicazione specificata.

    ```azurecli
    az hdinsight application show --name hue --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

## <a name="troubleshoot-the-installation"></a>Risolvere i problemi di installazione

È possibile controllare lo stato dell'installazione dell'applicazione dalla notifica del portale facendo clic sull'icona a forma di campana nella parte superiore del portale.

Se l'installazione di un'applicazione non è riuscita, è possibile visualizzare i messaggi di errore e le informazioni di debug da tre posizioni:

* Applicazioni HDInsight: informazioni generali sull'errore.

    Aprire il cluster dal portale e selezionare applicazioni da impostazioni:

    ![applicazioni HDInsight errore di installazione dell'applicazione](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-error.png)

* Azione script di HDInsight: se il messaggio di errore delle applicazioni di HDInsight indica un errore dell'azione script, nel pannello Azioni script verranno presentati altri dettagli sull'errore di script.

    Selezionare azione script da impostazioni. In Cronologia azione script vengono visualizzati i messaggi di errore.

    ![applicazioni HDInsight errore di azione script](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-script-action-error.png)

* Interfaccia utente Web di Apache Ambari: se lo script di installazione ha causato l'errore, usare l'interfaccia utente Web di Ambari per controllare i log completi sugli script di installazione.

    Per altre informazioni, vedere [Risoluzione dei problemi](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting).

## <a name="remove-hdinsight-applications"></a>Rimuovere applicazioni HDInsight

### <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il cluster in cui è stata installata l'applicazione.
1. Scegliere **applicazioni**dal menu **Impostazioni** .
1. Fare clic con il pulsante destro del mouse sull'applicazione che si desidera rimuovere, quindi scegliere **Elimina**.
1. Selezionare **Sì** per confermare.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Sostituire `NAME`, `CLUSTERNAME`e `RESOURCEGROUP` con i valori rilevanti, quindi immettere il comando seguente:

```azurecli
az hdinsight application delete --name NAME --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
```

## <a name="next-steps"></a>Passaggi successivi

* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN: Installare un'applicazione HDInsight): informazioni su come sviluppare modelli di Azure Resource Manager per distribuire applicazioni HDInsight.
* [Installare applicazioni HDInsight](hdinsight-apps-install-applications.md): informazioni su come installare un'applicazione HDInsight nei cluster.
* [Pubblicare applicazioni HDInsight](hdinsight-apps-publish-applications.md): informazioni su come pubblicare applicazioni HDInsight personalizzate in Azure Marketplace.
* [Personalizzare cluster HDInsight basati su Linux tramite Azioni script](hdinsight-hadoop-customize-cluster-linux.md): informazioni su come usare Azioni script per installare applicazioni aggiuntive.
* [Creare cluster Apache Hadoop basati su Linux in HDInsight tramite modelli ARM](hdinsight-hadoop-create-linux-clusters-arm-templates.md): informazioni su come chiamare i modelli di Azure Resource Manager per creare cluster HDInsight.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md)(Usare nodi perimetrali vuoti in HDInsight): informazioni su come usare un nodo perimetrale vuoto per l'accesso a cluster HDInsight, il test di applicazioni HDInsight e l'hosting di applicazioni HDInsight.
