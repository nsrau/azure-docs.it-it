---
title: Usare chiavi SSH con Hadoop basato su Linux da Linux, Unix o OS X | Documentazione Microsoft
description: " È possibile accedere a HDInsight basato su Linux tramite Secure Shell (SSH). Questo documento fornisce informazioni sull'uso di SSH con HDInsight da client OS X, Unix o Linux."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: a6a16405-a4a7-4151-9bbf-ab26972216c5
ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/13/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 476d9ce8b64f3442031310bd9170c682a9940b2b


---
# <a name="use-ssh-with-linuxbased-hadoop-on-hdinsight-from-linux-unix-or-os-x"></a>Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X
> [!div class="op_single_selector"]
> * [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
> * [Linux, Unix, OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
> 
> 

[Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) consente di eseguire in modalità remota operazioni nei cluster di HDInsight basati su Linux usando un'interfaccia della riga di comando. Questo documento fornisce informazioni sull'uso di SSH con HDInsight da client OS X, Unix o Linux.

> [!NOTE]
> I passaggi descritti in questo articolo presuppongono l'uso di un client Linux, Unix o OS X. Questi passaggi possono essere eseguiti in un client basato su Windows se è stato installato un pacchetto che include `ssh` e `ssh-keygen`, ad esempio [Bash in Ubuntu in Windows](https://msdn.microsoft.com/commandline/wsl/about).
> 
> Se SSH non è installato nel client basato su Windows, usare la procedura illustrata in [Usare SSH con HDInsight basato su Linux (Hadoop) da Windows](hdinsight-hadoop-linux-use-ssh-windows.md) per informazioni sull'installazione e sull'uso di PuTTY.
> 
> 

## <a name="prerequisites"></a>Prerequisiti
* **ssh-keygen** e **ssh** per i client Linux, Unix e OS X. Queste utilità vengono in genere fornita con il sistema operativo o sono disponibili tramite il sistema di gestione pacchetti.
* Un moderno Web browser che supporta HTML5.

OPPURE

* L'[interfaccia della riga di comando di Azure](../xplat-cli-install.md).
  
    [!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

## <a name="what-is-ssh"></a>Che cos'è SSH?
SSH è un'utilità per accedere ed eseguire in modalità remota i comandi in un server remoto. Con HDInsight basato su Linux, SSH stabilisce una connessione crittografata al nodo head del cluster e mette a disposizione una riga di comando che consente di digitare i comandi. I comandi vengono quindi eseguiti direttamente sul server.

### <a name="ssh-user-name"></a>SSH user name
Un nome utente SSH è il nome utilizzato per autenticarsi con il cluster HDInsight. Quando si specifica un nome utente SSH durante la creazione del cluster, l'utente viene creato in tutti i nodi del cluster. Una volta creato il cluster, è possibile usare questo nome utente per la connessione a nodi head del cluster HDInsight. Dai nodi head è quindi possibile connettersi ai singoli nodi del ruolo di lavoro.

### <a name="ssh-password-or-public-key"></a>Password SSH o chiave pubblica
Un utente SSH può utilizzare una password o chiave pubblica per l'autenticazione. Una password è semplicemente una stringa di testo composta dall’utente, mentre una chiave pubblica è parte di una coppia di chiavi crittografiche generata per identificare l’utente in modo univoco.

Una chiave è più sicura di una password, ma si richiedono passaggi aggiuntivi per generare la chiave ed è necessario gestire i file che contengono la chiave in un luogo sicuro. Chiunque riesca ad accedere ai file di chiave, ottiene accesso all’account dell’utente. O se si perdono i file di chiave, non si sarà in grado di accedere al proprio account.

Una coppia di chiavi è costituita da una chiave pubblica (che viene inviata al server di HDInsight) e una chiave privata (che verrà mantenuta nel computer client.) Quando ci si connette al server di HDInsight tramite SSH, il client SSH utilizzerà la chiave privata nel computer in uso per l'autenticazione con il server.

## <a name="create-an-ssh-key"></a>Creare una chiave SSH
Se si prevede di usare chiavi SSH con il cluster, attenersi alle seguenti informazioni. Se si prevede di utilizzare una password, è possibile ignorare questa sezione.

1. Aprire una sessione terminal e usare il comando seguente per verificare se sono disponibili chiavi SSH esistenti:
   
        ls -al ~/.ssh
   
    Cercare i file seguenti nell'elenco di directory. Si tratta di nomi comuni per le chiavi pubbliche SSH:
   
   * id\_dsa.pub
   * id\_ecdsa.pub
   * id\_ed25519.pub
   * id\_rsa.pub
2. Se non si vuole usare un file esistente o non si hanno chiavi SSH, eseguire il comando seguente per generare un nuovo file:
   
        ssh-keygen -t rsa
   
    Verrà richiesto di specificare le seguenti informazioni:
   
   * Il percorso del file: il percorso predefinito è ~/.ssh/id\_rsa.
   * Una passphrase: verrà richiesto di immetterla una seconda volta.
     
     > [!NOTE]
     > È consigliabile usare una passphrase sicura per la chiave. Tuttavia, se si dimentica la passphrase, non è possibile recuperarla.
     > 
     > 
     
     Al termine del comando, si disporrà di due nuovi file: la chiave privata (ad esempio **id\_rsa**) e la chiave pubblica (ad esempio **id\_rsa.pub**).

## <a name="create-a-linuxbased-hdinsight-cluster"></a>Creare un cluster HDInsight basato su Linux
Quando si crea un cluster HDInsight basato su Linux, è necessario fornire la chiave pubblica creata in precedenza. Da client Linux, Unix o OS X è possibile creare un cluster HDInsight in due modi:

* **Portale di Azure** - usa un portale basato sul Web per creare il cluster.
* **Interfaccia della riga di comando di Azure** : usa i comandi della riga di comando per creare il cluster.

Ognuno di questi metodi richiederà un password o una chiave pubblica. Per informazioni dettagliate sulla creazione di un cluster HDInsight basato su Linux, vedere l'articolo [Provisioning di cluster Hadoop Linux in HDInsight con opzioni personalizzate](hdinsight-hadoop-provision-linux-clusters.md).

### <a name="azure-portal"></a>Portale di Azure
Quando si usa il [portale di Azure][preview-portal] per cerare un cluster HDInsight basato su Linux, è necessario compilare il campo **NOME UTENTE SSH** e selezionare **PASSWORD** o **CHIAVE PUBBLICA SSH**.

Se si seleziona **CHIAVE PUBBLICA SSH**, incollare la chiave pubblica (contenuta nel file con l'estensione **.pub**) nel campo **SSH PublicKey** oppure selezionare **Seleziona un file** per cercare e selezionare il file di chiave pubblica.

![Image of form asking for public key](./media/hdinsight-hadoop-linux-use-ssh-unix/ssh-key.png)

> [!NOTE]
> Il file della chiave è semplicemente un file di testo. Il contenuto dovrebbe essere simile al seguente:
> 
> ```
> ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCelfkjrpYHYiks4TM+r1LVsTYQ4jAXXGeOAF9Vv/KGz90pgMk3VRJk4PEUSELfXKxP3NtsVwLVPN1l09utI/tKHQ6WL3qy89WVVVLiwzL7tfJ2B08Gmcw8mC/YoieT/YG+4I4oAgPEmim+6/F9S0lU2I2CuFBX9JzauX8n1Y9kWzTARST+ERx2hysyA5ObLv97Xe4C2CQvGE01LGAXkw2ffP9vI+emUM+VeYrf0q3w/b1o/COKbFVZ2IpEcJ8G2SLlNsHWXofWhOKQRi64TMxT7LLoohD61q2aWNKdaE4oQdiuo8TGnt4zWLEPjzjIYIEIZGk00HiQD+KCB5pxoVtp user@system
> ```
> 
> 

Verrà creato un account di accesso per l'utente specificato usando la password o la chiave pubblica fornita.

### <a name="azure-commandline-interface-for-mac-linux-and-windows"></a>Interfaccia della riga di comando per Azure per Mac, Linux e Windows
È possibile usare l'[interfaccia della riga di comando di Azure per Mac, Linux e Windows](../xplat-cli-install.md) per creare un nuovo cluster tramite il comando `azure hdinsight cluster create`.

Per altre informazioni sull'uso di questo comando, vedere l'articolo [Provisioning di cluster Hadoop Linux in HDInsight con opzioni personalizzate](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="connect-to-a-linuxbased-hdinsight-cluster"></a>Connettersi a un cluster HDInsight basato su Linux
Da una sessione terminal usare il comando SSH per la connessione al nodo head del cluster specificando l'indirizzo e il nome utente:

* **Indirizzo SSH** : per connettersi a un cluster con SSH è possibile usare due indirizzi:
  
  * **Connettersi al nodo head**: il nome del cluster, seguito da **-ssh.azurehdinsight.net**. Ad esempio, **mycluster-ssh.azurehdinsight.net**.
  * **Connettersi al nodo perimetrale**: se il cluster è R Server in HDInsight, contiene anche un nodo perimetrale a cui è possibile accedere usando **RServer.CLUSTERNAME.ssh.azurehdinsight.net**, dove **CLUSTERNAME** è il nome del cluster.
* **Nome utente** : il nome utente SSH fornito durante la creazione del cluster.

L'esempio seguente consentirà di connettersi al nodo head primario di **mycluster** come utente **me**:

    ssh me@mycluster-ssh.azurehdinsight.net

Se è stata usata una password per l'account utente, verrà richiesto di immetterla.

Se è stata usata una chiave SSH è protetta con una passphrase, verrà richiesto di immettere la passphrase. In caso contrario, SSH tenterà di eseguire automaticamente l'autenticazione usando una delle chiavi private locali nel client.

> [!NOTE]
> Se SSH non esegue automaticamente l'autenticazione con la chiave privata corretta, usare il parametro **-i** e specificare il percorso della chiave privata. Nell'esempio seguente la chiave privata verrà caricata da `~/.ssh/id_rsa`:
> 
> `ssh -i ~/.ssh/id_rsa me@mycluster-ssh.azurehdinsight.net`
> 
> 

Se si prova a connettersi usando l'indirizzo del nodo head ma non viene specificata alcuna porta, SSH imposta automaticamente la porta 22, che si connette al nodo head primario nel cluster HDInsight. Se si usa la porta 23, la connessione verrà eseguita al nodo head secondario. Per altre informazioni sui nodi head, vedere [Disponibilità e affidabilità dei cluster Hadoop in HDInsight](hdinsight-high-availability-linux.md).

### <a name="connect-to-worker-nodes"></a>Connettersi ai nodi di lavoro
I nodi del ruolo di lavoro non sono direttamente accessibili dall'esterno del data center di Azure, ma è possibile accedervi dal nodo head del cluster tramite SSH.

Se si usa una chiave SSH per autenticare l'account utente, è necessario completare i passaggi seguenti nel client:

1. Usando un editor di testo, aprire `~/.ssh/config`. Se questo file non esiste, è possibile crearlo immettendo `touch ~/.ssh/config` nel terminale.
2. Aggiungere il codice seguente al file. Sostituire *CLUSTERNAME* con il nome del cluster HDInsight.
   
        Host CLUSTERNAME-ssh.azurehdinsight.net
          ForwardAgent yes
   
    Questo consente di configurare l'inoltro dell'agente SSH per il cluster HDInsight.
3. Testare l'inoltro dell'agente SSH tramite il comando seguente dal terminale:
   
        echo "$SSH_AUTH_SOCK"
   
    Dovrebbero essere restituiti informazioni simili alle seguenti:
   
        /tmp/ssh-rfSUL1ldCldQ/agent.1792
   
    Se non viene restituita alcuna informazione, significa **ssh-agent** non è in esecuzione. Consultare la documentazione del sistema operativo per i passaggi specifici sull'installazione e sulla configurazione di **ssh agent**oppure vedere l'articolo relativo all' [uso di ssh-agent con ssh](http://mah.everybody.org/docs/ssh).
4. Dopo avere verificato che **ssh-agent** è in esecuzione, usare il comando seguente per aggiungere la chiave privata SSH all'agente:
   
        ssh-add ~/.ssh/id_rsa
   
    Se la chiave privata viene archiviata in un altro file, sostituire `~/.ssh/id_rsa` con il percorso del file.

Usare la procedura seguente per connettersi ai nodi di lavoro per il cluster.

> [!IMPORTANT]
> Se si usa una chiave SSH per autenticare l'account, è necessario completare i passaggi precedenti per verificare che l'inoltro dell'agente sia funzionando:
> 
> 

1. Connettersi al cluster HDInsight tramite SSH, come descritto in precedenza.
2. Una volta effettuata la connessione, usare il comando seguente per recuperare un elenco dei nodi del cluster. Sostituire *ADMINPASSWORD* con la password per l'account amministratore del cluster. Sostituire *CLUSTERNAME* con il nome del cluster.
   
        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts
   
    In questo modo le informazioni verranno sostituite nel formato JSON per i nodi del cluster, tra cui `host_name`, che contiene il nome di dominio completo (FQDN) per ogni nodo. Di seguito è riportato un esempio di una voce `host_name` restituita dal comando **curl** :
   
        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"
3. Dopo aver creato un elenco dei nodi di lavoro ai quali connettersi, usare il comando seguente dalla sessione SSH al server per aprire una connessione a un nodo di lavoro:
   
        ssh USERNAME@FQDN
   
    Sostituire *USERNAME* con il nome utente SSH e *FQDN* con il nome di dominio completo per il nodo di lavoro. Ad esempio: `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`.
   
   > [!NOTE]
   > Se è stata usata una password per l'autenticazione della sessione SSH, verrà richiesto di immetterla di nuovo. Se si usa una chiave SSH, la connessione dovrebbe terminare senza alcuna richiesta.
   > 
   > 
4. Una volta stabilita la sessione, la richiesta del terminale cambierà da `username@hn#-clustername` a `username@wk#-clustername` per indicare che si è connessi al nodo di lavoro. Tutti i comandi eseguiti a questo punto verranno eseguiti sul nodo del lavoro.
5. Al termine dell'esecuzione di azioni su un nodo di lavoro, usare il comando `exit` per chiudere la sessione per il nodo di lavoro. In questo modo si tornerà alla richiesta `username@hn#-clustername` .

## <a name="connect-to-a-domainjoined-hdinsight-cluster"></a>Connettersi a un cluster HDInsight aggiunto al dominio
[HDInsight aggiunto al dominio](hdinsight-domain-joined-introduction.md) integra Kerberos con Hadoop in HDInsight. Dato che l'utente SSH non è un utente del dominio di Active Directory, questo account utente non può eseguire i comandi di Hadoop dalla shell SSH direttamente a un cluster aggiunto al dominio. È necessario eseguire prima *kinit*. 

**Per eseguire query di Hive in un cluster HDInsight aggiunto al dominio usando SSH**

1. Connettersi a un cluster HDInsight aggiunto al dominio usando SSH.  Per istruzioni, vedere [Connettersi a un cluster HDInsight basato su Linux](#connect-to-a-linux-based-hdinsight-cluster).
2. Eseguire kinit. Verranno chiesti nome e password dell'utente del dominio. Per maggiori informazioni sulla configurazione degli utenti del dominio per i cluster HDInsight aggiunti al dominio, vedere [Configure Domain-joined HDInsight clusters](hdinsight-domain-joined-configure.md) (Configurare i cluster HDInsight aggiunti al dominio).
   
    ![Kinit aggiunto al dominio di Hadoop di HDInsight](./media/hdinsight-hadoop-linux-use-ssh-unix/hdinsight-domain-joined-hadoop-kinit.png)
3. Aprire la console Hive immettendo:
   
        hive
   
    È quindi possibile eseguire i comandi di Hive.

## <a name="add-more-accounts"></a>Aggiungere altri account
1. Generare una nuova chiave pubblica e una nuova chiave privata per il nuovo account utente come descritto nella sezione [Creare una chiave SSH](#create-an-ssh-key-optional) .
   
   > [!NOTE]
   > La chiave privata deve essere generata in un client che l'utente userà per connettersi al cluster o essere trasferita in modo sicuro in tale client dopo la creazione.
   > 
   > 
2. Da una sessione SSH al cluster aggiungere il nuovo utente con il comando seguente:
   
        sudo adduser --disabled-password <username>
   
    Verrà creato un nuovo account utente, ma verrà disabilitata l'autenticazione tramite password.
3. Creare la directory e i file per contenere la chiave usando i comandi seguenti:
   
        sudo mkdir -p /home/<username>/.ssh
        sudo touch /home/<username>/.ssh/authorized_keys
        sudo nano /home/<username>/.ssh/authorized_keys
4. Quando si apre l'editor nano, copiare e incollare il contenuto della chiave pubblica per il nuovo account utente. Usare infine **Ctrl-X** per salvare il file e uscire dall'editor.
   
    ![image of nano editor with example key](./media/hdinsight-hadoop-linux-use-ssh-unix/nano.png)
5. Usare il comando seguente per specificare il nuovo account utente come proprietario della cartella .ssh e del contenuto.
   
        sudo chown -hR <username>:<username> /home/<username>/.ssh
6. Sarà ora possibile eseguire l'autenticazione nel server con il nuovo account utente e la chiave privata.

## <a name="a-idtunnelassh-tunneling"></a><a id="tunnel"></a>Tunneling SSH
SSH può essere usato anche per effettuare il tunneling di richieste locali, ad esempio richieste Web, al cluster HDInsight. La richiesta verrà quindi instradata alla risorsa richiesta come se provenisse dal nodo head del cluster HDInsight.

> [!IMPORTANT]
> Un tunnel SSH è un requisito per l'accesso al web dell'interfaccia utente per alcuni servizi Hadoop. Ad esempio, la UI di cronologia processi o di gestione delle risorse dell'interfaccia utente possono essere accessibili solo tramite un tunnel SSH.
> 
> 

Per altre informazioni sulla creazione e sull'uso di un tunnel SSH, vedere [Usare il tunneling SSH per accedere all'interfaccia Web di Ambari, ResourceManager, JobHistory, NameNode, Oozie e altre interfacce Web](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="next-steps"></a>Passaggi successivi
Ora che si è appreso come eseguire l'autenticazione usando una chiave SSH, è possibile imparare a usare MapReduce con Hadoop in HDInsight.

* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con HDInsight](hdinsight-use-pig.md)
* [Usare processi MapReduce con HDInsight](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/



<!--HONumber=Nov16_HO2-->


