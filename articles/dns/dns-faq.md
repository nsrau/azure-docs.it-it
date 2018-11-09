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
ms.openlocfilehash: daf65b00ffa753568ab99e64365cc0625792f593
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092679"
---
# <a name="azure-dns-faq"></a>Domande frequenti su DNS di Azure

## <a name="about-azure-dns"></a>DNS di Azure

### <a name="what-is-azure-dns"></a>Che cos'è DNS di Azure?

Il sistema DNS (Domain Name System) esegue la conversione (o risoluzione) del nome di un sito Web o di un servizio nel relativo indirizzo IP. DNS di Azure è un servizio di hosting per i domini DNS che esegue la risoluzione dei nomi tramite l'infrastruttura di Microsoft Azure. L'hosting dei domini in Azure consente di gestire i record DNS usando gli stessi strumenti, credenziali, API e fatturazione usati per altri servizi Azure.

I domini DNS nel servizio DNS di Azure sono ospitati nella rete globale di Azure dei server dei nomi DNS. Il sistema usa le reti Anycast in modo che ogni query DNS riceva una risposta dal server DNS disponibile più vicino. DNS di Azure offre prestazioni migliori e consente di ottenere disponibilità elevata per il dominio.

DNS di Azure si basa su Azure Resource Manager e quindi ne sfrutta le funzionalità, ad esempio il controllo degli accessi in base al ruolo, i log di controllo e il blocco delle risorse. I domini e i record possono essere gestiti tramite il portale di Azure, i cmdlet di Azure PowerShell e l'interfaccia della riga di comando di Azure con supporto multipiattaforma. Le applicazioni che richiedono la gestione automatica del servizio DNS possono essere integrate con il servizio tramite l'API REST e gli SDK.

### <a name="how-much-does-azure-dns-cost"></a>Quanto costa DNS di Azure?

Il modello di fatturazione di DNS di Azure si basa sul numero di zone DNS ospitate in DNS di Azure e sul numero di query DNS ricevute. In base all'uso sono disponibili sconti.

