---
title: Panoramica del DNS inverso in Azure | Microsoft Docs
description: Informazioni sul funzionamento del DNS inverso e su come usarlo in Azure
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: jonatul
ms.openlocfilehash: 70a1ad070e812951fca3d2b19da12c67f0725dd0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>Panoramica del DNS inverso e supporto in Azure

Questo articolo fornisce una panoramica del funzionamento del DNS inverso e degli scenari relativi al DNS inverso supportati in Azure.

## <a name="what-is-reverse-dns"></a>Che cos'è il DNS inverso?

I record DNS convenzionali consentono un mapping da un nome DNS (ad esempio, "www.contoso.com") a un indirizzo IP (ad esempio, 64.4.6.100).  Il DNS inverso consente la riconversione di un indirizzo IP (64.4.6.100) in un nome ("www.contoso.com").

I record DNS inversi vengono usati in varie situazioni. I record DNS inversi, ad esempio, sono molto usati per combattere l'invio di posta indesiderata verificando il mittente di un messaggio di posta elettronica.  Il server di posta di destinazione recupera il record DNS inverso dell'indirizzo IP del server di origine e verifica che l'host sia autorizzato a inviare posta elettronica dal dominio di origine. 

## <a name="how-reverse-dns-works"></a>Come funziona il DNS inverso

I record DNS inversi sono ospitati in speciali zone DNS, chiamate zone "ARPA".  Queste zone costituiscono una gerarchia DNS separata parallela alla normale gerarchia che ospita domini come "contoso.com".

Il record DNS "www.contoso.com", ad esempio, viene implementato usando un record "A" DNS con il nome "www" nella zona "contoso.com".  Questo record A fa riferimento all'indirizzo IP corrispondente, in questo caso 64.4.6.100.  La ricerca inversa viene implementata separatamente, usando un record "PTR" denominato "100" nella zona "6.4.64.in-addr.arpa". Si noti che gli indirizzi IP sono inversi nelle zone ARPA.  Questo record PTR, se è stato configurato correttamente, fa riferimento al nome "www.contoso.com".

Quando a un'organizzazione viene assegnato un blocco di indirizzi IP, acquisisce anche il diritto di gestire la zona ARPA corrispondente. Le zone ARPA corrispondenti ai blocchi di indirizzi IP usati da Azure vengono ospitate e gestite da Microsoft. L'ISP può ospitare per l'utente la zona ARPA per gli indirizzi IP o può consentire di ospitare la zona ARPA nel servizio DNS preferito, ad esempio DNS di Azure.

> [!NOTE]
> Le ricerche DNS dirette e le ricerche DNS inverse vengono implementate in gerarchie DNS separate parallele. La ricerca inversa per "www.contoso.com" **non** è ospitata nella zona "contoso.com", ma nella zona ARPA per il blocco di indirizzi IP corrispondente. Per i blocchi di indirizzi IPv4 e IPv6 vengono usate zone separate.

### <a name="ipv4"></a>IPv4

Il nome di una zona di ricerca inversa di tipo IPv4 deve avere il formato seguente: `<IPv4 network prefix in reverse order>.in-addr.arpa`.

Ad esempio, quando si crea una zona inversa per ospitare i record per gli host con indirizzi IP con prefisso 192.0.2.0/24, il nome della zona viene creato tramite l'isolamento del prefisso di rete dell'indirizzo (192.0.2), quindi l'inversione dell'ordine(2.0.192) e l'aggiunta del suffisso `.in-addr.arpa`.

|Classe di subnet|Prefisso di rete  |Prefisso di rete inverso  |Suffisso standard  |Nome della zona inversa |
|-------|----------------|------------|-----------------|---------------------------|
|Classe A|203.0.0.0/8     | 203        | .in-addr.arpa   | `203.in-addr.arpa`        |
|Classe B|198.51.0.0/16   | 51.198     | .in-addr.arpa   | `51.198.in-addr.arpa`     |
|Classe C|192.0.2.0/24    | 2.0.192    | .in-addr.arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>Delega IPv4 senza classi

In alcuni casi l'intervallo di indirizzi IP allocato a un'organizzazione è inferiore a un intervallo di tipo Classe C (/24). In questo caso l'intervallo di indirizzi IP non è compreso in un limite di zona entro la gerarchia di zone `.in-addr.arpa` e quindi non può essere delegato come zona figlio.

Viene invece usato un meccanismo diverso per trasferire il controllo dei singoli record di ricerca inversa (PTR) in una zona DNS dedicata. Questo meccanismo delega una zona figlio per ogni intervallo di indirizzi IP, quindi esegue il mapping a tale zona figlio di ogni singolo indirizzo IP nell'intervallo tramite record CNAME.

