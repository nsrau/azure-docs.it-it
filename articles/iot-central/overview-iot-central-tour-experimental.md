---
title: Presentazione dell'interfaccia utente di Azure IoT Central | Microsoft Docs
description: I generatori devono acquisire familiarità con le aree principali dell'interfaccia utente di Azure IoT Central usata per creare una soluzione IoT.
author: dominicbetts
ms.author: dobett
ms.date: 01/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 80fe2fb2998ed129098a99f004da9c9e5e88e474
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55815031"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui-new-ui-design"></a>Presentazione dell'interfaccia utente di Azure IoT Central (nuovo design)

In questo articolo viene presentata l'interfaccia utente di Microsoft Azure IoT Central. È possibile usare l'interfaccia utente per creare, gestire e usare una soluzione Azure IoT Central e i relativi dispositivi connessi.

I _generatori_ usano l'interfaccia utente di Azure IoT Central per definire la soluzione Azure IoT Central. È possibile usare l'interfaccia utente per:

- Definire i tipi di dispositivo che si connettono alla soluzione.
- Configurare le regole e le azioni per i dispositivi.
- Personalizzare l'interfaccia utente per un _operatore_ che usa la soluzione.

Gli _operatori_ usano l'interfaccia utente di Azure IoT Central per gestire la soluzione Azure IoT Central. È possibile usare l'interfaccia utente per:

- Eseguire il monitoraggio dei dispositivi.
- Configurare i dispositivi.
- Risolvere e correggere i problemi relativi ai dispositivi.
- Eseguire il provisioning di nuovi dispositivi.

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="use-the-left-navigation-menu"></a>Usare il menu di spostamento a sinistra

Usare il menu di spostamento a sinistra per accedere alle diverse aree dell'applicazione:

| Menu | DESCRIZIONE |
| ---- | ----------- |
| ![Menu di spostamento a sinistra](media/overview-iot-central-tour-experimental/navigationbar.png) | <ul><li>Il pulsante **Home** consente di visualizzare la home page dell'applicazione. I generatori possono personalizzare questa home page per gli operatori.</li><li>Il pulsante **Device Explorer** consente di visualizzare un elenco dei dispositivi simulati e reali associati a ogni modello di dispositivo nell'applicazione. Gli operatori usano **Device Explorer** per gestire i dispositivi connessi.</li><li>Il pulsante **Set di dispositivi** consente di visualizzare e creare set di dispositivi. Gli operatori possono creare set di dispositivi come raccolta logica di dispositivi specificati da una query.</li><li>Il pulsante **Analisi** consente di visualizzare l'analisi derivata dai dati di telemetria dei dispositivi per dispositivi e set di dispositivi. Gli operatori possono creare visualizzazioni personalizzate basate sui dati dei dispositivi per derivare informazioni dettagliate dall'applicazione.</li><li>Il pulsante **Processi** abilita la gestione dei dispositivi in blocco consentendo di eseguire aggiornamenti su vasta scala tramite la creazione e l'esecuzione di processi.</li><li>Il pulsante **Modelli di dispositivo** visualizza gli strumenti usati da un generatore per creare e gestire i modelli di dispositivo.</li><li>Il pulsante **Esportazione dati continua** consente a un amministratore di configurare un'esportazione continua in altri servizi di Azure, come archiviazione e code.</li><li>Il pulsante **Amministrazione** consente di visualizzare le pagine di amministrazione dell'applicazione in cui un amministratore può gestire impostazioni, utenti e ruoli.</li></ul> |

## <a name="search-help-and-support"></a>Ricerca, guida e supporto tecnico

Il menu superiore viene visualizzato in ogni pagina:

![Barra degli strumenti](media/overview-iot-central-tour-experimental/toolbar.png)

- Per cercare modelli di dispositivo e dispositivi, scegliere l'icona **Cerca**.
- Per cambiare la lingua dell'interfaccia utente, scegliere l'icona **Lingua**.
- Per accedere alla guida e al supporto tecnico, fare clic sul menu a discesa **Guida** per visualizzare un elenco di risorse.
- Per cambiare il tema dell'interfaccia utente o disconnettersi dall'applicazione, scegliere l'icona **Account**.

È possibile scegliere tra un tema scuro o un tema chiaro per l'interfaccia utente:

