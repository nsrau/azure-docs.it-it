---
title: Panoramica delle zone e dei record DNS - DNS di Azure | Documentazione Microsoft
description: Panoramica del supporto per l&quot;hosting di zone e record DNS in DNS di Microsoft Azure.
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
editor: 
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: jonatul
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: f15654f621bafb2617bdb456bbda0233db656be5
ms.lasthandoff: 04/22/2017

---

# <a name="overview-of-dns-zones-and-records"></a>Panoramica delle zone e dei record DNS

Questa pagina presenta i concetti principali relativi a domini, zone DNS e record e set di record DNS, nonché il modo in cui vengono supportati in DNS di Azure.

## <a name="domain-names"></a>Nomi di dominio

Domain Name System è una gerarchia di domini. La gerarchia inizia dal dominio "radice", il cui nome è semplicemente "**.**",  seguito dai domini di primo livello, come "com", "net", "org", "uk" o "jp",  e quindi dai domini di secondo livello, come "org.uk" o "co.jp" I domini nella gerarchia DNS vengono distribuiti a livello globale, ospitati dai server dei nomi DNS in tutto il mondo.

Un registrar è un'organizzazione che consente di acquistare un nome di dominio, ad esempio "contoso.com".  L'acquisto di un nome di dominio comporta il diritto di controllare la gerarchia DNS con questo nome, permettendo ad esempio di indirizzare il nome "www.contoso.com" al sito Web della società. Il registrar può ospitare il dominio nei propri server dei nomi per conto dell'utente o consentire all'utente di specificare server dei nomi alternativi.

DNS di Azure offre un'infrastruttura di server dei nomi a disponibilità elevata e distribuita a livello globale, che può essere usata per ospitare il dominio. Ospitando i domini in DNS di Azure, è possibile gestire i record DNS usando gli stessi strumenti, credenziali, API, fatturazione e supporto degli altri servizi di Azure.

DNS di Azure attualmente non supporta l'acquisto dei nomi di dominio. Se si vuole acquistare un nome di dominio, è necessario ricorrere a un registrar di terze parti. Il registrar addebita in genere un prezzo annuo ridotto. I domini possono quindi essere ospitati in DNS di Azure per la gestione dei record DNS. Per informazioni dettagliate, vedere [Delegare un dominio a DNS di Azure](dns-domain-delegation.md) .

## <a name="dns-zones"></a>Zone DNS

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>Record DNS

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Time-To-Live

La durata Time-to-Live o TTL specifica per quanto tempo ogni record viene memorizzato nella cache da parte dei client, prima che venga eseguita nuovamente la query. Nell'esempio precedente, il valore TTL è 3600 secondi o 1 ora.

Poiché in DNS di Azure il valore TTL viene specificato per il set di record e non per ogni record, viene usato lo stesso valore per tutti i record all'interno del set di record.  È possibile specificare qualsiasi valore TTL compreso tra 1 e 2.147.483.647 secondi.

### <a name="wildcard-records"></a>Record con caratteri jolly

