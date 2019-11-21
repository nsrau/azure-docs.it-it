---
title: FAQ - Azure DNS
description: In this article, learn about frequently asked questions about Azure DNS
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 6/15/2019
ms.author: allensu
ms.openlocfilehash: c194d47842c927d3f8049a424dc08a34424a8a31
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212022"
---
# <a name="azure-dns-faq"></a>Domande frequenti su DNS di Azure

## <a name="about-azure-dns"></a>DNS di Azure

### <a name="what-is-azure-dns"></a>Che cos'è il servizio DNS di Azure?

Il sistema DNS (Domain Name System) esegue la conversione (o risoluzione) del nome di un sito Web o di un servizio nel relativo indirizzo IP. DNS di Azure è un servizio di hosting per i domini DNS che esegue la risoluzione dei nomi tramite l'infrastruttura di Microsoft Azure. L'hosting dei domini in Azure consente di gestire i record DNS usando gli stessi strumenti, credenziali, API e fatturazione usati per altri servizi Azure.

I domini DNS nel servizio DNS di Azure sono ospitati nella rete globale di Azure dei server dei nomi DNS. Il sistema usa le reti Anycast in modo che ogni query DNS riceva una risposta dal server DNS disponibile più vicino. DNS di Azure offre prestazioni migliori e consente di ottenere disponibilità elevata per il dominio.

DNS di Azure si basa su Azure Resource Manager e quindi ne sfrutta le funzionalità, ad esempio il controllo degli accessi in base al ruolo, i log di controllo e il blocco delle risorse. I domini e i record possono essere gestiti tramite il portale di Azure, i cmdlet di Azure PowerShell e l'interfaccia della riga di comando di Azure con supporto multipiattaforma. Le applicazioni che richiedono la gestione automatica del servizio DNS possono essere integrate con il servizio tramite l'API REST e gli SDK.

### <a name="how-much-does-azure-dns-cost"></a>Quanto costa DNS di Azure?

Il modello di fatturazione di DNS di Azure si basa sul numero di zone DNS ospitate in DNS di Azure e sul numero di query DNS ricevute. In base all'uso sono disponibili sconti.

