---
title: Sportello anteriore di Azure-protezione DDoS
description: Questa pagina fornisce informazioni sul modo in cui Azure front door aiuta a proteggersi da attacchi DDoS
services: frontdoor
documentationcenter: ''
author: johndowns
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: jodowns
ms.openlocfilehash: 24a8a559d72eae4dab0b6e740b34890f1d7ff114
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350784"
---
# <a name="ddos-protection-on-front-door"></a>Protezione DDoS sulla porta anteriore

Il front-end di Azure presenta diverse caratteristiche e caratteristiche che consentono di evitare attacchi di tipo Denial of Service (DDoS) distribuiti. Queste funzionalità possono impedire agli utenti malintenzionati di raggiungere l'applicazione e influire sulla disponibilità e sulle prestazioni dell'applicazione.

## <a name="integration-with-azure-ddos-protection-basic"></a>Integrazione con protezione DDoS di Azure Basic

La porta anteriore è protetta da protezione DDoS di Azure Basic. Per impostazione predefinita, è integrato nella piattaforma front door e senza costi aggiuntivi. La scalabilità e la capacità complete della rete distribuita a livello globale di front-end offrono una difesa contro gli attacchi comuni a livello di rete tramite il monitoraggio del traffico always on e la mitigazione in tempo reale. La protezione DDoS di base difende inoltre i flussi di query DNS di livello 7 più comuni e di livello 3 e 4 che si rivolgono agli endpoint pubblici. Questo servizio ha anche un tracking collaudato nella protezione dei servizi aziendali e consumer di Microsoft da attacchi su larga scala. Per altre informazioni, vedere [protezione DDoS di Azure](../security/fundamentals/ddos-best-practices.md).

## <a name="protocol-blocking"></a>Blocco del protocollo

Lo sportello anteriore accetta solo il traffico sui protocolli HTTP e HTTPS ed elabora le richieste valide solo con un' `Host` intestazione nota. Questo comportamento contribuisce a mitigare alcuni tipi di attacco DDoS comuni, inclusi gli attacchi volumetrici distribuiti in una gamma di protocolli e porte, attacchi di amplificazione DNS e attacchi di avvelenamento TCP.

## <a name="capacity-absorption"></a>Assorbimento della capacità

La porta anteriore è un servizio ampiamente distribuito a livello globale. Ci sono molti clienti, inclusi i prodotti cloud su larga scala Microsoft, che ricevono centinaia di migliaia di richieste al secondo. La porta anteriore si trova alla periferia della rete di Azure, assorbendo e isolando geograficamente gli attacchi di grandi volumi. Ciò può impedire che il traffico dannoso continui a essere superiore rispetto al perimetro della rete di Azure.

## <a name="caching"></a>Memorizzazione nella cache

Le [funzionalità di memorizzazione nella cache della porta anteriore](./front-door-caching.md) possono essere usate per proteggere i backend da volumi di traffico elevati generati da un attacco. Le risorse memorizzate nella cache verranno restituite dai nodi perimetrali front-end in modo che non vengano trasmesse al back-end. Anche i tempi di scadenza della cache brevi (secondi o minuti) sulle risposte dinamiche possono ridurre notevolmente il carico dei servizi back-end. Per ulteriori informazioni sulla memorizzazione nella cache di concetti e modelli, vedere [considerazioni sulla memorizzazione nella](/azure/architecture/best-practices/caching) cache e [modello cache-aside](/azure/architecture/patterns/cache-aside).

## <a name="web-application-firewall-waf"></a>WAF (Web application firewall)

[Il Web Application Firewall (WAF) dello sportello anteriore](../web-application-firewall/afds/afds-overview.md) può essere usato per attenuare diversi tipi di attacchi:

* L'utilizzo del set di regole gestite fornisce la protezione contro diversi attacchi comuni.
* Il traffico esterno a una determinata area geografica o all'interno di un'area definita può essere bloccato o reindirizzato a una pagina Web statica. Per altre informazioni, vedere [filtro geografico](../web-application-firewall/afds/waf-front-door-geo-filtering.md).
* Gli indirizzi IP e gli intervalli identificati come dannosi possono essere bloccati.
* È possibile applicare la limitazione della frequenza per impedire che gli indirizzi IP chiamino il servizio troppo spesso.
* È possibile creare [regole WAF personalizzate](../web-application-firewall/afds/waf-front-door-custom-rules.md) per bloccare e classificare automaticamente gli attacchi http o HTTPS con firme note.

## <a name="for-further-protection"></a>Per una maggiore protezione

Se è necessaria una protezione aggiuntiva, è possibile abilitare la [protezione DDoS di Azure standard](../security/fundamentals/ddos-best-practices.md#ddos-protection-standard) nel VNet in cui vengono distribuiti i back-end. I clienti di protezione DDoS standard ricevono vantaggi aggiuntivi, tra cui la protezione dei costi, la garanzia del contratto di contratto e l'accesso agli esperti del team di risposta rapida DDoS per assistenza immediata durante un attacco.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come configurare un [profilo WAF nella porta anteriore](front-door-waf.md). 
- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).