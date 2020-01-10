---
title: Procedure consigliate per la configurazione di app Azure | Microsoft Docs
description: Informazioni su come usare al meglio la configurazione di app Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 3b43ca5b6bec64d9283a64c9bcc0a3b60c21bca4
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750410"
---
# <a name="azure-app-configuration-best-practices"></a>Procedure consigliate per la configurazione di app Azure

Questo articolo illustra i modelli comuni e le procedure consigliate quando si usa la configurazione di app Azure.

## <a name="key-groupings"></a>Raggruppamenti di chiavi

La configurazione dell'app offre due opzioni per organizzare le chiavi:

* Prefissi di chiave
* Etichette

È possibile usare una o entrambe le opzioni per raggruppare le chiavi.

I *prefissi di chiave* sono le parti iniziali delle chiavi. È possibile raggruppare logicamente un set di chiavi usando lo stesso prefisso nei rispettivi nomi. I prefissi possono contenere più componenti connessi da un delimitatore, ad esempio `/`, simile a un percorso URL, per formare uno spazio dei nomi. Queste gerarchie sono utili quando si archiviano chiavi per molte applicazioni, Servizi componenti e ambienti in un archivio di configurazione dell'app.

Un aspetto importante da tenere presente è che le chiavi sono i riferimenti del codice dell'applicazione per recuperare i valori delle impostazioni corrispondenti. Le chiavi non devono essere modificate, altrimenti sarà necessario modificare il codice ogni volta che si verifica.

Le *etichette* sono un attributo delle chiavi. Sono usati per creare varianti di una chiave. Ad esempio, è possibile assegnare etichette a più versioni di una chiave. Una versione potrebbe essere un'iterazione, un ambiente o altre informazioni contestuali. L'applicazione può richiedere un set completamente diverso di valori di chiave specificando un'altra etichetta. Di conseguenza, tutti i riferimenti alla chiave rimangono invariati nel codice.

## <a name="key-value-compositions"></a>Composizioni chiave-valore

La configurazione dell'app considera tutte le chiavi archiviate come entità indipendenti. La configurazione dell'app non tenta di dedurre alcuna relazione tra le chiavi o di ereditare i valori delle chiavi in base alla gerarchia. È possibile aggregare più insiemi di chiavi, tuttavia, usando etichette abbinate al corretto stack di configurazione nel codice dell'applicazione.

Ecco un esempio. Si supponga di avere un'impostazione denominata **Asset1**, il cui valore può variare in base all'ambiente di sviluppo. Si crea una chiave denominata "Asset1" con un'etichetta vuota e un'etichetta denominata "Development". Nella prima etichetta si inserisce il valore predefinito per **Asset1**e si inserisce un valore specifico per "Development" nel secondo.

Nel codice è necessario innanzitutto recuperare i valori della chiave senza alcuna etichetta e quindi recuperare la stessa serie di valori di chiave una seconda volta con l'etichetta "Development". Quando si recuperano i valori la seconda volta, i valori precedenti delle chiavi vengono sovrascritti. Il sistema di configurazione di .NET Core consente di "stack" più set di dati di configurazione uno sull'altro. Se una chiave è presente in più di un set, viene usato l'ultimo set che lo contiene. Con un Framework di programmazione moderno, ad esempio .NET Core, questa funzionalità di stacking è disponibile gratuitamente se si usa un provider di configurazione nativo per accedere alla configurazione dell'app. Il frammento di codice seguente mostra come è possibile implementare lo stack in un'applicazione .NET Core:

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

## <a name="app-configuration-bootstrap"></a>Bootstrap configurazione app

Per accedere a un archivio di configurazione dell'app, è possibile usare la relativa stringa di connessione, disponibile nel portale di Azure. Poiché le stringhe di connessione contengono informazioni sulle credenziali, sono considerate segreti. Questi segreti devono essere archiviati in Azure Key Vault e il codice deve eseguire l'autenticazione a Key Vault per recuperarli.

Un'opzione migliore consiste nell'usare la funzionalità identità gestite in Azure Active Directory. Con le identità gestite è necessario solo l'URL dell'endpoint di configurazione dell'app per avviare l'accesso all'archivio di configurazione dell'app. È possibile incorporare l'URL nel codice dell'applicazione, ad esempio nel file *appSettings. JSON* . Per informazioni dettagliate, vedere l' [integrazione con le identità gestite di Azure](howto-integrate-azure-managed-service-identity.md) .

## <a name="app-or-function-access-to-app-configuration"></a>Accesso all'app o alla funzione per la configurazione dell'app

È possibile fornire l'accesso alla configurazione dell'app per le app Web o le funzioni usando uno dei metodi seguenti:

* Tramite il portale di Azure, immettere la stringa di connessione all'archivio di configurazione dell'app nelle impostazioni dell'applicazione del servizio app.
* Archiviare la stringa di connessione nell'archivio di configurazione dell'app in Key Vault e [farvi riferimento dal servizio app](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references).
* Usare le identità gestite di Azure per accedere all'archivio di configurazione dell'app. Per altre informazioni, vedere [integrazione con le identità gestite di Azure](howto-integrate-azure-managed-service-identity.md).
* Eseguire il push della configurazione dalla configurazione dell'app al servizio app. La configurazione dell'app fornisce una funzione di esportazione (in portale di Azure e l'interfaccia della riga di comando di Azure) che invia i dati direttamente nel servizio app. Con questo metodo non è necessario modificare il codice dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

* [Chiavi e valori](./concept-key-value.md)
