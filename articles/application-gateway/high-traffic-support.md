---
title: Supporto del volume di traffico elevato del gateway applicazione
description: Questo articolo fornisce indicazioni per configurare applicazione Azure gateway a supporto di scenari con volumi elevati di traffico di rete.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: 65f404b52a5fc06d8fa5bb5aad291e57fde8caba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80617191"
---
# <a name="application-gateway-high-traffic-support"></a>Supporto del traffico elevato nel gateway applicazione

>[!NOTE]
> Questo articolo descrive alcune linee guida consigliate per configurare il gateway applicazione in modo da gestire il traffico aggiuntivo a causa di volumi elevati di traffico che possono verificarsi a causa della crisi COVID-19.

È possibile usare il gateway applicazione con Web Application Firewall (WAF) per un modo sicuro e scalabile per gestire il traffico verso le applicazioni Web.

I suggerimenti seguenti consentono di configurare il gateway applicazione con WAF per gestire il traffico aggiuntivo.

## <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>Usare lo SKU V2 sopra V1 per le funzionalità di scalabilità automatica e i vantaggi delle prestazioni
Lo SKU V2 offre la scalabilità automatica per garantire che il gateway applicazione possa aumentare le prestazioni in quanto aumenta il traffico. Offre anche altri vantaggi significativi in merito alle prestazioni, ad esempio prestazioni di offload TLS migliori, tempi di distribuzione e aggiornamento più rapidi, ridondanza della zona e molto altro rispetto alla versione V1. Per ulteriori informazioni, vedere la [documentazione di V2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant). 

## <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>Impostare il numero massimo di istanze sul massimo possibile (125)
 
Supponendo che si disponga di uno SKU del gateway applicazione V2, l'impostazione del numero massimo di istanze sul valore massimo possibile di 125 consente la scalabilità orizzontale del gateway applicazione in base alle esigenze. Ciò consente di gestire il possibile aumento del traffico per le applicazioni. Verranno addebitati solo i costi per le unità di capacità usate.  

## <a name="set-your-minimum-instance-count-based-on-your-average-cu-usage"></a>Impostare il numero minimo di istanze in base all'utilizzo medio di CU

Supponendo che si disponga di uno SKU del gateway applicazione V2, la scalabilità automatica richiede da 6 a sette minuti. Con un numero di istanze minimo superiore, il gateway applicazione può gestire meglio il traffico quando il carico viene aumentato, perché un picco nel traffico non richiede un'operazione di ridimensionamento automatico.  

## <a name="alert-if-a-certain-metric-surpasses-75-of-average-cu-utilization"></a>Avvisa se una determinata metrica supera il 75% dell'utilizzo medio CU 
Per una spiegazione dettagliata delle metriche e di altre procedure dettagliate, vedere la [documentazione relativa alle metriche del gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-visualization) . 

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>Esempio: impostazione di un avviso sul 75% di utilizzo CU medio

Questo esempio illustra come usare il portale di Azure per configurare un avviso quando viene raggiunto il 75% dell'utilizzo medio di CU. 
1. Passare al **gateway applicazione**.
2. Nel riquadro sinistro selezionare **metriche** nella scheda **monitoraggio** . 
3. Aggiungere una metrica per le **unità di calcolo correnti medie**. 
![Impostazione della metrica WAF](./media/application-gateway-covid-guidelines/waf-setup-metrics.png)
4. Se è stato impostato il numero minimo di istanze come utilizzo medio di CU, procedere e impostare un avviso quando il 75% delle istanze minime è in uso. Ad esempio, se l'utilizzo medio è pari a 10 CUs, impostare un avviso su 7,5 CUs. In questo modo viene visualizzato un avviso se l'utilizzo aumenta e viene concesso il tempo di risposta. È possibile aumentare il valore minimo se si ritiene che il traffico venga sostenuto per segnalare che il traffico potrebbe aumentare. 
![Impostazione dell'avviso WAF](./media/application-gateway-covid-guidelines/waf-setup-monitoring-alert.png)

> [!NOTE]
> È possibile impostare l'avviso affinché venga eseguito a una percentuale di utilizzo CU inferiore o superiore, a seconda del livello di riservatezza dei potenziali picchi di traffico.

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>Configurare WAF con la protezione dei dati e dei bot per arrestare gli attacchi
Se si vuole un ulteriore livello di sicurezza davanti all'applicazione, usare il gateway applicazione WAF_v2 SKU per le funzionalità WAF. È possibile configurare lo SKU V2 in modo da consentire solo l'accesso alle applicazioni da un determinato paese o paesi. Si configura una regola personalizzata di WAF per consentire o bloccare in modo esplicito il traffico in base alla georilevazione. Per altre informazioni, vedere la pagina relativa alle [regole personalizzate di geofiltro](https://docs.microsoft.com/azure/web-application-firewall/ag/geomatch-custom-rules) e [come configurare le regole personalizzate sul gateway applicazione WAF_v2 SKU tramite PowerShell](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules).

Abilitare bot Protection per bloccare i bot cattivi noti. Questa operazione dovrebbe ridurre la quantità di traffico per l'applicazione. Per ulteriori informazioni, vedere la pagina relativa [alla protezione di bot con le istruzioni di configurazione](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules).

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Abilitare la diagnostica nel gateway applicazione e WAF

I log di diagnostica consentono di visualizzare i log del firewall, i registri delle prestazioni e i log di accesso. È possibile usare questi log in Azure per gestire e risolvere i problemi relativi ai gateway applicazione. Per ulteriori informazioni, vedere la [documentazione di diagnostica](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#diagnostic-logging). 

## <a name="set-up-an-tls-policy-for-extra-security"></a>Configurare un criterio TLS per la sicurezza aggiuntiva
Assicurarsi di usare la versione più recente dei criteri TLS ([AppGwSslPolicy20170401S](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview#appgwsslpolicy20170401s)). Questa operazione impone TLS 1,2 e crittografie più complesse. Per altre informazioni, vedere [configurazione delle versioni dei criteri TLS e dei pacchetti di crittografia tramite PowerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-configure-ssl-policy-powershell).
