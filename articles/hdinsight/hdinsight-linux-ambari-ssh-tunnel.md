---
title: Usare il tunneling SSH per accedere ad Azure HDInsight | Microsoft Docs
description: Informazioni su come utilizzare un tunnel SSH per esplorare in modo sicuro le risorse web ospitate sui nodi HDInsight basati su Linux.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 879834a4-52d0-499c-a3ae-8d28863abf65
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/07/2018
ms.author: larryfr
ms.openlocfilehash: a6604cca4056acf3ce759eaf56bb9130ef672bc7
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="use-ssh-tunneling-to-access-ambari-web-ui-jobhistory-namenode-oozie-and-other-web-uis"></a>Usare il tunneling SSH per accedere all'interfaccia utente Web di Ambari, JobHistory, NameNode, Oozie e altre interfacce utente Web

I cluster HDInsight basati su Linux forniscono l’accesso all’interfaccia web di Ambari su internet, ma alcune funzionalità dell’interfaccia utente non ci sono. Ad esempio, l'interfaccia utente web per altri servizi che vengono rilevati tramite Ambari. Per la funzionalità completa dell'interfaccia utente web di Ambari, è necessario utilizzare un tunnel SSH all’head del cluster.

## <a name="why-use-an-ssh-tunnel"></a>Perché usare un tunnel SSH

Diversi menu di Ambari funzionano solo con un tunnel SSH. Questi menu si basano su servizi e siti Web in esecuzione in altri tipi di nodi, ad esempio i nodi di lavoro. Spesso questi siti web non sono protetti, pertanto non è opportuno esporli direttamente su internet.

Le interfacce utente Web seguenti richiedono un tunnel SSH:

* JobHistory
* NameNode
* Thread Stacks
* Interfaccia utente Web di Oozie
* HBase Master e l'interfaccia utente di Log

Se si usano azioni script per personalizzare il cluster, tutti i servizi o le utilità installate che espongono un'interfaccia utente Web richiedono un tunnel SSH. Ad esempio, se si installa Hue utilizzando un'azione di Script, è necessario utilizzare un tunnel SSH per accedere all'interfaccia utente web di Hue.

> [!IMPORTANT]
> Se si ha accesso diretto a HDInsight tramite una rete virtuale, non è necessario usare tunnel SSH. Per un esempio di accesso diretto a HDInsight tramite una rete virtuale, vedere il documento [Connettere HDInsight alla rete locale](connect-on-premises-network.md).

## <a name="what-is-an-ssh-tunnel"></a>Che cos'è un tunnel SSH

Il [tunneling Secure Shell (SSH)](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) instrada il traffico inviato a una porta nella workstation locale. Il traffico viene instradato tramite una connessione SSH al nodo head del cluster HDInsight. La richiesta viene risolta come se fosse stata originata nel nodo head. La risposta viene instradata attraverso il tunnel alla workstation in uso.

## <a name="prerequisites"></a>prerequisiti

* Un client SSH. La maggior parte dei sistemi operativi offre un client SSH tramite il comando `ssh`. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* Un Web browser che può essere configurato per l'uso di un proxy SOCKS5.

    > [!WARNING]
    > Il supporto per il proxy SOCKS integrato in Windows non include il supporto per SOCKS5 e non può quindi essere usato per la procedura descritta in questo documento. I browser seguenti si basano sulle impostazioni proxy di Windows e non possono essere usati per la procedura illustrata in questo documento:
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > Anche Google Chrome si basa sulle impostazioni proxy di Windows. In questo caso, tuttavia, è possibile installare estensioni che supportano SOCKS5, tra cui [FoxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp) (opzione consigliata).

## <a name="usessh"></a>Creare un tunnel con il comando SSH

Utilizzare il comando seguente per creare un SSH tunnel utilizzando il comando `ssh` . Sostituire **USERNAME** con un utente SSH per il cluster HDInsight e **CLUSTERNAME** con il nome del cluster HDInsight:

```bash
ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
```

Questo comando crea una connessione che instrada il traffico alla porta locale 9876 al cluster su SSH. Le opzioni sono:

* **D 9876**: la porta locale che instrada il traffico attraverso il tunnel.
* **C** : comprime tutti i dati, in quanto il traffico Web è costituito prevalentemente da testo.
* **2** : forza SSH a tentare solo il protocollo versione 2.
* **q** : modalità non interattiva.
* **T** : disabilita l'allocazione pseudo-tty perché si tratta semplicemente dell'inoltro di una porta.
* **n**: impedisce la lettura di STDIN perché si tratta semplicemente dell'inoltro di una porta.
* **N** : non esegue un comando remoto perché si tratta semplicemente dell'inoltro di una porta.
* **f** : effettua l'esecuzione in background.

Al termine del comando, il traffico inviato alla porta 9876 nel computer locale viene instradato al nodo head del cluster.

## <a name="useputty"></a>Creare un tunnel utilizzando PuTTY

[PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty) è un client SSH grafico per Windows. Usare la procedura seguente per creare un tunnel SSH con PuTTY:

