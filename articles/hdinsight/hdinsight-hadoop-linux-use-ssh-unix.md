---
title: Usare SSH con HDInsight (Hadoop) da Windows, Linux, Unix o OS X | Documentazione Microsoft
description: " È possibile accedere a HDInsight tramite Secure Shell (SSH). Questo documento illustra l&quot;uso di SSH con HDInsight da client Windows, Linux, Unix o OS X."
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
ms.date: 02/27/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cfaade8249a643b77f3d7fdf466eb5ba38143f18
ms.openlocfilehash: 4cde035f75bfa3c448f12e9ebf2896b9a54a6873
ms.lasthandoff: 03/01/2017

---
# <a name="use-ssh-with-hdinsight-hadoop-from-bash-on-windows-10-linux-unix-or-os-x"></a>Usare SSH con HDInsight (Hadoop) da Bash in Windows 10, Linux, Unix o OS X

> [!div class="op_single_selector"]
> * [PuTTY (Windows)](hdinsight-hadoop-linux-use-ssh-windows.md)
> * [SSH (Windows, Linux, Unix, OS X)](hdinsight-hadoop-linux-use-ssh-unix.md)

[Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) consente di accedere ai cluster HDInsight basati su Linux ed eseguire comandi usando un'interfaccia della riga di comando. Questo documento fornisce informazioni di base su SSH e informazioni specifiche sull'uso di SSH con HDInsight.

## <a name="what-is-ssh"></a>Che cos'è SSH?

SSH è un protocollo di rete crittografico che permette di comunicare in modo sicuro con un server remoto tramite una rete non protetta. Il protocollo SSH viene usato per fornire un accesso sicuro da riga di comando a un server remoto. In questo caso si tratta dei nodi head o del nodo perimetrale di un cluster HDInsight.

