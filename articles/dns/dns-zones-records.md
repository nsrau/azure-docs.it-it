---
title: Zone e record DNS | Documentazione Microsoft
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
ms.workload: infrastructure-services
ms.date: 10/17/2016
ms.author: jtuliani
translationtype: Human Translation
ms.sourcegitcommit: 42d47741e414b2de177f1fd75b3e1ac3fde96579
ms.openlocfilehash: b99bc20b66787c7a87fa49143b4cbf861e0e68a2

---

# <a name="dns-zones-and-records"></a>Zone e record DNS

Questa pagina presenta i concetti principali relativi a domini, zone DNS e record e set di record DNS, nonché il modo in cui vengono supportati in DNS di Azure.

## <a name="domain-names"></a>Nomi di dominio

Domain Name System è una gerarchia di domini. La gerarchia inizia dal dominio "radice", il cui nome è semplicemente "**.**",  seguito dai domini di primo livello, come "com", "net", "org", "uk" o "jp",  e quindi dai domini di secondo livello, come "org.uk" o "co.jp" I domini nella gerarchia DNS vengono distribuiti a livello globale, ospitati dai server dei nomi DNS in tutto il mondo.

Un registrar è un'organizzazione che consente di acquistare un nome di dominio, ad esempio "contoso.com".  L'acquisto di un nome di dominio comporta il diritto di controllare la gerarchia DNS con questo nome, permettendo ad esempio di indirizzare il nome "www.contoso.com" al sito Web della società. Il registrar può ospitare il dominio nei propri server dei nomi per conto dell'utente o, in alternativa, l'utente può specificare server dei nomi alternativi.

DNS di Azure offre un'infrastruttura di server dei nomi a disponibilità elevata e distribuita a livello globale, che può essere usata per ospitare il dominio. Ospitando i domini in DNS di Azure, è possibile gestire i record DNS usando gli stessi strumenti, credenziali, API, fatturazione e supporto degli altri servizi di Azure.

DNS di Azure attualmente non supporta l'acquisto dei nomi di dominio. Se si vuole acquistare un nome di dominio, è necessario ricorrere a un registrar di terze parti. Il registrar in genere addebiterà un prezzo annuo irrisorio. I domini possono quindi essere ospitati in DNS di Azure per la gestione dei record DNS. Per informazioni dettagliate, vedere [Delegare un dominio a DNS di Azure](dns-domain-delegation.md) .

## <a name="dns-zones"></a>Zone DNS

Una zona DNS viene usata per ospitare i record DNS per un particolare dominio. Per iniziare a ospitare il dominio in DNS di Azure, è necessario creare una zona DNS per il nome di dominio. Ogni record DNS per il dominio viene quindi creato all'interno di questa zona DNS.

Il dominio "contoso.com", ad esempio, può contenere diversi record DNS, come "mail.contoso.com" (per un server di posta) e "www.contoso.com" (per un sito Web).

Quando si crea una zona DNS in DNS di Azure, il nome della zona deve essere univoco all'interno del gruppo di risorse. Lo stesso nome di zona può essere usato di nuovo in un gruppo di risorse diverso o in un'altra sottoscrizione di Azure. Se più zone condividono lo stesso nome, a ogni istanza vengono assegnati indirizzi di server dei nomi diversi. È possibile configurare solo un set di indirizzi con il registrar.

> [!NOTE]
> Non è necessario essere proprietari di un dominio per creare una zona DNS con questo nome di dominio in DNS di Azure, ma è necessario essere proprietari del dominio per configurare i server dei nomi DNS di Azure come server dei nomi corretti per il nome di dominio con il registrar.

Per altre informazioni, vedere [Delegare un dominio a DNS di Azure](dns-domain-delegation.md).

## <a name="dns-records"></a>Record DNS

### <a name="record-types"></a>Tipi di record

Ogni record DNS ha un nome e un tipo. I record sono organizzati in tipi diversi in base ai dati che contengono. Il tipo più comune è il record "A", che esegue il mapping di un nome a un indirizzo IPv4. Un altro tipo comune è il record "MX", che esegue il mapping di un nome a un server di posta.

DNS di Azure supporta tutti i tipi di record DNS comuni: A, AAAA, CNAME, MX, NS, PTR, SOA, SRV e TXT.

