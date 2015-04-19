<properties
   pageTitle="Usare chiavi SSH con Hadoop in HDInsight basato su Linux da Linux, Unix, or OS X | Aure"
   description="Informazioni su come creare e usare chiavi SSH per l'autenticazione nei cluster HDInsight basati su Linux."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

##Uso di SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X (anteprima)

I cluster HDInsight basati su Linux offrono la possibilità di proteggere l'accesso SSH usando una password o una chiave SSH. Questo documento fornisce informazioni sull'uso di SSH con HDInsight da client OS X, Unix o Linux.

> [AZURE.NOTE] I passaggi descritti in questo articolo presuppongono l'uso di un client Linux, Unix o OS X. Sebbene questa procedura possa essere eseguita su un client Windows se è stato installato un pacchetto che fornisce `ssh` e `ssh-keygen`, ad esempio Git per Windows, per i client Windows è consigliabile eseguire la procedura descritta in [Usare SSH con HDInsight (Hadoop) basato su Linux da Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

##Prerequisiti

* **ssh-keygen** e **ssh** per i client Linux, Unix e OS X. Questa utilità viene in genere fornita con il sistema operativo o è disponibile tramite il sistema di gestione pacchetti.

* Un moderno Web browser che supporta HTML5.

OPPURE

* Strumenti della riga di comando multipiattaforma di Azure

##Che cos'è SSH?

SSH è un'utilità per accedere ed eseguire in modalità remota i comandi in un server remoto. Con HDInsight basato su Linux, SSH stabilisce una connessione sicura al nodo head del cluster e fornisce una riga di comando che consente di immettere i comandi. I comandi vengono quindi eseguiti direttamente sul server.

##Creare una chiave SSH (facoltativo)

Quando si crea un cluster HDInsight basato su Linux, è possibile usare una password o una chiave SSH per l'autenticazione nel server se è in uso il protocollo SSH. Le chiavi SSH vengono considerate più sicure perché sono basate su certificati. Se si prevede di usare chiavi SSH con il cluster, attenersi alle seguenti informazioni.

1. Aprire una sessione terminal e usare il seguente comando per verificare se sono disponibili chiavi SSH esistenti.

		ls -al ~/.ssh

	cercare i seguenti file nell'elenco di directory. Si tratta di nomi comuni per le chiavi pubbliche SSH:

	* id\_dsa.pub
	* id\_ecdsa.pub
	* id\_ed25519.pub
	* id\_rsa.pub

2. Se non si desidera usare un file esistente o non si dispone di chiavi SSH, eseguire il seguente comando per generare un nuovo file.

		ssh-keygen -t rsa

	Verrà richiesto di specificare le seguenti informazioni:

	* Il percorso del file: l'impostazione predefinita è ~/.ssh/id\_rsa.
	* Una passphrase: verrà richiesto di immetterla una seconda volta.
	
		> [AZURE.NOTE] È consigliabile usare una passphrase sicura per la chiave. Tuttavia, se si dimentica la passphrase, non è possibile recuperarla.

	Al termine del comando, si disporrà di due nuovi file: la chiave privata (ad esempio **id\_rsa**) e la chiave pubblica (ad esempio **id\_rsa.pub**).

##Creare un cluster HDInsight basato su Linux

Quando si crea un cluster HDInsight basato su Linux, è necessario fornire la **chiave pubblica** creata in precedenza. Da client Linux, Unix o OS X è possibile creare un cluster HDInsight in due modi:

* **Portale di gestione di Azure**: viene usato un portale basato sul Web per creare il cluster.

* **Interfaccia della riga di comando multipiattaforma di Azure (xplat-cli)**: vengono usati comandi della riga di comando per creare il cluster

Ognuno di questi metodi richiederà un **password** o una **chiave pubblica**. Per informazioni dettagliate sulla creazione di un cluster HDInsight basato su Linux, vedere l'articolo relativo al <a href="/documentation/articles/hdinsight-hadoop-provision-linux-clusters/" target="_blank">provisioning dei cluster HDInsight basati su Linux</a>.

###Portale di gestione di Azure

Quando si usa il portale per creare un cluster HDInsight basato su Linux, è necessario completare il campo **SSH User Name** e selezionare **Password** o **SSH Public Key**. Se si seleziona **SSH Public Key**, è necessario incollare la chiave pubblica (contenuta nel file con estensione **pub**) nel seguente modulo.

![Image of form asking for public key](./media/hdinsight-hadoop-linux-use-ssh-unix/ssh-key.png)

> [AZURE.NOTE] Il file della chiave è semplicemente un file di testo. Il contenuto dovrebbe essere simile al seguente.
> ```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCelfkjrpYHYiks4TM+r1LVsTYQ4jAXXGeOAF9Vv/KGz90pgMk3VRJk4PEUSELfXKxP3NtsVwLVPN1l09utI/tKHQ6WL3qy89WVVVLiwzL7tfJ2B08Gmcw8mC/YoieT/YG+4I4oAgPEmim+6/F9S0lU2I2CuFBX9JzauX8n1Y9kWzTARST+ERx2hysyA5ObLv97Xe4C2CQvGE01LGAXkw2ffP9vI+emUM+VeYrf0q3w/b1o/COKbFVZ2IpEcJ8G2SLlNsHWXofWhOKQRi64TMxT7LLoohD61q2aWNKdaE4oQdiuo8TGnt4zWLEPjzjIYIEIZGk00HiQD+KCB5pxoVtp user@system
> ```

