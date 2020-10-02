---
title: Indirizzi IP in Funzioni di Azure
description: Informazioni su come trovare gli indirizzi IP in ingresso e in uscita per le app per le funzioni e sugli elementi che ne causano la modifica.
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: 1d2cf34ee4712705eaa1c0da5ad63712f9e649fe
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91652466"
---
# <a name="ip-addresses-in-azure-functions"></a>Indirizzi IP in Funzioni di Azure

Questo articolo descrive gli argomenti seguenti correlati agli indirizzi IP delle app per le funzioni:

* Come trovare gli indirizzi IP attualmente in uso da parte di un'app per le funzioni.
* Quali elementi causano la modifica degli indirizzi IP di un'app per le funzioni.
* Come limitare gli indirizzi IP che possono accedere a un'app per le funzioni.
* Come ottenere indirizzi IP dedicati per un'app per le funzioni.

Gli indirizzi IP sono associati alle app per le funzioni, non alle singole funzioni. Le richieste HTTP in ingresso non possono usare l'indirizzo IP in ingresso per chiamare le singole funzioni: devono usare il nome di dominio predefinito (functionappname.azurewebsites.net) o un nome di dominio personalizzato.

## <a name="function-app-inbound-ip-address"></a>Indirizzo IP in ingresso delle app per le funzioni

Ogni app per le funzioni ha un singolo indirizzo IP in ingresso. Per trovare tale indirizzo IP:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare all'app per le funzioni.
3. Selezionare **Funzionalità della piattaforma**.
4. Selezionare **Proprietà**. L'indirizzo IP in ingresso verrà visualizzati in **Indirizzo IP virtuale**.

## <a name="function-app-outbound-ip-addresses"></a><a name="find-outbound-ip-addresses"></a>Indirizzi IP in uscita delle app per le funzioni

Ogni app per le funzioni ha un set di indirizzi IP in uscita disponibili. Qualsiasi connessione in uscita da una funzione, ad esempio un database back-end, usa uno degli indirizzi IP in uscita disponibili come indirizzo IP di origine. Non è possibile sapere in anticipo quale indirizzo IP verrà usato da una determinata connessione. Per questo motivo, il servizio back-end deve aprire il firewall a tutti gli indirizzi IP in uscita dell'app per le funzioni.

Per trovare gli indirizzi IP in uscita disponibili per un'app per le funzioni:

