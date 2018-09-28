---
title: Domande frequenti su DNS di Azure | Microsoft Docs
description: Domande frequenti su DNS di Azure
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
editor: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 66e04e7f0b272f19788e79805ef06d11e2eda572
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948027"
---
# <a name="azure-dns-faq"></a>Domande frequenti su DNS di Azure

## <a name="about-azure-dns"></a>DNS di Azure

### <a name="what-is-azure-dns"></a>Che cos'è DNS di Azure?

Il nome DNS (Domain Name System) è responsabile della conversione (o risoluzione) del nome di un sito Web o del servizio nel relativo indirizzo IP. DNS di Azure è un servizio di hosting per i domini DNS, che fornisce la risoluzione dei nomi usando l'infrastruttura di Microsoft Azure. Ospitando i domini in Azure, è possibile gestire i record DNS usando le stesse credenziali, API, strumenti e fatturazione come per gli altri servizi Azure.

I domini DNS nel servizio DNS di Azure sono ospitati nella rete globale di Azure dei server dei nomi DNS. Il servizio usa le reti Anycast in modo che ogni query DNS riceva una risposta dal server DNS disponibile più vicino. Ciò consente di accelerare le prestazioni e ottenere la disponibilità elevata per il dominio.

Il servizio DNS di Azure è basato su Azure Resource Manager. Usufruisce quindi delle funzionalità di Resource Manager, come il controllo degli accessi in base al ruolo, i log di controllo e il blocco delle risorse. I domini e i record possono essere gestiti tramite il portale di Azure, i cmdlet di Azure PowerShell e l'interfaccia della riga di comando di Azure. Le applicazioni che richiedono la gestione automatica di DNS possono essere integrate con il servizio tramite l'API REST e gli SDK.

### <a name="how-much-does-azure-dns-cost"></a>Quanto costa DNS di Azure?

Il modello di fatturazione di DNS di Azure si basa sul numero di zone DNS ospitate in DNS di Azure e sul numero di query DNS ricevute. In base all'uso sono disponibili sconti.

