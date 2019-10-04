---
title: Implementare l'individuazione del modello Plug and Play di anteprima | Microsoft Docs
description: Gli sviluppatori di soluzioni possono scoprire come implementare l'individuazione dei modelli Plug and Play nella soluzione.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 07/17/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: c37446fd5a0cdc986044405a9aa3da32462d9c04
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114268"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>Implementare l'individuazione del modello di Plug and Play di anteprima in una soluzione Internet delle cose

Questo articolo descrive come gli sviluppatori di soluzioni possono implementare l'individuazione del modello di Plug and Play di anteprima in una soluzione Internet delle cose.  Il Plug and Play di individuazione dei modelli è il modo in cui i dispositivi Plug and Play identificano i modelli e le interfacce di funzionalità supportate e il modo in cui una soluzione Internet delle cose recupera tali modelli e interfacce.

Sono disponibili due categorie generali di soluzioni Internet, ovvero soluzioni progettate per lo scopo che funzionano con un set noto di dispositivi Plug and Play e soluzioni basate su modelli che funzionano con qualsiasi dispositivo Plug and Play.

Questo articolo descrive come implementare l'individuazione del modello in entrambi i tipi di soluzione.

## <a name="model-discovery"></a>Individuazione dei modelli

Quando un utente Plug and Play dispositivo si connette per la prima volta all'hub Internet delle cose, invia un messaggio di telemetria per informazioni sul modello. Questo messaggio include gli ID delle interfacce implementate dal dispositivo. Affinché la soluzione funzioni con il dispositivo, è necessario risolvere gli ID e recuperare le definizioni per ciascuna interfaccia.

Di seguito sono riportati i passaggi necessari per un dispositivo Plug and Play quando usa il servizio Device provisioning (DPS) per connettersi a un hub:

1. Quando il dispositivo è acceso, si connette all'endpoint globale per DPS ed esegue l'autenticazione usando uno dei metodi consentiti.
1. DPS esegue quindi l'autenticazione del dispositivo e cerca la regola che indica a quale hub Internet è necessario assegnare il dispositivo. DPS registra quindi il dispositivo con tale hub.
1. DPS restituisce una stringa di connessione dell'hub Internet al dispositivo.
1. Il dispositivo invia quindi un messaggio di telemetria di individuazione all'hub Internet. Il messaggio di telemetria di individuazione contiene gli ID delle interfacce implementate dal dispositivo.
1. Il dispositivo Plug and Play è ora pronto per lavorare con una soluzione che usa l'hub Internet delle cose.

Se il dispositivo si connette direttamente all'hub Internet, si connette usando una stringa di connessione incorporata nel codice del dispositivo. Il dispositivo invia quindi un messaggio di telemetria di individuazione all'hub Internet.

Per ulteriori informazioni sul messaggio di telemetria delle informazioni sul modello, vedere l'interfaccia [ModelInformation](concepts-common-interfaces.md) .

### <a name="purpose-built-iot-solutions"></a>Soluzioni Internet delle cose basate su scopo

Una soluzione di Internet delle cose basata su uno scopo funziona con un set noto di Internet delle cose Plug and Play modelli e interfacce per le funzionalità dei dispositivi.

Il modello di funzionalità e le interfacce per i dispositivi che si connetteranno alla soluzione saranno disponibili in anticipo. Per preparare la soluzione, attenersi alla procedura seguente:

1. Archiviare i file JSON dell'interfaccia in Azure in una posizione in cui la soluzione è in grado di leggerli.
1. Scrivi la logica nella tua soluzione Internet delle cose in base ai modelli di capacità e all'interfaccia del Plug and Play.
1. Sottoscrivere le notifiche dall'hub di Internet delle cose usato dalla soluzione.

Quando si riceve una notifica per una nuova connessione del dispositivo, attenersi alla procedura seguente:

1. Leggere il messaggio di telemetria di individuazione per recuperare gli ID del modello di funzionalità e delle interfacce implementate dal dispositivo.
1. Confrontare l'ID del modello di funzionalità con gli ID dei modelli di funzionalità archiviati in anticipo.
1. A questo punto si conosce il tipo di dispositivo connesso. Usare la logica scritta in precedenza per consentire agli utenti di interagire con il dispositivo in modo appropriato.

### <a name="model-driven-solutions"></a>Soluzioni basate su modelli

Una soluzione di gestione delle cose basata su modelli può funzionare con qualsiasi Plug and Play dispositivo. La creazione di una soluzione basata su modelli di tipo Internet è più complessa, ma il vantaggio è che la soluzione funziona con tutti i dispositivi aggiunti in futuro.

Per creare una soluzione di Internet delle cose basata su modelli, è necessario compilare la logica rispetto alle primitive dell'interfaccia Plug and Play: telemetria, proprietà e comandi. La logica della soluzione Internet delle cose rappresenta un dispositivo combinando più funzionalità di telemetria, proprietà e comandi.

La soluzione deve anche sottoscrivere le notifiche dall'hub Internet it usato.

Quando la soluzione riceve una notifica per una nuova connessione a un dispositivo, seguire questa procedura:

1. Leggere il messaggio di telemetria di individuazione per recuperare gli ID del modello di funzionalità e delle interfacce implementate dal dispositivo.
1. Per ogni ID, leggere il file JSON completo per trovare le funzionalità del dispositivo.
1. Verificare che ogni interfaccia sia presente in tutte le cache create per archiviare i file JSON recuperati in precedenza dalla soluzione.
1. Quindi, controllare se un'interfaccia con tale ID è presente nel repository del modello pubblico. Per altre informazioni, vedere [repository di modelli pubblici](howto-manage-models.md).
1. Se l'interfaccia non è presente nel repository del modello pubblico, provare a cercarla in tutti i repository del modello aziendale noti alla soluzione. Per accedere a un repository del modello aziendale è necessaria una stringa di connessione. Per altre informazioni, vedere [repository del modello aziendale](howto-manage-models.md).
1. Se non è possibile trovare tutte le interfacce nel repository del modello pubblico o in un repository del modello aziendale, è possibile verificare se il dispositivo è in grado di fornire la definizione dell'interfaccia. Un dispositivo può implementare l'interfaccia [ModelDefinition](concepts-common-interfaces.md) standard per pubblicare informazioni su come recuperare i file di interfaccia con un comando.
1. Se sono stati trovati file JSON per ogni interfaccia implementata dal dispositivo, è possibile enumerare le funzionalità del dispositivo. Usare la logica scritta in precedenza per consentire agli utenti di interagire con il dispositivo.
1. In qualsiasi momento, è possibile chiamare l'API Digital Twins per recuperare l'ID del modello di funzionalità e gli ID di interfaccia per il dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come individuare una soluzione Internet delle cose, è possibile ottenere altre informazioni sulla [piattaforma Azure](overview-iot-plug-and-play.md) per sfruttare le altre funzionalità della soluzione.
