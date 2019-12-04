---
title: Consigli sul centro sicurezza di Azure per la rete
description: Questo articolo elenca le raccomandazioni sulla sicurezza del Centro sicurezza di Azure che consentono di proteggere le risorse di rete.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2019
ms.author: memildin
ms.openlocfilehash: 1b8d84286d14949c007d1ece3d089a7606464aaf
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74781969"
---
# <a name="network-recommendations---reference-guide"></a>Raccomandazioni sulla rete-Guida di riferimento

Questo articolo fornisce l'elenco completo delle raccomandazioni che è possibile vedere nel centro sicurezza di Azure in merito alla topologia della rete e agli endpoint con connessione Internet.

Per una spiegazione di come trovare questi e come risolverli, vedere [qui](security-center-network-recommendations.md).

## <a name="network-recommendations"></a>Consigli sulle reti

|Nome raccomandazione|Description|Severity|Punteggio di sicurezza|Tipo di risorsa|
|----|----|----|----|----|----|
|I gruppi di sicurezza di rete a livello di subnet devono essere abilitati|Abilitare i gruppi di sicurezza di rete per controllare l'accesso alla rete delle risorse distribuite nelle subnet.|Alta/media|30|Subnet|
|Le macchine virtuali devono essere associate a un gruppo di sicurezza di rete|Consente di abilitare i gruppi di sicurezza di rete per controllare l'accesso alla rete delle macchine virtuali.|Alta/media|30|Macchine virtuali|
|L'accesso deve essere limitato per i gruppi di sicurezza di rete permissivi con macchine virtuali con connessione Internet|Rafforzare i gruppi di sicurezza di rete delle macchine virtuali con connessione Internet limitando l'accesso alle regole di autorizzazione esistenti.|Alte|20|Macchine virtuali|
|Le regole per le applicazioni Web in IaaS gruppi devono essere finalizzate|Rafforzare il gruppo di sicurezza di rete (NSG) delle macchine virtuali che eseguono applicazioni Web, con regole NSG che sono eccessivamente permissive per quanto concerne le porte dell'applicazione Web.|Alte|20|Macchine virtuali|
|L'accesso ai servizi app deve essere limitato|Limitare l'accesso ai servizi app modificando la configurazione di rete per negare il traffico in ingresso da intervalli troppo ampi.|Alte|10|Servizio app|
|Le porte di gestione devono essere chiuse nelle macchine virtuali|Rafforzare il gruppo di sicurezza di rete delle macchine virtuali per limitare l'accesso alle porte di gestione.|Alte|10|Macchine virtuali|
Lo standard di protezione DDoS deve essere abilitato|Proteggi le reti virtuali contenenti applicazioni con indirizzi IP pubblici abilitando il servizio protezione DDoS standard. La protezione DDoS consente la mitigazione di attacchi volumetrici e di protocollo di rete.|Alte|10|Rete virtuale|
|L'invio IP nella macchina virtuale deve essere disabilitato|Disabilitare l'invio IP. Quando l'indirizzo IP viene abilitato sulla scheda di interfaccia di rete di una macchina virtuale, il computer può ricevere il traffico destinato ad altre destinazioni. L'invio IP è raramente necessario, ad esempio quando si usa la VM come appliance virtuale di rete, e pertanto deve essere rivisto dal team di sicurezza di rete.|Media|10|Macchine virtuali|
|L'applicazione Web deve essere accessibile solo tramite HTTPS|Abilitare l'accesso "solo HTTPS" per le applicazioni Web. L'uso di HTTPS assicura l'autenticazione di server/Servizi e protegge i dati in transito da attacchi di intercettazione a livello di rete.|Media|20|Applicazione Web|
|Il controllo di accesso alla rete JIT deve essere applicato alle macchine virtuali|Applicare il controllo di accesso delle macchine virtuali JIT (just-in-Time) per bloccare in modo permanente l'accesso alle porte selezionate e consentire agli utenti autorizzati di aprirle, tramite JIT, solo per un periodo di tempo limitato.|Alte|20|Macchine virtuali|
|Le app per le funzioni devono essere accessibili solo tramite HTTPS|Abilitare l'accesso "solo HTTPS" per le app per le funzioni. L'uso di HTTPS assicura l'autenticazione di server/Servizi e protegge i dati in transito da attacchi di intercettazione a livello di rete.|Media|20|App per le funzioni|
|Il trasferimento sicuro negli account di archiviazione deve essere abilitato|Abilitare il trasferimento sicuro negli account di archiviazione. Il trasferimento sicuro è un'opzione che impone all'account di archiviazione di accettare richieste solo da connessioni sicure (HTTPS). L'uso di HTTPS assicura l'autenticazione tra il server e il servizio e protegge i dati in transito da attacchi a livello di rete, ad esempio Man-in-the-Middle, intercettazione e Hijack della sessione.|Alte|20|Account di archiviazione|


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle raccomandazioni applicabili ad altri tipi di risorse di Azure, vedere gli argomenti seguenti:

* [Monitorare identità e accesso](security-center-identity-access.md)
* [Protezione di computer e applicazioni](security-center-virtual-machine-protection.md)
* [Protezione del servizio SQL di Azure](security-center-sql-service-recommendations.md)

