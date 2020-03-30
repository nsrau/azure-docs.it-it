---
title: Implementare l'individuazione del modello IoT Plug and Play Preview Documenti Microsoft
description: Gli sviluppatori di soluzioni sono sempre in grado di implementare l'individuazione del modello IoT Plug and Play nella soluzione.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/26/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 66da0321930ac38217a336380c9889963a433e67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75531361"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>Implementare l'individuazione del modello IoT Plug and Play Preview in una soluzione IoTImplement IoT Plug and Play Preview model discovery in an IoT solution

In questo articolo viene descritto come, come sviluppatore di soluzioni, è possibile implementare l'individuazione del modello IoT Plug and Play Preview in una soluzione IoT.This article describes how, as a solution developer, you can implement IoT Plug and Play Preview model discovery in an IoT solution.  L'individuazione dei modelli Plug and Play IoT è il modo in cui i dispositivi Plug and Play IoT identificano i modelli e le interfacce di funzionalità supportati e il modo in cui una soluzione IoT recupera tali modelli e interfacce di funzionalità.

Esistono due ampie categorie di soluzioni IoT: soluzioni appositamente progettate che funzionano con un set noto di dispositivi IoT Plug and Play e soluzioni basate su modelli che funzionano con qualsiasi dispositivo IoT Plug and Play.

In questo articolo di concetto viene descritto come implementare l'individuazione dei modelli in entrambi i tipi di soluzione.

## <a name="model-discovery"></a>Individuazione dei modelli

Quando un dispositivo IoT Plug and Play si connette per la prima volta all'hub IoT, invia un messaggio di telemetria delle informazioni sul modello. Questo messaggio include gli ID delle interfacce implementate dal dispositivo. Affinché la soluzione funzioni con il dispositivo, deve risolvere tali ID e recuperare le definizioni per ogni interfaccia.

Ecco i passaggi eseguiti da un dispositivo Plug and Play IoT quando usa il servizio Device Provisioning (DPS) per connettersi a un hub:

1. Quando il dispositivo è acceso, si connette all'end point globale per DPS ed esegue l'autenticazione utilizzando uno dei metodi consentiti.
1. DPS autentica quindi il dispositivo e cerca la regola che indica a quale hub IoT assegnare il dispositivo. DPS registra quindi il dispositivo con tale hub.
1. DPS restituisce una stringa di connessione dell'hub IoT al dispositivo.
1. Il dispositivo invia quindi un messaggio di telemetria di individuazione all'hub IoT.The device then sends a discovery telemetry message to your IoT Hub. Il messaggio di telemetria di individuazione contiene gli ID delle interfacce implementate dal dispositivo.
1. Il dispositivo IoT Plug and Play è ora pronto per funzionare con una soluzione che usa l'hub IoT.

Se il dispositivo si connette direttamente all'hub IoT, si connette usando una stringa di connessione incorporata nel codice del dispositivo. Il dispositivo invia quindi un messaggio di telemetria di individuazione all'hub IoT.The device then sends a discovery telemetry message to your IoT Hub.

Vedere l'interfaccia [ModelInformation](concepts-common-interfaces.md) per altre informazioni sul messaggio di telemetria delle informazioni sul modello.

### <a name="purpose-built-iot-solutions"></a>Soluzioni IoT appositamente progettate

Una soluzione IoT appositamente progettata funziona con un set noto di interfacce e modelli di funzionalità dei dispositivi IoT Plug and Play.

Avrai il modello di funzionalità e le interfacce per i dispositivi che si connetteranno alla tua soluzione in anticipo. Utilizzare la procedura seguente per preparare la soluzione:Use the following steps to prepare your solution:

1. Archiviare i file JSON dell'interfaccia in Azure in un percorso in cui la soluzione può leggerli.
1. Scrivere la logica nella soluzione IoT in base ai modelli di funzionalità Plug and Play IoT previsti e all'interfaccia.
1. Sottoscrivere le notifiche dall'hub IoT utilizzato dalla soluzione.

Quando si riceve una notifica per una nuova connessione del dispositivo, attenersi alla seguente procedura:

1. Leggere il messaggio di telemetria di individuazione per recuperare gli ID del modello di funzionalità e delle interfacce implementate dal dispositivo.
1. Confrontare l'ID del modello di funzionalità con gli ID dei modelli di funzionalità archiviati in anticipo.
1. Ora sai che tipo di dispositivo ha collegato. Utilizzare la logica scritta in precedenza per consentire agli utenti di interagire con il dispositivo in modo appropriato.

### <a name="model-driven-solutions"></a>Soluzioni basate su modelli

Una soluzione IoT basata su modelli può funzionare con qualsiasi dispositivo IoT Plug and Play. La creazione di una soluzione IoT basata su modelli è più complessa, ma il vantaggio è che la soluzione funziona con tutti i dispositivi aggiunti in futuro.

Per compilare una soluzione IoT basata su modelli, è necessario creare la logica in base alle primitive dell'interfaccia Plug and Play IoT: telemetria, proprietà e comandi. La logica della soluzione IoT rappresenta un dispositivo combinando più funzionalità di telemetria, proprietà e comandi.

La soluzione deve anche sottoscrivere le notifiche dall'hub IoT che usa.

Quando la soluzione riceve una notifica per una nuova connessione del dispositivo, attenersi alla seguente procedura:

1. Leggere il messaggio di telemetria di individuazione per recuperare gli ID del modello di funzionalità e delle interfacce implementate dal dispositivo.
1. Per ogni ID, leggere il file JSON completo per trovare le funzionalità del dispositivo.
1. Verifica se ogni interfaccia è presente in tutte le cache create per archiviare i file JSON recuperati in precedenza dalla soluzione.
1. Verificare quindi se nel repository dei modelli pubblici è presente un'interfaccia con tale ID. Per ulteriori informazioni, consultate [Repository di modelli pubblici.](howto-manage-models.md)
1. Se l'interfaccia non è presente nel repository dei modelli pubblici, provare a cercarla in tutti i repository di modelli aziendali noti alla soluzione. È necessaria una stringa di connessione per accedere a un repository di modelli aziendali. Per ulteriori informazioni, vedere [Repository dei modelli aziendali](howto-manage-models.md).
1. Se non è possibile trovare tutte le interfacce nel repository dei modelli pubblici o in un repository di modelli aziendali, è possibile verificare se il dispositivo è in grado di fornire la definizione dell'interfaccia. Un dispositivo può implementare l'interfaccia [ModelDefinition](concepts-common-interfaces.md) standard per pubblicare informazioni su come recuperare i file di interfaccia con un comando.
1. Se sono stati trovati file JSON per ogni interfaccia implementata dal dispositivo, è possibile enumerare le funzionalità del dispositivo. Usare la logica scritta in precedenza per consentire agli utenti di interagire con il dispositivo.
1. In qualsiasi momento, è possibile chiamare l'API gemelli digitale per recuperare l'ID del modello di funzionalità e gli ID di interfaccia per il dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso l'individuazione dei modelli di una soluzione IoT, scopri di più sulla [piattaforma IoT](overview-iot-plug-and-play.md) di Azure per sfruttare altre funzionalità per la soluzione.
