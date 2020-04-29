---
title: Elenchi di esclusione di Web Application Firewall in Azure front door-portale di Azure
description: Questo articolo fornisce informazioni sulla configurazione degli elenchi di esclusione in Azure con il portale di Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/25/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 6ed382e88700e4ecd7f8de20a2c8da7ed3c13566
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77925930"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Web Application Firewall (WAF) con elenchi di esclusione del servizio front door 

A volte Web Application Firewall (WAF) potrebbe bloccare una richiesta che si desidera consentire per l'applicazione. Ad esempio, Active Directory inserisce i token usati per l'autenticazione. Questi token possono contenere caratteri speciali che possono attivare un falso positivo dalle regole WAF. Gli elenchi di esclusione di Web Application firewall consentono agli utenti di omettere determinati attributi di richiesta da una valutazione di WAF.  È possibile configurare un elenco di esclusioni usando [PowserShell](https://docs.microsoft.com/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-3.5.0), l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add), l' [API REST](https://docs.microsoft.com/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)o la portale di Azure. Nell'esempio seguente viene illustrata la configurazione del portale di Azure. 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>Configurare gli elenchi di esclusione usando il portale di Azure
**Gestire le esclusioni** è accessibile dal portale di WAF in **regole gestite**

![Gestisci](../media/waf-front-door-exclusion/exclusion1.png)
![gestione esclusioni exclusion_add](../media/waf-front-door-exclusion/exclusion2.png)

 Elenco di esclusione di esempio ![: gestisci exclusion_define](../media/waf-front-door-exclusion/exclusion3.png)

In questo esempio viene escluso il valore nel campo dell'intestazione *utente* . Una richiesta valida può includere il campo *utente* che contiene una stringa che attiva una regola SQL injection. In questo caso, è possibile escludere il parametro *User* , in modo che la regola WAF non valuti alcun valore nel campo.

Gli attributi seguenti possono essere aggiunti agli elenchi di esclusione in base al nome. I valori dei campi usati non vengono valutati rispetto alle regole WAF, ma i relativi nomi vengono valutati. Gli elenchi di esclusione rimuovono l'ispezione del valore del campo.

* Nome intestazione della richiesta
* Nome del cookie della richiesta
* Nome degli argomenti della stringa di query
* Nome argomenti post del corpo della richiesta

È possibile specificare un'esatta intestazione di richiesta, un corpo, un cookie o una corrispondenza dell'attributo stringa della query.  In alternativa, è possibile specificare facoltativamente corrispondenze parziali. Gli operatori seguenti sono i criteri di corrispondenza supportati:

- **Uguale a**: questo operatore viene usato per una corrispondenza esatta. Per selezionare, ad esempio, un'intestazione denominata **bearerToken**, usare l'operatore Equals con il selettore impostato come **bearerToken**.
- **Inizia con**: questo operatore corrisponde a tutti i campi che iniziano con il valore del selettore specificato.
- **Termina con**: questo operatore corrisponde a tutti i campi di richiesta che terminano con il valore del selettore specificato.
- **Contiene**: questo operatore corrisponde a tutti i campi di richiesta che contengono il valore del selettore specificato.
- **Uguale a any**: questo operatore corrisponde a tutti i campi della richiesta. * è il valore del selettore.

I nomi di intestazione e cookie non fanno distinzione tra maiuscole e minuscole.

È possibile applicare l'elenco di esclusioni a tutte le regole all'interno del set di regole gestite, alle regole per un gruppo di regole specifico o a una singola regola, come illustrato nell'esempio precedente. 

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato le impostazioni di WAF, vedere come visualizzare i log di WAF. Per altre informazioni, vedere [diagnostica della porta anteriore](../afds/waf-front-door-monitor.md).
