---
title: Elenchi di esclusione del firewall dell'applicazione Web in Azure Front Door - Portale di Azure
description: Questo articolo fornisce informazioni sulla configurazione degli elenchi di esclusione in Fronte di Azure con il portale di Azure.This article provides information on exclusion lists configuration in Azure Front with the Azure portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/25/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 6ed382e88700e4ecd7f8de20a2c8da7ed3c13566
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925930"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Web Application Firewall (WAF) con elenchi di esclusione di Front Door Service 

A volte Web Application Firewall (WAF) potrebbe bloccare una richiesta che si desidera consentire per l'applicazione. Ad esempio, Active Directory inserisce i token utilizzati per l'autenticazione. Questi token possono contenere caratteri speciali che possono attivare un falso positivo dalle regole WAF. Gli elenchi di esclusione di Web Application firewall consentono agli utenti di omettere determinati attributi di richiesta da una valutazione di WAF.  È possibile configurare un elenco di esclusione usando PowserShell , [l'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add)di Azure, [l'API Rest](https://docs.microsoft.com/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)o il portale di Azure.An exclusion list can be configured using [PowserShell](https://docs.microsoft.com/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-3.5.0), Azure CLI, Rest API, or the Azure portal. L'esempio seguente mostra la configurazione del portale di Azure.The following example shows the Azure portal configuration. 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>Configurare gli elenchi di esclusione tramite il portale di AzureConfigure exclusion lists using the Azure portal
**Gestire le esclusioni** è accessibile dal portale WAF in **Regole gestite**

![Gestire](../media/waf-front-door-exclusion/exclusion1.png)
![le exclusion_add di esclusione](../media/waf-front-door-exclusion/exclusion2.png)

 Esempio di elenco ![di esclusione: Gestire exclusion_define](../media/waf-front-door-exclusion/exclusion3.png)

In questo esempio viene escluso il valore nel campo dell'intestazione *utente.* Una richiesta valida può includere il campo *utente* che contiene una stringa che attiva una regola di inserimento SQL. In questo caso è possibile escludere il parametro *utente* in modo che la regola WAF non valuti nulla nel campo.

Gli attributi seguenti possono essere aggiunti agli elenchi di esclusione in base al nome. I valori dei campi utilizzati non vengono valutati in base alle regole WAF, ma i relativi nomi vengono valutati. Gli elenchi di esclusione rimuovono l'ispezione del valore del campo.

* Nome intestazione richiesta
* Richiedi nome cookie
* Nome degli argomenti della stringa di query
* Nome post args della richiesta

È possibile specificare un'esatta intestazione di richiesta, un corpo, un cookie o una corrispondenza dell'attributo stringa della query.  In alternativa, è possibile specificare facoltativamente corrispondenze parziali. I seguenti operatori sono i criteri di corrispondenza supportati:

- **Uguale a**: questo operatore viene usato per una corrispondenza esatta. Ad esempio, per selezionare un'intestazione denominata **bearerToken**, utilizzare l'operatore equals con il selettore impostato come **bearerToken**.
- **Inizia con**: questo operatore corrisponde a tutti i campi che iniziano con il valore del selettore specificato.
- **Termina con**: questo operatore corrisponde a tutti i campi di richiesta che terminano con il valore del selettore specificato.
- **Contiene**: questo operatore corrisponde a tutti i campi di richiesta che contengono il valore del selettore specificato.
- **Uguale a qualsiasi**: questo operatore corrisponde a tutti i campi di richiesta. : è il valore del selettore.

Per i nomi di intestazione e di cookie non viene fatta distinzione tra maiuscole e minuscole.

È possibile applicare l'elenco di esclusione a tutte le regole all'interno del set di regole gestite, alle regole per un gruppo di regole specifico o a una singola regola, come illustrato nell'esempio precedente. 

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato le impostazioni WAF, scopri come visualizzare i log WAF. Per ulteriori informazioni, vedere [Diagnostica porta anteriore](../afds/waf-front-door-monitor.md).
