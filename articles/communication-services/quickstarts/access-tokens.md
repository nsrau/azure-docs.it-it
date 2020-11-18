---
title: 'Avvio rapido: Creare e gestire i token di accesso'
titleSuffix: An Azure Communication Services quickstart
description: Informazioni su come gestire le identità e i token di accesso usando la libreria client di amministrazione di Servizi di comunicazione di Azure.
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 08/20/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: b67d0808643797d88628b626403c1b9d97cf1cad
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506251"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>Avvio rapido: Creare e gestire i token di accesso

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Introduzione a Servizi di comunicazione di Azure con l'uso della libreria client Amministrazione di Servizi di comunicazione. Consente di creare identità e di gestire i token di accesso. L'identità rappresenta l'entità dell'applicazione nel servizio di comunicazione di Azure, ad esempio utente o dispositivo. I token di accesso consentono alle librerie client per le chat e le chiamate di eseguire l'autenticazione direttamente in Servizi di comunicazione di Azure. È consigliabile generare token di accesso in un servizio lato server. I token di accesso vengono quindi usati per inizializzare le librerie client di Servizi di comunicazione nei dispositivi client.

Tutti i prezzi visualizzati nelle immagini di questa esercitazione sono riportati unicamente a scopo dimostrativo.

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

Created an identity: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Issued a access token with 'voip' scope for identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502:
<token signature here>

Successfully deleted the identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Deleted the identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502
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
 