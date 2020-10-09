---
title: Supporto di volumi di traffico elevati nel gateway applicazione
description: Questo articolo fornisce indicazioni per configurare il gateway applicazione di Azure per il supporto di scenari con volumi elevati di traffico di rete.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: b96720ead2c7b7bc942efca32a8510f57c2dbcad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85250249"
---
# <a name="application-gateway-high-traffic-support"></a>Supporto del traffico elevato nel gateway applicazione

>[!NOTE]
> Questo articolo descrive alcune linee guida consigliate per configurare il gateway applicazione in modo da gestire il traffico aggiuntivo causato dal volume di traffico elevato che può verificarsi a causa della crisi COVID-19.

È possibile usare il gateway applicazione con web application firewall (WAF) per un modo sicuro e scalabile di gestire il traffico verso le applicazioni Web.

I suggerimenti seguenti consentono di configurare il gateway applicazione con WAF per gestire il traffico aggiuntivo.

## <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>Usare lo SKU v2 piuttosto che la versione 1 per le funzionalità di scalabilità automatica e i vantaggi a livello di prestazioni
Lo SKU v2 offre funzionalità di scalabilità automatica per garantire che il gateway applicazione possa aumentare le prestazioni in caso di aumento del traffico. Offre anche altri vantaggi significativi a livello di prestazioni, ad esempio prestazioni di offload TLS 5 volte migliori, tempi di distribuzione e aggiornamento più rapidi, ridondanza della zona e molto altro rispetto alla versione v1. Per altre informazioni, vedere la [documentazione relativa a v2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant). 

## <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>Impostare il numero massimo di istanze sul massimo possibile (125)
 
Supponendo di avere SKU v2 del gateway applicazione, l'impostazione del numero massimo di istanze sul valore massimo possibile di 125 consente l'estensione del gateway applicazione in base alle esigenze. Ciò permette di gestire il possibile aumento del traffico per le applicazioni. Verranno addebitati solo i costi per le unità di capacità usate.  

## <a name="set-your-minimum-instance-count-based-on-your-average-cu-usage"></a>Impostare il numero minimo di istanze in base all'utilizzo medio di unità di capacità

Supponendo di avere uno SKU v2 del gateway applicazione, la funzionalità di scalabilità automatica richiede da 6 a 7 minuti per aumentare le risorse. Con un numero di istanze minimo superiore, il gateway applicazione può gestire meglio il traffico in caso di aumento del carico, perché un picco nel traffico non richiede un'operazione di scalabilità automatica.  

## <a name="alert-if-a-certain-metric-surpasses-75-of-average-cu-utilization"></a>Avviso se una determinata metrica supera il 75% dell'utilizzo medio di unità di capacità 
Per una spiegazione dettagliata delle metriche e altre procedure dettagliate, vedere la [documentazione relativa alle metriche del gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-visualization). 

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>Esempio: Configurazione di un avviso per utilizzo medio di unità di capacità pari al 75%

Questo esempio illustra come usare il portale di Azure per configurare un avviso quando viene raggiunto il 75% dell'utilizzo medio di unità di capacità. 
1. Passare al **gateway applicazione**.
2. Nel pannello a sinistra selezionare **Metrica** nella scheda **Monitoraggio**. 
3. Aggiungere una metrica per **Average Current Compute Units** (Unità di calcolo correnti medie). 
![Configurazione della metrica WAF](./media/application-gateway-covid-guidelines/waf-setup-metrics.png)
4. Se è stato impostato il numero minimo di istanze come utilizzo medio di unità di capacità, procedere e impostare un avviso quando il 75% del numero minimo di istanze è in uso. Ad esempio, se l'utilizzo medio è pari a 10 unità di capacità, impostare un avviso per 7,5 unità di capacità. In questo modo viene visualizzato un avviso se l'utilizzo aumenta e si ha quindi tempo per rispondere. È possibile aumentare il valore minimo se si ritiene che il traffico sarà sostenuto per segnalare che il traffico potrebbe aumentare. 
![Configurazione dell'avviso WAF](./media/application-gateway-covid-guidelines/waf-setup-monitoring-alert.png)

> [!NOTE]
> È possibile impostare l'avviso affinché venga generato per una percentuale di utilizzo di unità di capacità inferiore o superiore, a seconda del livello di dettaglio desiderato per il rilevamento dei potenziali picchi di traffico.

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>Configurare WAF con filtri geografici e protezione dei bot per arrestare gli attacchi
Se si vuole un ulteriore livello di sicurezza per l'applicazione, usare il gateway applicazione WAF_v2 SKU per le funzionalità WAF. È possibile configurare lo SKU v2 in modo da consentire solo l'accesso alle applicazioni da un determinato paese/area geografica o da più aree geografiche/paesi. Si configura una regola personalizzata di WAF per consentire o bloccare in modo esplicito il traffico in base alla georilevazione. Per altre informazioni, vedere [Regole personalizzate di corrispondenza geografica](https://docs.microsoft.com/azure/web-application-firewall/ag/geomatch-custom-rules) e [Come configurare regole personalizzate per il gateway applicazione WAF_v2 SKU tramite PowerShell](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules).

Abilitare la protezione dai bot per bloccare i bot dannosi noti. Questa operazione dovrebbe ridurre la quantità di traffico per l'applicazione. Per altre informazioni, vedere la pagina relativa alla [protezione dai bot con istruzioni di configurazione](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules).

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Attivare la diagnostica per il gateway applicazione e WAF

I log di diagnostica consentono di visualizzare i log del firewall, i log delle prestazioni e i log di accesso. È possibile usare questi log in Azure per gestire e risolvere i problemi dei gateway applicazione. Per altre informazioni, vedere la [documentazione relativa alla diagnostica](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#diagnostic-logging). 

## <a name="set-up-an-tls-policy-for-extra-security"></a>Configurare un criterio TLS per maggiore sicurezza
Assicurarsi di usare la versione più recente dei criteri TLS ([AppGwSslPolicy20170401S](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview#appgwsslpolicy20170401s)) per imporre l'uso di TLS 1.2 e crittografie più complesse. Per altre informazioni, vedere [Configurazione delle versioni dei criteri TLS e dei pacchetti di crittografia tramite PowerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-configure-ssl-policy-powershell).
