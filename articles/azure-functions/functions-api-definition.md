---
title: Come usare i metadati OpenAPI in Funzioni di Azure | Documentazione Microsoft
description: Panoramica del supporto OpenAPI in Funzioni di Azure
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: erikre
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/23/2017
ms.author: alkarche
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: aac362f5123038cc39e0d2b32c10f7747a702cfe
ms.contentlocale: it-it
ms.lasthandoff: 05/16/2017


---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Supporto per metadati OpenAPI 2.0 in Funzioni di Azure (anteprima)
Questa funzionalità di anteprima consente di scrivere una definizione OpenAPI 2.0 (in precedenza Swagger) all'interno di un'app per le funzioni e di ospitare il file che usa tale app.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

### <a name="what-is-openapi-metadata"></a>Definizione di metadati OpenAPI
I [metadati OpenAPI](http://swagger.io/) consentono a una funzione che ospita un'API REST di essere usata da un'ampia gamma di altri prodotti software, ad esempio PowerApps e [App per le API](https://docs.microsoft.com/azure/app-service-api/app-service-api-dotnet-get-started#a-idcodegena-generate-client-code-for-the-data-tier), strumenti di terze parti per sviluppatori come [Postman](https://www.getpostman.com/docs/importing_swagger), e [numerosi altri pacchetti](http://swagger.io/tools/).

>[!TIP]
>È consigliabile iniziare con l'[esercitazione introduttiva](./functions-api-definition-getting-started.md) e tornare successivamente a questo documento per approfondire le funzionalità specifiche.

## <a name="enable"></a>Abilitazione del supporto per la definizione OpenAPI
* È possibile configurare tutte le impostazioni di OpenAPI nella pagina `API Definition (preview)` nelle impostazioni dell'app per le funzioni.
* Impostare `API definition source` su `Function` per attivare una definizione di OpenAPI ospitata e la generazione di una definizione di avvio rapido.
  * `External URL` consente alla funzione di usare una definizione OpenAPI ospitata altrove.

## <a name="generate-definition"></a>Generare una struttura Swagger dai metadati della funzione
L'impiego di modello semplifica la scrittura della prima definizione OpenAPI. La funzionalità di modello della definizione crea una definizione di OpenAPI di tipo sparse usando tutti i metadati contenuti in function.json per ognuna delle funzioni trigger HTTP. **È necessario inserire le informazioni relative all'API in base alla [specifica OpenAPI](http://swagger.io/specification/), ad esempio i modelli di richiesta e risposta.**

[Per istruzioni dettagliate, consultare questa esercitazione introduttiva ](./functions-api-definition-getting-started.md)

### <a name="templates"></a>Modelli disponibili

|Nome| Descrizione |
|:-----|:-----|
|Definizione generata|Una definizione OpenAPI con la quantità massima di informazioni che è possibile dedurre dai metadati esistenti della funzione|

### <a name="quickstart-details"></a>Metadati inclusi nella definizione generata

La tabella seguente include le impostazioni del portale e i dati corrispondenti in function.json, così come vengono abbinati alla struttura Swagger generata.

|Swagger.json|Interfaccia utente del portale|Function.json|
|:----|:-----|:-----|
|[Host](http://swagger.io/specification/#fixed-fields-15)|`Function app settings` > `Go to App Service Settings` > `Overview` > `URL`|*non presente*
|[Percorsi](http://swagger.io/specification/#paths-object-29)|`Integrate` > `Selected HTTP methods`|Associazioni: Route
|[Elemento del percorso](http://swagger.io/specification/#path-item-object-32)|`Integrate` > `Route template`|Associazioni: Metodi
|[Sicurezza](http://swagger.io/specification/#security-scheme-object-112)|Chiavi|*non presente*|
|operationID*|Route + verbi consentiti|Route + verbi consentiti|

\*L'ID dell'operazione è necessario soltanto per l'integrazione di PowerApps e Flow
> [!NOTE]
>  x-ms-summary indica il nome visualizzato in App per la logica, Flow e PowerApps.
>
> Per altre informazioni, leggere [Personalizzare la definizione Swagger per PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/).

## <a name="CICD"></a>Uso di CI/CD per impostare una definizione dell'API

 È necessario abilitare l'hosting della definizione API nel portale prima di attivare il controllo del codice sorgente per modificare la definizione dal codice sorgente. Seguire le istruzioni riportate di seguito.

1. Passare a `API Definition (preview)` nelle impostazioni dell'app per le funzioni.
  1. Impostare `API definition source` su `Function`
  1. Fare clic su `Generate API definition template` e quindi su `Save` per creare una definizione di modello dal modificare successivamente.
  1. Annotare i valori di `API definition URL` e `key`
1. [Configurare CI/CD](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#continuous-deployment-requirements)
2. Modificare `swagger.json` nel controllo del codice sorgente in `\site\wwwroot\.azurefunctions\swagger\swagger.json`

A questo punto le modifiche apportate a `swagger.json` nel repository sono ospitate dall'app per le funzioni nell'`API definition URL` e nella `key` annotate al passaggio 1.3

## <a name="next-steps"></a>Passaggi successivi
* [Esercitazione introduttiva](functions-api-definition-getting-started.md)
  * Provare la procedura dettagliata per comprendere il funzionamento di una definizione OpenAPI.
* [Repository GitHub di Funzioni di Azure](https://github.com/Azure/Azure-Functions/)
  * In Github di Funzioni di Azure è possibile inviare commenti e suggerimenti relativi all'anteprima del supporto della definizione API. La pagina consente inoltre di inviare suggerimenti su eventuali elementi che si desidera vedere aggiornati.
* [Guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md)  
  * Informazioni di riferimento per programmatori in merito alla codifica delle funzioni e alla definizione di trigger e associazioni.

