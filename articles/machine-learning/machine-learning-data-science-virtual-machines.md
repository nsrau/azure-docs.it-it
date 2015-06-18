<properties 
	pageTitle="Configurazione di una macchina virtuale o di una macchina virtuale di SQL Server in Azure per l'analisi scientifica dei dati" 
	description="Configurazione di una macchina virtuale per l'analisi scientifica dei dati" 
	services="machine-learning" 
	documentationCenter="" 
	authors="msolhab" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/17/2015" 
	ms.author="mohabib;xibingao;bradsev" />

# Configurazione di una macchina virtuale o di una macchina virtuale di SQL Server in Azure per l'analisi scientifica dei dati

È possibile eseguire il provisioning di diversi tipi di macchine virtuali di Azure e configurarle per utilizzarle come parte di un ambiente di analisi scientifica dei dati basato su cloud. La scelta della versione di macchina virtuale da usare dipende dal tipo e dalla quantità di dati da modellare con l'apprendimento automatico e dalla destinazione di quei dati nel cloud. Per informazioni aggiuntive sulle questioni da prendere in considerazione per prendere questa decisione, vedere [Pianificazione dell'ambiente di analisi scientifica dei dati in Azure Machine Learning](machine-learning-data-science-plan-your-environment.md). I due scenari illustrati in questo articolo comprendono

Vengono fornite istruzioni che descrivono come configurare una macchina virtuale di Azure e una macchina virtuale di Azure con il servizio SQL come server di IPython Notebook. La macchina virtuale è in esecuzione su Windows ed è configurata con strumenti di supporto quali Esplora archivi Azure e AzCopy, nonché altri pacchetti per progetti di analisi scientifica dei dati. Ad esempio, Esplora archivi Azure e AzCopy forniscono modi efficaci per caricare dati nella memoria di Azure dal computer locale o per scaricarli dalla memoria nel computer locale. Sono disponibili due set di istruzioni:

* In [Configurazione di una macchina virtuale di Azure per l'analisi scientifica dei dati](machine-learning-data-science-setup-virtual-machine.md) viene illustrato come eseguire il provisioning di una macchina virtuale di Azure con IPython Notebook e altri strumenti utilizzati per l'analisi scientifica dei dati nei casi in cui è possibile usare una forma di risorsa di archiviazione di Azure diversa da SQL per archiviare i dati. 

* In [Configurazione di una macchina virtuale di SQL Server in Azure per l'analisi scientifica dei dati](machine-learning-data-science-setup-sql-server-virtual-machine.md) viene illustrato come eseguire il provisioning di una macchina virtuale di SQL Server in Azure con IPython Notebook e altri strumenti utilizzati per l'analisi scientifica dei dati nei casi in cui si utilizza un database SQL per archiviare i dati.

In seguito al provisioning e alla configurazione delle macchine virtuali, è possibile usarle come server di IPython Notebook per la navigazione e l'elaborazione dei dati e per altre attività legate ad Azure Machine Learning e al processo di analisi scientifica dei dati cloud. Questo processo può includere le procedure per lo spostamento, l'elaborazione e il campionamento dei dati in HDInsight in preparazione dell'apprendimento dei dati con Azure Machine Learning.

* Per informazioni su come spostare dati in HDInsight dall'archiviazione BLOB di Azure, vedere [Creare e caricare dati in tabelle Hive dall'archiviazione BLOB di Azure](machine-learning-data-science-hive-tables.md).
* Per informazioni sull'elaborazione dei dati in HDInsight con le query Hive, vedere [Inviare query Hive ai cluster Hadoop di HDInsight nel processo di analisi scientifica dei dati cloud](machine-learning-data-science-hive-queries.md).
* Per informazioni sul campionamento dei dati in HDInsight, vedere [Campionare i dati nelle tabelle Hive di Azure HDInsight](machine-learning-data-science-sample-data-hive.md).


> [AZURE.NOTE] Macchine virtuali di Azure è disponibile con **pagamento a consumo**. Per assicurarsi di non subire addebiti quando non si utilizza la macchina virtuale, lo stato deve essere impostato su **Arrestato (deallocato)** dal [portale di gestione di Azure](http://manage.windowsazure.com/). Per istruzioni dettagliate su come deallocare la macchina virtuale, vedere [Arrestare e deallocare la macchina virtuale quando non viene utilizzata](machine-learning-data-science-setup-virtual-machine.md#shutdown) 




<!--HONumber=49--> 