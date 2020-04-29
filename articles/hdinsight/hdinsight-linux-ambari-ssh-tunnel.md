---
title: Usare il tunneling SSH per accedere ad Azure HDInsight
description: Informazioni su come utilizzare un tunnel SSH per esplorare in modo sicuro le risorse web ospitate sui nodi HDInsight basati su Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: 9bdf7360ce00637b0eed3de7a3349da8656a3ed0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81314167"
---
# <a name="use-ssh-tunneling-to-access-apache-ambari-web-ui-jobhistory-namenode-apache-oozie-and-other-uis"></a>Usare il tunneling SSH per accedere all'interfaccia utente Web di Apache Ambari, JobHistory, NameNode, Apache oozie e altre interfacce utente

I cluster HDInsight forniscono l'accesso all'interfaccia utente Web di Apache Ambari tramite Internet. Alcune funzionalità richiedono un tunnel SSH. Ad esempio, non è possibile accedere all'interfaccia utente Web di Apache oozie tramite Internet senza un tunnel SSH.

## <a name="why-use-an-ssh-tunnel"></a>Perché usare un tunnel SSH

Diversi menu di Ambari funzionano solo con un tunnel SSH. Questi menu si basano su servizi e siti Web in esecuzione in altri tipi di nodi, ad esempio i nodi di lavoro.

Le interfacce utente Web seguenti richiedono un tunnel SSH:

* JobHistory
* NameNode
* Thread Stacks
* Interfaccia utente Web di Oozie
* HBase Master e l'interfaccia utente di Log

I servizi installati con azioni script che espongono un servizio Web richiederanno un tunnel SSH. Hue installato con azione script richiede un tunnel SSH per accedere all'interfaccia utente Web.

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

## <a name="create-a-tunnel-using-the-ssh-command"></a><a name="usessh"></a>Creare un tunnel con il comando SSH

Utilizzare il comando seguente per creare un SSH tunnel utilizzando il comando `ssh` . Sostituire `sshuser` con un utente SSH per il cluster HDInsight e sostituire `CLUSTERNAME` con il nome del cluster HDInsight:

```cmd
ssh -C2qTnNf -D 9876 sshuser@CLUSTERNAME-ssh.azurehdinsight.net
```

Questo comando crea una connessione che instrada il traffico alla porta locale 9876 al cluster su SSH. Le opzioni disponibili sono:

    |Opzione |Descrizione |
    |---|---|
    |D 9876|Porta locale che instrada il traffico attraverso il tunnel.|
    |C|Comprime tutti i dati, perché il traffico Web è per lo più testo.|
    |2|Forzare SSH solo a provare il protocollo versione 2.|
    |q|Modalità non interattiva.|
    |T|Disabilitare l'allocazione pseudo-TTY, perché si sta semplicemente inviando una porta.|
    |n|Evitare la lettura di STDIN poiché si sta semplicemente inviando una porta.|
    |N|Non eseguire un comando remoto poiché si sta semplicemente inviando una porta.|
    |f|Eseguire in background.|

Al termine del comando, il traffico inviato alla porta 9876 nel computer locale viene instradato al nodo head del cluster.

## <a name="create-a-tunnel-using-putty"></a><a name="useputty"></a>Creare un tunnel utilizzando PuTTY

[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty) è un client SSH grafico per Windows. Se non si ha familiarità con PuTTy, vedere la [documentazione di Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html). Usare la procedura seguente per creare un tunnel SSH con PuTTY:

### <a name="create-or-load-a-session"></a>Creare o caricare una sessione

1. Aprire PuTTY e assicurarsi che la **Sessione** sia selezionata nel menu a sinistra. Se è già stata salvata una sessione, selezionare il nome della sessione nell'elenco **sessioni salvate** e selezionare **carica**.

1. Se non si ha già una sessione salvata, immettere le informazioni di connessione:

    |Proprietà |valore |
    |---|---|
    |Nome host (o indirizzo IP)|Indirizzo SSH per il cluster HDInsight. Ad esempio, **mycluster-ssh.azurehdinsight.net**.|
    |Porta|22|
    |Tipo di connessione|SSH|

1. Selezionare **Salva**

    ![HDInsight creare una sessione Putty](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-create-putty-session.png)

1. Nella sezione **Category** sul lato sinistro della finestra di dialogo espandere **Connection**, **SSH** e infine selezionare **Tunnels**.

1. Fornire le seguenti informazioni nel modulo **Options controlling SSH port forwarding** :

    |Proprietà |valore |
    |---|---|
    |Porta di origine|La porta sul client che si desidera inviare. Ad esempio, **9876**.|
    |Destination|Indirizzo SSH per il cluster HDInsight. Ad esempio, **mycluster-ssh.azurehdinsight.net**.|
    |Dinamico|Abilita il routing proxy per i CALZINi dinamici.|

    ![Opzioni di tunneling della configurazione PuTTy](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-putty-tunnel.png)

