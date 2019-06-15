---
title: Configurazione delle App consigliate per Azure | Microsoft Docs
description: Informazioni su come utilizzare al meglio le configurazione delle App di Azure
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
ms.openlocfilehash: 3d9a597e7ced631627a121f3f0757e472f9a4bae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66393582"
---
# <a name="azure-app-configuration-best-practices"></a>Configurazione delle App consigliate per Azure

Questo articolo illustra le procedure consigliate e sui modelli comuni quando si usa una configurazione di App di Azure.

## <a name="key-groupings"></a>Raggruppamenti chiave

Configurazione delle App è disponibili due opzioni per l'organizzazione delle chiavi:

* Prefissi di chiavi
* Etichette

È possibile usare una o entrambe le opzioni per le chiavi di gruppo.

*I prefissi della chiave* rappresentano le parti di inizio delle chiavi. È possibile raggruppare un set di chiavi in modo logico usando lo stesso prefisso nei nomi. I prefissi degli spazi può contenere più componenti connessi da un delimitatore, ad esempio `/`, in modo analogo a un percorso URL, in modo da formare uno spazio dei nomi. Tali gerarchie sono utili quando si archiviano le chiavi per molte applicazioni, Servizi componenti e gli ambienti in un archivio di configurazione dell'App.

Un aspetto importante da tenere a mente è che le chiavi sono ciò che fa riferimento il codice dell'applicazione per recuperare i valori delle impostazioni corrispondente. Le chiavi non devono cambiare, altrimenti sarà necessario modificare il codice ogni volta che viene eseguita.

*Le etichette* sono un attributo sulle chiavi. Vengono utilizzati per creare varianti di una chiave. Ad esempio, è possibile assegnare etichette a più versioni di una chiave. Una versione potrebbe essere un'iterazione, un ambiente o ad altre informazioni contestuali. L'applicazione può richiedere una serie completamente diversa dei valori di chiave, specificando un'altra etichetta. Di conseguenza, tutti i riferimenti alle chiavi rimangono invariati nel codice.

## <a name="key-value-compositions"></a>Composizioni di chiave-valore

Configurazione delle App considera tutte le chiavi archiviate con esso come entità indipendenti. Configurazione delle App non prova a dedurre qualsiasi relazione tra le chiavi o per ereditare valori di chiave in base alla loro gerarchia. È possibile aggregare più set di chiavi, tuttavia, utilizzando le etichette associate a una configurazione appropriata nel codice dell'applicazione di sovrapposizione.

Ecco un esempio. Si supponga di avere un'impostazione denominata **Asset1**, il cui valore può variare in base l'ambiente di sviluppo. Si crea una chiave denominata "Asset1" con un'etichetta vuota e un'etichetta denominata "Sviluppo". Nella prima etichetta, puoi inserire il valore predefinito per **Asset1**, e inserire un valore specifico per "Sviluppo" in quest'ultimo.

Nel codice, è prima di tutto di recuperare i valori delle chiavi senza alcuna etichetta, e quindi recuperare lo stesso set di valori di chiave di una seconda volta con l'etichetta "Sviluppo". Quando si recuperano i valori la seconda volta, vengono sovrascritti i valori precedenti delle chiavi. Il sistema di configurazione di .NET Core consente "stack" più set di dati di configurazione uno sopra l'altro. Se esiste una chiave in più di un set, viene utilizzato l'ultimo set che lo contiene. Un framework di programmazione moderno, ad esempio .NET Core, offre questa funzionalità impilamento gratuitamente se si usa un provider di configurazione nativo per accedere alla configurazione di App. Il frammento di codice seguente illustra come è possibile implementare resiliente in un'applicazione .NET Core:

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Use(KeyFilter.Any, LabelFilter.Null)
           .Use(KeyFilter.Any, "Development");
});
```

## <a name="app-configuration-bootstrap"></a>Bootstrap di configurazione App

Per accedere a un archivio di configurazione dell'App, è possibile usare la stringa di connessione che è disponibile nel portale di Azure. Poiché le stringhe di connessione contengono informazioni sulle credenziali, considerate segreti. Questi segreti devono essere archiviate in Azure Key Vault e il codice è necessario eseguire l'autenticazione in Key Vault per recuperarle.

Un'opzione migliore consiste nell'usare la funzionalità di identità gestite in Azure Active Directory. Con identità gestite, è necessario solo l'URL di endpoint di configurazione delle App su bootstrap accesso all'archivio di configurazione dell'App. È possibile incorporare l'URL nel codice dell'applicazione (ad esempio, nelle *appSettings. JSON* file). Visualizzare [integra con Azure managed identità](howto-integrate-azure-managed-service-identity.md) per informazioni dettagliate.

## <a name="app-or-function-access-to-app-configuration"></a>Accesso App o funzioni di configurazione delle App

È possibile fornire l'accesso alla configurazione delle App per le app web o le funzioni usando uno dei metodi seguenti:

* Tramite il portale di Azure, immettere la stringa di connessione all'archivio di configurazione delle App nelle impostazioni dell'applicazione del servizio App.
* La stringa di connessione all'archivio di configurazione delle App di Store in Azure Key Vault e [farvi riferimento dal servizio App](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references).
* Usare Azure gestito le identità per accedere all'archivio di configurazione dell'App. Per altre informazioni, vedere [integra con Azure managed identità](howto-integrate-azure-managed-service-identity.md).
* Configurazione di push dalla configurazione dell'App nel servizio App. Configurazione dell'App fornisce una funzione di esportazione (nel portale di Azure e Azure CLI) che invia dati direttamente nel servizio App. Con questo metodo, non è necessario modificare il codice dell'applicazione affatto.

## <a name="next-steps"></a>Passaggi successivi

* [Chiavi e valori](./concept-key-value.md)
