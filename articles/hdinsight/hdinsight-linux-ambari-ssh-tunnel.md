---
title: Usare il tunneling SSH per accedere ad Azure HDInsight
description: Informazioni su come utilizzare un tunnel SSH per esplorare in modo sicuro le risorse web ospitate sui nodi HDInsight basati su Linux.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: hrasheed
ms.openlocfilehash: 943bf0f4bba014c31a11bb30bf8d3b6a7c11a343
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299365"
---
# <a name="use-ssh-tunneling-to-access-apache-ambari-web-ui-jobhistory-namenode-apache-oozie-and-other-web-uis"></a>Usare il tunneling SSH per accedere all'interfaccia utente Web di Apache Ambari, JobHistory, NameNode, Apache Oozie e altre interfacce utente Web

I cluster HDInsight offrono accesso all'interfaccia utente Web di Apache Ambari in Internet, ma alcune funzionalità richiedono un tunnel SSH. Ad esempio, l'interfaccia utente Web per il servizio Apache Oozie non è accessibile in Internet senza un tunnel SSH.

## <a name="why-use-an-ssh-tunnel"></a>Perché usare un tunnel SSH

Diversi menu di Ambari funzionano solo con un tunnel SSH. Questi menu si basano su servizi e siti Web in esecuzione in altri tipi di nodi, ad esempio i nodi di lavoro.

Le interfacce utente Web seguenti richiedono un tunnel SSH:

* JobHistory
* NameNode
* Thread Stacks
* Interfaccia utente Web di Oozie
* HBase Master e l'interfaccia utente di Log

Se si usano azioni script per personalizzare il cluster, tutti i servizi o le utilità installate che espongono un servizio Web richiedono un tunnel SSH. Ad esempio, se si installa Hue utilizzando un'azione di Script, è necessario utilizzare un tunnel SSH per accedere all'interfaccia utente web di Hue.

> [!IMPORTANT]  
> Se si ha accesso diretto a HDInsight tramite una rete virtuale, non è necessario usare tunnel SSH. Per un esempio di accesso diretto a HDInsight tramite una rete virtuale, vedere il documento [Connettere HDInsight alla rete locale](connect-on-premises-network.md).

## <a name="what-is-an-ssh-tunnel"></a>Che cos'è un tunnel SSH

Il [tunneling di Secure Shell, ovvero SSH,](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) connette una porta del computer locale a un nodo head in HDInsight. Il traffico inviato alla porta locale viene instradato attraverso una connessione SSH al nodo head. La richiesta viene risolta come se fosse stata originata nel nodo head. La risposta viene instradata attraverso il tunnel alla workstation in uso.

## <a name="prerequisites"></a>Prerequisiti

* Un client SSH. Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* Un Web browser che può essere configurato per l'uso di un proxy SOCKS5.

    > [!WARNING]  
    > Il supporto per il proxy SOCKS integrato nelle impostazioni Internet di Windows non supporta SOCKS5 e non può essere usato per la procedura descritta in questo documento. I browser seguenti si basano sulle impostazioni proxy di Windows e non possono essere usati per la procedura illustrata in questo documento:
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > Anche Google Chrome si basa sulle impostazioni proxy di Windows. In questo caso, tuttavia, è possibile installare estensioni che supportano SOCKS5, tra cui [FoxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp) (opzione consigliata).

## <a name="usessh"></a>Creare un tunnel con il comando SSH

Utilizzare il comando seguente per creare un SSH tunnel utilizzando il comando `ssh` . Sostituire `sshuser` con un utente SSH per il cluster HDInsight e `clustername` con il nome del cluster HDInsight:

