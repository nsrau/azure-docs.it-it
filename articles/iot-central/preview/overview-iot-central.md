---
title: Informazioni su Azure IoT Central | Microsoft Docs
description: Azure IoT Central è una piattaforma applicativa che semplifica la creazione di soluzioni IoT e contribuisce a ridurre il carico di lavoro e i costi delle operazioni di gestione e dello sviluppo IoT. Questo articolo offre una panoramica delle funzionalità di Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/12/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 8cfcbddfc8d50855860af655847f997fb2a01711
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74479709"
---
# <a name="what-is-azure-iot-central-preview-features"></a>Informazioni su Azure IoT Central (funzionalità di anteprima)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> Le funzionalità [Plug and Play IoT](../../iot-pnp/overview-iot-plug-and-play.md) incluse in Azure IoT Central sono attualmente disponibili in anteprima pubblica. Non usare un [modello di applicazione](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json) IoT Central abilitato per Plug and Play IoT per i carichi di lavoro di produzione. Per gli ambienti di produzione usare un'applicazione IoT Central creata da un [modello di applicazione](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json) corrente e disponibile a livello generale.

IoT Central è una piattaforma applicativa di Internet delle cose che riduce il carico di attività e i costi di sviluppo, gestione e manutenzione di soluzioni IoT di livello aziendale. La scelta di compilare con IoT Central consente di concentrare tempo, denaro ed energie per trasformare il business con dati IoT, anziché semplicemente gestire e aggiornare un'infrastruttura IoT complessa e in continua evoluzione.

L'interfaccia utente Web consente di monitorare le condizioni dei dispositivi, di creare regole e di gestire milioni di dispositivi e i relativi dati durante il ciclo di vita. Consente anche di agire sulle informazioni dettagliate sui dispositivi estendendo l'intelligence IoT alle applicazioni line-of-business.

Per IoT Central, questo articolo descrive:

- Descrizione degli utenti tipo associati a un progetto.
- Procedura per la creazione dell'applicazione.
- Procedura per la connessione di dispositivi all'applicazione.
- Procedura per la gestione dell'applicazione.
- Funzionalità Azure IoT Edge in IoT Central.
- Come connettere i dispositivi Azure IoT Edge in fase di esecuzione alla propria applicazione.

## <a name="known-issues"></a>Problemi noti

> [!Note]
> Questi problemi noti si riferiscono solo all'applicazione IoT Central (anteprima).

- Le regole non supportano tutte le azioni (solo la posta elettronica).
- Per i tipi complessi, le regole, le analisi e i gruppi di dispositivi non sono supportati.
- L'esportazione dati continua non supporta il formato Avro (incompatibilità).
- I dispositivi simulati non supportano tutti i tipi complessi.
- GeoJSON non è attualmente supportato.
- La tessera mappa non è attualmente supportato.
- I processi non supportano i tipi complessi.
- I tipi di schema della matrice non sono supportati.
- L'esportazione di modelli dell'applicazione e la copia di applicazioni non sono supportate.
- Sono supportati solo l'SDK per dispositivi C e gli SDK per dispositivi e servizi Node.js.
- È disponibile solo nelle località Stati Uniti e Europa.
- I modelli di funzionalità di dispositivo devono includere tutte le interfacce definite inline nello stesso file.

## <a name="personas"></a>Utenti tipo

Nella documentazione di IoT Central viene fatto riferimento a quattro utenti tipo che interagiscono con un'applicazione IoT Central:

- Un _generatore di soluzioni_ è responsabile della definizione dei tipi di dispositivi che si connettono all'applicazione e della personalizzazione dell'applicazione per l'operatore.
- Un _operatore_ gestisce i dispositivi connessi all'applicazione.
- Un _amministratore_ è responsabile di attività amministrative, ad esempio la gestione di [ruoli e autorizzazioni degli utenti](howto-administer.md) nell'applicazione.
- Uno _sviluppatori di dispositivi_ crea il codice che viene eseguito in un dispositivo o in un modulo IoT Edge connesso all'applicazione.

## <a name="create-your-iot-central-application"></a>Creare l'applicazione IoT Central

