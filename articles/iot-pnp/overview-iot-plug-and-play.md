---
title: Introduzione a Plug and Play IoT (anteprima) | Microsoft Docs
description: Informazioni su Plug and Play IoT (anteprima). Plug and Play IoT si basa su un linguaggio di modellazione aperto che consente ai dispositivi IoT di dichiarare le proprie funzionalità. I dispositivi IoT presentano tale dichiarazione, nota come modello di funzionalità di dispositivo, quando si connettono a soluzioni cloud come Azure IoT Central o ad applicazioni dei partner. La soluzione cloud può quindi riconoscere automaticamente il dispositivo e iniziare a interagire con esso, tutto senza scrivere codice.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 06/21/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 27063702729d8a61ab8919c2685fcfc6f743ce09
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70859000"
---
# <a name="what-is-iot-plug-and-play-preview"></a>Informazioni su Plug and Play IoT (anteprima)

Plug and Play IoT (anteprima) consente agli sviluppatori di soluzioni di integrare nei dispositivi le proprie soluzioni senza scrivere codice incorporato. La parte centrale di Plug and Play IoT Internet è costituita da uno schema del _modello di funzionalità di dispositivo_ che descrive le funzionalità dei dispositivi. Questo schema è un documento JSON strutturato come un set di interfacce che includono definizioni di:

- _Proprietà_ che rappresentano lo stato di sola lettura e di lettura/scrittura di un dispositivo o di un'altra entità. Ad esempio, il numero di serie del dispositivo può essere una proprietà di sola lettura, mentre la temperatura di destinazione di un termostato può essere una proprietà di lettura/scrittura.
- _Dati di telemetria_, ovvero i dati generati da un dispositivo, siano essi un normale flusso di letture di sensori, un errore occasionale o un messaggio informativo.
- _Comandi_ che descrivono una funzione o un'operazione che può essere eseguita su un dispositivo. Ad esempio, un comando può riavviare un gateway o scattare una foto usando una fotocamera remota.

È possibile riutilizzare le interfacce tra i vari modelli di funzionalità di dispositivo per semplificare la collaborazione e velocizzare lo sviluppo.

Per semplificare l'interazione tra Plug and Play IoT e [Gemelli digitali di Azure](../digital-twins/about-digital-twins.md), per la definizione dello schema di Plug and Play IoT si usa [DTDL (Digital Twin Definition Language)](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL), ovvero il linguaggio di definizione di Gemelli digitali. Plug and Play IoT e il linguaggio DTDL sono aperti alla community e Microsoft è lieta di ricevere i contributi di clienti, partner e dell'intero settore. Entrambi sono basati su standard W3C aperti, ad esempio JSON-LD e RDF, che facilitano l'adozione nei diversi servizi e strumenti. Non sono inoltre previsti costi aggiuntivi per l'uso di Plug and Play IoT e DTDL. La tariffe standard per [Hub IoT di Azure](../iot-hub/about-iot-hub.md), [Azure IoT Central](../iot-central/overview-iot-central.md) e altri servizi di Azure rimangono invariate.

Le soluzioni basate su hub IoT o IoT Central possono trarre vantaggio da Plug and Play IoT.

Questo articolo descrive:

