---
title: Usare il tunneling SSH per accedere all&quot;interfaccia Web di Ambari, ResourceManager, JobHistory, NameNode, Oozie e altre interfacce Web.
description: Informazioni su come utilizzare un tunnel SSH per esplorare in modo sicuro le risorse web ospitate sui nodi HDInsight basati su Linux.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 879834a4-52d0-499c-a3ae-8d28863abf65
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/17/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 446212192829cc55fefe4b1a1954e64e123c2c44


---
# <a name="use-ssh-tunneling-to-access-ambari-web-ui-jobhistory-namenode-oozie-and-other-web-uis"></a>Usare il tunneling SSH per accedere all'interfaccia Web di Ambari, JobHistory, NameNode, Oozie e altre interfacce Web
I cluster HDInsight basati su Linux forniscono l’accesso all’interfaccia web di Ambari su internet, ma alcune funzionalità dell’interfaccia utente non ci sono. Ad esempio, l'interfaccia utente web per altri servizi che vengono rilevati tramite Ambari. Per la funzionalità completa dell'interfaccia utente web di Ambari, è necessario utilizzare un tunnel SSH all’head del cluster.

## <a name="what-requires-an-ssh-tunnel"></a>Cosa è necessario per un tunnel SSH?
Molti dei menu in Ambari non si popoleranno completamente senza un tunnel SSH, poiché si basano su siti web e servizi esposti da altri servizi di Hadoop in esecuzione nel cluster. Spesso questi siti web non sono protetti, pertanto non è opportuno esporli direttamente su internet. In alcuni casi il servizio esegue il sito web in un altro nodo del cluster, come ad esempio un nodo Zookeeper.

Di seguito ci sono dei servizi che l'interfaccia utente web Ambari utilizza, e a cui non è possibile accedere senza un tunnel SSH:

* JobHistory,
* NameNode,
* Thread Stacks,
* Interfaccia utente Web di Oozie
* HBase Master e l'interfaccia utente di Log

Se si utilizzano azioni di Script per personalizzare il cluster, tutti i servizi o le utilità che si installano che espongono un'interfaccia utente web richiederanno un tunnel SSH. Ad esempio, se si installa Hue utilizzando un'azione di Script, è necessario utilizzare un tunnel SSH per accedere all'interfaccia utente web di Hue.

## <a name="what-is-an-ssh-tunnel"></a>Che cos'è un tunnel SSH?
[Tunneling Secure Shell (SSH)](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) instrada il traffico inviato a una porta sulla workstation locale, tramite una connessione SSH al nodo head del cluster HDInsight, dove la richiesta viene risolta come se avesse avuto origine nel nodo head. La risposta viene instradata attraverso il tunnel alla workstation in uso.

## <a name="prerequisites"></a>Prerequisiti
Quando si utilizza un tunnel SSH per il traffico web, è necessario disporre di quanto segue:

