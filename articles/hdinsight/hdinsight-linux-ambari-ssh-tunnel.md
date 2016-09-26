<properties
pageTitle="Usare il tunneling SSH per accedere all'interfaccia Web di Ambari, ResourceManager, JobHistory, NameNode, Oozie e altre interfacce Web."
description="Informazioni su come utilizzare un tunnel SSH per esplorare in modo sicuro le risorse web ospitate sui nodi HDInsight basati su Linux."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>  

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="07/22/2016"
ms.author="larryfr"/>

#Usare il tunneling SSH per accedere all'interfaccia Web di Ambari, JobHistory, NameNode, Oozie e altre interfacce Web

I cluster HDInsight basati su Linux forniscono l’accesso all’interfaccia web di Ambari su internet, ma alcune funzionalità dell’interfaccia utente non ci sono. Ad esempio, l'interfaccia utente web per altri servizi che vengono rilevati tramite Ambari. Per la funzionalità completa dell'interfaccia utente web di Ambari, è necessario utilizzare un tunnel SSH all’head del cluster.

##Cosa è necessario per un tunnel SSH?

Molti dei menu in Ambari non si popoleranno completamente senza un tunnel SSH, poiché si basano su siti web e servizi esposti da altri servizi di Hadoop in esecuzione nel cluster. Spesso questi siti web non sono protetti, pertanto non è opportuno esporli direttamente su internet. In alcuni casi il servizio esegue il sito web in un altro nodo del cluster, come ad esempio un nodo Zookeeper.

Di seguito ci sono dei servizi che l'interfaccia utente web Ambari utilizza, e a cui non è possibile accedere senza un tunnel SSH:

* JobHistory,
* NameNode,
* Thread Stacks,
* Interfaccia utente Web di Oozie
* HBase Master e l'interfaccia utente di Log

Se si utilizzano azioni di Script per personalizzare il cluster, tutti i servizi o le utilità che si installano che espongono un'interfaccia utente web richiederanno un tunnel SSH. Ad esempio, se si installa Hue utilizzando un'azione di Script, è necessario utilizzare un tunnel SSH per accedere all'interfaccia utente web di Hue.

##Che cos'è un tunnel SSH?

[Tunneling Secure Shell (SSH)](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) instrada il traffico inviato a una porta sulla workstation locale, tramite una connessione SSH al nodo head del cluster HDInsight, dove la richiesta viene risolta come se avesse avuto origine nel nodo head. La risposta viene instradata attraverso il tunnel alla workstation in uso.

##Prerequisiti

Quando si utilizza un tunnel SSH per il traffico web, è necessario disporre di quanto segue:

