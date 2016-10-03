<properties
	pageTitle="Come usare il plug-in slave di Azure con una soluzione di integrazione continua Hudson | Microsoft Azure"
	description="Descrive come usare il plug-in slave di Azure con una soluzione di integrazione continua Hudson"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="rmcmurray"
	manager="wpickett"
	editor="" />  

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="robmcm"/>  

# Come usare il plug-in slave di Azure con una soluzione di integrazione continua Hudson

Il plug-in slave di Azure per Hudson consente di eseguire il provisioning di nodi slave in Azure quando si eseguono build distribuite.

## Installare il plug-in slave di Azure

1. Nel dashboard di Hudson, fare clic su **Manage Hudson**.

1. Nella pagina **Manage Hudson** fare clic su **Manage Plugins**.

1. Fare clic sulla scheda **Available**.

1. Fare clic su **Search** (Cerca) e digitare **Azure** per limitare l'elenco ai plug-in pertinenti.

	Se si decide di scorrere l'elenco di plug-in disponibili, si troverà il plug-in slave di Azure nella sezione **Cluster Management and Distributed Build** (Gestione cluster e build distribuita) nella scheda **Others** (Altri).

1. Selezionare la casella di controllo **Azure Slave Plugin**.

1. Fare clic su **Installa**.

1. Riavviare Hudson.

Dopo aver installato il plug-in, è necessario procedere alla relativa configurazione tramite il profilo della sottoscrizione di Azure e alla creazione di un modello che verrà usato durante la creazione della VM relativa al nodo slave.

## Configurare il plug-in slave di Azure con il profilo di sottoscrizione

Un profilo di sottoscrizione, noto anche come impostazioni di pubblicazione, è un file XML contenente le credenziali protette e alcune informazioni aggiuntive necessarie per usare Azure nel proprio ambiente di sviluppo. Per configurare il plug-in slave di Azure, è necessario quanto segue:

* ID sottoscrizione personale
* Un certificato di gestione per la sottoscrizione

Questi elementi sono disponibili nel [profilo di sottoscrizione]. Di seguito è riportato un esempio di un profilo di sottoscrizione.

	<?xml version="1.0" encoding="utf-8"?>

		<PublishData>

  		<PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">

    	<Subscription

      		ServiceManagementUrl="https://management.core.windows.net"

      		Id="<Subscription ID>"

      		Name="Pay-As-You-Go"
			ManagementCertificate="<Management certificate value>" />

  		</PublishProfile>

	</PublishData>

Dopo avere creato il profilo di sottoscrizione, attenersi alla seguente procedura per configurare il plug-in slave di Azure.

1. Nel dashboard di Hudson, fare clic su **Manage Hudson**.

1. Fare clic su **Configure System**.

1. Scorrere verso il basso la pagina per trovare la sezione **Cloud**.

1. Fare clic su **Add new cloud > Microsoft Azure**.

    ![aggiungere nuovo cloud][add new cloud]

    Verranno visualizzati i campi in cui è necessario immettere i dettagli della sottoscrizione.

    ![configurare profilo][configure profile]

1. Copiare l'ID sottoscrizione e il certificato di gestione dal profilo di sottoscrizione e incollarli nei campi appropriati.

    Quando si copiano l'ID sottoscrizione e il certificato di gestione, **non** includere le virgolette che racchiudono i valori.

1. Fare clic su **Verify configuration**.

1. Quando la configurazione viene verificata correttamente, fare clic su **Save**.

## Configurare un modello di macchina virtuale per il plug-in slave di Azure

Un modello di macchina virtuale definisce i parametri che il plug-in userà per creare un nodo slave in Azure. Nella procedura seguente verrà creato un modello per una macchina virtuale Ubuntu.

1. Nel dashboard di Hudson, fare clic su **Manage Hudson**.

1. Fare clic su **Configure System**.

1. Scorrere verso il basso la pagina per trovare la sezione **Cloud**.

1. All'interno della sezione **Cloud**, trovare **Add Azure Virtual Machine Template** e fare clic sul pulsante **Add**.

    ![aggiungere modello macchina virtuale][add vm template]

1. Specificare un nome del servizio cloud nel campo **Name**. Se il nome specificato fa riferimento a un servizio cloud esistente, il provisioning della macchina virtuale verrà effettuato in tale servizio. In caso contrario, Azure ne creerà uno nuovo.

1. Nel campo **Description**, immettere il testo che descrive il modello che si sta creando. Queste informazioni sono esclusivamente a scopo di documentazione e non vengono utilizzate nel provisioning di una macchina virtuale.

1. Nel campo **Labels** immettere **linux**. Questa etichetta viene utilizzata per identificare il modello che si sta creando e viene successivamente utilizzata per fare riferimento al modello durante la creazione di un processo Hudson.

