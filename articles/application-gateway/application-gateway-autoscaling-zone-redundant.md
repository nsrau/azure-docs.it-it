---
title: Gateway applicazione con ridimensionamento automatico e ridondanza della zona in Azure (anteprima pubblica)
description: Questo articolo presenta la SKU versione 2 dell'applicazione Azure, che include le funzionalità di scalabilità automatica e di ridondanza della zona.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 3/6/2019
ms.author: victorh
ms.openlocfilehash: 95b14a0028134e522206f3595bc3b9ebf9aaf396
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128487"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-public-preview"></a>Gateway applicazione con scalabilità automatica e ridondanza della zona (anteprima pubblica)

Il gateway applicazione e Web application firewall sono ora disponibili in anteprima pubblica in una nuova SKU versione 2 che offre miglioramenti alle prestazioni e aggiunge il supporto per nuove importanti funzionalità quali scalabilità automatica, ridondanza della zona e supporto per gli indirizzi VIP statici. Le funzionalità esistenti nella SKU disponibile a livello generale continuano a essere supportate nella nuova SKU versione 2, con alcune eccezioni elencate nella sezione delle limitazioni note. Le nuove SKU versione 2 includono i miglioramenti seguenti:

- **Ridimensionamento automatico**: le distribuzioni del gateway applicazione o WAF nello SKU con ridimensionamento automatico possono passare a un piano superiore o inferiore in base alle modifiche dei modelli di carico del traffico. La scalabilità automatica elimina anche la necessità di scegliere un numero di istanze o le dimensioni della distribuzione durante il provisioning. Questo SKU offre elasticità true. Nella nuova SKU il gateway applicazione può operare sia in modalità di capacità fissa (ridimensionamento automatico disabilitato) che di ridimensionamento automatico abilitato. La modalità di capacità fissa è utile per gli scenari con carichi di lavoro coerenti e prevedibili. La modalità di scalabilità automatica è vantaggiosa nelle applicazioni con traffico delle applicazioni soggetto a numerose varianze.

- **Ridondanza della zona**: una distribuzione del gateway applicazione o WAF può estendersi a più zone di disponibilità, eliminando la necessità di effettuare il provisioning e aggiungere istanze del gateway applicazione separate in ogni zona con un'utilità di Gestione traffico. È possibile scegliere una o più zone in cui distribuire le istanze del gateway applicazione, assicurando così la resilienza dagli errori delle zone. Il pool di back-end per le applicazioni può analogamente essere distribuito tra zone di disponibilità.
- **Miglioramenti delle prestazioni**: la SKU con ridimensionamento automatico offre prestazioni di ripartizione del carico di lavoro SSL fino a 5 volte superiori a quelle della SKU disponibile a livello generale.
- **Tempi di distribuzione e di aggiornamento più veloci**: la SKU con scalabilità automatica offre tempi di distribuzione e di aggiornamento più veloci rispetto alla SKU disponibile a livello generale.
- **Indirizzo VIP statico**: l'indirizzo VIP del gateway applicazione supporta ora esclusivamente il tipo di indirizzo VIP statico. Ciò garantisce che l'indirizzo VIP associato al gateway applicazione non viene modificata anche dopo un riavvio.

> [!IMPORTANT]
> Lo SKU del gateway applicazione con scalabilità automatica e ridondanza della zona è attualmente in anteprima pubblica. Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

> [!NOTE]
> Il ridimensionamento automatico e il gateway applicazione ridondanza della zona supporta ora la SKU [probe di integrità predefinito](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe) per monitorare l'integrità di tutte le risorse nel pool back-end e di evidenziare i membri di back-end che sono considerati automaticamente tipo non integro. L'esecuzione di probe di integrità predefinito da configurati automaticamente per tutti i back-end per il quale è ancora configurato tutte le configurazioni di probe personalizzato. Per altre informazioni, vedere [probe di integrità nel gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview).

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Confronto delle funzionalità tra SKU v1 e v2 SKU

La tabella seguente confronta le funzionalità disponibili in ogni SKU.

|                                                   | v1 SKU   | SKU v2   |
| ------------------------------------------------- | -------- | -------- |
| Scalabilità automatica                                       |          | &#x2713; |
| Ridondanza di zona                                   |          | &#x2713; |
| &nbsp;Indirizzo VIP statico&nbsp;&nbsp;                      |          | &#x2713; |
| Routing basato su URL                                 | &#x2713; | &#x2713; |
| Hosting di più siti                             | &#x2713; | &#x2713; |
| Reindirizzamento del traffico                               | &#x2713; | &#x2713; |
| Web application firewall (WAF)                    | &#x2713; | &#x2713; |
| Terminazione di Secure Sockets Layer (SSL)            | &#x2713; | &#x2713; |
| Crittografia SSL end-to-end                         | &#x2713; | &#x2713; |
| Affinità di sessione                                  | &#x2713; | &#x2713; |
| Pagine di errore personalizzate                                | &#x2713; | &#x2713; |
| Riscrivere le intestazioni HTTP (S)                           |          | &#x2713; |
| Supporto per WebSocket                                 | &#x2713; | &#x2713; |
| Supporto HTTP/2                                    | &#x2713; | &#x2713; |
| Esaurimento delle connessioni                               | &#x2713; | &#x2713; |
| Azure controller di Ingress di Kubernetes Service (AKS) |          | &#x2713; |

## <a name="supported-regions"></a>Aree supportate

Lo SKU di ridimensionamento automatico è disponibile nelle aree seguenti: Stati Uniti centro-settentrionali, Stati Uniti centro-meridionali, Stati Uniti occidentali, Stati Uniti occidentali 2, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti centrali, Europa settentrionale, Europa occidentale, Asia sud-orientale, Francia centrale, Regno Unito occidentale, Giappone orientale, Giappone occidentale.

## <a name="pricing"></a>Prezzi

Durante l'anteprima, non sono previsti addebiti. Viene fatturata risorse diverse dai gateway applicazione, ad esempio Key Vault, le macchine virtuali e così via.

## <a name="known-issues-and-limitations"></a>Problemi noti e limitazioni

|Problema|Dettagli|
|--|--|
|Autenticazione del certificato|Non supportati.<br>Per altre informazioni, vedere [Panoramica di SSL end-to-end con il gateway applicazione](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Combinazione di versione 2 Standard e gateway applicazione standard nella stessa subnet|Non supportate|
|Applicazione di una route definita dall'utente alla subnet del gateway applicazione|Non supportate|
|Gruppo di sicurezza di rete per intervallo di porte in ingresso| - Da 65200 a 65535 per SKU versione 2 Standard<br>- Da 65503 a 65534 per SKU Standard.<br>Per altre informazioni, vedere la sezione [Domande frequenti](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Contatori delle prestazioni in Diagnostica di Azure|Non supportati.<br>Usare le metriche di Azure.|
|Fatturazione|Nessun addebito attualmente.|
|Modalità FIPS|Attualmente non supportati.|
|Modalità solo bilanciamento del carico interno|Non supportato attualmente. Le modalità pubblica e con bilanciamento del carico interno insieme non sono supportate.|
|Integrazione di Netwatcher|Non è supportata nell'anteprima pubblica.|

## <a name="next-steps"></a>Passaggi successivi
- [Creare un gateway applicazione con scalabilità automatica e ridondanza della zona con un indirizzo IP virtuale riservato tramite Azure PowerShell](tutorial-autoscale-ps.md)
- Altre informazioni sul [gateway applicazione](overview.md).
- Altre informazioni su [Firewall di Azure](../firewall/overview.md).
