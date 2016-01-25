<properties
    pageTitle="Come utilizzare il plug-in slave Azure con il server per l'integrazione continua Hudson"
    description="In questo articolo viene descritto come utilizzare il plug-in Azure slave con il server per l'integrazione continua Hudson"
	services="storage"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor="jimbe" />

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="01/11/2016"
	ms.author="robmcm"/>

# Come utilizzare il plug-in slave Azure con il server per l'integrazione continua Hudson

Il plug-in Azure slave per Hudson consente di eseguire il provisioning di nodi slave in Azure quando si eseguono build distribuite.

## Installare il plug-in Azure slave
1. Nel dashboard di Hudson, fare clic su **Manage Hudson**.
2. Nella pagina **Manage Hudson** fare clic su **Manage Plugins**.
3. Fare clic sulla scheda **Available**.
4. Fare clic su **Search** e digitare **Azure** per limitare l'elenco ai plug-in rilevanti.

	Se si decide di scorrere l'elenco di plug-in disponibili, si troverà il plug-in Azure slave nella sezione **Cluster Management and Distributed Build** nella scheda **Others**.

5. Selezionare la casella di controllo **Azure Slave Plugin**.
6. Fare clic su **Installa**.
7. Riavviare Hudson.

Ora che il plug-in è installato, sarà necessario procedere con la configurazione del plug-in mediante il profilo della sottoscrizione di Azure e con la creazione di un modello che verrà utilizzato durante la creazione della macchina virtuale relativa al nodo slave.


## Configurare il plug-in Azure slave mediante il profilo della propria sottoscrizione

Un profilo di sottoscrizione, noto anche come impostazioni di pubblicazione, è un file XML contenente le credenziali protette e alcune informazioni aggiuntive che saranno necessarie per utilizzare Azure nel proprio ambiente di sviluppo. Per configurare il plug-in Azure slave, sono necessari i seguenti elementi:

* L'ID sottoscrizione
* Un certificato di gestione della sottoscrizione

