---
title: Configurare la replica di cluster HBase nelle reti virtuali di Azure - Azure HDInsight
description: Informazioni su come configurare la replica HBase da una versione di HDInsight a un'altra, per bilanciamento del carico, disponibilità elevata, aggiornamenti e migrazione senza tempo di inattività e ripristino di emergenza.
services: hdinsight,virtual-network
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/15/2018
ms.openlocfilehash: b03cffe35337ee5720944dc4cfe88c17c3b5b748
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53163837"
---
# <a name="set-up-apache-hbase-cluster-replication-in-azure-virtual-networks"></a>Configurare la replica di cluster Apache HBase nelle reti virtuali di Azure

Informazioni su come configurare la replica [Apache HBase](http://hbase.apache.org/) in una rete virtuale o tra due reti virtuali in Azure.

La replica di cluster usa una metodologia con push dell'origine. Un cluster HBase può essere un'origine o una destinazione o può soddisfare entrambi i ruoli in una sola volta. La replica è asincrona. L'obiettivo della replica è la coerenza finale. Quando l'origine riceve una modifica a una famiglia di colonne con la replica abilitata, tale modifica viene propagata in tutti i cluster di destinazione. Quando i dati vengono replicati da un cluster a un altro, viene tenuta traccia del cluster di origine e di tutti i cluster che hanno già usato i dati, per evitare cicli di replica.

In questa esercitazione si configura una replica origine-destinazione. Per altre topologie di cluster, vedere la [guida di riferimento di Apache HBase](http://hbase.apache.org/book.html#_cluster_replication).

Di seguito sono illustrati i casi d'uso della replica HBase per una singola rete virtuale:

* Bilanciamento del carico. È ad esempio possibile eseguire analisi o processi MapReduce nel cluster di destinazione e inserire i dati nel cluster di origine.
* Aggiunta della disponibilità elevata.
* Migrazione dei dati da un cluster HBase a un altro.
* Aggiornamento di un cluster Azure HDInsight da una versione a un'altra.

Di seguito sono illustrati i casi d'uso della replica HBase per due reti virtuali:

* Configurazione del ripristino di emergenza.
* Bilanciamento del carico e partizionamento dell'applicazione.
* Aggiunta della disponibilità elevata.

È possibile replicare i cluster tramite gli script di [azione script](../hdinsight-hadoop-customize-cluster-linux.md) di [GitHub](https://github.com/Azure/hbase-utils/tree/master/replication).

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione, è necessario disporre di un abbonamento ad Azure. Vedere [Get an Azure free trial](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (Ottenere una versione di valutazione gratuita di Azure).

## <a name="set-up-the-environments"></a>Configurare gli ambienti

Sono disponibili tre opzioni di configurazione:

- Due cluster Apache HBase in una rete virtuale di Azure.
- Due cluster Apache HBase in due reti virtuali diverse nella stessa area.
- Due cluster Apache HBase in due reti virtuali diverse in due aree diverse (replica geografica).

Questo articolo illustra lo scenario di replica geografica.

Per semplificare la configurazione degli ambienti, sono disponibili alcuni [modelli di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Se si preferisce configurare gli ambienti con altri metodi, vedere:

- [Creare cluster Apache Hadoop in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
- [Creare cluster Apache HBase nella rete virtuale di Azure](apache-hbase-provision-vnet.md)

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Configurare due reti virtuali in due aree diverse

Per usare un modello che riesca a creare due reti virtuali in due aree diverse e la connessione VPN tra le reti virtuali, selezionare il pulsante seguente **Implementa in Azure**. La definizione del modello è disponibile in un'[archiviazione BLOB pubblica](https://hditutorialdata.blob.core.windows.net/hbaseha/azuredeploy.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

Alcuni valori hardcoded nel modello:

**Rete virtuale 1**

| Proprietà | Valore |
|----------|-------|
| Località | Stati Uniti occidentali |
| Nome della rete virtuale | &lt;ClusterNamePrevix>-vnet1 |
| Prefisso dello spazio degli indirizzi | 10.1.0.0/16 |
| Nome della subnet | subnet 1 |
| Prefisso della subnet | 10.1.0.0/24 |
| Nome della subnet (gateway) | GatewaySubnet (non può essere modificato) |
| Prefisso della subnet (gateway) | 10.1.255.0/27 |
| Nome del gateway | vnet1gw |
| Tipo gateway | VPN |
| Tipo di gateway VPN | RouteBased |
| SKU del gateway | Basic |
| IP del gateway | vnet1gwip |

**VNet 2**

| Proprietà | Valore |
|----------|-------|
| Località | Stati Uniti orientali |
| Nome della rete virtuale | &lt;ClusterNamePrevix>-vnet2 |
| Prefisso dello spazio degli indirizzi | 10.2.0.0/16 |
| Nome della subnet | subnet 1 |
| Prefisso della subnet | 10.2.0.0/24 |
| Nome della subnet (gateway) | GatewaySubnet (non può essere modificato) |
| Prefisso della subnet (gateway) | 10.2.255.0/27 |
| Nome del gateway | vnet2gw |
| Tipo gateway | VPN |
| Tipo di gateway VPN | RouteBased |
| SKU del gateway | Basic |
| IP del gateway | vnet1gwip |

## <a name="setup-dns"></a>Configurare il DNS

Nella sezione precedente il modello crea una macchina virtuale Ubuntu in ognuna delle due reti virtuali.  In questa sezione si installa Bind nelle due macchine virtuali DNS e quindi si configura l'inoltro DNS nelle due macchine virtuali.

Per installare Bind, è necessario trovare l'indirizzo IP pubblico delle due macchine virtuali DNS.

1. Aprire il [portale di Azure](https://portal.azure.com).
2. Aprire la macchina virtuale DNS selezionando **Gruppi di risorse > [nome gruppo di risorse] > [vnet1DNS]**.  Il nome del gruppo di risorse è quello creato nella procedura precedente. I nomi di macchina virtuale DNS predefiniti sono *vnet1DNS* e *vnet2NDS*.
3. Selezionare **Proprietà** per aprire la pagina delle proprietà della rete virtuale.
4. Annotare l'**Indirizzo IP pubblico** e verificare inoltre l'**Indirizzo IP privato**.  L'indirizzo IP privato deve essere **10.1.0.4** per vnet1DNS e **10.2.0.4** per vnet2DNS.  
5. Modificare i server DNS in modo che entrambe le reti virtuali usino i server DNS predefiniti (forniti da Azure) per consentire l'accesso in ingresso e in uscita ai pacchetti di download per poter installare Bind con i passaggi seguenti.

Per installare Bind, usare la procedura seguente:

1. Usare SSH per connettersi all'__indirizzo IP pubblico__ della macchina virtuale DNS. L'esempio seguente consente la connessione a una macchina virtuale all'indirizzo 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Sostituire `sshuser` con l'account utente SSH specificato durante la creazione della macchina virtuale DNS.

    > [!NOTE]
    > È possibile ottenere l'utilità `ssh` in diversi modi. In Linux, Unix e macOS viene fornita come parte del sistema operativo. Se si usa Windows, prendere in considerazione una delle opzioni seguenti:
    >
    > * [Azure Cloud Shell](../../cloud-shell/quickstart.md)
    > * [Bash in Ubuntu su Windows 10](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git (https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Per installare Bind, usare i comandi seguenti dalla sessione SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Configurare Bind per inoltrare le richieste di risoluzione dei nomi al server DNS locale. A questo scopo, usare il testo seguente come contenuto del file `/etc/bind/named.conf.options`:

    ```
    acl goodclients {
        10.1.0.0/16; # Replace with the IP address range of the virtual network 1
        10.2.0.0/16; # Replace with the IP address range of the virtual network 2
        localhost;
        localhost;
    };
    
    options {
        directory "/var/cache/bind";
        recursion yes;
        allow-query { goodclients; };

        forwarders {
            168.63.129.16; #This is the Azure DNS server
        };

        dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
    };
    ```
    
    > [!IMPORTANT]
    > Sostituire i valori nella sezione `goodclients` con l'intervallo di indirizzi IP delle due reti virtuali. Questa sezione definisce gli indirizzi da cui il server DNS accetta le richieste.

    Per modificare questo file, usare il comando seguente:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Per salvare il file, usare __CTRL+X__, __Y__ e quindi __INVIO__.

4. Dalla sessione SSH usare il comando seguente:

    ```bash
    hostname -f
    ```

    Il comando restituisce un valore simile al testo seguente:

        vnet1DNS.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    Il testo `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` è il __suffisso DNS__ per la rete virtuale. Salvare questo valore, che verrà usato in un secondo momento.

    È inoltre necessario individuare il suffisso DNS dell'altro server DNS, perché sarà necessario nel passaggio successivo.

5. Per configurare Bind per la risoluzione dei nomi DNS per le risorse nella rete virtuale, usare il testo seguente come contenuto del file `/etc/bind/named.conf.local`:

    ```
    // Replace the following with the DNS suffix for your virtual network
    zone "v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net" {
            type forward;
            forwarders {10.2.0.4;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]
    > È necessario sostituire `v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` con il suffisso DNS dell'altra rete virtuale. Inoltre, l'indirizzo IP della riga forwarders è l'indirizzo IP privato del server DNS nell'altra rete virtuale.

    Per modificare questo file, usare il comando seguente:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Per salvare il file, usare __CTRL+X__, __Y__ e quindi __INVIO__.

6. Per avviare Bind, usare il comando seguente:

    ```bash
    sudo service bind9 restart
    ```

7. Per verificare che Bind riesca a risolvere i nomi delle risorse nell'altra rete virtuale, usare i comandi seguenti:

    ```bash
    sudo apt install dnsutils
    nslookup vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    ```

    > [!IMPORTANT]
    > Sostituire `vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` con il nome di dominio completo (FQDN) della macchina virtuale DNS nell'altra rete.
    >
    > Sostituire `10.2.0.4` con l'__indirizzo IP interno__ del server DNS personalizzato nell'altra rete virtuale.

    La risposta visualizzata sarà simile al testo seguente:

    ```
    Server:         10.2.0.4
    Address:        10.2.0.4#53
    
    Non-authoritative answer:
    Name:   vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    Address: 10.2.0.4
    ```

    Fino ad ora, non è possibile cercare l'indirizzo IP dall'altra rete senza specificare l'indirizzo IP del server DNS.

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Configurare la rete virtuale per usare il server DNS personalizzato

Per configurare la rete virtuale per usare il server DNS personalizzato invece del resolver ricorsivo di Azure, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com) selezionare la rete virtuale e quindi selezionare __Server DNS__.

2. Selezionare __Personalizzato__ e immettere l'__indirizzo IP interno__ del server DNS personalizzato. Infine, selezionare __Salva__.

6. Aprire la macchina virtuale del server DNS in vnet1 e fare clic su **Riavvia**.  È necessario riavviare tutte le macchine virtuali nella rete virtuale per rendere effettiva la configurazione DNS.
7. Ripetere i passaggi di configurazione del server DNS personalizzato per vnet2.

Per testare la configurazione DNS, è possibile connettersi alle due macchine virtuali DNS tramite SSH ed eseguire il ping del server DNS dell'altra rete virtuale usando il relativo nome host. Se non funziona, usare il comando seguente per controllare lo stato del servizio DNS:

```bash
sudo service bind9 status
```

## <a name="create-apache-hbase-clusters"></a>Creare i cluster Apache HBase

Creare un cluster [Apache HBase](http://hbase.apache.org/) in ognuna delle due reti virtuali con la configurazione seguente:

- **Nome gruppo di risorse**: usare lo stesso nome di gruppo di risorse creato per le reti virtuali.
- **Tipo di cluster**: hbase
- **Versione**: HBase 1.1.2 (HDI 3.6)
- **Posizione**: Usare la stessa posizione della rete virtuale.  Per impostazione predefinita, vnet1 è *Stati Uniti occidentali* e vnet2 è *Stati Uniti orientali*.
- **Archiviazione**: creare un nuovo account di archiviazione per il cluster.
- **Rete virtuale** (da Impostazioni avanzate nel portale): Selezionare vnet1 creata nella procedura precedente.
- **Subnet**: il nome predefinito usato nel modello è **subnet1**.

Per verificare che l'ambiente sia configurato correttamente, provare a effettuare il ping dell'FQDN del nodo head tra i due cluster.

## <a name="load-test-data"></a>Dati del test di carico

Quando si esegue la replica di un cluster, è necessario specificare le tabelle da replicare. In questa sezione, alcuni dati verranno caricati nel cluster di origine. Nella sezione successiva viene abilitata la replica tra i due cluster.

Per creare una tabella **Contatti** e inserire alcuni dati nella tabella, seguire le istruzioni in [Esercitazione su Apache HBase: Introduzione ad Apache HBase in HDInsight](apache-hbase-tutorial-get-started-linux.md).

## <a name="enable-replication"></a>Abilitare la replica

La procedura seguente illustra come chiamare lo script di azione script dal portale di Azure. Per informazioni su come eseguire un'azione script tramite Azure PowerShell e l'interfaccia della riga di comando classica di Azure, vedere [Personalizzare i cluster HdInsight usando l'azione script](../hdinsight-hadoop-customize-cluster-linux.md).

**Per abilitare la replica di HBase dal portale di Azure**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Aprire il cluster HBase di origine.
3. Dal menu del cluster scegliere **Azioni script**.
4. Nella parte superiore della pagina selezionare **Invia nuova**.
5. Selezionare o immettere le seguenti informazioni:

  1. **Nome**: immettere **Abilitazione replica**.
  2. **URL script Bash**: Immettere **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**.
  3.  **Head**: assicurarsi che questa opzione sia selezionata. Deselezionare gli altri tipi di nodo.
  4. **Parametri**: i parametri di esempio seguenti abilitano la replica per tutte le tabelle esistenti e quindi copiano tutti i dati dal cluster di origine al cluster di destinazione:

          -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata
    
    >[!note]
    >
    > Usare il nome host invece di FQDN per il nome DNS del cluster di origine e di destinazione.

6. Selezionare **Create**. L'esecuzione dello script può richiedere tempo, in particolare se si usa l'argomento **-copydata**.

Argomenti obbligatori:

|NOME|DESCRIZIONE|
|----|-----------|
|-s, --src-cluster | Specifica il nome DNS del cluster HBase di origine. Ad esempio: -s hbsrccluster, --src-cluster=hbsrccluster |
|-d, - dst-cluster | Specifica il nome DNS del cluster HBase di destinazione (replica). Ad esempio: -s dsthbcluster, --src-cluster=dsthbcluster |
|-sp, --src-ambari-password | Specifica la password amministratore per Ambari nel cluster HBase di origine. |
|-dp, --dst-ambari-password | Specificare la password amministratore per Ambari nel cluster HBase di destinazione.|

Argomenti facoltativi:

|NOME|DESCRIZIONE|
|----|-----------|
|-su, --src-ambari-user | Specifica il nome utente amministratore per Ambari nel cluster HBase di origine. Il valore predefinito è **admin**. |
|-du, --dst-ambari-user | Specifica il nome utente amministratore per Ambari nel cluster HBase di destinazione. Il valore predefinito è **admin**. |
|-t, --table-list | Specifica le tabelle da replicare. Ad esempio: --table-list="table1;table2;table3". Se non si specificano tabelle, vengono replicate tutte le tabelle HBase esistenti.|
|-m, --machine | Specifica il nodo head in cui viene eseguita l'azione script. Il valore può essere **hn0** oppure **hn1** e deve essere selezionato in base al nodo head attivo. Usare questa opzione quando si esegue lo script $0 come azione script dal portale di HDInsight o da Azure PowerShell.|
|-cp, -copydata | Abilita la migrazione dei dati esistenti nelle tabelle in cui è abilitata la replica. |
|-rpm, -replicate-phoenix-meta | Abilita la replica nelle tabelle di sistema Phoenix. <br><br>*Questa opzione deve essere usata con attenzione.* È consigliabile ricreare le tabelle di Phoenix nei cluster di replica prima di usare questo script. |
|-h, --help | Visualizza le informazioni sull'utilizzo. |

La sezione `print_usage()` dello [script](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) contiene una spiegazione dettagliata dei parametri.

Dopo aver distribuito correttamente l'azione script, è possibile usare SSH per connettersi al cluster HBase di destinazione e verificare che i dati siano stati replicati.

### <a name="replication-scenarios"></a>Scenari di replica

L'elenco seguente illustra alcuni casi di utilizzo generale e le relative impostazioni dei parametri:

- **Abilitare la replica su tutte le tabelle tra i due cluster**. Questo scenario non richiede la copia o la migrazione dei dati esistenti nelle tabelle e non usa tabelle Phoenix. È possibile usare i parametri seguenti:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **Abilitare la replica su tabelle specifiche**. Per abilitare la replica in table1, table2 e table3, usare i parametri seguenti:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **Abilitare la replica in tabelle specifiche e copiare i dati esistenti**. Per abilitare la replica in table1, table2 e table3, usare i parametri seguenti:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **Abilitare la replica in tutte le tabelle e replicare i metadati di Phoenix dall'origine alla destinazione**. La replica dei metadati di Phoenix non è perfetta. Usarla con cautela. È possibile usare i parametri seguenti:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>Copia e migrazione dei dati

Sono disponibili due script di azione script distinti per la copia o la migrazione dei dati dopo aver abilitato la replica:

- [Script per tabelle di piccole dimensioni](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (tabelle con dimensioni di pochi gigabyte per cui si prevede che la copia completa richieda meno di un'ora)

- [Script per tabelle di grandi dimensioni](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (tabelle per cui si prevede che la copia richieda più di un'ora)

È possibile eseguire la stessa procedura descritta in [Abilitare la replica](#enable-replication) per chiamare l'azione script. È possibile usare i parametri seguenti:

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

La sezione `print_usage()` dello [script](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) contiene una descrizione dettagliata dei parametri.

### <a name="scenarios"></a>Scenari

- **Copiare tabelle specifiche (test1, test2 e test3) per tutte le righe modificate finora (timestamp corrente)**:

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  In alternativa:

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **Copiare tabelle specifiche con un intervallo di tempo specificato**:

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>Disabilitare la replica

Per disabilitare la replica, usare un altro script di azione script disponibile in [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). È possibile eseguire la stessa procedura descritta in [Abilitare la replica](#enable-replication) per chiamare l'azione script. È possibile usare i parametri seguenti:

    -m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

La sezione `print_usage()` dello [script](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) contiene una spiegazione dettagliata dei parametri.

### <a name="scenarios"></a>Scenari

- **Disabilitare la replica in tutte le tabelle**:

        -m hn1 -s <source hbase cluster name> -sp Mypassword\!789 -all
  oppure

        --src-cluster=<source hbase cluster name> --dst-cluster=<destination hbase cluster name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **Disabilitare la replica in tabelle specifiche (table1, table2 e table3)**:

        -m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come configurare la replica Apache HBase in una rete virtuale o tra due reti virtuali. Per altre informazioni su HDInsight e Apache HBase, vedere questi articoli:

* [Introduzione ad Apache HBase in HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Panoramica di Apache HBase di HDInsight](./apache-hbase-overview.md)
* [Creare cluster Apache HBase nella rete virtuale di Azure](./apache-hbase-provision-vnet.md)