I generatori di soluzioni usano IoT Central per creare una soluzione IoT ospitata nel cloud e personalizzata per l'organizzazione. Una soluzione IoT personalizzata è in genere costituita da:

- Un'applicazione basata sul cloud che riceve i dati di telemetria dai dispositivi e consente di gestire tali dispositivi.
- Più dispositivi che eseguono codice personalizzato connessi all'applicazione basata sul cloud.

È possibile distribuire rapidamente una nuova applicazione IoT Central e quindi personalizzarla in base a requisiti specifici nel browser. I generatori di soluzioni possono usare gli strumenti basati sul Web per creare un _modello di dispositivo_ per i dispositivi che si connettono all'applicazione. Un modello di dispositivo è il progetto che definisce le caratteristiche e il comportamento di un tipo di dispositivo, ad esempio:

- I dati di telemetrica che invia.
- Le proprietà di business modificabili da un operatore.
- Le proprietà del dispositivo impostate da un dispositivo che sono di sola lettura nell'applicazione.
- Le proprietà, impostate da un operatore, che determinano il comportamento del dispositivo.

Questo modello di dispositivo include:

- Un _modello di funzionalità di dispositivo_ in cui sono descritte le funzionalità che un dispositivo deve implementare, ad esempio i dati di telemetria inviati e le proprietà segnalate.
- Proprietà cloud non archiviate nel dispositivo.
- Personalizzazioni, dashboard e moduli che fanno parte dell'applicazione IoT Central.

### <a name="create-device-templates"></a>Creare modelli di dispositivo

[Plug and Play IoT](../../iot-pnp/overview-iot-plug-and-play.md) consente a IoT Central di integrare dispositivi senza dover scrivere il codice del dispositivo incorporato. La parte centrale di Plug and Play IoT è costituita da uno schema del modello di funzionalità di dispositivo che descrive le funzionalità dei dispositivi. In un'applicazione IoT Central (anteprima) i modelli di dispositivo usano questi modelli di funzionalità di dispositivo di Plug and Play IoT.

I generatori di soluzioni hanno a disposizione numerose opzioni per creare modelli di dispositivo:

