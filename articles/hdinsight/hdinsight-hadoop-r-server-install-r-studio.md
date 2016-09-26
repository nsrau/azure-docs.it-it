<properties
	pageTitle="Installare RStudio con R Server su HDInsight (anteprima) | Microsoft Azure"
	description="Installazione di RStudio con R Server su HDInsight (anteprima)."
	services="hdinsight"
	documentationCenter=""
	authors="jeffstokes72"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/22/2016"
   ms.author="jeffstok"/>


# Installazione di RStudio con R Server su HDInsight (anteprima)

Sono attualmente disponibili più ambienti di sviluppo integrato (IDE) per R, tra cui [R Tools per Visual Studio](https://www.visualstudio.com/it-IT/features/rtvs-vs.aspx) (RTVS) recentemente annunciato da Microsoft, una famiglia di strumenti desktop e server di [RStudio](https://www.rstudio.com/products/rstudio-server/), o [StatET](http://www.walware.de/goto/statet), un IDE basato su Eclipse di Walware. Molto diffuso in Linux è l'uso di [RStudio Server](https://www.rstudio.com/products/rstudio-server/) che offre un IDE basato sul browser per l'uso da parte di client remoti. L'installazione di RStudio Server nel nodo perimetrale di un cluster HDInsight Premium offre un'esperienza IDE completa per lo sviluppo e l'esecuzione di script R con R Server nel cluster e può essere molto più produttiva rispetto all'uso della console di R predefinita.

In questo articolo si apprenderà come installare la versione Community (gratuita) di RStudio Server nel nodo perimetrale di un cluster usando uno script personalizzato. Se si preferisce la versione Pro con licenza commerciale di RStudio Server, è necessario seguire le istruzioni di installazione da [RStudio Server](https://www.rstudio.com/products/rstudio/download-server/).

> [AZURE.NOTE] La procedura descritta in questo documento richiede un R Server basato su un cluster HDInsight e non funzionerà correttamente se si usa un cluster HDInsight dove R è stato installato con l'[Installazione di script R](hdinsight-hadoop-r-scripts-linux.md).

## Prerequisiti

* Un cluster Azure HDInsight con R Server Per istruzioni, vedere l'articolo di [introduzione all'uso di R Server in cluster HDInsight](hdinsight-hadoop-r-server-get-started.md).
* Un client SSH. Per distribuzioni Linux e Unix o Macintosh OS X, il comando `ssh` viene offerto con il sistema operativo. Per Windows, è consigliabile [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)


## Installare RStudio nel cluster usando uno script personalizzato

1. Identificare il nodo perimetrale del cluster. Per un cluster HDInsight con R Server, di seguito è indicata la convenzione di denominazione per il nodo head e il nodo perimetrale.

	* Nodo head: `CLUSTERNAME-ssh.azurehdinsight.net`
	* Nodo perimetrale - `R-Server.CLUSTERNAME-ssh.azurehdinsight.net`

2. SSH nel nodo perimetrale del cluster tramite il modello di denominazione precedente.
 
	* Se ci si connette da un client Linux, vedere [Connettersi a un cluster HDInsight basato su Linux](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster).
	* Se ci si connette da un client Windows, vedere l'articolo relativo alla [connessione a un cluster HDInsight basato su Linux tramite PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster).

3. Dopo aver eseguito la connessione, diventare un utente radice nel cluster. Nella sessione SSH usare il comando seguente.

		sudo su -

4. Scaricare lo script personalizzato per installare RStudio. Usare il comando seguente.

		wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/InstallRStudio.sh

5. Modificare le autorizzazioni nel file di script personalizzato ed eseguire lo script. Usare i comandi seguenti.

		chmod 755 InstallRStudio.sh
		./InstallRStudio.sh

6. Se si usa una password SSH durante la creazione di un cluster HDInsight con R Server, è possibile ignorare questo passaggio e procedere al successivo. Se invece si usa una chiave SSH per creare il cluster, è necessario impostare una password per l'utente SSH. Questa password sarà necessaria per la connessione a RStudio. Eseguire i comandi seguenti. Quando viene richiesto di immettere la **password Kerberos corrente**, è sufficiente premere **INVIO**. Si noti che è necessario sostituire `USERNAME` con un utente SSH per il cluster HDInsight.

		passwd USERNAME
		Current Kerberos password:
		New password:
		Retype new password:
		Current Kerberos password:
		
	Se la password è impostata correttamente, viene visualizzato un messaggio simile al seguente.

		passwd: password updated successfully


	Chiudere la sessione SSH.

7. Creare un tunnel SSH al cluster tramite il mapping di `localhost:8787` sul cluster HDInsight per il computer client. Prima di aprire una nuova sessione del browser, è necessario creare un tunnel SSH.

	* Tramite [Cygwin](http://www.redhat.com/services/custom/cygwin/) aprire in un client Linux o Windows una sessione terminal e usare il comando seguente.

			ssh -L localhost:8787:localhost:8787 USERNAME@R-Server.CLUSTERNAME-ssh.azurehdinsight.net
			
		Sostituire **USERNAME** con un utente SSH per il cluster HDInsight e sostituire **CLUSTERNAME** con il nome del cluster HDInsight. È anche possibile usare una chiave SSH al posto di una password aggiungendo `-i id_rsa_key`

	* In un client Windows creare un tunnel SSH PuTTY.

		1.  Aprire PuTTY e immettere le informazioni di connessione. Se non si ha familiarità con PuTTY, vedere l'articolo relativo all'[uso di SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md) per informazioni su come usarlo con HDInsight.
		2.  Nella sezione **Category** sul lato sinistro della finestra di dialogo espandere **Connection**, **SSH** e infine selezionare **Tunnels**.
		3.  Fornire le seguenti informazioni nel modulo **Options controlling SSH port forwarding**:

			* **Source port**: la porta del client che si desidera inoltrare. Ad esempio **8787**.
			* **Destination** (Destinazione): la destinazione che deve essere mappata al computer client locale. Ad esempio **localhost:8787**.

			![Creazione di un tunnel SSH](./media/hdinsight-hadoop-r-server-install-r-studio/createsshtunnel.png "Creazione di un tunnel SSH")

		4. Fare infine clic su **Add** per aggiungere le impostazioni, quindi su **Open** per aprire una connessione SSH.
		5. Quando richiesto, accedere al server. Verrà stabilita una sessione SSH e verrà abilitato il tunnel.

8. Aprire un Web browser e immettere l'URL seguente, in base alla porta specificata per il tunnel.

		http://localhost:8787/ 

9. Verrà richiesto di immettere il nome utente e la password SSH per connettersi al cluster. Se si usa una chiave SSH durante la creazione del cluster, è necessario immettere la password creata nel passaggio 5.

	![Connessione a R Studio](./media/hdinsight-hadoop-r-server-install-r-studio/connecttostudio.png "Creazione di un tunnel SSH")

10. Per verificare se l'installazione di RStudio è riuscita, eseguire uno script di test che esegue i processi MapReduce e Spark basati su R nel cluster. Tornare alla console SSH e immettere i comandi seguenti per scaricare lo script di test da eseguire in RStudio.

	* Se è stato creato un cluster Hadoop con R, usare questo comando.
		
			wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi.r

	* Se è stato creato un cluster Spark con R, usare questo comando.

			wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi_spark.r

11. In RStudio verrà visualizzato lo script di test che è stato scaricato. Fare doppio clic sul file per aprirlo, selezionare il contenuto del file e quindi fare clic su **Esegui**. L'output verrà visualizzato nel riquadro **Console**.
 
	![Verifica dell'installazione](./media/hdinsight-hadoop-r-server-install-r-studio/test-r-script.png "Verifica dell'installazione")

In alternativa è possibile digitare `source(testhdi.r)` o `source(testhdi_spark.r)` per eseguire lo script.

## Vedere anche

- [Opzioni del contesto di calcolo per R Server in cluster HDInsight](hdinsight-hadoop-r-server-compute-contexts.md)

- [Opzioni di Archiviazione di Azure per R Server in HDInsight Premium](hdinsight-hadoop-r-server-storage.md)


 

<!---HONumber=AcomDC_0914_2016-->