SSH può essere usato anche per effettuare il tunneling del traffico di rete dal client al cluster HDInsight. L'uso di un tunnel permette di accedere ai servizi nel cluster HDInsight che non sono esposti direttamente a Internet. Per altre informazioni sull'uso del tunneling SSH con HDInsight, vedere l'articolo relativo all'[uso del tunneling SSH con HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ssh-clients"></a>Client SSH

Molti sistemi operativi offrono la funzionalità client SSH tramite le utilità della riga di comando `ssh` e `scp`.

* __ssh__: client SSH generale che può essere usato per stabilire una sessione remota della riga di comando e creare tunnel.
* __scp__: utilità che consente di copiare file tra sistemi locali e remoti usando il protocollo SSH.

L'Aggiornamento dell'anniversario di Windows 10 offre Bash come funzionalità per gli sviluppatori. Fornisce `ssh`, `scp` e altri comandi Linux. Per altre informazioni sull'uso di Bash per Windows 10, vedere [Bash on Ubuntu on Windows](https://msdn.microsoft.com/commandline/wsl/about) (Bash in Ubuntu in Windows).

Se si usa Windows e non si ha accesso a Bash per Windows 10, è possibile usare i client SSH riportati di seguito:

* [Git per Windows](https://git-for-windows.github.io/): offre le utilità della riga di comando `ssh` e `scp`.
* [Cygwin](https://cygwin.com/): offre le utilità della riga di comando `ssh` e `scp`.

> [!NOTE]
> Le procedure descritte in questo documento presuppongono che si abbia accesso al comando `ssh`. Se si usa un client come PuTTY o MobaXterm, vedere la documentazione del client per informazioni sui parametri e i comandi equivalenti.

## <a name="ssh-authentication"></a>Autenticazione SSH

Una connessione SSH può essere autenticata tramite password o [crittografia a chiave pubblica](https://en.wikipedia.org/wiki/Public-key_cryptography). L'uso della chiave rappresenta l'opzione più sicura, perché non è vulnerabile a molti degli attacchi che possono colpire le password. Tuttavia, la creazione e la gestione di chiavi risultano più complicate rispetto all'uso di una password.

L'uso della crittografia a chiave pubblica prevede la creazione di una coppia di chiavi _pubblica_ e _privata_.

* La **chiave pubblica** viene caricata nei nodi del cluster HDInsight o in qualsiasi altro servizio da usare con la crittografia a chiave pubblica.

* La **chiave privata** è ciò che viene presentato al cluster HDInsight per la verifica dell'identità quando si accede con un client SSH. Sulla chiave privata è necessario mantenere la massima riservatezza, evitando di condividerla.

    Per migliorare ulteriormente la sicurezza è possibile creare una passphrase per la chiave privata. Se si usa una passphrase, è necessario immetterla durante l'autenticazione con SSH.

### <a name="create-a-public-and-private-key"></a>Creare una chiave pubblica e una privata

Il modo più semplice per creare una coppia di chiavi pubblica e privata per l'uso con HDInsight è l'utilità `ssh-keygen`. Usare il comando seguente dalla riga di comando per creare una nuova coppia di chiavi per l'uso con HDInsight:

> [!NOTE]
> Se si usa un client SSH GUI come MobaXterm o PuTTY, vedere la documentazione del client per informazioni su come generare chiavi.

    ssh-keygen -t rsa -b 2048

Verrà richiesto di specificare le informazioni seguenti:

* Percorso del file: il percorso predefinito è `~/.ssh/id_rsa`.

* Passphrase facoltativa: se si immette una passphrase, è necessario immetterla nuovamente durante l'autenticazione nel cluster HDInsight.

> [!IMPORTANT]
> La passphrase è una password per la chiave privata. Ogni volta che si usa la chiave privata per l'autenticazione, è necessario specificare la passphrase per poter usare la chiave. Anche se qualcun altro dovesse entrare in possesso della chiave privata, non potrebbe usarla senza la passphrase.
>
> Tuttavia, se si dimentica la passphrase non è possibile ripristinarla o recuperarla.

Al termine dell'esecuzione del comando, sono disponibili due nuovi file:

* __id\_rsa__: questo file contiene la chiave privata.

    > [!WARNING]
    > Limitare l'accesso a questo file per impedire l'accesso non autorizzato ai servizi protetti dalla chiave pubblica.

* __id\_rsa.pub__: questo file contiene la chiave pubblica. Usare questo file durante la creazione di un cluster HDInsght.

    > [!NOTE]
    > Non è necessario limitare l'accesso alla chiave _pubblica_, che viene usata soltanto per la verifica della chiave privata. Servizi come il server SSH fanno uso della chiave pubblica per verificare l'identità dell'utente quando si esegue l'autenticazione con la chiave privata.

## <a name="configure-ssh-on-hdinsight"></a>Configurare SSH in HDInsight

Quando si crea un cluster HDInsight basato su Linux, è necessario fornire un _nome utente SSH_ e una _password_ o una _chiave pubblica_. Durante la creazione del cluster, queste informazioni vengono usate per creare un account di accesso nei nodi del cluster HDInsight. La password o la chiave pubblica serve a proteggere l'account utente.

Per altre informazioni sulla configurazione di SSH durante la creazione di un cluster, vedere uno dei documenti seguenti:

* [Creare cluster HDInsight tramite il portale di Azure](hdinsight-hadoop-create-linux-clusters-portal.md)
* [Creare cluster HDInsight tramite l'interfaccia della riga di comando](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
* [Creare cluster HDInsight tramite Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Creare cluster HDInsight tramite modelli di Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md)
* [Creare cluster HDInsight tramite .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* [Creare cluster HDInsight tramite l'API REST](hdinsight-hadoop-create-linux-clusters-curl-rest.md)

### <a name="additional-ssh-users"></a>Utenti SSH aggiuntivi

È possibile aggiungere altri utenti SSH al cluster dopo la sua creazione, ma non è consigliabile farlo.

* I nuovi utenti SSH devono essere aggiunti a ogni nodo del cluster.

* I nuovi utenti SSH hanno lo stesso accesso a HDInsight dell'utente predefinito. Non è possibile limitare l'accesso ai dati o ai processi in HDInsight sulla base dell'account utente SSH.

Per limitare l'accesso in base al singolo utente è necessario usare un cluster HDInsight aggiunto a un dominio, che si serve di Active Directory per controllare l'accesso alle risorse del cluster.

L'uso di un cluster HDInsight aggiunto a un dominio permette di eseguire l'autenticazione tramite Active Directory dopo la connessione tramite SSH. Più utenti possono connettersi usando SSH e quindi eseguire l'autenticazione al proprio account Active Directory dopo la connessione. Per altre informazioni, vedere la sezione [HDInsight aggiunto a un dominio](#domainjoined).

##<a id="connect"></a> Connettersi a HDInsight

Anche se tutti i nodi in un cluster HDInsight eseguono il server SSH, è possibile connettersi solo ai nodi head o ai nodi perimetrali tramite Internet pubblico.

* Per connettersi ai _nodi head_, usare `CLUSTERNAME-ssh.azurehdinsight.net`, dove __CLUSTERNAME__ è il nome del cluster HDInsight. La porta 22 è quella predefinita per SSH e permette di connettersi al nodo head primario. La porta 23 permette di connettersi al nodo head secondario.

* Per connettersi a un _nodo perimetrale_, usare `EDGENAME.CLUSTERNAME-ssh.azurehdinsight.net`, dove __EDGENAME__ è il nome del nodo perimetrale e __CLUSTERNAME__ è il nome del cluster HDInsight. Usare la porta 22 per la connessione al nodo perimetrale.

Gli esempi seguenti illustrano come connettersi ai nodi head e al nodo perimetrale di un cluster denominato __myhdi__ usando __sshuser__ come nome utente SSH. Il nodo perimetrale è denominato __myedge__.

| Per | Opzione |
| ----- | ----- |
| Connettersi al nodo head primario | `ssh sshuser@myhdi-ssh.azurehdinsight.net` |
| Connettersi al nodo head secondario | `ssh -p 23 sshuser@myhdi-ssh.azurehdinsight.net` |
| Connettersi al nodo perimetrale | `ssh sshuser@edge.myhdi-ssh.azurehdinsight.net` |

Se l'account SSH è protetto da una password, viene richiesto di immetterla.

Se l'account SSH è protetto da una chiave pubblica, potrebbe essere necessario specificare il percorso della chiave privata corrispondente usando l'opzione `-i`. L'esempio seguente illustra l'uso dell'opzione `-i`:

    ssh -i /path/to/public.key sshuser@myhdi-ssh.azurehdinsight.net

### <a name="connect-to-other-nodes"></a>Connettersi ad altri nodi

I nodi di lavoro e i nodi Zookeeper non sono direttamente accessibili dall'esterno del cluster, ma è possibile accedervi dai nodi head o dai nodi perimetrali del cluster. Di seguito è riportata la procedura generale da seguire per connettersi ad altri nodi:

1. Usare SSH per connettersi a un nodo head o perimetrale:

        ssh sshuser@myhdi-ssh.azurehdinsight.net

2. Dalla connessione SSH al nodo head o perimetrale, usare il comando `ssh` per connettersi a un nodo di lavoro nel cluster:

        ssh sshuser@wn0-myhdi

    Per recuperare un elenco dei nodi di lavoro del cluster, vedere la sezione Esempio: Ottenere il nome di dominio completo dei nodi del cluster in [Gestire i cluster HDInsight mediante l'API REST Ambari](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes).

Se l'account SSH è protetto da una password, viene richiesto di immetterla e quindi viene stabilita la connessione.

Se si usa una chiave SSH per autenticare l'account utente, è necessario assicurarsi che l'ambiente locale sia configurato per l'inoltro dell'agente SSH.

> [!IMPORTANT]
> La procedura seguente presuppone l'uso di un sistema basato su Linux/UNIX e di Bash per Windows 10. Se la procedura non funziona per il sistema corrente, è consigliabile vedere la documentazione del client SSH.

1. Usando un editor di testo, aprire `~/.ssh/config`. Se questo file non esiste, è possibile crearlo immettendo `touch ~/.ssh/config` nella riga di comando.

2. Aggiungere il codice seguente al file. Sostituire *CLUSTERNAME* con il nome del cluster HDInsight.

        Host CLUSTERNAME-ssh.azurehdinsight.net
          ForwardAgent yes

    Questa voce permette di configurare l'inoltro dell'agente SSH per il cluster HDInsight.

3. Testare l'inoltro dell'agente SSH tramite il comando seguente dal terminale:

        echo "$SSH_AUTH_SOCK"

    Questo comando restituisce informazioni simili al testo seguente:

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    Se non viene restituita alcuna informazione, `ssh-agent` non è in esecuzione. Vedere le informazioni sugli script di avvio agente in [Using ssh-agent with ssh](http://mah.everybody.org/docs/ssh) (Uso di ssh-agent con ssh) o la documentazione del client SSH per conoscere la procedura specifica per l'installazione e la configurazione di `ssh-agent`.

4. Dopo avere verificato che **ssh-agent** è in esecuzione, usare il comando seguente per aggiungere la chiave privata SSH all'agente:

        ssh-add ~/.ssh/id_rsa

    Se la chiave privata viene archiviata in un altro file, sostituire `~/.ssh/id_rsa` con il percorso del file.

<a id="domainjoined"></a>
### <a name="domain-joined-hdinsight"></a>HDInsight aggiunto a un dominio

[HDInsight aggiunto al dominio](hdinsight-domain-joined-introduction.md) integra Kerberos con Hadoop in HDInsight. L'utente SSH non è un utente di dominio di Active Directory. Non è quindi possibile eseguire comandi Hadoop finché non si esegue l'autenticazione con Active Directory. Per autenticare la sessione SSH con Active Directory, seguire questa procedura:

1. Connettersi a un cluster HDInsight aggiunto al dominio usando SSH. Ad esempio, il comando seguente si connette a un cluster HDInsight denominato __myhdi__ usando un account SSH denominato __sshuser__.

        ssh sshuser@myhdi-ssh.azurehdinsight.net

2. Per eseguire l'autenticazione tramite un utente di dominio e una password, usare quanto segue:

        kinit

     Quando viene richiesto, immettere un nome utente di dominio e la relativa password.

    Per altre informazioni sulla configurazione degli utenti di dominio per cluster HDInsight aggiunti a un dominio, vedere [Configure Domain-joined HDInsight clusters](hdinsight-domain-joined-configure.md) (Configurare cluster HDInsight aggiunti a un dominio).

Dopo aver eseguito l'autenticazione con il comando `kinit`, è possibile usare comandi Hadoop come `hdfs dfs -ls /` o `hive`.

## <a id="tunnel"></a>Tunneling SSH

SSH può essere usato anche per effettuare il tunneling di richieste locali, ad esempio richieste Web, al cluster HDInsight. La richiesta viene inoltrata al cluster e quindi risolta all'interno del cluster stesso.

> [!IMPORTANT]
> Un tunnel SSH è un requisito per l'accesso al web dell'interfaccia utente per alcuni servizi Hadoop. Ad esempio, la UI di cronologia processi o di gestione delle risorse dell'interfaccia utente possono essere accessibili solo tramite un tunnel SSH.

Per altre informazioni sulla creazione e sull'uso di un tunnel SSH, vedere [Usare il tunneling SSH per accedere all'interfaccia Web di Ambari, JobHistory, NameNode, Oozie e altre interfacce Web](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come eseguire l'autenticazione usando una chiave SSH, è possibile imparare a usare MapReduce con Hadoop in HDInsight.

* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con HDInsight](hdinsight-use-pig.md)
* [Usare processi MapReduce con HDInsight](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/

