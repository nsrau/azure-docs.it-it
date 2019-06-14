---
title: Filtro basato su intelligence per le minacce Firewall Azure
description: Informazioni sui filtri di Firewall Azure threat intelligence
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: 4ef9089c94d9e806cc519c4f8243cdcb7e73953a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60194048"
---
# <a name="azure-firewall-threat-intelligence-based-filtering---public-preview"></a>Filtro delle minacce in Azure Firewall basato su intelligence - versione di anteprima pubblica

I filtri basati sull'intelligence per le minacce possono essere abilitati per il firewall per la creazione di avvisi e il rifiuto del traffico da o verso indirizzi IP e domini dannosi noti. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft. [Intelligent Security Graph](https://www.microsoft.com/en-us/security/operations/intelligence) alla base di intelligence sulle minacce di Microsoft ed è usato da più servizi, tra cui Centro sicurezza di Azure.

![Intelligence per le minacce firewall](media/threat-intel/firewall-threat.png)

> [!IMPORTANT]
> Threat intelligence basato il filtraggio è attualmente in anteprima pubblica e viene fornito con un contratto di servizio di anteprima. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.  Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se threat intelligence in base al filtro è abilitato, le regole associate vengono elaborate prima delle regole NAT, regole di rete o le regole di applicazione. Durante l'anteprima, sono inclusi solo i record di confidenza più elevati.

È possibile accedere solo un avviso quando viene attivata una regola oppure è possibile scegliere di avviso e negare modalità.

Per impostazione predefinita, la filtro basato su intelligence delle minacce è abilitato in modalità degli avvisi. Non è possibile disattivare questa funzionalità o modificare la modalità finché l'interfaccia del portale non è disponibile nella propria area.

![Intelligence per le minacce basato su interfaccia del portale del filtro](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>Log

L'estratto seguente di log contiene una regola di attivata:

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

- **Test in uscita** -gli avvisi del traffico in uscita devono essere un caso raro, in quanto significa che l'ambiente sia stata compromessa. Per gli avvisi in uscita test lavora, un test di nome di dominio completo è stato creato che attiva un avviso. Uso **testmaliciousdomain.eastus.cloudapp.azure.com** per i test in uscita.

- **Connessioni in entrata test** -sarà possibile visualizzare gli avvisi sul traffico in ingresso se DNAT regole sono configurate sul firewall. Questo vale anche se sono consentite solo origini specifiche sulla regola DNAT e il traffico viene negato in caso contrario. Firewall di Azure non invia alcun avviso in tutti gli scanner di porta nota; solo in strumenti di analisi che notoriamente coinvolgere anche in attività dannose.

## <a name="next-steps"></a>Passaggi successivi

- Vedere [esempi Analitica Log Firewall di Azure](log-analytics-samples.md)
- Informazioni su come [distribuire e configurare un Firewall di Azure](tutorial-firewall-deploy-portal.md)
- Esaminare il [report di intelligence per Microsoft Security](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)