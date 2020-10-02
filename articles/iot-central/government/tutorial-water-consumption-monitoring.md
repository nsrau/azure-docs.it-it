---
title: "Esercitazione: Creare un'app di monitoraggio consumo idrico con Azure IoT Central"
description: "Esercitazione: Informazioni su come creare un'app di monitoraggio del consumo idrico usando i modelli di applicazione di Azure IoT Central."
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 400585d3e5908268708d93ceeefd26a4a5efdd49
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90972391"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-with-azure-iot-central"></a>Esercitazione: Creare un'applicazione per il monitoraggio del consumo idrico con Azure IoT Central

Questa esercitazione illustra come creare un'applicazione Azure IoT Central per il monitoraggio del consumo idrico dal modello di applicazione Monitoraggio consumo idrico di Azure IoT Central.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Usare il modello Monitoraggio consumo idrico di Azure IoT Central per creare un'applicazione per il monitoraggio del consumo idrico.
> * Esplorare e personalizzare il dashboard dell'operatore.
> * Esplorare i modelli di dispositivo.
> * Esplorare i dispositivi simulati.
> * Esplorare e configurare le regole.
> * Configurare i processi.
> * Personalizzare il nome dell'applicazione tramite white-labeling.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, sono necessari:

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile crearne una nella [pagina di iscrizione ad Azure](https://aka.ms/createazuresubscription).

## <a name="create-a-water-consumption-monitoring-app-with-azure-iot-central"></a>Creare un'app di monitoraggio consumo idrico con Azure IoT Central

In questa sezione si usa il modello Monitoraggio consumo idrico di Azure IoT Central per creare un'applicazione per il monitoraggio del consumo idrico in Azure IoT Central.

Per creare una nuova applicazione di monitoraggio consumo idrico:

1. Passare alla [home page di Azure IoT Central](https://aka.ms/iotcentral).

    Se si ha una sottoscrizione di Azure, accedere con le credenziali usate per tale sottoscrizione, altrimenti accedere con un account Microsoft.

    ![Immettere l'account dell'organizzazione](media/tutorial-waterconsumptionmonitoring/sign-in.png)

1. Selezionare **Compila** nel riquadro sinistro e quindi la scheda **Enti governativi**. La pagina **Enti governativi** visualizza diversi modelli di applicazioni per enti governativi.

   ![Creare modelli di applicazioni per enti governativi](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. Selezionare il modello di applicazione **Monitoraggio consumo idrico**.
Questo modello include un modello di esempio di dispositivo per il monitoraggio del consumo idrico, un dispositivo simulato, un dashboard per operatore e regole di monitoraggio preconfigurate.

1. Selezionare **Crea app** per aprire il modulo di creazione **Nuova applicazione**, che contiene i campi seguenti:
    * **Nome applicazione**: per impostazione predefinita, il nome dell'applicazione è *Monitoraggio consumo idrico* seguito da una stringa ID univoca generata da Azure IoT Central. Se si preferisce, scegliere un nome applicazione descrittivo. È anche possibile cambiare il nome dell'applicazione in un secondo momento.
    * **URL**: Azure IoT Central genera automaticamente un URL basato sul nome dell'applicazione. È possibile scegliere di aggiornare l'URL in base alle esigenze. È anche possibile cambiare l'URL in un secondo momento.
    * Se si ha una sottoscrizione di Azure, immettere i valori relativi a **Directory**, **Sottoscrizione di Azure** e **Località**. Se non si ha una sottoscrizione, è possibile selezionare l'opzione **Versione di valutazione gratuita valida 7 giorni** e completare le informazioni di contatto necessarie.

    Per altre informazioni su directory e sottoscrizioni, vedere la guida di avvio rapido [Creare un'applicazione](../core/quick-deploy-iot-central.md).

1. Selezionare **Crea** nella parte inferiore della pagina.

    ![Pagina Nuova applicazione di Azure IoT Central](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

    ![Pagina di informazioni sulla fatturazione di Azure IoT Central](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring-billinginfo.png)

È stata creata un'app per il monitoraggio del consumo idrico usando il modello Monitoraggio consumo idrico di Azure IoT Central.

L'applicazione per il monitoraggio consumo idrico è già preconfigurata:

* Esempi di dashboard per operatore.
* Modelli predefiniti di esempio di dispositivi di controllo dei flussi d'acqua e delle valvole.
* Dispositivi simulati di controllo dei flussi d'acqua e delle valvole intelligenti.
* Regole e processi.
* Personalizzazione di esempio tramite white-labeling.

L'applicazione può essere modificata in qualsiasi momento. È quindi il momento di esplorare l'applicazione e personalizzarla.

## <a name="explore-and-customize-the-operator-dashboard"></a>Esplorare e personalizzare il dashboard dell'operatore

Una volta creata l'applicazione, si apre l'esempio **Wide World water consumption dashboard** (Dashboard sul consumo idrico di Wide World).

   ![Dashboard di monitoraggio consumo idrico](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

Tra le attività di compilazione, è possibile creare e personalizzare le visualizzazioni nel dashboard per gli operatori. Prima di procedere con la personalizzazione, esplorare il dashboard.

> [!NOTE]
> Tutti i dati visualizzati nel dashboard sono basati sui dati del dispositivo simulato, descritti nella sezione successiva.
  
Il dashboard è costituito da diversi tipi di riquadri:

* **Riquadro dell'immagine dell'ente di gestione acqua Wide World**: il primo riquadro del dashboard è un'immagine dell'ente di gestione dell'acqua fittizio Wide World. È possibile personalizzarlo inserendo la propria immagine oppure rimuoverlo del tutto.
* **Riquadro dei KPI sulla media dei flussi d'acqua**: il riquadro dei KPI è configurato in modo da visualizzare come esempio *la media negli ultimi 30 minuti*. È possibile personalizzare il riquadro dei KPI e impostarlo su un tipo e un intervallo di tempo diversi.
* **Riquadri dei comandi del dispositivo**: Questi riquadri includono **Close valve** (Chiudi valvola), **Open valve** (Apri valvola) e **Set valve position** (Imposta la posizione della valvola). Selezionando i comandi verrà visualizzata la pagina dei comandi del dispositivo simulato. In Azure IoT Central un *comando* è un tipo di *funzionalità di dispositivo*. Questo concetto verrà esaminato più avanti nella sezione "Modello di dispositivo" di questa esercitazione.
* **Mappa dell'area di distribuzione dell'acqua**: la mappa si basa su Mappe di Azure, che è possibile configurare direttamente in Azure IoT Central. Il riquadro della mappa visualizza la posizione del dispositivo. Passare il puntatore del mouse sulla mappa e provare i relativi controlli, ad esempio *zoom avanti*, *zoom indietro* o *espandi*.

    ![Mappa del dashboard di monitoraggio consumo idrico](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* **Grafico a linee del flusso di acqua medio** e **grafico a linee della condizione ambientale**: è possibile visualizzare uno o più i dati di telemetria del dispositivo tracciati in un grafico a linee su un intervallo di tempo a scelta.
* **Grafico della mappa termica della pressione media della valvola**: è possibile scegliere il tipo di visualizzazione della mappa termica dei dati di telemetria del dispositivo distribuiti in un intervallo di tempo con un indice colori.
* **Riquadro di contenuto per la reimpostazione delle soglie di avviso**: è possibile includere riquadri di contenuto di tipo invito all'azione e incorporare un collegamento a una pagina di azioni. In questo caso, il comando di reimpostazione delle soglie di avviso consente di passare all'applicazione **Jobs** (Processi), in cui è possibile apportare aggiornamenti alle proprietà del dispositivo. Questa opzione verrà esaminata più avanti nella sezione "Configurare i processi" di questa esercitazione.
* **Riquadri di proprietà**: il dashboard visualizza le proprietà **Valve operational info** (Informazioni operative valvole), **Flow alert thresholds** (Soglie di avviso flusso) e **Maintenance info** (Informazioni di manutenzione).

### <a name="customize-the-dashboard"></a>Personalizzare il dashboard

Lo sviluppatore può personalizzare le visualizzazioni del dashboard per gli operatori.

1. Selezionare **Modifica** per personalizzare il riquadro **Wide World water consumption dashboard** (Dashboard del consumo idrico Wide World). È possibile personalizzare il dashboard selezionando il menu **Modifica**. Una volta impostato il dashboard in modalità di **modifica**, è possibile aggiungere nuovi riquadri o configurarlo.

     ![Dashboard in modalità di modifica](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

1. Selezionare **+ Nuovo** per creare un nuovo dashboard e configurarlo da zero. È possibile creare più dashboard e spostarsi da uno all'altro con il menu Dashboard.

## <a name="explore-the-device-template"></a>Esplorare il modello di dispositivo

Un modello di dispositivo in Azure IoT Central definisce le funzionalità di un dispositivo, che possono essere costituite da telemetria, proprietà e comandi. In Azure IoT Central lo sviluppatore può definire uno o più modelli di dispositivo, che rappresentano le funzionalità dei dispositivi che verranno connessi.

L'applicazione per il monitoraggio del consumo idrico include due modelli di dispositivo di riferimento che rappresentano un *misuratore del flusso* e una *valvola intelligente*.

Per visualizzare il modello di dispositivo:

1. Selezionare **Modelli di dispositivo** nel riquadro sinistro dell'applicazione in Azure IoT Central. Nell'elenco **Modelli di dispositivo** sono presenti due modelli di dispositivo: **Smart Valve** (Valvola intelligente) e **Flow meter** (Misuratore del flusso).

   ![Modello di dispositivo](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

1. Selezionare il modello di dispositivo **Flow meter** (Misuratore del flusso) e acquisire familiarità con le relative funzionalità.

     ![Modello di dispositivo Flow meter](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customize-the-device-template"></a>Personalizzare il modello di dispositivo

Per personalizzare il modello di dispositivo:

1. Scegliere **Personalizza** dal menu **Modelli di dispositivo**.
1. Trovare il tipo di dati di telemetria `Temperature`.
1. Aggiornare il campo **Nome visualizzato** da `Temperature` a `Reported temperature`.
1. Aggiornare l'unità di misura oppure impostare **Valore minimo** e **Valore massimo**.
1. Selezionare **Salva** per salvare le eventuali modifiche.

### <a name="add-a-cloud-property"></a>Aggiungere una proprietà cloud

1. Scegliere **Proprietà cloud** dal menu **Modelli di dispositivo**.
1. Selezionare **+ Aggiungi proprietà cloud** per aggiungere una nuova proprietà cloud.
    In Azure IoT Central è possibile aggiungere una proprietà appropriata per il dispositivo. Un esempio di proprietà cloud può essere una soglia di avviso specifica per un'area di installazione, informazioni sulle risorse o altre informazioni di manutenzione.
1. Selezionare **Salva** per salvare le eventuali modifiche.

### <a name="views"></a>Viste

Il modello di dispositivo per il monitoraggio del consumo idrico è dotato di visualizzazioni predefinite. Esplorare le visualizzazioni e, se necessario, aggiornarle. Le visualizzazioni definiscono il modo in cui gli operatori vedranno i dati del dispositivo, ma anche come immetteranno le proprietà cloud.

  ![Visualizzazioni del modello di dispositivo](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>Pubblica

Se sono state apportate modifiche, assicurarsi di selezionare **Pubblica** per pubblicare il modello di dispositivo.

### <a name="create-a-new-device-template"></a>Creare un nuovo modello di dispositivo

Selezionare **+ Nuovo** per creare un nuovo modello di dispositivo e seguire il processo di creazione.
È possibile creare un modello di dispositivo personalizzato da zero oppure sceglierne uno dal catalogo dei dispositivi di Azure.

## <a name="explore-simulated-devices"></a>Esplorare i dispositivi simulati

In Azure IoT Central è possibile creare dispositivi simulati per testare il proprio modello di dispositivo e l'applicazione. L'applicazione per il monitoraggio del consumo idrico include due dispositivi simulati mappati ai modelli di dispositivo **Flow meter** (Misuratore del flusso) e **Smart Valve** (Valvola intelligente).

### <a name="view-the-devices"></a>Visualizzare i dispositivi

1. Selezionare **Dispositivi** > **Tutti i dispositivi** nel riquadro sinistro.

   ![Riquadro Tutti i dispositivi](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

1. Selezionare **Smart Valve 1** (Valvola intelligente 1).

    ![Smart Valve 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

1. Nella scheda **Comandi** è possibile vedere i tre comandi del dispositivo **Close Valve** (Chiudi valvola), **Open Valve** (Apri valvola) e **Set Valve position** (Imposta posizione valvola), che sono funzionalità definite nel modello di dispositivo **Smart Valve** (Valvola intelligente).

1. Esplorare la scheda **Proprietà dispositivo** e la scheda **Device Dashboard** (Dashboard dispositivo).

> [!NOTE]
> Si noti che tutte le schede vengono configurate dalle visualizzazioni del modello di dispositivo.

### <a name="add-new-devices"></a>Aggiungere nuovi dispositivi

Aggiungere nuovi dispositivi selezionando **+ Nuovo** nella scheda **Dispositivi**.

## <a name="explore-and-configure-rules"></a>Esplorare e configurare le regole

In Azure IoT Central è possibile creare regole per monitorare automaticamente i dati di telemetria del dispositivo e attivare azioni quando vengono soddisfatte una o più condizioni. Le azioni possono includere l'invio di notifiche tramite posta elettronica o l'attivazione di un'azione di Microsoft Power Automate o un'azione webhook per l'invio dei dati ad altri servizi.

L'applicazione Monitoraggio consumo idrico creata prevede tre regole preconfigurate.

### <a name="view-rules"></a>Visualizzare le regole

1. Selezionare **Regole** nel riquadro sinistro.

   ![Riquadro Regole](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

1. Selezionare **High pH alert** (Avviso pH elevato), ossia una delle regole preconfigurate nell'applicazione.

     ![High pH alert](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    La regola `High flow alert` è configurata per controllare che la condizione `Acidity (pH)` sia `greater than` di `Max flow threshold`. Max flow threshold (Soglia massima flusso) è una proprietà cloud definita nel modello di dispositivo **Smart Valve** (Valvola intelligente). Il valore di `Max flow threshold` è impostato per istanza di dispositivo.

Si creerà ora un'azione di posta elettronica.

Per aggiungere un'azione alla regola:

1. Selezionare **+ Posta elettronica**.
1. Nel campo **Nome visualizzato** immettere **High water flow alert** (Avviso flusso acqua elevato) come nome visualizzato descrittivo dell'azione.
1. In **A** immettere l'indirizzo di posta elettronica associato all'account di Azure IoT Central.
1. Se si vuole, immettere una nota da includere nel testo del messaggio di posta elettronica.
1. Selezionare **Fine** per completare l'azione.
1. Selezionare **Salva** per salvare e attivare la nuova regola.

Quando la condizione configurata viene soddisfatta, si dovrebbe ricevere un messaggio di posta elettronica entro pochi minuti.

> [!NOTE]
> L'applicazione invia un messaggio ogni volta che viene soddisfatta una condizione. Selezionare **Disabilita** se non si vogliono più ricevere messaggi di posta elettronica dalla regola automatizzata.
  
Per creare una nuova regola:

* Selezionare **+ Nuovo** nella scheda **Regole** nel riquadro sinistro.

## <a name="configure-jobs"></a>Configurare i processi

In Azure IoT Central i processi consentono di attivare gli aggiornamenti delle proprietà del dispositivo o cloud su più dispositivi. Oltre alle proprietà, è possibile usare i processi anche per attivare i comandi di dispositivo su più dispositivi. Azure IoT Central automatizza il flusso di lavoro.

1. Selezionare **Processi** nel riquadro sinistro.
1. Selezionare **+ Nuovo** e configurare uno o più processi.

## <a name="customize-your-application"></a>Personalizzare l'applicazione

In qualità di autore, è possibile modificare diverse impostazioni per personalizzare l'esperienza utente nell'applicazione.

1. Selezionare **Amministrazione** > **Personalizzare l'applicazione**.
1. Per scegliere un'immagine da caricare come **logo dell'applicazione**, selezionare il pulsante **Cambia**.
1. Per scegliere un'immagine per l'**icona del browser** che verrà visualizzata nelle schede del browser, selezionare il pulsante **Cambia**.
1. In **Colori del browser** è anche possibile sostituire i colori predefiniti con codici colore esadecimali HTML.

   ![Selezioni per il logo dell'applicazione, l'icona del browser e i colori del browser](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1. È anche possibile cambiare le immagini dell'applicazione selezionando **Amministrazione** > **Impostazioni applicazione**. Per scegliere un'immagine da caricare come immagine dell'applicazione, selezionare il pulsante **Seleziona immagine**.
1. Infine, è anche possibile modificare il **tema** selezionando l'icona **Impostazioni** nell'angolo in alto a destra dell'applicazione.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminarla.

1. Selezionare **Amministrazione** nel riquadro sinistro dell'applicazione Azure IoT Central.
1. Selezionare **Impostazioni applicazione** e quindi **Elimina** nella parte inferiore della pagina.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su: 

> [!div class="nextstepaction"]
> [Concetti sul monitoraggio del consumo idrico](./concepts-waterconsumptionmonitoring-architecture.md).
