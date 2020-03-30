---
title: Procedure consigliate per la configurazione delle app di Azure Documenti Microsoft
description: Informazioni su come usare al meglio la configurazione delle app di Azure
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: df56f53b64a35737700529b80c004efeb31eaabc
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348671"
---
# <a name="azure-app-configuration-best-practices"></a>Procedure consigliate per la configurazione delle app di AzureAzure App Configuration best practices

Questo articolo illustra i modelli comuni e le procedure consigliate quando si usa Configurazione app di Azure.This article discusses common patterns and best practices when you're using Azure App Configuration.

## <a name="key-groupings"></a>Raggruppamenti di chiaviKey groupings

Configurazione app offre due opzioni per l'organizzazione delle chiavi:App Configuration provides two options for organizing keys:

* Prefissi di chiave
* Etichette

È possibile utilizzare una o entrambe le opzioni per raggruppare le chiavi.

*I prefissi* di chiave sono le parti iniziali delle chiavi. È possibile raggruppare logicamente un set di chiavi utilizzando lo stesso prefisso nei relativi nomi. I prefissi possono contenere più componenti connessi `/`da un delimitatore, ad esempio , simile a un percorso URL, per formare uno spazio dei nomi. Tali gerarchie sono utili quando si archiviano le chiavi per molte applicazioni, servizi componenti e ambienti in un archivio di configurazione app.

Una cosa importante da tenere a mente è che le chiavi sono ciò che il codice dell'applicazione fa riferimento per recuperare i valori delle impostazioni corrispondenti. Le chiavi non devono essere modificate, altrimenti dovrai modificare il codice ogni volta che si verifica.

*Le etichette* sono un attributo sulle chiavi. Vengono utilizzati per creare varianti di una chiave. Ad esempio, è possibile assegnare etichette a più versioni di una chiave. Una versione può essere un'iterazione, un ambiente o altre informazioni contestuali. L'applicazione può richiedere un set completamente diverso di valori di chiave specificando un'altra etichetta. Di conseguenza, tutti i riferimenti alle chiavi rimangono invariati nel codice.

## <a name="key-value-compositions"></a>Composizioni chiave-valore

La configurazione delle app considera tutte le chiavi archiviate come entità indipendenti. Configurazione app non tenta di dedurre qualsiasi relazione tra le chiavi o di ereditare i valori di chiave in base alla gerarchia. È tuttavia possibile aggregare più set di chiavi usando etichette associate a uno stack di configurazione corretto nel codice dell'applicazione.

Ecco un esempio. Si supponga di disporre di un'impostazione denominata **Asset1**, il cui valore può variare in base all'ambiente di sviluppo. Creare una chiave denominata "Asset1" con un'etichetta vuota e un'etichetta denominata "Sviluppo". Nella prima etichetta si inserisce il valore predefinito per **Asset1**e si inserisce un valore specifico per "Sviluppo" in quest'ultimo.

Nel codice, è innanzitutto recuperare i valori di chiave senza etichette e quindi recuperare lo stesso set di valori di chiave una seconda volta con l'etichetta "Sviluppo". Quando si recuperano i valori la seconda volta, i valori precedenti delle chiavi vengono sovrascritti. Il sistema di configurazione .NET Core consente di "impilare" più set di dati di configurazione uno sopra l'altro. Se una chiave è presente in più set, viene utilizzato l'ultimo set che la contiene. Con un framework di programmazione moderno, ad esempio .NET Core, si ottiene questa funzionalità di stacking gratuitamente se si utilizza un provider di configurazione nativo per accedere a Configurazione app. Il frammento di codice seguente mostra come implementare lo stacking in un'applicazione .NET Core:The following code snippet shows how you can implement stacking in a .NET Core application:

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

[Utilizzare le etichette per abilitare configurazioni diverse per ambienti diversi](./howto-labels-aspnet-core.md) fornisce un esempio completo.

## <a name="app-configuration-bootstrap"></a>Bootstrap di configurazione app