- Progettare il modello di dispositivo in IoT Central e quindi implementarne il modello di funzionalità di dispositivo nel codice del dispositivo.
- Importare un modello di funzionalità di dispositivo dal [catalogo dei dispositivi Azure Certified per IoT](https://aka.ms/iotdevcat) e quindi aggiungere eventuali personalizzazioni, dashboard e proprietà cloud richiesti dell'applicazione IoT Central.
- Creare un modello di funzionalità di dispositivo con Visual Studio Code. Implementare il codice del dispositivo dal modello e connettere il dispositivo all'applicazione IoT Central. IoT Central trova il modello di funzionalità di dispositivo da un repository e crea automaticamente un modello di dispositivo semplice.
- Creare un modello di funzionalità di dispositivo con Visual Studio Code. Implementare il codice del dispositivo dal modello. Importare manualmente il modello di funzionalità di dispositivo nell'applicazione IoT Central e quindi aggiungere eventuali personalizzazioni, dashboard e proprietà cloud richiesti dell'applicazione IoT Central.

I generatori di soluzioni possono usare IoT Central per generare codice per dispositivi di test utili per convalidare i modelli di dispositivo.

### <a name="customize-the-ui"></a>Personalizzare l'interfaccia utente

I generatori di soluzioni possono anche personalizzare l'interfaccia utente dell'applicazione IoT Central per gli operatori responsabili dell'uso quotidiano dell'applicazione. Le personalizzazioni che possono essere apportate da un generatore di soluzioni includono le seguenti:

- Definizione del layout di proprietà e impostazioni in un modello di dispositivo.
- Configurazione di dashboard personalizzati per consentire agli operatori di individuare informazioni dettagliate e risolvere i problemi più velocemente.
- Configurazione di analisi personalizzate per esplorare i dati della serie temporale dai dispositivi connessi.

## <a name="connect-your-devices"></a>Connettere i dati dell'utente

Dopo che il generatore ha definito i tipi di dispositivi che possono connettersi all'applicazione, uno sviluppatore di dispositivi crea il codice da eseguire nei dispositivi. Gli sviluppatori di dispositivi possono usare gli [SDK di Azure IoT](https://github.com/Azure/azure-iot-sdks) open source di Microsoft per creare il codice del dispositivo. Questi SDK supportano un numero di linguaggi, piattaforme e protocolli tale da soddisfare le esigenze di connessione dei dispositivi all'applicazione IoT Central. Gli SDK consentono di implementare le funzionalità di dispositivo seguenti:

- Creare una connessione sicura.
- Inviare i dati di telemetria.
- Creare un report sullo stato.
- Ricevere gli aggiornamenti della configurazione.

Per altre informazioni, vedere il post di blog [Benefits of using the Azure IoT SDKs, and pitfalls to avoid if you don't](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) (Vantaggi dell'uso di SDK di Azure IoT e problemi da evitare se non vengono usati).

### <a name="azure-iot-edge-devices"></a>Dispositivi Azure IoT Edge

Oltre ai dispositivi creati tramite [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks), è anche possibile connettere [dispositivi Azure IoT Edge](../../iot-edge/about-iot-edge.md) a un'applicazione IoT Central. Azure IoT Edge consente di eseguire funzioni di intelligence e logica personalizzata direttamente nei dispositivi IoT gestiti da IoT Central. Il runtime di IoT Edge consente di:

- Installare e aggiornare i carichi di lavoro nel dispositivo.
- Mantenere gli standard di sicurezza di Azure IoT Edge sul dispositivo.
- Assicurare che i moduli di IoT Edge siano sempre in esecuzione.
- Segnalare l'integrità dei moduli al cloud per il monitoraggio remoto.
- Gestire la comunicazione tra i dispositivi foglia downstream e un dispositivo IoT Edge, tra i moduli in un dispositivo IoT Edge e tra un dispositivo IoT Edge e il cloud.

Per altre informazioni, vedere [Dispositivi Azure IoT Edge e IoT Central](./concepts-architecture.md#azure-iot-edge-devices).

## <a name="manage-your-application"></a>Gestire l'applicazione

Le applicazioni IoT Central sono completamente ospitate in Microsoft e ciò consente di ridurre il carico amministrativo correlato alla gestione delle applicazioni.

Gli operatori usano l'applicazione IoT Central per gestire i dispositivi nella soluzione IoT Central. Gli operatori eseguono attività quali:

- Eseguire il monitoraggio dei dispositivi connessi all'applicazione.
- Risolvere e correggere i problemi relativi ai dispositivi.
- Eseguire il provisioning di nuovi dispositivi.

Un generatore di soluzioni può definire regole e azioni personalizzate che operano sullo streaming di dati dai dispositivi connessi. Un operatore può abilitare o disabilitare queste regole a livello di dispositivo per controllare e automatizzare le attività all'interno dell'applicazione.

Gli amministratori gestiscono l'accesso all'applicazione con [regole e autorizzazioni utente](howto-administer.md).

## <a name="quotas"></a>Quote

Le quote predefinite di ogni sottoscrizione di Azure possono influire sull'ambito della soluzione IoT. Attualmente, IoT Central limita il numero di applicazioni distribuibili in una sottoscrizione a 10. Se è necessario aumentare questo limite, contattare il [supporto tecnico Microsoft](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver letto la panoramica di IoT Central, vengono consigliati alcuni passaggi successivi:

- Comprendere le differenze tra gli [acceleratori di soluzioni IoT Central e Azure IoT](../core/overview-iot-options.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json).
- Acquisire familiarità con l'[interfaccia utente di Azure IoT Central](overview-iot-central-tour.md).
- Iniziare a [creare un'applicazione Azure IoT Central](quick-deploy-iot-central.md).
- Altre informazioni su [Plug and Play IoT](../../iot-pnp/overview-iot-plug-and-play.md)
- Altre informazioni su come [creare un modello di dispositivo Azure IoT Edge](./tutorial-define-edge-device-type.md)
