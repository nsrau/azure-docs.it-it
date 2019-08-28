---
title: Informazioni su Azure IoT Central | Microsoft Docs
description: Azure IoT Central è una soluzione SaaS end-to-end che è possibile usare per compilare e gestire una soluzione IoT personalizzata. Questo articolo offre una panoramica delle funzionalità di Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 0675be988e7e9560560dd07338563c9fa22fcb4c
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69881650"
---
# <a name="what-is-azure-iot-central-preview-features"></a>Informazioni su Azure IoT Central (funzionalità di anteprima)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> Le funzionalità [Plug and Play IoT](https://aka.ms/iot-pnp-docs) incluse in Azure IoT Central sono attualmente disponibili in anteprima pubblica. Non usare per i carichi di lavoro di produzione un'applicazione IoT Central abilitata per Plug and Play IoT. Per gli ambienti di produzione usare un'applicazione IoT Central creata da un modello di applicazione corrente e disponibile a livello generale.

Azure IoT Central è una soluzione SaaS (Software-as-a-Service) IoT completamente gestita che semplifica la creazione di prodotti che si connettono ai mondi fisico e digitale. Per realizzare prodotti connessi, è possibile:

- Derivare nuove informazioni dettagliate dai dispositivi connessi allo scopo di migliorare prodotti ed esperienze per i clienti.
- Creare nuove opportunità di business per l'organizzazione.

Rispetto a un progetto IoT tipico, Azure IoT Central:

- Riduce il carico di gestione.
- Riduce costi generali e costi operativi.
- Semplifica la personalizzazione dell'applicazione e al contempo sfrutta:
  - Tecnologie leader del settore come [Hub IoT di Azure](https://azure.microsoft.com/services/iot-hub/) e [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/).
  - Funzionalità di sicurezza di livello aziendale, come la crittografia end-to-end.

Il video seguente offre una panoramica di Azure IoT Central:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Microsoft-IoT-Central-intro-walkthrough/Player]

Questo articolo fornisce le informazioni seguenti su Azure IoT Central:

- Descrizione degli utenti tipo associati a un progetto.
- Procedura per la creazione dell'applicazione.
- Procedura per la connessione di dispositivi all'applicazione.
- Procedura per la gestione dell'applicazione.

## <a name="known-issues"></a>Problemi noti

> [!Note]
> Questi problemi noti si riferiscono solo all'applicazione IoT Central Preview.

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
- È disponibile solo in aree selezionate.

## <a name="personas"></a>Utenti tipo

Nella documentazione di Azure IoT Central viene fatto riferimento a quattro utenti tipo che interagiscono con un'applicazione Azure IoT Central:

- Un _generatore_ è responsabile della definizione dei tipi di dispositivi che si connettono all'applicazione e della personalizzazione dell'applicazione per l'operatore.
- Un _operatore_ gestisce i dispositivi connessi all'applicazione.
- Un _amministratore_ è responsabile delle attività amministrative, ad esempio la gestione di utenti e ruoli nell'applicazione.
- Uno _sviluppatore di dispositivi_ crea il codice che viene eseguito in un dispositivo connesso all'applicazione.

## <a name="create-your-azure-iot-central-application"></a>Creare l'applicazione Azure IoT Central

I generatori usano Azure IoT Central per creare una soluzione IoT ospitata nel cloud e personalizzata per l'organizzazione. Una soluzione IoT personalizzata è in genere costituita da:

- Un'applicazione basata sul cloud che riceve i dati di telemetria dai dispositivi e consente di gestire tali dispositivi.
- Più dispositivi che eseguono codice personalizzato connessi all'applicazione basata sul cloud.

È possibile distribuire rapidamente una nuova applicazione Azure IoT Central e quindi personalizzarla in base a requisiti specifici nel browser. I generatori possono usare gli strumenti basati sul Web per creare un _modello di dispositivo_ per i dispositivi che si connettono all'applicazione. Un modello di dispositivo è il progetto che definisce le caratteristiche e il comportamento di un tipo di dispositivo, ad esempio:

- I dati di telemetrica che invia.
- Le proprietà di business modificabili da un operatore.
- Le proprietà del dispositivo impostate da un dispositivo che sono di sola lettura nell'applicazione.
- Proprietà impostate da un operatore che determinano il comportamento del dispositivo.

Questo modello di dispositivo include:

- Un _modello di funzionalità di dispositivo_ in cui sono descritte le funzionalità che un dispositivo deve implementare, ad esempio i dati di telemetria inviati e le proprietà segnalate.
- Proprietà cloud non archiviate nel dispositivo.
- Personalizzazioni, dashboard e moduli che fanno parte dell'applicazione IoT Central.

### <a name="create-device-templates"></a>Creare modelli di dispositivo

