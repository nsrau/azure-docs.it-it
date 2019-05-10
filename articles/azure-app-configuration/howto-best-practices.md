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
ms.openlocfilehash: d1275a48de5cad9321186ba20860d853b8ce55ad
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413611"
---
# <a name="azure-app-configuration-best-practices"></a>Configurazione delle App consigliate per Azure

Questo articolo illustra i modelli e procedure consigliate comuni quando si utilizza configurazione delle App di Azure.

## <a name="key-groupings"></a>Raggruppamenti chiave

Configurazione delle App è disponibili due opzioni per l'organizzazione delle chiavi: chiavi prefissi o etichette. È possibile usare uno o entrambi.

I prefissi chiave sono le parti di inizio delle chiavi. È possibile raggruppare un set di chiavi in modo logico usando lo stesso prefisso nei nomi. I prefissi degli spazi può contenere più componenti connessi tra loro da un delimitatore, ad esempio `/`, in modo analogo a un percorso URL, in modo da formare uno spazio dei nomi. Tali gerarchie sono utili quando si archiviano le chiavi per molte applicazioni, Servizi componenti e gli ambienti in un archivio di configurazione dell'App. Un aspetto importante da tenere a mente è che le chiavi sono ciò che fa riferimento il codice dell'applicazione per recuperare i valori delle impostazioni corrispondente. Non è necessario modificare una chiave, altrimenti sarà necessario modificare il codice ogni volta che viene eseguita.

Le etichette è un attributo sulle chiavi. Vengono utilizzati per creare varianti di una chiave. Ad esempio, è possibile assegnare etichette a più versioni di una chiave. Una versione può essere un'iterazione, ambiente o ad altre informazioni contestuali. L'applicazione può richiedere un set di valori di chiave completamente diverso semplicemente specificando un'altra etichetta. Tutti i riferimenti alle chiavi possono restare invariati.

## <a name="key-value-compositions"></a>Composizioni di chiave-valore

Configurazione delle App considera tutte le chiavi archiviate con esso come entità indipendenti. Non tenta di dedurre qualsiasi relazione tra le chiavi o ereditare valori di chiave in base alla loro gerarchia. È possibile aggregare più set di chiavi, tuttavia, utilizzando le etichette insieme a una configurazione appropriata nel codice dell'applicazione di sovrapposizione.

Ecco un esempio. Si dispone di un'impostazione **Asset1** il cui valore sia diverso per l'ambiente di "Sviluppo". È possibile creare una chiave denominata "Asset1" con un'etichetta vuota e un'etichetta denominata "Sviluppo". Inserire il valore predefinito per **Asset1** in quest'ultimo e qualsiasi valore specifico per "Sviluppo" in quest'ultimo. Nel codice, è innanzitutto necessario recuperare i valori delle chiavi senza un'etichetta e quindi quelli con un'etichetta di "Sviluppo" per sovrascrivere eventuali valori precedenti delle stesse chiavi. Se si usa un framework di programmazione moderno, ad esempio .NET Core, è possibile ottenere questa funzionalità impilamento gratuitamente se si usa un provider di configurazione nativo per accedere alla configurazione di App. Il frammento di codice seguente illustra come è possibile implementare resiliente in un'applicazione .NET Core.

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

Per accedere a un archivio di configurazione dell'App, è possibile usare la stringa di connessione che è disponibile nel portale di Azure. Le stringhe di connessione contengono informazioni sulle credenziali e sono considerate come segreti. Devono essere archiviati in un insieme di credenziali chiave. È preferibile per usare Azure gestito di identità. Con questo metodo, è necessario solo URL di endpoint di configurazione delle App per l'accesso all'archivio di configurazione di avvio automatico. È possibile incorporare l'URL nel codice dell'applicazione (ad esempio, nelle *appSettings. JSON* file). Visualizzare [integra con Azure managed identità](howto-integrate-azure-managed-service-identity.md) per altri dettagli.

## <a name="web-app-or-function-access-to-app-configuration"></a>App Web o una funzione di accesso alla configurazione delle App

È possibile immettere la stringa di connessione all'archivio di configurazione delle App nelle impostazioni dell'applicazione del servizio App tramite il portale di Azure. È anche possibile archiviarlo in Key Vault e [farvi riferimento dal servizio App](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references). È anche possibile usare Azure gestito di identità per accedere all'archivio di configurazione. Visualizzare [integra con Azure managed identità](howto-integrate-azure-managed-service-identity.md) per altri dettagli.

In alternativa, è possibile eseguire il push configurazione dalla configurazione dell'App nel servizio App. Configurazione dell'App fornisce una funzione di esportazione (nel portale di Azure e della riga di comando) che invia dati direttamente nel servizio App. Con questo metodo, non devi modificare tutto il codice dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

* [Chiavi e valori](./concept-key-value.md)
