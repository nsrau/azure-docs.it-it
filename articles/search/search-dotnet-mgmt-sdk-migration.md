---
title: Aggiornamento ad Azure Search .NET Management SDK versione 2 | Microsoft Docs
description: Aggiornamento ad Azure Search .NET Management SDK versione 2
services: search
documentationcenter: 
author: brjohnstmsft
manager: pablocas
editor: 
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/15/2018
ms.author: brjohnst
ms.openlocfilehash: ade32dcb4e0885c251c17fad46fb753b6134d027
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="upgrading-to-the-azure-search-net-management-sdk-version-2"></a>Aggiornamento ad Azure Search .NET Management SDK versione 2
Se si usa la versione 1.0.2 o successiva di [Azure Search .NET Management SDK](https://aka.ms/search-mgmt-sdk), questo articolo include informazioni utili per aggiornare l'applicazione per l'uso della versione 2.

La versione 2 di Azure Search .NET Management SDK contiene alcune modifiche rispetto alle versioni precedenti. ma per lo più secondarie, quindi il codice potrà essere modificato facilmente. Per le istruzioni su come modificare il codice per usare la nuova versione dell'SDK, vedere [Passaggi per eseguire l'aggiornamento](#UpgradeSteps) .

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2"></a>Novità della versione 2
La versione 2 di Azure Search .NET Management SDK fa riferimento alla stessa versione disponibile a livello generale dell'API REST di gestione di Ricerca di Azure a cui facevano riferimento le versioni precedenti dell'SDK, ovvero la versione del 19-08-2015. Le modifiche all'SDK sono esclusivamente modifiche sul lato client e consentono di migliorare l'usabilità dell'SDK stesso. Queste modifiche includono, ad esempio:

* `Services.CreateOrUpdate` e le relative versioni asincrone eseguono ora automaticamente il polling del provisioning di `SearchService` e non restituiscono alcun risultato fino a quando non viene completato il provisioning del servizio. In questo modo non è necessario scrivere manualmente il codice di polling.
* Se si vuole comunque eseguire manualmente il polling del provisioning del servizio, è possibile usare il nuovo metodo `Services.BeginCreateOrUpdate` o una delle relative versioni asincrone.
* Sono stati aggiunti all'SDK nuovi metodi `Services.Update` e le relative versioni asincrone. Questi metodi usano HTTP PATCH per supportare l'aggiornamento incrementale di un servizio. È ora possibile, ad esempio, ridimensionare un servizio passando a questi metodi un'istanza `SearchService` contenente solo le proprietà `partitionCount` e `replicaCount` desiderate. Il metodo usato in precedenza per chiamare `Services.Get`, modificando il servizio `SearchService` restituito e passandolo a `Services.CreateOrUpdate`, è ancora supportato, ma non è più necessario. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passaggi per eseguire l'aggiornamento
Per prima cosa aggiornare il riferimento a NuGet per `Microsoft.Azure.Management.Search` usando NuGet Package Manager Console o facendo clic con il pulsante destro del mouse sui riferimenti di progetto e scegliendo "Gestisci pacchetti NuGet" in Visual Studio.

Una volta che NuGet ha scaricato i nuovi pacchetti e le relative dipendenze, ricompilare il progetto. A seconda della struttura del codice, la ricompilazione potrebbe essere eseguita correttamente. In questo caso è possibile procedere.

Se la compilazione non riesce, è possibile che siano state implementate alcune interfacce SDK (ad esempio, a scopo di testing delle unità) che sono state modificate. Per risolvere questo problema, è necessario implementare nuovi metodi come `BeginCreateOrUpdateWithHttpMessagesAsync`.

Dopo avere corretto gli errori di compilazione, è possibile apportare modifiche all'applicazione per sfruttare la nuova funzionalità, se si vuole. Le nuove funzionalità nell'SDK sono descritte in dettaglio in [Novità della versione 2](#WhatsNew).

## <a name="conclusion"></a>Conclusioni
I commenti degli utenti sull'SDK saranno molto apprezzati. In caso di problemi, è possibile richiedere assistenza nel [forum MSDN su Ricerca di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Se si trova un bug, è possibile registrare il problema nel [repository di GitHub su Azure .NET SDK](https://github.com/Azure/azure-sdk-for-net/issues). Verificare di avere anteposto al titolo del problema il prefisso "[Ricerca di Azure]".

Grazie per avere usato Ricerca di Azure.
