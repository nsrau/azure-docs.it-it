---
title: 'Avvio rapido: Libreria client di gestione di Azure per Node.js'
description: Questo argomento di avvio rapido contiene un'introduzione alla libreria client di gestione di Azure per Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: fcfefb2b6cd5c23e9b77d7d27413d1ff6d775bfe
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89321620"
---
[Documentazione di riferimento](https://docs.microsoft.com/javascript/api/@azure/arm-cognitiveservices/?view=azure-node-latest) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices) | [Pacchetto (NPM)](https://www.npmjs.com/package/@azure/arm-cognitiveservices) | [Esempi](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices#sample-code)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure valida: [crearne una gratuitamente](https://azure.microsoft.com/free/).
* Versione corrente di [Node.js](https://nodejs.org/)

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-nodejs-application"></a>Creare una nuova applicazione Node.js

In una finestra della console, ad esempio cmd, PowerShell o Bash, creare e passare a una nuova directory per l'app. 

```console
mkdir myapp && cd myapp
```

Eseguire il comando `npm init` per creare un'applicazione Node con un file `package.json`. 

```console
npm init
```

Prima di procedere, creare un file denominato _index.js_.

### <a name="install-the-client-library"></a>Installare la libreria client

Installare i pacchetti NPM seguenti:

```console
npm install @azure/arm-cognitiveservices
npm install @azure/ms-rest-js
npm install @azure/ms-rest-nodeauth
```

Il file `package.json` dell'app viene aggiornato con le dipendenze.

### <a name="import-libraries"></a>Importare le librerie

Aprire lo script _index.js_ e importare le librerie seguenti.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_imports)]

## <a name="authenticate-the-client"></a>Autenticare il client

Aggiungere i campi seguenti alla radice dello script e inserire i relativi valori usando l'entità servizio creata e le informazioni sull'account Azure.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_constants)]

Aggiungere quindi la funzione `quickstart` seguente per gestire le operazioni principali del programma. Il primo blocco di codice crea un oggetto **CognitiveServicesManagementClient** usando le variabili di credenziali immesse sopra. Questo oggetto è necessario per tutte le operazioni di gestione di Azure.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main_auth)]

## <a name="call-management-functions"></a>Chiamare le funzioni di gestione

Aggiungere il codice seguente alla fine della funzione `quickstart` per elencare le risorse disponibili, creare una risorsa di esempio, elencare le risorse di cui si è proprietari e quindi eliminare la risorsa di esempio. Queste funzioni verranno definite nei passaggi seguenti.

## <a name="create-a-cognitive-services-resource"></a>Creare una risorsa per Servizi cognitivi

### <a name="choose-a-service-and-pricing-tier"></a>Scegliere un servizio e un piano tariffario

Quando si crea una nuova risorsa, è necessario conoscere il tipo di servizio da usare, oltre al [piano tariffario](https://azure.microsoft.com/pricing/details/cognitive-services/) (o SKU) desiderato. Questa e altre informazioni verranno usate come parametri per la creazione della risorsa. La funzione seguente elenca i tipi di Servizi cognitivi disponibili.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="create-a-cognitive-services-resource"></a>Creare una risorsa per Servizi cognitivi

Per creare e sottoscrivere una nuova risorsa di Servizi cognitivi, usare la funzione **Crea**. Questa funzione aggiunge una nuova risorsa fatturabile al gruppo di risorse che viene passato. Quando si crea una nuova risorsa, è necessario conoscere il tipo di servizio da usare, oltre al piano tariffario (o SKU) e a una località di Azure. La funzione seguente accetta tutti questi argomenti e crea una risorsa.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_create)]

## <a name="view-your-resources"></a>Visualizzare le risorse

Per visualizzare tutte le risorse nell'account Azure (in tutti i gruppi di risorse), usare la funzione seguente:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list)]

## <a name="delete-a-resource"></a>Eliminare una risorsa

La funzione seguente elimina la risorsa specificata dal gruppo di risorse specificato.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_delete)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Aggiungere il codice seguente alla fine dello script per chiamare la funzione `quickstart` principale con la gestione degli errori.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main)]

Quindi, nella finestra della console, eseguire l'applicazione con il comando `node`.

```console
node index.js
```

## <a name="see-also"></a>Vedere anche

* [Documentazione di riferimento Azure Management SDK](https://docs.microsoft.com/javascript/api/@azure/arm-cognitiveservices/?view=azure-node-latest)
* [Informazioni su Servizi cognitivi di Azure](../../Welcome.md)
* [Autenticare le richieste a Servizi cognitivi di Azure](../../authentication.md)
* [Creare una nuova risorsa con il portale di Azure](../../cognitive-services-apis-create-account.md)