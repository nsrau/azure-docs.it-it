---
title: Domande frequenti su DNS di Azure | Microsoft Docs
description: Domande frequenti su DNS di Azure
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
editor: 
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2017
ms.author: jonatul
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: f365574a12047f6952209dc3883af32a2e9ecd1e
ms.lasthandoff: 04/22/2017

---

# <a name="azure-dns-faq"></a>Domande frequenti su DNS di Azure

## <a name="about-azure-dns"></a>DNS di Azure

### <a name="what-is-azure-dns"></a>Che cos'è DNS di Azure?

Il nome DNS (Domain Name System) è responsabile della conversione (o risoluzione) del nome di un sito Web o del servizio nel relativo indirizzo IP. DNS di Azure è un servizio di hosting per i domini DNS, che fornisce la risoluzione dei nomi usando l'infrastruttura di Microsoft Azure. Ospitando i domini in Azure, è possibile gestire i record DNS usando le stesse credenziali, API, strumenti e fatturazione come per gli altri servizi Azure.

I domini DNS nel servizio DNS di Azure sono ospitati nella rete globale di Azure dei server dei nomi DNS. Usiamo le reti Anycast, in modo che ogni query DNS riceva una risposta dal server DNS disponibile più vicino. Ciò consente di accelerare le prestazioni e ottenere la disponibilità elevata per il dominio.

Il servizio DNS di Azure è basato su Azure Resource Manager. Usufruisce quindi delle funzionalità di Resource Manager, come il controllo degli accessi in base al ruolo, i log di controllo e il blocco delle risorse. I domini e i record possono essere gestiti tramite il portale di Azure, i cmdlet di Azure PowerShell e l'interfaccia della riga di comando di Azure. Le applicazioni che richiedono la gestione automatica di DNS possono essere integrate con il servizio tramite l'API REST e gli SDK.

### <a name="how-much-does-azure-dns-cost"></a>Quanto costa DNS di Azure?

Il modello di fatturazione di DNS di Azure si basa sul numero di zone DNS ospitate in DNS di Azure e sul numero di query DNS ricevute. In base all'uso sono disponibili sconti.

