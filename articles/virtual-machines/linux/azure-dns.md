---
title: Opzioni di risoluzione dei nomi DNS per macchine virtuali Linux in Azure
description: "Scenari di risoluzione dei nomi per macchine virtuali Linux in IaaS di Azure, inclusi i servizi DNS forniti, DNS esterno ibrido e possibilità di usare il proprio server DNS."
services: virtual-machines
documentationcenter: na
author: RicksterCDN
manager: timlt
editor: tysonn
ms.assetid: 787a1e04-cebf-4122-a1b4-1fcf0a2bbf5f
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2016
ms.author: rclaus
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: cc06ee9305b4d3034154a0825c1aea53fe446f80
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017

---
# <a name="dns-name-resolution-options-for-linux-virtual-machines-in-azure"></a>Opzioni di risoluzione dei nomi DNS per macchine virtuali Linux in Azure
Per impostazione predefinita, Azure fornisce la risoluzione dei nomi DNS per tutte le macchine virtuali contenute in una singola rete virtuale. È possibile implementare la soluzione di risoluzione dei nomi DNS configurando i servizi DNS nelle macchine virtuali ospitate da Azure. Gli scenari seguenti consentono di scegliere quello più adatto alla situazione specifica.

* [Risoluzione dei nomi forniti da Azure](#azure-provided-name-resolution)
* [Risoluzione dei nomi usando il server DNS](#name-resolution-using-your-own-dns-server)

Il tipo di risoluzione dei nomi usato dipende dal modo in cui macchine virtuali e istanze del ruolo devono comunicare.

Nella tabella seguente vengono illustrate diverse ipotesi e le corrispondenti soluzioni di risoluzione dei nomi:

| **Scenario** | **Soluzione** | **Suffisso** |
| --- | --- | --- |
| Risoluzione dei nomi tra istanze del ruolo o macchine virtuali presenti nella stessa rete virtuale |[Risoluzione dei nomi forniti da Azure](#azure-provided-name-resolution) |nome host o nome di dominio completo (FQDN) |
| Risoluzione dei nomi tra istanze del ruolo o macchine virtuali presenti in reti virtuali diverse |Server DNS gestiti dal cliente che inoltrano query tra reti virtuali per la risoluzione da parte di Azure (proxy DNS). Vedere [Risoluzione dei nomi usando il server DNS](#name-resolution-using-your-own-dns-server). |Solo nome di dominio completo |
| Risoluzione dei nomi servizi e computer locali da istanze del ruolo o macchine virtuali in Azure |Server DNS gestiti dal cliente, ad esempio controller di dominio locale, controller di dominio di sola lettura locale o server DNS secondario sincronizzati tramite trasferimenti di zona. Vedere [Risoluzione dei nomi usando il server DNS](#name-resolution-using-your-own-dns-server). |Solo nome di dominio completo |
| Risoluzione di nomi host di Azure da computer locali |Inoltro delle query a un server proxy DNS gestito dal cliente nella rete virtuale corrispondente. Il server proxy inoltra le richieste ad Azure per la risoluzione. Vedere [Risoluzione dei nomi usando il server DNS](#name-resolution-using-your-own-dns-server). |Solo nome di dominio completo |
| DNS inversi per indirizzi IP interni |[Risoluzione dei nomi usando il server DNS](#name-resolution-using-your-own-dns-server) |N/D |

## <a name="name-resolution-that-azure-provides"></a>Risoluzione dei nomi forniti da Azure
Oltre alla risoluzione dei nomi DNS pubblici, Azure offre la risoluzione dei nomi interni per macchine virtuali e istanze del ruolo che si trovano all'interno della stessa rete virtuale. In reti virtuali basate su Azure Resource Manager, il suffisso DNS è coerente attraverso la rete virtuale; il nome FQDN non è necessario. I nomi DNS possono essere assegnati sia a schede di interfaccia di rete (NIC), sia a macchine virtuali. Sebbene la risoluzione dei nomi fornita da Azure non richieda alcuna configurazione, non è la scelta più appropriata per tutti gli scenari di distribuzione, come illustrato nella tabella precedente.

### <a name="features-and-considerations"></a>Funzionalità e considerazioni
**Funzionalità**

* Non è necessaria alcuna configurazione per usare la risoluzione dei nomi fornita da Azure.
* Il servizio di risoluzione dei nomi fornito da Azure ha un'elevata disponibilità. Non è necessario creare e gestire i cluster dei server DNS propri.
* Il servizio di risoluzione dei nomi fornito da Azure può essere usato insieme ai propri server DNS per risolvere i nomi host locali e i nomi host di Azure.
* Viene fornita la risoluzione dei nomi tra macchine virtuali in reti virtuali, senza necessità del nome FQDN.
* È possibile usare nomi host personalizzati in grado di descrivere in modo più adeguato le distribuzioni, anziché usare nomi generati automaticamente.

**Considerazioni:**

* Il suffisso DNS creato da Azure non può essere modificato.
* Non è possibile registrare manualmente i propri record.
* WINS e NetBIOS non sono supportati.
* I nomi host devono essere compatibili con DNS.
    I nomi devono usare solo 0-9, a-z e "-" e non possono iniziare o terminare con un "-". Vedere RFC 3696 Sezione 2.
* Il traffico di query DNS è limitato per ogni macchina virtuale. La limitazione in genere non comporta alcun impatto sulla maggior parte delle applicazioni.  Se viene osservata la limitazione delle richieste, assicurarsi che la memorizzazione nella cache sul lato client sia abilitata.  Per altre informazioni, vedere [Come usare al meglio la risoluzione dei nomi fornita da Azure](#getting-the-most-from-name-resolution-that-azure-provides).

### <a name="getting-the-most-from-name-resolution-that-azure-provides"></a>Come usare al meglio la risoluzione dei nomi fornita da Azure
**Memorizzazione nella cache sul lato client:**

Alcune query DNS non vengono inviate attraverso la rete. La memorizzazione nella cache sul lato client consente di ridurre la latenza e migliorare la resilienza alle incoerenze di rete, tramite la risoluzione di query DNS ricorrenti da una cache locale. I record DNS contengono una durata (TTL) che consente alla cache di memorizzare il record per il periodo di tempo più lungo possibile senza che questo influisca sullo stato di aggiornamento del record. Di conseguenza, la memorizzazione nella cache sul lato client è ideale per la maggior parte delle situazioni.

Alcune distribuzioni Linux non includono la memorizzazione nella cache per impostazione predefinita. Si consiglia di aggiungere una cache per ogni macchina virtuale dopo aver controllato che non vi sia già una cache locale.

Sono disponibili diversi pacchetti di memorizzazione nella cache DNS, ad esempio dnsmasq. Ecco i passaggi per installare dnsmasq sulle distribuzioni più comuni:

**Ubuntu (usa resolvconf)**
  * Installare il pacchetto dnsmasq ("sudo apt-get install dnsmasq").

**SUSE (usa netconf)**:
1. Installare il pacchetto dnsmasq ("sudo zypper install dnsmasq").
2. Abilitare il servizio dnsmasq ("systemctl enable dnsmasq.service").
3. Avviare il servizio dnsmasq ("systemctl start dnsmasq.service").
4. Modificare "/etc/sysconfig/network/config" e sostituire NETCONFIG_DNS_FORWARDER="" con "dnsmasq".
5. Aggiornare resolv.conf ("netconfig update") per impostare la cache come resolver DNS locale.

**CentOS di Rogue Wave Software (in precedenza OpenLogic) (usa NetworkManager)**
1. Installare il pacchetto dnsmasq ("sudo yum install dnsmasq").
2. Abilitare il servizio dnsmasq ("systemctl enable dnsmasq.service").
3. Avviare il servizio dnsmasq ("systemctl start dnsmasq.service").
4. Aggiungere "prepend domain-name-servers 127.0.0.1;" in "/etc/dhclient-eth0.conf".
5. Riavviare il servizio di rete ("service network restart") per impostare la cache come resolver DNS locale

> [!NOTE]
> : il pacchetto 'dnsmasq' è solo una delle cache DNS disponibili per Linux. Prima di usarla, verificare che sia adatta in base alle esigenze specifiche e assicurarsi che non siano installate altre cache.
>
>

**Ripetizione di tentativi sul lato client**

DNS è principalmente un protocollo UDP. Poiché il protocollo UDP non garantisce il recapito dei messaggi, la logica di ripetizione dei tentativi viene gestita nel protocollo DNS stesso. Ogni client DNS (sistema operativo) può includere una logica di ripetizione dei tentativi diversa, in base alle preferenze degli autori:

* i sistemi operativi Windows eseguono un nuovo tentativo dopo 1 secondo e ne eseguono un altro dopo 2, 4 e altri 4 secondi.
* La configurazione di Linux predefinita esegue il tentativo dopo 5 secondi.  Modificare questa impostazione in modo da eseguire cinque tentativi a intervalli di 1 secondo.  

Per verificare le impostazioni correnti in una macchina virtuale Linux, aprire il file "cat /etc/resolv.conf" e osservare la riga "options", ad esempio:

    options timeout:1 attempts:5

Il file resolv.conf viene generato automaticamente e non deve essere modificato. La procedura specifica per aggiungere la riga "options" varia a seconda della distribuzione:

**Ubuntu** (usa resolvconf)
1. Aggiungere la riga per le opzioni in "/etc/resolveconf/resolv.conf.d/head".
2. Eseguire "resolvconf -u" per aggiornare.

**SUSE** (usa netconf)
1. Aggiungere "timeout:1 attempts:5" al parametro NETCONFIG_DNS_RESOLVER_OPTIONS="" in "/etc/sysconfig/network/config".
2. Eseguire "netconfig update" per aggiornare.

**CentOS di Rogue Wave Software (in precedenza OpenLogic)** (usa NetworkManager)
1. Aggiungere "echo "options timeout:1 attempts:5"" in "/etc/NetworkManager/dispatcher.d/11-dhclient".
2. Eseguire "service network restart" per aggiornare.

## <a name="name-resolution-using-your-own-dns-server"></a>Risoluzione dei nomi usando il server DNS
Le esigenze di risoluzione nome possono andare oltre le funzionalità presenti in Azure. Ad esempio, potrebbe essere necessaria una risoluzione DNS tra reti virtuali. Per coprire questo scenario, è possibile usare i propri server DNS.  

I server DNS all'interno di una rete virtuale possono inoltrare query DNS ai resolver ricorsivi di Azure per risolvere i nomi host all'interno della stessa rete virtuale. Ad esempio, un server DNS in esecuzione in Azure può rispondere a query DNS per i propri file di zona DNS e inoltrare tutte le altre query ad Azure. Questa funzionalità consente alle macchine virtuali di visualizzare sia le voci nel file di zona che i nomi host forniti da Azure (tramite il server d'inoltro). L'accesso ai resolver ricorsivi di Azure viene fornito tramite l'indirizzo IP virtuale 168.63.129.16.

L'inoltro del DNS abilita anche la risoluzione DNS tra reti virtuali e consente ai computer locali di risolvere i nomi host forniti da Azure. Per risolvere il nome host di una macchina virtuale, la macchina virtuale del server DNS deve risiedere nella stessa rete virtuale ed essere configurata per inoltrare le query relative ai nomi host ad Azure. Poiché il suffisso DNS è diverso in ogni rete virtuale, è possibile usare le regole di inoltro condizionale per inviare le query DNS alla rete virtuale corretta per la risoluzione. L'immagine seguente mostra due reti virtuali e una rete locale che gestiscono la risoluzione DNS tra reti virtuali con questo metodo:

![Risoluzione DNS tra reti virtuali](./media/azure-dns/inter-vnet-dns.png)

Quando si usa la risoluzione dei nomi fornita da Azure, il suffisso DNS interno viene fornito a ogni macchina virtuale tramite DHCP. Quando si usa la soluzione di risoluzione dei nomi personalizzata, questo suffisso non viene fornito alle macchine virtuali perché interferisce con altre architetture DNS. Per fare riferimento alle macchine virtuali usando il nome di dominio completo o per configurare il suffisso nelle macchine virtuali, è possibile usare PowerShell o l'API per determinare il suffisso:

* Per le reti virtuali gestite da Azure Resource Manager, il suffisso è disponibile tramite la risorsa [scheda interfaccia di rete](https://msdn.microsoft.com/library/azure/mt163668.aspx). È possibile eseguire anche il comando `azure network public-ip show <resource group> <pip name>` per visualizzare i dettagli dell'indirizzo IP pubblico, che include il nome FQDN della scheda di rete.

Se l'inoltro delle query ad Azure non soddisfa le esigenze correnti, sarà necessario offrire una soluzione DNS personalizzata.  La soluzione DNS deve:

* Offrire una soluzione di risoluzione dei nomi host appropriata, ad esempio tramite [DNS dinamico](../../virtual-network/virtual-networks-name-resolution-ddns.md). Se si usa DDNS, potrebbe essere necessario disabilitare lo scavenging dei record DNS. Le lease DHCP di Azure sono molto lunghe e lo scavenging può rimuovere i record DNS in modo anomalo.
* Fornire una soluzione di risoluzione ricorsiva appropriata per consentire la risoluzione dei nomi di dominio esterni.
* Essere accessibile (tramite TCP e UDP sulla porta 53) dai client che gestisce e in grado di accedere a Internet.
* Essere protetta dagli accessi provenienti da Internet per attenuare i rischi rappresentati da agenti esterni.

> [!NOTE]
> Per prestazioni ottimali, quando si usano macchine virtuali nei server DNS di Azure, disabilitare il protocollo IPv6 e assegnare un [indirizzo IP pubblico a livello di istanza](../../virtual-network/virtual-networks-instance-level-public-ip.md) a ogni macchina virtuale di server DNS.  
>
>

