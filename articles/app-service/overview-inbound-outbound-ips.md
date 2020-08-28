---
title: Indirizzi IP in ingresso/in uscita
description: Informazioni sul modo in cui gli indirizzi IP in ingresso e in uscita vengono usati nel servizio app Azure, quando cambiano e come trovare gli indirizzi per l'app.
ms.topic: article
ms.date: 08/25/2020
ms.custom: seodec18
ms.openlocfilehash: 8fa9fec9219cfd85a8a0b25f50835425766d9043
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050693"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Indirizzi IP in ingresso e in uscita in Servizio app di Azure

[Servizio App di Azure](overview.md) è un servizio multi-tenant, ad eccezione di quanto riguarda gli [ambienti del servizio app](environment/intro.md). Le app che non sono incluse in un ambiente del servizio app (non incluse nel [livello Isolato](https://azure.microsoft.com/pricing/details/app-service/)) condividono l'infrastruttura di rete con altre app. Di conseguenza, gli indirizzi IP in ingresso e in uscita di un'app possono essere diversi e in determinate situazioni possono addirittura cambiare.

Gli [ambienti del servizio app](environment/intro.md) usano infrastrutture di rete dedicate e di conseguenza le app in esecuzione nell'ambiente del servizio app ottengono indirizzi IP statici dedicati per le connessioni sia in ingresso sia in uscita.

## <a name="how-ip-addresses-work-in-app-service"></a>Funzionamento degli indirizzi IP nel servizio app

Un'app del servizio app viene eseguita in un piano di servizio app e i piani di servizio app vengono distribuiti in una delle unità di distribuzione nell'infrastruttura di Azure, denominata internamente uno spazio Web. A ogni unità di distribuzione vengono assegnati fino a cinque indirizzi IP virtuali, che includono un indirizzo IP in ingresso pubblico e quattro indirizzi IP in uscita. Tutti i piani di servizio app nella stessa unità di distribuzione e le istanze dell'app in esecuzione in essi condividono lo stesso set di indirizzi IP virtuali. Per un ambiente del servizio app (un piano di servizio app in un [livello isolato](https://azure.microsoft.com/pricing/details/app-service/)), il piano di servizio app è l'unità di distribuzione stessa, quindi gli indirizzi IP virtuali sono dedicati come risultato.

Poiché non si è autorizzati a spostare un piano di servizio app tra unità di distribuzione, gli indirizzi IP virtuali assegnati all'app in genere rimangono invariati, ma vi sono eccezioni.

## <a name="when-inbound-ip-changes"></a>Casi in cui gli indirizzi IP in ingresso cambiano

Indipendentemente dal numero di istanze cui è applicata scalabilità orizzontale, ogni app ha un singolo indirizzo IP in ingresso. L'indirizzo IP in entrata può cambiare quando si esegue una delle azioni seguenti:

- Eliminare un'app e ricrearla in un gruppo di risorse diverso. l'unità di distribuzione può cambiare.
- Eliminare l'ultima app in una combinazione di gruppi di risorse _e_ aree e ricrearla (l'unità di distribuzione può cambiare).
- Eliminare un'associazione TLS/SSL basata su IP esistente, ad esempio durante il rinnovo del certificato. vedere [rinnovo del certificato](configure-ssl-certificate.md#renew-certificate).

## <a name="find-the-inbound-ip"></a>Trovare l'indirizzo IP in ingresso

È sufficiente eseguire il comando seguente in un terminale locale:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Ottenere un indirizzo IP in ingresso statico

A volte è necessario un indirizzo IP statico dedicato per l'app. Per ottenere un indirizzo IP in ingresso statico, è necessario [proteggere un dominio personalizzato](configure-ssl-bindings.md#secure-a-custom-domain). Se la funzionalità TLS non è effettivamente necessaria per proteggere l'app, è anche possibile caricare un certificato autofirmato per questa associazione. In un'associazione TLS basata su IP, il certificato è associato all'indirizzo IP stesso, quindi il servizio app effettua il provisioning di un indirizzo IP statico per fare in modo che si verifichi. 

## <a name="when-outbound-ips-change"></a>Casi in cui gli indirizzi IP in uscita cambiano

Indipendentemente dal numero di istanze cui è applicata scalabilità orizzontale, ogni app ha un numero impostato di indirizzi IP in uscita in qualsiasi momento. Qualsiasi connessione in uscita dall'app di Servizio app di Azure, ad esempio un database back-end, usa uno degli indirizzi IP in uscita come indirizzo IP di origine. L'indirizzo IP da usare è selezionato in modo casuale in fase di esecuzione, quindi il servizio back-end deve aprire il firewall a tutti gli indirizzi IP in uscita per l'app.

Il set di indirizzi IP in uscita per l'app viene modificato quando si esegue una delle azioni seguenti:

- Eliminare un'app e ricrearla in un gruppo di risorse diverso. l'unità di distribuzione può cambiare.
- Eliminare l'ultima app in una combinazione di gruppi di risorse _e_ aree e ricrearla (l'unità di distribuzione può cambiare).
- Ridimensionare l'app tra i livelli inferiori (**Basic**, **standard**e **Premium**) e il livello **Premium v2** (gli indirizzi IP possono essere aggiunti o sottratti dal set).

È possibile trovare il set di tutti i possibili indirizzi IP in uscita che possono essere usati dall'app, indipendentemente dai piani tariffari, cercando la `possibleOutboundIpAddresses` proprietà o nel campo **indirizzi IP in uscita aggiuntivi** nel pannello **proprietà** della portale di Azure. Vedere [Trovare gli indirizzi IP in uscita](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Trovare gli indirizzi IP in uscita

Per trovare gli indirizzi IP in uscita attualmente usati dall'app nel portale di Azure, fare clic su **Proprietà** nel riquadro di spostamento a sinistra dell'app. Sono elencate nel campo **indirizzi IP in uscita** .

È possibile trovare le stesse informazioni eseguendo il comando seguente in [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Per trovare _tutti i_ possibili indirizzi IP in uscita per l'app, indipendentemente dai piani tariffari, fare clic su **Proprietà** nel riquadro di spostamento a sinistra dell'app. Sono elencate nel campo **altri indirizzi IP in uscita** .

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
