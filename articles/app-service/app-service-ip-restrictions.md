---
title: Restrizioni di accesso al servizio app di AzureAzure App Service access restrictions
description: Informazioni su come proteggere l'app nel servizio app di Azure specificando le restrizioni di accesso.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 298555da2056bc4c16d4d7b16615604f9798b91b
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639278"
---
# <a name="azure-app-service-access-restrictions"></a>Restrizioni di accesso al servizio app di AzureAzure App Service access restrictions

Le restrizioni di accesso consentono di definire un elenco consentito/rifiuta con priorità che controlla l'accesso di rete all'app. L'elenco può includere indirizzi IP o subnet di rete virtuale di Azure.The list can include IP addresses or Azure Virtual Network subnets. Quando sono presenti una o più voci, è presente un implicito "nega tutto" che esiste alla fine dell'elenco.

La funzionalità di restrizioni di accesso funziona con tutti i carichi di lavoro ospitati del servizio app, inclusi; app Web, app API, app Linux, app contenitore Linux e Funzioni.

Quando viene effettuata una richiesta all'app, l'indirizzo FROM viene valutato in base alle regole dell'indirizzo IP nell'elenco delle restrizioni di accesso. Se l'indirizzo FROM si trova in una subnet configurata con gli endpoint del servizio in Microsoft.Web, la subnet di origine viene confrontata con le regole della rete virtuale nell'elenco delle restrizioni di accesso. Se all'indirizzo non viene consentito l'accesso in base alle regole nell'elenco, il servizio replica con un codice di stato [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403).

La funzionalità di restrizioni di accesso viene implementata nei ruoli front-end del servizio app, che sono a monte degli host di lavoro in cui viene eseguito il codice. Di conseguenza, le restrizioni di accesso sono in modo efficace ACL di rete.

La possibilità di limitare l'accesso all'app Web da una rete virtuale di Azure è denominata endpoint del [servizio.][serviceendpoints] Gli endpoint di servizio consentono di limitare l'accesso a un servizio multi-tenant da subnet selezionate. Deve essere abilitato sia sul lato rete che sul servizio con cui viene abilitato. Non funziona per limitare il traffico alle app ospitate in un ambiente del servizio app. Se ti trovi in un ambiente del servizio app, puoi controllare l'accesso alla tua app con le regole dell'indirizzo IP.

