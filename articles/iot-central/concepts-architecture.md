---
title: Concetti dell'architettura di Azure IoT Central | Microsoft Docs
description: Questo articolo presenta i concetti fondamentali relativi all'architettura di Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 4bc9a79576c3165585a4a2c897bd41bfb77c080c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66693142"
---
# <a name="azure-iot-central-architecture"></a>Architettura di Azure IoT Central

Questo articolo presenta una panoramica dell'architettura di Microsoft Azure IoT Central.

![Architettura di primo livello](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Dispositivi

I dispositivi scambiano dati con l'applicazione Azure IoT Central. Un dispositivo può:

- Inviare misure, ad esempio dati di telemetria.
- Sincronizzare le impostazioni con l'applicazione.

In Azure IoT Central i dati che un dispositivo può scambiare con l'applicazione sono specificati in un modello di dispositivo. Per altre informazioni sui modelli di dispositivo, vedere [Gestione dei metadati](#metadata-management).

Per altre informazioni sul modo in cui i dispositivi si connettono all'applicazione Azure IoT Central, vedere [Connettività dei dispositivi](concepts-connectivity.md).

## <a name="cloud-gateway"></a>Gateway cloud

Azure IoT Central usa l'hub IoT come gateway del cloud che permette la connettività dei dispositivi. L'hub IoT consente:

- Inserimento dati su larga scala nel cloud.
- Gestione di dispositivi.
- Connettività dei dispositivi sicura.

Per altre informazioni sull'hub IoT, vedere [Documentazione sull'hub IoT](https://docs.microsoft.com/azure/iot-hub/).

Per altre informazioni sulla connettività dei dispositivi in Azure IoT Central, vedere [Connettività dei dispositivi](concepts-connectivity.md).

## <a name="data-stores"></a>Archivi dati

Azure IoT Central archivia i dati dell'applicazione nel cloud. I dati archiviati dell'applicazione comprendono:

- Modelli di dispositivo.
- Identità dei dispositivi.
- Metadati dei dispositivi.
- Dati di utenti e ruoli.

Azure IoT Central usa un archivio di serie temporali per i dati di misura inviati dai dispositivi. Dati di serie temporali dei dispositivi utilizzati dal servizio di analisi.

## <a name="analytics"></a>Analytics

Il servizio di analisi è responsabile della generazione dei dati dei report personalizzati mostrati dall'applicazione. Un operatore può [personalizzare le analisi](howto-create-analytics.md) visualizzate nell'applicazione. Il servizio di analisi si basa su [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) ed elabora i dati di misura inviati dai dispositivi.

## <a name="rules-and-actions"></a>Regole e azioni

[Regole e azioni](howto-create-telemetry-rules.md) interagiscono strettamente per automatizzare le attività all'interno dell'applicazione. Un costruttore può definire regole basate su dati di telemetria dei dispositivi, ad esempio la temperatura che supera una soglia definita. Azure IoT Central usa un elaboratore di flusso per determinare quando sono soddisfatte le condizioni della regola. Quando una condizione della regola è soddisfatta, attiva un'azione definita dal costruttore. Ad esempio, un'azione può inviare un'e-mail per notificare a un tecnico che la temperatura di un dispositivo è troppo elevata.

## <a name="metadata-management"></a>Gestione dei metadati

I modelli di dispositivo in un'applicazione Azure IoT Central definiscono il comportamento e le funzionalità dei tipi di dispositivo. Ad esempio, un modello di dispositivo frigorifero specifica i dati di telemetria che il frigorifero invia all'applicazione.

![Architettura dei modelli](media/concepts-architecture/template_architecture.png)

Un modello di dispositivo:

- Le **misure** specificano i dati di telemetria che il dispositivo invia all'applicazione.
- Le **impostazioni** specificano le configurazioni che un operatore può impostare.
- Le **proprietà** specificano i metadati che un operatore può impostare.
- Le **regole** automatizzano il comportamento nell'applicazione in base ai dati inviati da un dispositivo.
- I **dashboard** sono visualizzazioni personalizzabili di un dispositivo nell'applicazione.

Un'applicazione può avere uno o più dispositivi simulati e reali basati su ogni modello di dispositivo.

## <a name="data-export"></a>Esportazione dei dati

In un'applicazione Azure IoT Central, è possibile [esportare continuamente dati](howto-export-data-event-hubs-service-bus.md) proprio hub eventi di Azure e le istanze del Bus di servizio di Azure. È possibile esportare anche periodicamente i dati all'account di archiviazione Blob di Azure. IoT Central è possibile esportare le misurazioni, dispositivi e modelli di dispositivo.

## <a name="batch-device-updates"></a>Aggiornamenti del dispositivo batch

In un'applicazione Azure IoT Central, è possibile [creare ed eseguire processi](howto-run-a-job.md) per gestire i dispositivi connessi. Questi processi consentono di aggiornamenti per le impostazioni o le proprietà dei dispositivi in blocco oppure eseguire i comandi. Ad esempio, è possibile creare un processo per aumentare la velocità della ventola per più frigoriferi distributori automatici.

## <a name="role-based-access-control-rbac"></a>Controllo degli accessi in base al ruolo

Un [amministratore può definire le regole di accesso](howto-administer.md) per un'applicazione Azure IoT Central tramite i ruoli predefiniti. Un amministratore può assegnare gli utenti ai ruoli determinando a quali aree dell'applicazione l'utente può accedere.

## <a name="security"></a>Security

Le funzionalità di sicurezza all'interno di Azure IoT Central includono:

- I dati sono crittografati quando sono in transito o inattivi.
- L'autenticazione viene fornita tramite Azure Active Directory o l'account Microsoft. È supportata l'autenticazione a due fattori.
- Isolamento del tenant completo.
- Sicurezza a livello di dispositivo.

## <a name="ui-shell"></a>Shell dell'interfaccia utente

La shell dell'interfaccia utente è un'applicazione browser HTML5 moderna e reattiva.
Un amministratore può personalizzare l'interfaccia utente dell'applicazione, applicazione di temi personalizzati e modificando i collegamenti della Guida in modo da puntare alle risorse di file della Guida personalizzato. Per altre informazioni sulla personalizzazione dell'interfaccia utente, vedere [personalizzare Azure IoT Central UI](howto-customize-ui.md) articolo.

Un operatore può creare dashboard applicazioni personalizzate. È possibile avere diversi dashboard che visualizzano dati diversi e spostarsi tra le due.

## <a name="next-steps"></a>Passaggi successivi

Ora che si sono appresi l'architettura di Azure IoT Central, il passaggio successivo consigliato sia per apprendere [connettività dei dispositivi](concepts-connectivity.md) in Azure IoT Central.