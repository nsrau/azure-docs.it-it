<properties
	pageTitle="Come usare il plug-in slave di Azure con una soluzione di integrazione continua Jenkins | Microsoft Azure"
	description="Descrive come usare il plug-in slave di Azure con una soluzione di integrazione continua Jenkins."
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

# Come usare il plug-in slave di Azure con una soluzione di integrazione continua Jenkins

È possibile usare il plug-in slave di Azure per Jenkins per effettuare il provisioning di nodi slave in Azure quando si eseguono compilazioni distribuite.

## Installare il plug-in slave di Azure

1. Nel dashboard di Jenkins fare clic su **Manage Jenkins**.

1. Nella pagina **Manage Jenkins** fare clic su **Manage Plugins**.

1. Fare clic sulla scheda **Available**.

1. Nel campo del filtro sopra l'elenco dei plug-in disponibili digitare **Azure** per limitare l'elenco ai plug-in rilevanti.

    Se si decide di scorrere l'elenco dei plug-in disponibili, sarà possibile trovare il plug-in slave di Azure nella sezione **Cluster Management and Distributed Build**.

1. Selezionare la casella di controllo **Azure Slave Plugin**.

1. Fare clic su **Install without restart** oppure su **Download now and install after restart**.

Una volta installato il plug-in, i passaggi successivi consistono nel configurarlo con il profilo della sottoscrizione di Azure e nel creare un modello che verrà usato durante la creazione della macchina virtuale per il nodo slave.


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

      		Id="<Subscription ID value>"

      		Name="Pay-As-You-Go"
			ManagementCertificate="<Management certificate value>" />

  		</PublishProfile>

	</PublishData>

Dopo avere creato il profilo di sottoscrizione, seguire questa procedura per configurare il plug-in slave di Azure:

1. Nel dashboard di Jenkins fare clic su **Manage Jenkins**.

1. Fare clic su **Configure System**.

1. Scorrere verso il basso la pagina per trovare la sezione **Cloud**.

1. Fare clic su **Add new cloud > Microsoft Azure**.

    ![sezione cloud][cloud section]

    Verranno visualizzati i campi in cui è necessario immettere i dettagli della sottoscrizione.

    ![configurazione della sottoscrizione][subscription configuration]

1. Copiare i valori dell'ID sottoscrizione e del certificato di gestione dal profilo di sottoscrizione e incollarli nei campi appropriati.

    Quando si copiano l'ID sottoscrizione e il certificato di gestione, non includere le virgolette che racchiudono i valori.

1. Fare clic su **Verify configuration**.

1. Quando la configurazione viene verificata correttamente, fare clic su **Save**.

## Configurare un modello di macchina virtuale per il plug-in slave di Azure

Un modello di macchina virtuale definisce i parametri che il plug-in userà per creare un nodo slave in Azure. Nella procedura seguente verrà creato un modello per una macchina virtuale Ubuntu.

1. Nel dashboard di Jenkins fare clic su **Manage Jenkins**.

1. Fare clic su **Configure System**.

1. Scorrere verso il basso la pagina per trovare la sezione **Cloud**.

1. Nella sezione **Cloud** trovare **Add Azure Virtual Machine Template** e quindi fare clic su **Add**.

    ![aggiungere modello macchina virtuale][add vm template]

    Verranno visualizzati i campi in cui è possibile immettere dettagli relativi al modello che si sta creando.

    ![configurazione generale campo vuoto][blank general configuration]

1. Nella casella **Name** immettere il nome di un servizio cloud di Azure. Se il nome immesso fa riferimento a un servizio cloud esistente, il provisioning della macchina virtuale verrà effettuato in tale servizio. In caso contrario, Azure ne creerà uno nuovo.

1. Nella casella **Description** immettere il testo che descrive il modello che si sta creando. Queste informazioni sono esclusivamente a scopo di riferimento e non vengono usate nel provisioning di una macchina virtuale.

1. La casella **Labels** consente di identificare il modello che si sta creando e viene successivamente usata per fare riferimento al modello durante la creazione di un processo Jenkins. Ai fini di questa esercitazione, immettere **linux** in questa casella.

1. Nell'elenco **Region** selezionare l'area in cui verrà creata la macchina virtuale.

1. Nell'elenco **Virtual Machine Size** selezionare le dimensioni appropriate.

1. Nella casella **Storage Account Name** specificare un account di archiviazione in cui verrà creata la macchina virtuale. Assicurarsi che l'account si trovi nella stessa area del servizio cloud che verrà utilizzato. Per creare una nuova risorsa di archiviazione, lasciare vuota questa casella.