Si supponga ad esempio che a un'organizzazione venga concesso dal provider di servizi Internet l'intervallo di indirizzi IP 192.0.2.128/26. Questo intervallo rappresenta 64 indirizzi IP, da 192.0.2.128 a 192.0.2.191. Il DNS inverso per questo intervallo viene implementato come segue:
- L'organizzazione crea una zona di ricerca inversa denominata 128-26.2.0.192.in-addr.arpa. Il prefisso "128-26" rappresenta il segmento di rete assegnato all'organizzazione nell'intervallo di tipo Classe C (/24).
- Il provider di servizi Internet crea record NS per configurare la delega DNS per la zona precedente dalla zona padre di tipo Classe C. Crea anche record CNAME nella zona di ricerca inversa (Classe C), mappando ogni indirizzo IP nell'intervallo di indirizzi IP alla nuova zona creata dall'organizzazione:

```
$ORIGIN 2.0.192.in-addr.arpa
; Delegate child zone
128-26    NS       <name server 1 for 128-26.2.0.192.in-addr.arpa>
128-26    NS       <name server 2 for 128-26.2.0.192.in-addr.arpa>
; CNAME records for each IP address
129       CNAME    129.128-26.2.0.192.in-addr.arpa
130       CNAME    130.128-26.2.0.192.in-addr.arpa
131       CNAME    131.128-26.2.0.192.in-addr.arpa
; etc
```
- L'organizzazione gestisce quindi i singoli record PTR all'interno della rispettiva zona figlio.

```
$ORIGIN 128-26.2.0.192.in-addr.arpa
; PTR records for each UIP address. Names match CNAME targets in parent zone
129      PTR    www.contoso.com
130      PTR    mail.contoso.com
131      PTR    partners.contoso.com
; etc
```
Una ricerca inversa per l'indirizzo IP "192.0.2.129" esegue una query per un record PTR denominato "129.2.0.192.in-addr.arpa". Questa query restituisce un valore tramite il record CNAME nella zona padre al record PTR nella zona figlio.

### <a name="ipv6"></a>IPv6

Il nome di una zona di ricerca inversa di tipo IPv6 deve avere il formato seguente: `<IPv6 network prefix in reverse order>.ip6.arpa`.

Ad esempio: Quando si crea una zona inversa per ospitare i record per gli host con indirizzi IP con prefisso 2001:db8:1000:abdc::/64, il nome della zona viene creato tramite l'isolamento del prefisso di rete dell'indirizzo (2001:db8:abdc::). Viene quindi espanso il prefisso di rete IPv6 per rimuovere la [compressione degli zeri](https://technet.microsoft.com/library/cc781672(v=ws.10).aspx), se è stata usata per accorciare il prefisso dell'indirizzo IPv6 (2001:0db8:abdc:0000::). Invertire l'ordine, usando un punto come delimitatore tra ogni numero esadecimale nel prefisso, per creare il prefisso di rete inverso (`0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2`) e quindi aggiungere il suffisso `.ip6.arpa`.


|Prefisso di rete  |Prefisso di rete espanso e inverso |Suffisso standard |Nome della zona inversa  |
|---------|---------|---------|---------|
|2001:db8:abdc::/64    | 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2        | .ip6.arpa        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001:db8:1000:9102::/64    | 2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2        | .ip6.arpa        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>Supporto di Azure per DNS inverso

Azure supporta due scenari distinti correlati al DNS inverso:

**Hosting della zona di ricerca inversa corrispondente al blocco di indirizzi IP.**
DNS Azure può essere usato per l'[hosting delle zone di ricerca inversa e la gestione dei record PTR per ogni zona DNS di ricerca inversa](dns-reverse-dns-hosting.md), per IPv4 e IPv6.  Il processo di creazione della zona di ricerca inversa (ARPA), configurazione della delega e configurazione dei record PTR è uguale a quello delle normali zone DNS.  Le sole differenze consistono nel fatto che la delega deve essere configurata tramite l'ISP invece che tramite il registrar DNS e che deve essere usato solo il tipo di record PTR.

**Configurare il record DNS inverso per l'indirizzo IP assegnato al servizio di Azure.** Azure consente di [configurare la ricerca inversa per gli indirizzi IP allocati al servizio di Azure](dns-reverse-dns-for-azure-services.md).  Questa ricerca inversa viene configurata da Azure come record PTR nella zona ARPA corrispondente.  Queste zone ARPA, corrispondenti a tutti gli intervalli di IP usati da Azure, vengono ospitate da Microsoft.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul DNS inverso, vedere [Risoluzione DNS inversa su Wikipedia](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Informazioni su come eseguire l'[hosting della zona di ricerca inversa per l'intervallo di indirizzi IP assegnato dal provider di servizi Internet in DNS Azure](dns-reverse-dns-for-azure-services.md).
<br>
Informazioni su come [gestire i record di DNS inverso per i servizi di Azure](dns-reverse-dns-for-azure-services.md).

