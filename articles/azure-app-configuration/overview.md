---
title: Informazioni su Configurazione app di Azure | Microsoft Docs
description: Panoramica del servizio Configurazione app di Azure.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 985845197f8a1ece76fe0a620f05194109f51bd6
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65408672"
---
# <a name="what-is-azure-app-configuration"></a>Informazioni su Configurazione app di Azure

Configurazione app di Azure è un servizio per la gestione centralizzata delle impostazioni delle applicazioni e dei flag di funzionalità. I programmi moderni, specialmente quelli eseguiti nel cloud, hanno in genere molti componenti che sono distribuiti per natura. Disseminare le impostazioni di configurazione tra questi componenti può generare errori difficili da risolvere durante la distribuzione di un'applicazione. Configurazione app consente di archiviare tutte le impostazioni dell'applicazione e di proteggerne gli accessi in un'unica posizione.

Configurazione app è attualmente in anteprima pubblica. È gratuita durante il periodo di anteprima. È possibile effettuare l'iscrizione nel [portale di Azure](https://portal.azure.com).

## <a name="why-use-app-configuration"></a>Perché usare Configurazione app

Le applicazioni basate sul cloud vengono spesso eseguite in più macchine virtuali o contenitori in più aree e usano più servizi esterni. Creare un'applicazione distribuita che sia robusta e scalabile è una sfida.

Diverse metodologie di programmazione consentono agli sviluppatori di far fronte alla crescente complessità legata alla compilazione di queste applicazioni. Ad esempio, l'app a 12 fattori illustra molti modelli di architettura e procedure consigliate ben testati per l'uso con le applicazioni cloud. Un consiglio fondamentale di questa guida è quello di separare la configurazione dal codice. In questo caso, le impostazioni di configurazione di un'applicazione devono essere mantenute esterne al relativo eseguibile e lette dall'ambiente di runtime o da un'origine esterna.

Di seguito sono riportati esempi dei tipi di applicazione che traggono vantaggio dall'uso di Configurazione app, che può comunque essere usato da qualsiasi applicazione:

* Microservizi basati sul servizio Azure Kubernetes, sul servizio Service Fabric o su altre app aggiunte a contenitori distribuite in una o più aree geografiche
* App serverless, tra cui Funzioni di Azure o altre app di calcolo senza stato basate su eventi
* Pipeline di distribuzione continua

Configurazione app offre i vantaggi seguenti:

* Un servizio completamente gestito che può essere configurato in pochi minuti
* Mapping e rappresentazioni di chiavi flessibili
* Assegnazione di tag con le etichette
* Riproduzione temporizzata delle impostazioni
* Interfaccia utente dedicata per la gestione dei flag di funzionalità
* Confronto di due set di configurazioni su dimensioni personalizzate
* Sicurezza avanzata grazie alle identità gestite da Azure
* Crittografia completa dei dati inattivi o in transito
* Integrazione nativa con i framework più diffusi

Configurazione app completa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), che viene usato per archiviare i segreti dell'applicazione. Configurazione app rende più semplice implementare gli scenari seguenti:

* Centralizzare la gestione e la distribuzione dei dati di configurazione gerarchici per ambienti e aree geografiche diversi
* Modificare in modo dinamico le impostazioni applicazione senza la necessità di ridistribuire o riavviare un'applicazione
* Controllare la disponibilità delle funzionalità in tempo reale

## <a name="use-app-configuration"></a>Usare Configurazione app

Il modo più facile di aggiungere un archivio di configurazione delle app alla propria applicazione è tramite una libreria client fornita da Microsoft. Di seguito sono indicati i migliori metodi a disposizione in base al linguaggio di programmazione e al framework in uso.

| Linguaggio di programmazione e framework | Come connettersi |
|---|---|
| .NET Core e ASP.NET Core | Provider di Configurazione app per .NET Core |
| .NET e ASP.NET | Generatore di Configurazione app per .NET |
| Java Spring | Client di Configurazione app per Spring Cloud |
| Altro | API REST di Configurazione app |

## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido per ASP.NET Core](./quickstart-aspnet-core-app.md)
* [Avvio rapido per .NET Core](./quickstart-dotnet-core-app.md)
* [Avvio rapido per .NET Framework](./quickstart-dotnet-app.md)
* [Avvio rapido per Funzioni di Azure](./quickstart-azure-function-csharp.md)
* [Avvio rapido per Java Spring](./quickstart-java-spring-app.md)
* [Avvio rapido per i flag di funzionalità di ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)
