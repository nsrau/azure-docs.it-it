<properties
   	pageTitle="Pubblicare applicazioni HDInsight | Microsoft Azure"
   	description="Informazioni su come creare e pubblicare applicazioni HDInsight."
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
   	ms.date="06/01/2016"
   	ms.author="jgao"/>

# Pubblicare applicazioni HDInsight in Azure Marketplace

Un'applicazione HDInsight è un'applicazione che gli utenti possono installare in un cluster HDInsight basato su Linux. Queste applicazioni possono essere sviluppate da Microsoft, da fornitori di software indipendenti (ISV) o dall'utente. In questo articolo si apprenderà come pubblicare un'applicazione HDInsight in Azure Marketplace. Per informazioni generali sulla pubblicazione in Azure Marketplace, vedere [Pubblicare un'offerta in Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md).

Le applicazioni HDInsight usano il modello *Bring Your Own License (BYOL)*, in cui il provider dell'applicazione è responsabile di concedere in licenza l'applicazione agli utenti finali e agli utenti finali vengono addebitate da Azure solo le risorse create, ad esempio il cluster HDInsight e le VM o i nodi. Attualmente la fatturazione per l'applicazione in sé non viene effettuata con Azure.

Altro articolo correlato all'applicazione HDInsight:

- [Installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md): informazioni su come installare e testare le applicazioni HDInsight personalizzate.

 
## Prerequisiti

Prima di inviare l'applicazione personalizzata al marketplace, è necessario crearla e testarla. Vedere gli articoli seguenti:

- [Installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md): informazioni su come installare e testare le applicazioni HDInsight personalizzate.

È anche necessario registrare l'account per sviluppatore. Vedere [Pubblicare un'offerta in Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md) e [Creare un account di Microsoft Developer](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).

## Definire l'applicazione

La pubblicazione di applicazioni in Azure Marketplace richiede due passaggi. Prima si definisce un file **createUiDef.json** per indicare i cluster con cui è compatibile l'applicazione e quindi si pubblica il modello dal portale di Azure. Il seguente è un file createUiDef.json di esempio.

	{
		"handler": "Microsoft.HDInsight",
		"version": "0.0.1-preview",
		"clusterFilters": {
			"types": ["Hadoop", "HBase", "Storm", "Spark"],
			"tiers": ["Standard", "Premium"],
			"versions": ["3.4"]
		}
	}


|Campo | Descrizione | Valori possibili|
|-------|---------------|----------------|
|types |Tipi di cluster con cui è compatibile l'applicazione. |Hadoop, HBase, Storm, Spark (o qualsiasi combinazione di questi valori)|
|tiers |Livelli di cluster con cui è compatibile l'applicazione. |Standard, Premium (o entrambi)|
|versions|	Tipi di cluster HDInsight con cui è compatibile l'applicazione. |3\.4|

## Inserire l'applicazione in un pacchetto

Creare un file ZIP contenente tutti i file necessari per installare le applicazioni HDInsight. Il file ZIP sarà necessario in [Pubblicare l'applicazione](#publish-application).

- [createUiDefinition.json](#define-application).
- mainTemplate.json. Vedere un esempio in [Installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md).

	>[AZURE.IMPORTANT] Il nome dello script di installazione dell'applicazione deve essere univoco per un determinato cluster con il formato seguente.
	
	>	name": "[concat('hue-install-v0','-' ,uniquestring(‘applicationName’)]"
		
	>Si noti che il nome dello script è composto da tre parti:
		
	>	1. A script name prefix, which shall include either the application name or a name relevant to the application.
	>	2. A "-" for readability.
	>	3. A unique string function with the application name as the parameter.

	>	An example is the above ends up becoming: hue-install-v0-4wkahss55hlas in the persisted script action list. For a sample JSON payload, see [https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).

- Tutti gli script necessari.

> [AZURE.NOTE] I file dell'applicazione (inclusi eventuali file dell'applicazione Web) possono trovarsi in qualsiasi endpoint accessibile pubblicamente.

## Pubblicare l'applicazione

Seguire questa procedura per pubblicare un'applicazione HDInsight:

1. Accedere al [portale di pubblicazione di Azure](https://publish.windowsazure.com/).
2. Fare clic su **Solution templates** per creare un nuovo modello di soluzione.
3. Fare clic su **Create Dev Center account and join the Azure program** per registrare la società, se non è ancora stato fatto. Vedere [Creare un account di Microsoft Developer](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).
4. Fare clic su **Define some Topologies to get Started**. Un modello di soluzione è un elemento padre per tutte le relative topologie. È possibile definire più topologie in un singolo modello di soluzione/offerta. Quando un'offerta passa alla fase di gestione temporanea, passano a tale fase anche tutte le relative topologie. 
5. Aggiungere una nuova versione
6. Caricare il file ZIP preparato in [Inserire l'applicazione in un pacchetto](#package-application).  
7. Fare clic su **Request Certification**. Il team di certificazione Microsoft esaminerà i file e certificherà la topologia.

## Passaggi successivi

- [Installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md): informazioni su come distribuire in HDInsight un'applicazione HDInsight non pubblicata.
- [Personalizzare cluster HDInsight basati su Linux tramite Azioni script](hdinsight-hadoop-customize-cluster-linux.md): informazioni su come usare Azioni script per installare applicazioni aggiuntive.
- [Creare cluster Hadoop basati su Linux in HDInsight tramite modelli ARM](hdinsight-hadoop-create-linux-clusters-arm-templates.md): informazioni su come chiamare i modelli ARM per creare cluster HDInsight.

<!---HONumber=AcomDC_0601_2016-->