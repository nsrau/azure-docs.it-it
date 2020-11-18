---
title: Supporto di volumi di traffico elevati nel gateway applicazione
description: Questo articolo fornisce indicazioni per configurare il gateway applicazione di Azure per il supporto di scenari con volumi elevati di traffico di rete.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: 3854e7f3c19f1724a2df1508c9fa519809e07ba9
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658673"
---
# <a name="application-gateway-high-traffic-support"></a>Supporto del traffico elevato nel gateway applicazione

>[!NOTE]
> Questo articolo descrive alcune linee guida consigliate che consentono di configurare il gateway applicazione in modo da gestire il traffico aggiuntivo per qualsiasi volume di traffico elevato che può verificarsi. Le soglie di avviso sono puramente suggerimenti e natura generica. Gli utenti possono determinare le soglie di avviso in base al carico di lavoro e alle aspettative di utilizzo.

È possibile usare il gateway applicazione con web application firewall (WAF) per un modo sicuro e scalabile di gestire il traffico verso le applicazioni Web.

È importante ridimensionare il gateway applicazione in base al traffico e con una quantità di buffer in modo da prepararsi a eventuali picchi di traffico o picchi e ridurre al minimo l'effetto che può avere nel QoS. I suggerimenti seguenti consentono di configurare il gateway applicazione con WAF per gestire il traffico aggiuntivo.

Per l'elenco completo delle metriche offerte dal gateway applicazione, vedere la [documentazione relativa alle metriche](./application-gateway-metrics.md) . Vedere [visualizzare le metriche](./application-gateway-metrics.md#metrics-visualization) nella portale di Azure e la documentazione di [monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric) per informazioni su come impostare gli avvisi per le metriche.

## <a name="scaling-for-application-gateway-v1-sku-standardwaf-sku"></a>Scalabilità per lo SKU del gateway applicazione V1 (SKU standard/WAF)

### <a name="set-your-instance-count-based-on-your-peak-cpu-usage"></a>Impostare il numero di istanze in base all'utilizzo massimo della CPU
Se si usa un gateway SKU V1, sarà possibile impostare il gateway applicazione fino a 32 istanze per la scalabilità. Controllare l'utilizzo della CPU del gateway applicazione nell'ultimo mese per i picchi superiori al 80%, disponibile come metrica per il monitoraggio. È consigliabile impostare il numero di istanze in base all'utilizzo di picco e con un buffer aggiuntivo dal 10% al 20% per tenere conto di eventuali picchi di traffico.

### <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>Usare lo SKU v2 piuttosto che la versione 1 per le funzionalità di scalabilità automatica e i vantaggi a livello di prestazioni
Lo SKU v2 offre funzionalità di scalabilità automatica per garantire che il gateway applicazione possa aumentare le prestazioni in caso di aumento del traffico. Offre anche altri vantaggi significativi a livello di prestazioni, ad esempio prestazioni di offload TLS 5 volte migliori, tempi di distribuzione e aggiornamento più rapidi, ridondanza della zona e molto altro rispetto alla versione v1. Per ulteriori informazioni, vedere la [documentazione V2](./application-gateway-autoscaling-zone-redundant.md) e vedere la [documentazione relativa alla migrazione](./migrate-v1-v2.md) da V1 a V2 per informazioni su come eseguire la migrazione dei gateway SKU V1 esistenti allo SKU V2. 

## <a name="autoscaling-for-application-gateway-v2-sku-standard_v2waf_v2-sku"></a>Scalabilità automatica per lo SKU del gateway applicazione V2 (SKU Standard_v2/WAF_v2)

### <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>Impostare il numero massimo di istanze sul massimo possibile (125)
 
Per lo SKU del gateway applicazione V2, impostando il numero massimo di istanze sul valore massimo possibile di 125, il gateway applicazione può scalare in orizzontale in base alle esigenze. Ciò permette di gestire il possibile aumento del traffico per le applicazioni. Verranno addebitati solo i costi per le unità di capacità usate. 

