---
title: Concetti dell'architettura di Azure IoT Central | Microsoft Docs
description: Questo articolo presenta i concetti fondamentali relativi all'architettura di Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 11/12/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 66792d9d0a8b1cd72ef8f22481016a35f37a1597
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013893"
---
# <a name="azure-iot-central-architecture-preview-features"></a>Architettura del IoT Central di Azure (funzionalità di anteprima)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Questo articolo presenta una panoramica dell'architettura di Microsoft Azure IoT Central.

![Architettura di primo livello](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Dispositivi

I dispositivi scambiano dati con l'applicazione Azure IoT Central. Un dispositivo può:

- Inviare misure, ad esempio dati di telemetria.
- Sincronizzare le impostazioni con l'applicazione.

In Azure IoT Central i dati che un dispositivo può scambiare con l'applicazione sono specificati in un modello di dispositivo. Per altre informazioni sui modelli di dispositivo, vedere [Gestione dei metadati](#metadata-management).

Per altre informazioni sul modo in cui i dispositivi si connettono all'applicazione Azure IoT Central, vedere [Connettività dei dispositivi](overview-iot-central-get-connected.md).

## <a name="azure-iot-edge-devices"></a>Dispositivi Azure IoT Edge

Oltre ai dispositivi creati usando gli [SDK Azure](https://github.com/Azure/azure-iot-sdks), è anche possibile connettere [Azure IOT Edge dispositivi](../../iot-edge/about-iot-edge.md) a un'applicazione IoT Central. IoT Edge consente di eseguire l'Intelligence per il cloud e la logica personalizzata direttamente nei dispositivi Internet delle cose gestite da IoT Central. Il runtime di IoT Edge consente di:

- Installare e aggiornare i carichi di lavoro nel dispositivo.
- Mantenere IoT Edge gli standard di sicurezza sul dispositivo.
- Assicurare che i moduli di IoT Edge siano sempre in esecuzione.
- Segnalare l'integrità dei moduli al cloud per il monitoraggio remoto.
- Gestire la comunicazione tra i dispositivi foglia downstream e un dispositivo IoT Edge, tra i moduli in un dispositivo IoT Edge e tra un dispositivo IoT Edge e il cloud.

![Azure IoT Central con Azure IoT Edge](./media/concepts-architecture/iotedge.png)

IoT Central Abilita le funzionalità seguenti a per i dispositivi IoT Edge:

- Modelli di dispositivo per descrivere le funzionalità di un dispositivo IoT Edge, ad esempio:
  - Funzionalità di caricamento del manifesto di distribuzione, che consente di gestire un manifesto per una flotta di dispositivi.
  - Moduli in esecuzione nel dispositivo IoT Edge.
  - I dati di telemetria inviati da ogni modulo.
  - Proprietà che ogni modulo segnala.
  - Comandi a cui ogni modulo risponde.
  - Relazioni tra un modello di funzionalità del dispositivo gateway IoT Edge e un modello di capacità del dispositivo downstream.
  - Proprietà del cloud che non sono archiviate nel dispositivo IoT Edge.
  - Personalizzazioni, dashboard e moduli che fanno parte dell'applicazione IoT Central.

  Per ulteriori informazioni, vedere l'esercitazione [creare un modello di dispositivo IOT Edge](./tutorial-define-edge-device-type.md) .

- La possibilità di effettuare il provisioning di dispositivi IoT Edge su larga scala usando il servizio Device provisioning di Azure
- Regole e azioni.
- Dashboard e analisi personalizzati.
- Esportazione continua dei dati di telemetria da dispositivi IoT Edge.

### <a name="iot-edge-device-types"></a>Tipi di dispositivi IoT Edge

IoT Central classifica IoT Edge tipi di dispositivo come segue:

- Dispositivi foglia. Un dispositivo IoT Edge può avere dispositivi foglia a valle, ma questi dispositivi non vengono sottoposti a provisioning in IoT Central.
- Dispositivi gateway con dispositivi downstream. Il provisioning del dispositivo gateway e dei dispositivi downstream viene eseguito in IoT Central

![IoT Central con IoT Edge Panoramica](./media/concepts-architecture/gatewayedge.png)

### <a name="iot-edge-patterns"></a>Modelli di IoT Edge

IoT Central supporta i modelli di dispositivo IoT Edge seguenti:

#### <a name="iot-edge-as-leaf-device"></a>IoT Edge come dispositivo foglia

![IoT Edge come dispositivo foglia](./media/concepts-architecture/edgeasleafdevice.png)

Il provisioning del dispositivo IoT Edge viene eseguito in IoT Central e tutti i dispositivi downstream e i relativi dati di telemetria sono rappresentati come provenienti dal dispositivo IoT Edge. Non viene effettuato il provisioning dei dispositivi downstream connessi al dispositivo IoT Edge in IoT Central.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity"></a>IoT Edge dispositivo gateway connesso ai dispositivi downstream con identità

![IoT Edge con l'identità del dispositivo downstream](./media/concepts-architecture/edgewithdownstreamdeviceidentity.png)

Il provisioning del dispositivo IoT Edge viene eseguito in IoT Central insieme ai dispositivi downstream collegati al dispositivo IoT Edge. Il supporto di runtime per il provisioning di dispositivi downstream tramite il gateway non è attualmente supportato.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity-provided-by-the-iot-edge-gateway"></a>IoT Edge dispositivo gateway connesso ai dispositivi downstream con identità fornita dal gateway di IoT Edge

![IoT Edge con dispositivo downstream senza identità](./media/concepts-architecture/edgewithoutdownstreamdeviceidentity.png)

Il provisioning del dispositivo IoT Edge viene eseguito in IoT Central insieme ai dispositivi downstream collegati al dispositivo IoT Edge. Il supporto di runtime del gateway che fornisce l'identità ai dispositivi downstream e il provisioning di dispositivi downstream non è attualmente supportato. Se si usa un modulo di conversione delle identità personalizzato, IoT Central possibile supportare questo modello.

## <a name="cloud-gateway"></a>Gateway cloud

Azure IoT Central usa l'hub IoT come gateway del cloud che permette la connettività dei dispositivi. L'hub IoT consente:

- Inserimento dati su larga scala nel cloud.
- Gestione di dispositivi.
- Connettività dei dispositivi sicura.

Per altre informazioni sull'hub IoT, vedere [Documentazione sull'hub IoT](https://docs.microsoft.com/azure/iot-hub/).

Per altre informazioni sulla connettività dei dispositivi in Azure IoT Central, vedere [Connettività dei dispositivi](overview-iot-central-get-connected.md).

## <a name="data-stores"></a>Archivi dati

Azure IoT Central archivia i dati dell'applicazione nel cloud. I dati archiviati dell'applicazione comprendono:

- Modelli di dispositivo.
- Identità dei dispositivi.
- Metadati dei dispositivi.
- Dati di utenti e ruoli.

Azure IoT Central usa un archivio di serie temporali per i dati di misura inviati dai dispositivi. Dati di serie temporali dei dispositivi utilizzati dal servizio di analisi.

## <a name="analytics"></a>Analisi

Il servizio di analisi è responsabile della generazione dei dati dei report personalizzati mostrati dall'applicazione. Un operatore può [personalizzare le analisi](howto-create-analytics.md) visualizzate nell'applicazione. Il servizio di analisi si basa su [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) ed elabora i dati di misura inviati dai dispositivi.

## <a name="rules-and-actions"></a>Regole e azioni

[Regole e azioni](tutorial-create-telemetry-rules.md) interagiscono strettamente per automatizzare le attività all'interno dell'applicazione. Un costruttore può definire regole basate su dati di telemetria dei dispositivi, ad esempio la temperatura che supera una soglia definita. Azure IoT Central usa un elaboratore di flusso per determinare quando sono soddisfatte le condizioni della regola. Quando una condizione della regola è soddisfatta, attiva un'azione definita dal costruttore. Ad esempio, un'azione può inviare un'e-mail per notificare a un tecnico che la temperatura di un dispositivo è troppo elevata.

## <a name="metadata-management"></a>Gestione dei metadati

I modelli di dispositivo in un'applicazione Azure IoT Central definiscono il comportamento e le funzionalità dei tipi di dispositivo. Ad esempio, un modello di dispositivo frigorifero specifica i dati di telemetria che il frigorifero invia all'applicazione.

![Architettura dei modelli](media/concepts-architecture/template-architecture.png)

In un modello IoT Central anteprima dispositivo applicazione:

- I **modelli di funzionalità del dispositivo** specificano le funzionalità di un dispositivo, ad esempio i dati di telemetria inviati, le proprietà che definiscono lo stato del dispositivo e i comandi a cui risponde il dispositivo. Le funzionalità del dispositivo sono organizzate in una o più interfacce. Per ulteriori informazioni sui modelli di funzionalità del dispositivo, vedere la documentazione relativa ai [plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) .
- Le **proprietà del cloud** specificano le proprietà IOT Central archivi per un dispositivo. Queste proprietà vengono archiviate solo in IoT Central e non vengono mai inviate a un dispositivo.
- Le **visualizzazioni** specificano i dashboard e i moduli creati dal generatore per consentire all'operatore di monitorare e gestire i dispositivi.
- Le **personalizzazioni** consentono al generatore di eseguire l'override di alcune definizioni nel modello di funzionalità del dispositivo per renderle più rilevanti per l'applicazione IoT Central.

Un'applicazione può avere uno o più dispositivi simulati e reali basati su ogni modello di dispositivo.

## <a name="data-export"></a>Esportazione dati

In un'applicazione IoT Central di Azure è possibile [esportare i dati](howto-export-data.md) in modo continuo nelle istanze di hub eventi di Azure e del bus di servizio di Azure. È anche possibile esportare periodicamente i dati nell'account di archiviazione BLOB di Azure. IoT Central possibile esportare misure, dispositivi e modelli di dispositivo.

## <a name="batch-device-updates"></a>Aggiornamenti del dispositivo batch

In un'applicazione IoT Central di Azure è possibile [creare ed eseguire processi](../core/howto-run-a-job.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json) per gestire i dispositivi connessi. Questi processi consentono di eseguire aggiornamenti in blocco per le proprietà o le impostazioni del dispositivo o di eseguire comandi. Ad esempio, è possibile creare un processo per aumentare la velocità della ventola per più computer distributori frigoriferi.

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
Un amministratore può personalizzare l'interfaccia utente dell'applicazione applicando temi personalizzati e modificando i collegamenti della Guida in modo che puntino alle risorse della Guida personalizzate. Per altre informazioni sulla personalizzazione dell'interfaccia utente, vedere [l'articolo personalizzare l'interfaccia utente di Azure IOT Central](howto-customize-ui.md) .

Un operatore può creare dashboard di applicazione personalizzati. È possibile avere diversi dashboard che visualizzano dati diversi e passano tra loro.

## <a name="next-steps"></a>Passaggi successivi

Ora che sono state apprese le informazioni sull'architettura di Azure IoT Central, il passaggio successivo suggerito consiste nell'ottenere informazioni sulla [connettività dei dispositivi](overview-iot-central-get-connected.md) in Azure IOT Central.