---
title: Connettere Azure HDInsight alla rete locale
description: Informazioni su come creare un cluster HDInsight in una rete virtuale di Azure e quindi connetterlo alla rete locale. Informazioni su come configurare la risoluzione dei nomi tra HDInsight e la rete locale usando un server DNS personalizzato.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/04/2019
ms.openlocfilehash: 52fe8c05101f9647549acec276f0bdb9fa52d1c7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60537901"
---
# <a name="connect-hdinsight-to-your-on-premises-network"></a>Connettere HDInsight alla rete locale

Informazioni su come connettere HDInsight alla rete locale usando Reti virtuali di Azure e un gateway VPN. Questo documento fornisce le informazioni di pianificazione su:

* Uso di HDInsight in una rete virtuale di Azure che si connette alla rete locale.
* Configurazione della risoluzione dei nomi DNS tra la rete virtuale e la rete locale.
* Configurazione dei gruppi di sicurezza di rete per limitare l'accesso Internet per HDInsight.
* Porte fornite da HDInsight nella rete virtuale.

## <a name="overview"></a>Panoramica

Per consentire a HDInsight e alle risorse nella rete aggiunta di comunicare in base al nome, è necessario eseguire queste operazioni:

* Creare una rete virtuale di Azure.
* Creare un server DNS personalizzato in Rete virtuale di Azure.
* Configurare la rete virtuale per usare il server DNS personalizzato invece del resolver ricorsivo predefinito di Azure.
* Configurare l'inoltro tra il server DNS personalizzato e il server DNS locale.

Questa configurazione consente il comportamento seguente:

* Le richieste per i nomi di dominio completi con il suffisso DNS __per la rete virtuale__ vengono inoltrate al server DNS personalizzato. Il server DNS personalizzato inoltra quindi le richieste al resolver ricorsivo di Azure, che restituisce l'indirizzo IP.
* Tutte le altre richieste vengono inoltrate al server DNS locale. Anche le richieste di risorse Internet pubbliche, ad esempio microsoft.com, vengono inoltrate al server DNS locale per la risoluzione dei nomi.

Nel diagramma seguente le linee verdi sono richieste di risorse che terminano nel suffisso DNS della rete virtuale. Le linee blu sono richieste di risorse nella rete locale o nella rete Internet pubblica.

