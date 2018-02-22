---
title: 'Usare SSH con Hadoop: Azure HDInsight | Microsoft Docs'
description: "È possibile accedere a HDInsight tramite Secure Shell (SSH). Questo documento offre informazioni sulla connessione a HDInsight con i comandi ssh e scp da client Windows, Linux, Unix o macOS."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
keywords: comandi Hadoop in Linux, comandi Linux per Hadoop, Hadoop in macOS, Hadoop SSH, cluster Hadoop SSH
ms.assetid: a6a16405-a4a7-4151-9bbf-ab26972216c5
ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/07/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 1a1fbac1eb4c529ea89f0f4c72b3317718ac45ea
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="connect-to-hdinsight-hadoop-using-ssh"></a>Connettersi a HDInsight (Hadoop) con SSH

Questo articolo illustra come usare [Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) per stabilire una connessione sicura a Hadoop in Azure HDInsight. 

HDInsight può usare Linux (Ubuntu) come sistema operativo per i nodi nel cluster Hadoop. La tabella seguente contiene le informazioni relative a indirizzo e porta necessarie per la connessione a HDInsight basato su Linux tramite un client SSH:

| Indirizzo | Porta | Connessione a |
| ----- | ----- | ----- |
| `<clustername>-ed-ssh.azurehdinsight.net` | 22 | Nodo perimetrale (R Server in HDInsight) |
| `<edgenodename>.<clustername>-ssh.azurehdinsight.net` | 22 | Nodo perimetrale (qualsiasi tipo di cluster, se è presente un nodo perimetrale) |
| `<clustername>-ssh.azurehdinsight.net` | 22 | Nodo head primario |
| `<clustername>-ssh.azurehdinsight.net` | 23 | Nodo head secondario |

> [!NOTE]
> Sostituire `<edgenodename>` con il nome del nodo perimetrale.
>
> Sostituire `<clustername>` con il nome del cluster.
>
> Se il cluster contiene un nodo perimetrale, è consigliabile __connettersi sempre al nodo perimetrale__ usando SSH. I nodi head ospitano servizi critici per l'integrità di Hadoop. Il nodo perimetrale esegue solo quanto inserito dall'utente nel nodo stesso.
>
> Per altre informazioni sull'uso dei nodi perimetrali, vedere l'articolo su come [usare nodi perimetrali in HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node).

> [!TIP]
> Alla prima connessione a HDInsight, è possibile che il client SSH visualizzi un avviso relativo all'impossibilità di confermare l'autenticità dell'host. Quando richiesto, selezionare 'sì' per aggiungere l'host all'elenco di server attendibili del client SSH.
>
> Se è già stata effettuata in precedenza una connessione a un server con lo stesso nome, è possibile che venga visualizzato un avviso relativo alla mancata corrispondenza della chiave host archiviata con la chiave host del server. Per informazioni su come rimuovere la voce esistente per il nome del server, vedere la documentazione per il client SSH.

## <a name="ssh-clients"></a>Client SSH

Nei sistemi Linux, Unix e macOS sono disponibili i comandi `ssh` e `scp`. Il client `ssh` viene in genere usato per creare una sessione della riga di comando remota con un sistema basato su Linux o Unix. Il client `scp` viene usato per la copia sicura dei file tra il client e il sistema remoto.

Per impostazione predefinita, Microsoft Windows non installa client SSH. I client `ssh` e `scp` sono disponibili per Windows nei pacchetti seguenti:

* Client OpenSSH (versione beta): in Fall Creators Update andare a __Impostazioni__ > __App e funzionalità__ > __Gestisci funzionalità facoltative__ > __Aggiungi una funzionalità__ e selezionare il __client OpenSSH__. 

    > [!NOTE]
    > Se dopo l'abilitazione di questa funzionalità i comandi `ssh` e `scp` non sono disponibili in PowerShell, disconnettersi e accedere di nuovo.