1. Selezionare **Aggiungi** per aggiungere le impostazioni e quindi fare clic su **Apri** per aprire una connessione SSH.

1. Quando richiesto, accedere al server.

## <a name="use-the-tunnel-from-your-browser"></a>Usare il tunnel dal browser

> [!IMPORTANT]  
> Nella procedura illustrata in questa sezione viene usato il browser Mozilla FireFox, poiché prevede le stesse impostazioni proxy per tutte le piattaforme. È possibile che altri browser moderni, tra cui Google Chrome, richiedano un'estensione come FoxyProxy per poter interagire con il tunnel.

1. Configurare il browser in modo che usi **localhost** e la porta usata al momento della creazione del tunnel come proxy **SOCKS v5**. Ecco visualizzate le impostazioni di Firefox. Se si usa una porta diversa da quella 9876, cambiare la porta con quella usata:

    ![impostazioni del proxy del browser Firefox](./media/hdinsight-linux-ambari-ssh-tunnel/firefox-proxy-settings.png)

   > [!NOTE]  
   > Se si seleziona **Remote DNS**, le richieste DNS (Domain Name System) vengono risolte usando il cluster HDInsight. Questa impostazione risolve DNS usando il nodo head del cluster.

2. Verificare il funzionamento del tunnel visitando un sito, ad [https://www.whatismyip.com/](https://www.whatismyip.com/)esempio. L'indirizzo IP restituito deve essere uno usato dal data center di Microsoft Azure.

## <a name="verify-with-ambari-web-ui"></a>Verificare con l’interfaccia utente web di Ambari

Una volta stabilito il cluster, utilizzare la procedura seguente per verificare che sia possibile accedere all’interfaccia utente del servizio dal web Ambari:

1. Nel browser passare a `http://headnodehost:8080`. L'indirizzo `headnodehost` viene inviato al cluster tramite il tunnel e risolto nel nodo head in cui è in esecuzione Ambari. Quando richiesto, immettere il nome dell'account amministratore (admin) e la password per il cluster. Può essere richiesto una seconda volta dall'interfaccia utente web di Ambari. In tal caso, è necessario immettere nuovamente le informazioni.

   > [!NOTE]  
   > Quando si usa l'indirizzo `http://headnodehost:8080` per connettersi al cluster, la connessione viene stabilita tramite il tunnel. La comunicazione viene protetta usando il tunnel SSH invece di HTTPS. Per connettersi tramite Internet tramite HTTPS, usare `https://clustername.azurehdinsight.net`, dove `clustername` è il nome del cluster.

2. Dall'interfaccia utente Web di Ambari, selezionare HDFS dall'elenco a sinistra della pagina.

    ![Servizio HDFS di Apache Ambari selezionato](./media/hdinsight-linux-ambari-ssh-tunnel/hdfs-service-selected.png)

3. Quando le informazioni del servizio HDFS vengono visualizzate, selezionare **Collegamenti rapidi**. Verrà visualizzato un elenco di nodi head del cluster. Selezionare uno dei nodi head e quindi selezionare l' **interfaccia utente di NameNode**.

    ![Immagine con il menu di collegamenti rapidi espanso](./media/hdinsight-linux-ambari-ssh-tunnel/namenode-drop-down-menu.png)

    > [!NOTE]  
    > Quando si seleziona __Quick Links__ (Collegamenti rapidi), è possibile che venga visualizzato un indicatore di attesa. Questa condizione può verificarsi se la connessione Internet è lenta. Attendere un minuto o due perché i dati vengano ricevuti dal server, poi riprovare con l'elenco.
    >
    > Alcune voci del menu **Quick Links** (Collegamenti rapidi) potrebbero risultare troncate sul lato destro della schermata. In tal caso, espandere il menu con il mouse e usare il tasto freccia destra per scorrere la schermata verso destra e visualizzare il resto del menu.

4. Viene visualizzata una pagina simile all'immagine seguente:

    ![Immagine dell'interfaccia utente di Hadoop NameNode](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-namenode-ui.png)

    > [!NOTE]  
    > Si noti l'URL della pagina, che dovrebbe essere simile a `http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster`. Questo URI usa il nome di dominio interno completo (FQDN) del nodo ed è accessibile solo quando si usa un tunnel SSH.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come creare e usare un tunnel SSH, vedere il documento seguente per altri modi di usare Ambari:

* [Gestire i cluster HDInsight mediante Apache Ambari](hdinsight-hadoop-manage-ambari.md)
