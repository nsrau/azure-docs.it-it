---
title: Proteggere gli hub virtuali di Azure tramite Check Point Cloudguard Connect
description: Informazioni su Check Point CloudGuard Connect per proteggere gli hub virtuali di Azure
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: victorh
ms.openlocfilehash: 3c61dc689d19e1a7d6f9b6dbefae846e9458d750
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146859"
---
# <a name="secure-virtual-hubs-using-check-point-cloudguard-connect"></a>Proteggere gli hub virtuali con check point Cloudguard Connect

Check Point CloudGuard Connect è un partner di sicurezza attendibile in Azure Firewall Manager. Protegge le connessioni da succursali distribuite a Internet (B2I) o da rete virtuale a Internet (V2I) con la prevenzione avanzata delle minacce. 

Con una semplice configurazione in gestione firewall di Azure, è possibile indirizzare le connessioni di hub e reti virtuali a Internet tramite CloudGuard Connect Security as a Service (SECas). Il traffico è protetto in transito dall'hub al servizio cloud Check Point nei tunnel VPN IPsec.

Quando si Abilita la sincronizzazione automatica nel portale Check Point, qualsiasi risorsa contrassegnata come *protetta* nel portale di Azure viene protetta automaticamente. Non è necessario gestire le risorse due volte. È sufficiente scegliere di proteggerli una volta nel portale di Azure.

Check Point unifica più servizi di sicurezza in un unico ombrello. Il traffico di sicurezza integrato viene decrittografato una volta e controllato in un singolo passaggio. Il controllo delle applicazioni, il filtro URL e la consapevolezza del contenuto (DLP) applicano un utilizzo Web sicuro e proteggono i dati. Indirizzi IP e antivirus proteggono gli utenti da exploit di rete noti. Anti-bot blocca le connessioni ai server di comando e controllo e avvisa l'utente se un host è infetto.

L'emulazione delle minacce (sandboxing) protegge gli utenti da minacce sconosciute e zero-day. Check Point Sabbiation Zero-Day Protection è una tecnologia di Boxing di sabbia ospitata nel cloud in cui i file vengono rapidamente messi in quarantena e controllati. Viene eseguito in una sandbox virtuale per individuare il comportamento dannoso prima di entrare nella rete. Evita le minacce prima che il danno venga eseguito per risparmiare tempo prezioso del personale per rispondere alle minacce. 

## <a name="deployment-example"></a>Esempio di distribuzione

Vedere il video seguente per informazioni su come distribuire Check Point CloudGuard Connect come partner di sicurezza di Azure attendibile.

> [!VIDEO https://www.youtube.com/embed/C8AuN76DEmU]

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire un provider del partner di sicurezza](deploy-trusted-security-partner.md)