<properties
	pageTitle="Usare un ambiente sandbox Hadoop per informazioni su Hadoop | Microsoft Azure"
	description="Per iniziare ad apprendere l'uso dell'ecosistema Handoop, è possibile impostare un ambiente sandbox Hadoop di Hortonworks in una macchina virtuale Azure. "
	keywords="emulatore hadoop, sandbox hadoop"
	editor="cgronlun"
	manager="jhubbard"
	services="hdinsight"
	authors="nitinme"
	documentationCenter=""
	tags="azure-portal"/> 

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2016"
	ms.author="nitinme"/>

# Introduzione all'ecosistema Hadoop con un ambiente sandbox Hadoop su una macchina virtuale

Informazioni su come installare l'ambiente sandbox Hadoop da Hortonworks in una macchina virtuale per acquisire familiarità con l'ecosistema di Hadoop. L'ambiente sandbox è un ambiente di sviluppo locale per informazioni su Hadoop, Hadoop Distributed File System (HDFS) e l'invio di processi.

## Prerequisiti

* [Oracle VirtualBox](https://www.virtualbox.org/)

Dopo aver acquisito familiarità con Hadoop è possibile iniziare a usare Hadoop in Azure creando un cluster HDInsight. Per altre informazioni sulle attività iniziali, vedere l'articolo [Introduzione all'uso di Hadoop basato su Linux in HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).

## Scaricare e installare la macchina virtuale

1. Da [http://hortonworks.com/downloads/#sandbox](http://hortonworks.com/downloads/#sandbox), selezionare l'elemento __DOWNLOAD FOR VIRTUALBOX__ (SCARICA PER VIRTUALBOX) per HDP 2.4 su Sandbox di Hortonworks. È necessario registrarsi con Hortonworks prima di avviare il download.

    ![Immagine di collegamento per scaricare Sandbox di Hortonworks per VirtualBox](./media/hdinsight-hadoop-emulator-get-started/download-sandbox.png) 

2. Dalla stessa pagina Web, selezionare la __Guida all'installazione di VirtualBox__ per HDP 2.4 in Sandbox di Hortonworks. Ciò consente di scaricare un file PDF contenente le istruzioni per l'installazione della macchina virtuale.

    ![Consultare la guida all'installazione](./media/hdinsight-hadoop-emulator-get-started/view-install-guide.png) 

## Avviare la macchina virtuale

1. Avviare VirtualBox, selezionare Sandbox di Hortonworks, fare clic su __Avvia__ e su __Avvio normale__.

    ![Avvio normale](./media/hdinsight-hadoop-emulator-get-started/normal-start.png) 

2. Al termine del processo di avvio della macchina virtuale, verranno visualizzate le istruzioni di accesso. Lanciare un browser Web e accedere all'URL visualizzato (in genere http://127.0.0.1:8888).

## Impostare le password

1. Dal passaggio __introduttivo__ della pagina di Sandbox di Hortonworks, selezionare __Visualizza opzioni avanzate__. Utilizzare le informazioni in questa pagina per accedere a Sandbox con SSH. Utilizzare il nome e la password forniti.

    > [AZURE.NOTE] Se non è stato installato un client SSH, è possibile utilizzare l'SSH basato sul Web fornito dalla macchina virtuale in \_http://localhost:4200/__.

    Al primo collegamento tramite SSH verrà richiesto di cambiare la password per l'account root. Immettere una nuova password da utilizzare per i futuri accesi tramite SSH.

2. Dopodiché immettere il comando seguente:

        ambari-admin-password-reset
    
    Quando richiesto, fornire una password per l'account di amministratore di Ambari. Questo verrà utilizzato quando si accede all'interfaccia utente Web di Ambari.

## Usare il comando Hive

1. Da una connessione SSH a Sandbox, utilizzare il comando seguente per avviare la shell di Hive:

        hive

2. Una volta avviata la shell, utilizzarla per visualizzare le tabelle fornite con Sandbox:

        show tables;

3. Usare il codice seguente per recuperare 10 righe dalla tabella `sample_07`:

        select * from sample_07 limit 10;

## Passaggi successivi

* [Informazioni su come utilizzare Visual Studio con Sandbox di Hortonworks](hdinsight-hadoop-emulator-visual-studio.md)
* [Acquisire dimestichezza con Sandbox di Hortonworks](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Esercitazione di Hadoop: introduzione a HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

<!---HONumber=AcomDC_0921_2016-->