1. Selezionare un'area in cui verrà creata la macchina virtuale.

1. Selezionare le dimensioni appropriate della macchina virtuale.

1. Specificare un account di archiviazione in cui verrà creata la macchina virtuale. Assicurarsi che l'account si trovi nella stessa area del servizio cloud che verrà utilizzato. Per creare una nuova risorsa di archiviazione, lasciare questo campo vuoto.

1. Il periodo di memorizzazione specifica il numero di minuti prima dell'eliminazione di uno slave inattivo da parte di Hudson. Lasciare il valore predefinito pari a 60.

1. In **Usage**, selezionare la condizione appropriata quando verrà utilizzato questo nodo slave. Per il momento, selezionare **Utilize this node as much as possible**.

    A questo punto, il modulo dovrebbe avere un aspetto simile al seguente:

    ![configurazione modello][template config]

1. In **Image Family or Id** è necessario specificare quale immagine del sistema verrà installata nella macchina virtuale. È possibile scegliere da un elenco di famiglie di immagini o specificare un'immagine personalizzata.

    Per selezionare da un elenco di famiglie di immagini, immettere il primo carattere (maiuscole/minuscole) del nome della famiglia dell'immagine. Ad esempio, digitando **U** verrà visualizzato l'elenco delle famiglie di Ubuntu Server. Dopo aver selezionato dall'elenco, Jenkins utilizzerà la versione più recente di tale immagine del sistema di tale famiglia durante il provisioning della macchina virtuale.

    ![elenco famiglie sistemi operativi][OS family list]

    Se invece si dispone di un'immagine personalizzata che si desidera utilizzare, immettere il nome dell'immagine personalizzata. I nomi delle immagini personalizzati non vengono visualizzati nell'elenco, pertanto è necessario verificare che il nome venga immesso correttamente.

    Per questa esercitazione, digitare **U** per visualizzare un elenco di immagini Ubuntu e selezionare **Ubuntu Server 14.04 LTS**.

1. Per **Launch method**, selezionare **SSH**.

1. Copiare lo script seguente e incollarlo nel campo **Init script**.

		# Install Java

		sudo apt-get -y update

		sudo apt-get install -y openjdk-7-jdk

		sudo apt-get -y update --fix-missing

		sudo apt-get install -y openjdk-7-jdk

		# Install git

		sudo apt-get install -y git

		#Install ant

		sudo apt-get install -y ant

		sudo apt-get -y update --fix-missing

		sudo apt-get install -y ant

    L'**Init script** verrà eseguito dopo aver creato la macchina virtuale. In questo esempio, lo script installa Java, git e ant.

1. Nei campi **Username** e **Password** immettere i valori preferiti relativi all'account amministratore che verrà creato nella macchina virtuale.

1. Fare clic su **Verify Template** per verificare che i parametri specificati siano validi.

1. Fare clic su **Save**.

## Creare un processo Hudson eseguito in un nodo slave in Azure

In questa sezione si creerà un'attività di Hudson che verrà eseguita in un nodo slave in Azure.

1. Nel dashboard di Hudson, fare clic su **New Job**.

1. Immettere un nome per il processo che si sta creando.

1. Per il tipo di processo, selezionare **Build a free-style software job**.

1. Fare clic su **OK**.

1. Nella pagina di configurazione del processo, selezionare **Restrict where this project can be run**.

1. Selezionare **Node and label menu** e **linux** (questa etichetta è stata specificata al momento della creazione del modello di macchina virtuale nella sezione precedente).

1. Nella sezione **Build** fare clic su **Add build step** e selezionare **Execute shell**.

1. Modificare lo script seguente, sostituendo **{your github account name}**, **{your project name}** e **{your project directory}** con valori appropriati e incollare lo script modificato nell'area di testo visualizzata.

		# Clone from git repo

		currentDir="$PWD"

		if [ -e {your project directory} ]; then

  			cd {your project directory}

  			git pull origin master

		else

  			git clone https://github.com/{your github account name}/{your project name}.git

		fi

		# change directory to project

		cd $currentDir/{your project directory}

		#Execute build task

		ant

1. Fare clic su **Save**.

1. Nel dashboard di Hudson, trovare il processo appena creato e fare clic sull'icona **Schedule a build**.

Hudson creerà quindi un nodo slave utilizzando il modello creato nella sezione precedente ed eseguirà lo script specificato nell'istruzione di compilazione di questa attività.

## Passaggi successivi

Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure].

<!-- URL List -->  

[Centro per sviluppatori Java di Azure]: https://azure.microsoft.com/develop/java/
[profilo di sottoscrizione]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->  

[add new cloud]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addcloud.png
[configure profile]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-configureprofile.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addnewvmtemplate.png
[template config]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-templateconfig1-withdata.png
[OS family list]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-oslist.png

<!---HONumber=AcomDC_0921_2016-->