### <a name="record-names"></a>Nomi dei record

Nel servizio DNS di Azure i record vengono specificati usando nomi relativi. Un nome di dominio *completo* (FQDN) include il nome della zona, mentre un nome *relativo* no. Ad esempio, il nome di record relativo "www" nella zona "contoso.com" genera il nome di record completo "www.contoso.com".

Un record *vertice* è un record DNS alla radice (o *vertice*) di una zona DNS. Ad esempio, nella zona DNS "contoso.com", anche un record vertice ha il nome completo "contoso.com", chiamato a volte dominio di tipo *naked*.  Per convenzione, per usare record vertice viene usato il nome relativo '@'.

### <a name="record-sets"></a>Set di record

In alcuni casi è necessario creare più record DNS con un determinato nome e tipo. Si supponga, ad esempio, che il sito Web "www.contoso.com" sia ospitato in due diversi indirizzi IP. Questo sito Web richiede due diversi record A, uno per ogni indirizzo IP. Ecco un esempio di un set di record:

    www.contoso.com.        3600    IN    A    134.170.185.46
    www.contoso.com.        3600    IN    A    134.170.188.221

DNS di Azure gestisce i record DNS tramite *set di record*. Un set di record, chiamato anche set di record di *risorse* è la raccolta di record DNS che hanno lo stesso nome e sono dello stesso tipo in una zona. La maggior parte dei set di record contiene un singolo record, ma non sono rari esempi come questo in cui un set di record contiene più di un record

Si supponga, ad esempio, di aver già creato un record "www" nella zona "contoso.com", che punta all'indirizzo IP "134.170.185.46" (primo record sopra).  Per creare il secondo record, è necessario aggiungere il record al set di record esistente invece di creare un nuovo set di record.

I tipi di record SOA e CNAME sono eccezioni. Gli standard DNS non permettono più record con lo stesso nome per questi tipi, quindi questi set di record possono contenere un solo record.

### <a name="time-to-live"></a>Time-To-Live

La durata Time-to-Live o TTL specifica per quanto tempo ogni record viene memorizzato nella cache da parte dei client, prima che venga eseguita nuovamente la query. Nell'esempio precedente, il valore TTL è 3600 secondi o 1 ora.

Poiché in DNS di Azure il valore TTL viene specificato per il set di record e non per ogni record, viene usato lo stesso valore per tutti i record all'interno del set di record.  È possibile specificare qualsiasi valore TTL compreso tra 1 e 2.147.483.647 secondi.

### <a name="wildcard-records"></a>Record con caratteri jolly

DNS di Azure supporta [record con caratteri jolly](https://en.wikipedia.org/wiki/Wildcard_DNS_record). I record con caratteri jolly vengono restituiti in risposta a qualsiasi query con un nome corrispondente, a meno che non esista una corrispondenza più vicina con un set di record non con caratteri jolly. I set di record con caratteri jolly sono supportati per tutti i tipi di record tranne NS e SOA.

Per creare un set di record con caratteri jolly, usare il nome del set di record "\*". In alternativa, è anche possibile usare un nome con "\*" come etichetta più a sinistra, ad esempio "\*.foo".

### <a name="cname-records"></a>Record CNAME

I set di record CNAME non possono coesistere con altri set di record con lo stesso nome. Non è ad esempio possibile creare contemporaneamente un set di record CNAME con il nome relativo "www" e un record A con il nome relativo '"www".

Dal momento che il vertice della zona (name = '@') contiene sempre i set di record NS e SOA creati quando è stata creata la zona, non è possibile creare un set di record CNAME al vertice della zona.

Questi vincoli derivano dagli standard DNS e non sono limitazioni di DNS di Azure.

### <a name="ns-records"></a>Record NS

Un set di record NS viene automaticamente creato in corrispondenza del vertice di ogni zona (nome = '@'),) e viene automaticamente eliminato quando viene eliminata la zona (non può essere eliminato separatamente).  È possibile modificare il valore TTL di questo set di record, ma non i record, che sono preconfigurati in modo da fare riferimento ai server dei nomi DNS di Azure assegnati alla zona.