![Diagramma della modalità di risoluzione delle richieste DNS nella configurazione usata in questo documento](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

## <a name="prerequisites"></a>Prerequisiti

* Un client SSH. Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).
* Se si usa PowerShell, è necessario il [modulo di AZ](https://docs.microsoft.com/powershell/azure/overview).
* Se vogliono usare Azure CLI e non è stato ancora installato, vedere [installare CLI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-virtual-network-configuration"></a>Creare la configurazione della rete virtuale

Vedere i documenti seguenti per informazioni su come creare una rete virtuale di Azure connessa alla rete locale:

* [Uso del portale di Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Uso di Azure PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Uso dell'interfaccia della riga di comando di Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="create-custom-dns-server"></a>Creare server DNS personalizzato

> [!IMPORTANT]  
> È necessario creare e configurare il server DNS prima di installare HDInsight nella rete virtuale.

Questa procedura usa il [portale di Azure](https://portal.azure.com) per creare una macchina virtuale di Azure. Per informazioni su altri modi per creare una macchina virtuale, vedere [Creare una VM: interfaccia della riga di comando di Azure](../virtual-machines/linux/quick-create-cli.md) e [Creare una VM: Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md).  Per creare una VM Linux che usa il software DNS [Bind](https://www.isc.org/downloads/bind/), eseguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
  
2. Nel menu a sinistra, passare a **+ crea una risorsa** > **calcolo** > **Ubuntu Server 18.04 LTS**.

    ![Creare una macchina virtuale Ubuntu](./media/connect-on-premises-network/create-ubuntu-vm.png)

3. Nella scheda __Informazioni di base__ immettere le informazioni seguenti:  
  
    | Campo | Value |
    | --- | --- |
    |Sottoscrizione |Selezionare la sottoscrizione appropriata.|
    |Gruppo di risorse |Selezionare il gruppo di risorse che contiene la rete virtuale creata in precedenza.|
    |Nome macchina virtuale | Immettere un nome descrittivo che identifica la macchina virtuale. In questo esempio viene usato **DNSProxy**.|
    |Region | Selezionare la stessa area della rete virtuale creata in precedenza.  Non tutte le dimensioni di macchina virtuale sono disponibili in tutte le aree.  |
    |Opzioni di disponibilità |  Selezionare il livello di disponibilità desiderato.  Azure offre una gamma di opzioni per la gestione della disponibilità e della resilienza delle applicazioni.  Progettando una soluzione per l'uso di macchine virtuali replicate in zone di disponibilità o set di disponibilità è possibile proteggere le app e i dati da eventuali interruzioni del data center ed eventi di manutenzione. Im questo esempio viene usata l'opzione **La ridondanza dell'infrastruttura non è richiesta**. |
    |Image | Lasciare **LTS Ubuntu Server 18.04**. |
    |Tipo di autenticazione | __Password__ o __Chiave pubblica SSH__: metodo di autenticazione per l'account SSH. Si consiglia di usare le chiavi pubbliche, che sono più sicure. Questo esempio viene usato **Password**.  Per altre informazioni, vedere il documento [Creare e usare chiavi SSH per VM Linux](../virtual-machines/linux/mac-create-ssh-keys.md).|
    |Nome utente |Immettere il nome utente dell'amministratore della macchina virtuale.  In questo esempio viene usato **sshuser**.|
    |Password o chiave pubblica SSH | Il campo disponibile è determinato dall'opzione selezionata per **Tipo di autenticazione**.  Immettere il valore appropriato.|
    |Porte in ingresso pubbliche|Selezionare **Consenti porte selezionate**. Quindi selezionare **SSH (22)** dalle **selezionare le porte in ingresso** elenco a discesa.|

    ![Configurazione di base della macchina virtuale](./media/connect-on-premises-network/vm-basics.png)

    Per le altre voci lasciare i valori predefiniti e quindi selezionare la scheda **Rete**.

4. Nella scheda **Rete** immettere le informazioni seguenti:

    | Campo | Value |
    | --- | --- |
    |Rete virtuale | Selezionare la rete virtuale creata in precedenza.|
    |Subnet | Selezionare la subnet predefinita per la rete virtuale creata in precedenza. __Non__ selezionare la subnet usata dal gateway VPN.|
    |IP pubblico | Usare il valore compilato automaticamente.  |

    ![Impostazioni della rete virtuale](./media/connect-on-premises-network/virtual-network-settings.png)

    Per le altre voci lasciare i valori predefiniti e quindi selezionare la scheda **Rivedi e crea**.

5. Nella scheda **Rivedi e crea** selezionare **Crea** per creare la macchina virtuale.

### <a name="review-ip-addresses"></a>Verificare gli indirizzi IP
Dopo aver creato la macchina virtuale, si riceverà una notifica contenente il messaggio **La distribuzione è riuscita** e un pulsante **Vai alla risorsa**.  Selezionare **Vai alla risorsa** per accedere alla nuova macchina virtuale.  Nella visualizzazione predefinita della nuova macchina virtuale seguire questa procedura per identificare gli indirizzi IP associati:

1. In **Impostazioni** selezionare **Proprietà**.

2. Prendere nota dei valori specificati per **ETICHETTA INDIRIZZO IP PUBBLICO/NOME DNS** e **INDIRIZZO IP PRIVATO** per usarli in seguito.

   ![Indirizzi IP pubblico e privato](./media/connect-on-premises-network/vm-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>Installare e configurare Bind (software DNS)

1. Usare SSH per connettersi all'__indirizzo IP pubblico__ della macchina virtuale. Sostituire `sshuser` con l'account utente SSH specificato durante la creazione della macchina virtuale. L'esempio seguente consente la connessione a una macchina virtuale all'indirizzo 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

2. Per installare Bind, usare i comandi seguenti dalla sessione SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Per configurare Bind per inoltrare le richieste di risoluzione nome per il server DNS locale, usare il testo seguente come contenuto del `/etc/bind/named.conf.options` file:

        acl goodclients {
            10.0.0.0/16; # Replace with the IP address range of the virtual network
            10.1.0.0/16; # Replace with the IP address range of the on-premises network
            localhost;
            localnets;
        };

        options {
                directory "/var/cache/bind";

                recursion yes;

                allow-query { goodclients; };

                forwarders {
                192.168.0.1; # Replace with the IP address of the on-premises DNS server
                };

                dnssec-validation auto;

                auth-nxdomain no;    # conform to RFC1035
                listen-on { any; };
        };

    > [!IMPORTANT]  
    > Sostituire i valori nella sezione `goodclients` con l'intervallo di indirizzi IP della rete virtuale e della rete locale. Questa sezione definisce gli indirizzi da cui il server DNS accetta le richieste.
    >
    > Sostituire la voce `192.168.0.1` nella sezione `forwarders` con l'indirizzo IP del server DNS locale. Questa voce indirizza le richieste DNS al server DNS locale per la risoluzione.

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

    ```output
    dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net
    ```

    Il testo `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` è il __suffisso DNS__ per la rete virtuale. Salvare questo valore, che verrà usato in un secondo momento.

5. Per configurare Bind per la risoluzione dei nomi DNS per le risorse nella rete virtuale, usare il testo seguente come contenuto del file `/etc/bind/named.conf.local`:

        // Replace the following with the DNS suffix for your virtual network
        zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
            type forward;
            forwarders {168.63.129.16;}; # The Azure recursive resolver
        };

    > [!IMPORTANT]  
    > È necessario sostituire `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` con il suffisso DNS recuperato in precedenza.

    Per modificare questo file, usare il comando seguente:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Per salvare il file, usare __CTRL+X__, __Y__ e quindi __INVIO__.

6. Per avviare Bind, usare il comando seguente:

    ```bash
    sudo service bind9 restart
    ```

7. Per verificare che Bind riesca a risolvere i nomi delle risorse nella rete locale, usare i comandi seguenti:

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]  
    > Sostituire `dns.mynetwork.net` con il nome di dominio completo (FQDN) di una risorsa nella rete locale.
    >
    > Sostituire `10.0.0.4` con l'__indirizzo IP interno__ del server DNS personalizzato nella rete virtuale.

    La risposta visualizzata sarà simile al testo seguente:

    ```output
    Server:         10.0.0.4
    Address:        10.0.0.4#53

    Non-authoritative answer:
    Name:   dns.mynetwork.net
    Address: 192.168.0.4
    ```

## <a name="configure-virtual-network-to-use-the-custom-dns-server"></a>Configurare la rete virtuale per usare il server DNS personalizzato

Per configurare la rete virtuale per usare il server DNS personalizzato invece del resolver ricorsivo di Azure, seguire questa procedura nel [portale di Azure](https://portal.azure.com):

1. Nel menu a sinistra, passare a **tutti i servizi** > **Networking** > **reti virtuali**.

2. Selezionare la rete virtuale dall'elenco, che aprirà la visualizzazione predefinita per la rete virtuale.  

3. Nella visualizzazione predefinita, in **Impostazioni**, selezionare **Server DNS**.  

4. Selezionare __Personalizzato__ e in **INDIRIZZO IP PRIVATO** immettere il valore del server DNS personalizzato.   

5. Selezionare __Salva__.  <br />  

    ![Impostare il server DNS personalizzato per la rete](./media/connect-on-premises-network/configure-custom-dns.png)

## <a name="configure-on-premises-dns-server"></a>Configurare server DNS locale

Nella sezione precedente è stato configurato il server DNS personalizzato per inoltrare le richieste al server DNS locale. È quindi necessario configurare il server DNS locale per inoltrare le richieste al server DNS personalizzato.

Per i passaggi specifici su come configurare il server DNS, vedere la documentazione per il prodotto server DNS. Cercare i passaggi su come configurare un __server d'inoltro condizionale__.

L'inoltro condizionale consente di inoltrare solo le richieste per un suffisso DNS specifico. In questo caso, è necessario configurare un server d'inoltro per il suffisso DNS della rete virtuale. Le richieste per questo suffisso devono essere inoltrate all'indirizzo IP del server DNS personalizzato. 

Il testo seguente è un esempio di una configurazione di server d'inoltro condizionale per il software DNS **Bind**:

    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
    };

Per informazioni sull'uso di DNS in **Windows Server 2016**, vedere [Add-DnsServerConditionalForwarderZone](https://technet.microsoft.com/itpro/powershell/windows/dnsserver/add-dnsserverconditionalforwarderzone).

Dopo aver configurato il server DNS locale, è possibile usare `nslookup` dalla rete locale per verificare che sia possibile risolvere i nomi nella rete virtuale. Vedere l'esempio seguente: 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

Questo esempio usa il server DNS locale in 196.168.0.4 per risolvere il nome del server DNS personalizzato. Sostituire l'indirizzo IP con uno del server DNS locale. Sostituire l'indirizzo `dnsproxy` con il nome di dominio completo del server DNS personalizzato.

## <a name="optional-control-network-traffic"></a>Facoltativo: Controllare il traffico di rete

È possibile usare gruppi di sicurezza di rete (NSG) o route definite dall'utente (UDR) per controllare il traffico di rete. Gli NSG permettono di filtrare il traffico in ingresso e in uscita e di consentire o negare il traffico. Le route definite dall'utente permettono di controllare il flusso di traffico tra le risorse nella rete virtuale, in Internet e nella rete locale.

> [!WARNING]  
> HDInsight richiede l'accesso in ingresso da indirizzi IP specifici nel cloud Azure e l'accesso in uscita senza restrizioni. Quando si usano NSG o route definite dall'utente per controllare il traffico, è necessario eseguire questa procedura:

1. Trovare gli indirizzi IP per la località contenente la rete virtuale. Per un elenco di indirizzi IP necessari in base alla località, vedere [Indirizzi IP richiesti](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip).

2. Per gli indirizzi IP identificati nel passaggio 1, consentire il traffico in ingresso da tali indirizzi.

   * Se si usano i __gruppi di sicurezza di rete__: consentire il traffico __in ingresso__ sulla porta __443__ per gli indirizzi IP.
   * Se si usano le __route definite dall'utente__: impostare il tipo __Hop successivo__ della route su __Internet__ per gli indirizzi IP.

Per un esempio di utilizzo di Azure PowerShell o dell'interfaccia della riga di comando di Azure per creare gruppi NSG, vedere il documento [Estendere le funzionalità di HDInsight usando Rete virtuale di Azure](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-nsg).

## <a name="create-the-hdinsight-cluster"></a>Creare il cluster HDInsight

> [!WARNING]  
> È necessario configurare il server DNS personalizzato prima di installare HDInsight nella rete virtuale.

Seguire i passaggi riportati in [Creare un cluster HDInsight tramite il portale di Azure](./hdinsight-hadoop-create-linux-clusters-portal.md) per creare un cluster HDInsight.

> [!WARNING]  
> * Durante la creazione del cluster, è necessario scegliere la posizione per la rete virtuale.
> * Nella sezione __Impostazioni avanzate__ della configurazione, è necessario selezionare la rete virtuale e la subnet create in precedenza.

## <a name="connecting-to-hdinsight"></a>Connessione a HDInsight

La maggior parte della documentazione in HDInsight presuppone che sia disponibile l'accesso al cluster tramite Internet. ad esempio che sia possibile connettersi al cluster all'indirizzo `https://CLUSTERNAME.azurehdinsight.net`. Questo indirizzo usa il gateway pubblico, che non è disponibile se sono stati usati gruppi di sicurezza di rete o route definite dall'utente per limitare l'accesso da Internet.

Parte della documentazione fa riferimento a `headnodehost` anche per la connessione al cluster da una sessione SSH. Questo indirizzo è disponibile solo dai nodi all'interno di un cluster e non è utilizzabile nei client connessi tramite la rete virtuale.

Per connettersi direttamente a HDInsight attraverso la rete virtuale, seguire questa procedura:

1. Per individuare i nomi di dominio completi interni dei nodi del cluster HDInsight, usare uno dei metodi seguenti:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

2. Per determinare la porta su cui un servizio è disponibile, vedere il documento [Porte usate dai servizi Apache Hadoop su HDInsight](./hdinsight-hadoop-port-settings-for-services.md).

    > [!IMPORTANT]  
    > Alcuni servizi ospitati nei nodi head sono attivi solo in un nodo alla volta. Se si prova ad accedere a un servizio in un nodo head e si verifica un errore, passare all'altro nodo head.
    >
    > Apache Ambari, ad esempio, è attivo solo in un nodo head per volta. Se si prova ad accedere ad Ambari in un nodo head e viene restituito un errore 404, significa che è in esecuzione nell'altro nodo head.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sull'uso HDInsight in una rete virtuale, vedere [Estendere le funzionalità di HDInsight usando Rete virtuale di Azure](./hdinsight-extend-hadoop-virtual-network.md).

* Per altre informazioni sulle reti virtuali di Azure, vedere [Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md).

* Per altre informazioni sui gruppi di sicurezza di rete, vedere [Gruppi di sicurezza di rete](../virtual-network/security-overview.md).

* Per altre informazioni sulle route definite dall'utente, vedere [Route definite dall'utente e inoltro IP](../virtual-network/virtual-networks-udr-overview.md).