Assicurarsi di controllare le dimensioni della subnet e il numero di indirizzi IP disponibili nella subnet e impostare il numero massimo di istanze in base a tale valore. Se la subnet non dispone di spazio sufficiente per contenere, sarà necessario ricreare il gateway nella stessa subnet o in una subnet diversa con capacità sufficiente. 

### <a name="set-your-minimum-instance-count-based-on-your-average-compute-unit-usage"></a>Imposta il numero minimo di istanze in base all'utilizzo medio delle unità di calcolo

Per lo SKU del gateway applicazione V2, la scalabilità automatica richiede da sei a sette minuti per la scalabilità orizzontale e il provisioning di un set aggiuntivo di istanze pronte per l' Fino ad allora, in caso di picchi di traffico brevi, le istanze del gateway esistenti potrebbero essere sottoposte a stress e questo potrebbe causare una latenza o una perdita di traffico imprevista. 

Si consiglia di impostare il numero minimo di istanze su un livello ottimale. Se, ad esempio, sono necessarie 50 istanze per gestire il traffico al picco del carico, è consigliabile impostare il valore minimo da 25 a 30, anziché <10, in modo che anche quando ci sono picchi di traffico brevi, il gateway applicazione può gestirlo e fornire tempo sufficiente per la scalabilità automatica per rispondere e rendere effettive le prestazioni.

Controllare la metrica dell'unità di calcolo per l'ultimo mese. Metrica unità di calcolo è una rappresentazione dell'utilizzo della CPU del gateway e, in base all'utilizzo massimo diviso 10, è possibile impostare il numero minimo di istanze necessarie. Si noti che 1 istanza del gateway applicazione può gestire almeno 10 unità di calcolo

## <a name="manual-scaling-for-application-gateway-v2-sku-standard_v2waf_v2"></a>Ridimensionamento manuale per lo SKU del gateway applicazione V2 (Standard_v2/WAF_v2)

### <a name="set-your-instance-count-based-on-your-peak-compute-unit-usage"></a>Imposta il numero di istanze in base al picco di utilizzo delle unità di calcolo 

Diversamente dalla scalabilità automatica, nel ridimensionamento manuale è necessario impostare manualmente il numero di istanze del gateway applicazione in base ai requisiti di traffico. È consigliabile impostare il numero di istanze in base all'utilizzo di picco e con un buffer aggiuntivo dal 10% al 20% per tenere conto di eventuali picchi di traffico. Se, ad esempio, il traffico richiede 50 istanze a picco, effettuare il provisioning di 55 a 60 istanze per gestire picchi di traffico imprevisti che possono verificarsi.

Controllare la metrica dell'unità di calcolo per l'ultimo mese. Metrica unità di calcolo è una rappresentazione dell'utilizzo della CPU del gateway e in base all'utilizzo di picco diviso 10. è possibile impostare il numero di istanze necessarie, poiché 1 istanza del gateway applicazione può gestire almeno 10 unità di calcolo

## <a name="monitoring-and-alerting"></a>Monitoraggio e avviso

Per ricevere notifiche relative a traffico o anomalie di utilizzo, è possibile configurare avvisi per determinate metriche. Vedere la [documentazione relativa alle metriche](./application-gateway-metrics.md) per l'elenco completo delle metriche offerte dal gateway applicazione. Vedere [visualizzare le metriche](./application-gateway-metrics.md#metrics-visualization) nella portale di Azure e la documentazione di [monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric) per informazioni su come impostare gli avvisi per le metriche.

## <a name="alerts-for-application-gateway-v1-sku-standardwaf"></a>Avvisi per lo SKU del gateway applicazione V1 (standard/WAF)

### <a name="alert-if-average-cpu-utilization-crosses-80"></a>Avviso se l'utilizzo medio della CPU supera il 80%