1. Aprire PuTTY e immettere le informazioni di connessione. Se non si ha familiarità con PuTTY, vedere la [documentazione di PuTTY (http://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html)](http://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html).

2. Nella sezione **Category** sul lato sinistro della finestra di dialogo espandere **Connection**, **SSH** e infine selezionare **Tunnels**.

3. Fornire le seguenti informazioni nel modulo **Options controlling SSH port forwarding** :
   
   * **Source port** : la porta del client che si desidera inoltrare. Ad esempio, **9876**.

   * **Destination** : l'indirizzo SSH del cluster HDInsight basato su Linux. Ad esempio, **mycluster-ssh.azurehdinsight.net**.

   * **Dynamic** : consente il routing tramite proxy SOCKS dinamico.
     
     ![image of tunneling options](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)

4. Fare clic su **Add** per aggiungere le impostazioni, quindi su **Open** per aprire una connessione SSH.

5. Quando richiesto, accedere al server.

## <a name="use-the-tunnel-from-your-browser"></a>Usare il tunnel dal browser

> [!IMPORTANT]
> Nella procedura illustrata in questa sezione viene usato il browser Mozilla FireFox, poiché prevede le stesse impostazioni proxy per tutte le piattaforme. È possibile che altri browser moderni, tra cui Google Chrome, richiedano un'estensione come FoxyProxy per poter interagire con il tunnel.

1. Configurare il browser in modo che usi **localhost** e la porta usata al momento della creazione del tunnel come proxy **SOCKS v5**. Ecco visualizzate le impostazioni di Firefox. Se si usa una porta diversa da quella 9876, cambiare la porta con quella usata:
   
    ![image of Firefox settings](./media/hdinsight-linux-ambari-ssh-tunnel/firefoxproxy.png)
   
   > [!NOTE]
   > Se si seleziona **Remote DNS**, le richieste DNS (Domain Name System) vengono risolte usando il cluster HDInsight. Questa impostazione risolve DNS usando il nodo head del cluster.

2. Per verificare il funzionamento del tunnel, visitare un sito, ad esempio [http://www.whatismyip.com/](http://www.whatismyip.com/). L'indirizzo IP restituito deve essere uno usato dal data center di Microsoft Azure.

## <a name="verify-with-ambari-web-ui"></a>Verificare con l’interfaccia utente web di Ambari

Una volta stabilito il cluster, utilizzare la procedura seguente per verificare che sia possibile accedere all’interfaccia utente del servizio dal web Ambari:

1. Nel browser passare a http://headnodehost:8080. L'indirizzo `headnodehost` viene inviato al cluster tramite il tunnel e quindi risolto nel nodo head in cui è in esecuzione Ambari. Quando richiesto, immettere il nome dell'account amministratore (admin) e la password per il cluster. Può essere richiesto una seconda volta dall'interfaccia utente web di Ambari. In tal caso, è necessario immettere nuovamente le informazioni.

   > [!NOTE]
   > Quando si usa l'indirizzo http://headnodehost:8080 per connettersi al cluster, la connessione viene stabilita tramite il tunnel. La comunicazione viene protetta usando il tunnel SSH invece di HTTPS. Per connettersi a Internet tramite HTTPS, usare https://CLUSTERNAME.azurehdinsight.net, in cui **CLUSTERNAME** è il nome del cluster.

2. Dall'interfaccia utente Web di Ambari, selezionare HDFS dall'elenco a sinistra della pagina.

    ![Immagine con HDFS selezionata](./media/hdinsight-linux-ambari-ssh-tunnel/hdfsservice.png)

3. Quando le informazioni del servizio HDFS vengono visualizzate, selezionare **Collegamenti rapidi**. Verrà visualizzato un elenco di nodi head del cluster. Selezionare uno dei nodi head e quindi selezionare l' **interfaccia utente di NameNode**.

    ![Immagine con il menu di collegamenti rapidi espanso](./media/hdinsight-linux-ambari-ssh-tunnel/namenodedropdown.png)

   > [!NOTE]
   > Quando si seleziona __Quick Links__ (Collegamenti rapidi), è possibile che venga visualizzato un indicatore di attesa. Questa condizione può verificarsi se la connessione Internet è lenta. Attendere un minuto o due perché i dati vengano ricevuti dal server, poi riprovare con l'elenco.
   >
   > Alcune voci del menu **Quick Links** (Collegamenti rapidi) potrebbero risultare troncate sul lato destro della schermata. In tal caso, espandere il menu con il mouse e usare il tasto freccia destra per scorrere la schermata verso destra e visualizzare il resto del menu.

4. Viene visualizzata una pagina simile all'immagine seguente:

    ![Immagine dell'interfaccia utente di NameNode](./media/hdinsight-linux-ambari-ssh-tunnel/namenode.png)

   > [!NOTE]
   > Si noti l'URL della pagina, che dovrebbe essere simile a **http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster**. Questo URI usa il nome di dominio interno completo (FQDN) del nodo ed è accessibile solo quando si usa un tunnel SSH.

## <a name="next-steps"></a>Passaggi successivi

Dopo avere appreso come creare e usare un tunnel SSH, vedere il documento seguente per conoscere gli altri modi di usare Ambari:

* [Gestire i cluster HDInsight mediante Ambari](hdinsight-hadoop-manage-ambari.md)

Per altre informazioni sull'uso di SSH con HDInsight, vedere l'articolo [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

