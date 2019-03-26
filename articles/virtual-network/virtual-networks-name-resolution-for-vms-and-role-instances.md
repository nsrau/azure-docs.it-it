---
title: Risoluzione dei nomi per le risorse in reti virtuali di Azure
titlesuffix: Azure Virtual Network
description: Scenari di risoluzione dei nomi per Azure IaaS, soluzioni ibride, tra diversi servizi cloud, Active Directory e usando il proprio server DNS.
services: virtual-network
documentationcenter: na
author: subsarma
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/25/2019
ms.author: subsarma
ms.openlocfilehash: ea15468722fcf1b9e2649236ef4dd05549d8f460
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418738"
---
# <a name="name-resolution-for-resources-in-azure-virtual-networks"></a>Risoluzione dei nomi per le risorse in reti virtuali di Azure

A seconda di come si usa Azure per ospitare soluzioni IaaS, PaaS e ibride, potrebbe essere necessario consentire alle macchine virtuali e ad altre risorse implementate in una rete virtuale di comunicare tra loro. Anche se è possibile abilitare la comunicazione tramite indirizzi IP, è molto più semplice usare nomi che possono essere facilmente ricordati e che non cambiano. 

Quando le risorse implementate nelle reti virtuali devono risolvere nomi di dominio trasformandoli in indirizzi IP interni, possono usare uno di questi due metodi:

