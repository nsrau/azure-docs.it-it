---
title: Gateway applicazione con scalabilità automatica e ridondanza della zona in Azure (anteprima pubblica) | Microsoft Docs
description: Questo articolo descrive gli elenchi di esclusione e i limiti di dimensioni delle richieste di Web application firewall nel gateway applicazione con il portale di Azure.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: victorh
ms.openlocfilehash: 5d051ed049964af708056e2963f04cb478c15906
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951509"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-public-preview"></a>Gateway applicazione con scalabilità automatica ridondanza della zona (anteprima pubblica)

Il gateway applicazione e Web application firewall sono ora disponibili in anteprima pubblica in una nuova SKU che offre miglioramenti alle prestazioni e aggiunge il supporto per nuove importanti funzionalità quali scalabilità automatica, ridondanza della zona e supporto per gli indirizzi VIP. Le funzionalità esistenti nella SKU disponibile a livello generale continuano a essere supportate nella nuova SKU, con alcune eccezioni elencate nella sezione delle limitazioni note. Le nuove SKU includono i miglioramenti seguenti:

- **Scalabilità automatica**: le distribuzioni del gateway applicazione o WAF nella SKU con scalabilità automatica possono passare a un piano superiore o inferiore in base alle modifiche dei modelli di carico del traffico. La scalabilità automatica elimina anche la necessità di scegliere un numero di istanze o le dimensioni della distribuzione durante il provisioning. Di conseguenza, la SKU offre una reale elasticità. Nella nuova SKU il gateway applicazione può operare sia in modalità di capacità fissa (scalabilità automatica disabilitata) che di scalabilità automatica abilitata. La modalità di capacità fissa è utile per gli scenari con carichi di lavoro coerenti e prevedibili. La modalità di scalabilità automatica è vantaggiosa nelle applicazioni con traffico delle applicazioni soggetto a numerose varianze.
   
   > [!NOTE]
   > La scalabilità automatica non è attualmente disponibile per la SKU WAF. Configurare WAF con la modalità di capacità fissa, invece che con la modalità di scalabilità automatica.
- **Ridondanza della zona**: una distribuzione di Gateway applicazione o WAF può estendersi a più zone di disponibilità, eliminando la necessità di effettuare il provisioning e aggiungere istanze del gateway applicazione separate in ogni zona con un'utilità di gestione del traffico. È possibile scegliere una o più zone in cui distribuire le istanze del gateway applicazione, assicurando così la resilienza dagli errori delle zone. Il pool di back-end per le applicazioni può analogamente essere distribuito tra zone di disponibilità.
- **Miglioramenti alle prestazioni**: la SKU con scalabilità automatica offre prestazioni di ripartizione del carico di lavoro SSL fino a 5 volte superiori a quelle della SKU disponibile a livello generale.
- **Tempi di distribuzione e di aggiornamento più veloci**: la SKU con scalabilità automatica offre tempi di distribuzione e di aggiornamento più veloci rispetto alla SKU disponibile a livello generale.
- **VIP statico**: l'indirizzo VIP del gateway applicazione supporta ora esclusivamente il tipo di indirizzo VIP statico. Questa funzionalità garantisce che l'indirizzo VIP associato al gateway applicazione non cambi neppure dopo il riavvio.

> [!IMPORTANT]
> La SKU del gateway applicazione con scalabilità automatica e ridondanza della zona è attualmente in anteprima pubblica. Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Aree supportate
La SKU con scalabilità automatica è disponibile nelle aree Stati Uniti orientali 2, Stati Uniti centrali, Stati Uniti occidentali 2, Francia centrale, Europa occidentale e Asia sud-orientale.

## <a name="pricing"></a>Prezzi
Durante l'anteprima, non è previsto alcun addebito. Viene fatturato l'uso di risorse diverse dal gateway applicazione, ad esempio Key Vault, macchine virtuali e così via. 

## <a name="known-issues-and-limitations"></a>Problemi noti e limitazioni

|Problema|Dettagli|
|--|--|
|Fatturazione|Nessun addebito attualmente.|
|Modalità FIPS, WebSocket|Attualmente non supportati.|
|Modalità solo bilanciamento del carico interno|Non supportato attualmente. Le modalità pubblica e con bilanciamento del carico interno insieme non sono supportate.|
|Scalabilità automatica di Web application firewall|WAF non supporta la modalità di scalabilità automatica. È supportata la modalità con capacità fissa.|

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sul [gateway applicazione](overview.md).
- Altre informazioni su [Firewall di Azure](../firewall/overview.md). 