- I ruoli tipici associati a un progetto che usa Plug and Play IoT.
- Come usare dispositivi Plug and Play IoT nell'applicazione.
- Come sviluppare un'applicazione per dispositivi IoT che supporti Plug and Play IoT.
- Come certificare un dispositivo Plug and Play IoT e pubblicarlo nel [catalogo dei dispositivi Certified per IoT](https://catalog.azureiotsolutions.com/).

## <a name="user-roles"></a>Ruoli utente

Plug and Play IoT è utile per due tipi di sviluppatori:

- Uno _sviluppatore di soluzioni_ è responsabile dello sviluppo di una soluzione IoT che usa Azure IoT e altre risorse di Azure e dell'identificazione dei dispositivi IoT da integrare.
- Uno _sviluppatore di dispositivi_ crea il codice che viene eseguito in un dispositivo connesso alla soluzione.

## <a name="use-iot-plug-and-play-devices"></a>Usare dispositivi Plug and Play IoT

Gli sviluppatori di soluzioni possono sviluppare una soluzione IoT ospitata nel cloud che usa dispositivi Plug and Play IoT. È possibile usare i servizi di Azure seguenti:

- [IoT Central](../iot-central/overview-iot-central.md): una soluzione SaaS (Software-as-a-Service) IoT completamente gestita per semplificare la creazione di prodotti che si connettono ai mondi fisico e digitale.
- [IoT Hub](../iot-hub/about-iot-hub.md): un servizio cloud gestito che funge da hub dei messaggi per le comunicazioni sicure e bidirezionali tra l'applicazione IoT e i dispositivi.

I dispositivi Plug and Play IoT sono disponibili nel catalogo dei dispositivi Azure Certified per IoT. Ogni dispositivo Plug and Play IoT presente nel catalogo è stato convalidato e include un modello di funzionalità di dispositivo. Visualizzare il modello di funzionalità di dispositivo per informazioni sulla funzionalità del dispositivo o usarlo per simulare il dispositivo in Azure IoT Central.

Quando si connette un dispositivo Plug and Play IoT, è possibile visualizzarne il modello di funzionalità di dispositivo, le interfacce incluse nel modello, nonché i dati di telemetria, le proprietà e i comandi definiti in tali interfacce.

## <a name="develop-an-iot-device-application"></a>Sviluppare un'applicazione per dispositivi IoT

Gli sviluppatori di dispositivi possono sviluppare un prodotto hardware IoT che supporti Plug and Play IoT. Il processo prevede due passaggi chiave:

1. Definire il modello di funzionalità di dispositivo e le interfacce. Per creare un set di file JSON che dichiarano le funzionalità del dispositivo, si usa il linguaggio [DTDL](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL). Un modello di funzionalità di dispositivo descrive un'entità completa, ad esempio un prodotto fisico, e definisce il set di interfacce implementate da tale entità. Le interfacce sono contratti condivisi che identificano in modo univoco i dati di telemetria, le proprietà e i comandi supportati da un dispositivo. Le interfacce possono essere riutilizzate in diversi modelli di funzionalità di dispositivo.

1. Creare il software o il firmware del dispositivo che implementa le funzionalità dichiarate nelle interfacce e nel modello di funzionalità di dispositivo. Azure IoT SDK include le API per implementare modelli di funzionalità di dispositivo.

Il pacchetto di estensione [Azure IoT Tools per VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) fornisce numerose funzionalità utili. Uno sviluppatore di dispositivi può ad esempio usare un'estensione per generare uno scheletro di progetto C da un modello di funzionalità. È tuttavia possibile usare qualsiasi IDE per creare e implementare modelli di funzionalità di dispositivo.

## <a name="certify-an-iot-plug-and-play-device"></a>Certificare un dispositivo Plug and Play IoT

Gli sviluppatori di dispositivi possono inviare prodotti hardware IoT per la certificazione. È possibile pubblicare un dispositivo certificato nel catalogo dei dispositivi Certified per IoT. I passaggi del processo di certificazione includono:

- Adesione al programma [Microsoft Partner Network](https://partner.microsoft.com).
- Onboarding al portale Azure Certified per IoT.
- Invio di un modello di funzionalità di dispositivo Plug and Play IoT e delle informazioni di marketing per creare un nuovo record di dispositivo.
- Superamento del set automatizzato di test di convalida per il dispositivo.
- Pubblicazione nel catalogo dei dispositivi Certified per IoT.

## <a name="regional-availability"></a>Disponibilità internazionale

Nella fase di anteprima pubblica Plug and Play IoT è disponibile nelle aree Europa settentrionale, Stati Uniti centrali e Giappone orientale. Assicurarsi quindi che l'hub venga creato in una di queste aree.

## <a name="message-quotas-in-iot-hub"></a>Quote messaggi nell'hub IoT
Nella fase di anteprima pubblica i dispositivi Plug and Play IoT inviano messaggi distinti per ogni interfaccia. Questo comportamento può implicare un aumento del numero di messaggi conteggiati ai fini della [quota messaggi](../iot-hub/iot-hub-devguide-quotas-throttling.md).

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver letto la panoramica di Plug and Play IoT, come passaggio successivo consigliato è possibile provare alcune delle guide di avvio rapido:

- [Usare un modello di funzionalità di dispositivo per creare un dispositivo Plug and Play IoT](./quickstart-create-pnp-device.md)
- [Connettere un dispositivo all'hub IoT](./quickstart-connect-pnp-device.md)
- [Connettersi a un dispositivo nella soluzione](./quickstart-connect-pnp-device-solution.md)
