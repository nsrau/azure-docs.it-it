---
title: "Utilizzare probe di bilanciamento del carico personalizzati per monitorare lo stato di integrità | Documenti Microsoft"
description: Informazioni su come usare probe personalizzati per il servizio di bilanciamento del carico di Azure per monitorare le relative istanze
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 266132d8cbb6f9922ce7b49759981132c2c17f47
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="understand-load-balancer-probes"></a>Comprendere i probe di bilanciamento del carico

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Il servizio di bilanciamento del carico di Azure consente di monitorare l'integrità delle istanze del server tramite probe. Se un probe non risponde, il servizio di bilanciamento del carico interrompe l'invio di nuove connessioni all'istanza non integra. Le connessioni esistenti non sono interessate, mentre quelle nuove vengono inviate alle istanze integre.

I ruoli del servizio cloud, ovvero i ruoli di lavoro e i ruoli Web, usano un agente guest per il monitoraggio probe. Probe personalizzati TCP o HTTP devono essere configurati quando si utilizzano macchine virtuali di bilanciamento del carico.

## <a name="understand-probe-count-and-timeout"></a>Informazioni su conteggio e timeout dei probe

Il comportamento dei probe dipende dagli elementi seguenti:

* Numero di probe riusciti che consentono di etichettare un'istanza come attiva.
* Numero di probe non riusciti che fanno sì che un'istanza sia etichettata come inattiva.

I valori di timeout e la frequenza impostati in SuccessFailCount determinano se un'istanza viene confermata di essere in esecuzione o non è in esecuzione. Nel portale di Azure il timeout è impostato sul doppio del valore della frequenza.

La configurazione dei probe deve essere la stessa in tutte le istanze con bilanciamento del carico per un endpoint, ovvero un set con bilanciamento del carico. È possibile avere una configurazione di probe diverso per ogni istanza del ruolo o macchine Virtuali nello stesso servizio ospitato per una combinazione di endpoint in questione. Ad esempio, ogni istanza deve avere porte locali e i timeout identici.

> [!IMPORTANT]
> Un probe di bilanciamento del carico Usa l'indirizzo IP 168.63.129.16. Questo indirizzo IP pubblico facilita la comunicazione con le risorse interne della piattaforma per lo scenario Rete virtuale di Azure che prevede l'uso di un IP personale ("bring your own IP"). L'indirizzo IP pubblico virtuale 168.63.129.16 viene utilizzata in tutte le aree e non cambia. È consigliabile consentire questo indirizzo IP in tutti i criteri firewall locali. Non deve essere considerato come un rischio per la sicurezza, perché solo la piattaforma Azure interna può generare un messaggio da questo indirizzo. Se si non consente questo indirizzo IP in criteri del firewall, si verifica un comportamento imprevisto in una vasta gamma di scenari. Il comportamento include la configurazione stesso intervallo di indirizzi IP di 168.63.129.16 e indirizzi IP di duplicazione.

## <a name="learn-about-the-types-of-probes"></a>Informazioni sui tipi di probe

### <a name="guest-agent-probe"></a>Probe dell'agente guest

È disponibile per i servizi Cloud di Azure solo un probe di agente guest. Servizio di bilanciamento del carico utilizza l'agente guest nella macchina virtuale. Quindi, è in ascolto e risponde con una risposta HTTP 200 OK solo quando l'istanza è nello stato pronto. (Gli altri Stati sono occupato, il riciclo o arresto).