Tali elementi sono reperibili nel profilo di sottoscrizione. Se non si dispone di una copia del profilo di sottoscrizione, è possibile scaricarla da [qui](https://manage.windowsazure.com/publishsettings/Index?SchemaVersion=2.0). Di seguito è riportato un esempio di un profilo di sottoscrizione.

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

Dopo aver creato il profilo di sottoscrizione, attenersi alla seguente procedura per configurare il plug-in Azure slave.

1. Nel dashboard di Hudson, fare clic su **Manage Hudson**.
2. Fare clic su **Configure System**.
3. Scorrere verso il basso la pagina per trovare la sezione **Cloud**.
4. Fare clic su **Add new cloud > Microsoft Azure**.



	![aggiungere nuovo cloud](./media/azure-slave-plugin-for-hudson/hudson-setup-addcloud.png)

	Verranno visualizzati i campi in cui è necessario immettere i dettagli della sottoscrizione.

	![configurare profilo](./media/azure-slave-plugin-for-hudson/hudson-setup-configureprofile.png)

5. Copiare l'ID sottoscrizione e il certificato di gestione dal profilo di sottoscrizione e incollarli nei campi appropriati.

	Quando si copiano l'ID sottoscrizione e il certificato di gestione, **non** includere le virgolette che racchiudono i valori.

6. Fare clic su **Verify configuration**.
7. Quando la configurazione viene verificata correttamente, fare clic su **Save**.

## Configurare un modello di macchina virtuale per il plug-in Azure slave

Un modello di macchina virtuale definisce i parametri che il plug-in utilizzerà per creare un nodo slave in Azure. Nella procedura seguente verrà creato un modello per una macchina virtuale Ubuntu.

1. Nel dashboard di Hudson, fare clic su **Manage Hudson**.
2. Fare clic su **Configure System**.
3. Scorrere verso il basso la pagina per trovare la sezione **Cloud**.
4. All'interno della sezione **Cloud**, trovare **Add Azure Virtual Machine Template** e fare clic sul pulsante **Add**.



	![aggiungere modello macchina virtuale](./media/azure-slave-plugin-for-hudson/hudson-setup-addnewvmtemplate.png)

5. Specificare un nome del servizio cloud nel campo **Name**. Se il nome specificato fa riferimento a un servizio cloud esistente, il provisioning della macchina virtuale verrà effettuato in tale servizio. In caso contrario, Azure ne creerà uno nuovo.
6. Nel campo **Description**, immettere il testo che descrive il modello che si sta creando. Queste informazioni sono esclusivamente a scopo di documentazione e non vengono utilizzate nel provisioning di una macchina virtuale.
7. Nel campo **Labels** immettere **linux**. Questa etichetta viene utilizzata per identificare il modello che si sta creando e viene successivamente utilizzata per fare riferimento al modello durante la creazione di un processo Hudson.
8. Selezionare un'area in cui verrà creata la macchina virtuale.
9. Selezionare le dimensioni appropriate della macchina virtuale.
10. Specificare un account di archiviazione in cui verrà creata la macchina virtuale. Assicurarsi che l'account si trovi nella stessa area del servizio cloud che verrà utilizzato. Per creare una nuova risorsa di archiviazione, lasciare questo campo vuoto.
11. Il periodo di memorizzazione specifica il numero di minuti prima dell'eliminazione di uno slave inattivo da parte di Hudson. Lasciare il valore predefinito pari a 60.
12. In **Usage**, selezionare la condizione appropriata quando verrà utilizzato questo nodo slave. Per il momento, selezionare **Utilize this node as much as possible**.



	A questo punto, il modulo dovrebbe avere un aspetto simile al seguente:

	![configurazione modello](./media/azure-slave-plugin-for-hudson/hudson-setup-templateconfig1-withdata.png)

13. In **Image Family or Id** è necessario specificare quale immagine del sistema verrà installata nella macchina virtuale. È possibile scegliere da un elenco di famiglie di immagini o specificare un'immagine personalizzata.

	Per selezionare da un elenco di famiglie di immagini, immettere il primo carattere (maiuscole/minuscole) del nome della famiglia dell'immagine. Ad esempio, digitando **U** verrà visualizzato l'elenco delle famiglie di Ubuntu Server. Dopo aver selezionato dall'elenco, Jenkins utilizzerà la versione più recente di tale immagine del sistema di tale famiglia durante il provisioning della macchina virtuale.

	![elenco famiglie sistemi operativi](./media/azure-slave-plugin-for-hudson/hudson-oslist.png)

	Se invece si dispone di un'immagine personalizzata che si desidera utilizzare, immettere il nome dell'immagine personalizzata. I nomi delle immagini personalizzati non vengono visualizzati nell'elenco, pertanto è necessario verificare che il nome venga immesso correttamente.

	Per questa esercitazione, digitare **U** per visualizzare un elenco di immagini Ubuntu e selezionare **Ubuntu Server 14.04 LTS**.

14. Per **Launch method**, selezionare **SSH**.
15. Copiare lo script seguente e incollarlo nel campo **Init script**.

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

16. Nei campi **Username** e **Password** immettere i valori preferiti relativi all'account amministratore che verrà creato nella macchina virtuale.
17. Fare clic su **Verify Template** per verificare che i parametri specificati siano validi.
18. Fare clic su **Save**.


## Creare un processo Hudson eseguito in un nodo slave in Azure

In questa sezione si creerà un'attività di Hudson che verrà eseguita in un nodo slave in Azure.

1. Nel dashboard di Hudson, fare clic su **New Job**.
2. Immettere un nome per il processo che si sta creando.
3. Per il tipo di processo, selezionare **Build a free-style software job**.
4. Fare clic su **OK**.
5. Nella pagina di configurazione del processo, selezionare **Restrict where this project can be run**.
6. Selezionare **Node and label menu** e **linux** (questa etichetta è stata specificata al momento della creazione del modello di macchina virtuale nella sezione precedente).

7. Nella sezione **Build** fare clic su **Add build step** e selezionare **Execute shell**.
8. Modificare il seguente script, sostituendo **(il nome dell'account github)**, **(il nome del progetto)** e **(la directory del progetto)** con valori appropriati e incollare lo script modificato nell'area di testo visualizzata.


		# Clone from git repo

		currentDir="$PWD"

		if [ -e (your project directory) ]; then

  			cd (your project directory)

  			git pull origin master

		else

  			git clone https://github.com/(your github account name)/(your project name).git

		fi

		# change directory to project

		cd $currentDir/(your project directory)



		#Execute build task

		ant

9. Fare clic su **Save**.
10. Nel dashboard di Hudson, trovare il processo appena creato e fare clic sull'icona **Schedule a build**.

Hudson creerà quindi un nodo slave utilizzando il modello creato nella sezione precedente ed eseguirà lo script specificato nell'istruzione di compilazione di questa attività.

<!---HONumber=AcomDC_0114_2016-->