---
title: Presentazione dell'interfaccia utente di Azure IoT Central | Microsoft Docs
description: Acquisisci familiarità con le aree principali dell'interfaccia utente di Azure IoT Central che consente di creare, gestire e usare la soluzione IoT.
author: lmasieri
ms.author: lmasieri
ms.date: 10/21/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: f8aa467f95bb97c42e726d1392deca53e15df624
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73896489"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui-preview-features"></a>Presentazione dell'interfaccia utente di Azure IoT Central (funzionalità di anteprima)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

In questo articolo viene presentata l'interfaccia utente di Microsoft Azure IoT Central. È possibile usare l'interfaccia utente per creare, gestire e usare una soluzione Azure IoT Central e i relativi dispositivi connessi.

Un _generatore di soluzioni_ usa l'interfaccia utente di Azure IoT Central per definire la soluzione Azure IoT Central. È possibile usare l'interfaccia utente per:

* Definire i tipi di dispositivo che si connettono alla soluzione.
* Configurare le regole e le azioni per i dispositivi. 
* Personalizzare l'interfaccia utente per un _operatore_ che usa la soluzione.

Gli _operatori_ usano l'interfaccia utente di Azure IoT Central per gestire la soluzione Azure IoT Central. È possibile usare l'interfaccia utente per:

* Eseguire il monitoraggio dei dispositivi.
* Configurare i dispositivi.
* Risolvere e correggere i problemi relativi ai dispositivi.
* Eseguire il provisioning di nuovi dispositivi.

## <a name="iot-central-homepage"></a>Home page di IoT Central

