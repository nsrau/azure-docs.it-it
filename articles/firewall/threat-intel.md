---
title: Filtro basato su Intelligence per le minacce del firewall di Azure
description: I filtri basati sull'intelligence per le minacce possono essere abilitati per il firewall per la creazione di avvisi e il rifiuto del traffico da o verso indirizzi IP e domini dannosi noti.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: c291dbe9c1eb37e68174a2353e296a376c7d0896
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168667"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Filtro basato su Intelligence per le minacce del firewall di Azure

I filtri basati sull'intelligence per le minacce possono essere abilitati per il firewall per la creazione di avvisi e il rifiuto del traffico da o verso indirizzi IP e domini dannosi noti. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft. La soluzione Microsoft Threat Intelligence, basata su [Intelligent Security Graph](https://www.microsoft.com/en-us/security/operations/intelligence), viene usata da più servizi, tra cui Centro sicurezza di Azure.

![Intelligence per le minacce del firewall](media/threat-intel/firewall-threat.png)

Se è stato abilitato il filtro basato su Intelligence per le minacce, le regole associate vengono elaborate prima delle regole NAT, delle regole di rete o delle regole dell'applicazione.

È possibile scegliere di registrare un avviso solo quando viene attivata una regola oppure è possibile scegliere la modalità di avviso e di negazione.

Per impostazione predefinita, il filtro basato su Intelligence per le minacce è abilitato in modalità avviso. Non è possibile disattivare questa funzionalità o modificare la modalità finché l'interfaccia del portale non diventa disponibile nella propria area.

![Interfaccia del portale di filtro basato su Intelligence per le minacce](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>Log

L'Estratto di log seguente mostra una regola attivata:

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

- **Test in uscita** : gli avvisi del traffico in uscita devono essere un evento raro, perché significa che l'ambiente è stato compromesso. Per testare il funzionamento degli avvisi in uscita, è stato creato un FQDN di test che attiva un avviso. Usare **testmaliciousdomain.eastus.cloudapp.Azure.com** per i test in uscita.

- **Test in ingresso** : è possibile prevedere di visualizzare gli avvisi sul traffico in ingresso se le regole di DNAT sono configurate sul firewall. Questo vale anche se sono consentite solo origini specifiche nella regola DNAT e il traffico viene negato in altro modo. Il firewall di Azure non invia avvisi su tutti gli scanner di porta noti; solo sugli scanner noti per coinvolgere anche attività dannose.

## <a name="next-steps"></a>Passaggi successivi

- Vedere gli [esempi di log Analytics del firewall di Azure](log-analytics-samples.md)
- Informazioni su come [distribuire e configurare un firewall di Azure](tutorial-firewall-deploy-portal.md)
- Esaminare il [report di intelligence sulla sicurezza Microsoft](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)