Per accedere a un archivio di configurazione app, è possibile usare la relativa stringa di connessione, disponibile nel portale di Azure.To access an App Configuration store, you can use its connection string, which is available in the Azure portal. Poiché le stringhe di connessione contengono informazioni sulle credenziali, vengono considerate segreti. Questi segreti devono essere archiviati nell'insieme di credenziali delle chiavi di Azure e il codice deve autenticarsi nell'insieme di credenziali delle chiavi per recuperarli.

Un'opzione migliore consiste nell'usare la funzionalità delle identità gestite in Azure Active Directory.A better option is to use the managed identities feature in Azure Active Directory. Con le identità gestite, è necessario solo l'URL dell'endpoint di configurazione app per eseguire il bootstrap dell'accesso all'archivio di configurazione delle app. È possibile incorporare l'URL nel codice dell'applicazione, ad esempio nel file *appsettings.json.* Per informazioni [dettagliate, vedere Integrazione con le identità gestite di Azure.See Integrate with Azure managed identities](howto-integrate-azure-managed-service-identity.md) for details.

## <a name="app-or-function-access-to-app-configuration"></a>Accesso alle app o alle funzioni di Configurazione app

È possibile fornire l'accesso a Configurazione app per le app Web o le funzioni utilizzando uno dei metodi seguenti:

* Tramite il portale di Azure immettere la stringa di connessione all'archivio di configurazione app nelle impostazioni dell'applicazione del servizio app.
* Archiviare la stringa di connessione all'archivio di configurazione app nell'insieme di credenziali delle chiavi e [farvi riferimento dal servizio app](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references).
* Usare le identità gestite di Azure per accedere all'archivio di configurazione delle app. Per altre informazioni, vedere [Integrazione con le identità gestite di Azure](howto-integrate-azure-managed-service-identity.md).For more information, see Integrate with Azure managed identities .
* Configurazione push da Configurazione app al servizio app. Configurazione app offre una funzione di esportazione (nel portale di Azure e nell'interfaccia della riga di comando di Azure) che invia i dati direttamente nel servizio app. Con questo metodo, non è necessario modificare il codice dell'applicazione a tutti.

## <a name="reduce-requests-made-to-app-configuration"></a>Ridurre le richieste effettuate alla configurazione dell'app

Un numero eccessivo di richieste di configurazione delle app può comportare la limitazione delle richieste o costi di superaone. Per ridurre il numero di richieste effettuate:

* Aumentare il timeout di aggiornamento, soprattutto se i valori di configurazione non cambiano frequentemente. Specificare un nuovo [ `SetCacheExpiration` ](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.azureappconfigurationrefreshoptions.setcacheexpiration)timeout di aggiornamento utilizzando il metodo .

* Guarda una singola *chiave sentinel,* invece di guardare i singoli tasti. Aggiornare tutta la configurazione solo se la chiave sentinel cambia. Per un esempio, vedere Usare la [configurazione dinamica in un'app ASP.NET Core.See](enable-dynamic-configuration-aspnet-core.md) Use dynamic configuration in an ASP.NET Core app for an example.

* Usare Griglia di eventi di Azure per ricevere notifiche quando la configurazione cambia, anziché eseguire costantemente il polling di eventuali modifiche. Per altre informazioni, vedere [Instradare](./howto-app-configuration-event.md) gli eventi di configurazione delle app di Azure a un endpoint Web

## <a name="importing-configuration-data-into-app-configuration"></a>Importazione dei dati di configurazione nella configurazione dell'appImporting configuration data into App Configuration

Configurazione app offre l'opzione per [importare](https://aka.ms/azconfig-importexport1) in blocco le impostazioni di configurazione dai file di configurazione correnti usando il portale di Azure o l'interfaccia della riga di comando. È inoltre possibile utilizzare le stesse opzioni per esportare i valori da Configurazione app, ad esempio tra negozi correlati. Se si desidera configurare una sincronizzazione in corso con il repository GitHub, è possibile utilizzare la nostra [azione GitHub](https://aka.ms/azconfig-gha2) in modo da poter continuare a utilizzare le procedure di controllo del codice sorgente esistenti ottenendo i vantaggi di Configurazione App.

## <a name="next-steps"></a>Passaggi successivi

* [Chiavi e valori](./concept-key-value.md)