* Un client SSH. Per distribuzioni Linux e Unix o Macintosh OS X, il comando `ssh` viene offerto con il sistema operativo. Per Windows, è consigliabile [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
  
  > [!NOTE]
  > Se si vuole usare un client SSH diverso da `ssh` o PuTTY, consultare la documentazione per il client su come stabilire un tunnel SSH.
  > 
  > 
* Un browser Web che può essere configurato per usare un proxy SOCKS

## <a name="a-nameusesshacreate-a-tunnel-using-the-ssh-command"></a><a name="usessh"></a>Creare un tunnel con il comando SSH
Utilizzare il comando seguente per creare un SSH tunnel utilizzando il comando `ssh` . Sostituire **USERNAME** con un utente SSH per il cluster HDInsight e **CLUSTERNAME** con il nome del cluster HDInsight.

    ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

Verrà creata una connessione che instrada il traffico alla porta locale 9876 al cluster su SSH. Le opzioni sono:

* **D 9876** : la porta locale che instraderà il traffico attraverso il tunnel.
* **C** : comprime tutti i dati, in quanto il traffico Web è costituito prevalentemente da testo.
* **2** : forza SSH a tentare solo il protocollo versione 2.
* **q** : modalità non interattiva.
* **T** : disabilita l'allocazione pseudo-tty perché si tratta semplicemente dell'inoltro di una porta.
* **n** : impedisce la lettura di STDIN perché si tratta semplicemente dell'inoltro di una porta.
* **N** : non esegue un comando remoto perché si tratta semplicemente dell'inoltro di una porta.
* **f** : effettua l'esecuzione in background.

Se il cluster è stato configurato con una chiave SSH, potrebbe essere necessario usare il parametro `-i` e specificare il percorso della chiave privata SSH.

Al termine del comando, il traffico inviato alla porta 9876 nel computer locale verrà instradato su SSL (Secure Sockets Layer) al nodo head del cluster e sembrerà provenire da tale nodo.

## <a name="a-nameuseputtyacreate-a-tunnel-using-putty"></a><a name="useputty"></a>Creare un tunnel utilizzando PuTTY
Utilizzare la procedura seguente per creare un tunnel SSH utilizzando PuTTY.

1. Aprire PuTTY e immettere le informazioni di connessione. Se non si ha familiarità con PuTTY, vedere l'articolo relativo all' [uso di SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md) per informazioni su come usarlo con HDInsight.
2. Nella sezione **Category** sul lato sinistro della finestra di dialogo espandere **Connection**, **SSH** e infine selezionare **Tunnels**.
3. Fornire le seguenti informazioni nel modulo **Options controlling SSH port forwarding** :
   
   * **Source port** : la porta del client che si desidera inoltrare. Ad esempio, **9876**.
   * **Destination** : l'indirizzo SSH del cluster HDInsight basato su Linux. Ad esempio, **mycluster-ssh.azurehdinsight.net**.
   * **Dynamic** : consente il routing tramite proxy SOCKS dinamico.
     
     ![image of tunneling options](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)
4. Fare clic su **Add** per aggiungere le impostazioni, quindi su **Open** per aprire una connessione SSH.
5. Quando richiesto, accedere al server. Verrà stabilita una sessione SSH e verrà abilitato il tunnel.

## <a name="use-the-tunnel-from-your-browser"></a>Usare il tunnel dal browser
> [!NOTE]
> I passaggi descritti in questa sezione usano il browser FireFox, poiché è disponibile gratuitamente per i sistemi Linux, Unix, Macintosh OS X e Windows. È possibile usare altri browser moderni che supportano l'uso di un proxy SOCKS.
> 
> 

1. Configurare il browser per usare **localhost:9876** come proxy **SOCKS v5**. Ecco visualizzate le impostazioni di Firefox. Se si usa una porta diversa da quella 9876, cambiare la porta con quella usata:
   
    ![image of Firefox settings](./media/hdinsight-linux-ambari-ssh-tunnel/socks.png)
   
   > [!NOTE]
   > Se si seleziona **Remote DNS** , le richieste DNS (Domain Name System) verranno risolte usando il cluster HDInsight. Se questa opzione è deselezionata, il DNS verrà risolto localmente.
   > 
   > 
2. Verificare che il traffico sia instradato attraverso il tunnel visitando un sito come [http://www.whatismyip.com/](http://www.whatismyip.com/) con le impostazioni proxy abilitate e disabilitate in Firefox. Mentre le impostazioni sono abilitate, l'indirizzo IP sarà per un computer nel data center di Microsoft Azure.

## <a name="verify-with-ambari-web-ui"></a>Verificare con l’interfaccia utente web di Ambari
Una volta stabilito il cluster, utilizzare la procedura seguente per verificare che sia possibile accedere all’interfaccia utente del servizio dal web Ambari:

1. Nel browser passare a http://headnodehost:8080. L'indirizzo `headnodehost` verrà inviato al cluster tramite il tunnel e verrà risolto nel nodo head in cui è in esecuzione Ambari. Quando richiesto, immettere il nome dell'account amministratore (admin) e la password per il cluster. Può essere richiesto una seconda volta dall'interfaccia utente web di Ambari. In tal caso, è necessario immettere nuovamente le informazioni.
   
   > [!NOTE]
   > Quando si usa l'indirizzo http://headnodehost:8080 per connettersi al cluster, ci si connette direttamente tramite il tunnel al nodo head in cui è in esecuzione Ambari con HTTP e le comunicazioni sono protette tramite il tunnel SSH. Quando ci si connette a Internet senza tunnel, le comunicazioni sono protette tramite HTTPS. Quando ci si connette a Internet senza tunnel, le comunicazioni sono protette tramite HTTPS. Per connettersi a Internet tramite HTTPS, usare https://CLUSTERNAME.azurehdinsight.net, in cui **CLUSTERNAME** è il nome del cluster.
   > 
   > 
2. Dall'interfaccia utente Web di Ambari, selezionare HDFS dall'elenco a sinistra della pagina.
   
    ![Immagine con HDFS selezionata](./media/hdinsight-linux-ambari-ssh-tunnel/hdfsservice.png)
3. Quando le informazioni del servizio HDFS vengono visualizzate, selezionare **Collegamenti rapidi**. Verrà visualizzato un elenco di nodi head del cluster. Selezionare uno dei nodi head e quindi selezionare l' **interfaccia utente di NameNode**.
   
    ![Immagine con il menu di collegamenti rapidi espanso](./media/hdinsight-linux-ambari-ssh-tunnel/namenodedropdown.png)
   
   > [!NOTE]
   > Se si dispone di una connessione internet lenta o il nodo head è molto occupato, è possibile che venga visualizzato un indicatore di attesa invece di un menu quando si seleziona **collegamenti rapidi**. In tal caso, attendere un minuto o due affinché i dati vengano ricevuti dal server, poi riprovare con l'elenco.
   > 
   > Se si dispone di un monitor con risoluzione inferiore o la finestra del browser non è ingrandita, alcune voci del menu dei **collegamenti rapidi** possono essere tagliate dal lato destro dello schermo. In tal caso, espandere il menu utilizzando il mouse, poi utilizzare il tasto freccia destra per scorrere la schermata verso destra e visualizzare il resto del menu.
   > 
   > 
4. Verrà visualizzata una pagina simile alla seguente:
   
    ![Immagine dell'interfaccia utente di NameNode](./media/hdinsight-linux-ambari-ssh-tunnel/namenode.png)
   
   > [!NOTE]
   > Si noti l'URL della pagina, che dovrebbe essere simile a **http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster**. Questo utilizza il nome di dominio interno completo (FQDN) del nodo e non è accessibile senza utilizzare un tunnel SSH.
   > 
   > 

## <a name="next-steps"></a>Passaggi successivi
Ora che si è appreso come creare e utilizzare un tunnel SSH, vedere gli argomenti seguenti per informazioni sul monitoraggio e la gestione del cluster utilizzando Ambari:

* [Gestire i cluster HDInsight mediante Ambari](hdinsight-hadoop-manage-ambari.md)

Per altre informazioni sull'uso di SSH con HDInsight, vedere gli articoli seguenti:

* [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)




<!--HONumber=Nov16_HO3-->


