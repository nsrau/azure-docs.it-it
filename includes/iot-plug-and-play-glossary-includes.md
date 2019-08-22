---
author: ChrisGMsft
ms.service: iot-pnp
ms.topic: include
ms.date: 06/28/2019
ms.author: chrisgre
ms.openlocfilehash: 7e8174855800bc6beea8750c32a6dd32588ccd9e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880462"
---
## <a name="iot-plug-and-play-device"></a>Dispositivo Plug and Play

Un dispositivo Plug and Play è in genere un dispositivo di elaborazione autonomo su scala ridotta che può raccogliere dati o controllare altri dispositivi e che esegue software o firmware che implementa le funzionalità dichiarate in un [modello di funzionalità del dispositivo](#device-capability-model).  Ad esempio, un dispositivo Plug and Play potrebbe essere un dispositivo di monitoraggio ambientale o un controller per un sistema di irrigazione intelligente agrigulture. È possibile scrivere soluzioni di Internet delle cose ospitate nel cloud per il comando, il controllo e la ricezione di dati da dispositivi Plug and Play. I dispositivi Plug and Play sono reperibili tramite il [Catalogo dei dispositivi Azure Certified per](https://catalog.azureiotsolutions.com/)l'it. Ogni sacco Plug and Play dispositivo nel catalogo è stato convalidato e ha un [modello di capacità del dispositivo](#device-capability-model).

## <a name="digital-twin"></a>Gemello digitale

I dispositivi gemelli digitali sono modelli di dispositivi Plug and Play.  I dispositivi gemelli digitali sono modellati usando il linguaggio di definizione del dispositivo [digitale gemello](https://aka.ms/DTDL).  È possibile usare l'API Azure per l'interazione con i dispositivi gemelli digitali. 

## <a name="digital-twin-definition-language"></a>Digital Twin Definition Language

Linguaggio per la descrizione dei modelli e delle interfacce per i [dispositivi Plug and Play](#iot-plug-and-play-device).  Usa il [linguaggio di definizione doppia digitale](https://aka.ms/DTDL) per descrivere le funzionalità di un dispositivo [gemello digitale](#digital-twin) e abilitare la piattaforma Internet delle cose e le soluzioni Internet delle cose per sfruttare la semantica dell'entità.

## <a name="device-capability-model"></a>Modello di funzionalità del dispositivo

Un modello di funzionalità del dispositivo descrive un dispositivo e definisce il set di interfacce implementato dal dispositivo. Creare un modello di funzionalità del dispositivo in modo che corrisponda a un dispositivo fisico, un prodotto o uno SKU.

## <a name="interface"></a>Interfaccia

Un'interfaccia descrive le funzionalità correlate implementate da un dispositivo o un dispositivo gemello digitale. Le interfacce possono essere riutilizzate in modelli di funzionalità diversi.

## <a name="property"></a>Proprietà

Le proprietà sono campi dati definiti in un' [interfaccia](#interface) che rappresentano uno stato di un gemello digitale. È possibile dichiarare le proprietà in sola lettura o in scrittura. Le proprietà di sola lettura vengono impostate dal codice in esecuzione nel contesto del Plug and Play dispositivo stesso, ad esempio il numero di serie.  Le proprietà scrivibili vengono impostate da entità esterne, ad esempio soglie di allarme.

## <a name="telemetry"></a>Telemetria

I campi di telemetria definiti in un' [interfaccia](#interface) rappresentano le misurazioni. Queste misurazioni sono in genere valori come le letture del sensore.

## <a name="command"></a>Comando

I comandi definiti in un' [interfaccia](#interface) rappresentano i metodi che possono essere eseguiti nel dispositivo gemello digitale. Ad esempio, un comando reset per reimpostare un dispositivo.
