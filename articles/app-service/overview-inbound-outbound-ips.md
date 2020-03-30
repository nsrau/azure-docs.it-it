---
title: Indirizzi IP in ingresso/in uscita
description: Informazioni sull'uso degli indirizzi IP in ingresso e in uscita nel servizio app di Azure, quando cambiano e come trovare gli indirizzi per l'app.
ms.topic: article
ms.date: 06/06/2019
ms.custom: seodec18
ms.openlocfilehash: aebce04fe2f1b055a4d498021dcd25144cd122a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279208"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Indirizzi IP in ingresso e in uscita in Servizio app di Azure

[Servizio App di Azure](overview.md) è un servizio multi-tenant, ad eccezione di quanto riguarda gli [ambienti del servizio app](environment/intro.md). Le app che non sono incluse in un ambiente del servizio app (non incluse nel [livello Isolato](https://azure.microsoft.com/pricing/details/app-service/)) condividono l'infrastruttura di rete con altre app. Di conseguenza, gli indirizzi IP in ingresso e in uscita di un'app possono essere diversi e in determinate situazioni possono addirittura cambiare. 

Gli [ambienti del servizio app](environment/intro.md) usano infrastrutture di rete dedicate e di conseguenza le app in esecuzione nell'ambiente del servizio app ottengono indirizzi IP statici dedicati per le connessioni sia in ingresso sia in uscita.

## <a name="when-inbound-ip-changes"></a>Casi in cui gli indirizzi IP in ingresso cambiano

Indipendentemente dal numero di istanze cui è applicata scalabilità orizzontale, ogni app ha un singolo indirizzo IP in ingresso. L'indirizzo IP in entrata può cambiare quando si esegue una delle azioni seguenti:

- Eliminazione di un'app e sua successiva ricreazione in un gruppo di risorse diverso.
- Eliminazione dell'ultima app in un gruppo di risorse _e_ combinazione di aree e sua ricreazione.
- Eliminare un binding SSL esistente, ad esempio durante il rinnovo del certificato (vedere [Rinnovare](configure-ssl-certificate.md#renew-certificate)il certificato ).

## <a name="find-the-inbound-ip"></a>Trovare l'IP in ingresso

Basta eseguire il seguente comando in un terminale locale:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Ottenere un IP statico in ingressoGet a static inbound IP

A volte è necessario un indirizzo IP statico dedicato per l'app. Per ottenere un indirizzo IP in ingresso statico, è necessario configurare un'[associazione SSL basata su IP](configure-ssl-bindings.md#secure-a-custom-domain). Se la funzionalità SSL non è effettivamente necessaria per proteggere l'app, è anche possibile caricare un certificato autofirmato per questa associazione. In un'associazione SSL basata su IP il certificato è associato all'indirizzo IP stesso e di conseguenza Servizio app di Azure effettua il provisioning di un indirizzo IP statico a questo scopo. 

## <a name="when-outbound-ips-change"></a>Casi in cui gli indirizzi IP in uscita cambiano

Indipendentemente dal numero di istanze cui è applicata scalabilità orizzontale, ogni app ha un numero impostato di indirizzi IP in uscita in qualsiasi momento. Qualsiasi connessione in uscita dall'app di Servizio app di Azure, ad esempio un database back-end, usa uno degli indirizzi IP in uscita come indirizzo IP di origine. Poiché non è possibile identificare in anticipo quale indirizzo IP verrà usato da un'istanza dell'app specifica per stabilire la connessione in uscita, il servizio back-end deve aprire il proprio firewall a tutti gli indirizzi IP in uscita dell'app.

Il set di indirizzi IP in uscita per l'app cambia quando si ridimensiona l'app tra i livelli inferiori (**Basic**, **Standard** e **Premium**) e il livello ** Premium V2**.

È possibile trovare il set di tutti i possibili indirizzi IP in uscita che `possibleOutboundIpAddresses` l'app può usare, indipendentemente dai piani tariffari, cercando la proprietà o nel campo **Indirizzi IP in uscita aggiuntivi** nel pannello Proprietà nel portale di Azure.You can find the set of all possible outbound IP addresses your app can use, regardless of pricing tiers, looking for the property or in the Additional Outbound IP Addresses field in the **Properties** blade in the Azure portal. Vedere [Trovare gli indirizzi IP in uscita](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Trovare gli indirizzi IP in uscita

Per trovare gli indirizzi IP in uscita attualmente usati dall'app nel portale di Azure, fare clic su **Proprietà** nel riquadro di spostamento a sinistra dell'app. Sono elencati nel campo **Indirizzi IP in uscita.**

È possibile trovare le stesse informazioni eseguendo il comando seguente in [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Per trovare _tutti i_ possibili indirizzi IP in uscita per l'app, indipendentemente dai piani tariffari, fai clic su **Proprietà** nel riquadro di spostamento sinistro dell'app. Sono elencati nel campo **Indirizzi IP in uscita aggiuntivi.**

È possibile trovare le stesse informazioni eseguendo il comando seguente in [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>Passaggi successivi

Apprendere come limitare il traffico in ingresso tramite indirizzi IP di origine.

> [!div class="nextstepaction"]
> [Limitazioni relative agli indirizzi IP statici](app-service-ip-restrictions.md)