Per ulteriori informazioni, vedere [configurare il file di definizione del servizio (csdef) per probe di integrità](https://msdn.microsoft.com/library/azure/ee758710.aspx) o [iniziare creando un servizio di bilanciamento del carico pubblico per i servizi cloud](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>Perché un probe dell'agente guest contrassegna un'istanza come non integra

Se l'agente guest non riesce a rispondere con HTTP 200 OK, il bilanciamento del carico contrassegna l'istanza non risponde. Quindi, si arresta l'invio di traffico a tale istanza. Il servizio di bilanciamento del carico continua a eseguire il ping dell'istanza. Se l'agente guest risponde con un messaggio HTTP 200, il servizio di bilanciamento del carico continua a inviare il traffico a tale istanza.

Quando si utilizza un ruolo web, il codice del sito Web viene eseguito in genere in w3wp.exe, non è monitorato da Azure agente dell'infrastruttura o guest. Non sono segnalati errori in w3wp.exe (ad esempio, le risposte HTTP 500) per l'agente guest. Di conseguenza, il bilanciamento del carico non accetta l'istanza dalla rotazione.

### <a name="http-custom-probe"></a>Probe HTTP personalizzato

Il probe personalizzato HTTP esegue l'override del probe di agente guest predefinito. È possibile creare la logica personalizzata per determinare l'integrità dell'istanza del ruolo. Per impostazione predefinita, il servizio di bilanciamento del carico esegue regolarmente probe sull'endpoint ogni 15 secondi. L'istanza è considerato in rotazione del bilanciamento del carico se il servizio risponde con un HTTP 200 entro il periodo di timeout. Il periodo di timeout è 31 secondi per impostazione predefinita.

Un probe personalizzato HTTP può essere utile se si desidera implementare la logica per rimuovere istanze di rotazione del bilanciamento del carico. Ad esempio, si potrebbe decidere di rimuovere un'istanza se è superiore al 90% della CPU e restituisce uno stato non-200. Se si dispone di ruoli web che utilizzano w3wp.exe, anche possibile ottenere automatica del sito Web di monitoraggio. Errori nel codice del sito Web restituiscono uno stato non-200 per il probe di bilanciamento del carico.

> [!NOTE]
> Il probe HTTP personalizzato supporta solo percorsi relativi e il protocollo HTTP. HTTPS non è supportata.

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>Perché un probe HTTP personalizzato contrassegna un'istanza come non integra

* L'applicazione HTTP restituisce un codice di risposta HTTP diverso da 200, ad esempio 403, 404 o 500. Il riconoscimento segnala che è necessario eseguire immediatamente l'istanza di applicazione fuori servizio.
* Il server HTTP non risponde affatto dopo il periodo di timeout. A seconda del valore di timeout è impostato, più le richieste di probe potrebbero essere senza risposta prima che il probe viene contrassegnato come non in esecuzione (vale a dire prima SuccessFailCount probe vengono inviati).
* Il server chiude la connessione tramite l'invio di TCP Reset.

### <a name="tcp-custom-probe"></a>Probe TCP personalizzato

Probe personalizzati TCP avviano una connessione mediante l'esecuzione di un handshake a tre vie con la porta definita.

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>Perché un probe TCP personalizzato contrassegna un'istanza come non integra

* Il server TCP non risponde affatto dopo il periodo di timeout. Il probe viene contrassegnato come non in esecuzione in base alla configurazione del numero di richieste di probe non riuscite che possono rimanere senza risposta prima che il probe sia contrassegnato come non in esecuzione.
* Il probe riceve un TCP Reset dall'istanza del ruolo.

Per ulteriori informazioni su come configurare un probe di stato HTTP o un probe TCP, vedere [iniziare a creare un servizio di bilanciamento del carico pubblico di gestione risorse tramite PowerShell](load-balancer-get-started-internet-arm-ps.md).

## <a name="add-healthy-instances-back-into-the-load-balancer-rotation"></a>Aggiungere istanze integre alla rotazione del bilanciamento del carico

I probe TCP e HTTP sono considerati integri e contrassegnano come integra l'istanza del ruolo quando:

* Il servizio di bilanciamento del carico ottiene un probe positivo al primo avvio della macchina virtuale.
* Il numero di SuccessFailCount (descritto in precedenza) definisce il valore di esito positivo probe necessari per contrassegnare l'istanza del ruolo come integro. Se un'istanza del ruolo è stata rimossa, il numero di probe successivi riusciti deve essere uguale o maggiore del valore di SuccessFailCount per contrassegnare l'istanza del ruolo come in esecuzione.

> [!NOTE]
> Se è compresa l'integrità di un'istanza del ruolo, il bilanciamento del carico è in attesa più prima che l'istanza del ruolo in stato integro. Il tempo di attesa aggiuntivo consente di proteggere l'utente e l'infrastruttura e un criterio intenzionale.

## <a name="use-log-analytics-for-a-load-balancer"></a>Utilizzare analitica di log per un servizio di bilanciamento del carico

È possibile utilizzare [log analitica](load-balancer-monitor-log.md) per controllare lo stato di integrità probe di bilanciamento carico e verificare la presenza di conteggio. La registrazione è utilizzabile con Power BI o informazioni operative di Azure per fornire statistiche sullo stato di integrità del servizio di bilanciamento carico.
