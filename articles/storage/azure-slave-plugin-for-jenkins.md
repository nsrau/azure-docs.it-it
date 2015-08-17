<properties
    pageTitle="Come utilizzare il plug-in slave Azure con una soluzione di Integrazione continuata Jenkins"
    description="In questo articolo viene descritto come utilizzare il plug-in Azure slave con una soluzione di Integrazione continuata Jenkins."
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
	ms.date="06/03/2015" 
	ms.author="v-dedomi"/>

#Come utilizzare il plug-in slave Azure con una soluzione di Integrazione continuata Jenkins

Il plug-in Azure slave per Jenkins consente di eseguire il provisioning di nodi slave in Azure quando si eseguono build distribuite.

## Installare il plug-in Azure slave
1. Nel dashboard di Jenkins fare clic su **Manage Jenkins**.
2. Nella pagina **Manage Jenkins** fare clic su **Manage Plugins**.
3. Fare clic sulla scheda **Available**.
4. Nel campo filtro sopra l'elenco dei plug-in disponibili, digitare **Azure** per limitare la lista ai plug-in rilevanti.

	Se si decide di scorrere l'elenco di plug-in disponibili, si troverà il plug-in Azure slave nella sezione **Cluster Management and Distributed Build**.
	 
5. Selezionare la casella di controllo **Azure Slave Plugin**.
6. Fare clic su **Install without restart** oppure su **Download now and install after restart**.

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

      		Id="<Subscription ID value>"

      		Name="Pay-As-You-Go"
			ManagementCertificate="<Management certificate value>" />

  		</PublishProfile>

	</PublishData>

Dopo aver creato il profilo di sottoscrizione, attenersi alla seguente procedura per configurare il plug-in Azure slave.

1. Nel dashboard di Jenkins fare clic su **Manage Jenkins**.
2. Fare clic su **Configure System**.
3. Scorrere verso il basso la pagina per trovare la sezione **Cloud**.
4. Fare clic su **Add new cloud > Microsoft Azure**.

	![sezione cloud](./media/azure-slave-plugin-for-jenkins/jenkins-cloud-section.png)

	Verranno visualizzati i campi in cui è necessario immettere i dettagli della sottoscrizione.

	![configurazione sottoscrizione](./media/azure-slave-plugin-for-jenkins/jenkins-account-configuration-fields.png)

5. Copiare l'ID sottoscrizione e i valori del certificato di gestione dal profilo di sottoscrizione e incollarli nei campi appropriati.

	Quando si copiano l'ID sottoscrizione e il certificato di gestione, **non** includere le virgolette che racchiudono i valori.
	
6. Fare clic su **Verify configuration**.
7. Quando la configurazione viene verificata correttamente, fare clic su **Save**.

## Configurare un modello di macchina virtuale per il plug-in Azure slave

Un modello di macchina virtuale definisce i parametri che il plug-in utilizzerà per creare un nodo slave in Azure. Nella procedura seguente verrà creato un modello per una macchina virtuale Ubuntu.

1. Nel dashboard di Jenkins fare clic su **Manage Jenkins**.
2. Fare clic su **Configure System**.
3. Scorrere verso il basso la pagina per trovare la sezione **Cloud**.

4. Nella sezione Cloud, trovare Add Azure Virtual Machine Template e fare clic sul pulsante Add.

	![aggiungere modello macchina virtuale](./media/azure-slave-plugin-for-jenkins/jenkins-add-vm-template.png)

	Verranno visualizzati i campi in cui è possibile immettere dettagli relativi al modello che si sta creando.

	![configurazione generale campo vuoto](./media/azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration-blank.png)

5. Immettere un nome del servizio cloud di Azure nel campo **Name**. Se il nome immesso fa riferimento a un servizio cloud esistente, il provisioning della macchina virtuale verrà effettuato in tale servizio. In caso contrario, Azure ne creerà uno nuovo.

6. Nel campo **Description**, immettere il testo che descrive il modello che si sta creando. Queste informazioni sono esclusivamente a scopo di documentazione e non vengono utilizzate nel provisioning di una macchina virtuale.
7. Il campo **Labels** viene utilizzato per identificare il modello che si sta creando e viene successivamente utilizzato per fare riferimento al modello durante la creazione di un processo Jenkins. Ai fini di questa esercitazione, immettere **linux** in questo campo. 
8. Selezionare un'area in cui verrà creata la macchina virtuale.
9. Selezionare le dimensioni appropriate della macchina virtuale.
10. Specificare un account di archiviazione in cui verrà creata la macchina virtuale. Assicurarsi che l'account si trovi nella stessa area del servizio cloud che verrà utilizzato. Per creare una nuova risorsa di archiviazione, lasciare questo campo vuoto.
11. Il periodo di memorizzazione specifica il numero di minuti prima dell'eliminazione di uno slave inattivo da parte di Jenkins. Lasciare il valore predefinito pari a 60. È inoltre possibile arrestare lo slave invece di eliminarlo quando è inattivo. A tale scopo, selezionare la casella di controllo **Shutdown Only (Do Not Delete) After Retention Time**.
12. In **Usage**, selezionare la condizione appropriata quando verrà utilizzato questo nodo slave. Per il momento, selezionare **Utilize this node as much as possible**.

	A questo punto, il modulo dovrebbe avere un aspetto simile al seguente:

	![configurazione modello generale checkpoint](./media/azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration.png)

	Nel prossimo passaggio vengono forniti i dettagli sull'immagine del sistema operativo in cui si desidera creare lo slave.
 
13. In **Image Family or Id** è necessario specificare quale immagine del sistema verrà installata nella macchina virtuale. È possibile scegliere da un elenco di famiglie di immagini o specificare un'immagine personalizzata.

	Per selezionare da un elenco di famiglie di immagini, immettere il primo carattere (maiuscole/minuscole) del nome della famiglia dell'immagine. Ad esempio, digitando **U** verrà visualizzato l'elenco delle famiglie di Ubuntu Server. Dopo aver selezionato dall'elenco, Jenkins utilizzerà la versione più recente di tale immagine del sistema di tale famiglia durante il provisioning della macchina virtuale.

	![Esempio di elenco di immagini dei sistemi operativi](./media/azure-slave-plugin-for-jenkins/jenkins-os-family-list-sample.png)
	
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


## Creare un processo Jenkins eseguito in un nodo slave in Azure

In questa sezione si creerà un'attività di Jenkins che verrà eseguita in un nodo slave in Azure. È necessario disporre di un progetto su GitHub per proseguire.

1. Nel dashboard di Jenkins fare clic su **New Item**. 
2. Immettere un nome per l'attività che si sta creando.
3. Per il tipo di progetto, selezionare **Freestyle project**.
4. Fare clic su **OK**.
5. Nella pagina di configurazione dell'attività, selezionare **Restrict where this project can be run**.
6. Nel campo **Label Expression**, immettere **linux**. Nella sezione precedente, è stato creato un modello di slave denominato **linux** che è ciò che viene specificato qui.
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
10. Nel dashboard di Jenkins, passare il puntatore sull'attività appena creata e fare clic sulla freccia rivolta verso il basso per visualizzare le opzioni dell'attività.
11. Fare clic su **Build now**.

Jenkins creerà quindi un nodo slave utilizzando il modello creato nella sezione precedente ed eseguirà lo script specificato nell'istruzione di compilazione di questa attività.






  

  

<!---HONumber=August15_HO6-->