In condizioni normali, l'utilizzo della CPU non deve superare regolarmente il 90%, in quanto ciò potrebbe causare una latenza nei siti Web ospitati dietro il gateway applicazione e compromettere l'esperienza del client. È possibile controllare indirettamente o migliorare l'utilizzo della CPU modificando la configurazione del gateway applicazione aumentando il numero di istanze o passando a dimensioni SKU maggiori o eseguendo entrambe le azioni. Impostare un avviso se la metrica di utilizzo della CPU supera la media del 80%.

### <a name="alert-if-unhealthy-host-count-crosses-threshold"></a>Avviso se il numero di host non integri supera la soglia

Questa metrica indica il numero di server back-end che il gateway applicazione non è in grado di verificare correttamente. Questo consente di rilevare i problemi in cui le istanze del gateway applicazione non sono in grado di connettersi al back-end. Avviso se questo numero supera il 20% della capacità di back-end. ad esempio Se al momento sono presenti 30 server back-end nel pool back-end, impostare un avviso se il numero di host non integri supera i 6.

### <a name="alert-if-response-status-4xx-5xx-crosses-threshold"></a>Avviso se lo stato della risposta (4xx, 5xx) supera la soglia 

Creare un avviso quando lo stato della risposta del gateway applicazione è 4xx o 5xx. Potrebbe verificarsi una risposta occasionale 4xx o 5xx a causa di problemi temporanei. È necessario osservare il gateway in produzione per determinare la soglia statica o usare la soglia dinamica per l'avviso.

### <a name="alert-if-failed-requests-crosses-threshold"></a>Avviso se le richieste non riuscite superano la soglia 

Crea avviso quando la metrica richieste non riuscite supera la soglia. È necessario osservare il gateway in produzione per determinare la soglia statica o usare la soglia dinamica per l'avviso.

## <a name="alerts-for-application-gateway-v2-sku-standard_v2waf_v2"></a>Avvisi per lo SKU del gateway applicazione V2 (Standard_v2/WAF_v2)

### <a name="alert-if-compute-unit-utilization-crosses-75-of-average-usage"></a>Avviso se l'utilizzo delle unità di calcolo supera il 75% dell'utilizzo medio 

Unità di calcolo è la misura dell'utilizzo di calcolo del gateway applicazione. Controllare l'utilizzo medio delle unità di calcolo nell'ultimo mese e impostare un avviso se ne supera il 75%. Ad esempio, se l'utilizzo medio è pari a 10 unità di calcolo, impostare un avviso su 7,5 CUs. In questo modo viene visualizzato un avviso se l'utilizzo aumenta e si ha quindi tempo per rispondere. È possibile aumentare il valore minimo se si ritiene che il traffico sarà sostenuto per segnalare che il traffico potrebbe aumentare. Seguire i suggerimenti per la scalabilità riportati sopra per aumentare la scalabilità orizzontale secondo necessità.

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

### <a name="alert-if-capacity-unit-utilization-crosses-75-of-peak-usage"></a>Avviso se l'utilizzo delle unità di capacità supera il 75% di utilizzo massimo 

Le unità di capacità rappresentano l'utilizzo complessivo del gateway in termini di velocità effettiva, calcolo e conteggio delle connessioni. Controllare l'utilizzo dell'unità di capacità massima nell'ultimo mese e impostare un avviso se il valore supera il 75%. Se, ad esempio, l'utilizzo massimo è di 100 unità di capacità, impostare un avviso su 75 CUs. Seguire i due suggerimenti sopra riportati per aumentare il livello di scalabilità orizzontale, se necessario.

### <a name="alert-if-unhealthy-host-count-crosses-threshold"></a>Avviso se il numero di host non integri supera la soglia 

Questa metrica indica il numero di server back-end che il gateway applicazione non è in grado di verificare correttamente. Questo consente di rilevare i problemi in cui le istanze del gateway applicazione non sono in grado di connettersi al back-end. Avviso se questo numero supera il 20% della capacità di back-end. ad esempio Se al momento sono presenti 30 server back-end nel pool back-end, impostare un avviso se il numero di host non integri supera i 6.

