---
title: Condivisione di risorse tra le origini (CORS) in Azure Cosmos DB
description: Questo articolo descrive come configurare la condivisione di risorse tra le origini (CORS) in Azure Cosmos DB usando il portale di Azure e i modelli di Azure Resource Manager.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2018
ms.author: dech
ms.openlocfilehash: 04e7d4d2e54ad037e7a486640c4ea0f32d8aefb4
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52855021"
---
# <a name="cross-origin-resource-sharing-cors-in-azure-cosmos-db"></a>Condivisione di risorse tra le origini (CORS) in Azure Cosmos DB 

Condivisione risorse tra le origini (CORS, Cross Origin Resource Sharing) è una funzionalità HTTP che consente a un'applicazione Web in esecuzione in un dominio di accedere alle risorse in un altro dominio. I Web browser implementano una limitazione di sicurezza nota come criteri di corrispondenza dell'origine, che impedisce a una pagina Web di chiamare API in un dominio diverso. La funzionalità CORS offre un modo sicuro per consentire al dominio di origine di chiamare API in un altro dominio. L'API SQL di base in Azure Cosmos DB ora supporta la condivisione di risorse tra le origini (CORS) tramite l'intestazione "allowedOrigins". Dopo l'attivazione del supporto di CORS per l'account Azure Cosmos vengono valutate solo le richieste autenticate, per determinare se sono consentite in base alle regole specificate.

È possibile configurare l'impostazione Condivisione risorse tra le origini (CORS) dal portale di Azure o da un modello di Azure Resource Manager. L'API SQL di base in Azure Cosmos DB supporta una libreria JavaScript che funziona sia negli ambienti Node.js che in quelli basati su browser. Ora questa libreria può sfruttare i vantaggi del supporto CORS quando si usa la modalità Gateway. Non è necessaria nessuna configurazione lato client per l'uso di questa funzionalità. Grazie al supporto di CORS, le risorse di un browser possono accedere direttamente ad Azure Cosmos DB tramite la [libreria JavaScript](https://www.npmjs.com/package/@azure/cosmos) o direttamente dall'[API REST](https://docs.microsoft.com/rest/api/cosmos-db/) per operazioni semplici. 

## <a name="enable-cors-support-from-azure-portal"></a>Abilitare il supporto di CORS dal portale di Azure

Seguire questa procedura per abilitare Condivisione di risorse tra le origini usando il portale di Azure:

1. Accedere all'account Azure Cosmos DB. Aprire il pannello **CORS**.

2. Specificare un elenco separato da virgole di origini che potranno effettuare chiamate multiorigine all'account Azure Cosmos DB. Ad esempio `https://www.mydomain.com`, `https://mydomain.com`, `https://api.mydomain.com`. È anche possibile usare un carattere jolly "\*" per consentire tutte le origini, quindi selezionare **Invia**. 

   > [!NOTE]
   > Attualmente non è possibile usare i caratteri jolly come parte del nome di dominio. Ad esempio il formato `https://*.mydomain.net` non è ancora supportato. 
   
   ![Abilitare la condivisione di risorse multiorigine usando il portale di Azure](./media/how-to-configure-cross-origin-resource-sharing/enable-cross-origin-resource-sharing-using-azure-portal.png)
 
## <a name="enable-cors-support-from-resource-manager-template"></a>Abilitare il supporto di CORS dal modello di Resource Manager

Per abilitare CORS usando un modello di Resource Manager, aggiungere la sezione "cors" con la proprietà "allowedOrigins" ai modelli esistenti. Il codice JSON seguente è un esempio di un modello che crea un nuovo account Azure Cosmos DB con la funzionalità CORS abilitata.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "name": "test",
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "apiVersion": "2015-04-08",
            "location": "East US 2",
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "Session",
                    "maxIntervalInSeconds": 5,
                    "maxStalenessPrefix": 100
                },
                "locations": [
                    {
                        "id": "test-eastus2",
                        "failoverPriority": 0,
                        "locationName": "East US 2"
                    }
                ],
                "cors": [
                    {
                        "allowedOrigins": "*"
                    }
                ]
            },
            "dependsOn": [
            ]
        }
    ]
}
```

## <a name="using-the-azure-cosmos-db-javascript-library-from-a-browser"></a>Uso della libreria JavaScript Azure Cosmos DB da un browser

Attualmente il pacchetto della libreria JavaScript di Azure Cosmos DB include solo la versione CommonJS. Per usare questa libreria dal browser è necessario usare uno strumento come Rollup o Webpack per creare una libreria compatibile con il browser. Per alcune librerie Node.js sono disponibili simulazioni di browser. L'esempio seguente illustra un file di configurazione webpack con le impostazioni di simulazione necessarie.

```javascript
const path = require("path");

module.exports = {
  entry: "./src/index.ts",
  devtool: "inline-source-map",
  node: {
    net: "mock",
    tls: "mock"
  },
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist")
  }
};
```
 
Di seguito è riportato un [esempio di codice](https://github.com/christopheranderson/cosmos-browser-sample) che usa TypeScript e Webpack con la libreria SDK JavaScript di Azure Cosmos DB per creare un'app Todo che invia aggiornamenti in tempo reale quando vengono creati nuovi elementi.
Come procedura consigliata, evitare l'uso della chiave primaria per comunicare con Azure Cosmos DB dal browser. Per le comunicazioni, usare invece i token delle risorse. Per altre informazioni sui token delle risorse, vedere l'articolo [Protezione dell'accesso ai dati in Azure Cosmos DB](secure-access-to-data.md#resource-tokens).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su altri modi per proteggere l'account Azure Cosmos DB, vedere gli articoli seguenti:

* [Configurare un firewall IP per l'account Azure Cosmos DB](how-to-configure-firewall.md)

* [Configurare l'accesso basato su rete virtuale e subnet per l'account di Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md)
    

