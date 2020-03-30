---
title: Filtro basato su Threat Intelligence di Azure FirewallAzure Firewall threat intelligence based filtering
description: I filtri basati sull'intelligence per le minacce possono essere abilitati per il firewall per la creazione di avvisi e il rifiuto del traffico da o verso indirizzi IP e domini dannosi noti.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: c291dbe9c1eb37e68174a2353e296a376c7d0896
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168667"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Filtro basato su threat intelligence di Azure FirewallAzure Firewall threat-based filtering

I filtri basati sull'intelligence per le minacce possono essere abilitati per il firewall per la creazione di avvisi e il rifiuto del traffico da o verso indirizzi IP e domini dannosi noti. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft. [Intelligent Security Graph](https://www.microsoft.com/en-us/security/operations/intelligence) potenzia Microsoft threat intelligence e viene utilizzato da più servizi, tra cui il Centro sicurezza di Azure.Intelligent Security Graph powers Microsoft threat intelligence and is used by multiple services including Azure Security Center.

![Informazioni sulle minacce del firewall](media/threat-intel/firewall-threat.png)

Se è stato abilitato il filtro basato su threat intelligence, le regole associate vengono elaborate prima di qualsiasi regola NAT, regole di rete o regole dell'applicazione.

È possibile scegliere di registrare un avviso solo quando viene attivata una regola oppure è possibile scegliere la modalità di avviso e negazione.

Per impostazione predefinita, il filtro basato su Threat Intelligence è abilitato in modalità di avviso. Non è possibile disattivare questa funzionalità o modificare la modalità finché l'interfaccia del portale non diventa disponibile nella propria area geografica.

![Interfaccia del portale di filtraggio basato su Threat Intelligence](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>Log

Il seguente estratto del log mostra una regola attivata:

```
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>Test

- **Test in uscita:** gli avvisi sul traffico in uscita devono essere un evento raro, in quanto significa che l'ambiente è stato compromesso. Per testare il funzionamento degli avvisi in uscita, è stato creato un nome di dominio completo di test che attiva un avviso. Utilizzare **testmaliciousdomain.eastus.cloudapp.azure.com** per i test in uscita.

- **Test in ingresso:** è possibile visualizzare avvisi sul traffico in ingresso se le regole DNAT sono configurate nel firewall. Ciò vale anche se sono consentite solo fonti specifiche sulla regola DNAT e il traffico viene altrimenti negato. Firewall di Azure non viene avvisato in tutti gli scanner di porte noti. solo su scanner che sono noti per impegnarsi anche in attività dannose.

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Esempi di Azure Firewall Log AnalyticsSee Azure Firewall Log Analytics samples](log-analytics-samples.md)
- Informazioni su come [distribuire e configurare un firewall](tutorial-firewall-deploy-portal.md) di AzureLearn how to deploy and configure an Azure Firewall
- Esaminare il [report Microsoft Security Intelligence](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)