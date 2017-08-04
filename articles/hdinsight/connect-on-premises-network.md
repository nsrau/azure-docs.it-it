---
title: Connettere HDInsight alla rete locale - Azure HDInsight | Microsoft Docs
description: Informazioni su come creare un cluster HDInsight in una rete virtuale di Azure e quindi connetterlo alla rete locale. Informazioni su come configurare la risoluzione dei nomi tra HDInsight e la rete locale usando un server DNS personalizzato.
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/11/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: ad3549a9e8278c3364533ec2cd2321744ecda3b9
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="connect-hdinsight-to-your-on-premise-network"></a>Connettere HDInsight alla rete locale

Informazioni su come connettere HDInsight alla rete locale usando Reti virtuali di Azure e un gateway VPN. Questo documento fornisce le informazioni seguenti:

* Come creare una rete virtuale di Azure che si connette alla rete locale.

* Come abilitare la risoluzione dei nomi DNS tra la rete virtuale e la rete locale.

* Come usare i gruppi di sicurezza di rete per limitare l'accesso Internet per HDInsight.

* Come individuare le porte fornite da HDInsight nella rete virtuale.

## <a name="create-the-virtual-network-configuration"></a>Creare la configurazione della rete virtuale

Vedere i documenti seguenti per informazioni su come creare una rete virtuale di Azure connessa alla rete locale:
    
