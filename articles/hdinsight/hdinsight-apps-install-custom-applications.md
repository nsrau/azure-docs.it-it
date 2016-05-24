<properties
   	pageTitle="Installare applicazioni Hadoop in HDInsight | Microsoft Azure"
   	description="Informazioni su come installare applicazioni HDInsight in applicazioni HDInsight."
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="hero-article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="05/18/2016"
   	ms.author="jgao"/>

# Installare applicazioni HDInsight personalizzate

Un'applicazione HDInsight è un'applicazione che gli utenti possono installare in un cluster HDInsight basato su Linux. Queste applicazioni possono essere sviluppate da Microsoft, da fornitori di software indipendenti (ISV) o dall'utente. In questo articolo, si apprenderà come installare un'applicazione HDInsight che non è stata pubblicata nel portale di Azure in HDInsight. L'applicazione da installare è [Hue](http://gethue.com/).

Altri articoli correlati:

- [Pubblicare applicazioni HDInsight](hdinsight-apps-publish-applications.md): informazioni su come pubblicare le applicazioni HDInsight personalizzate in Azure Marketplace.
- [MSDN: Installare un'applicazione HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): informazioni su come definire le applicazioni HDInsight.

 
## Prerequisiti

Per installare applicazioni HDInsight in un cluster HDInsight esistente, è necessario un cluster HDInsight. Per crearne uno, vedere [Creare cluster](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster). È anche possibile installare applicazioni HDInsight quando si crea un cluster HDInsight.


## Installare un'applicazione HDInsight

Le applicazioni HDInsight possono essere installate quando si crea un cluster o in un cluster HDInsight esistente. Per definire i modelli di Azure Resource Manager, vedere [MSDN: Installare un'applicazione HDInsight](https://msdn.microsoft.com/library/mt706515.aspx).

File necessari per distribuire questa applicazione (Hue):

- [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): modello di Azure Resource Manager per installare l'applicazione HDInsight. Per sviluppare il proprio modello di Azure Resource Manager, vedere [MSDN: Installare un'applicazione HDInsight](https://msdn.microsoft.com/library/mt706515.aspx).
- [hue-install\_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh): Azione script chiamata dal modello di Azure Resource Manager per configurare il nodo perimetrale. 
- [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): file binario hue chiamato da hui-install\_v0.sh. 
- [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): file binario hue chiamato da hui-install\_v0.sh. 
- [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz): applicazione Web di esempio (Tomcat) chiamata da hui-install\_v0.sh.

**Per installare Hue in un cluster HDInsight esistente**

1. Fare clic sull'immagine seguente per accedere ad Azure e aprire il modello di Azure Resource Manager nel portale di Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Questo pulsante apre un modello di Azure Resource Manager nel portale di Azure. Il modello di Azure Resource Manager è disponibile all'indirizzo [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue). Per informazioni su come scrivere questo modello di Azure Resource Manager, vedere [MSDN: Installare un'applicazione HDInsight](https://msdn.microsoft.com/library/mt706515.aspx).
    
2. Nel pannello **Parametri** immettere le informazioni seguenti:

    - **ClusterName**: immettere il nome del cluster in cui installare l'applicazione. Deve essere un cluster esistente.
    
3. Fare clic su **OK** per salvare i parametri.
4. Nel pannello **Distribuzione personalizzata** immettere **Gruppo di risorse**. Il gruppo di risorse è un contenitore che raggruppa il cluster, l'account di archiviazione dipendente e altre risorse. È necessario usare lo stesso gruppo di risorse del cluster.
5. Fare clic su **Note legali** e quindi su **Crea**.
6. Verificare che la casella di controllo **Aggiungi al dashboard** sia selezionata e quindi fare clic su **Crea**. È possibile visualizzare lo stato dell'installazione dal riquadro aggiunto al dashboard del portale e dalla notifica del portale facendo clic sull'icona a forma di campana nella parte superiore del portale. Sono necessari circa 10 minuti per installare l'applicazione.

**Per installare Hue durante la creazione di un cluster**

1. Fare clic sull'immagine seguente per accedere ad Azure e aprire il modello di Azure Resource Manager nel portale di Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Questo pulsante apre un modello di Azure Resource Manager nel portale di Azure. Il modello di Azure Resource Manager è disponibile all'indirizzo [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json). Per informazioni su come scrivere questo modello di Azure Resource Manager, vedere [MSDN: Installare un'applicazione HDInsight](https://msdn.microsoft.com/library/mt706515.aspx).

2. Seguire le istruzioni per creare il cluster e installare Hue. Per altre informazioni sulla creazione di cluster HDInsight, vedere [Creare cluster Hadoop basati su Linux in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

## Convalidare l'installazione

È possibile controllare lo stato dell'applicazione nel portale di Azure per convalidare l'installazione dell'applicazione. È anche possibile verificare che il risultato di tutti gli endpoint HTTP sia quello previsto e convalidare l'eventuale pagina Web:

**Per aprire il portale di Hue**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere **Cluster HDInsight** dal menu di sinistra. Se non viene visualizzato, fare clic su **Esplora** e quindi fare clic su **Cluster HDInsight**.
3. Fare clic sul cluster in cui è stata installata l'applicazione.
4. Nel pannello **Impostazioni** fare clic su **Applicazioni** sotto la categoria **Generale**. **hue** risulterà elencato nel pannello **App installate**.
5. Fare clic su **hue** nell'elenco per visualizzare le proprietà.  
6. Fare clic sul collegamento Pagina Web per convalidare il sito Web. Aprire l'endpoint HTTP in un a browser per convalidare l'interfaccia utente Web di Hue e aprire l'endpoint SSH usando [PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md) o altri [client SSH](hdinsight-hadoop-linux-use-ssh-unix.md).
 
## Risolvere i problemi di installazione

Vedere la sezione [Risolvere i problemi](hdinsight-apps-install-applications.md#troubleshoot) in Installare applicazioni HDInsight.


## Passaggi successivi

- [MSDN: Installare un'applicazione HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): informazioni su come sviluppare modelli di Azure Resource Manager per distribuire le applicazioni HDInsight.
- [Pubblicare applicazioni HDInsight](hdinsight-apps-publish-applications.md): informazioni su come pubblicare le applicazioni HDInsight personalizzate in Azure Marketplace.
- [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md): informazioni su come usare Azione script per installare applicazioni aggiuntive.
- [Creare cluster Hadoop basati su Linux in HDInsight tramite modelli ARM](hdinsight-hadoop-create-linux-clusters-arm-templates.md): informazioni su come chiamare i modelli di Azure Resource Manager per creare cluster HDInsight.

<!---HONumber=AcomDC_0518_2016-->