* [Bash in Ubuntu in Windows 10](https://msdn.microsoft.com/commandline/wsl/about): i comandi `ssh` e `scp` sono disponibili tramite Bash nella riga di comando di Windows.

* [Client OpenSSH (beta)](https://blogs.msdn.microsoft.com/powershell/2017/12/15/using-the-openssh-beta-in-windows-10-fall-creators-update-and-windows-server-1709/): questa è una funzionalità facoltativa introdotta in Windows 10 Fall Creators Update.

* [Azure Cloud Shell](../cloud-shell/quickstart.md): Cloud Shell offre un ambiente Bash nel browser, oltre a `ssh`, `scp` e altri comandi Linux comuni.

* [Git (https://git-scm.com/)](https://git-scm.com/): i comandi `ssh` e `scp` sono disponibili tramite la riga di comando GitBash.

Sono disponibili anche diversi client SSH con interfaccia grafica, come [PuTTY (http://www.chiark.greenend.org.uk/~sgtatham/putty/)](http://www.chiark.greenend.org.uk/~sgtatham/putty/) e [MobaXterm (http://mobaxterm.mobatek.net/)](http://mobaxterm.mobatek.net/). Anche se questi client possono essere usati per connettersi a HDInsight, il processo di connessione è diverso rispetto a quello tramite l'utilità `ssh`. Per altre informazioni, vedere la documentazione del client con interfaccia grafica usato.

## <a id="sshkey"></a>Autenticazione: chiavi SSH

Le chiavi SSH usano la [crittografia a chiave pubblica](https://en.wikipedia.org/wiki/Public-key_cryptography) per autenticare le sessioni SSH. Le chiavi SSH sono più sicure delle password e consentono di proteggere facilmente l'accesso al cluster Hadoop.

Se l'account SSH è protetto con una chiave, al momento della connessione il client deve fornire la chiave privata corrispondente:

* La maggior parte dei client può essere configurata per l'uso di una __chiave predefinita__. Negli ambienti Linux e Unix, ad esempio, il client `ssh` cerca una chiave privata in `~/.ssh/id_rsa`.

* È possibile specificare il __percorso di una chiave privata__. Con il client `ssh`, per specificare il percorso della chiave privata viene usato il parametro `-i`. Ad esempio, `ssh -i ~/.ssh/id_rsa sshuser@myedge.mycluster-ssh.azurehdinsight.net`.

* Se si hanno __più chiavi private__ per server diversi, considerare la possibilità di usare un'utilità come [ssh-agent (https://en.wikipedia.org/wiki/Ssh-agent)](https://en.wikipedia.org/wiki/Ssh-agent). L'utilità `ssh-agent` consente di selezionare automaticamente la chiave da usare quando si stabilisce una sessione SSH.

> [!IMPORTANT]
>
> Se si protegge la chiave privata con una passphrase, è necessario immettere la passphrase quando si usa la chiave. Per praticità, le utilità come `ssh-agent` possono memorizzare la password nella cache.

### <a name="create-an-ssh-key-pair"></a>Creare una coppia di chiavi SSH

Usare il comando `ssh-keygen` per creare i file di chiave pubblica e privata. Il comando seguente genera una coppia di chiavi RSA a 2048 bit che può essere usata con HDInsight:

    ssh-keygen -t rsa -b 2048

Durante il processo di creazione delle chiavi vengono richieste informazioni, ad esempio la posizione di archiviazione delle chiavi o se deve essere usata una passphrase. Al termine del processo vengono creati due file: una chiave pubblica e una chiave privata.

* La __chiave pubblica__ viene usata per creare un cluster HDInsight e ha estensione `.pub`.

* La __chiave privata__ viene usata per autenticare il client nel cluster HDInsight.

> [!IMPORTANT]
> È possibile proteggere le chiavi con una passphrase, che è di fatto una password per la chiave privata. Se qualcuno entra in possesso della chiave privata, per usarla deve avere la passphrase.

### <a name="create-hdinsight-using-the-public-key"></a>Creare cluster HDInsight con la chiave pubblica

| Metodo di creazione | Come usare la chiave pubblica |
| ------- | ------- |
| **Portale di Azure** | Deselezionare __Usa la stessa password dell'account di accesso del cluster__ e quindi selezionare __Chiave pubblica__ come tipo di autenticazione SSH. Selezionare infine il file di chiave pubblica oppure incollare il contenuto di testo del file nel campo __Chiave pubblica SSH__.</br>![Finestra di dialogo per la chiave pubblica SSH nella creazione di cluster HDInsight](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-public-key.png) |
| **Azure PowerShell** | Usare il parametro `-SshPublicKey` del cmdlet `New-AzureRmHdinsightCluster` e passare il contenuto della chiave pubblica come stringa.|
| **Interfaccia della riga di comando di Azure 1.0** | Usare il parametro `--sshPublicKey` del comando `azure hdinsight cluster create` e passare il contenuto della chiave pubblica come stringa. |
| **Modello di Resource Manager** | Per un esempio dell'uso di SSH con un modello, vedere l'articolo su come [distribuire HDInsight in Linux con una chiave SSH](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-publickey/). L'elemento `publicKeys` del file [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-publickey/azuredeploy.json) viene usato per passare le chiavi ad Azure durante la creazione del cluster. |

## <a id="sshpassword"></a>Autenticazione: password

Gli account SSH possono essere protetti con una password. Quando ci si connette a HDInsight con SSH, viene richiesto di immettere la password.

> [!WARNING]
> Microsoft sconsiglia di usare l'autenticazione tramite password per SSH. Le password sono intuibili e vulnerabili ad attacchi di forza bruta. È consigliabile usare invece [chiavi SSH per l'autenticazione](#sshkey).

### <a name="create-hdinsight-using-a-password"></a>Creare cluster HDInsight con una password

| Metodo di creazione | Come specificare la password |
| --------------- | ---------------- |
| **Portale di Azure** | Per impostazione predefinita, l'account utente SSH ha la stessa password dell'account di accesso del cluster. Per usare una password diversa, deselezionare __Usa la stessa password dell'account di accesso del cluster__ e quindi immettere la password nel campo __Password SSH__.</br>![Finestra di dialogo per la password SSH nella creazione di cluster HDInsight](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-password.png)|
| **Azure PowerShell** | Usare il parametro `--SshCredential` del cmdlet `New-AzureRmHdinsightCluster` e passare un oggetto `PSCredential` contenente il nome e la password dell'account utente SSH. |
| **Interfaccia della riga di comando di Azure 1.0** | Usare il parametro `--sshPassword` del comando `azure hdinsight cluster create` e specificare il valore della password. |
| **Modello di Resource Manager** | Per un esempio dell'uso di una password con un modello, vedere l'articolo su come [distribuire HDInsight in Linux con una password SSH](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/). L'elemento `linuxOperatingSystemProfile` del file [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-password/azuredeploy.json) viene usato per passare il nome e la password dell'account SSH ad Azure durante la creazione del cluster.|

### <a name="change-the-ssh-password"></a>Modificare la password SSH

Per informazioni sulla modifica della password dell'account utente SSH, vedere la sezione __Modificare le password__ del documento relativo alla [gestione di HDInsight](hdinsight-administer-use-portal-linux.md#change-passwords).

## <a id="domainjoined"></a>Autenticazione: HDInsight aggiunto al dominio

Se si usa un __cluster HDInsight aggiunto al dominio__, è necessario usare il comando `kinit` dopo la connessione con SSH. Questo comando richiede di specificare un utente di dominio e una password e autentica la sessione con il dominio di Azure Active Directory associato al cluster.

Per altre informazioni, vedere [Configurare i cluster HDInsight aggiunti al dominio](./domain-joined/apache-domain-joined-configure.md).

## <a name="connect-to-nodes"></a>Stabilire la connessione ai nodi

È possibile accedere ai nodi head e al nodo perimetrale (se presente) tramite Internet sulle porte 22 e 23.

* Quando ci si connette ai __nodi head__, usare la porta __22__ per la connessione al nodo head primario e la porta __23__ per la connessione al nodo head secondario. Il nome di dominio completo da usare è `clustername-ssh.azurehdinsight.net`, dove `clustername` è il nome del cluster.

    ```bash
    # Connect to primary head node
    # port not specified since 22 is the default
    ssh sshuser@clustername-ssh.azurehdinsight.net

    # Connect to secondary head node
    ssh -p 23 sshuser@clustername-ssh.azurehdinsight.net
    ```
    
* Quando ci si connette al __nodo perimetrale__, usare la porta 22. Il nome di dominio completo è `edgenodename.clustername-ssh.azurehdinsight.net`, dove `edgenodename` è un nome specificato durante la creazione del nodo perimetrale. `clustername` è il nome del cluster.

    ```bash
    # Connect to edge node
    ssh sshuser@edgnodename.clustername-ssh.azurehdinsight.net
    ```

> [!IMPORTANT]
> Gli esempi precedenti presuppongono l'uso dell'autenticazione della password o che l'autenticazione del certificato avvenga automaticamente. Se si usa una coppia di chiavi SSH per l'autenticazione e il certificato non viene usato automaticamente, usare il parametro `-i` per specificare la chiave privata. Ad esempio, `ssh -i ~/.ssh/mykey sshuser@clustername-ssh.azurehdinsight.net`.

Una volta stabilita la connessione, il prompt cambia per indicare il nome utente SSH e il nodo a cui si è connessi. Quando ad esempio si è connessi al nodo head primario come `sshuser`, il prompt è `sshuser@hn0-clustername:~$`.

### <a name="connect-to-worker-and-zookeeper-nodes"></a>Connettersi a nodi del ruolo di lavoro e Zookeeper

I nodi del ruolo di lavoro e i nodi Zookeeper non sono direttamente accessibili da Internet, ma dai nodi head o perimetrali del cluster. Di seguito è riportata la procedura generale da seguire per connettersi ad altri nodi:

1. Usare SSH per connettersi a un nodo head o perimetrale:

        ssh sshuser@myedge.mycluster-ssh.azurehdinsight.net

2. Dalla connessione SSH al nodo head o perimetrale, usare il comando `ssh` per connettersi a un nodo di lavoro nel cluster:

        ssh sshuser@wn0-myhdi

    Per recuperare un elenco dei nomi di nodo, vedere il documento [Gestire i cluster HDInsight con l'API REST Ambari](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes).

Se l'account SSH viene protetto usando una __password__, immetterla quando ci si connette.

Se l'account SSH viene protetto usando __chiavi SSH__, verificare che l'inoltro SSH sia abilitato nel client.

> [!NOTE]
> Un altro modo per accedere direttamente a tutti i nodi del cluster è installare HDInsight in una rete virtuale di Azure. È quindi possibile aggiungere il computer remoto alla stessa rete virtuale e accedere direttamente a tutti i nodi del cluster.
>
> Per altre informazioni, vedere l'articolo su come [usare una rete virtuale con HDInsight](hdinsight-extend-hadoop-virtual-network.md).

### <a name="configure-ssh-agent-forwarding"></a>Configurare l'inoltro dell'agente SSH

> [!IMPORTANT]
> La procedura seguente presuppone l'uso di un sistema basato su Linux o UNIX e di Bash per Windows 10. Se la procedura non funziona per il sistema corrente, è consigliabile vedere la documentazione del client SSH.

1. Usando un editor di testo, aprire `~/.ssh/config`. Se questo file non esiste, è possibile crearlo immettendo `touch ~/.ssh/config` nella riga di comando.

2. Aggiungere il testo seguente al file `config`.

        Host <edgenodename>.<clustername>-ssh.azurehdinsight.net
          ForwardAgent yes

    Sostituire le informazioni di __Host__ con l'indirizzo del nodo a cui ci si connette con SSH. L'esempio precedente usa il nodo perimetrale. Questa voce configura l'inoltro dell'agente SSH per il nodo specificato.

3. Testare l'inoltro dell'agente SSH tramite il comando seguente dal terminale:

        echo "$SSH_AUTH_SOCK"

    Questo comando restituisce informazioni simili al testo seguente:

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    Se non viene restituita alcuna informazione, `ssh-agent` non è in esecuzione. Per altre informazioni, vedere il contenuto relativo agli script di avvio agente in [Using ssh-agent with ssh (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh) (Uso di ssh-agent con ssh) o la documentazione del client SSH.

4. Dopo avere verificato che **ssh-agent** è in esecuzione, usare il comando seguente per aggiungere la chiave privata SSH all'agente:

        ssh-add ~/.ssh/id_rsa

    Se la chiave privata viene archiviata in un altro file, sostituire `~/.ssh/id_rsa` con il percorso del file.

5. Connettersi ai nodi head o al nodo perimetrale del cluster con SSH. Usare quindi il comando SSH per connettersi a un nodo del ruolo di lavoro o Zookeeper. La connessione viene stabilita usando la chiave inoltrata.

## <a name="copy-files"></a>Copiare i file

L'utilità `scp` può essere usata per copiare i file da e verso singoli nodi del cluster. Il comando seguente copia ad esempio la directory `test.txt` dal sistema locale al nodo head primario:

```bash
scp test.txt sshuser@clustername-ssh.azurehdinsight.net:
```

Poiché dopo `:` non è specificato alcun percorso, il file viene inserito nella home directory `sshuser`.

L'esempio seguente copia il file `test.txt` dalla home directory `sshuser` nel nodo head primario al sistema locale:

```bash
scp sshuser@clustername-ssh.azurehdinsight.net:test.txt .
```

> [!IMPORTANT]
> `scp` può accedere solo al file system dei singoli nodi all'interno del cluster. Non può essere usato per accedere ai dati nell'archivio compatibile con HDFS per il cluster.
>
> Usare `scp` quando è necessario caricare una risorsa per l'uso da una sessione SSH. Caricare ad esempio uno script Python e quindi eseguire lo script da una sessione SSH.
>
> Per informazioni sul caricamento diretto dei dati nell'archivio compatibile con HDFS, vedere i documenti seguenti:
>
> * [HDInsight con Archiviazione di Azure](hdinsight-hadoop-use-blob-storage.md).
>
> * [HDInsight con Azure Data Lake Store](hdinsight-hadoop-use-data-lake-store.md).

## <a name="next-steps"></a>Passaggi successivi

* [Usare il tunneling SSH con HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)
* [Usare una rete virtuale con HDInsight](hdinsight-extend-hadoop-virtual-network.md)
* [Usare nodi perimetrali in HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node)