DNS di Azure supporta [record con caratteri jolly](https://en.wikipedia.org/wiki/Wildcard_DNS_record). I record con caratteri jolly vengono restituiti in risposta a qualsiasi query con un nome corrispondente, a meno che non esista una corrispondenza più vicina con un set di record non con caratteri jolly. DNS di Azure supporta set di record con caratteri jolly per tutti i tipi di record tranne NS e SOA.

Per creare un set di record con caratteri jolly, usare il nome del set di record "\*". In alternativa, è anche possibile usare un nome con "\*" come etichetta più a sinistra, ad esempio "\*.foo".

### <a name="cname-records"></a>Record CNAME

I set di record CNAME non possono coesistere con altri set di record con lo stesso nome. Non è ad esempio possibile creare contemporaneamente un set di record CNAME con il nome relativo "www" e un record A con il nome relativo '"www".

Dal momento che il vertice della zona (name = '@') contiene sempre i set di record NS e SOA creati quando è stata creata la zona, non è possibile creare un set di record CNAME al vertice della zona.

Questi vincoli derivano dagli standard DNS e non sono limitazioni di DNS di Azure.

### <a name="ns-records"></a>Record NS

Un set di record NS viene creato automaticamente in corrispondenza del vertice di ogni zona (nome = '@') e viene eliminato automaticamente quando viene eliminata la zona; non può essere eliminato separatamente.  È possibile modificare il valore TTL di questo set di record, ma non i record, che sono preconfigurati in modo da fare riferimento ai server dei nomi DNS di Azure assegnati alla zona.

È possibile creare ed eliminare altri record NS nella zona, ma non nel vertice della zona.  In questo modo, è possibile configurare zone figlio. Vedere [Delegare un dominio al servizio DNS di Azure](dns-domain-delegation.md).

### <a name="soa-records"></a>Record SOA

Un set di record SOA viene creato automaticamente in corrispondenza del vertice di ogni zona (nome = '@') e viene eliminato automaticamente quando viene eliminata la zona.  I record SOA non possono essere creati o eliminati separatamente.

È possibile modificare tutte le proprietà del record SOA ad eccezione della proprietà "host", che è preconfigurata in modo da fare riferimento al nome del server dei nomi primario fornito da DNS di Azure.

### <a name="spf-records"></a>Record SPF

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>Record SRV

I [record SRV](https://en.wikipedia.org/wiki/SRV_record) vengono usati da svariati servizi per specificare i percorsi dei server. Quando si specifica un record SRV in DNS di Azure:

* Il *servizio* e il *protocollo* devono essere specificati come parte del nome del set di record, preceduti da caratteri di sottolineatura.  Ad esempio, "\_sip.\_tcp.name".  Per un record nel vertice della zona non è necessario specificare '@' nel nome del record, ma è sufficiente usare il servizio e il protocollo, ad esempio '\_sip.\_tcp'.
* La *priorità*, il *peso*, la *porta* e la *destinazione* vengono specificati come parametri di ogni record nel set di record.

### <a name="txt-records"></a>Record TXT

I record TXT vengono usati per eseguire il mapping di nomi di dominio a stringhe di testo arbitrarie. Sono usati in più applicazioni, in particolare quelle correlate alla configurazione della posta elettronica, ad esempio [Sender Policy Framework (SPF)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) e [DomainKeys Identified Mail (DKIM)](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail).

Gli standard DNS consentono a un singolo record TXT di contenere più stringhe, ognuna con una lunghezza massima di 254 caratteri. Se sono usate più stringhe, vengono concatenate dai client e considerate come stringa singola.

Quando si chiama l'API REST del servizio DNS di Azure, è necessario specificare ogni stringa TXT separatamente.  Quando si usano le interfacce del portale di Azure, di PowerShell o l'interfaccia della riga di comando, è consigliabile specificare una sola stringa per record, che viene automaticamente divisa in segmenti di 254 caratteri, se necessario.

Le stringhe multiple in un record DNS non devono essere confuse con i record TXT multipli in un set di record TXT.  Un set di record TXT può contenere più record, *ognuno dei quali* può contenere più stringhe.  Il servizio DNS di Azure supporta una lunghezza massima totale delle stringhe pari a 1024 caratteri in ogni set di record TXT (in tutti i record combinati).

## <a name="tags-and-metadata"></a>Tag e metadati

### <a name="tags"></a>Tag

I tag sono un elenco di coppie nome-valore usate da Azure Resource Manager per etichettare le risorse.  Azure Resource Manager usa i tag per abilitare visualizzazioni filtrate della fattura di Azure e permette anche di impostare criteri sui tag necessari. Per altre informazioni sui tag, vedere [Uso dei tag per organizzare le risorse di Azure](../azure-resource-manager/resource-group-using-tags.md).

DNS di Azure supporta l'uso di tag di Azure Resource Manager in risorse di zona DNS.  Non supporta i tag nei set di record DNS, anche se in alternativa sono supportati i "metadati", come illustrato di seguito.

### <a name="metadata"></a>Metadati

In alternativa ai tag dei set di record, DNS di Azure supporta l'annotazione dei set di report tramite "metadati".  Analogamente ai tag, i metadati permettono di associare coppie nome-valore a ogni set di record.  Questo approccio può essere utile, ad esempio, per registrare lo scopo di ogni set di record.  Diversamente dai tag, i metadati non possono essere usati per fornire una visualizzazione filtrata della fattura di Azure e non possono essere specificati in criteri di Azure Resource Manager.

## <a name="etags"></a>ETag

Si supponga che due persone o due processi provino a modificare un record DNS nello stesso momento. Quale prevale? E quello che prevale sa di avere sovrascritto le modifiche create da qualcun altro?

DNS di Azure usa gli Etag per gestire in modo sicuro le modifiche simultanee alla stessa risorsa. Gli ETag sono separati dai ["tag" di Azure Resource Manager](#tags). Ogni risorsa DNS (zona o set di record) ah un Etag associato. Quando viene recuperata una risorsa, viene recuperato anche il relativo valore Etag. Quando si aggiorna una risorsa, è possibile scegliere di passare nuovamente il valore ETag in modo che DNS di Azure possa verificare che corrisponda al valore ETag sul server. Dal momento che ogni aggiornamento di una risorsa comporta la rigenerazione dell'Etag, una mancata corrispondenza del valore Etag indica che si è verificata una modifica simultanea. Gli ETag possono essere usati anche quando si crea una nuova risorsa per garantire che questa non esista già.

Per impostazione predefinita, PowerShell in DNS di Azure usa gli Etag per bloccare le modifiche simultanee a zone e a set di record. L'opzione facoltativa *-Overwrite* può essere usata per disattivare i controlli di ETag e in questo caso le eventuali modifiche simultanee vengono sovrascritte.

A livello dell'API REST di DNS di Azure, gli ETag vengono specificati usando le intestazioni HTTP.  Il relativo comportamento è illustrato nella tabella seguente:

| Intestazione | Comportamento |
| --- | --- |
| None |PUT riesce sempre (nessun controllo di Etag) |
| If-Match <etag> |PUT riesce solo se la risorsa esiste e l'Etag corrisponde |
| If-match * |PUT riesce solo se la risorsa esiste |
| If-none-match * |PUT riesce solo se la risorsa non esiste |


## <a name="limits"></a>Limiti

Quando si usa DNS di Azure, si applicano i limiti predefiniti seguenti:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a usare DNS di Azure, è necessario sapere come [creare una zona DNS](dns-getstarted-create-dnszone-portal.md) e come [creare record DNS](dns-getstarted-create-recordset-portal.md).
* Per eseguire la migrazione di una zona DNS esistente, è necessario saper [importare ed esportare un file di zona DNS](dns-import-export.md).

