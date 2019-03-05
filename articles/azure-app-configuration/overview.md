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
ms.openlocfilehash: 11dd91039bb352e86800982d0a294f82622a56fe
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885020"
---
# <a name="what-is-azure-app-configuration"></a>Informazioni su Configurazione app di Azure

Configurazione app di Azure è un servizio per la gestione centralizzata delle impostazioni delle applicazioni. Le applicazioni moderne, specialmente quelle eseguite nel cloud, hanno in genere molti componenti che sono distribuiti per natura. Disseminare le impostazioni di configurazione tra questi componenti può generare errori difficili da risolvere durante la distribuzione di un'applicazione. Configurazione app consente di archiviare tutte le impostazioni dell'applicazione e di proteggerne gli accessi in un'unica posizione.

## <a name="why-use-app-configuration"></a>Perché usare Configurazione app

Le applicazioni basate sul cloud vengono spesso eseguite in più macchine virtuali o contenitori in più aree e usano più servizi esterni. La creazione di un'applicazione così distribuita che sia al contempo affidabile e scalabile è una vera sfida. Sono state messe a punto varie metodologie di programmazione per aiutare gli sviluppatori a far fronte alla crescente complessità legata alla compilazione di queste applicazioni. Ad esempio, l'app a 12 fattori illustra nel dettaglio molti modelli di architettura e procedure consigliate ben testati per l'uso con le applicazioni cloud. Un consiglio fondamentale di questa guida è quello di separare la configurazione dal codice. Questo significa che la configurazione delle impostazioni di un'applicazione deve essere mantenuta esterna al relativo eseguibile e letta dall'ambiente di runtime o da un'origine esterna.

Di seguito sono riportati esempi dei tipi di applicazioni che trarrebbero i migliori vantaggi dall'uso di Configurazione app, che può comunque essere sfruttato da qualunque applicazione:

* Microservizi basati sul servizio Azure Container, su Service Fabric o su altre app aggiunte a contenitori distribuite in una o più aree geografiche.
* App serverless, tra cui Funzioni di Azure o altre app di calcolo senza stato basate su eventi.
* Pipeline di distribuzione continua.

Configurazione app offre i vantaggi seguenti:

* Un servizio completamente gestito che può essere configurato in pochi minuti.
* Mapping e rappresentazioni di chiavi flessibili.
* Assegnazione di tag con le etichette.
* Riproduzione temporizzata delle impostazioni.
* Confronto di due set di configurazioni su dimensioni personalizzate.
* Sicurezza avanzata grazie alle identità gestite da Azure.
* Crittografia completa dei dati inattivi e in transito.
* Integrazione nativa con i framework più diffusi.

## <a name="how-to-use-app-configuration"></a>Come usare Configurazione app

Il modo più facile di aggiungere un archivio di configurazione delle app alla propria applicazione è tramite una libreria client fornita da Microsoft. Di seguito sono indicati i migliori metodi a disposizione in base al linguaggio di programmazione e al framework in uso:

| Linguaggio di programmazione e framework | Come connettersi |
|---|---|
| **.NET Core** e **ASP.NET Core** | Provider di configurazione di Configurazione app per .NET Core. |
| **.NET** e **ASP.NET** | Generatore di configurazione di Configurazione app per .NET. |
| **Java Spring** | Client di configurazione di Configurazione app per Spring Cloud. |
| Altro | API REST di Configurazione app. |

## <a name="next-steps"></a>Passaggi successivi

* [Guida introduttiva: Creare un'app Web ASP.NET](quickstart-aspnet-core-app.md)  
