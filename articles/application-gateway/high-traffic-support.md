---
title: Supporto per volumi di traffico elevato del gateway applicazioneApplication Gateway high traffic volume support
description: Questo articolo fornisce indicazioni per configurare il gateway applicazione di Azure a supporto di scenari con volumi di traffico di rete elevati.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: 65f404b52a5fc06d8fa5bb5aad291e57fde8caba
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617191"
---
# <a name="application-gateway-high-traffic-support"></a>Supporto del traffico elevato nel gateway applicazione

>[!NOTE]
> In questo articolo vengono descritte alcune linee guida suggerite per configurare il gateway applicazione per gestire il traffico aggiuntivo a causa di un volume di traffico elevato che può verificarsi a causa della crisi COVID-19.

È possibile utilizzare Gateway applicazione con Web Application Firewall (WAF) per un modo scalabile e sicuro per gestire il traffico verso le applicazioni Web.

I suggerimenti seguenti consentono di configurare il gateway applicazione con WAF per gestire il traffico aggiuntivo.

## <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>Usare lo SKU v2 superiore a v1 per le funzionalità di scalabilità automatica e i vantaggi in termini di prestazioniUse the v2 SKU over v1 for its autoscaling capabilities and performance benefits
Lo SKU v2 offre la scalabilità automatica per garantire che il gateway applicazione possa aumentare con l'aumento del traffico. Offre inoltre altri vantaggi significativi in termini di prestazioni, ad esempio prestazioni di offload TLS 5 volte migliori, tempi di distribuzione e aggiornamento più rapidi, ridondanza di zona e altro ancora rispetto alla versione 1. Per ulteriori informazioni, vedere la [documentazione v2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant). 

## <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>Impostare il numero massimo di istanze sul numero massimo possibile (125)
 
Supponendo di disporre di uno SKU del gateway applicazione v2, l'impostazione del numero massimo di istanze sul valore massimo possibile di 125 consente al gateway applicazione di scalare orizzontalmente in base alle esigenze. Ciò consente di gestire il possibile aumento del traffico verso le applicazioni. Ti verrà addebitato solo per le Unità di Capacità (CU) che usi.  

## <a name="set-your-minimum-instance-count-based-on-your-average-cu-usage"></a>Impostare il numero minimo di istanze in base all'utilizzo medio dell'unità CUSet your minimum instance count based on your average CU usage

Supponendo che si disponga di uno SKU del gateway applicazione v2, la scalabilità automatica richiede da sei a sette minuti per la scalabilità orizzontale. Con un numero minimo di istanze superiore, il gateway applicazione può gestire meglio il traffico quando il carico viene aumentato, perché un picco di traffico non richiede un'operazione di scalabilità automatica.  

## <a name="alert-if-a-certain-metric-surpasses-75-of-average-cu-utilization"></a>Avvisa se una determinata metrica supera il 75% dell'utilizzo medio dell'CU 
Per una spiegazione dettagliata delle metriche e di altre procedure dettagliate, vedere la [documentazione relativa alle metriche del gateway applicazione.](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-visualization) 

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>Esempio: impostazione di un avviso sul 75% dell'utilizzo medio dell'unità CU

Questo esempio illustra come usare il portale di Azure per configurare un avviso quando viene raggiunto il 75% dell'utilizzo medio dell'aggiornamento della CU. 
1. Passare al **gateway applicazione**.
2. Nel riquadro sinistro selezionare **Metriche** nella scheda **Monitoraggio.On** the left panel, select Metrics under the Monitoring tab. 
3. Aggiungere una metrica per Unità di **calcolo correnti medie**. 
![Configurazione della metrica WAF](./media/application-gateway-covid-guidelines/waf-setup-metrics.png)
4. Se è stato impostato il numero minimo di istanze come utilizzo medio dell'unità CU, impostare un avviso quando il 75% delle istanze minime è in uso. Ad esempio, se l'utilizzo medio è di 10 CUs, impostare un avviso su 7,5 CUs.For example, if your average usage is 10 CUs, set an alert on 7.5 CUs. Questo ti avvisa se l'utilizzo è in aumento e ti dà il tempo di rispondere. Puoi aumentare il minimo se pensi che questo traffico sarà sostenuto per avvisarti che il traffico potrebbe aumentare. 
![Impostazione dell'avviso WAF](./media/application-gateway-covid-guidelines/waf-setup-monitoring-alert.png)

> [!NOTE]
> È possibile impostare l'avviso in modo che si verifichi a una percentuale di utilizzo dell'CU inferiore o superiore a seconda della sensibilità ai potenziali picchi di traffico.

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>Configurare WAF con geofiltro e protezione bot per fermare gli attacchi
Se si desidera un ulteriore livello di sicurezza davanti all'applicazione, usare lo SKU WAF_v2 gateway applicazione per le funzionalità WAF. È possibile configurare lo SKU v2 per consentire l'accesso alle applicazioni solo da uno o più paesi. Impostare una regola personalizzata WAF per consentire o bloccare in modo esplicito il traffico in base alla georilevazione. Per altre informazioni, vedere [regole personalizzate di geofiltro](https://docs.microsoft.com/azure/web-application-firewall/ag/geomatch-custom-rules) e [come configurare regole personalizzate in WAF_v2 SKU del gateway applicazione tramite PowerShell.](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules)

Abilitare la protezione bot per bloccare i bot non noti. Questo dovrebbe ridurre la quantità di traffico che ottiene all'applicazione. Per altre informazioni, vedere [Protezione dei bot con istruzioni di configurazione](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules).

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Attivare la diagnostica in Application Gateway e WAF

I log di diagnostica consentono di visualizzare i registri del firewall, i registri delle prestazioni e i log di accesso. È possibile usare questi log in Azure per gestire e risolvere i problemi dei gateway applicazione. Per ulteriori informazioni, vedere la [documentazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#diagnostic-logging)relativa alla diagnostica . 

## <a name="set-up-an-tls-policy-for-extra-security"></a>Configurare un criterio TLS per una maggiore sicurezzaSet up an TLS policy for extra security
Assicurarsi di utilizzare la versione più recente dei criteri TLS ([AppGwSslPolicy20170401S](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview#appgwsslpolicy20170401s)). Questo impone TLS 1.2 e cifrature più forti. Per ulteriori informazioni, vedere [Configurazione delle versioni dei criteri TLS e dei pacchetti di crittografia tramite PowerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-configure-ssl-policy-powershell).