```cmd
ssh -C2qTnNf -D 9876 sshuser@clustername-ssh.azurehdinsight.net
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

[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty) è un client SSH grafico per Windows. Se non si ha familiarità con PuTTY, vedere la [documentazione di PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html). Usare la procedura seguente per creare un tunnel SSH con PuTTY:

### <a name="create-or-load-a-session"></a>Creare o caricare una sessione

1. Aprire PuTTY e assicurarsi che la **Sessione** sia selezionata nel menu a sinistra. Se è già stata salvata una sessione, selezionare il nome della sessione dal **sessioni salvate** elencare e selezionare **carico**.

1. Se non si ha già una sessione salvata, immettere le informazioni di connessione:
    * **Nome host (o indirizzo IP)** - L'indirizzo SSH per il cluster HDInsight. Ad esempio, **mycluster-ssh.azurehdinsight.net**
    * **Porta**: 22
    * **Tipo di connessione**: SSH

1. Selezionare **Salva**

    ![creare una sessione SSH](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-create-putty-session.png)

1. Nella sezione **Category** sul lato sinistro della finestra di dialogo espandere **Connection**, **SSH** e infine selezionare **Tunnels**.

1. Fornire le seguenti informazioni nel modulo **Options controlling SSH port forwarding** :
   
   * **Source port** : la porta del client che si desidera inoltrare. Ad esempio, **9876**.

   * **Destination** : l'indirizzo SSH del cluster HDInsight. Ad esempio, **mycluster-ssh.azurehdinsight.net**.

   * **Dynamic** : consente il routing tramite proxy SOCKS dinamico.
     
     ![image of tunneling options](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)

1. Selezionare **Add** per aggiungere le impostazioni e quindi fare clic su **aprire** per aprire una connessione SSH.

1. Quando richiesto, accedere al server.

## <a name="use-the-tunnel-from-your-browser"></a>Usare il tunnel dal browser

> [!IMPORTANT]  
> Nella procedura illustrata in questa sezione viene usato il browser Mozilla FireFox, poiché prevede le stesse impostazioni proxy per tutte le piattaforme. È possibile che altri browser moderni, tra cui Google Chrome, richiedano un'estensione come FoxyProxy per poter interagire con il tunnel.

1. Configurare il browser in modo che usi **localhost** e la porta usata al momento della creazione del tunnel come proxy **SOCKS v5**. Ecco visualizzate le impostazioni di Firefox. Se si usa una porta diversa da quella 9876, cambiare la porta con quella usata:
   
    ![image of Firefox settings](./media/hdinsight-linux-ambari-ssh-tunnel/firefoxproxy.png)
   
   > [!NOTE]  
   > Se si seleziona **Remote DNS**, le richieste DNS (Domain Name System) vengono risolte usando il cluster HDInsight. Questa impostazione risolve DNS usando il nodo head del cluster.

2. Per verificare il funzionamento del tunnel, visitare un sito, ad esempio [https://www.whatismyip.com/](https://www.whatismyip.com/). L'indirizzo IP restituito deve essere uno usato dal data center di Microsoft Azure.

## <a name="verify-with-ambari-web-ui"></a>Verificare con l’interfaccia utente web di Ambari

Una volta stabilito il cluster, utilizzare la procedura seguente per verificare che sia possibile accedere all’interfaccia utente del servizio dal web Ambari:

1. Nel browser passare a `http://headnodehost:8080`. L'indirizzo `headnodehost` viene inviato al cluster tramite il tunnel e risolto nel nodo head in cui è in esecuzione Ambari. Quando richiesto, immettere il nome dell'account amministratore (admin) e la password per il cluster. Può essere richiesto una seconda volta dall'interfaccia utente web di Ambari. In tal caso, è necessario immettere nuovamente le informazioni.

   > [!NOTE]  
   > Quando si usa l'indirizzo `http://headnodehost:8080` per connettersi al cluster, la connessione viene stabilita tramite il tunnel. La comunicazione viene protetta usando il tunnel SSH invece di HTTPS. Per connettersi a internet tramite HTTPS, usare `https://clustername.azurehdinsight.net`, dove `clustername` è il nome del cluster.

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
    > Si noti l'URL della pagina. dovrebbe essere simile a `http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster`. Questo URI usa il nome di dominio interno completo (FQDN) del nodo ed è accessibile solo quando si usa un tunnel SSH.

## <a name="next-steps"></a>Passaggi successivi

Dopo avere appreso come creare e usare un tunnel SSH, vedere il documento seguente per conoscere gli altri modi di usare Ambari:

* [Gestire i cluster HDInsight mediante Apache Ambari](hdinsight-hadoop-manage-ambari.md)