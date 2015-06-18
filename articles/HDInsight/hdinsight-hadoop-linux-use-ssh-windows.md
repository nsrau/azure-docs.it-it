<properties
   pageTitle="Usare chiavi SSH con Hadoop in HDInsight basato su Linux da Windows | Aure"
   description="Informazioni su come creare e usare chiavi SSH per l'autenticazione nei cluster HDInsight basati su Linux."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/20/2015"
   ms.author="larryfr"/>

#Usare SSH con Hadoop basato su Linux in HDInsight da Windows (anteprima)

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
- [Linux, Unix, OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

I cluster Azure HDInsight basati su Linux offrono la possibilità di usare l'accesso SSH (Secure Shell) tramite una password o una chiave SSH. Questo documento fornisce informazioni sulla connessione a HDInsight da client basati su Windows mediante il client SSH PuTTY.

> [AZURE.NOTE]I passaggi descritti in questo articolo presuppongono l'uso di un client basato su Windows. Se si usa un client Linux, Unix o OS X, vedere [Uso di SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

##Prerequisiti

* **PuTTY** e **PuTTYGen** per client basati su Windows. Queste utilità sono disponibili nella pagina Web <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>.

* Un moderno Web browser che supporta HTML5.

OPPURE

* [Azure CLI per Mac, Linux e Windows](../xplat-cli.md).

##Che cos'è SSH?

SSH è un'utilità per accedere ed eseguire in modalità remota i comandi in un server remoto. Con HDInsight basato su Linux, SSH stabilisce una connessione crittografata al nodo head del cluster e fornisce una riga di comando che consente di digitare i comandi. I comandi vengono quindi eseguiti direttamente sul server.

##Creare una chiave SSH (facoltativo)

Quando si crea un cluster HDInsight basato su Linux, è possibile usare una password o una chiave SSH per l'autenticazione nel server se è in uso il protocollo SSH. Le chiavi SSH vengono considerate più sicure perché sono basate su certificati. Se si prevede di usare chiavi SSH con il cluster, attenersi alle seguenti informazioni.

1. Aprire PuTTYGen.

2. Per **Type of key to generate** selezionare **SSH-2 RSA** e quindi fare clic su **Generate**.

	![PuTTYGen interface](./media/hdinsight-hadoop-linux-use-ssh-windows/puttygen.png)

3. Spostare il puntatore del mouse nell'area al di sotto dell'indicatore di stato finché la relativa barra non si riempie. Spostando il mouse si generano dati casuali che vengono usati per generare la chiave.

	![moving the mouse around](./media/hdinsight-hadoop-linux-use-ssh-windows/movingmouse.png)

	Dopo che la chiave è stata generata, verrà visualizzata la chiave pubblica.

4. Per maggiore sicurezza, è possibile immettere una passphrase nel campo **Key passphrase** e quindi digitare lo stesso valore nel campo **Confirm passphrase**.

	![passphrase](./media/hdinsight-hadoop-linux-use-ssh-windows/key.png)

	> [AZURE.NOTE]È consigliabile usare una passphrase sicura per la chiave. Tuttavia, se si dimentica la passphrase, non è possibile recuperarla.

5. Fare clic su **Save private key** per salvare la chiave in un file con estensione **ppk**. Questa chiave verrà usata per l'autenticazione nel cluster HDInsight basato su Linux.

	> [AZURE.NOTE]Conservare la chiave in un luogo sicuro, in quanto può essere usata per accedere al cluster HDInsight basato su Linux.

6. Fare clic su **Save public key** per salvare la chiave come file con estensione **txt**. In questo modo sarà possibile riusare la chiave pubblica in futuro, quando si creeranno altri cluster HDInsight basati su Linux.

	> [AZURE.NOTE]La chiave pubblica viene visualizzata anche nella parte superiore di PuTTYGen. È possibile fare clic con il pulsante destro del mouse su questo campo, copiare il valore e quindi incollarlo in un modulo, ad esempio nella procedura guidata HDInsight nel portale di Azure.

##Creare un cluster HDInsight basato su Linux

Quando si crea un cluster HDInsight basato su Linux, è necessario fornire la chiave pubblica creata in precedenza. Dai client basati su Windows è possibile creare un cluster HDInsight basato su Linux in due modi:

* **Portale di Azure**: viene usato un portale basato sul Web per creare il cluster.

* **Interfaccia della riga di comando di Azure**: usa i comandi della riga di comando per creare il cluster.

Ognuno di questi metodi richiede la chiave pubblica. Per informazioni dettagliate sulla creazione di un cluster HDInsight basato su Linux, vedere l'articolo <a href="./hdinsight-hadoop-provision-linux-clusters/" target="_blank">Provisioning di cluster Hadoop Linux in HDInsight con opzioni personalizzate (anteprima)</a>.

###Portale di Azure

Quando si usa il portale per creare un cluster HDInsight basato su Linux, è necessario immettere un nome utente e una password o una chiave pubblica nel modulo seguente:

![Image of form asking for public key](./media/hdinsight-hadoop-linux-use-ssh-windows/ssh-key.png)

Viene creato un account di accesso per l'utente specificato ed è possibile eseguire l'autenticazione tramite password o con chiave SSH.

###Interfaccia della riga di comando di Azure per Mac, Linux e Windows

È possibile usare l'[interfaccia della riga di comando di Azure per Mac, Linux e Windows](../xplat-cli.md) per creare un nuovo cluster tramite il comando `azure hdinsight cluster create`.

Per altre informazioni sull'uso di questo comando, vedere l'articolo <a href="../hdinsight-hadoop-provision-linux-clusters/" target="_blank">Provisioning di cluster Hadoop Linux in HDInsight con opzioni personalizzate</a>.

##Connettersi a un cluster HDInsight basato su Linux

1. Aprire PuTTY.

	![putty interface](./media/hdinsight-hadoop-linux-use-ssh-windows/putty.png)

2. Se è stata specificata una chiave SSH quando è stato creato l'account utente, è necessario effettuare il passaggio seguente per selezionare la chiave privata da usare durante l'autenticazione nel cluster:

	In **Category** espandere **Connection**, **SSH** e selezionare **Auth**. Infine, fare clic su **Browse** e selezionare il file ppk che contiene la chiave privata.

	![putty interface, select private key](./media/hdinsight-hadoop-linux-use-ssh-windows/puttykey.png)

3. In **Category** selezionare **Session**. Nella schermata **Basic options for your PuTTY session** immettere l'indirizzo SSH del server HDInsight nel campo **Host name (or IP address)**. L'indirizzo SSH è il nome del cluster, quindi **-ssh.azurehdinsight.net**. Ad esempio, **mycluster-ssh.azurehdinsight.net**.

	![putty interface with ssh address entered](./media/hdinsight-hadoop-linux-use-ssh-windows/puttyaddress.png)

4. Per salvare le informazioni di connessione per un uso futuro, immettere un nome per la connessione in **Saved Sessions**, quindi fare clic su **Save**. La connessione verrà aggiunta all'elenco delle sessioni salvate.

5. Fare clic su **Open** per connettersi al cluster.

	> [AZURE.NOTE]Se questa è la prima volta che ci si è connessi al cluster, si riceverà un avviso di sicurezza. Si tratta di una situazione normale.  Per continuare, fare clic su **Yes** per memorizzare nella cache la chiave RSA2 del server.

6. Quando richiesto, immettere il nome utente immesso durante la creazione del cluster. Se è stata specificata una password per l'utente, sarà necessario specificare anch'essa.

###Connettersi ai nodi di lavoro

I nodi di lavoro non sono direttamente accessibili dall'esterno del data center di Azure, ma è possibile accedervi dal nodo head del cluster tramite SSH.

Se è stata specificata una chiave SSH quando è stato creato l'account utente, è necessario eseguire la procedura seguente per selezionare la chiave privata da usare durante l'autenticazione nel cluster per connettersi ai nodi di lavoro.

1. Installare Pageant dalla pagina Web <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>. Questa utilità consente di memorizzare nella cache le chiavi SSH per PuTTY.

2. Eseguire Pageant. Si ridurrà a icona nella barra di stato. Fare clic con il pulsante destro del mouse sull'icona e scegliere **Add Key**.

    ![adding key](./media/hdinsight-hadoop-linux-use-ssh-windows/addkey.png)

3. Quando viene visualizzata la finestra Sfoglia, selezionare il file .ppk che contiene la chiave e quindi fare clic su **Apri**. La chiave verrà aggiunta a Pageant, che la fornirà a PuTTY durante la connessione al cluster.

    > [AZURE.IMPORTANT]Se si usa una chiave SSH per proteggere l'account, è necessario completare i passaggi precedenti per potersi connettere ai nodi di lavoro.

4. Aprire PuTTY.

5. Se si usa una chiave SSH per effettuare l'autenticazione, nella sezione **Category** espandere **Connection**, **SSH** e quindi selezionare **Auth**.

    Nella sezione **Authentication parameters** abilitare **Allow agent forwarding**. In questo modo PuTTY passa automaticamente l'autenticazione del certificato tramite la connessione al nodo head del cluster durante la connessione ai nodi di lavoro.

    ![allow agent forwarding](./media/hdinsight-hadoop-linux-use-ssh-windows/allowforwarding.png)

6. Connettersi al cluster come descritto in precedenza. Se si usa una chiave SSH per l'autenticazione, non è necessario selezionare la chiave: la chiave SSH aggiunta a Pageant verrà usata per l'autenticazione del cluster.

7. Dopo aver stabilito la connessione, usare il comando seguente per recuperare un elenco dei nodi del cluster. Sostituire *ADMINPASSWORD* con la password per l'account amministratore del cluster. Sostituire *CLUSTERNAME* con il nome del cluster.

        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts

    In questo modo le informazioni verranno sostituite nel formato JSON per i nodi del cluster, tra cui `host_name`, che contiene il nome di dominio completo (FQDN) per ogni nodo. Di seguito è riportato un esempio di una voce `host_name` restituita dal comando **curl**:

        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"

8. Dopo aver creato un elenco dei nodi di lavoro ai quali connettersi, usare il comando seguente dalla sessione PuTTY al server per aprire una connessione a un nodo di lavoro:

        ssh USERNAME@FQDN

    Sostituire *USERNAME* con il nome utente SSH e *FQDN* con il FQDN per il nodo di lavoro, ad esempio `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`.

    > [AZURE.NOTE]Se è stata usata una password per l'autenticazione della sessione SSH, verrà richiesto di immetterla di nuovo. Se si usa una chiave SSH, la connessione dovrebbe terminare senza alcuna richiesta.

9. Una volta stabilita la sessione, la richiesta per la sessione PuTTY cambierà da `username@headnode` a `username@workernode` per indicare che si è connessi al nodo di lavoro. Tutti i comandi eseguiti a questo punto verranno eseguiti sul nodo del lavoro.

10. Al termine dell'esecuzione di azioni su un nodo di lavoro, usare il comando `exit` per chiudere la sessione per il nodo di lavoro. In questo modo si tornerà alla richiesta `username@headnode`.

##Aggiungere altri account

Se è necessario aggiungere altri account al cluster, eseguire i passaggi seguenti:

1. Generare una nuova chiave pubblica e una nuova chiave privata per il nuovo account utente come descritto in precedenza.

2. Da una sessione SSH al cluster aggiungere il nuovo utente con il comando seguente:

		sudo adduser --disabled-password <username>

	Verrà creato un nuovo account utente, ma verrà disabilitata l'autenticazione tramite password.

3. Creare la directory e i file per contenere la chiave usando i comandi seguenti:

        sudo mkdir -p /home/<username>/.ssh
        sudo touch /home/<username>/.ssh/authorized_keys
        sudo nano /home/<username>/.ssh/authorized_keys

4. Quando si apre l'editor nano, copiare e incollare il contenuto della chiave pubblica per il nuovo account utente. Usare infine **Ctrl-X** per salvare il file e uscire dall'editor.

	![image of nano editor with example key](./media/hdinsight-hadoop-linux-use-ssh-windows/nano.png)

5. Usare il comando seguente per specificare il nuovo account utente come proprietario della cartella .ssh e del contenuto.

		sudo chown -hR <username>:<username> /home/<username>/.ssh

6. Sarà ora possibile eseguire l'autenticazione nel server con il nuovo account utente e la chiave privata.

##<a id="tunnel"></a>Tunneling SSH

SSH può essere usato anche per effettuare il tunneling di richieste locali, ad esempio richieste Web, al cluster HDInsight. La richiesta verrà quindi instradata alla risorsa richiesta come se provenisse dal nodo head del cluster HDInsight.

Ciò è particolarmente utile quando si accede a servizi basati sul Web nel cluster HDInsight che usano nomi di dominio interno per i nodi head o di lavoro del cluster. Ad esempio, alcune sezioni della pagina Web Ambari usano nomi di dominio interno come **headnode0.mycluster.d1.internal.cloudapp.net**. Questi nomi non possono essere risolti all'esterno del cluster, ma le richieste con tunneling su SSH hanno origine all'interno del cluster e vengono risolte correttamente.

Seguire questa procedura per creare un tunnel SSH e configurare il browser in modo che lo usi per connettersi al cluster:

1. Aprire PuTTY e immettere le informazioni di connessione come specificato in precedenza nella sezione [Connettersi a un cluster basato su Linux](#connect-to-a-linux-based-hdinsight-cluster).

2. Nella sezione **Category** sul lato sinistro della finestra di dialogo espandere **Connection**, **SSH** e infine selezionare **Tunnels**.

3. Fornire le seguenti informazioni nel modulo **Options controlling SSH port forwarding**:

	* **Source port**: la porta del client che si desidera inoltrare. Ad esempio, **9876**.

	* **Destination**: l'indirizzo SSH del cluster HDInsight basato su Linux. Ad esempio, **mycluster-ssh.azurehdinsight.net**.

	* **Dynamic**: consente il routing tramite proxy SOCKS dinamico.

	![image of tunneling options](./media/hdinsight-hadoop-linux-use-ssh-windows/puttytunnel.png)

4. Fare infine clic su **Add** per aggiungere le impostazioni, quindi su **Open** per aprire una connessione SSH.

5. Quando richiesto, accedere al server. Verrà stabilita una sessione SSH e verrà abilitato il tunnel.

6. Configurare il programma client, ad esempio Firefox, per l'uso di **localhost:9876** come proxy **SOCKS v5**. Ecco visualizzate le impostazioni di Firefox:

	![image of Firefox settings](./media/hdinsight-hadoop-linux-use-ssh-windows/socks.png)

	> [AZURE.NOTE]Se si seleziona **Remote DNS**, le richieste DNS verranno risolte usando il cluster HDInsight. Se questa opzione è deselezionata, il DNS verrà risolto localmente.

	È possibile verificare che il traffico sia instradato attraverso il tunnel visitando un sito come <a href="http://www.whatismyip.com/" target="_blank">http://www.whatismyip.com/</a> con le impostazioni proxy abilitate e disabilitate in Firefox. Mentre sono abilitate, l'indirizzo IP sarà per un computer nel data center di Microsoft Azure.

###Estensioni del browser

Quando si configura il browser per l'uso del tunnel, in genere non è opportuno instradare tutto il traffico attraverso il tunnel. Le estensioni del browser, ad esempio <a href="http://getfoxyproxy.org/" target="_blank">FoxyProxy</a>, supportano i criteri di ricerca per le richieste URL (solo FoxyProxy Standard o Plus) in modo che vengano inviate tramite tunnel solo le richieste relative a URL specifici.

Se è stato installato FoxyProxy Standard, seguire questa procedura per configurarlo in modo che inoltri tramite tunnel solo il traffico per HDInsight.

1. Aprire l'estensione FoxyProxy nel browser. Ad esempio, in Firefox fare clic sull'icona FoxyProxy accanto al campo dell'indirizzo.

	![foxyproxy icon](./media/hdinsight-hadoop-linux-use-ssh-windows/foxyproxy.png)

2. Selezionare **Add New Proxy**, fare clic sulla scheda **General** e immettere **HDInsightProxy** come nome proxy.

	![foxyproxy general](./media/hdinsight-hadoop-linux-use-ssh-windows/foxygeneral.png)

3. Fare clic sulla scheda **Proxy Details** e popolare i campi seguenti:

	* **Host or IP Address**: localhost perché viene usato un tunnel SSH nel computer locale.

	* **Port**: la porta usata per il tunnel SSH.

	* **SOCKS proxy**: selezionare questa opzione per consentire al browser di usare il tunnel come proxy.

	* **SOCKS v5**: selezionare questa opzione per impostare la versione necessaria per il proxy.

	![foxyproxy proxy](./media/hdinsight-hadoop-linux-use-ssh-windows/foxyproxyproxy.png)

4. Fare clic sulla scheda **URL Patterns** e selezionare  **Add New Pattern**. Usare i campi seguenti per definire il criterio, quindi fare clic su **OK**:

	* **Pattern Name** - **headnode**: si tratta di un nome descrittivo per il criterio.

	* **URL pattern** - ***headnode***: definisce un criterio che trova la corrispondenza con qualsiasi URL contenente la parola **headnode**.

	![foxyproxy pattern](./media/hdinsight-hadoop-linux-use-ssh-windows/foxypattern.png)

4. Fare clic su **OK** per aggiungere il proxy e chiudere la finestra **Proxy Settings**.

5. Nella parte superiore della finestra di dialogo FoxyProxy impostare **Select Mode** su **Use proxies based on their pre-defined patterns and priorities**, quindi fare clic su **Close**.

	![foxyproxy select mode](./media/hdinsight-hadoop-linux-use-ssh-windows/selectmode.png)

Dopo aver eseguito questa procedura, solo le richieste per gli URL contenenti la stringa **headnode** verranno instradate attraverso il tunnel SSL.

##Passaggi successivi

Ora che si è appreso come eseguire l'autenticazione usando una chiave SSH, è possibile imparare a usare MapReduce con Hadoop in HDInsight.

* [Usare Hive con HDInsight](hdinsight-use-hive.md)

* [Usare Pig con HDInsight](hdinsight-use-pig.md)

* [Usare processi MapReduce con HDInsight](hdinsight-use-mapreduce.md)

<!--HONumber=54--> 