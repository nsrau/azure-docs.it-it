---
title: 'Avvio rapido: Creare e gestire i token di accesso'
titleSuffix: An Azure Communication Services quickstart
description: Informazioni su come gestire le identità e i token di accesso usando la libreria client di amministrazione di Servizi di comunicazione di Azure.
author: matthewrobertson
manager: jken
services: azure-communication-services
ms.author: marobert
ms.date: 08/20/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: a76000ecacdf78196ec1b80a60940484f6421641
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944576"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>Avvio rapido: Creare e gestire i token di accesso

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Iniziare a usare Servizi di comunicazione di Azure tramite la libreria client di amministrazione di Servizi di comunicazione per effettuare il provisioning e gestire i token di accesso. I token di accesso consentono alle librerie client per le chat e le chiamate di eseguire l'autenticazione direttamente in Servizi di comunicazione di Azure. Questi token vengono generati in un servizio di provisioning di token sul lato server implementato dall'utente. Vengono quindi usati per inizializzare le librerie client di Servizi di comunicazione nei dispositivi client.

Si noti che tutti i prezzi visualizzati nelle immagini in questa esercitazione sono solo a scopo esemplificativo.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/user-access-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/user-access-token-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/user-access-token-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/user-access-token-java.md)]
::: zone-end

L'output dell'app descrive ogni azione completata:
<!---cSpell:disable --->
```console
Azure Communication Services - Access Tokens Quickstart

Issued a access token with 'voip' scope for identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050:
<token signature here>

Issued a access token with 'chat' scope for identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050:
<token signature here>

Successfully deleted the identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050

Deleted the identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050
```
<!---cSpell:enable --->

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire e rimuovere una sottoscrizione di Servizi di comunicazione, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate. Altre informazioni sulla [pulizia delle risorse](./create-communication-resource.md#clean-up-resources).


## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come:

> [!div class="checklist"]
> * Gestire le identità
> * Risolvere i problemi dei token di accesso
> * Usare la libreria client di amministrazione di Servizi di comunicazione


> [!div class="nextstepaction"]
> [Aggiungere chiamate vocali all'app](./voice-video-calling/getting-started-with-calling.md)

Può essere utile vedere anche gli articoli seguenti:

 - [Informazioni sull'autenticazione](../concepts/authentication.md)
 - [Aggiungere una chat all'app](./chat/get-started.md)
 - [Informazioni sull'architettura client e server](../concepts/client-and-server-architecture.md)
