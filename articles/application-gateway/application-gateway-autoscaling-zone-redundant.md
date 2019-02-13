---
title: Gateway applicazione con ridimensionamento automatico e ridondanza della zona in Azure (anteprima pubblica)
description: Questo articolo presenta la SKU versione 2 dell'applicazione Azure, che include le funzionalità di scalabilità automatica e di ridondanza della zona.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 2/5/2019
ms.author: victorh
ms.openlocfilehash: 880a6fc9d5636ff0ac67d24d0ef66c8f48ae22fb
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2019
ms.locfileid: "55743651"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-public-preview"></a>Gateway applicazione con scalabilità automatica e ridondanza della zona (anteprima pubblica)

Il gateway applicazione e Web application firewall sono ora disponibili in anteprima pubblica in una nuova SKU versione 2 che offre miglioramenti alle prestazioni e aggiunge il supporto per nuove importanti funzionalità quali scalabilità automatica, ridondanza della zona e supporto per gli indirizzi VIP statici. Le funzionalità esistenti nella SKU disponibile a livello generale continuano a essere supportate nella nuova SKU versione 2, con alcune eccezioni elencate nella sezione delle limitazioni note. Le nuove SKU versione 2 includono i miglioramenti seguenti:

- **Ridimensionamento automatico**: le distribuzioni del gateway applicazione o WAF nello SKU con ridimensionamento automatico possono passare a un piano superiore o inferiore in base alle modifiche dei modelli di carico del traffico. La scalabilità automatica elimina anche la necessità di scegliere un numero di istanze o le dimensioni della distribuzione durante il provisioning. Di conseguenza, la SKU offre una reale elasticità. Nella nuova SKU il gateway applicazione può operare sia in modalità di capacità fissa (ridimensionamento automatico disabilitato) che di ridimensionamento automatico abilitato. La modalità di capacità fissa è utile per gli scenari con carichi di lavoro coerenti e prevedibili. La modalità di scalabilità automatica è vantaggiosa nelle applicazioni con traffico delle applicazioni soggetto a numerose varianze.

   > [!NOTE]
   > La scalabilità automatica non è attualmente disponibile per la SKU WAF. Configurare WAF con la modalità di capacità fissa, invece che con la modalità di scalabilità automatica.
- **Ridondanza della zona**: una distribuzione del gateway applicazione o WAF può estendersi a più zone di disponibilità, eliminando la necessità di effettuare il provisioning e aggiungere istanze del gateway applicazione separate in ogni zona con un'utilità di Gestione traffico. È possibile scegliere una o più zone in cui distribuire le istanze del gateway applicazione, assicurando così la resilienza dagli errori delle zone. Il pool di back-end per le applicazioni può analogamente essere distribuito tra zone di disponibilità.
- **Miglioramenti delle prestazioni**: la SKU con ridimensionamento automatico offre prestazioni di ripartizione del carico di lavoro SSL fino a 5 volte superiori a quelle della SKU disponibile a livello generale.
- **Tempi di distribuzione e di aggiornamento più veloci**: la SKU con scalabilità automatica offre tempi di distribuzione e di aggiornamento più veloci rispetto alla SKU disponibile a livello generale.
- **Indirizzo VIP statico**: l'indirizzo VIP del gateway applicazione supporta ora esclusivamente il tipo di indirizzo VIP statico. Questa funzionalità garantisce che l'indirizzo VIP associato al gateway applicazione non cambi neppure dopo il riavvio.

> [!IMPORTANT]
> Lo SKU del gateway applicazione con scalabilità automatica e ridondanza della zona è attualmente in anteprima pubblica. Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Aree supportate

Lo SKU di ridimensionamento automatico è disponibile nelle aree seguenti: Stati Uniti centro-settentrionali, Stati Uniti centro-meridionali, Stati Uniti occidentali, Stati Uniti occidentali 2, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti centrali, Europa settentrionale, Europa occidentale, Asia sud-orientale, Francia centrale, Regno Unito occidentale, Giappone orientale, Giappone occidentale.

## <a name="pricing"></a>Prezzi

Durante l'anteprima, non è previsto alcun addebito. Viene fatturato l'uso di risorse diverse dal gateway applicazione, ad esempio Key Vault, macchine virtuali e così via.

## <a name="known-issues-and-limitations"></a>Problemi noti e limitazioni

|Problema|Dettagli|
|--|--|
|Autenticazione del certificato|Non supportati.<br>Per altre informazioni, vedere [Panoramica di SSL end-to-end con il gateway applicazione](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Combinazione di versione 2 Standard e gateway applicazione standard nella stessa subnet|Non supportati.<br>Inoltre, se è abilitata la scalabilità automatica, una subnet può avere solo un gateway applicazione.|
|Applicazione di una route definita dall'utente alla subnet del gateway applicazione|Non supportate|
|Gruppo di sicurezza di rete per intervallo di porte in ingresso| - Da 65200 a 65535 per SKU versione 2 Standard<br>- Da 65503 a 65534 per SKU Standard.<br>Per altre informazioni, vedere la sezione [Domande frequenti](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Contatori delle prestazioni in Diagnostica di Azure|Non supportati.<br>Usare le metriche di Azure.|
|Fatturazione|Nessun addebito attualmente.|
|Modalità FIPS, WebSocket|Attualmente non supportati.|
|Modalità solo bilanciamento del carico interno|Non supportato attualmente. Le modalità pubblica e con bilanciamento del carico interno insieme non sono supportate.|
|Scalabilità automatica di Web application firewall|WAF non supporta la modalità di scalabilità automatica. È supportata la modalità con capacità fissa.|
|Integrazione di Netwatcher|Non è supportata nell'anteprima pubblica.|

## <a name="next-steps"></a>Passaggi successivi
- [Creare un gateway applicazione con scalabilità automatica e ridondanza della zona con un indirizzo IP virtuale riservato tramite Azure PowerShell](tutorial-autoscale-ps.md)
- Altre informazioni sul [gateway applicazione](overview.md).
- Altre informazioni su [Firewall di Azure](../firewall/overview.md).