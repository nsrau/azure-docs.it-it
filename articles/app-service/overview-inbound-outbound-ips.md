---
title: Indirizzi IP in ingresso/in uscita - Servizio app di Azure | Microsoft Docs
description: Descrive come vengono usati gli indirizzi IP in ingresso e in uscita in Servizio app di Azure e come trovare informazioni relative a questi indirizzi per l'app.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: de9ae8e5c0cbf0997811db9624f6c6b92e03a5df
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66742947"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Indirizzi IP in ingresso e in uscita in Servizio app di Azure

[Servizio App di Azure](overview.md) è un servizio multi-tenant, ad eccezione di quanto riguarda gli [ambienti del servizio app](environment/intro.md). Le app che non sono incluse in un ambiente del servizio app (non incluse nel [livello Isolato](https://azure.microsoft.com/pricing/details/app-service/)) condividono l'infrastruttura di rete con altre app. Di conseguenza, gli indirizzi IP in ingresso e in uscita di un'app possono essere diversi e in determinate situazioni possono addirittura cambiare. 

Gli [ambienti del servizio app](environment/intro.md) usano infrastrutture di rete dedicate e di conseguenza le app in esecuzione nell'ambiente del servizio app ottengono indirizzi IP statici dedicati per le connessioni sia in ingresso sia in uscita.

## <a name="when-inbound-ip-changes"></a>Casi in cui gli indirizzi IP in ingresso cambiano

Indipendentemente dal numero di istanze cui è applicata scalabilità orizzontale, ogni app ha un singolo indirizzo IP in ingresso. L'indirizzo IP in entrata può cambiare quando si esegue una delle azioni seguenti:

- Eliminazione di un'app e sua successiva ricreazione in un gruppo di risorse diverso.
- Eliminazione dell'ultima app in un gruppo di risorse _e_ combinazione di aree e sua ricreazione.
- Eliminazione di un'associazione SSL esistente, ad esempio durante il rinnovo del certificato. Vedere [Rinnovare i certificati](app-service-web-tutorial-custom-ssl.md#renew-certificates).

## <a name="find-the-inbound-ip"></a>Trovare l'indirizzo IP in ingresso

Eseguire semplicemente il comando seguente in un terminale locale:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Ottenere un indirizzo IP in ingresso statico

A volte è necessario un indirizzo IP statico dedicato per l'app. Per ottenere un indirizzo IP in ingresso statico, è necessario configurare un'[associazione SSL basata su IP](app-service-web-tutorial-custom-ssl.md#secure-a-custom-domain). Se la funzionalità SSL non è effettivamente necessaria per proteggere l'app, è anche possibile caricare un certificato autofirmato per questa associazione. In un'associazione SSL basata su IP il certificato è associato all'indirizzo IP stesso e di conseguenza Servizio app di Azure effettua il provisioning di un indirizzo IP statico a questo scopo. 

## <a name="when-outbound-ips-change"></a>Casi in cui gli indirizzi IP in uscita cambiano

Indipendentemente dal numero di istanze cui è applicata scalabilità orizzontale, ogni app ha un numero impostato di indirizzi IP in uscita in qualsiasi momento. Qualsiasi connessione in uscita dall'app di Servizio app di Azure, ad esempio un database back-end, usa uno degli indirizzi IP in uscita come indirizzo IP di origine. Poiché non è possibile identificare in anticipo quale indirizzo IP verrà usato da un'istanza dell'app specifica per stabilire la connessione in uscita, il servizio back-end deve aprire il proprio firewall a tutti gli indirizzi IP in uscita dell'app.

Il set di indirizzi IP in uscita per l'app cambia quando si ridimensiona l'app tra i livelli inferiori (**Basic**, **Standard** e **Premium**) e il livello  **Premium V2**.

È possibile trovare il set di tutti i possibili in uscita indirizzi IP in cui è possibile usare l'app, indipendentemente dal fatto di piani tariffari, cercando il `possibleOutboundIPAddresses` proprietà o nella **altri indirizzi IP in uscita** campo il **proprietà**  pannello nel portale di Azure. Vedere [Trovare gli indirizzi IP in uscita](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Trovare gli indirizzi IP in uscita

Per trovare gli indirizzi IP in uscita attualmente usati dall'app nel portale di Azure, fare clic su **Proprietà** nel riquadro di spostamento a sinistra dell'app. Cui sono elencati i **indirizzi IP in uscita** campo.

È possibile trovare le stesse informazioni eseguendo il comando seguente in [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Per trovare _tutte_ possibili indirizzi IP in uscita per l'app, indipendentemente dal fatto di piani tariffari, fare clic su **proprietà** nel riquadro di spostamento a sinistra dell'app. Cui sono elencati i **altri indirizzi IP in uscita** campo.

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