1. Accedere ad [Azure Resource Explorer](https://resources.azure.com).
2. Selezionare **sottoscrizioni > {sottoscrizione} > provider > Microsoft.Web > siti**.
3. Nel pannello JSON individuare il sito con una proprietà `id` che termina con il nome dell'app per le funzioni.
4. Controllare `outboundIpAddresses` e `possibleOutboundIpAddresses`. 

Il set `outboundIpAddresses` è attualmente disponibile per l'app per le funzioni. Il set `possibleOutboundIpAddresses` include gli indirizzi IP che saranno disponibili solo se l'app per le funzioni è [adattabile ad altri piani tariffari](#outbound-ip-address-changes).

Un modo alternativo per trovare gli indirizzi IP in uscita disponibili consiste nell'uso di [Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

> [!NOTE]
> Quando viene ridimensionata un'app per le funzioni in esecuzione nel [piano a consumo](functions-scale.md#consumption-plan) o nel [piano Premium](functions-scale.md#premium-plan) , è possibile assegnare un nuovo intervallo di indirizzi IP in uscita. Quando si esegue uno di questi piani, potrebbe essere necessario aggiungere l'intero data center a un elenco Consenti.

## <a name="data-center-outbound-ip-addresses"></a>Indirizzi IP in uscita del data center

Se è necessario aggiungere gli indirizzi IP in uscita usati dalle app per le funzioni a un elenco consentiti, un'altra opzione consiste nell'aggiungere il data center delle app per le funzioni (area di Azure) a un elenco consentiti. È possibile [scaricare un file JSON che elenca gli indirizzi IP per tutti i data center di Azure](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Trovare quindi l'elemento JSON applicabile per l'area in cui viene eseguita l'app per le funzioni.

Ad esempio, l'elemento JSON per l'Europa occidentale è simile al seguente:

```
{
  "name": "AzureCloud.westeurope",
  "id": "AzureCloud.westeurope",
  "properties": {
    "changeNumber": 9,
    "region": "westeurope",
    "platform": "Azure",
    "systemService": "",
    "addressPrefixes": [
      "13.69.0.0/17",
      "13.73.128.0/18",
      ... Some IP addresses not shown here
     "213.199.180.192/27",
     "213.199.183.0/24"
    ]
  }
}
```

 Per informazioni sull'aggiornamento di questo file e la modifica degli indirizzi IP, espandere la sezione **Dettagli** della [pagina dell'Area download](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

## <a name="inbound-ip-address-changes"></a><a name="inbound-ip-address-changes"></a>Modifiche degli indirizzi IP in ingresso

L'indirizzo IP in ingresso **potrebbe** cambiare in caso di:

- Eliminazione di un'app per le funzioni e successiva ricreazione in un gruppo di risorse diverso.
- Eliminazione dell'ultima app per le funzioni in una combinazione di gruppo di risorse e area e successiva ricreazione.
- Eliminare un'associazione TLS, ad esempio durante il [rinnovo del certificato](../app-service/configure-ssl-certificate.md#renew-certificate).

Quando l'app per le funzioni viene eseguita in un [piano a consumo](functions-scale.md#consumption-plan) o in un [piano Premium](functions-scale.md#premium-plan), l'indirizzo IP in ingresso potrebbe cambiare anche se non sono state eseguite azioni come quelle [elencate in precedenza](#inbound-ip-address-changes).

## <a name="outbound-ip-address-changes"></a>Modifiche degli indirizzi IP in uscita

Il set di indirizzi IP in uscita disponibili per un'app per le funzioni potrebbe cambiare in caso di:

* Esecuzione di un'azione che può modificare l'indirizzo IP in ingresso.
* Modifica del piano tariffario del servizio app. L'elenco di tutti i possibili indirizzi IP in uscita utilizzabili dall'app, per tutti i piani tariffari, è disponibile nella proprietà `possibleOutboundIPAddresses`. Vedere [Trovare gli indirizzi IP in uscita](#find-outbound-ip-addresses).

Quando l'app per le funzioni viene eseguita in un [piano a consumo](functions-scale.md#consumption-plan) o in un [piano Premium](functions-scale.md#premium-plan), l'indirizzo IP in uscita potrebbe cambiare anche se non sono state eseguite azioni come quelle [elencate in precedenza](#inbound-ip-address-changes).

Per forzare deliberatamente la modifica di un indirizzo IP in uscita:

1. Aumentare o ridurre il piano di servizio app tra i piani tariffari Standard e Premium v2.
2. Attendere 10 minuti.
3. Ridurre di nuovo il piano al livello iniziale.

## <a name="ip-address-restrictions"></a>Restrizioni degli indirizzi IP

È possibile configurare un elenco di indirizzi IP per cui si vuole concedere o negare l'accesso a un'app per le funzioni. Per altre informazioni, vedere [Restrizioni IP statico del Servizio app di Azure](../app-service/app-service-ip-restrictions.md).

## <a name="dedicated-ip-addresses"></a>Indirizzi IP dedicati

Se sono necessari indirizzi IP statici dedicati, è consigliabile [Ambienti del servizio app](../app-service/environment/intro.md) ([livello isolato](https://azure.microsoft.com/pricing/details/app-service/) dei piani di servizio app). Per altre informazioni, vedere [Indirizzi IP dell'ambiente del servizio app](../app-service/environment/network-info.md#ase-ip-addresses) e [Come controllare il traffico in ingresso a un ambiente del servizio app](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md).

Per scoprire se l'app per le funzioni viene eseguita in un ambiente del servizio app:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare all'app per le funzioni.
3. Selezionare la scheda **Panoramica**.
4. Il livello del piano di servizio app viene visualizzato in **Piano di servizio app/piano tariffario**. Il piano tariffario dell'ambiente del servizio app è **Isolato**.
 
In alternativa, è possibile usare [Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query sku --output tsv
```

Lo `sku` dell'ambiente del servizio app è `Isolated`.

## <a name="next-steps"></a>Passaggi successivi

Una causa comune della modifica degli indirizzi IP è il ridimensionamento delle app per le funzioni. [Altre informazioni sul ridimensionamento delle app per le funzioni](functions-scale.md).