Verrà creato un account di accesso per l'utente specificato, usando la password o la chiave pubblica fornita.

###Interfaccia della riga di comando multipiattaforma di Azure

È possibile usare l'<a href="../xplat-cli/" target="_brad">interfaccia della riga di comando multipiattaforma di Azure</a> per creare un nuovo cluster usando il comando  `azure hdinsight cluster create`.

Per altre informazioni sull'uso di questo comando, vedere l'articolo relativo al <a href="../hdinsight-hadoop-provision-linux-clusters/" target="_blank">provisioning dei cluster Hadoop Linux in HDInsight con opzioni personalizzate</a>

##Connettersi a un cluster HDInsight basato su Linux

Da una sessione terminal usare il comando SSH per la connessione al cluster specificando il nome utente e l'indirizzo del cluster.

* **Indirizzo SSH**: il nome del cluster, seguito da **-ssh.azurehdinsight.net**. Ad esempio, **mycluster-ssh.azurehdinsight.net**.

* **Nome utente**: il nome utente SSH fornito durante la creazione del cluster.

Il seguente esempio consentirà di connettersi al cluster **mycluster** come utente **me**.

	ssh me@mycluster-ssh.azurehdinsight.net

Se è stata usata una **password** per l'account utente, verrà richiesto di immetterla.

Se è stata usata una **chiave SSH** protetta con una passphrase, verrà richiesto di immettere la passphrase. In caso contrario, SSH tenterà di eseguire automaticamente l'autenticazione usando una delle chiavi private locali nel client.

> [AZURE.NOTE] Se SSH non esegue automaticamente l'autenticazione con la **chiave privata** corretta, usare il parametro **-i** e specificare il percorso della chiave privata. Nell'esempio seguente la **chiave privata** verrà caricata da `~/.ssh/id_rsa`.
> 
> `ssh -i ~/.ssh/id_rsa me@mycluster-ssh.azurehdinsight.net`

##Aggiungere altri account

