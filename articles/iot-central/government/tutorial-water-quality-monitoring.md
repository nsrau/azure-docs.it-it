---
title: "Esercitazione: Creare un'app di monitoraggio della qualità dell'acqua con Azure IoT Central"
description: "Esercitazione: Informazioni su come creare un'app di monitoraggio della qualità dell'acqua usando i modelli di applicazione di Azure IoT Central."
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 7014882e7da9eb7ab7df65cd20403982a46018a3
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112615"
---
# <a name="tutorial-create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Esercitazione: Creare un'applicazione di monitoraggio della qualità dell'acqua in Azure IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Questa esercitazione illustra nel dettaglio la creazione di un'applicazione di monitoraggio della qualità dell'acqua in Azure IoT Central. L'applicazione viene creata dal modello di applicazione **Monitoraggio qualità acqua** di Azure IoT Central.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Usare il modello **Monitoraggio qualità acqua** per creare un'applicazione di monitoraggio della qualità dell'acqua.
> * Esplorare e personalizzare un dashboard dell'operatore.
> * Esplorare un modello di dispositivo di monitoraggio della qualità dell'acqua.
> * Esplorare i dispositivi simulati.
> * Esplorare e configurare le regole.
> * Configurare i processi.
> * Modificare la personalizzazione dell'applicazione tramite white-label.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è consigliabile avere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile crearne una nella [pagina di iscrizione ad Azure](https://aka.ms/createazuresubscription).

## <a name="create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Creare un'applicazione di monitoraggio della qualità dell'acqua in Azure IoT Central

In questa sezione si userà il modello **Monitoraggio qualità acqua** di Azure IoT Central per creare un'applicazione di monitoraggio della qualità dell'acqua.