La [home page di IoT Central](https://aka.ms/iotcentral-get-started) contiene le novità e le funzionalità più recenti di IoT Central e consente di creare nuove applicazioni nonché di visualizzare e avviare l'applicazione esistente.

> [!div class="mx-imgBorder"]
> ![Home page di IoT Central](media/overview-iot-central-tour/iot-central-homepage-pnp.png)

### <a name="create-an-application"></a>Creare un'applicazione

Nella sezione di compilazione è possibile esplorare l'elenco di modelli di IoT Central pertinenti per il settore per iniziare rapidamente oppure usare un modello di app personalizzato.  
> [!div class="mx-imgBorder"]
> ![Pagina di compilazione di IoT Central](media/overview-iot-central-tour/iot-central-build-pnp.png)

Per altre informazioni, vedere la Guida introduttiva [Creare un'applicazione di Azure IoT Central](quick-deploy-iot-central.md).

### <a name="launch-your-application"></a>Avviare l'applicazione

Per avviare l'applicazione IoT Central, accedere all'URL scelto dall'utente o dal generatore di soluzioni durante la creazione dell'app. È anche possibile visualizzare un elenco di tutte le applicazioni a cui è possibile accedere nella pagina di [gestione app di IoT Central](https://aka.ms/iotcentral-apps).

> [!div class="mx-imgBorder"]
> ![Gestione app IoT Central](media/overview-iot-central-tour/app-manager-pnp.png)

## <a name="navigate-your-application"></a>Esplorare l'applicazione

All'interno dell'applicazione IoT, usare il riquadro sinistro per accedere alle diverse aree. È possibile espandere o comprimere la barra di spostamento selezionando l'icona con tre linee nella parte superiore della barra:

> [!NOTE]
> Gli elementi visualizzati sulla barra di spostamento dipendono dal ruolo utente. Altre informazioni sulla [gestione di utenti e ruoli](howto-manage-users-roles.md). 

:::row:::
  :::column span="":::
      > [!div class="mx-imgBorder"]
      > ![riquadro sinistro](media/overview-iot-central-tour/navigationbar-pnp.png)
  :::column-end:::
  :::column span="2":::
     **Dashboard** consente di accedere al dashboard dell'applicazione. Un *generatore di soluzioni* può personalizzare il dashboard globale per gli operatori. In base al ruolo utente, gli operatori possono anche creare dashboard personali.
     
     **Dispositivi** consente di gestire i dispositivi connessi, reali e simulati.

     **Gruppi di dispositivi** consente di visualizzare e creare raccolte logiche di dispositivi specificati da una query. È possibile salvare la query e usare i gruppi di dispositivi nell'applicazione per eseguire operazioni in blocco.

     **Regole** consente di creare e modificare le regole per monitorare i dispositivi. Le regole vengono valutate in base ai dati di telemetria del dispositivo e attivano azioni personalizzabili.

     **Analisi** consente di creare visualizzazioni personalizzate sulla base dei dati dei dispositivi per derivare informazioni approfondite dall'applicazione.

     **Processi** consente di gestire i dispositivi con scalabilità eseguendo operazioni in blocco.

     **Modelli di dispositivo** consente di creare e gestire le caratteristiche dei dispositivi che si connettono all'applicazione.

     **Esportazione dati** consente di configurare un'esportazione continua a servizi esterni, ad esempio archiviazione e code.

     **Amministrazione** consente di gestire le impostazioni dell'applicazione, la personalizzazione, la fatturazione, gli utenti e i ruoli.

     **IoT Central** consente agli *amministratori* di accedere alla pagina di gestione delle di IoT Central.
     
   :::column-end:::
:::row-end:::

### <a name="search-help-theme-and-support"></a>Ricerca, guida, tema e supporto

Il menu superiore viene visualizzato in ogni pagina:

> [!div class="mx-imgBorder"]
> ![Barra degli strumenti](media/overview-iot-central-tour/toolbar-pnp.png)

* Per cercare modelli di dispositivo e dispositivi, immettere un valore in **Search** (Cerca).
* Per cambiare la lingua o il tema dell'interfaccia utente, scegliere l'icona **Settings** (Impostazioni). Altre informazioni sulla [gestione delle preferenze dell'applicazione](../core/howto-manage-preferences.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)
* Per disconnettersi dall'applicazione, scegliere l'icona **Account**.
* Per accedere alla guida e al supporto tecnico, fare clic sul menu a discesa **Help** (Guida) per visualizzare un elenco di risorse. In una versione di valutazione dell'applicazione le risorse di supporto includono l'accesso alla [live chat](../core/howto-show-hide-chat.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json).

È possibile scegliere tra un tema scuro o un tema chiaro per l'interfaccia utente:

> [!NOTE]
> L'opzione per scegliere il tema chiaro o scuro non è disponibile se l'amministratore ha configurato un tema personalizzato per l'applicazione.

> [!div class="mx-imgBorder"]
> ![Scegliere un tema per l'interfaccia utente](media/overview-iot-central-tour/themes-pnp.png)

### <a name="dashboard"></a>dashboard
> [!div class="mx-imgBorder"]
> ![Dashboard](media/overview-iot-central-tour/dashboard-pnp.png)

* Il dashboard è la prima pagina visualizzata quando si accede all'applicazione Azure IoT Central. Un *generatore di soluzioni* può creare e personalizzare più dashboard di applicazioni globali per altri utenti. Altre informazioni sull'[aggiunta di riquadri al dashboard](../core/howto-add-tiles-to-your-dashboard.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)

* Un'*operatore*, se il ruolo utente lo consente, può creare dashboard personali per monitorare gli elementi che interessano. Per altre informazioni, vedere la procedura dettagliata [Creare dashboard personali di Azure IoT Central](../core/howto-create-personal-dashboards.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json).

### <a name="devices"></a>Dispositivi

> [!div class="mx-imgBorder"]
> ![Pagina Dispositivi](media/overview-iot-central-tour/devices-pnp.png)

La pagina Explorer visualizza i _dispositivi_ presenti nell'applicazione Azure IoT Central raggruppati per _modello di dispositivo_. 

* Un modello di dispositivo consente di definire un tipo di dispositivo in grado di connettersi all'applicazione.
* Per dispositivo si intende un dispositivo simulato o reale presente nell'applicazione.

Per altre informazioni, vedere la Guida introduttiva [Monitorare i dispositivi](./quick-monitor-devices.md). 

### <a name="device-groups"></a>Gruppi di dispositivi

> [!div class="mx-imgBorder"]
> ![Pagina Gruppi di dispositivi](media/overview-iot-central-tour/device-groups-pnp.png)

Per gruppo di dispositivi si intende una raccolta di dispositivi correlati. Un *generatore di soluzioni* definisce una query per identificare i dispositivi inclusi in un gruppo di dispositivi. I gruppi di dispositivi vengono usati per eseguire operazioni in blocco nell'applicazione. Per altre informazioni, vedere l'articolo [Usare gruppi di dispositivi nell'applicazione Azure IoT Central](tutorial-use-device-groups.md).

### <a name="rules"></a>Regole
> [!div class="mx-imgBorder"]
> ![Pagina Regole](media/overview-iot-central-tour/rules-pnp.png)

La pagina Regole consente di definire regole in base ai dati di telemetria, allo stato o agli eventi relativi ai dispositivi. Quando una regola viene generata, può attivare una o più azioni, ad esempio l'invio di un messaggio di posta elettronica, la notifica a un sistema esterno tramite gli avvisi del webhook e così via. Per altre informazioni, vedere l'esercitazione [Configurazione di regole](tutorial-create-telemetry-rules.md). 

### <a name="analytics"></a>Analisi

> [!div class="mx-imgBorder"]
> ![Pagina Analisi](media/overview-iot-central-tour/analytics-pnp.png)

La pagina Analisi consente di creare visualizzazioni personalizzate sulla base dei dati dei dispositivi per derivare informazioni approfondite dall'applicazione. Per altre informazioni, vedere l'articolo [Creare analisi per l'applicazione Azure IoT Central](howto-create-analytics.md).

### <a name="jobs"></a>Processi

> [!div class="mx-imgBorder"]
> ![Pagina Processi](media/overview-iot-central-tour/jobs-pnp.png)

La pagina Processi consente di eseguire operazioni di gestione in blocco dei dispositivi nei propri dispositivi. È possibile aggiornare le proprietà del dispositivo, le impostazioni e i comandi di esecuzione nei gruppi di dispositivi. Per altre informazioni, vedere l'articolo [Eseguire un processo](../core/howto-run-a-job.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json).

### <a name="device-templates"></a>Modelli di dispositivo

> [!div class="mx-imgBorder"]
> ![Pagina Modelli di dispositivo](media/overview-iot-central-tour/templates-pnp.png)

Questa pagina consente a un generatore di creare e gestire i modelli di dispositivo nell'applicazione. Un modello di dispositivo specifica le caratteristiche del dispositivo, ad esempio:

* Dati di telemetria, stato e misure di evento
* Proprietà
* Comandi
* Visualizzazioni

Il *generatore di soluzioni* può anche creare moduli e dashboard che gli operatori possono usare per gestire i dispositivi.

Per altre informazioni, vedere l'esercitazione sulla [definizione di un nuovo tipo di dispositivo nell'applicazione Azure IoT Central](howto-set-up-template.md). 

### <a name="data-export"></a>Esportazione dati
> [!div class="mx-imgBorder"]
> ![Pagina Esportazione dati](media/overview-iot-central-tour/export-pnp.png)

La pagina Esportazione dati consente di configurare flussi di dati, ad esempio di telemetria, dall'applicazione ai sistemi esterni. Per altre informazioni, vedere l'articolo [Esportare i dati in Azure IoT Central](./howto-export-data.md).

### <a name="administration"></a>Administration
> [!div class="mx-imgBorder"]
> ![Pagina Amministrazione](media/overview-iot-central-tour/administration-pnp.png)

La pagina Amministrazione consente di configurare e personalizzare l'applicazione IoT Central. In tale pagina è possibile modificare nome dell'applicazione, URL e temi, gestire utenti e ruoli, creare token API ed esportare l'applicazione. Per altre informazioni, vedere l'articolo sull'[amministrazione dell'applicazione Azure IoT Central](howto-administer.md).

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver letto la panoramica di Azure IoT Central e aver acquisito familiarità con il layout dell'interfaccia utente, i passaggi successivi consigliati consistono nel completare la guida introduttiva [Creare un'applicazione Azure IoT Central](quick-deploy-iot-central.md).