* [Risoluzione dei nomi fornita da Azure](#azure-provided-name-resolution)
* [Risoluzione dei nomi con l'uso del proprio server DNS](#name-resolution-that-uses-your-own-dns-server) (che potrebbe inoltrare query ai server DNS forniti da Azure)

Il tipo di risoluzione dei nomi usato dipende dal modo in cui le risorse devono comunicare tra loro. Nella tabella seguente vengono illustrate diverse ipotesi e le corrispondenti soluzioni di risoluzione dei nomi:

> [!NOTE]
> A seconda dello scenario, può essere opportuno usare la funzionalità Zone private di DNS di Azure, attualmente in anteprima pubblica. Per altre informazioni, vedere [Uso di DNS di Azure per i domini privati](../dns/private-dns-overview.md).
>

| **Scenario** | **Soluzione** | **Suffisso** |
| --- | --- | --- |
| Risoluzione dei nomi tra macchine virtuali situate nella stessa rete virtuale o tra istanze di ruolo di Servizi cloud di Microsoft Azure nello stesso servizio cloud. | [Zone private di DNS di Azure](../dns/private-dns-overview.md) o [risoluzione dei nomi fornita da Azure](#azure-provided-name-resolution) |Nome host o nome di dominio completo |
| Risoluzione dei nomi tra macchine virtuali in diverse reti virtuali o istanze del ruolo in servizi cloud diversi. |[Zone private di DNS di Azure](../dns/private-dns-overview.md) o server DNS gestiti dal cliente che inoltrano query tra reti virtuali per la risoluzione da parte di Azure (proxy DNS). Vedere [Risoluzione dei nomi usando il server DNS](#name-resolution-that-uses-your-own-dns-server). |Solo nome di dominio completo |
| Risoluzione dei nomi da un servizio app di Azure (App Web, Funzioni o Bot) con l'integrazione della rete virtuale in istanze del ruolo o macchine virtuali nella stessa rete virtuale. |Server DNS gestiti dal cliente che inoltrano query tra reti virtuali per la risoluzione da parte di Azure (proxy DNS). Vedere [Risoluzione dei nomi usando il server DNS](#name-resolution-that-uses-your-own-dns-server). |Solo nome di dominio completo |
| Risoluzione dei nomi da app Web del servizio app a macchine virtuali nella stessa rete virtuale. |Server DNS gestiti dal cliente che inoltrano query tra reti virtuali per la risoluzione da parte di Azure (proxy DNS). Vedere [Risoluzione dei nomi usando il server DNS](#name-resolution-that-uses-your-own-dns-server). |Solo nome di dominio completo |
| Risoluzione dei nomi da app Web del servizio app in una rete virtuale a macchine virtuali in una rete virtuale diversa. |Server DNS gestiti dal cliente che inoltrano query tra reti virtuali per la risoluzione da parte di Azure (proxy DNS). Vedere Risoluzione dei nomi usando il server DNS. |Solo nome di dominio completo |
| Risoluzione dei nomi di servizi e computer locali da istanze del ruolo o macchine virtuali in Azure. |Server DNS gestiti dal cliente (ad esempio, controller di dominio locale, controller di dominio di sola lettura locale o server DNS secondario sincronizzati con trasferimenti di zona). Vedere [Risoluzione dei nomi usando il server DNS](#name-resolution-that-uses-your-own-dns-server). |Solo nome di dominio completo |
| Risoluzione di nomi host di Azure da computer locali. |Inoltra le query a un server proxy DNS gestito dal cliente nella rete virtuale corrispondente. Il server proxy trasferisce le query ad Azure per la risoluzione. Vedere [Risoluzione dei nomi usando il server DNS](#name-resolution-that-uses-your-own-dns-server). |Solo nome di dominio completo |
| DNS inversi per indirizzi IP interni. |[Risoluzione dei nomi usando il server DNS](#name-resolution-that-uses-your-own-dns-server). |Non applicabile |
| Risoluzione dei nomi tra macchine virtuali o istanze del ruolo situate in servizi cloud diversi e non in una rete virtuale. |Non applicabile Connettività tra macchine virtuali e istanze del ruolo in servizi cloud diversi non è supportata esternamente a una rete virtuale. |Non applicabile|

## <a name="azure-provided-name-resolution"></a>Risoluzione dei nomi fornita da Azure

Oltre alla risoluzione dei nomi DNS pubblici, Azure offre la risoluzione dei nomi interni per VM e istanze del ruolo che si trovano all'interno della stessa rete virtuale o dello stesso servizio cloud. Le macchine virtuali e le istanze di un servizio cloud condividono lo stesso suffisso DNS, pertanto è sufficiente il solo nome host. Nelle reti virtuali implementate tramite il modello di distribuzione classica, invece, servizi cloud diversi hanno suffissi DNS diversi. In questo caso, è necessario il nome di dominio completo per la risoluzione dei nomi tra servizi cloud diversi. Nelle reti virtuali implementate in base al modello di distribuzione Azure Resource Manager il suffisso DNS è coerente in tutta la rete, pertanto il nome FQDN completo non è necessario. I nomi DNS possono essere assegnati sia alle macchine virtuali che alle interfacce di rete. Sebbene la risoluzione dei nomi fornita da Azure non richieda alcuna configurazione, non è la scelta più appropriata per tutti gli scenari di distribuzione, come illustrato nel dettaglio nella tabella precedente.

> [!NOTE]
> In caso di uso di ruoli Web e di lavoro basati su servizi cloud, è possibile accedere anche gli indirizzi IP interni delle istanze del ruolo con l'API REST di gestione del servizio Azure. Per altre informazioni, vedere [Riferimento all'API REST di gestione dei servizi](https://msdn.microsoft.com/library/azure/ee460799.aspx). L'indirizzo si basa sul nome del ruolo e sul numero di istanza. 
>
>

### <a name="features"></a>Funzionalità

La risoluzione dei nomi fornita da Azure presenta le caratteristiche seguenti:
* Semplicità d'uso. Non è necessaria alcuna configurazione.
* Disponibilità elevata. Non è necessario creare e gestire i cluster dei server DNS propri.
* Possibilità di usare il servizio in combinazione con i propri server DNS per risolvere i nomi host locali e quelli di Azure.
* Possibilità di usare la risoluzione dei nomi tra istanze del ruolo e macchine virtuali presenti nello stesso servizio cloud, senza la necessità di un nome di dominio completo.
* Possibilità di usare la risoluzione dei nomi tra macchine virtuali presenti in reti virtuali che usano il modello di distribuzione Azure Resource Manager, senza la necessità di un nome di dominio completo. Le reti virtuali nel modello di distribuzione classica richiedono il nome di dominio completo per la risoluzione dei nomi in servizi cloud diversi. 
* Possibilità di usare nomi host che descrivano le distribuzioni nel modo più adeguato, anziché usare nomi generati automaticamente.

### <a name="considerations"></a>Considerazioni

Ecco gli aspetti da prendere in considerazione quando si usa la risoluzione dei nomi fornita da Azure:
* Il suffisso DNS creato da Azure non può essere modificato.
* Non è possibile registrare manualmente i propri record.
* WINS e NetBIOS non sono supportati. Non è possibile visualizzare le macchine virtuali in Esplora risorse.
* I nomi host devono essere compatibili con DNS. I nomi devono includere solo i numeri 0-9, le lettere a-z e il segno - e non possono iniziare o terminare con il segno -.
* Il traffico di query DNS è limitato per ogni VM. La limitazione in genere non comporta alcun impatto sulla maggior parte delle applicazioni. Se viene osservata la limitazione delle richieste, assicurarsi che la memorizzazione nella cache sul lato client sia abilitata. Per altre informazioni, vedere [Configurazione del client DNS](#dns-client-configuration).
* Solo le VM nei primi 180 servizi cloud sono registrate per ogni rete virtuale in un modello di distribuzione classica. Questo limite non si applica alle reti virtuali in Azure Resource Manager.
* L'indirizzo IP di DNS di Azure è 168.63.129.16. Questo è un indirizzo IP statico e non verrà modificato.

## <a name="dns-client-configuration"></a>Configurazione del client DNS

Questa sezione descrive la memorizzazione nella cache sul lato client e la ripetizione di tentativi sul lato client.

### <a name="client-side-caching"></a>Memorizzazione nella cache sul lato client

Non tutte le query DNS devono essere inviate attraverso la rete. La memorizzazione nella cache sul lato client consente di ridurre la latenza e migliorare la resilienza ai blip (brevi interruzioni) di rete tramite la risoluzione di query DNS ricorrenti da una cache locale. I record DNS includono un meccanismo di durata (TTL) che consente alla cache di memorizzare il record per il periodo di tempo più lungo possibile senza che questo influisca sullo stato di aggiornamento del record. Di conseguenza, la memorizzazione nella cache sul lato client è ideale nella maggior parte dei casi.

Il client DNS di Windows predefinito contiene una cache DNS predefinita. Alcune distribuzioni Linux non includono la memorizzazione nella cache per impostazione predefinita. Se si verifica che non è presente una cache locale, aggiungere una cache DNS a ogni macchina virtuale Linux.

Sono disponibili alcuni pacchetti di memorizzazione nella cache DNS diversi, tra cui dnsmasq. Ecco la procedura per installare dnsmasq nelle distribuzioni più comuni:

* **Ubuntu (usa resolvconf)**:
  * Installare il pacchetto dnsmasq con `sudo apt-get install dnsmasq`.
* **SUSE (usa netconf)**:
  * Installare il pacchetto dnsmasq con `sudo zypper install dnsmasq`.
  * Abilitare il servizio dnsmasq con `systemctl enable dnsmasq.service`. 
  * Avviare il servizio dnsmasq con `systemctl start dnsmasq.service`. 
  * Modificare **/etc/sysconfig/network/config** e sostituire *NETCONFIG_DNS_FORWARDER=""* con *dnsmasq*.
  * Aggiornare resolv.conf con `netconfig update` per impostare la cache come resolver DNS locale.
* **OpenLogic (usa NetworkManager)**:
  * Installare il pacchetto dnsmasq con `sudo yum install dnsmasq`.
  * Abilitare il servizio dnsmasq con `systemctl enable dnsmasq.service`.
  * Avviare il servizio dnsmasq con `systemctl start dnsmasq.service`.
  * Aggiungere *prepend domain-name-servers 127.0.0.1;* a **/etc/dhclient-eth0.conf**.
  * Riavviare il servizio di rete con `service network restart` per impostare la cache come resolver DNS locale.

> [!NOTE]
> Il pacchetto dnsmasq è solo una delle numerose cache DNS disponibili per Linux. Prima di usarlo, assicurarsi che sia adatto alle esigenze specifiche e verificare che non siano installate altre cache.
>
>
    
### <a name="client-side-retries"></a>Ripetizione di tentativi sul lato client

DNS è principalmente un protocollo UDP. Poiché UDP non garantisce il recapito dei messaggi, la logica di ripetizione dei tentativi viene gestita nel protocollo DNS stesso. Ogni client DNS (sistema operativo) può includere una logica di ripetizione dei tentativi diversa, in base alle preferenze dell'autore:

* I sistemi operativi Windows eseguono un nuovo tentativo dopo 1 secondo e quindi ne eseguono un altro dopo 2, 4 e altri 4 secondi. 
* La configurazione di Linux predefinita esegue il tentativo dopo 5 secondi. È consigliabile aumentare il numero di ripetizione dei tentativi a 5, a intervalli di 1 secondo.

Controllare le impostazioni correnti in una VM Linux con `cat /etc/resolv.conf`. Osservare ad esempio la riga *options*:

```bash
options timeout:1 attempts:5
```

Il file resolv.conf viene di solito generato automaticamente e non deve essere modificato. La procedura specifica per aggiungere la riga *options* varia a seconda della distribuzione:

* **Ubuntu** (usa resolvconf):
  1. Aggiungere la riga *options* a **/etc/resolvconf/resolv.conf.d/tail**.
  2. Eseguire `resolvconf -u` per aggiornare.
* **SUSE** (usa netconf):
  1. Aggiungere *timeout:1 attempts:5* al parametro **NETCONFIG_DNS_RESOLVER_OPTIONS=""** in **/etc/sysconfig/network/config**.
  2. Eseguire `netconfig update` per aggiornare.
* **OpenLogic** (usa NetworkManager):
  1. Aggiungere *echo "options timeout:1 attempts:5"* a **/etc/NetworkManager/dispatcher.d/11-dhclient**.
  2. Aggiornare con `service network restart`.

## <a name="name-resolution-that-uses-your-own-dns-server"></a>Risoluzione dei nomi con l'uso del proprio server DNS

Questa sezione descrive le macchine virtuali, le istanze del ruolo e le app Web.

### <a name="vms-and-role-instances"></a>Istanze del ruolo e delle VM

Le funzionalità offerte da Azure possono non essere sufficienti a soddisfare le esigenze di risoluzione dei nomi di un utente. Ad esempio, potrebbe essere necessario utilizzare i domini di Active Directory in Microsoft Windows Server, risolvere i nomi DNS tra reti virtuali. Per questi scenari specifici, Azure offre la possibilità di usare i propri server DNS.

I server DNS all'interno di una rete virtuale possono inoltrare query DNS ai resolver ricorsivi di Azure. In questo modo è possibile risolvere i nomi host all'interno di tale rete virtuale. Ad esempio, un controller di dominio eseguito in Azure può rispondere a query DNS relative ai propri domini e inoltrare tutte le altre query ad Azure. L'inoltro delle query consente alle macchine virtuali di visualizzare sia le risorse locali, tramite il controller di dominio, sia i nomi host forniti da Azure, tramite il server di inoltro. L'accesso ai resolver ricorsivi di Azure viene fornito tramite l'indirizzo IP virtuale 168.63.129.16.

L'inoltro DNS consente anche la risoluzione DNS tra reti virtuali e permette ai computer locali di risolvere i nomi host forniti da Azure. Per risolvere il nome host di una macchina virtuale, la macchina virtuale del server DNS deve trovarsi nella stessa rete virtuale ed essere configurata per l'inoltro di query relative ai nomi host ad Azure. Poiché il suffisso DNS è diverso in ogni rete virtuale, è possibile usare le regole di inoltro condizionale per inviare le query DNS alla rete virtuale corretta per la risoluzione. L'immagine seguente illustra due reti virtuali e una rete locale che gestiscono la risoluzione DNS tra reti virtuali con il metodo descritto. Un esempio di server di inoltro DNS è disponibile nella [raccolta di modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) e su [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

> [!NOTE]
> Un'istanza del ruolo può eseguire la risoluzione dei nomi di macchine virtuali all'interno della stessa rete virtuale. A tale scopo, usa il nome di dominio completo, costituito dal nome host della macchina virtuale e dal suffisso DNS **internal.cloudapp.net**. Tuttavia, in questo caso, la risoluzione dei nomi ha esito positivo solo se per l'istanza del ruolo il nome della VM è definito nello [schema Role (file con estensione cscfg)](https://msdn.microsoft.com/library/azure/jj156212.aspx).
> <Role name="<role-name>" vmName="<vm-name>">
>
> Le istanze del ruolo che devono eseguire la risoluzione dei nomi delle macchine virtuali in un'altra rete virtuale (nome di dominio completo con il suffisso **internal.cloudapp.net**) devono usare il metodo descritto in questa sezione (server DNS personalizzati per l'inoltro tra le due reti virtuali).
>

![Figura della risoluzione DNS tra reti virtuali](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Quando si usa la risoluzione dei nomi fornita da Azure, il protocollo DHCP (Dynamic Host Configuration Protocol) di Azure assegna un suffisso DNS interno (**.internal.cloudapp.net**) a ogni macchina virtuale. Questo suffisso consente la risoluzione dei nomi host perché i record dei nomi host si trovano nell'area **internal.cloudapp.net**. Quando si usa la soluzione di risoluzione dei nomi personalizzata, questo suffisso non viene fornito alle macchine virtuali perché interferisce con altre architetture DNS, ad esempio gli scenari con aggiunta al dominio. Azure assegna invece un segnaposto non funzionante (*reddog.microsoft.com*).

Se necessario, è possibile determinare il suffisso DNS interno usando PowerShell o l'API:

* Per le reti virtuali nei modelli di distribuzione Azure Resource Manager, il suffisso è disponibile tramite il [interfaccia di rete l'API REST](https://docs.microsoft.com/rest/api/virtualnetwork/networkinterfaces), il [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) cmdlet di PowerShell e il [ presentazione di AZ network nic](/cli/azure/network/nic#az-network-nic-show) comando di Azure.
* Nei modelli di distribuzione classica il suffisso è disponibile tramite la chiamata all'[API Get Deployment](https://msdn.microsoft.com/library/azure/ee460804.aspx) o il cmdlet [Get-AzureVM -Debug](/powershell/module/servicemanagement/azure/get-azurevm).

Se l'inoltro delle query ad Azure non soddisfa le esigenze specifiche, sarà necessario offrire una soluzione DNS personalizzata. La soluzione DNS deve:

* Offrire una soluzione di risoluzione dei nomi host appropriata, ad esempio con [DNS dinamico](virtual-networks-name-resolution-ddns.md). Usando il DNS dinamico potrebbe essere necessario disabilitare lo scavenging dei record DNS. Le lease DHCP di Azure sono lunghe e lo scavenging potrebbe rimuovere i record DNS in modo anomalo. 
* Fornire una soluzione di risoluzione ricorsiva appropriata per consentire la risoluzione dei nomi di dominio esterni.
* Essere accessibile (tramite TCP e UDP sulla porta 53) dai client che gestisce ed essere in grado di accedere a Internet.
* Essere protetta dagli accessi provenienti da Internet per attenuare i rischi rappresentati da agenti esterni.

> [!NOTE]
> Per ottenere prestazioni ottimali, quando si usano le macchine virtuali di Azure come server DNS è necessario disabilitare IPv6. È consigliabile assegnare un [indirizzo IP pubblico](virtual-network-public-ip-address.md) a ogni macchina virtuale del server DNS. Per altre analisi delle prestazioni e ottimizzazioni quando si usa Windows Server come proprio server DNS, vedere [Name resolution performance of a recursive Windows DNS Server 2012 R2](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx) (Prestazioni di risoluzione dei nomi di un server DNS Windows 2012 R2).
> 
> 

### <a name="web-apps"></a>App Web
Si supponga di dover eseguire la risoluzione dei nomi dall'app Web creata tramite il servizio app, collegato a una rete virtuale, nelle macchine virtuali presenti nella stessa rete virtuale. Oltre a configurare un server DNS personalizzato dotato di un server di inoltro di query ad Azure (IP virtuale 168.63.129.16), eseguire la procedura seguente:
1. Se non è ancora stato fatto, abilitare l'integrazione della rete virtuale per l'app Web, come descritto in [Integrare un'app in una rete virtuale](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Nel portale di Azure selezionare **Sincronizza rete** in **Rete**, **Virtual Network Integration** per il piano di servizio app che ospita l'app Web.

    ![Screenshot della risoluzione dei nomi delle reti virtuali](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Se è necessario eseguire la risoluzione dei nomi dall'app Web realizzata tramite il servizio app, collegata a una rete virtuale, alle macchine virtuali in un'altra rete virtuale, è necessario utilizzare i server DNS personalizzati in entrambe le reti virtuali, come indicato di seguito:

* Configurare un server DNS nella rete virtuale di destinazione, in una macchina virtuale che può anche inoltrare le query al resolver ricorsivo di Azure (IP virtuale 168.63.129.16). Un esempio di server di inoltro DNS è disponibile nella [raccolta di modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) e su [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder). 
* Configurare un server d'inoltro DNS nella rete virtuale di origine in una VM. Configurare questo server d'inoltro DNS per inoltrare le query al server DNS nella rete virtuale di destinazione.
* Configurare il server DNS di origine nelle impostazioni della rete virtuale di origine.
* Abilitare l'integrazione della rete virtuale per consentire all'app Web di collegarsi alla rete virtuale di origine, seguendo le istruzioni in [Integrare un'app in una rete virtuale](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Nel portale di Azure selezionare **Sincronizza rete** in **Rete**, **Virtual Network Integration** per il piano di servizio app che ospita l'app Web.

## <a name="specify-dns-servers"></a>Specificare i server DNS
Quando si usano i propri server DNS, Azure offre la possibilità di specificare più server DNS per ogni rete virtuale. È anche possibile specificare più server DNS per ogni interfaccia di rete (per Azure Resource Manager) o per ogni servizio cloud (per il modello di distribuzione classica). I server DNS specificati per un'interfaccia di rete o un servizio cloud hanno la precedenza su quelli specificati per la rete virtuale.

> [!NOTE]
> Le proprietà della connessione di rete, ad esempio gli IP del server DNS, non devono essere modificate direttamente nelle macchine virtuali Windows. Potrebbero infatti essere cancellate durante la correzione del servizio, quando la scheda di rete virtuale viene sostituita.
>
>

Quando si usa il modello di distribuzione Azure Resource Manager, è possibile specificare i server DNS per una rete virtuale e un’interfaccia di rete. Per informazioni dettagliate, vedere [Gestire una rete virtuale](manage-virtual-network.md) e [Gestire un'interfaccia di rete](virtual-network-network-interface.md).

> [!NOTE]
> Se si opta per il server DNS personalizzato per la rete virtuale, è necessario specificare almeno un indirizzo IP del server DNS. In caso contrario, la rete virtuale ignorerà la configurazione e userà invece il server DNS fornito da Azure.
>
>

Quando si usa il modello di distribuzione classica, è possibile specificare i server DNS per la rete virtuale nel portale di Azure o nel [file di configurazione della rete](https://msdn.microsoft.com/library/azure/jj157100). Per i servizi cloud, è possibile specificare i server DNS nel [file di configurazione del servizio](https://msdn.microsoft.com/library/azure/ee758710) o tramite PowerShell con [New-AzureVM](/powershell/module/servicemanagement/azure/new-azurevm).

> [!NOTE]
> Se si modificano le impostazioni DNS di una rete virtuale o una macchina virtuale già distribuita, è necessario riavviare ogni macchina virtuale interessata affinché le modifiche vengano applicate.
>
>

## <a name="next-steps"></a>Passaggi successivi

Modello di distribuzione Azure Resource Manager:

* [Gestire una rete virtuale](manage-virtual-network.md)
* [Gestire un'interfaccia di rete](virtual-network-network-interface.md)

Modello di distribuzione classica:

* [Schema di configurazione dei servizi di Azure](https://msdn.microsoft.com/library/azure/ee758710)
* [Schema di configurazione di Rete virtuale](https://msdn.microsoft.com/library/azure/jj157100)
* [Configurare una rete virtuale usando un file di configurazione di rete](virtual-networks-using-network-configuration-file.md)