* Un client SSH. Per distribuzioni Linux e Unix o Macintosh OS X, il comando `ssh` viene offerto con il sistema operativo. Per Windows, è consigliabile [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

	> [AZURE.NOTE] Se si vuole usare un client SSH diverso da `ssh` o PuTTY, consultare la documentazione per il client su come stabilire un tunnel SSH.

* Un browser Web che può essere configurato per usare un proxy SOCKS

* __(facoltativo)__: un plug-in, ad esempio [FoxyProxy](http://getfoxyproxy.org/,) che possa applicare delle regole che instradano solo richieste specifiche attraverso il tunnel.

	> [AZURE.WARNING] Senza un plug-in come FoxyProxy, tutte le richieste effettuate tramite il browser possono essere instradate attraverso il tunnel. Ciò può comportare un caricamento più lento delle pagine web nel browser.

##<a name="usessh"></a>Creare un tunnel con il comando SSH

Utilizzare il comando seguente per creare un SSH tunnel utilizzando il comando `ssh`. Sostituire __NOME UTENTE__ con un utente SSH per il cluster HDInsight e sostituire __NOME CLUSTER__ con il nome del cluster HDInsight

	ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

Verrà creata una connessione che instrada il traffico alla porta locale 9876 al cluster su SSH. Le opzioni sono:

* **D 9876**: la porta locale che instraderà il traffico attraverso il tunnel.

* **C**: comprime tutti i dati, in quanto il traffico Web è costituito prevalentemente da testo.

* **2**: forza SSH a tentare solo il protocollo versione 2.

* **q**: modalità non interattiva.

* **T**: disabilita l'allocazione pseudo-tty perché si tratta semplicemente dell'inoltro di una porta.

* **n**: impedisce la lettura di STDIN perché si tratta semplicemente dell'inoltro di una porta.

* **N**: non esegue un comando remoto perché si tratta semplicemente dell'inoltro di una porta.

* **f**: effettua l'esecuzione in background.

Se il cluster è stato configurato con una chiave SSH, potrebbe essere necessario usare il parametro `-i` e specificare il percorso della chiave privata SSH.

Al termine del comando, il traffico inviato alla porta 9876 nel computer locale verrà instradato su SSL (Secure Sockets Layer) al nodo head del cluster e sembrerà provenire da tale nodo.

##<a name="useputty"></a>Creare un tunnel utilizzando PuTTY

Utilizzare la procedura seguente per creare un tunnel SSH utilizzando PuTTY.

1. Aprire PuTTY e immettere le informazioni di connessione. Se non si ha familiarità con PuTTY, vedere l'articolo relativo all'[uso di SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md) per informazioni su come usarlo con HDInsight.

2. Nella sezione **Category** sul lato sinistro della finestra di dialogo espandere **Connection**, **SSH** e infine selezionare **Tunnels**.

3. Fornire le seguenti informazioni nel modulo **Options controlling SSH port forwarding**:

	* **Source port**: la porta del client che si desidera inoltrare. Ad esempio, **9876**.

	* **Destination**: l'indirizzo SSH del cluster HDInsight basato su Linux. Ad esempio, **mycluster-ssh.azurehdinsight.net**.

	* **Dynamic**: consente il routing tramite proxy SOCKS dinamico.

	![image of tunneling options](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)

4. Fare infine clic su **Add** per aggiungere le impostazioni, quindi su **Open** per aprire una connessione SSH.

5. Quando richiesto, accedere al server. Verrà stabilita una sessione SSH e verrà abilitato il tunnel.

##Usare il tunnel dal browser

> [AZURE.NOTE] I passaggi descritti in questa sezione usano il browser FireFox, poiché è disponibile gratuitamente per i sistemi Linux, Unix, Macintosh OS X e Windows. Anche altri browser moderni come Google Chrome, Edge Microsoft o Apple Safari dovrebbero funzionare; tuttavia, il plug-in FoxyProxy usato in alcuni passaggi potrebbe non essere disponibile per tutti i browser.

1. Configurare il browser per usare **localhost:9876** come proxy **SOCKS v5**. Ecco visualizzate le impostazioni di Firefox. Se si usa una porta diversa da quella 9876, cambiare la porta con quella usata:

	![image of Firefox settings](./media/hdinsight-linux-ambari-ssh-tunnel/socks.png)

	> [AZURE.NOTE] Se si seleziona **Remote DNS**, le richieste DNS (Domain Name System) verranno risolte usando il cluster HDInsight. Se questa opzione è deselezionata, il DNS verrà risolto localmente.

2. Verificare che il traffico sia instradato attraverso il tunnel visitando un sito come [http://www.whatismyip.com/](http://www.whatismyip.com/) con le impostazioni proxy abilitate e disabilitate in Firefox. Mentre le impostazioni sono abilitate, l'indirizzo IP sarà per un computer nel data center di Microsoft Azure.

###Estensioni del browser

Quando si configura il browser per l'uso del tunnel, in genere non è opportuno instradare tutto il traffico attraverso il tunnel. Le estensioni del browser, ad esempio [FoxyProxy](http://getfoxyproxy.org/), supportano i criteri di ricerca per le richieste URL (solo FoxyProxy Standard o Plus), in modo che vengano inviate tramite tunnel solo le richieste relative a URL specifici.

Se è stato installato FoxyProxy Standard, seguire questa procedura per configurarlo in modo che inoltri tramite tunnel solo il traffico per HDInsight.

1. Aprire l'estensione FoxyProxy nel browser. Ad esempio, in Firefox fare clic sull'icona FoxyProxy accanto al campo dell'indirizzo.

	![foxyproxy icon](./media/hdinsight-linux-ambari-ssh-tunnel/foxyproxy.png)

2. Selezionare **Add New Proxy**, fare clic sulla scheda **General** e immettere **HDInsightProxy** come nome proxy.

	![foxyproxy general](./media/hdinsight-linux-ambari-ssh-tunnel/foxygeneral.png)

3. Fare clic sulla scheda **Proxy Details** e popolare i campi seguenti:

	* **Host or IP Address**: localhost perché viene usato un tunnel SSH nel computer locale.

	* **Port**: la porta usata per il tunnel SSH.

	* **SOCKS proxy**: selezionare questa opzione per consentire al browser di usare il tunnel come proxy.

	* **SOCKS v5**: selezionare questa opzione per impostare la versione necessaria per il proxy.

	![foxyproxy proxy](./media/hdinsight-linux-ambari-ssh-tunnel/foxyproxyproxy.png)

4. Fare clic sulla scheda **URL Patterns** e selezionare **Add New Pattern**. Usare i campi seguenti per definire il criterio, quindi fare clic su **OK**:

	* **Nome del modello** - **nodo del cluster**: si tratta di un nome descrittivo per il modello.

	* **Modello di URL** - ***internal.cloudapp.net*** -definisce un modello che corrisponde al nome di dominio completo interno dei nodi del cluster.

	![foxyproxy pattern](./media/hdinsight-linux-ambari-ssh-tunnel/foxypattern.png)

    Aggiungere un altro modello, usando le informazioni seguenti per le impostazioni:

    * __Nome modello __: headnode
    * __Modello URL__: *headnodehost *

    Fare clic su OK per salvare questo modello.

4. Fare clic su **OK** per aggiungere il proxy e chiudere la finestra **Proxy Settings**.

5. Nella parte superiore della finestra di dialogo FoxyProxy impostare **Select Mode** su **Use proxies based on their pre-defined patterns and priorities**, quindi fare clic su **Close**.

	![foxyproxy select mode](./media/hdinsight-linux-ambari-ssh-tunnel/selectmode.png)

Dopo aver eseguito questa procedura, solo le richieste per gli URL contenenti la stringa __internal.cloudapp.net__ verranno instradate attraverso il tunnel SSL.

##Verificare con l’interfaccia utente web di Ambari

Una volta stabilito il cluster, utilizzare la procedura seguente per verificare che sia possibile accedere all’interfaccia utente del servizio dal web Ambari:

1. Nel browser passare a http://headnodehost:8080. L'indirizzo `headnodehost` verrà inviato al cluster tramite il tunnel e verrà risolto nel nodo head in cui è in esecuzione Ambari. Quando richiesto, immettere il nome dell'account amministratore (admin) e la password per il cluster. Può essere richiesto una seconda volta dall'interfaccia utente web di Ambari. In tal caso, è necessario immettere nuovamente le informazioni.
    
    > [AZURE.NOTE] Quando si usa l'indirizzo http://headnodehost:8080 per connettersi al cluster, ci si connette direttamente tramite il tunnel al nodo head in cui è in esecuzione Ambari con HTTP e le comunicazioni sono protette tramite il tunnel SSH. Quando ci si connette a Internet senza tunnel, le comunicazioni sono protette tramite HTTPS. Per connettersi a Internet tramite HTTPS, usare https://CLUSTERNAME.azurehdinsight.net, in cui __CLUSTERNAME__ è il nome del cluster.

2. Dall'interfaccia utente Web di Ambari, selezionare HDFS dall'elenco a sinistra della pagina.

	![Immagine con HDFS selezionata](./media/hdinsight-linux-ambari-ssh-tunnel/hdfsservice.png)

3. Quando le informazioni del servizio HDFS vengono visualizzate, selezionare __Collegamenti rapidi__. Verrà visualizzato un elenco di nodi head del cluster. Selezionare uno dei nodi head e quindi selezionare l'__interfaccia utente di NameNode__.

	![Immagine con il menu di collegamenti rapidi espanso](./media/hdinsight-linux-ambari-ssh-tunnel/namenodedropdown.png)

	> [AZURE.NOTE] Se si dispone di una connessione internet lenta o il nodo head è molto occupato, è possibile che venga visualizzato un indicatore di attesa invece di un menu quando si seleziona __collegamenti rapidi__. In tal caso, attendere un minuto o due affinché i dati vengano ricevuti dal server, poi riprovare con l'elenco.
    >
	> Se si dispone di un monitor con risoluzione inferiore o la finestra del browser non è ingrandita, alcune voci del menu dei __collegamenti rapidi__ possono essere tagliate dal lato destro dello schermo. In tal caso, espandere il menu utilizzando il mouse, poi utilizzare il tasto freccia destra per scorrere la schermata verso destra e visualizzare il resto del menu.

4. Verrà visualizzata una pagina simile alla seguente:

	![Immagine dell'interfaccia utente di NameNode](./media/hdinsight-linux-ambari-ssh-tunnel/namenode.png)

	> [AZURE.NOTE] Si noti l'URL della pagina. dovrebbe essere simile a \_http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster__. Questo utilizza il nome di dominio interno completo (FQDN) del nodo e non è accessibile senza utilizzare un tunnel SSH.

##Passaggi successivi

Ora che si è appreso come creare e utilizzare un tunnel SSH, vedere gli argomenti seguenti per informazioni sul monitoraggio e la gestione del cluster utilizzando Ambari:

* [Gestire i cluster HDInsight mediante Ambari](hdinsight-hadoop-manage-ambari.md)

Per altre informazioni sull'uso di SSH con HDInsight, vedere gli articoli seguenti:

* [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

* [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

<!---HONumber=AcomDC_0914_2016-->