![Scegliere un tema per l'interfaccia utente](media/overview-iot-central-tour-experimental/themes.png)

## <a name="home-page"></a>Home page

![Home page](media/overview-iot-central-tour-experimental/homepage.png)

La home page è la prima pagina visualizzata quando si accede all'applicazione Azure IoT Central. Per personalizzare la home page per altri utenti dell'applicazione, i generatori possono aggiungere riquadri. Per altre informazioni, vedere l'esercitazione sulla [personalizzazione della visualizzazione operatore di Azure IoT Central](tutorial-customize-operator-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="device-explorer"></a>Esplora dispositivi

![Pagina Explorer](media/overview-iot-central-tour-experimental/explorer.png)

La pagina Explorer visualizza i _dispositivi_ presenti nell'applicazione Azure IoT Central raggruppati per _modello di dispositivo_.

* Un modello di dispositivo consente di definire un tipo di dispositivo in grado di connettersi all'applicazione. Per altre informazioni, vedere l'esercitazione sulla [definizione di un nuovo tipo di dispositivo nell'applicazione Azure IoT Central](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
* Per dispositivo si intende un dispositivo simulato o reale presente nell'applicazione. Per altre informazioni, vedere l'esercitazione sull'[aggiunta di un nuovo dispositivo all'applicazione Azure IoT Central](tutorial-add-device-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="device-sets"></a>Set di dispositivi

![Pagina Set di dispositivi](media/overview-iot-central-tour-experimental/devicesets.png)

La pagina _Set di dispositivi_ visualizza i set di dispositivi creati dal generatore. Per set di dispositivi si intende una raccolta di dispositivi correlati. I generatori definiscono una query per identificare i dispositivi inclusi in un set di dispositivi. I set di dispositivi vengono usati durante la personalizzazione dell'analisi nell'applicazione. Per altre informazioni, vedere l'articolo sull'[uso di set di dispositivi nell'applicazione Azure IoT Central](howto-use-device-sets-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="analytics"></a>Analisi

![Pagina Analisi](media/overview-iot-central-tour-experimental/analytics.png)

La pagina Analisi visualizza grafici che consentono di comprendere il comportamento dei dispositivi connessi all'applicazione. Gli operatori usano questa pagina per controllare e analizzare i problemi relativi ai dispositivi connessi. I generatori possono definire i grafici visualizzati in questa pagina. Per altre informazioni, vedere l'articolo sulla [creazione di analisi personalizzate per l'applicazione Azure IoT Central](howto-use-device-sets-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="jobs"></a>Processi

![Pagina dei processi](media/overview-iot-central-tour-experimental/jobs.png)

La pagina dei processi consente di eseguire operazioni di gestione in blocco per i dispositivi. Il generatore usa questa pagina per aggiornare le proprietà, le impostazioni e i comandi del dispositivo. Per altre informazioni, vedere l'articolo [Eseguire un processo](howto-run-a-job-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="device-templates"></a>Modelli di dispositivo

![Pagina Modelli di dispositivo](media/overview-iot-central-tour-experimental/templates.png)

Questa pagina consente a un generatore di creare e gestire i modelli di dispositivo nell'applicazione. Per altre informazioni, vedere l'esercitazione sulla [definizione di un nuovo tipo di dispositivo nell'applicazione Azure IoT Central](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="continuous-data-export"></a>Esportazione dati continua

![Pagina Esportazione dati continua](media/overview-iot-central-tour-experimental/export.png)

Questa pagina consente a un amministratore di definire come esportare i dati, ad esempio di telemetria, dall'applicazione. I dati esportati possono essere archiviati in altri servizi o usati per l'analisi. Per altre informazioni, vedere l'articolo [Esportare i dati in Azure IoT Central](howto-export-data-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="administration"></a>Amministrazione

![Pagina Amministrazione](media/overview-iot-central-tour-experimental/administration.png)

La pagina Amministrazione contiene i collegamenti agli strumenti usati da un amministratore, ad esempio per definire utenti e ruoli nell'applicazione. Per altre informazioni, vedere l'articolo sull'[amministrazione dell'applicazione Azure IoT Central](howto-administer-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver letto la panoramica di Azure IoT Central e aver acquisito familiarità con il layout dell'interfaccia utente, i passaggi successivi consigliati consistono nel completare la guida introduttiva [Creare un'applicazione Azure IoT Central](quick-deploy-iot-central-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).