* [Uso del portale di Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

* [Uso di Azure PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

* [Uso dell'interfaccia della riga di comando di Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="configure-name-resolution"></a>Configurare la risoluzione dei nomi

Per consentire a HDInsight e alle risorse nella rete aggiunta di comunicare in base al nome, è necessario eseguire queste operazioni:

* Creare un server DNS personalizzato in Rete virtuale di Azure.

* Configurare la rete virtuale per usare il server DNS personalizzato invece del resolver ricorsivo predefinito di Azure.

* Configurare l'inoltro tra il server DNS personalizzato e il server DNS locale.

Questa configurazione consente il comportamento seguente:

* Le richieste per i nomi di dominio completi con il suffisso DNS __per la rete virtuale__ vengono inoltrate al server DNS personalizzato. Il server DNS personalizzato inoltra quindi le richieste al resolver ricorsivo di Azure, che restituisce l'indirizzo IP.

* Tutte le altre richieste vengono inoltrate al server DNS locale. Anche le richieste di risorse Internet pubbliche, ad esempio microsoft.com, vengono inoltrate al server DNS locale per la risoluzione dei nomi.

Nel diagramma seguente le linee verdi sono richieste di risorse che terminano nel suffisso DNS della rete virtuale. Le linee blu sono richieste di risorse nella rete locale o nella rete Internet pubblica.

![Diagramma della modalità di risoluzione delle richieste DNS nella configurazione usata in questo documento](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

### <a name="create-a-custom-dns-server"></a>Creare un server DNS personalizzato

> [!IMPORTANT]
> È necessario creare e configurare il server DNS prima di installare HDInsight nella rete virtuale.

Per creare una VM Linux che usa il software DNS [Bind](https://www.isc.org/downloads/bind/), eseguire questa procedura:

> [!NOTE]
> Nella procedura seguente viene usato il [portale di Azure](https://portal.azure.com) per creare una macchina virtuale di Azure. Per altri modi per creare una macchina virtuale, vedere i documenti [Creare una VM: interfaccia della riga di comando di Azure](../virtual-machines/linux/quick-create-cli.md) e [Creare una VM: Azure PowerShell](../virtual-machines/linux/quick-create-portal.md).

1. Dal [portale di Azure](https://portal.azure.com) selezionare __+__, __Calcolo__ e __Ubuntu Server 16.04 LTS__.

    ![Creare una macchina virtuale Ubuntu](./media/connect-on-premises-network/create-ubuntu-vm.png)

2. Nel pannello __Informazioni di base__ immettere le informazioni seguenti:

    * __Nome__: nome descrittivo che identifica questa macchina virtuale. Ad esempio __DNSProxy__.
    * __Nome utente__: nome dell'account SSH.
    * __Chiave pubblica SSH__ o __Password__: metodo di autenticazione per l'account SSH. Si consiglia di usare le chiavi pubbliche, che sono più sicure. Per altre informazioni, vedere il documento [Creare e usare chiavi SSH per VM Linux](../virtual-machines/linux/mac-create-ssh-keys.md).
    * __Gruppo di risorse__: selezionare __Usa esistente__ e quindi selezionare il gruppo di risorse contenente la rete virtuale creata in precedenza.
    * __Località__: selezionare la stessa località della rete virtuale.

    ![Configurazione di base della macchina virtuale](./media/connect-on-premises-network/vm-basics.png)

    Lasciare i valori predefiniti per le altre voci e quindi selezionare __OK__.

3. Nel pannello __Scegli una dimensione__ selezionare la dimensione della VM. Per questa esercitazione selezionare l'opzione più piccola e a più basso costo. Per continuare, usare il pulsante __Seleziona__.

4. Nel pannello __Impostazioni__ immettere le informazioni seguenti:

    * __Rete virtuale__: selezionare la rete virtuale creata in precedenza.

    * __Subnet__: selezionare la subnet predefinita per la rete virtuale. __Non__ selezionare la subnet usata dal gateway VPN.

    * __Account di archiviazione di diagnostica__: selezionare un account di archiviazione esistente o crearne uno nuovo.

    ![Impostazioni della rete virtuale](./media/connect-on-premises-network/virtual-network-settings.png)

    Lasciare i valori predefiniti per le altre voci e quindi selezionare __OK__ per continuare.

5. Nel pannello __Acquisto__ fare clic sul pulsante __Acquisto__ per creare la macchina virtuale.

6. Dopo che la macchina virtuale è stata creata, viene visualizzato il relativo pannello __Panoramica__. Nell'elenco a sinistra selezionare __Proprietà__. Salvare i valori di __Indirizzo IP pubblico__ e __Indirizzo IP privato__. Verranno usati nella sezione successiva.

    ![Indirizzi IP pubblico e privato](./media/connect-on-premises-network/vm-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>Installare e configurare Bind (software DNS)

1. Usare SSH per connettersi all'__indirizzo IP pubblico__ della macchina virtuale. L'esempio seguente consente la connessione a una macchina virtuale all'indirizzo 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Sostituire `sshuser` con l'account utente SSH specificato durante la creazione del cluster.

    > [!NOTE]
    > È possibile ottenere l'utilità `ssh` in diversi modi. In Linux, Unix e macOS viene in genere fornita come parte del sistema operativo. Se si usa Windows, prendere in considerazione una delle opzioni seguenti:
    >
    > * [Azure Cloud Shell](../cloud-shell/quickstart.md)
    > * [Bash in Ubuntu su Windows 10](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git (https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Per installare Bind, usare i comandi seguenti dalla sessione SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Per configurare Bind per l'inoltro delle richieste di risoluzione dei nomi al server DNS locale, usare il testo seguente come contenuto del file `/etc/bind/named.conf.options`:

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

        dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

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

        Server:         10.0.0.4
        Address:        10.0.0.4#53

        Non-authoritative answer:
        Name:   dns.mynetwork.net
        Address: 192.168.0.4

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Configurare la rete virtuale per usare il server DNS personalizzato

Per configurare la rete virtuale per usare il server DNS personalizzato invece del resolver ricorsivo di Azure, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com) selezionare la rete virtuale e quindi selezionare __Server DNS__.

2. Selezionare __Personalizzato__ e immettere l'__indirizzo IP interno__ del server DNS personalizzato. Infine, selezionare __Salva__.

    ![Impostare il server DNS personalizzato per la rete](./media/connect-on-premises-network/configure-custom-dns.png)

### <a name="configure-the-on-premises-dns-server"></a>Configurare il server DNS locale

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
>
> 1. Trovare gli indirizzi IP per la località contenente la rete virtuale. Per un elenco di indirizzi IP necessari in base alla località, vedere [Indirizzi IP richiesti](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip).
>
> 2. Consentire il traffico in ingresso dagli indirizzi IP.
>
>    * __NSG__: consentire il traffico __in ingresso__ sulla porta __443__ da __Internet__.
>    * __Route definita dall'utente__: impostare il tipo di __Hop successivo__ della route su __Internet__.

Per un esempio di utilizzo di Azure PowerShell o dell'interfaccia della riga di comando di Azure per creare gruppi NSG, vedere il documento [Estendere le funzionalità di HDInsight usando Rete virtuale di Azure](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-nsg).

## <a name="create-the-hdinsight-cluster"></a>Creare il cluster HDInsight

> [!WARNING]
> È necessario configurare il server DNS personalizzato prima di installare HDInsight nella rete virtuale.

Seguire i passaggi riportati in [Creare un cluster HDInsight tramite il portale di Azure](./hdinsight-hadoop-create-linux-clusters-portal.md) per creare un cluster HDInsight.

> [!WARNING]
> * Durante la creazione del cluster, è necessario scegliere la posizione per la rete virtuale.
>
> * Nella sezione __Impostazioni avanzate__ della configurazione, è necessario selezionare la rete virtuale e la subnet create in precedenza.

## <a name="connecting-to-hdinsight"></a>Connessione a HDInsight

La maggior parte delle documentazione in HDInsight presuppone che sia disponibile l'accesso al cluster tramite internet. Verificare ad esempio che sia possibile connettersi al cluster all'indirizzo https://CLUSTERNAME.azurehdinsight.net. Questo indirizzo usa il gateway pubblico, che non è disponibile se sono stati usati NSG o route definite dall'utente per limitare l'accesso da Internet.

Per connettersi direttamente a HDInsight attraverso la rete virtuale, seguire questa procedura:

1. Per individuare i nomi di dominio completi interni dei nodi del cluster HDInsight, usare uno dei metodi seguenti:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

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

2. Per determinare la porta su cui un servizio è disponibile, vedere il documento [Porte usate dai servizi Hadoop su HDInsight](./hdinsight-hadoop-port-settings-for-services.md).

    > [!IMPORTANT]
    > Alcuni servizi ospitati nei nodi head sono attivi solo in un nodo per volta. Se si prova ad accedere a un servizio in un nodo head e si verifica un errore, passare all'altro nodo head.
    >
    > Ambari, ad esempio, è attivo solo in un nodo head per volta. Se si prova ad accedere ad Ambari in un nodo head e viene restituito un errore 404, significa che è in esecuzione nell'altro nodo head.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sull'uso HDInsight in una rete virtuale, vedere [Estendere le funzionalità di HDInsight usando Rete virtuale di Azure](./hdinsight-extend-hadoop-virtual-network.md).

* Per altre informazioni sulle reti virtuali di Azure, vedere [Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md).

* Per altre informazioni sui gruppi di sicurezza di rete, vedere [Gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md).

* Per altre informazioni sulle route definite dall'utente, vedere [Route definite dall'utente e inoltro IP](../virtual-network/virtual-networks-udr-overview.md).