Per altre informazioni, vedere la pagina relativa ai [prezzi di DNS di Azure](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Qual è il contratto di servizio per DNS di Azure?

Microsoft garantisce che le richieste DNS valide riceveranno una risposta da almeno uno dei server dei nomi DNS di Azure almeno il 99,99% delle volte.

Per altre informazioni, vedere la pagina relativa al [contratto di Servizio per DNS di Azure](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Che cos'è una "zona DNS"? È la stessa cosa di un dominio DNS? 

Un dominio è un nome univoco nel Domain Name System, ad esempio "contoso.com".

Una zona DNS viene usata per ospitare i record DNS per un particolare dominio. Ad esempio, il dominio "contoso.com" può contenere diversi record DNS, come "mail.contoso.com" (per un server di posta) e "www.contoso.com" (per un sito Web). Questi verrebbero ospitati nella zona DNS "contoso.com".

Un nome di dominio è *solo un nome*, mentre una zona DNS è una risorsa di dati contenente i record DNS per un nome di dominio. DNS di Azure consente di ospitare una zona DNS e gestire i record DNS per un dominio in Azure. Offre anche server dei nomi DNS per rispondere alle query DNS da Internet.

### <a name="do-i-need-to-purchase-a-dns-domain-name-to-use-azure-dns"></a>Devo acquistare un nome di dominio DNS per usare DNS di Azure? 

Non necessariamente.

Non è necessario acquistare un dominio per ospitare una zona DNS in DNS di Azure. È possibile creare una zona DNS in qualsiasi momento senza essere il proprietario del nome di dominio. Le query DNS per la zona verranno risolte solo se sono indirizzate ai server di nomi DNS di Azure assegnato alla zona.

È necessario acquistare il nome di dominio se si desidera collegare la zona DNS nella gerarchia DNS globale, per permettere alle query DNS eseguite da qualsiasi parte del mondo di trovare la zona DNS e ottenere risposte con i record DNS.

## <a name="azure-dns-features"></a>Funzionalità DNS di Azure

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>DNS di Azure supporta il routing del traffico basato su DNS o il failover dell'endpoint?

Il failover dell'endpoint e il routing del traffico basato su DNS vengono garantiti da Gestione traffico di Azure. Si tratta di un servizio di Azure separato che può essere usato insieme a DNS di Azure. Per altre informazioni, vedere la [panoramica sulla Gestione traffico](../traffic-manager/traffic-manager-overview.md).

DNS di Azure supporta solo domini DNS di Traffic Manager "statici", in cui ogni query DNS per un determinato record DNS riceve sempre la stessa risposta DNS.

### <a name="does-azure-dns-support-domain-name-registration"></a>DNS di Azure supporta la registrazione del nome di dominio?

No. DNS di Azure attualmente non supporta l'acquisto dei nomi di dominio. Se si vogliono acquistare domini, è necessario ricorrere a un registrar di terze parti, che addebita in genere una tariffa annuale contenuta. I domini possono quindi essere ospitati in DNS di Azure per la gestione dei record DNS. Per informazioni dettagliate, vedere [Delegare un dominio a DNS di Azure](dns-domain-delegation.md) .

Questa è una funzionalità che Microsoft sta verificando nel backlog. È possibile usare il sito per i commenti per [registrare il supporto per questa funzionalità](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-private-domains"></a>DNS di Azure supporta domini "privati"?

No. DNS di Azure attualmente supporta solo i domini con connessione Internet.

Questa è una funzionalità che Microsoft sta verificando nel backlog. È possibile usare il sito per i commenti per [registrare il supporto per questa funzionalità](https://feedback.azure.com/forums/217313-networking/suggestions/10737696-enable-split-dns-for-providing-both-public-and-int).

Per informazioni sulle opzioni DNS interne in Azure, vedere [Risoluzione dei nomi per le macchine virtuali e le istanze del ruolo](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

### <a name="does-azure-dns-support-dnssec"></a>DNS di Azure supporta DNSSEC?

No. DNS di Azure attualmente non supporta DNSSEC.

Questa è una funzionalità che Microsoft sta verificando nel backlog. È possibile usare il sito per i commenti per [registrare il supporto per questa funzionalità](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>DNS di Azure supporta i trasferimenti di zona (AXFR/IXFR)?

No. DNS di Azure attualmente non supporta i trasferimenti di zona. Le zone DNS possono essere [importate in DNS di Azure tramite l'interfaccia della riga di comando di Azure](dns-import-export.md). I record DNS possono quindi essere gestiti tramite il [portale di gestione di DNS di Azure](dns-operations-recordsets-portal.md), l'[API REST](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), i [cmdlet di PowerShell](dns-operations-recordsets.md) o lo [strumento dell'interfaccia della riga di comando](dns-operations-recordsets-cli.md).

Questa è una funzionalità che Microsoft sta verificando nel backlog. È possibile usare il sito per i commenti per [registrare il supporto per questa funzionalità](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>DNS di Azure supporta i reindirizzamenti URL?

No. Il servizio di reindirizzamento URL non è attualmente un servizio DNS, poiché opera al livello HTTP, piuttosto che a livello di DNS. Alcuni provider DNS offrono anche un servizio di reindirizzamento dell'URL come parte dell'offerta complessiva. Questo servizio non è attualmente supportato da DNS di Azure.

Questa funzionalità viene rilevata nel backlog. È possibile usare il sito per i commenti per [registrare il supporto per questa funzionalità](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

## <a name="using-azure-dns"></a>Uso di DNS di Azure

### <a name="can-i-co-host-a-domain-using-azure-dns-and-another-dns-provider"></a>È possibile ospitare un dominio usando DNS di Azure e un altro provider DNS?

Sì. DNS di Azure supporta i domini di hosting condiviso con altri servizi DNS.

A tale scopo, è necessario modificare i record NS per il dominio, che controlla i provider che ricevono le query DNS per il dominio, in modo da puntare ai server dei nomi di entrambi i provider. I record NS devono essere modificati in 3 posizioni: in DNS di Azure, nell'altro provider e nella zona padre, in genere configurata tramite il registar. Per altre informazioni sulla delega DNS, vedere [Delega del dominio DNS](dns-domain-delegation.md).

È anche necessario assicurarsi che i record DNS per il dominio siano sincronizzati tra i due provider DNS. DNS di Azure attualmente non supporta i trasferimenti di zona DNS. È necessario sincronizzare i record DNS usando [il portale di gestione di DNS di Azure](dns-operations-recordsets-portal.md), l'[API REST](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), i [cmdlet di PowerShell](dns-operations-recordsets.md) o lo [strumento dell'interfaccia della riga di comando](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-4-azure-dns-name-servers"></a>È necessario delegare il dominio a tutti e 4 i server dei nomi DNS di Azure?

Sì. DNS di Azure assegna 4 server dei nomi per ogni zona DNS, per isolare gli errori e aumentare la resilienza. Per qualificarsi per il contratto di servizio DNS di Azure, è necessario delegare il dominio a tutti e 4 i server dei nomi.

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

### <a name="how-do-i-set-up-an-international-domain-name-idn-in-azure-dns"></a>Come configurare un nome di dominio internazionale, IDN, in DNS di Azure?

I nomi di dominio internazionali, IDN, usano la codifica per ogni nome DNS con "[punycode](https://en.wikipedia.org/wiki/Punycode)". Le query DNS vengono create usando i nomi con codifica punycode.

È possibile configurare i nomi di dominio internazionale, IDN, in DNS di Azure prima di convertire il nome della zona o il nome del set di record in punycode. DNS di Azure non supporta attualmente la conversione incorporata da e verso punycode.

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni su DNS di Azure](dns-overview.md)
<br>
[Altre informazioni su zone e record DNS](dns-zones-records.md)
<br>
[Introduzione a DNS di Azure](dns-getstarted-portal.md)