[Plug and Play IoT](https://aka.ms/iot-pnp-docs) consente a IoT Central di integrare dispositivi senza dover scrivere il codice del dispositivo incorporato. La parte centrale di Plug and Play IoT Internet è costituita da uno schema del modello di funzionalità di dispositivo che descrive le funzionalità dei dispositivi. In un'applicazione IoT Central (anteprima) i modelli di dispositivo usano questi modelli di funzionalità di dispositivo di Plug and Play IoT.

I generatori hanno a disposizione numerose opzioni per creare modelli di dispositivo:

- Progettare il modello di dispositivo in IoT Central e quindi implementarne il modello di funzionalità di dispositivo nel codice del dispositivo.
- Importare un modello di funzionalità di dispositivo dal [catalogo dei dispositivi Azure Certified per IoT](https://aka.ms/iotdevcat) e quindi aggiungere eventuali personalizzazioni, dashboard e proprietà cloud richiesti dell'applicazione IoT Central.
- Creare un modello di funzionalità di dispositivo con Visual Studio Code. Implementare il codice del dispositivo dal modello e connettere il dispositivo all'applicazione IoT Central. IoT Central trova il modello di funzionalità di dispositivo da un repository e crea automaticamente un modello di dispositivo semplice.
- Creare un modello di funzionalità di dispositivo con Visual Studio Code. Implementare il codice del dispositivo dal modello. Importare manualmente il modello di funzionalità di dispositivo nell'applicazione IoT Central e quindi aggiungere eventuali personalizzazioni, dashboard e proprietà cloud richiesti dell'applicazione IoT Central.

I generatori possono usare IoT Central per generare codice per dispositivi di test utili per convalidare i modelli di dispositivo.

### <a name="customize-the-ui"></a>Personalizzare l'interfaccia utente

I generatori possono anche personalizzare l'interfaccia utente dell'applicazione Azure IoT Central per gli operatori che sono responsabili dell'uso quotidiano dell'applicazione. Le personalizzazioni che un generatore può apportare includono:

- Definizione del layout di proprietà e impostazioni in un modello di dispositivo.
- Configurazione di dashboard personalizzati per consentire agli operatori di individuare informazioni dettagliate e risolvere i problemi più velocemente.
- Configurazione di analisi personalizzate per esplorare i dati della serie temporale dai dispositivi connessi.

## <a name="connect-your-devices"></a>Connettere i dati dell'utente

Dopo che il generatore ha definito i tipi di dispositivi che possono connettersi all'applicazione, uno sviluppatore di dispositivi crea il codice da eseguire nei dispositivi. Gli sviluppatori di dispositivi possono usare gli [SDK di Azure IoT](https://github.com/Azure/azure-iot-sdks) open source di Microsoft per creare il codice del dispositivo. Questi SDK supportano un numero di linguaggi, piattaforme e protocolli tale da soddisfare le esigenze di connessione dei dispositivi all'applicazione Azure IoT Central. Gli SDK consentono di implementare le funzionalità di dispositivo seguenti:

- Creare una connessione sicura.
- Inviare i dati di telemetria.
- Creare un report sullo stato.
- Ricevere gli aggiornamenti della configurazione.

Per altre informazioni, vedere il post di blog [Benefits of using the Azure IoT SDKs, and pitfalls to avoid if you don't](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) (Vantaggi dell'uso di SDK di Azure IoT e problemi da evitare se non vengono usati).

## <a name="manage-your-application"></a>Gestire l'applicazione

Le applicazioni Azure IoT Central sono completamente ospitate da Microsoft e questo consente di ridurre il carico amministrativo correlato alla gestione delle applicazioni.

Gli operatori usano l'applicazione Azure IoT Central per gestire i dispositivi nella soluzione Azure IoT Central. Gli operatori eseguono attività quali:

- Eseguire il monitoraggio dei dispositivi connessi all'applicazione.
- Risolvere e correggere i problemi relativi ai dispositivi.
- Eseguire il provisioning di nuovi dispositivi.

Un generatore può definire regole e azioni personalizzate che operano sullo streaming di dati dai dispositivi connessi. Un operatore può abilitare o disabilitare queste regole a livello di dispositivo per controllare e automatizzare le attività all'interno dell'applicazione.

Gli amministratori gestiscono l'accesso all'applicazione con [regole e autorizzazioni utente](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver letto la panoramica di Azure IoT Central, ecco alcuni passaggi successivi consigliati:

- Comprendere le differenze tra gli [acceleratori di soluzioni Azure IoT Central e Azure IoT Suite](overview-iot-options.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
- Acquisire familiarità con l'[interfaccia utente di Azure IoT Central](overview-iot-central-tour-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
- Iniziare a [creare un'applicazione Azure IoT Central](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
- Seguire una sequenza di esercitazioni che mostrano come eseguire le attività seguenti:
  - [Come generatore, creare un modello di dispositivo](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [Come generatore, aggiungere regole per automatizzare la soluzione](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [Come operatore, eseguire il monitoraggio dei dispositivi](tutorial-monitor-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [Come operatore, aggiungere un dispositivo alla soluzione](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
- Altre informazioni su [Plug and Play IoT](https://aka.ms/iot-pnp-docs)