1. Passare alla [home page di Azure IoT Central](https://aka.ms/iotcentral).

    Se si ha una sottoscrizione di Azure, accedere con le credenziali usate per tale sottoscrizione, altrimenti accedere con un account Microsoft:

    ![Accedere con l'account aziendale](./media/tutorial-waterqualitymonitoring/sign-in.png)

1. Selezionare **Compila** nel riquadro sinistro di Azure IoT Central e quindi selezionare la scheda **Enti governativi**. Il riquadro Enti governativi include diversi modelli di applicazioni per enti pubblici.

    ![Modelli di applicazioni per enti pubblici](./media/tutorial-waterqualitymonitoring/iotcentral-government-tab-overview1.png)

1. Selezionare il modello di applicazione **Monitoraggio qualità acqua**. Questo modello di applicazione include un modello di dispositivo per la qualità dell'acqua, dispositivi simulati, un dashboard dell'operatore e regole di monitoraggio preconfigurate.

1. Selezionare **Crea app**. Viene visualizzato il riquadro **Nuova applicazione** con gli elementi seguenti:

    * **Nome applicazione**: per impostazione predefinita, il nome dell'applicazione è **Monitoraggio qualità acqua** seguito da una stringa ID univoca generata da Azure IoT Central. Se si vuole, è possibile immettere un nome visualizzato o modificare il nome dell'applicazione in un secondo momento.
    * **URL**: è possibile immettere qualsiasi URL si voglia o modificare il valore dell'URL in un secondo momento.
    * Se si ha una sottoscrizione di Azure, immettere i valori corrispondenti in **Directory**, **Sottoscrizione di Azure** e **Località**. Se non si ha una sottoscrizione, è possibile attivare la **versione di valutazione gratuita valida per 7 giorni** e compilare le informazioni di contatto richieste.

    Per altre informazioni su directory e sottoscrizioni, vedere la guida di avvio rapido [Creare un'applicazione Azure IoT Central (funzionalità di anteprima)](../core/quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

1. Selezionare il pulsante **Crea** nella parte inferiore sinistra della pagina.

    ![Pagina Nuova applicazione di Azure IoT Central](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1.png)

È stata così creata un'applicazione di monitoraggio della qualità dell'acqua tramite il modello **Monitoraggio qualità acqua** di Azure IoT Central.

La nuova applicazione include questi componenti preconfigurati:

* Dashboard dell'operatore
* Modelli di dispositivo di monitoraggio della qualità dell'acqua
* Dispositivi di monitoraggio della qualità dell'acqua simulati
* Regole e processi
* Personalizzazione con white-label

È possibile modificare l'applicazione in qualsiasi momento.

A questo punto occorre esplorare l'applicazione e personalizzarla.

## <a name="explore-and-customize-the-operator-dashboard"></a>Esplorare e personalizzare il dashboard dell'operatore

Una volta creata l'applicazione, si apre il **dashboard di monitoraggio della qualità dell'acqua Wide World**.

   ![Dashboard di monitoraggio della qualità dell'acqua](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-dashboard1.png)

Tra le attività di compilazione, è possibile creare e personalizzare le visualizzazioni nel dashboard per l'uso da parte degli operatori. Prima di procedere con la personalizzazione, però, è opportuno esplorare il dashboard.

Tutti i dati visualizzati nel dashboard sono basati sui dati dei dispositivi simulati, descritti nella sezione successiva.

Il dashboard include i tipi di riquadri seguenti:

* **Riquadro dell'immagine dell'ente di gestione acqua Wide World**: il primo riquadro nell'angolo superiore sinistro del dashboard è un'immagine del servizio fittizio Wide World. È possibile personalizzare il riquadro con la propria immagine oppure rimuoverlo del tutto.

* **Riquadri dei KPI medi del pH**: I riquadri dei KPI, come **Average pH in the last 30 minutes** (pH medio negli ultimi 30 minuti), si trovano nella parte superiore del riquadro del dashboard. È possibile personalizzare i riquadri dei KPI e impostarne ognuno su un tipo e un intervallo di tempo diversi.

* **Mappa dell'area di monitoraggio acqua**: Azure IoT Central usa Mappe di Azure, che è possibile impostare direttamente nell'applicazione per visualizzare la posizione del dispositivo. È anche possibile eseguire il mapping delle informazioni sulla posizione dall'applicazione al dispositivo e quindi usare Mappe di Azure per visualizzare le informazioni su una mappa. Passare il puntatore del mouse sulla mappa e provare i controlli.

* **Mappa termica della distribuzione media del pH**: è possibile selezionare grafici di visualizzazione diversi per visualizzare i dati di telemetria dei dispositivi nel modo più appropriato per l'applicazione.

* **Grafico a linee degli indicatori di qualità critici**: è possibile visualizzare i dati di telemetria del dispositivo tracciati in un grafico a linee su un intervallo di tempo.  

* **Grafico a barre sulla concentrazione degli agenti chimici**: è possibile visualizzare i dati di telemetria del dispositivo in un grafico a barre.

* **Pulsante di azione**: il dashboard include un riquadro per le azioni che possono essere avviate direttamente da un operatore dal dashboard di monitoraggio, ad esempio la reimpostazione delle proprietà di un dispositivo.

* **Riquadri degli elenchi di proprietà**: il dashboard include più riquadri delle proprietà che rappresentano informazioni sulle soglie, sull'integrità dei dispositivi e sulla manutenzione.

### <a name="customize-the-dashboard"></a>Personalizzare il dashboard

Tra le attività di compilazione, è possibile personalizzare le visualizzazioni nel dashboard per l'uso da parte degli operatori.

1. Selezionare **Modifica** per personalizzare il riquadro **Wide World water quality dashboard** (Dashboard della qualità dell'acqua Wide World). È possibile personalizzare il dashboard selezionando i comandi nel menu **Modifica**. Una volta impostato il dashboard in modalità di modifica, è possibile aggiungere nuovi riquadri oppure configurare i riquadri esistenti.

    ![Modificare il dashboard](./media/tutorial-waterqualitymonitoring/edit-dashboard.png)

1. Selezionare **+ Nuovo** per creare un nuovo dashboard che è possibile configurare. È possibile creare più dashboard e spostarsi tra di essi dal menu Dashboard.

## <a name="explore-a-water-quality-monitoring-device-template"></a>Esplorare un modello di dispositivo di monitoraggio della qualità dell'acqua

Un modello di dispositivo in Azure IoT Central definisce le funzionalità di un dispositivo. Le funzionalità disponibili sono costituite da telemetria, proprietà e comandi. In Azure IoT Central è possibile definire modelli di dispositivo che rappresentano le funzionalità dei dispositivi connessi. È anche possibile creare dispositivi simulati per testare il modello di dispositivo e l'applicazione.

L'applicazione di monitoraggio della qualità dell'acqua creata è completa di un modello Monitoraggio qualità acqua.

Per visualizzare il modello di dispositivo:

1. Selezionare **Modelli di dispositivo** nel riquadro più a sinistra dell'applicazione in Azure IoT Central.
1. Nell'elenco dei modelli di dispositivo selezionare **Monitoraggio qualità acqua**. Si aprirà il modello di dispositivo corrispondente.

    ![Modello di dispositivo](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate.png)

### <a name="customize-the-device-template"></a>Personalizzare il modello di dispositivo

A questo punto è opportuno fare pratica con la personalizzazione delle impostazioni dei modelli di dispositivo seguenti:

1. Scegliere **Personalizza** dal menu del modello di dispositivo.
1. Passare al tipo di telemetria **Temperatura**.
1. Cambiare il valore di **Nome visualizzato** in **Temperatura segnalata**.
1. Cambiare l'unità di misura oppure impostare **Valore minimo** e **Valore massimo**.
1. Selezionare **Salva**.

#### <a name="add-a-cloud-property"></a>Aggiungere una proprietà cloud

1. Scegliere **Proprietà cloud** dal menu del modello di dispositivo.
1. Per aggiungere una nuova proprietà cloud, selezionare **+ Aggiungi proprietà cloud**. In Azure IoT Central è possibile aggiungere una proprietà rilevante per un dispositivo, ma che non si prevede venga inviata dal dispositivo. Un esempio di una proprietà di questo tipo è una soglia di avviso specifica per l'area di installazione, informazioni sulle risorse o informazioni di manutenzione.
1. Selezionare **Salva**.

### <a name="explore-views"></a>Esplorare le visualizzazioni

Il modello di dispositivo per il monitoraggio della qualità dell'acqua è dotato di visualizzazioni predefinite. Le visualizzazioni definiscono il modo in cui gli operatori vedono i dati del dispositivo e impostano le proprietà cloud. Esplorare le visualizzazioni e fare un po' di pratica apportando modifiche.

  ![Visualizzazioni del modello di dispositivo](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate-views.png)

### <a name="publish-the-device-template"></a>Pubblicare il modello di dispositivo

Se si apportano modifiche, assicurarsi di selezionare **Pubblica** per pubblicare il modello di dispositivo.

### <a name="create-a-new-device-template"></a>Creare un nuovo modello di dispositivo

1. Selezionare **+ Nuovo** per creare un nuovo modello di dispositivo e seguire il processo di creazione.
1. Creare un modello di dispositivo personalizzato o sceglierne uno dal catalogo dei dispositivi di Azure IoT.

## <a name="explore-simulated-devices"></a>Esplorare i dispositivi simulati

L'applicazione di monitoraggio della qualità dell'acqua creata dal modello di applicazione ha due dispositivi simulati. Questi dispositivi sono mappati al modello di dispositivo di monitoraggio della qualità dell'acqua.

### <a name="view-the-devices"></a>Visualizzare i dispositivi

1. Selezionare **Dispositivi** nel riquadro più a sinistra dell'applicazione.

   ![Dispositivi](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devices.png)

1. Selezionare un dispositivo simulato.

    ![Selezionare il dispositivo 1](./media/tutorial-waterqualitymonitoring/waterqualitymonitor-device1.png)

1. Nella scheda **Proprietà cloud** cambiare il valore di **Acidity (pH) threshold** (Soglia acidità (pH)) da **8** a **9**.
1. Esplorare la scheda **Proprietà dispositivo** e la scheda **Device Dashboard** (Dashboard dispositivo).

> [!NOTE]
> Tutte le schede sono state configurate dalle **visualizzazioni del modello di dispositivo**.

### <a name="add-new-devices"></a>Aggiungere nuovi dispositivi

Nella scheda **Dispositivi** selezionare **+ Nuovo** per aggiungere un nuovo dispositivo.

## <a name="explore-and-configure-rules"></a>Esplorare e configurare le regole

In Azure IoT Central è possibile creare regole che monitorano automaticamente i dati di telemetria del dispositivo. Queste regole attivano un'azione quando viene soddisfatta una delle relative condizioni. Una possibile azione è l'invio di notifiche tramite posta elettronica. Altre possibilità sono un'azione di Microsoft Flow o un'azione di webhook per l'invio di dati ad altri servizi.

L'applicazione di monitoraggio della qualità dell'acqua creata ha due regole preconfigurate.

### <a name="view-rules"></a>Visualizzare le regole

1. Selezionare **Regole** nel riquadro più a sinistra dell'applicazione.

   ![Regole](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-rules.png)

1. Selezionare **High pH alert** (Avviso pH elevato), ossia una delle regole preconfigurate nell'applicazione.

   ![Regola High pH alert (Avviso pH elevato)](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-highphalert.png)

   La regola **High pH alert** (Avviso pH elevato) è configurata in modo da controllare se la condizione di acidità (pH) supera il valore 8.

Aggiungere ora un'azione di posta elettronica alla regola:

1. Selezionare **+ Posta elettronica**.
1. Nella casella **Nome visualizzato** immettere **High pH alert** (Avviso pH elevato).
1. Nella casella **A** immettere l'indirizzo di posta elettronica associato all'account di Azure IoT Central.
1. Se si vuole, immettere una nota da includere nel testo del messaggio di posta elettronica.
1. Selezionare **Fine** per completare l'azione.
1. Selezionare **Salva** per salvare e attivare la nuova regola.

Quando la condizione configurata viene soddisfatta, si dovrebbe ricevere un messaggio di posta elettronica dopo pochi minuti.

> [!NOTE]
> L'applicazione invia un messaggio ogni volta che viene soddisfatta una condizione. Selezionare **Disabilita** per una regola se non si vogliono più ricevere messaggi di posta elettronica automatici da tale regola.
  
Per creare una nuova regola, selezionare **Regole** nel riquadro più a sinistra dell'applicazione e quindi selezionare **+ Nuovo**.

## <a name="configure-jobs"></a>Configurare i processi

Con i processi di Azure IoT Central è possibile attivare aggiornamenti per le proprietà del dispositivo o cloud su più dispositivi. È anche possibile usare i processi per attivare i comandi di dispositivo su più dispositivi. Azure IoT Central automatizza il flusso di lavoro.

1. Selezionare **Processi** nel riquadro più a sinistra dell'applicazione.
1. Selezionare **+ Nuovo** e configurare uno o più processi.

## <a name="customize-your-application"></a>Personalizzare l'applicazione

In qualità di autore, è possibile modificare diverse impostazioni per personalizzare l'esperienza utente nell'applicazione.

1. Selezionare **Amministrazione** > **Personalizzare l'applicazione**.
1. In **Logo applicazione** selezionare **Cambia** per scegliere l'immagine da caricare come logo.
1. In **Icona del browser** selezionare **Cambia** per scegliere l'immagine da visualizzare sulle schede del browser.
1. In **Colori del browser** è possibile sostituire i valori predefiniti con codici colore esadecimali HTML.
1. Selezionare **Impostazioni** per cambiare il valore di **Tema**.

   ![Personalizzare l'applicazione](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-customize-your-application1.png)

### <a name="update-the-application-image"></a>Aggiornare l'immagine dell'applicazione

1. Selezionare **Amministrazione** > **Impostazioni applicazione**.

1. Usare il pulsante **Scegli un'immagine** per scegliere un'immagine da caricare come immagine dell'applicazione.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare l'applicazione, eliminarla seguendo questa procedura:

1. Aprire la scheda **Amministrazione** nel riquadro a sinistra dell'applicazione.
1. Selezionare **Impostazioni applicazione** e quindi il pulsante **Elimina**.

    ![Eliminare l'applicazione](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-application-settings-delete-app1.png)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sui [concetti di monitoraggio della qualità dell'acqua](./concepts-waterqualitymonitoring-architecture.md)
