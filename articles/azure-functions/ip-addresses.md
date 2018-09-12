---
title: Indirizzi IP in Funzioni di Azure
description: Informazioni su come trovare gli indirizzi IP in ingresso e in uscita per le app per le funzioni e sugli elementi che ne causano la modifica.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: glenga
ms.openlocfilehash: 0fcda59add346d60f37273625fbbcf41faab0e15
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44091204"
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

## <a name="find-outbound-ip-addresses"></a>Indirizzi IP in uscita delle app per le funzioni

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

## <a name="data-center-outbound-ip-addresses"></a>Indirizzi IP in uscita del data center

Se è necessario aggiungere all'elenco degli elementi consentiti gli indirizzi IP in uscita usati dall'app per le funzioni, un'altra opzione è aggiungere all'elenco degli elementi consentiti il data center delle app per le funzioni (area di Azure). È possibile [scaricare un file XML che elenca gli indirizzi IP per tutti i data center di Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653). Trovare quindi l'elemento XML applicabile per l'area in cui viene eseguita l'app per le funzioni.

Ad esempio, l'elemento XML per l'Europa occidentale è simile al seguente:

```
  <Region Name="europewest">
    <IpRange Subnet="13.69.0.0/17" />
    <IpRange Subnet="13.73.128.0/18" />
    <!-- Some IP addresses not shown here -->
    <IpRange Subnet="213.199.180.192/27" />
    <IpRange Subnet="213.199.183.0/24" />
  </Region>
```

 Per informazioni sull'aggiornamento di questo file e la modifica degli indirizzi IP, espandere la sezione **Dettagli** della [pagina dell'Area download](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

## <a name="inbound-ip-address-changes"></a>Modifiche degli indirizzi IP in ingresso

 L'indirizzo IP in ingresso **potrebbe** cambiare in caso di:

- Eliminazione di un'app per le funzioni e successiva ricreazione in un gruppo di risorse diverso.
- Eliminazione dell'ultima app per le funzioni in una combinazione di gruppo di risorse e area e successiva ricreazione.
- Eliminazione di un binding SSL, ad esempio durante il [rinnovo del certificato](../app-service/app-service-web-tutorial-custom-ssl.md#renew-certificates).

L'indirizzo IP in ingresso potrebbe cambiare anche quando non sono state eseguite azioni come quelle elencate.

## <a name="outbound-ip-address-changes"></a>Modifiche degli indirizzi IP in uscita

Il set di indirizzi IP in uscita disponibili per un'app per le funzioni potrebbe cambiare in caso di:

* Esecuzione di un'azione che può modificare l'indirizzo IP in ingresso.
* Modifica del piano tariffario del servizio app. L'elenco di tutti i possibili indirizzi IP in uscita utilizzabili dall'app, per tutti i piani tariffari, è disponibile nella proprietà `possibleOutboundIPAddresses`. Vedere [Trovare gli indirizzi IP in uscita](#find-outbound-ip-addresses).

L'indirizzo IP in ingresso potrebbe cambiare anche quando non sono state eseguite azioni come quelle elencate.

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
