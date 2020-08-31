---
title: Introduzione a Plug and Play IoT (anteprima) | Microsoft Docs
description: Informazioni su Plug and Play IoT (anteprima). Plug and Play IoT si basa su un linguaggio di modellazione aperto che consente ai dispositivi IoT intelligenti di dichiarare le proprie funzionalità. I dispositivi IoT presentano tale dichiarazione, definita modello di dispositivo, quando si connettono alle soluzioni cloud. La soluzione cloud può quindi riconoscere automaticamente il dispositivo e iniziare a interagire con esso, tutto senza scrivere codice.
author: rido-min
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: overview
ms.service: iot-pnp
services: iot-pnp
manager: eliotgra
ms.custom: references_regions
ms.openlocfilehash: 32a873af3d287c3bd1e83de6db8e17ebc1d2958b
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855634"
---
# <a name="what-is-iot-plug-and-play-preview"></a>Informazioni su Plug and Play IoT (anteprima)

Plug and Play IoT (anteprima) consente agli sviluppatori di soluzioni di integrare dispositivi intelligenti con le rispettive soluzioni senza configurazione manuale. Plug and Play IoT è basato su un _modello_ di dispositivo che viene usato dal dispositivo per annunciare le rispettive funzionalità a un'applicazione abilitata per Plug and Play IoT. Questo modello è strutturato come un set di elementi che definiscono quanto segue:

- _Proprietà_ che rappresentano lo stato di sola lettura e di scrittura di un dispositivo o di un'altra entità. Ad esempio, il numero di serie del dispositivo può essere una proprietà di sola lettura, mentre la temperatura di destinazione di un termostato può essere una proprietà scrivibile.
- _Dati di telemetria_ generati da un dispositivo, siano essi un normale flusso di letture di sensori, un errore occasionale o un messaggio informativo.
- _Comandi_ che descrivono una funzione o un'operazione che può essere eseguita su un dispositivo. Ad esempio, un comando può riavviare un gateway o scattare una foto usando una fotocamera remota.

È possibile raggruppare questi elementi in interfacce per riutilizzarli tra i vari modelli per semplificare la collaborazione e accelerare lo sviluppo.

Per semplificare l'interazione tra Plug and Play IoT e [Gemelli digitali di Azure](../digital-twins/about-digital-twins.md), definire modelli e interfacce usando [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl). Plug and Play IoT e il linguaggio DTDL sono aperti alla community e Microsoft è lieta di ricevere i contributi di clienti, partner e dell'intero settore. Entrambi sono basati su standard W3C aperti, ad esempio JSON-LD e RDF, che facilitano l'adozione nei diversi servizi e strumenti.

Non sono previsti costi aggiuntivi per l'uso di Plug and Play IoT e DTDL. La tariffe standard per l'[Hub IoT di Azure](../iot-hub/about-iot-hub.md) e altri servizi di Azure rimangono invariate.

Questo articolo descrive:

- I ruoli tipici associati a un progetto che usa Plug and Play IoT.
- Come usare dispositivi Plug and Play IoT nell'applicazione.
- Come sviluppare un'applicazione per dispositivi IoT che supporti Plug and Play IoT.

## <a name="user-roles"></a>Ruoli utente

Plug and Play IoT è utile per due tipi di sviluppatori:

- Un _autore di soluzioni_ è responsabile dello sviluppo di una soluzione IoT che usa un hub IoT di Azure e altre risorse di Azure e dell'identificazione dei dispositivi IoT da integrare.
- Un _creatore di dispositivi_ crea il codice che viene eseguito in un dispositivo connesso alla soluzione.

## <a name="use-iot-plug-and-play-devices"></a>Usare dispositivi Plug and Play IoT

Gli autori di soluzioni possono sviluppare una soluzione IoT ospitata nel cloud che usa dispositivi Plug and Play IoT. Usare l'[hub IoT](../iot-hub/about-iot-hub.md), un servizio cloud gestito che funge da hub dei messaggi per le comunicazioni sicure e bidirezionali tra l'applicazione IoT e i dispositivi.

Quando si connette un dispositivo Plug and Play IoT a un hub IoT, è possibile usare lo strumento [Azure IoT Explorer](./howto-use-iot-explorer.md) per visualizzare la telemetria, le proprietà e i comandi definiti nelle interfacce che compongono il modello.

## <a name="develop-an-iot-device-application"></a>Sviluppare un'applicazione per dispositivi IoT

I creatori di dispositivi possono sviluppare un prodotto hardware IoT che supporti Plug and Play IoT. Il processo include tre passaggi principali:

1. Definire il modello di dispositivo. Per creare un set di file JSON che definiscono le funzionalità del dispositivo, si usa il linguaggio [DTDL](https://github.com/Azure/opendigitaltwins-dtdl). Un modello descrive un'entità completa, ad esempio un prodotto fisico, e definisce il set di interfacce implementate da tale entità. Le interfacce sono contratti condivisi che identificano in modo univoco i dati di telemetria, le proprietà e i comandi supportati da un dispositivo. Le interfacce possono essere riutilizzate in diversi modelli.

1. Creare il software o il firmware del dispositivo in modo che la telemetria, le proprietà e i comandi corrispondenti seguano le convenzioni di Plug and Play IoT.

1. Il dispositivo annuncia l'ID modello come parte della connessione MQTT. Azure IoT SDK include nuovi costrutti per fornire l'ID modello in fase di connessione.

> [!Important]
> I dispositivi Plug and Play IoT devono usare MQTT o MQTT su WebSockets. Altri protocolli, tra cui AMQP o HTTP, non sono validi per l'implementazione di dispositivi Plug and Play IoT.

## <a name="device-certification"></a>Certificato del dispositivo

Il [programma di certificazione dei dispositivi Plug and Play IoT](howto-certify-device.md) verifica che un dispositivo soddisfi i requisiti di certificazione Plug and Play IoT. È possibile aggiungere un dispositivo certificato al [catalogo di dispositivi Certified for Azure IoT](https://aka.ms/devicecatalog) pubblico.

## <a name="regional-availability"></a>Disponibilità a livello di area

L'aggiornamento di Plug and Play IoT (anteprima) è disponibile negli hub IoT creati nelle aree Stati Uniti centrali, Europa settentrionale e Giappone orientale.

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver letto la panoramica di Plug and Play IoT, come passaggio successivo consigliato è possibile provare alcune delle guide di avvio rapido:

- [Connettere un dispositivo all'hub IoT (C)](./quickstart-connect-device-c.md)
- [Interagire con un dispositivo dalla soluzione (Node.js)](./quickstart-service-node.md)