Per altre informazioni, vedere la pagina relativa ai [prezzi di DNS di Azure](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Che cos'è il Contratto di servizio per DNS di Azure?

Azure garantisce che le richieste DNS valide ricevano una risposta da almeno uno dei server dei nomi DNS di Azure il 100% delle volte.

Per altre informazioni, vedere la pagina relativa al [contratto di Servizio per DNS di Azure](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Che cos'è una zona DNS? È la stessa cosa di un dominio DNS? 

Un dominio è un nome univoco nel Domain Name System, Un esempio è contoso.com.

Una zona DNS viene usata per ospitare i record DNS per un dominio specifico. Il dominio contoso.com può contenere, ad esempio, diversi record DNS. The records might include mail.contoso.com for a mail server and www\.contoso.com for a website. e sono ospitati nella zona DNS contoso.com.

Un nome di dominio è *solo un nome*, mentre una zona DNS è una risorsa di dati contenente i record DNS per un nome di dominio. È possibile usare DNS di Azure per ospitare una zona DNS e gestire i record DNS per un dominio in Azure. Offre anche server dei nomi DNS per rispondere alle query DNS da Internet.

### <a name="do-i-need-to-buy-a-dns-domain-name-to-use-azure-dns"></a>È necessario acquistare un nome di dominio DNS per usare DNS di Azure? 

Non necessariamente.

Non è necessario acquistare un dominio per ospitare una zona DNS in DNS di Azure. Puoi creare una zona DNS in qualsiasi momento senza essere il proprietario del nome di dominio. Le query DNS per la zona vengono risolte solo se sono indirizzate ai server dei nomi DNS di Azure assegnati alla zona.

Il nome di dominio deve essere acquistato se si vuole collegare la zona DNS alla gerarchia DNS globale. Questo collegamento consente alle query DNS eseguite da qualsiasi parte del mondo di trovare la zona DNS e di ottenere risposte con i record DNS.

## <a name="azure-dns-features"></a>Funzionalità di DNS di Azure

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>Sono previste restrizioni quando si usano i record alias per un vertice di nome di dominio con Gestione traffico?

Sì. È necessario usare indirizzi IP pubblici statici con Gestione traffico di Microsoft Azure. Configurare pertanto la destinazione **endpoint esterno** con un indirizzo IP statico. 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>DNS di Azure supporta il routing del traffico basato su DNS o il failover dell'endpoint?

Il failover dell'endpoint e il routing del traffico basato su DNS vengono garantiti da Gestione traffico, un servizio di Azure separato che può essere usato con DNS di Azure. Per altre informazioni, vedere la [panoramica sulla Gestione traffico](../traffic-manager/traffic-manager-overview.md).

DNS di Azure supporta solo domini DNS di hosting statici, in cui ogni query DNS correlata a un determinato record DNS riceve sempre la stessa risposta DNS.

### <a name="does-azure-dns-support-domain-name-registration"></a>DNS di Azure supporta la registrazione del nome di dominio?

No. DNS di Azure non supporta attualmente la possibilità di acquistare i nomi di dominio. Per acquistare domini, è necessario rivolgersi a un registrar di nomi di dominio di terze parti, Il registrar addebita in genere un prezzo annuo ridotto. I domini possono quindi essere ospitati in DNS di Azure per gestire i record DNS. Per altre informazioni, vedere [Delegare un dominio a DNS di Azure](dns-domain-delegation.md).

La funzionalità per l'acquisto dei nomi di domini è disponibile nel backlog di Azure. Usare il sito dei commenti e suggerimenti per [registrare il supporto per questa funzionalità](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>DNS di Azure supporta DNSSEC?

No. DNS di Azure non supporta attualmente la funzionalità DNSSEC (Domain Name System Security Extensions).

La funzionalità DNSSEC è disponibile nel backlog di DNS di Azure. Usare il sito dei commenti e suggerimenti per [registrare il supporto per questa funzionalità](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>DNS di Azure supporta i trasferimenti di zona (AXFR/IXFR)?

No. DNS di Azure attualmente non supporta i trasferimenti di zona. Le zone DNS possono essere [importate in DNS di Azure tramite l'interfaccia della riga di comando di Azure](dns-import-export.md). I record DNS vengono gestiti tramite il [portale di gestione di DNS di Azure](dns-operations-recordsets-portal.md), l'[API REST](https://docs.microsoft.com/powershell/module/az.dns), l'[SDK](dns-sdk.md), i [cmdlet di PowerShell](dns-operations-recordsets.md) o lo [strumento di interfaccia della riga di comando](dns-operations-recordsets-cli.md).

La funzionalità di trasferimento di zona è disponibile nel backlog di DNS Azure. Usare il sito dei commenti e suggerimenti per [registrare il supporto per questa funzionalità](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>DNS di Azure supporta i reindirizzamenti URL?

No. Il reindirizzamento URL non è un servizio DNS perché opera a livello HTTP anziché a livello di DNS. Alcuni provider DNS offrono un servizio di reindirizzamento URL nell'ambito dell'offerta complessiva. Tale servizio non è attualmente supportato da DNS di Azure.

La funzionalità di reindirizzamento URL è disponibile nel backlog di DNS di Azure. Usare il sito dei commenti e suggerimenti per [registrare il supporto per questa funzionalità](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

### <a name="does-azure-dns-support-the-extended-ascii-encoding-8-bit-set-for-txt-record-sets"></a>DNS di Azure supporta il set di codifica ASCII esteso (8 bit) per i set di record TXT?

Sì. DNS di Azure supporta il set di codifica ASCII esteso per i set di record TXT purché si usi la versione più recente delle API REST, degli SDK, di PowerShell e dell'interfaccia della riga di comando di Azure. Le versioni precedenti a quella del 1° ottobre 2017 o alla versione 2.1 dell'SDK non supportano il set ASCII esteso. 

Se un utente indica una stringa come il valore di un record TXT che contiene il carattere ASCII esteso \128, ad esempio "abcd\128efgh", DNS di Azure usa il valore in byte di questo carattere (ovvero 128) nella rappresentazione interna. Questo valore in byte viene restituito anche nella risposta della risoluzione DNS. Si noti inoltre che "abc" e "\097\098\099" sono interscambiabili nell'ambito della risoluzione. 

Vengono applicate le regole di escape del formato master dei file di zona [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) per i record TXT. Ad esempio, `\` viene sempre ignorato in base alle regole RFC. Se si specifica `A\B` come valore di record TXT, viene rappresentato e risolto come `AB`. Se si desidera che il record TXT contenga effettivamente `A\B` al momento della risoluzione, è necessario eseguire nuovamente l'escape di `\`. Ad esempio, specificare `A\\B`.

Questa possibilità non è attualmente supportata per i record TXT creati nel portale di Azure.

## <a name="alias-records"></a>Record alias

### <a name="what-are-some-scenarios-where-alias-records-are-useful"></a>Quali sono alcuni scenari in cui i record alias sono utili?

Vedere la sezione sugli scenari in [Panoramica dei record alias DNS di Azure](dns-alias.md).

### <a name="what-record-types-are-supported-for-alias-record-sets"></a>Quali tipi di record sono supportati per i set di record alias?

I set di record alias sono supportati per i tipi di record seguenti in una zona DNS di Azure:
 
- A 
- AAAA
- CNAME 

### <a name="what-resources-are-supported-as-targets-for-alias-record-sets"></a>Quali risorse sono supportate come destinazioni per i set di record alias?

- **Puntare a una risorsa IP pubblica da un set di record DNS A/AAAA**. È possibile creare un set di record A/AAAA e renderlo un set di record alias impostato per puntare a una risorsa IP pubblica.
- **Puntare a un profilo di Gestione traffico da un set di record DNS A/AAAA/CNAME**. È possibile puntare al CNAME di un profilo di Gestione traffico da un set di record CNAME DNS, ad esempio contoso.trafficmanager.net. A questo punto è anche possibile puntare a un profilo di Gestione traffico con endpoint esterni da un set di record A/AAAA nella zona DNS in uso.
- **Point to an Azure Content Delivery Network (CDN) endpoint**. This is useful when you create static websites using Azure storage and Azure CDN.
- **Puntare a un altro set di record DNS all'interno della stessa zona**. I record di alias possono fare riferimento ad altri set di record dello stesso tipo. È ad esempio possibile avere un set di record CNAME DNS come alias per un altro set di record CNAME dello stesso tipo. Questo approccio è utile se si vuole che solo alcuni set di record siano alias.

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>È possibile creare e aggiornare i record alias dal portale di Azure?

Sì. I record alias possono essere creati o gestiti nel portale di Azure oltre che con le API REST di Azure, Azure PowerShell, l'interfaccia della riga di comando e gli SDK.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>I record alias garantiscono che il set di record DNS in uso venga eliminato quando viene eliminato l'indirizzo IP pubblico sottostante?

Sì. Questa è una delle funzionalità principali dei record alias e consente di evitare potenziali interruzioni agli utenti finali dell'applicazione.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>I record alias garantiscono che il set di record DNS in uso venga aggiornato all'indirizzo IP corretto quando viene modificato l'indirizzo IP pubblico sottostante?

Sì. Questa è una delle funzionalità principali dei record alias e consente di evitare potenziali interruzioni o rischi per la sicurezza a livello di applicazione.

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-a-or-aaaa-records-to-point-to-traffic-manager"></a>Sono previste restrizioni quando si usano i set di record alias per fare in modo che i record A/AAAA puntino a Gestione traffico?

Sì. Per puntare a un profilo di Gestione traffico come alias da un set di record A/AAAA, è necessario verificare che il profilo di Gestione traffico usi solo endpoint esterni. Quando si creano gli endpoint esterni in Gestione traffico, assicurarsi di indicare gli indirizzi IP effettivi degli endpoint.

### <a name="is-there-an-additional-charge-to-use-alias-records"></a>L'uso di record alias prevede un addebito aggiuntivo?

I record alias sono una qualifica in un set di record DNS valido e non prevedono alcun addebito aggiuntivo.

## <a name="use-azure-dns"></a>Uso di DNS di Azure

### <a name="can-i-co-host-a-domain-by-using-azure-dns-and-another-dns-provider"></a>Can I co-host a domain by using Azure DNS and another DNS provider?

Sì. DNS di Azure supporta i domini di hosting condiviso con altri servizi DNS.

To set up co-hosting, modify the NS records for the domain to point to the name servers of both providers. I record dei server dei nomi controllano quali provider ricevono le query DNS per il dominio. È possibile modificare tali record in DNS di Azure, nell'altro provider e nella zona padre, configurata in genere tramite il registrar di nomi di dominio. Per altre informazioni sulla delega DNS, vedere [Delega del dominio DNS](dns-domain-delegation.md).

È anche necessario verificare che i record DNS per il dominio siano sincronizzati tra i due provider DNS. DNS di Azure attualmente non supporta i trasferimenti di zona DNS e i record DNS devono essere sincronizzati tramite il [portale di gestione di DNS di Azure](dns-operations-recordsets-portal.md), l'[API REST](https://docs.microsoft.com/powershell/module/az.dns), l'[SDK](dns-sdk.md), i [cmdlet di PowerShell](dns-operations-recordsets.md) o lo [strumento di interfaccia della riga di comando](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>È necessario delegare il dominio a tutti e quattro i server dei nomi DNS di Azure?

Sì. DNS di Azure assegna quattro server dei nomi a ogni zona DNS per isolare gli errori e aumentare la resilienza. Per risultare idonei al contratto di servizio per DNS di Azure, delegare il dominio a tutti i quattro server dei nomi.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Quali sono i limiti di uso per DNS di Azure?

Quando si usa DNS di Azure, si applicano i limiti predefiniti indicati di seguito.

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>È possibile spostare una zona DNS di Azure tra gruppi di risorse o tra sottoscrizioni?

Sì. Le zone DNS possono essere spostate tra gruppi di risorse o tra sottoscrizioni.

Lo spostamento di una zona DNS non influenza le query DNS. I server dei nomi assegnati alla zona rimangono invariati e le query DNS vengono elaborate come di consueto.

Per altre informazioni e istruzioni su come spostare le zone DNS, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../azure-resource-manager/resource-group-move-resources.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Quanto tempo occorre per rendere effettive le modifiche DNS?

Le nuove zone DNS e i nuovi record DNS diventano in genere disponibili nei server dei nomi DNS di Azure rapidamente, entro pochi secondi.

Le modifiche ai record DNS esistenti possono richiedere più tempo, ma diventano effettive nel server dei nomi DNS di Azure entro 60 secondi. La memorizzazione nella cache di DNS eseguita dai client e dai resolver ricorsivi DNS all'esterno di DNS di Azure può anche influire sul tempo necessario. Per controllare la durata della cache, usare la proprietà TTL (Time-To-Live) di ogni set di record.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Come proteggere le zone DNS da eliminazioni accidentali?

DNS di Azure viene gestito tramite Azure Resource Manager e di conseguenza ne sfrutta le funzionalità di controllo di accesso. Il controllo degli accessi in base al ruolo consente di monitorare gli utenti con accesso in lettura o scrittura alle zone DNS e ai set di record, mentre i blocchi delle risorse consentono di evitare modifiche o eliminazioni accidentali di zone DNS e set di record.

Per altre informazioni, vedere [Come proteggere le zone e i record DNS](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Come configurare i record SPF in DNS di Azure?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-name-servers-resolve-over-ipv6"></a>I server dei nomi DNS di Azure risolvono gli indirizzi su IPv6? 

Sì. I server dei nomi DNS di Azure sono dual stack, ovvero dispongono sia di indirizzi IPv4 che di indirizzi IPv6. Per trovare l'indirizzo IPv6 per i server dei nomi DNS di Azure assegnati alla zona DNS, usare uno strumento come nslookup. Un esempio è `nslookup -q=aaaa <Azure DNS Nameserver>`.

### <a name="how-do-i-set-up-an-idn-in-azure-dns"></a>Come configurare un nome IDN in DNS di Azure?

I nomi IDN (Internationalized Domain Name) usano [punycode](https://en.wikipedia.org/wiki/Punycode) per codificare ogni nome DNS. Le query DNS vengono create usando i nomi con codifica punycode.

È possibile configurare i nomi IDN in DNS di Azure prima di convertire il nome della zona o del set di record in punycode. DNS di Azure non supporta attualmente la conversione integrata da e verso punycode.

## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è DNS di Azure?](dns-overview.md)

- [Usare DNS di Azure per i domini privati](private-dns-overview.md)

- [Panoramica delle zone e dei record DNS](dns-zones-records.md)

- [Introduzione a DNS di Azure](dns-getstarted-portal.md)