Per altre informazioni, vedere la pagina relativa ai [prezzi di DNS di Azure](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Qual è il contratto di servizio per DNS di Azure?

Azure garantisce che le richieste DNS valide ricevano una risposta da almeno uno dei server dei nomi DNS di Azure almeno il 99,99% delle volte.

Per altre informazioni, vedere la pagina relativa al [contratto di Servizio per DNS di Azure](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Che cos'è una "zona DNS"? È la stessa cosa di un dominio DNS? 

Un dominio è un nome univoco nel Domain Name System, ad esempio "contoso.com".


Una zona DNS viene usata per ospitare i record DNS per un particolare dominio. Ad esempio, il dominio "contoso.com" può contenere diversi record DNS, come "mail.contoso.com" (per un server di posta) e "www.contoso.com" (per un sito Web). Questi record verrebbero ospitati nella zona DNS "contoso.com".

Un nome di dominio è *solo un nome*, mentre una zona DNS è una risorsa di dati contenente i record DNS per un nome di dominio. DNS di Azure consente di ospitare una zona DNS e gestire i record DNS per un dominio in Azure. Offre anche server dei nomi DNS per rispondere alle query DNS da Internet.

### <a name="do-i-need-to-purchase-a-dns-domain-name-to-use-azure-dns"></a>Devo acquistare un nome di dominio DNS per usare DNS di Azure? 

Non necessariamente.

Non è necessario acquistare un dominio per ospitare una zona DNS in DNS di Azure. È possibile creare una zona DNS in qualsiasi momento senza essere il proprietario del nome di dominio. Le query DNS per la zona verranno risolte solo se sono indirizzate ai server di nomi DNS di Azure assegnato alla zona.

È necessario acquistare il nome di dominio se si vuole collegare la zona DNS alla gerarchia DNS globale. Questo collegamento consente alle query DNS eseguite da qualsiasi parte del mondo di trovare la zona DNS e ottenere risposte con i record DNS.

## <a name="azure-dns-features"></a>Funzionalità DNS di Azure

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>Sono previste restrizioni quando si usano i record alias per un vertice di nome di dominio con Gestione traffico?

Sì. È necessario usare indirizzi IP pubblici statici con Gestione traffico. Configurare la destinazione dell'**endpoint esterno** con un indirizzo IP statico. 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>DNS di Azure supporta il routing del traffico basato su DNS o il failover dell'endpoint?

Il failover dell'endpoint e il routing del traffico basato su DNS vengono garantiti da Gestione traffico di Azure. un servizio di Azure separato che può essere usato insieme a DNS di Azure. Per altre informazioni, vedere la [panoramica sulla Gestione traffico](../traffic-manager/traffic-manager-overview.md).

DNS di Azure supporta solo domini DNS di Traffic Manager "statici", in cui ogni query DNS per un determinato record DNS riceve sempre la stessa risposta DNS.

### <a name="does-azure-dns-support-domain-name-registration"></a>DNS di Azure supporta la registrazione del nome di dominio?

No. DNS di Azure attualmente non supporta l'acquisto dei nomi di dominio. Se si vogliono acquistare domini, è necessario ricorrere a un registrar di terze parti, che addebita in genere una tariffa annuale contenuta. I domini possono quindi essere ospitati in DNS di Azure per la gestione dei record DNS. Per informazioni dettagliate, vedere [Delegare un dominio a DNS di Azure](dns-domain-delegation.md) .

La funzionalità per l'acquisto di domini è inclusa nel backlog delle funzionalità da implementare in Azure. È possibile usare il sito dei commenti e suggerimenti per [registrare il proprio supporto per questa funzionalità](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>DNS di Azure supporta DNSSEC?

No. DNS di Azure attualmente non supporta DNSSEC.

La funzionalità DNSSEC è inclusa nel backlog delle funzionalità da implementare in DNS di Azure. È possibile usare il sito dei commenti e suggerimenti per [registrare il proprio supporto per questa funzionalità](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>DNS di Azure supporta i trasferimenti di zona (AXFR/IXFR)?

No. DNS di Azure attualmente non supporta i trasferimenti di zona. Le zone DNS possono essere [importate in DNS di Azure tramite l'interfaccia della riga di comando di Azure](dns-import-export.md). I record DNS possono quindi essere gestiti tramite il [portale di gestione di DNS di Azure](dns-operations-recordsets-portal.md), l'[API REST](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), i [cmdlet di PowerShell](dns-operations-recordsets.md) o lo [strumento dell'interfaccia della riga di comando](dns-operations-recordsets-cli.md).

La funzionalità per i trasferimenti di zona è inclusa nel backlog delle funzionalità da implementare in DNS di Azure. È possibile usare il sito dei commenti e suggerimenti per [registrare il proprio supporto per questa funzionalità](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>DNS di Azure supporta i reindirizzamenti URL?

No. Il servizio di reindirizzamento URL non è attualmente un servizio DNS, poiché opera al livello HTTP, piuttosto che a livello di DNS. Alcuni provider DNS offrono anche un servizio di reindirizzamento dell'URL come parte dell'offerta complessiva. Questo servizio non è attualmente supportato da DNS di Azure.

La funzionalità per i reindirizzamenti URL è inclusa nel backlog delle funzionalità da implementare in DNS di Azure. È possibile usare il sito dei commenti e suggerimenti per [registrare il proprio supporto per questa funzionalità](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

### <a name="does-azure-dns-support-extended-ascii-encoding-8-bit-set-for-txt-recordset"></a>Il DNS di Azure supporta il set di codifica ASCII esteso (8 bit) per i recordset TXT?

Sì. Il DNS di Azure supporta il set di codifica ASCII esteso per i recordset TXT, purché si usi la versione più recente delle API REST, degli SDK, di PowerShell e dell'interfaccia della riga di comando di Azure (le versioni precedenti alla 2017-10-01 o a SDK 2.1 non supportano il set ASCII esteso). Ad esempio, se l'utente fornisce una stringa come valore di un record TXT che contiene il carattere ASCII esteso \128 (come "abcd\128efgh"), il DNS di Azure userà il valore in byte di questo carattere (ossia 128) nella rappresentazione interna. Questo valore in byte verrà anche restituito nella risposta della risoluzione DNS. Si noti inoltre che "abc" e "\097\098\099" sono interscambiabili nell'ambito della risoluzione. 

Vengono applicate le regole di escape del formato master dei file di zona [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) per i record TXT. Ad esempio, "\" viene sempre ignorato in base alle regole RFC. Se si specifica "A\B" come valore del record TXT, verrà rappresentato e risolto solo come "AB". Se si vuole che il record TXT contenga effettivamente "A\B" al momento della risoluzione, è necessario eseguire nuovamente l'escape di "\", ossia specificare "A\\B". 

Questo supporto non è attualmente disponibile per i record TXT creati dal portale di Azure. 

## <a name="alias-records"></a>Record alias

### <a name="what-are-some-scenarios-where-alias-records-are-useful"></a>Quali sono alcuni scenari in cui i record alias sono utili?
Vedere la sezione sugli scenari in [Panoramica dei record di alias DNS di Azure](dns-alias.md).

### <a name="what-record-types-are-supported-for-alias-record-sets"></a>Quali tipi di record sono supportati per i set di record alias?
I set di record alias sono supportati per i tipi di record seguenti in una zona DNS di Azure: A, AAAA e CNAME. 

### <a name="what-resources-are-supported-as-targets-for-alias-record-sets"></a>Quali risorse sono supportate come destinazioni per i set di record alias?
- **Puntare a una risorsa IP pubblica da un set di record DNS A/AAAA**. È possibile creare un set di record A/AAAA e renderlo un set di record alias impostato per puntare a una risorsa IP pubblica.
- **Puntare a un profilo di Gestione traffico da un set di record DNS A/AAAA/CNAME**. Oltre a poter puntare al CNAME del profilo di Gestione traffico (ad esempio: contoso.trafficmanager.net) da un set di record DNS CNAME, è ora possibile anche puntare a un profilo di Gestione traffico con endpoint esterni da un set di record A o AAAA nella propria zona DNS.
- **Puntare a un altro set di record DNS all'interno della stessa zona**. I record alias possono fare riferimento ad altri set di record dello stesso tipo. È ad esempio possibile avere un set di record DNS CNAME come alias per un altro set di record CNAME dello stesso tipo. Ciò è utile se si vuole avere alcuni set di record come alias e altri come non alias in termini di comportamento.

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>È possibile creare e aggiornare i record alias dal portale di Azure?
Sì. I record alias possono essere creati o gestiti nel portale di Azure oltre che nelle API REST di Azure, in Azure PowerShell, nell'interfaccia della riga di comando e negli SDK.

### <a name="will-alias-records-help-ensure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>I record alias garantiscono che il set di record DNS venga eliminato quando viene eliminato l'indirizzo IP pubblico sottostante?
Sì. Questa è in effetti una delle funzionalità principali dei record alias. Consentono di evitare potenziali interruzioni per gli utenti finali dell'applicazione.

### <a name="will-alias-records-help-ensure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>I record alias garantiscono che il set di record DNS venga aggiornato all'indirizzo IP corretto quando viene modificato l'indirizzo IP pubblico sottostante?
Sì. Come indicato nella domanda precedente, questa è una delle funzionalità principali dei record alias che consentono di evitare potenziali interruzioni o rischi per la sicurezza dell'applicazione.

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-an-a-or-aaaa-records-to-point-to-traffic-manager"></a>Sono previste restrizioni quando si usano i set di record alias per fare in modo che i record A o AAAA puntino a Gestione traffico?
Sì. Per puntare a un profilo di Gestione traffico come alias da un set di record A o AAAA, è necessario assicurarsi che il profilo di Gestione traffico usi solo endpoint esterni. Quando si creano gli endpoint esterni in Gestione traffico, assicurarsi di fornire gli indirizzi IP effettivi degli endpoint.

### <a name="is-there-an-additional-charge-for-using-alias-records"></a>Sono previsti addebiti aggiuntivi per l'uso dei record alias?
I record alias sono una qualifica in un set di record DNS valido e non sono previsti addebiti aggiuntivi per i record alias.

## <a name="using-azure-dns"></a>Uso di DNS di Azure

### <a name="can-i-co-host-a-domain-using-azure-dns-and-another-dns-provider"></a>È possibile ospitare un dominio usando DNS di Azure e un altro provider DNS?

Sì. DNS di Azure supporta i domini di hosting condiviso con altri servizi DNS.

A tale scopo, è necessario modificare i record NS per il dominio, che controlla i provider che ricevono le query DNS per il dominio, in modo da puntare ai server dei nomi di entrambi i provider. I record NS possono essere modificati in tre posizioni: in DNS di Azure, nell'altro provider e nella zona padre, in genere configurata tramite il registrar. Per altre informazioni sulla delega DNS, vedere [Delega del dominio DNS](dns-domain-delegation.md).

È anche necessario assicurarsi che i record DNS per il dominio siano sincronizzati tra i due provider DNS. DNS di Azure attualmente non supporta i trasferimenti di zona DNS. I record DNS devono essere sincronizzati tramite il [portale di gestione di DNS di Azure](dns-operations-recordsets-portal.md), l'[API REST](https://docs.microsoft.com/powershell/module/azurerm.dns), l'[SDK](dns-sdk.md), i [cmdlet di PowerShell](dns-operations-recordsets.md) o lo [strumento dell'interfaccia della riga di comando](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>È necessario delegare il dominio a tutti e quattro i server dei nomi DNS di Azure?

Sì. DNS di Azure assegna quattro server dei nomi a ogni zona DNS, per isolare gli errori e aumentare la resilienza. Per risultare idonei al contratto di servizio per DNS di Azure, è necessario delegare il dominio a tutti e quattro i server dei nomi.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Quali sono i limiti di uso per DNS di Azure?

Quando si usa DNS di Azure, si applicano i limiti predefiniti seguenti:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>È possibile spostare una zona DNS di Azure tra gruppi di risorse o tra sottoscrizioni?

Sì. Le zone DNS possono essere spostate tra gruppi di risorse o tra sottoscrizioni.

Lo spostamento di una zona DNS non influenza le query DNS. I server dei nomi assegnati alla zona rimangono invariati e le query DNS vengono elaborate come di consueto.

Per altre informazioni e istruzioni su come spostare le zone DNS, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../azure-resource-manager/resource-group-move-resources.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Quanto tempo occorre per rendere effettive le modifiche DNS?

Le nuove zone DNS e i record DNS sono in genere applicati nel server dei nomi DNS di Azure rapidamente, entro pochi secondi.

Le modifiche ai record DNS esistenti possono richiedere un po' più di tempo, ma vengono applicate nel server dei nomi DNS di Azure entro 60 secondi. In questo caso, la memorizzazione nella cache di DNS esterna al DNS di Azure, da parte dei client DNS e i resolver ricorsivi DNS, possono incidere sul tempo impiegato per l'applicazione di una modifica DNS. La durata della cache può essere controllata usando la proprietà di Time-To-Live (TTL) di ogni set di record.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Come proteggere le zone DNS da eliminazioni accidentali?

DNS di Azure viene gestito tramite Azure Resource Manager e gode delle funzionalità di controllo dell'accesso offerte da Azure Resource Manager. Il controllo di accesso basato sui ruoli consente di controllare gli utenti che hanno accesso in lettura o scrittura alle zone DNS e ai set di record. Per evitare modifiche o eliminazioni accidentali di zone DNS e set di record è possibile usare il blocco delle risorse.

Per altre informazioni, vedere [Protezione delle zone e dei record DNS](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Come configurare i record SPF in DNS di Azure?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-nameservers-resolve-over-ipv6"></a>I server dei nomi DNS di Azure risolvono gli indirizzi su IPv6? 

Sì. I server di nomi DNS di Azure sono dual-stack (hanno sia indirizzi IPv4 che indirizzi IPv6). Per trovare l'indirizzo IPv6 per i server dei nomi DNS di Azure assegnati alla zona DNS, è possibile usare uno strumento come nslookup (ad esempio `nslookup -q=aaaa <Azure DNS Nameserver>`).

### <a name="how-do-i-set-up-an-international-domain-name-idn-in-azure-dns"></a>Come configurare un nome di dominio internazionale, IDN, in DNS di Azure?

I nomi di dominio internazionali, IDN, usano la codifica per ogni nome DNS con "[punycode](https://en.wikipedia.org/wiki/Punycode)". Le query DNS vengono create usando i nomi con codifica punycode.

È possibile configurare i nomi di dominio internazionale, IDN, in DNS di Azure prima di convertire il nome della zona o il nome del set di record in punycode. DNS di Azure non supporta attualmente la conversione incorporata da e verso punycode.

## <a name="private-dns"></a>DNS privato

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

### <a name="does-azure-dns-support-private-domains"></a>DNS di Azure supporta domini "privati"?
Il supporto dei domini "privati" viene implementato tramite la funzionalità Zone private.  Questa funzionalità è attualmente disponibile in anteprima pubblica.  Le zone private vengono gestite tramite gli stessi strumenti usati per le zone DNS di Azure con connessione Internet, ma sono risolvibili solo all'interno di reti virtuali specificate.  Vedere la [panoramica](private-dns-overview.md) per informazioni dettagliate.

Attualmente la funzionalità Zone private non è supportata nel portale di Azure. 

Per informazioni sulle altre opzioni DNS interne in Azure, vedere [Risoluzione dei nomi per le macchine virtuali e le istanze del ruolo](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

### <a name="what-is-the-difference-between-registration-virtual-network-and-resolution-virtual-network-in-the-context-of-private-zones"></a>Qual è la differenza tra rete virtuale di registrazione e rete virtuale di risoluzione nel contesto delle zone private? 
È possibile collegare le reti virtuali a una zona DNS privata in due modi: come rete virtuale di registrazione o come rete virtuale di risoluzione. In entrambi i casi, le macchine virtuali nella rete virtuale saranno in grado di risolvere i nomi correttamente rispetto ai record nella zona privata. Se tuttavia si specifica una rete virtuale come rete virtuale di registrazione, Azure registrerà automaticamente (registrazione dinamica) i record DNS nella zona per le macchine virtuali della rete virtuale. Inoltre, quando una macchina virtuale in una rete virtuale di registrazione viene eliminata, Azure rimuove automaticamente anche il record DNS corrispondente dalla zona privata collegata. 

### <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Il servizio Zone private di DNS di Azure funziona tra le diverse aree di Azure?
Sì. Il servizio Zone private è supportato per la risoluzione DNS tra le reti virtuali nelle aree di Azure, anche senza peering esplicito delle reti virtuali, purché siano tutte specificate come reti virtuali di risoluzione per la zona privata. I clienti possono richiedere il peering delle reti virtuali per consentire il flusso del traffico TCP/HTTP da un'area all'altra. 

### <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Per le zone private è necessaria la connettività a Internet dalle reti virtuali?
No. Le zone private interagiscono con le reti virtuali e consentono ai clienti di gestire i domini per Macchine virtuali o altre risorse all'interno delle reti virtuali o fra tali reti. La connettività a Internet non è necessaria per la risoluzione dei nomi. 

### <a name="can-the-same-private-zone-be-used-for-multiple-virtual-networks-for-resolution"></a>È possibile usare la stessa zona privata per più reti virtuali di risoluzione? 
Sì. I clienti possono associare un massimo di 10 reti virtuali di risoluzione a una singola zona privata.

### <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-resolution-virtual-network-to-a-private-zone"></a>È possibile aggiungere a una zona privata una rete virtuale appartenente a una diversa sottoscrizione come rete virtuale di risoluzione? 
Sì, purché l'utente disponga dell'autorizzazione alle operazioni di scrittura in entrambe le reti virtuali e nella zona DNS privata. L'autorizzazione di scrittura può essere assegnata a più ruoli Controllo degli accessi in base al ruolo. Il ruolo RBAC Collaboratore Rete classica, ad esempio, dispone di autorizzazioni di scrittura per le reti virtuali. Per altre informazioni sui ruoli RBAC, vedere [Controllo degli accessi in base al ruolo](../role-based-access-control/overview.md).

### <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-the-virtual-machines-are-deleted-by-the-customer"></a>I record DNS di macchina virtuale registrati automaticamente in una zona privata vengono eliminati automaticamente quando il cliente elimina le macchine virtuali?
Sì. Se si elimina una macchina virtuale all'interno di una rete virtuale di registrazione, si eliminano automaticamente i record DNS registrati nella zona perché la rete è una rete virtuale di registrazione. 

### <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-registration-virtual-network-be-deleted-manually"></a>Un record di macchina virtuale registrato automaticamente in una zona privata (da una rete virtuale di registrazione) può essere eliminato manualmente? 
No. Attualmente, i record DNS di macchina virtuale registrati automaticamente in una zona privata da una rete virtuale di registrazione non sono visibili e non possono essere modificati dai clienti. È tuttavia possibile sostituire (sovrascrivere) i record DNS registrati automaticamente con un record DNS creato manualmente nella zona. Vedere la domanda e la risposta seguenti che illustrano questa operazione.

### <a name="what-happens-when-we-attempt-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-registration-virtual-network"></a>Cosa accade quando si prova a creare manualmente un nuovo record DNS in una zona privata che ha lo stesso nome host di una macchina virtuale esistente (registrata automaticamente) in una rete virtuale di registrazione? 
Se si prova a creare manualmente un nuovo record DNS in una zona privata con lo stesso nome host di una macchina virtuale esistente (registrata automaticamente) in una rete virtuale di registrazione, il nuovo record DNS sovrascriverà il record di macchina virtuale registrato automaticamente. Inoltre, se in seguito si prova a eliminare dalla zona il record DNS creato manualmente, quest'ultimo verrà eliminato e la registrazione automatica verrà ripetuta, ovvero il record DNS verrà ricreato automaticamente nella zona, a condizione che la macchina virtuale esista ancora e abbia un indirizzo IP privato. 

### <a name="what-happens-when-we-unlink-a-registration-virtual-network-from-a-private-zone-would-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-as-well"></a>Cosa accade quando si scollega una rete virtuale di registrazione da una zona privata? I record di macchina virtuale registrati automaticamente nella rete virtuale vengono rimossi anche dalla zona?
Sì. Se si scollega da una zona privata una rete virtuale di registrazione, ovvero si aggiorna la zona DNS per rimuovere la rete virtuale di registrazione associata, Azure rimuoverà dalla zona i record di macchina virtuale registrati automaticamente. 

### <a name="what-happens-when-we-delete-a-registration-or-resolution-virtual-network-that-is-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-registration-or-resolution--virtual-network-from-the-zone"></a>Cosa accade quando si elimina una rete virtuale di registrazione (o di risoluzione) collegata a una zona privata? È necessario aggiornare manualmente la zona privata per scollegare la rete virtuale come rete virtuale di registrazione (o di risoluzione) dalla zona?
Sì. Quando si elimina una rete virtuale di registrazione (o di risoluzione) senza prima scollegarla da una zona privata, Azure consente l'operazione di eliminazione, ma la rete virtuale non viene scollegata automaticamente dalla zona privata, se presente. È necessario scollegare la rete virtuale dalla zona privata manualmente. Per questo motivo è consigliabile eseguire questa operazione prima di eliminare la rete virtuale.

### <a name="would-dns-resolution-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-contosolocal-is-linked-to-a-virtual-network"></a>La risoluzione DNS tramite il nome di dominio completo (FQDN) predefinito (internal.cloudapp.net) continua a funzionare anche quando una zona privata, ad esempio contoso.local, è collegata a una rete virtuale? 
Sì. La funzionalità Zone private non sostituisce le risoluzioni DNS predefinite, eseguite tramite la zona internal.cloudapp.net fornita da Azure, e viene offerta come miglioramento o funzionalità aggiuntiva. Indipendentemente dal fatto che si usi la zona internal.cloudapp.net fornita da Azure o una zona privata, è consigliabile usare il nome FQDN della zona rispetto alla quale si vuole eseguire la risoluzione. 

### <a name="would-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Il suffisso DNS sulle macchine virtuali all'interno di una rete virtuale collegata deve essere sostituito con quello della zona privata? 
No. Attualmente, il suffisso DNS sulle macchine virtuali nella rete virtuale collegata continua a essere quello predefinito fornito da Azure ("*.internal.cloudapp.net"). È tuttavia possibile sostituire questo suffisso manualmente sulle macchine virtuali con quello della zona privata. 

### <a name="are-there-any-limitations-for-private-zones-during-this-preview"></a>Sono presenti limitazioni per le zone private in questa versione di anteprima?
Sì. Nell'anteprima pubblica sono presenti le limitazioni seguenti:
* È consentita una sola rete virtuale di registrazione per zona privata.
* È consentito un massimo di 10 reti virtuali di risoluzione per zona privata
* Una determinata rete virtuale può essere collegata a una sola zona privata come rete virtuale di registrazione
* Una determinata rete virtuale può essere collegata a un massimo di 10 zone private come rete virtuale di risoluzione
* Se viene specificata una rete virtuale di registrazione, i record DNS per le macchine virtuali di tale rete che sono registrati nella zona privata non sono visualizzabili né recuperabili tramite PowerShell/CLI/API, ma sono effettivamente registrati e vengono risolti correttamente
* Il DNS inverso funziona solo per lo spazio IP privato nella rete virtuale di registrazione.
* Il DNS inverso per un indirizzo IP privato che non è registrato nella zona privata, ad esempio un indirizzo IP privato per una macchina virtuale in una rete virtuale collegata a una zona privata come rete virtuale di risoluzione, restituisce "internal.cloudapp.net" come suffisso DNS, ma questo suffisso non è risolvibile.   
* Le rete virtuale deve essere vuota, ovvero priva di macchine virtuali con scheda di interfaccia di rete, quando viene collegata per la prima volta a una zona privata come rete virtuale di registrazione o di risoluzione. Questo requisito tuttavia non si applica quando la rete virtuale viene successivamente collegata come rete virtuale di registrazione o di risoluzione ad altre zone private. 
* Attualmente, l'inoltro condizionale non è supportato, ad esempio per abilitare la risoluzione tra reti di Azure e locali. Per informazioni su come i clienti possono sfruttare questo scenario tramite altri meccanismi, vedere [Risoluzione dei nomi per le macchine virtuali e le istanze del ruolo](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

### <a name="are-there-any-quotas-or-limits-on-zones-or-records-for-private-zones"></a>Sono presenti limiti relativi al numero di zone o di record per le zone private?
Per le zone private non sono previsti limiti separati relativi al numero di zone consentito per sottoscrizione o al numero di set di record per zona. Per le zone pubbliche e private vengono applicati i limiti complessivi relativi a DNS, come illustrato [in questo articolo](../azure-subscription-service-limits.md#dns-limits).

### <a name="is-there-portal-support-for-private-zones"></a>È disponibile il supporto del portale per le zone private?
Le zone private già create tramite meccanismi diversi dal portale (API/PowerShell/CLI/SDK) saranno visibili nel portale di Azure, ma i clienti non potranno né creare nuove zone private né gestire le associazioni con le reti virtuali. Inoltre, per le reti virtuali associate come reti virtuali di registrazione, i record di macchina virtuale registrati automaticamente non saranno visibili dal portale. 

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni su DNS di Azure](dns-overview.md)
<br>
[Altre informazioni sull'uso di DNS di Azure per i domini privati](private-dns-overview.md)
<br>
[Altre informazioni sulle zone e sui record DNS](dns-zones-records.md)
<br>
[Introduzione a DNS di Azure](dns-getstarted-portal.md)

