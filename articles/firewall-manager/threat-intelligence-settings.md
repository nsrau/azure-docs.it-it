---
title: Configurazione di intelligence per le minacce di Azure firewall
description: Informazioni su come configurare il filtro basato su Intelligence per le minacce per i criteri del firewall di Azure per segnalare e negare il traffico da e verso domini e indirizzi IP dannosi noti.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: a663c5f3bcf3492c4a9bc74fe93c6ed6a86137ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530642"
---
# <a name="azure-firewall-threat-intelligence-configuration"></a>Configurazione di intelligence per le minacce di Azure firewall

È possibile configurare il filtro basato sull'Intelligence per le minacce per i criteri del firewall di Azure in modo da avvisare e negare il traffico da e verso domini e indirizzi IP dannosi noti. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft. La soluzione Microsoft Threat Intelligence, basata su [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence), viene usata da più servizi, tra cui Centro sicurezza di Azure.<br>

Se è stato configurato il filtro basato su Intelligence per le minacce, le regole associate vengono elaborate prima delle regole NAT, delle regole di rete o delle regole dell'applicazione.

:::image type="content" source="media/threat-intelligence-settings/threat-intelligence-policy.png" alt-text="Criteri di intelligence per le minacce":::

## <a name="threat-intelligence-mode"></a>Modalità di intelligence per le minacce

È possibile scegliere di registrare solo un avviso quando viene attivata una regola oppure è possibile scegliere la modalità di avviso e di negazione.

Per impostazione predefinita, il filtro basato sull'intelligence sulle minacce è abilitato in modalità di avviso.

## <a name="allowed-list-addresses"></a>Indirizzi elenco consentiti

È possibile configurare un elenco di indirizzi IP consentiti in modo che Intelligence per le minacce non filtri alcun indirizzo, intervallo o subnet specificato.



## <a name="logs"></a>Log

L'estratto di log seguente mostra una regola attivata:

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

- **Test in uscita**: gli avvisi del traffico in uscita dovrebbero essere un evento raro, poiché indicano che l'ambiente è stato compromesso. Per testare il funzionamento degli avvisi in uscita, è stato creato un nome di dominio completo di test che attiva un avviso. Usare **testmaliciousdomain.eastus.cloudapp.azure.com** per i test in uscita.

- **Test in ingresso**: gli avvisi sul traffico in ingresso vengono in genere visualizzati se le regole DNAT sono configurate sul firewall. Questo vale anche se sono consentite solo origini specifiche nella regola DNAT e il traffico viene negato negli altri casi. Il firewall di Azure non invia avvisi su tutti gli scanner di porta noti, ma solo su quelli che sono anche notoriamente coinvolti in attività dannose.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare il [Microsoft Security Intelligence Report](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)