1. Il periodo di memorizzazione specifica il numero di minuti prima dell'eliminazione di uno slave inattivo da parte di Jenkins. Lasciare il valore predefinito pari a 60. È anche possibile arrestare lo slave invece di eliminarlo quando è inattivo. A tale scopo, selezionare la casella di controllo **Shutdown Only (Do Not Delete) After Retention Time**.

1. Nell'elenco **Usage** selezionare la condizione appropriata per quando verrà usato questo nodo slave. Per il momento, selezionare **Utilize this node as much as possible**.

    A questo punto, il modulo dovrebbe avere un aspetto simile al seguente:

    ![configurazione modello generale checkpoint][checkpoint general template config]

    Nel prossimo passaggio vengono forniti i dettagli sull'immagine del sistema operativo in cui si vuole creare lo slave.

1. Nella casella **Image Family or Id** è necessario specificare quale immagine di sistema verrà installata nella macchina virtuale. È possibile scegliere da un elenco di famiglie di immagini o specificare un'immagine personalizzata.

    Per selezionare da un elenco di famiglie di immagini, immettere il primo carattere (maiuscole/minuscole) del nome della famiglia dell'immagine. Ad esempio, digitando **U** verrà visualizzato l'elenco delle famiglie di Ubuntu Server. Dopo aver selezionato un'opzione nell'elenco, Jenkins userà la versione più recente dell'immagine di sistema di tale famiglia durante il provisioning della macchina virtuale.

    ![Esempio di elenco di immagini dei sistemi operativi][OS Image list sample]

    Se invece si dispone di un'immagine personalizzata che si desidera utilizzare, immettere il nome dell'immagine personalizzata. I nomi delle immagini personalizzate non vengono visualizzati nell'elenco e quindi è necessario verificare che il nome venga immesso correttamente.

    Per questa esercitazione, digitare **U** per visualizzare un elenco di immagini Ubuntu e quindi selezionare **Ubuntu Server 14.04 LTS**.

1. Nell'elenco **Launch Method** fare clic su **SSH**.

1. Copiare lo script seguente e incollarlo nella casella **Init script**.

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

    Lo script di inizializzazione verrà eseguito dopo la creazione della macchina virtuale. In questo esempio lo script installa Java, Git e ant.

1. Nelle caselle **Username** e **Password** immettere i valori preferiti relativi all'account amministratore che verrà creato nella macchina virtuale.

1. Fare clic su **Verify Template** per verificare che i parametri specificati siano validi.

1. Fare clic su **Save**.


## Creare un processo Jenkins eseguito in un nodo slave in Azure

In questa sezione si creerà un'attività di Jenkins che verrà eseguita in un nodo slave in Azure. È necessario disporre di un progetto in GitHub per proseguire.

1. Nel dashboard di Jenkins fare clic su **New Item**.

1. Immettere un nome per l'attività che si sta creando.

1. Per il tipo di progetto, fare clic su **Freestyle project**.

1. Fare clic su **OK**.

1. Nella pagina di configurazione dell'attività, selezionare **Restrict where this project can be run**.

1. Nella casella **Label Expression** immettere **linux**. Nella sezione precedente è stato creato un modello di slave denominato **linux**, che verrà specificato qui.

1. Nella sezione **Build** fare clic su **Add build step** e selezionare **Execute shell**.

1. Modificare lo script seguente, sostituendo **(your GitHub account name)**, **(your project name)** e **(your project directory)** con valori appropriati e incollare lo script modificato nell'area di testo visualizzata.

		# Clone from git repo

		currentDir="$PWD"

		if [ -e (your project directory) ]; then

  			cd (your project directory)

  			git pull origin master

		else

  			git clone https://github.com/(your GitHub account name)/(your project name).git

		fi

		# change directory to project

		cd $currentDir/(your project directory)

		#Execute build task

		ant

1. Fare clic su **Save**.

1. Nel dashboard di Jenkins passare il puntatore sull'attività appena creata e fare clic sulla freccia dell'elenco a discesa per visualizzare le opzioni dell'attività.

1. Fare clic su **Build now**.

Jenkins creerà quindi un nodo slave usando il modello creato nella sezione precedente ed eseguirà lo script specificato nell'istruzione di compilazione di questa attività.

## Passaggi successivi

Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure].

<!-- URL List --> 

[Centro per sviluppatori Java di Azure]: https://azure.microsoft.com/develop/java/
[profilo di sottoscrizione]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List --> 

[cloud section]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-cloud-section.png
[subscription configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-account-configuration-fields.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-add-vm-template.png
[blank general configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration-blank.png
[checkpoint general template config]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration.png
[OS Image list sample]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-os-family-list-sample.png

<!---HONumber=AcomDC_0921_2016-->