### <a name="alert-if-response-status-4xx-5xx-crosses-threshold"></a>Avviso se lo stato della risposta (4xx, 5xx) supera la soglia 

Creare un avviso quando lo stato della risposta del gateway applicazione è 4xx o 5xx. Potrebbe verificarsi una risposta occasionale 4xx o 5xx a causa di problemi temporanei. È necessario osservare il gateway in produzione per determinare la soglia statica o usare la soglia dinamica per l'avviso.

### <a name="alert-if-failed-requests-crosses-threshold"></a>Avviso se le richieste non riuscite superano la soglia 

Crea avviso quando la metrica richieste non riuscite supera la soglia. È necessario osservare il gateway in produzione per determinare la soglia statica o usare la soglia dinamica per l'avviso.

### <a name="alert-if-backend-last-byte-response-time-crosses-threshold"></a>Avvisa se il tempo di risposta ultimo byte del back-end supera la soglia 

Questa metrica indica l'intervallo di tempo tra l'inizio del tentativo di stabilire una connessione al server back-end e la ricezione dell'ultimo byte del corpo della risposta. Creare un avviso se la latenza della risposta back-end è maggiore di quella determinata dalla consueta soglia. Ad esempio, impostarlo in modo che venga generato un avviso quando la latenza della risposta back-end aumenta di oltre il 30% rispetto al consueto valore.

### <a name="alert-if-application-gateway-total-time-crosses-threshold"></a>Avviso se il tempo totale del gateway applicazione supera la soglia

Si tratta dell'intervallo dal momento in cui il gateway applicazione riceve il primo byte della richiesta HTTP al momento in cui l'ultimo byte di risposta è stato inviato al client. Deve creare un avviso se la latenza della risposta back-end è maggiore di quella determinata dalla consueta soglia. Ad esempio, possono impostarlo in modo che venga generato un avviso quando la latenza totale del tempo aumenta di oltre il 30% rispetto al consueto valore.

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>Configurare WAF con filtri geografici e protezione dei bot per arrestare gli attacchi
Se si vuole un ulteriore livello di sicurezza per l'applicazione, usare il gateway applicazione WAF_v2 SKU per le funzionalità WAF. È possibile configurare lo SKU v2 in modo da consentire solo l'accesso alle applicazioni da un determinato paese/area geografica o da più aree geografiche/paesi. Si configura una regola personalizzata di WAF per consentire o bloccare in modo esplicito il traffico in base alla georilevazione. Per altre informazioni, vedere [Regole personalizzate di corrispondenza geografica](../web-application-firewall/ag/geomatch-custom-rules.md) e [Come configurare regole personalizzate per il gateway applicazione WAF_v2 SKU tramite PowerShell](../web-application-firewall/ag/configure-waf-custom-rules.md).

Abilitare la protezione dai bot per bloccare i bot dannosi noti. Questa operazione dovrebbe ridurre la quantità di traffico per l'applicazione. Per altre informazioni, vedere la pagina relativa alla [protezione dai bot con istruzioni di configurazione](../web-application-firewall/ag/configure-waf-custom-rules.md).

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Attivare la diagnostica per il gateway applicazione e WAF

I log di diagnostica consentono di visualizzare i log del firewall, i log delle prestazioni e i log di accesso. È possibile usare questi log in Azure per gestire e risolvere i problemi dei gateway applicazione. Per altre informazioni, vedere la [documentazione relativa alla diagnostica](./application-gateway-diagnostics.md#diagnostic-logging). 

## <a name="set-up-an-tls-policy-for-extra-security"></a>Configurare un criterio TLS per maggiore sicurezza
Assicurarsi di usare la versione più recente dei criteri TLS ([AppGwSslPolicy20170401S](./application-gateway-ssl-policy-overview.md#appgwsslpolicy20170401s)) per imporre l'uso di TLS 1.2 e crittografie più complesse. Per altre informazioni, vedere [Configurazione delle versioni dei criteri TLS e dei pacchetti di crittografia tramite PowerShell](./application-gateway-configure-ssl-policy-powershell.md).