2. Generare una nuova **chiave pubblica** e una nuova **chiave privata** per il nuovo account utente come descritto nella sezione [Creare una chiave SSH](#create) .

	> [AZURE.NOTE] La chiave privata deve essere generata in un client che l'utente userà per connettersi al cluster o essere trasferita in modo sicuro in tale client dopo la creazione.

1. Da una sessione SSH al cluster aggiungere il nuovo utente con il seguente comando.

		sudo adduser --disabled-password <username> 

	Verrà creato un nuovo account utente, ma verrà disabilitata l'autenticazione tramite password.

2. Creare la directory e i file che conterranno la chiave usando i seguenti comandi.

		sudo mkdir -p /home/<username>/.ssh
		sudo touch /home/<username>/.ssh/authorized_keys
		sudo nano /home/<username>/.ssh/authorized_keys

3. Quando si apre l'editor nano, copiare e incollare il contenuto della **chiave pubblica** per il nuovo account utente. Usare infine **CTRL-X** per salvare il file e uscire dall'editor.

	![image of nano editor with example key](./media/hdinsight-hadoop-linux-use-ssh-unix/nano.png)

4. Usare il seguente comando per specificare il nuovo account utente come proprietario della cartella .ssh e del contenuto.

		sudo chown -hR <username>:<username> /home/<username>/.ssh

5. Sarà ora possibile eseguire l'autenticazione nel server con il nuovo account utente e la **chiave privata**.

##<a id="tunnel"></a>Tunneling SSH

SSH può essere usato anche per effettuare il tunneling di richieste locali, ad esempio richieste Web, al cluster HDInsight. La richiesta verrà quindi instradata alla risorsa richiesta come se provenisse dal nodo head del cluster HDInsight.

Ciò è particolarmente utile quando si accede a servizi basati sul Web nel cluster HDInsight che usano nomi di dominio interno per i nodi head o di lavoro del cluster. Ad esempio, alcune sezioni della pagina Web Ambari usano nomi di dominio interno come **headnode0.mycluster.d1.internal.cloudapp.net**. Questi nomi non possono essere risolti all'esterno del cluster, tuttavia le richieste con tunneling su SSH hanno origine all'interno del cluster e vengono risolte correttamente.

Seguire questa procedura per creare un tunnel SSH e configurare il browser in modo che lo usi per connettersi al cluster.

1. Il seguente comando consente di creare un tunnel SSH al nodo head del cluster.

		ssh -C2qTnNf -D 9876 username@clustername-ssh.azurehdinsight.net

	Verrà creata una connessione che instrada il traffico alla porta locale 9876 al cluster su SSH. Le opzioni sono:

	* **D 8080**: la porta locale che instraderà il traffico attraverso il tunnel

	* **C**: comprime tutti i dati, in quanto il traffico Web è costituito prevalentemente da testo

	* **2**: forza SSH a tentare solo il protocollo versione 2

	* **q**: modalità non interattiva

	* **T**: disabilita l'allocazione pseudo-tty perché si tratta semplicemente dell'inoltro di una porta
	
	* **n**: impedisce la lettura di STDIN perché si tratta semplicemente dell'inoltro di una porta

	* **N**: non esegue un comando remoto perché si tratta semplicemente dell'inoltro di una porta

	* **f**: effettua l'esecuzione in background

	Se il cluster è stato configurato con una **chiave SSH**, potrebbe essere necessario usare il parametro`-i`  e specificare il percorso della chiave privata SSH.

	Al termine del comando, il traffico inviato alla porta 9876 nel computer locale verrà instradato su SSL al nodo head del cluster e sembrerà provenire da tale nodo.

2. Configurare il programma client, ad esempio Firefox, per l'uso di **localhost:9876** come proxy **SOCKS v5**. Ecco visualizzate le impostazioni di Firefox.

	![image of Firefox settings](./media/hdinsight-hadoop-linux-use-ssh-unix/socks.png)

	> [AZURE.NOTE] Se si seleziona **Remote DNS**, le richieste DNS verranno risolte usando il cluster HDInsight. Se non si seleziona questa opzione, il DNS verrà risolto localmente.

	È possibile verificare che il traffico sia instradato attraverso il tunnel visitando un sito come <a href="http://www.whatismyip.com/" target="_blank">http://www.whatismyip.com/</a> con le impostazioni proxy abilitate e disabilitate in Firefox. Mentre sono abilitate, l'indirizzo IP sarà per un computer nel data center di Microsoft Azure.

###Estensioni del browser

Quando si configura il browser per l'uso del tunnel, in genere non è opportuno instradare tutto il traffico attraverso il tunnel. Le estensioni del browser, ad esempio <a href="http://getfoxyproxy.org/" target="_blank">FoxyProxy</a>, supportano i criteri di ricerca per le richieste URL (solo FoxyProxy Standard o Plus) in modo che vengano inviate tramite tunnel solo le richieste relative a URL specifici.

Se è stato installato **FoxyProxy Standard**, seguire questa procedura per configurarlo in modo che inoltri tramite tunnel solo il traffico per HDInsight.

1. Aprire l'estensione FoxyProxy nel browser. Ad esempio, in Firefox fare clic sull'icona FoxyProxy accanto al campo dell'indirizzo.

	![foxyproxy icon](./media/hdinsight-hadoop-linux-use-ssh-unix/foxyproxy.png)

2. Selezionare **Add New Proxy**, fare clic sulla scheda **General** e immettere **HDInsight** come nome proxy.

	![foxyproxy general](./media/hdinsight-hadoop-linux-use-ssh-unix/foxygeneral.png)

3. Fare clic sulla scheda **Proxy Details** e completare i seguenti campi.

	* **Host or IP Address**: localhost perché viene usato un tunnel SSH nel computer locale

	* **Port**: la porta usata per il tunnel SSH

	* **SOCKS Proxy**: selezionare questa opzione per consentire al browser di usare il tunnel come proxy

	* **SOCKS v5**: selezionare questa impostazione per impostare la versione necessaria per il proxy

	![foxyproxy proxy](./media/hdinsight-hadoop-linux-use-ssh-unix/foxyproxyproxy.png)

4. Fare clic sulla scheda **URL Patterns** e selezionare **Add New Pattern**. Usare i seguenti campi per definire il criterio, quindi fare clic su **OK**.

	* **Pattern Name** - **headnode**: si tratta di un nome descrittivo per il criterio

	* **URL pattern** - **\*headnode\***: definisce un criterio che trova la corrispondenza con qualsiasi URL contenente la parola **headnode**.

	![foxyproxy pattern](./media/hdinsight-hadoop-linux-use-ssh-unix/foxypattern.png)

4. Fare clic su **OK** per aggiungere il proxy e chiudere la finestra **Proxy Settings**

5. Nella parte superiore della finestra di dialogo FoxyProxy impostare **Select Mode** su **Use proxies based on their pre-defined patterns and priorities**, quindi fare clic su **Close**.

	![foxyproxy select mode](./media/hdinsight-hadoop-linux-use-ssh-unix/selectmode.png)

Dopo aver eseguito questa procedura, solo le richieste per gli URL contenenti la stringa **headnode** verranno instradate attraverso il tunnel SSL.

##Passaggi successivi

Ora che si è appreso come eseguire l'autenticazione usando una chiave SSH, è possibile imparare a usare MapReduce con Hadoop in HDInsight.

* [Usare Hive con HDInsight](hdinsight-use-hive.md)

* [Usare Pig con HDInsight](hdinsight-use-pig.md)

* [Usare processi MapReduce con HDInsight](hdinsight-use-mapreduce.md)
 
<!--HONumber=47-->