È possibile creare ed eliminare altri record NS all'interno della zona, ma non nel vertice della zona.  In questo modo, è possibile configurare zone figlio. Vedere [Delegare un dominio al servizio DNS di Azure](dns-domain-delegation.md#delegating-sub-domains-in-azure-dns).

### <a name="soa-records"></a>Record SOA

Un set di record SOA viene automaticamente creato in corrispondenza del vertice di ogni zona (nome = '@'),) e viene automaticamente eliminato quando viene eliminata la zona.  I record SOA non possono essere creati o eliminati separatamente.

È possibile modificare tutte le proprietà del record SOA ad eccezione della proprietà "host", che è preconfigurata in modo da fare riferimento al nome del server dei nomi primario fornito da DNS di Azure.

### <a name="spf-records"></a>Record SPF

I record Sender Policy Framework (SPF) vengono usati per specificare i server di posta elettronica permessi per l'invio di posta elettronica per conto di un nome di dominio specifico.  La configurazione corretta dei record SPF è importante per impedire ai destinatari di contrassegnare i messaggi di posta elettronica come posta indesiderata.

I documenti RFC di DNS in origine hanno introdotto un nuovo tipo di record "SPF" per supportare questo scenario. Per supportare server dei nomi meno recenti, hanno anche permesso l'uso del tipo di record TXT per specificare record SPF.  Questa ambiguità ha prodotto una certa confusione, che è stata risolta dal documento [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1).  Questo documento specifica che i record SPF devono essere creati solo usando il tipo di record TXT e che il tipo di record SPF è deprecato.

**I record SPF sono supportati da DNS di Azure e devono essere creati usando il tipo di record TXT.** Il tipo di record SPF obsoleto non è supportato. Quando si [importa un file di zona DNS](dns-import-export.md), qualsiasi record SPF che usa il tipo di record SPF viene convertito in tipo di record TXT.

### <a name="srv-records"></a>Record SRV

I [record SRV](https://en.wikipedia.org/wiki/SRV_record) vengono usati da svariati servizi per specificare i percorsi dei server. Quando si specifica un record SRV in DNS di Azure:

* Il *servizio* e il *protocollo* devono essere specificati come parte del nome del set di record, preceduti da caratteri di sottolineatura.  Ad esempio, "\_sip.\_tcp.name".  Per un record nel vertice della zona, non è necessario specificare '@' nel nome del record, ma è sufficiente usare il servizio e il protocollo, ad esempio "\_sip.\_tcp".
* La *priorità*, il *peso*, la *porta* e la *destinazione* vengono specificati come parametri di ogni record nel set di record.

## <a name="tags-and-metadata"></a>Tag e metadati

### <a name="tags"></a>Tag

I tag sono un elenco di coppie nome-valore usate da Azure Resource Manager per etichettare le risorse.  Azure Resource Manager usa i tag per abilitare visualizzazioni filtrate della fattura di Azure e permette anche di impostare criteri sui tag necessari. Per altre informazioni sui tag, vedere [Uso dei tag per organizzare le risorse di Azure](../resource-group-using-tags.md).

DNS di Azure supporta l'uso di tag di Azure Resource Manager in risorse di zona DNS.  Non supporta i tag nei set di record DNS.

### <a name="metadata"></a>Metadati

In alternativa ai tag dei set di record, DNS di Azure supporta l'annotazione dei set di report tramite "metadati".  Analogamente ai tag, i metadati permettono di associare coppie nome-valore a ogni set di record.  Questo approccio può essere utile, ad esempio, per registrare lo scopo di ogni set di record.  Diversamente dai tag, i metadati non possono essere usati per fornire una visualizzazione filtrata della fattura di Azure e non possono essere specificati in criteri di Azure Resource Manager.

## <a name="limits"></a>Limiti

Quando si usa DNS di Azure, si applicano i limiti predefiniti seguenti:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a usare DNS di Azure, è necessario sapere come [creare una zona DNS](dns-getstarted-create-dnszone-portal.md) e come [creare record DNS](dns-getstarted-create-recordset-portal.md).
* Per eseguire la migrazione di una zona DNS esistente, è necessario saper [importare un file di zona DNS](dns-import-export.md).




<!--HONumber=Nov16_HO3-->