![flusso di restrizioni di accesso](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Aggiunta e modifica di regole di restrizione dell'accesso nel portale ##

Per aggiungere una regola di restrizione di accesso all'app, usare il menu per aprire**Restrizioni** di accesso alla **rete**>e fare clic su **Configura restrizioni** di accesso

![Opzioni di connettività di rete del servizio app](media/app-service-ip-restrictions/access-restrictions.png)  

Dall'interfaccia utente Restrizioni di accesso, è possibile esaminare l'elenco delle regole di restrizione di accesso definite per l'app.

![elencare le restrizioni di accesso](media/app-service-ip-restrictions/access-restrictions-browse.png)

L'elenco mostrerà tutte le restrizioni correnti presenti nell'app. If you have a VNet restriction on your app, the table will show if service endpoints are enabled for Microsoft.Web. Quando non esistono restrizioni definite per l'app, l'app sarà accessibile da qualsiasi luogo.  

## <a name="adding-ip-address-rules"></a>Aggiunta di regole di indirizzi IP

È possibile fare clic su **Aggiungi regola** per aggiungere una nuova regola di restrizione di accesso. Dopo l'aggiunta una regola diventa effettiva immediatamente. Le regole vengono applicate in base alle priorità dal numero più basso al più alto. È presente un nega tutto implicito anche dopo l'aggiunta di una singola regola.

Quando si crea una regola, è necessario selezionare consenti/Nega e anche il tipo di regola. È inoltre necessario fornire il valore di priorità e ciò a cui si sta limitando l'accesso.  Facoltativamente, è possibile aggiungere un nome e una descrizione alla regola.  

![aggiungere una regola di restrizione dell'accesso IP](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

Per impostare una regola basata su indirizzi IP, selezionare un tipo di IPv4 o IPv6. La notazione Indirizzo IP deve essere specificata nella notazione CIDR per gli indirizzi IPv4 e IPv6. Per specificare un indirizzo esatto, è possibile usare un indirizzo simile a 1.2.3.4/32, dove i primi quattro otteti rappresentano l'indirizzo IP e /32 è la maschera. La notazione CIDR IPv4 per tutti gli indirizzi è 0.0.0.0/0. Per ulteriori informazioni sulla notazione CIDR, è possibile leggere [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

## <a name="service-endpoints"></a>Endpoint di servizio

Gli endpoint di servizio consentono di limitare l'accesso alle subnet di rete virtuale di Azure selezionate. Per limitare l'accesso a una subnet specifica, creare una regola di restrizione con un tipo di rete virtuale. È possibile selezionare la sottoscrizione, la rete virtuale e la subnet con cui si desidera consentire o negare l'accesso. Se gli endpoint del servizio non sono già abilitati con Microsoft.Web per la subnet selezionata, verranno abilitati automaticamente a meno che non si eschi la casella che richiede di non eseguire questa operazione. La situazione in cui si desidera abilitarla nell'app, ma non la subnet, è in gran parte correlata se si dispone delle autorizzazioni per abilitare o meno gli endpoint del servizio nella subnet. Se è necessario chiedere a un altro utente di abilitare gli endpoint del servizio nella subnet, è possibile selezionare la casella e configurare l'app per gli endpoint del servizio in previsione dell'abilitazione in un secondo momento nella subnet. 

![aggiungere una regola di restrizione dell'accesso della rete virtualeAdd a VNet access restriction rule](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Gli endpoint di servizio non possono essere usati per limitare l'accesso alle app eseguite in un ambiente del servizio app. Quando l'app si trova in un ambiente del servizio app, puoi controllare l'accesso all'app con le regole di accesso IP. 

Con gli endpoint del servizio, è possibile configurare l'app con gateway applicazione o altri dispositivi WAF. È inoltre possibile configurare applicazioni multilivello con back-end protetti. Per ulteriori informazioni su alcune delle possibilità, vedere Funzionalità di rete e [integrazione](networking/app-gateway-with-service-endpoints.md)del servizio [app](networking-features.md) e del gateway applicazione con gli endpoint del servizio .

## <a name="managing-access-restriction-rules"></a>Gestione delle regole di restrizione di accesso

È possibile fare clic su qualsiasi riga per modificare una regola di restrizione di accesso esistente. Le modifiche diventano effettive immediatamente, incluse le modifiche nell'ordine di priorità.

![modificare una regola di restrizione di accesso](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

Quando si modifica una regola, non è possibile modificare il tipo tra una regola di indirizzo IP e una regola di rete virtuale. 

![modificare una regola di restrizione di accesso](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

Per eliminare una regola, fare clic sul **pulsante ...** nella regola e quindi fare clic su **Rimuovi**.

![eliminare la regola di restrizione di accesso](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="blocking-a-single-ip-address"></a>Blocco di un singolo indirizzo IP ##

Quando si aggiunge la prima regola di restrizione IP, il servizio aggiungerà una regola **Nega tutto** esplicita con priorità 2147483647. In pratica, la regola **esplicita Nega tutto** verrà eseguita per l'ultima regola e bloccherà l'accesso a qualsiasi indirizzo IP non esplicitamente consentito tramite una regola **Allow.**

Per lo scenario in cui gli utenti desiderano bloccare in modo esplicito un singolo indirizzo IP o blocco di indirizzi IP, ma consentire l'accesso a tutto il resto, è necessario aggiungere una regola **Consenti tutto** esplicita.

![bloccare singolo indirizzo IP](media/app-service-ip-restrictions/block-single-address.png)

## <a name="scm-site"></a>Sito SCM 

Oltre a poter controllare l'accesso alla tua app, puoi anche limitare l'accesso al sito scm usato dalla tua app. Il sito scm è l'endpoint di distribuzione web e anche la console Kudu. È possibile assegnare separatamente restrizioni di accesso al sito scm dall'app o utilizzare lo stesso set sia per l'app che per il sito scm. Quando si seleziona la casella per avere le stesse restrizioni dell'app, tutto è vuoto. Se si deseleziona la casella, vengono applicate le impostazioni precedenti sul sito scm. 

![elencare le restrizioni di accesso](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>Manipolazione a livello di codice delle regole di restrizione dell'accessoProgrammatic manipulation of access restriction rules ##

[L'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/webapp/config/access-restriction?view=azure-cli-latest) e Azure [PowerShell](https://docs.microsoft.com/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-3.1.0) include il supporto per la modifica delle restrizioni di accesso. Esempio di aggiunta di una restrizione di accesso tramite l'interfaccia della riga di comando di Azure:Example of adding an access restriction using Azure CLI:

```azurecli-interactive
az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
```
Esempio di aggiunta di una restrizione di accesso tramite Azure PowerShell:Example of adding an access restriction using Azure PowerShell:

```azurepowershell-interactive
Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
    -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
```

I valori possono anche essere impostati manualmente con un'operazione PUT [dell'API REST](https://docs.microsoft.com/rest/api/azure/) di Azure nella configurazione dell'app in Resource Manager o usando un modello di Azure Resource Manager.Values can also be set manually with an Azure REST API PUT operation on the app configuration in Resource Manager or using an Azure Resource Manager template. Come esempio, è possibile usare resources.azure.com e modificare il blocco ipSecurityRestrictions per aggiungere il JSON necessario.

Il percorso per questa informazione in Gestione risorse è:

management.azure.com/subscriptions/**subscription ID**/resourceGroups/**resource groups**/providers/Microsoft.Web/sites/**web app name**/config/web?api-version=2018-02-01

La sintassi JSON per l'esempio precedente è:
```json
{
  "properties": {
    "ipSecurityRestrictions": [
      {
        "ipAddress": "122.133.144.0/24",
        "action": "Allow",
        "priority": 100,
        "name": "IP example rule"
      }
    ]
  }
}
```

## <a name="azure-functions-access-restrictions"></a>Restrizioni di accesso di Funzioni di AzureAzure Functions access restrictions

Le restrizioni di accesso sono disponibili anche per le app per le funzioni con le stesse funzionalità dei piani di servizio app. L'abilitazione delle restrizioni di accesso disabiliterà l'editor del codice del portale per tutti gli indirizzi IP non consentiti.

## <a name="next-steps"></a>Passaggi successivi
[Restrizioni di accesso per Funzioni di AzureAccess restrictions for Azure Functions](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)

[Integrazione del gateway applicazione con gli endpoint del servizioApplication Gateway integration with service endpoints](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
