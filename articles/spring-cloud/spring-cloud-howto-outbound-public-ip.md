---
title: Come identificare gli indirizzi IP pubblici in uscita nel cloud Spring di Azure
description: Come visualizzare gli indirizzi IP pubblici in uscita statici per comunicare con le risorse esterne, ad esempio database, archiviazione, Key Vault e così via.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: 04174b9cffb7e853dee235a4141ccda74a7847c6
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663778"
---
# <a name="how-to-identify-outbound-public-ip-addresses-in-azure-spring-cloud"></a>Come identificare gli indirizzi IP pubblici in uscita nel cloud Spring di Azure

Questa pagina illustra come visualizzare gli indirizzi IP pubblici in uscita statici delle applicazioni cloud Spring di Azure.  Gli indirizzi IP pubblici vengono usati per comunicare con le risorse esterne, ad esempio database, archiviazione e insiemi di credenziali delle chiavi.

## <a name="how-ip-addresses-work-in-azure-spring-cloud"></a>Funzionamento degli indirizzi IP nel cloud Spring di Azure

Un servizio cloud Spring di Azure ha uno o più indirizzi IP pubblici in uscita. Il numero di indirizzi IP pubblici in uscita può variare in base ai livelli e ad altri fattori. 

Gli indirizzi IP pubblici in uscita sono in genere costanti e rimangono invariati, ma vi sono eccezioni.

## <a name="when-outbound-ips-change"></a>Casi in cui gli indirizzi IP in uscita cambiano

Ogni istanza di Azure Spring cloud ha un numero impostato di indirizzi IP pubblici in uscita in un determinato momento. Qualsiasi connessione in uscita dalle applicazioni, ad esempio a un database back-end, usa uno degli indirizzi IP pubblici in uscita come indirizzo IP di origine. L'indirizzo IP viene selezionato in modo casuale in fase di esecuzione, quindi il servizio back-end deve aprire il firewall a tutti gli indirizzi IP in uscita.

Il numero di indirizzi IP pubblici in uscita viene modificato quando si esegue una delle azioni seguenti:

- Aggiornare l'istanza del cloud Spring di Azure tra i livelli.
- Genera un ticket di supporto per più indirizzi IP pubblici in uscita per le esigenze aziendali.

## <a name="find-outbound-ips"></a>Trovare gli indirizzi IP in uscita

Per trovare gli indirizzi IP pubblici in uscita attualmente usati dall'istanza del servizio nell'portale di Azure, fare clic su **rete** nel riquadro di spostamento a sinistra dell'istanza. Sono elencate nel campo **indirizzi IP in uscita** .

È possibile trovare le stesse informazioni eseguendo il comando seguente nella Cloud Shell

```Azure CLI
az spring-cloud show --resource-group <group_name> --name <service_name> --query properties.networkProfile.outboundIPs.publicIPs --output tsv
```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
* [Altre informazioni sulle identità gestite per le risorse di Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Scopri di più su Key Vault nel cloud Spring di Azure](spring-cloud-tutorial-managed-identities-key-vault.md)
