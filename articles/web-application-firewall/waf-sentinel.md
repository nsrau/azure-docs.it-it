---
title: Uso di Azure Sentinel con il firewall applicazione Web di Azure
description: Questo articolo illustra come usare Sentinel di Azure con il Web Application Firewall (WAF) di Azure
services: web-application-firewall
author: TreMansdoerfer
ms.service: web-application-firewall
ms.date: 10/12/2020
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: 525ec334e73ca010d319b40ab864d08dae32f493
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91997380"
---
# <a name="using-azure-sentinel-with-azure-web-application-firewall"></a>Uso di Azure Sentinel con il firewall applicazione Web di Azure

Il Web Application Firewall (WAF) di Azure combinato con Sentinel di Azure può fornire la gestione degli eventi di informazioni di sicurezza per le risorse di WAF. Azure Sentinel fornisce analisi della sicurezza con Log Analytics, che consente di suddividere e visualizzare facilmente i dati WAF. Con Sentinel è possibile accedere alle cartelle di lavoro predefinite e modificarle per adattarle alle esigenze dell'organizzazione. La cartella di lavoro può visualizzare le analisi per WAF nella rete per la distribuzione di contenuti (CDN) di Azure, WAF in un front-end di Azure e WAF sul gateway applicazione in diverse sottoscrizioni e aree di lavoro.

## <a name="waf-log-analytics-categories"></a>Categorie di log Analytics di WAF

WAF log Analytics sono suddivisi nelle categorie seguenti:  

- Tutte le azioni WAF eseguite 
- Primi 40 indirizzi URI di richiesta bloccati 
- Primi 50 trigger evento,  
- Messaggi nel tempo 
- Dettagli messaggio completo 
- Eventi di attacco per messaggio  
- Eventi di attacco nel tempo 
- Filtro ID di rilevamento 
- Messaggi ID di rilevamento 
- Primi 10 indirizzi IP di attacco 
- Messaggi di attacco degli indirizzi IP 

## <a name="waf-workbook-examples"></a>Esempi di cartella di lavoro WAF

Gli esempi di cartella di lavoro WAF seguenti illustrano i dati di esempio:

:::image type="content" source="media//waf-sentinel/waf-actions-filter.png" alt-text="Filtro azioni WAF":::

:::image type="content" source="media//waf-sentinel/top-50-event-trigger.png" alt-text="Filtro azioni WAF":::

:::image type="content" source="media//waf-sentinel/attack-events.png" alt-text="Filtro azioni WAF":::

:::image type="content" source="media//waf-sentinel/top-10-attacking-ip-address.png" alt-text="Filtro azioni WAF":::

## <a name="launch-a-waf-workbook"></a>Avviare una cartella di lavoro di WAF

La cartella di lavoro WAF funziona per tutti i WAFs di Azure, il gateway applicazione e la rete CDN. Prima di connettere i dati da queste risorse, è necessario abilitare log Analytics per la risorsa. 

Per abilitare log Analytics per ogni risorsa, passare alla risorsa di Azure, al gateway applicazione o alla rete CDN del singolo utente:

1. Selezionare **Impostazioni di diagnostica**.
2. Fare clic su **+ Aggiungi impostazione di diagnostica**. 
3. Nella pagina delle impostazioni di diagnostica:
   1. Digitare un nome. 
   1. Selezionare **Invia a Log Analytics**. 
   1. Scegliere l'area di lavoro destinazione log. 
   1. Selezionare i tipi di log che si desidera analizzare:
      1. Gateway applicazione:' ApplicationGatewayAccessLog ' è ApplicationGatewayFirewallLog '
      1. Sportello anteriore di Azure:' FrontDoorAccessLog ' è FrontDoorFirewallLog '
      1. Rete CDN:' AzureCdnAccessLog '
   1. Selezionare **Salva**.

   :::image type="content" source="media//waf-sentinel/diagnostics-setting.png" alt-text="Filtro azioni WAF":::

4. Nel home page di Azure, digitare **Azure Sentinel** nella barra di ricerca e selezionare la risorsa **Azure Sentinel** . 
2. Selezionare un'area di lavoro già attiva o creare una nuova area di lavoro in Sentinel. 
3. Nel pannello laterale sinistro in **configurazione** selezionare **connettori dati**.
4. Cercare **microsoft Web Application Firewall** e selezionare **Microsoft Web Application Firewall (WAF)**. Selezionare **Apri connettore** nella parte inferiore destra.

   :::image type="content" source="media//waf-sentinel/data-connectors.png" alt-text="Filtro azioni WAF":::

8. Seguire le istruzioni in **configurazione** per ogni risorsa WAF per cui si vogliono ottenere i dati analitici di log, se non è già stato fatto in precedenza.
6. Al termine della configurazione delle singole risorse di WAF, selezionare la scheda **passaggi successivi** . Selezionare una delle cartelle di lavoro consigliate. Questa cartella di lavoro userà tutti i dati analitici di log abilitati in precedenza. È ora disponibile una cartella di lavoro WAF funzionante per le risorse di WAF.

   :::image type="content" source="media//waf-sentinel/waf-workbooks.png" alt-text="Filtro azioni WAF":::


## <a name="next-steps"></a>Passaggi successivi

- [Scopri di più su Azure Sentinel](../sentinel/overview.md)
- [Altre informazioni sulle cartelle di lavoro di monitoraggio di Azure](../azure-monitor/platform/workbooks-overview.md)