Per altre informazioni, vedere la pagina relativa ai [prezzi di DNS di Azure](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Qual è il contratto di servizio per DNS di Azure?

Azure garantisce che le richieste DNS valide ricevano una risposta da almeno uno dei server dei nomi DNS di Azure come minimo il 99,99% delle volte.

Per altre informazioni, vedere la pagina relativa al [contratto di Servizio per DNS di Azure](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Che cos'è una zona DNS? È la stessa cosa di un dominio DNS? 

Un dominio è un nome univoco nel Domain Name System, ad esempio contoso.com.

Una zona DNS viene usata per ospitare i record DNS per un particolare dominio. Il dominio contoso.com può contenere, ad esempio, diversi record DNS. Tali record possono includere mail.contoso.com per un server di posta e www.contoso.com per un sito Web e sono ospitati nella zona DNS contoso.com.

Un nome di dominio è *solo un nome*, mentre una zona DNS è una risorsa di dati contenente i record DNS per un nome di dominio. È possibile usare DNS di Azure per ospitare una zona DNS e gestire i record DNS per un dominio in Azure. Offre anche server dei nomi DNS per rispondere alle query DNS da Internet.

### <a name="do-i-need-to-buy-a-dns-domain-name-to-use-azure-dns"></a>È necessario acquistare un nome di dominio DNS per usare DNS di Azure? 

No.

Non è necessario acquistare un dominio per ospitare una zona DNS in DNS di Azure. È possibile creare una zona DNS in qualsiasi momento senza essere il proprietario del nome di dominio. Le query DNS per la zona vengono risolte solo se sono indirizzate ai server dei nomi DNS di Azure assegnati alla zona.

Il nome di dominio deve essere acquistato se si vuole collegare la zona DNS alla gerarchia DNS globale. Questo collegamento consente alle query DNS eseguite da qualsiasi parte del mondo di trovare la zona DNS e di ottenere risposte con i record DNS.

## <a name="azure-dns-features"></a>Funzionalità di DNS di Azure

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>Sono previste restrizioni quando si usano i record alias per un vertice di nome di dominio con Gestione traffico?

Sì. È necessario usare indirizzi IP pubblici statici con Gestione traffico di Microsoft Azure. Configurare pertanto la destinazione **endpoint esterno** con un indirizzo IP statico. 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>DNS di Azure supporta il routing del traffico basato su DNS o il failover dell'endpoint?

Il failover dell'endpoint e il routing del traffico basato su DNS vengono garantiti da Gestione traffico, un servizio di Azure separato che può essere usato con DNS di Azure. Per altre informazioni, vedere la [panoramica sulla Gestione traffico](../traffic-manager/traffic-manager-overview.md).

DNS di Azure supporta solo domini DNS di hosting statici, in cui ogni query DNS correlata a un determinato record DNS riceve sempre la stessa risposta DNS.

### <a name="does-azure-dns-support-domain-name-registration"></a>DNS di Azure supporta la registrazione del nome di dominio?

No. DNS di Azure non supporta attualmente la possibilità di acquistare i nomi di dominio. Per acquistare domini, è necessario rivolgersi a un registrar di nomi di dominio di terze parti, che addebita in genere una tariffa annuale contenuta. I domini possono quindi essere ospitati in DNS di Azure per gestire i record DNS. Per altre informazioni, vedere [Delegare un dominio a DNS di Azure](dns-domain-delegation.md).

La funzionalità per l'acquisto dei nomi di domini è disponibile nel backlog di Azure. Usare il sito dei commenti e suggerimenti per [registrare il supporto per questa funzionalità](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>DNS di Azure supporta DNSSEC?

No. DNS di Azure non supporta attualmente la funzionalità DNSSEC (Domain Name System Security Extensions).

La funzionalità DNSSEC è disponibile nel backlog di DNS di Azure. Usare il sito dei commenti e suggerimenti per [registrare il supporto per questa funzionalità](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>DNS di Azure supporta i trasferimenti di zona (AXFR/IXFR)?

No. DNS di Azure attualmente non supporta i trasferimenti di zona. Le zone DNS possono essere [importate in DNS di Azure tramite l'interfaccia della riga di comando di Azure](dns-import-export.md). I record DNS vengono gestiti tramite il [portale di gestione di DNS di Azure](dns-operations-recordsets-portal.md), l'[API REST](https://docs.microsoft.com/powershell/module/azurerm.dns), l'[SDK](dns-sdk.md), i [cmdlet di PowerShell](dns-operations-recordsets.md) o lo [strumento di interfaccia della riga di comando](dns-operations-recordsets-cli.md).

La funzionalità di trasferimento di zona è disponibile nel backlog di DNS Azure. Usare il sito dei commenti e suggerimenti per [registrare il supporto per questa funzionalità](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>DNS di Azure supporta i reindirizzamenti URL?

No. Il reindirizzamento URL non è un servizio DNS perché opera a livello HTTP anziché a livello di DNS. Alcuni provider DNS offrono un servizio di reindirizzamento URL nell'ambito dell'offerta complessiva. Tale servizio non è attualmente supportato da DNS di Azure.

La funzionalità di reindirizzamento URL è disponibile nel backlog di DNS di Azure. Usare il sito dei commenti e suggerimenti per [registrare il supporto per questa funzionalità](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

### <a name="does-azure-dns-support-the-extended-ascii-encoding-8-bit-set-for-txt-record-sets"></a>DNS di Azure supporta il set di codifica ASCII esteso (8 bit) per i set di record TXT?

Sì. DNS di Azure supporta il set di codifica ASCII esteso per i set di record TXT purché si usi la versione più recente delle API REST, degli SDK, di PowerShell e dell'interfaccia della riga di comando di Azure. Le versioni precedenti a quella del 1° ottobre 2017 o alla versione 2.1 dell'SDK non supportano il set ASCII esteso. 

Se un utente indica una stringa come il valore di un record TXT che contiene il carattere ASCII esteso \128, ad esempio "abcd\128efgh", DNS di Azure usa il valore in byte di questo carattere (ovvero 128) nella rappresentazione interna. Questo valore in byte viene restituito anche nella risposta della risoluzione DNS. Si noti inoltre che "abc" e "\097\098\099" sono interscambiabili nell'ambito della risoluzione. 

Vengono applicate le regole di escape del formato master dei file di zona [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) per i record TXT. Il carattere "\" viene sempre ignorato, ad esempio, in base alle regole RFC. Se si specifica "A\B" come valore del record TXT, la sequenza viene rappresentata e risolta solo come "AB". Se si vuole che il record TXT contenga effettivamente "A\B" al momento della risoluzione, è necessario eseguire nuovamente l'escape di "\", ovvero specificare "A\\B". 

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
- **Puntare a un altro set di record DNS all'interno della stessa zona**. I record alias possono fare riferimento ad altri set di record dello stesso tipo. È ad esempio possibile avere un set di record CNAME DNS come alias per un altro set di record CNAME dello stesso tipo. Questo approccio è utile se si vuole che solo alcuni set di record siano alias.

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

### <a name="can-i-cohost-a-domain-by-using-azure-dns-and-another-dns-provider"></a>È possibile ospitare un dominio in modalità condivisa usando DNS di Azure e un altro provider DNS?

Sì. DNS di Azure supporta i domini di hosting condiviso con altri servizi DNS.

Per configurare l'hosting condiviso, è necessario modificare i record dei server dei nomi in modo che il dominio punti ai server dei nomi di entrambi i provider. I record dei server dei nomi controllano quali provider ricevono le query DNS per il dominio. È possibile modificare tali record in DNS di Azure, nell'altro provider e nella zona padre, configurata in genere tramite il registrar di nomi di dominio. Per altre informazioni sulla delega DNS, vedere [Delega del dominio DNS](dns-domain-delegation.md).

È anche necessario verificare che i record DNS per il dominio siano sincronizzati tra i due provider DNS. DNS di Azure attualmente non supporta i trasferimenti di zona DNS e i record DNS devono essere sincronizzati tramite il [portale di gestione di DNS di Azure](dns-operations-recordsets-portal.md), l'[API REST](https://docs.microsoft.com/powershell/module/azurerm.dns), l'[SDK](dns-sdk.md), i [cmdlet di PowerShell](dns-operations-recordsets.md) o lo [strumento di interfaccia della riga di comando](dns-operations-recordsets-cli.md).

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

## <a name="private-dns"></a>DNS privato

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

### <a name="does-azure-dns-support-private-domains"></a>DNS di Azure supporta domini privati?
Il supporto per i domini privati viene implementato usando la funzionalità Zone private. Questa funzionalità è attualmente disponibile in anteprima pubblica. Le zone private vengono gestite tramite gli stessi strumenti usati per le zone DNS di Azure con connessione Internet, ma sono risolvibili solo all'interno di reti virtuali specificate. Per altre informazioni, vedere l'articolo sulla [panoramica](private-dns-overview.md).

La funzionalità Zone private non è attualmente supportata nel portale di Azure. 

Per informazioni sulle altre opzioni DNS in Azure, vedere [Risoluzione dei nomi per le risorse in reti virtuali di Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

### <a name="whats-the-difference-between-registration-virtual-network-and-resolution-virtual-network-in-the-context-of-private-zones"></a>Qual è la differenza tra rete virtuale di registrazione e rete virtuale di risoluzione nel contesto delle zone private? 
È possibile collegare le reti virtuali a una zona DNS privata come rete virtuale di registrazione o come rete virtuale di risoluzione. In entrambi i casi le macchine virtuali nella rete virtuale sono in grado di risolvere i nomi correttamente rispetto ai record nella zona privata. In una rete virtuale di registrazione i record DNS vengono registrati automaticamente nella zona per le macchine virtuali della rete. Quando una macchina virtuale in una rete virtuale di registrazione viene eliminata, anche il record DNS corrispondente della zona privata collegata viene eliminato automaticamente. 

### <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Il servizio Zone private di DNS di Azure funziona tra le diverse aree di Azure?
Sì. Il servizio Zone private è supportato per la risoluzione DNS tra reti virtuali nelle aree di Azure. Il servizio funziona anche senza peering esplicito delle reti virtuali, purché siano tutte specificate come reti virtuali di risoluzione per la zona privata. I clienti possono richiedere il peering delle reti virtuali per consentire il flusso del traffico TCP/HTTP da un'area all'altra.

### <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Per le zone private è necessario che le reti virtuali siano connesse a Internet?
No. Le zone private interagiscono con le reti virtuali e consentono ai clienti di gestire i domini per le macchine virtuali o per altre risorse all'interno o tra le reti virtuali. La connettività a Internet non è necessaria per la risoluzione dei nomi. 

### <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>È possibile usare la stessa zona privata per più reti virtuali di risoluzione? 
Sì. I clienti possono associare un massimo di 10 reti virtuali di risoluzione a una singola zona privata.

### <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-resolution-virtual-network-to-a-private-zone"></a>È possibile aggiungere a una zona privata una rete virtuale appartenente a una sottoscrizione diversa come rete virtuale di risoluzione? 
Sì. L'utente deve disporre dell'autorizzazione per le operazioni di scrittura sia nelle reti virtuali che nella zona DNS privata. L'autorizzazione di scrittura può essere assegnata a più ruoli Controllo degli accessi in base al ruolo. Il ruolo RBAC Collaboratore Rete classica, ad esempio, dispone di autorizzazioni di scrittura per le reti virtuali. Per altre informazioni sui ruoli Controllo degli accessi in base al ruolo, vedere [Che cos'è il controllo degli accessi in base al ruolo?](../role-based-access-control/overview.md).

### <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-the-virtual-machines-are-deleted-by-the-customer"></a>I record DNS di macchina virtuale registrati automaticamente in una zona privata vengono eliminati automaticamente quando il cliente elimina le macchine virtuali?
Sì. Se si elimina una macchina virtuale all'interno di una rete virtuale di registrazione, i record DNS registrati nella zona vengono eliminati automaticamente. 

### <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-registration-virtual-network-be-deleted-manually"></a>Un record di macchina virtuale registrato automaticamente in una zona privata da una rete virtuale di registrazione può essere eliminato manualmente? 
No. I record DNS di macchina virtuale registrati automaticamente in una zona privata da una rete virtuale di registrazione non sono visibili e non possono essere modificati dai clienti. È tuttavia possibile sovrascrivere i record DNS registrati automaticamente con un record DNS creato manualmente nella zona. La domanda e la risposta seguenti sono correlate a questa operazione.

### <a name="what-happens-when-we-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-registration-virtual-network"></a>Cosa accade quando si prova a creare manualmente un nuovo record DNS in una zona privata con lo stesso nome host di una macchina virtuale esistente e registrata automaticamente in una rete virtuale di registrazione? 
Se si prova a creare manualmente un nuovo record DNS in una zona privata con lo stesso nome host di una macchina virtuale esistente e registrata automaticamente in una rete virtuale di registrazione, il nuovo record DNS sovrascrive il record di macchina virtuale registrato automaticamente. Se si prova a eliminare dalla zona il record DNS creato manualmente, quest'ultimo verrà eliminato e la registrazione automatica viene ripetuta purché la macchina virtuale esista ancora e disponga di un indirizzo IP privato. A questo punto il record DNS viene creato nuovamente nella zona.

### <a name="what-happens-when-we-unlink-a-registration-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>Cosa accade quando si scollega una rete virtuale di registrazione da una zona privata? I record di macchina virtuale registrati automaticamente nella rete virtuale vengono rimossi anche dalla zona?
Sì. Per scollegare da una zona privata una rete virtuale di registrazione, si aggiorna la zona DNS per rimuovere la rete virtuale di registrazione associata. Durante questo processo i record di macchina virtuale registrati automaticamente vengono rimossi dalla zona. 

### <a name="what-happens-when-we-delete-a-registration-or-resolution-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-registration-or-resolution--virtual-network-from-the-zone"></a>Cosa accade quando si elimina una rete virtuale di registrazione o di risoluzione collegata a una zona privata? È necessario aggiornare manualmente la zona privata per scollegare la rete virtuale come rete virtuale di registrazione o di risoluzione dalla zona?
Sì. Quando si elimina una rete virtuale di registrazione o di risoluzione senza prima scollegarla da una zona privata, l'operazione di eliminazione ha esito positivo, ma la rete virtuale non viene scollegata automaticamente dalla zona privata, se presente. È necessario scollegare manualmente la rete virtuale dalla zona privata. Per questo motivo è consigliabile scollegare la rete virtuale dalla zona privata prima di eliminarla.

### <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-contosolocal-is-linked-to-a-virtual-network"></a>La risoluzione DNS tramite il nome di dominio completo (FQDN) predefinito (internal.cloudapp.net) continua a funzionare anche quando una zona privata, ad esempio contoso.local, è collegata a una rete virtuale? 
Sì. La funzionalità Zone private non sostituisce le risoluzioni DNS predefinite eseguite tramite la zona internal.cloudapp.net di Azure e viene offerta come funzionalità o aggiornamento aggiuntivo. Indipendentemente dal fatto che si usi la zona internal.cloudapp.net di Azure o la propria zona privata, è consigliabile usare il nome FQDN della zona rispetto alla quale si vuole eseguire la risoluzione. 

### <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Il suffisso DNS sulle macchine virtuali all'interno di una rete virtuale collegata deve essere sostituito con quello della zona privata? 
No. Il suffisso DNS sulle macchine virtuali nella rete virtuale collegata continua a essere quello predefinito di Azure ("*.internal.cloudapp.net"). È possibile sostituire manualmente questo suffisso nelle macchine virtuali con quello della zona privata. 

### <a name="are-there-any-limitations-for-private-zones-during-this-preview"></a>In questa versione di anteprima sono presenti limitazioni per le zone private?
Sì. Nell'anteprima pubblica sono presenti le limitazioni seguenti:
* È consentita una rete virtuale di registrazione per zona.
* È consentito un massimo di 10 reti virtuali di risoluzione per zona privata.
* Una determinata rete virtuale può essere collegata a una sola zona privata come rete virtuale di registrazione.
* Una determinata rete virtuale può essere collegata a un massimo di 10 zone private come rete virtuale di risoluzione.
* Se viene specificata una rete virtuale di registrazione, i record DNS per le macchine virtuali di tale rete registrati nella zona privata non sono visualizzabili né recuperabili tramite PowerShell, l'interfaccia della riga di comando o le API. I record di macchina virtuale sono registrati e vengono risolti in modo corretto.
* Il DNS inverso funziona solo per lo spazio IP privato nella rete virtuale di registrazione.
* Il DNS inverso per un indirizzo IP privato non registrato nella zona privata restituisce "internal.cloudapp.net" come suffisso DNS. Tale suffisso non può essere risolto. Un esempio è un indirizzo IP privato per una macchina virtuale in una rete virtuale collegata come rete virtuale di risoluzione in una zona privata.
* Quando viene collegata per la prima volta a una zona privata come rete virtuale di registrazione o di risoluzione, una rete virtuale deve essere priva di macchine virtuali con scheda di interfaccia di rete. In altre parole, la rete virtuale deve essere vuota. Questo requisito non si applica quando la rete virtuale viene successivamente collegata come rete virtuale di registrazione o di risoluzione ad altre zone private. 
* L'inoltro condizionale non è supportato, ad esempio, per abilitare la risoluzione tra reti di Azure e locali. Per informazioni su come i clienti possono sfruttare questo scenario tramite altri meccanismi, vedere [Risoluzione dei nomi per le risorse in reti virtuali di Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

### <a name="are-there-any-quotas-or-limits-on-zones-or-records-for-private-zones"></a>Sono presenti quote o limiti relativi al numero di zone o di record per le zone private?
Per le zone private non sono previsti limiti relativi né al numero di zone consentite per sottoscrizione né al numero di set di record per zona. Per le zone pubbliche e private vengono applicati i limiti complessivi relativi a DNS. Per altre informazioni, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md#dns-limits).

### <a name="is-there-portal-support-for-private-zones"></a>È disponibile il supporto del portale per le zone private?
Le zone private già create tramite API, PowerShell, l'interfaccia della riga di comando e gli SDK sono visibili nel portale di Azure, ma i clienti non possono né creare nuove zone private né gestire le associazioni con le reti virtuali. Per le reti virtuali associate come reti virtuali di registrazione, i record di macchina virtuale registrati automaticamente non sono visibili nel portale. 

## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è DNS di Azure?](dns-overview.md)
<br>
- [Usare DNS di Azure per i domini privati](private-dns-overview.md)
<br>
- [Panoramica delle zone e dei record DNS](dns-zones-records.md)
<br>
- [Introduzione a DNS di Azure](dns-getstarted-portal.md)

