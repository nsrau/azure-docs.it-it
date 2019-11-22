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
ms.openlocfilehash: 0b100e0bebc7c5b26449e396650da5434334075d
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112628"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-in-iot-central"></a>Esercitazione: Creare un'applicazione di monitoraggio consumo idrico in IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Questa esercitazione illustra come creare un'applicazione di monitoraggio del consumo idrico di Azure IoT Central dal modello di applicazione Monitoraggio consumo idrico di IoT Central. 

Nel corso dell'esercitazione si apprenderà come: 

> [!div class="checklist"]
> * Usare il modello **Monitoraggio consumo idrico** di Azure IoT Central per creare un'applicazione di monitoraggio del consumo idrico
> * Esplorare e personalizzare il dashboard dell'operatore 
> * Esplorare il modello di dispositivo
> * Esplorare i dispositivi simulati
> * Esplorare e configurare le regole
> * Configurare i processi
> * Modificare la personalizzazione dell'applicazione tramite white-label

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario:
-  Una sottoscrizione di Azure (consigliata). Se non si ha una sottoscrizione di Azure, è possibile crearne una nella [pagina di iscrizione ad Azure](https://aka.ms/createazuresubscription).

## <a name="create-water-consumption-monitoring-app-in-iot-central"></a>Creare un'app di monitoraggio consumo idrico in IoT Central

In questa sezione si userà il modello **Monitoraggio consumo idrico** di Azure IoT Central per creare un'applicazione di monitoraggio del consumo idrico in IoT Central.

Per creare una nuova applicazione di monitoraggio consumo idrico:  

1. Passare alla [home page di Azure IoT Central](https://aka.ms/iotcentral).

    Se si ha una sottoscrizione di Azure, accedere con le credenziali usate per tale sottoscrizione, altrimenti accedere usando un account Microsoft:

    ![Immettere l'account dell'organizzazione](media/tutorial-waterconsumptionmonitoring/sign-in.png)

2. Fare clic su **Compila** nel riquadro sinistro e selezionare la scheda **Enti governativi**. Il riquadro Enti governativi mostra diversi modelli di applicazioni per enti pubblici.

   ![Compilare modelli di applicazione per enti pubblici](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. Selezionare il modello di applicazione **Monitoraggio consumo idrico**. Questo modello include un modello di dispositivo di monitoraggio del consumo idrico di esempio, dispositivi simulati, un dashboard dell'operatore e regole di monitoraggio preconfigurate.    

2. Fare clic su **Crea app** per aprire il modulo di creazione **Nuova applicazione**, che contiene i campi seguenti:
    * **Nome applicazione**: per impostazione predefinita, il nome dell'applicazione è *Monitoraggio consumo idrico* seguito da una stringa ID univoca generata da IoT Central. Se si preferisce, scegliere un nome applicazione descrittivo. È possibile cambiare il nome dell'applicazione anche in un secondo momento.
    * **URL**: IoT Central genererà automaticamente un URL univoco in base al nome dell'applicazione. È possibile scegliere di aggiornare l'URL in base alle esigenze. Si può anche cambiare l'URL in un secondo momento. 
    * Se si ha una sottoscrizione di Azure, immettere i valori relativi a *directory, sottoscrizione di Azure e area*. Se non si ha una sottoscrizione, è possibile abilitare la **versione di valutazione gratuita di 7 giorni** e completare le informazioni di contatto richieste.  

    Per altre informazioni su directory e sottoscrizioni, vedere la [guida introduttiva per la creazione di un'applicazione](../preview/quick-deploy-iot-central.md).

5. Fare clic sul pulsante **Crea** nella parte inferiore della pagina. 

    ![Pagina Crea applicazione di Azure IoT Central](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

6. È stata così creata un'applicazione di monitoraggio del consumo idrico usando il modello **Monitoraggio consumo idrico** di Azure IoT Central.

Congratulazioni! È stata completata la creazione dell'applicazione di monitoraggio del consumo idrico, che è preconfigurata con gli elementi seguenti:
* Dashboard operatore di esempio
* Modelli predefiniti di esempio di dispositivi di controllo dei flussi d'acqua e delle valvole
* Dispositivi simulati di controllo dei flussi d'acqua e delle valvole intelligenti
* Regole e processi preconfigurati
* Personalizzazione di esempio tramite white-label 

L'applicazione può essere modificata in qualsiasi momento. È quindi il momento di esplorare l'applicazione e personalizzarla.  

## <a name="explore-and-customize-operator-dashboard"></a>Esplorare e personalizzare il dashboard dell'operatore 
Dopo aver creato l'applicazione viene aperto il dashboard dell'operatore di esempio, denominato **Wide World water consumption dashboard** (Dashboard di monitoraggio consumo idrico Wide World).

   ![Dashboard di monitoraggio consumo idrico](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

Tra le attività di compilazione, è possibile creare e personalizzare le visualizzazioni nel dashboard per gli operatori. Prima di procedere con la personalizzazione, è opportuno esplorare il dashboard. 

> [!NOTE]
> Tutti i dati visualizzati nel dashboard sono basati sui dati dei dispositivi simulati, che verranno descritti nella sezione successiva. 
  
Il dashboard è costituito da diversi tipi di riquadri:

* **Riquadro dell'immagine dell'ente di gestione dell'acqua Wide World**: il primo riquadro nel dashboard è quello dell'immagine dell'ente di gestione dell'acqua fittizio Wide World. È possibile personalizzare il riquadro con la propria immagine o rimuoverlo del tutto. 

* **Riquadro dei KPI medi dei flussi d'acqua**: il riquadro dei KPI è configurato in modo da visualizzare come esempio *la media negli ultimi 30 minuti*. È possibile personalizzare i riquadri dei KPI e impostarli su un tipo e un intervallo di tempo diversi.

* I riquadri dei *comandi del dispositivo* nel dashboard contengono **Close valve** (Chiudi valvola), **Open valve** (Apro valvola) e **Set valve position** (Imposta posizione valvola). Facendo clic sui comandi verrà visualizzata la pagina dei comandi del dispositivo simulato. In IoT Central un *comando* è un tipo di *funzionalità di dispositivo* che verrà illustrata più avanti nella **sezione relativa ai modelli di dispositivo** di questa esercitazione.

*  **Mappa dell'area di distribuzione dell'acqua**: la mappa usa Mappe di Azure, che è possibile configurare direttamente in Azure IoT Central. Il riquadro della mappa visualizza la posizione del dispositivo. Passare il puntatore del mouse sulla mappa e provare i controlli sulla mappa, ad esempio *zoom avanti*, *zoom indietro* o *espansione*. 

    ![Mappa del dashboard di monitoraggio consumo idrico](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* **Grafico a linee del flusso medio delle acque** e **grafico a linee delle condizioni ambientali**: è possibile visualizzare i dati di telemetria di uno o più dispositivi tracciati come grafico a linee su un intervallo di tempo desiderato.  

* **Grafico della mappa termica della pressione media delle valvole**: è possibile scegliere il tipo di visualizzazione mappa termica dei dati di telemetria del dispositivo di cui si è interessati a esaminare la distribuzione in un intervallo di tempo con un indice colori.

* **Riquadro di reimpostazione delle soglie di avviso**: è possibile includere riquadri di contenuti di invito all'azione con un collegamento incorporato a una pagina d'azione. In questo caso la reimpostazione della soglia di avviso indirizzerà alla pagina **Processi** dell'applicazione, in cui è possibile aggiornare le proprietà dei dispositivi e che verrà illustrata più avanti nella sezione **Configurare i processi** di questa esercitazione.

* **Riquadri delle proprietà**: il dashboard visualizza le proprietà del dispositivo **Valve operational info** (Informazioni operative valvole), **Flow alert thresholds** (Soglie di avviso flusso) e **Maintenance info** (Informazioni di manutenzione).  


### <a name="customize-dashboard"></a>Personalizzare il dashboard 

Tra le attività di compilazione, è possibile personalizzare le visualizzazioni nel dashboard per gli operatori. Provare questa procedura:
1. Fare clic su **Modifica** per personalizzare il **dashboard di monitoraggio consumo idrico Wide World**. È possibile personalizzare il dashboard facendo clic sul menu **Modifica**. Una volta impostato il dashboard in modalità di **modifica**, è possibile aggiungere nuovi riquadri o configurare quelli esistenti. 

     ![Dashboard in modalità di modifica](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

2. Fare clic su **+ Nuovo** per creare un nuovo dashboard e configurarlo da zero. È possibile creare più dashboard e spostarsi tra di essi dal menu Dashboard. 

## <a name="explore-device-template"></a>Esplorare il modello di dispositivo
Un modello di dispositivo in Azure IoT Central definisce le funzionalità di un dispositivo, che possono essere costituite da telemetria, proprietà e comandi. A livello di compilazione, in IoT Central è possibile definire uno o più modelli di dispositivo che rappresentano le funzionalità dei dispositivi connessi. 
 

L'applicazione **Monitoraggio consumo idrico** ha due modelli di dispositivo di riferimento che rappresentano un dispositivo *misuratore del flusso* e un dispositivo di *controllo delle valvole intelligenti*. 

Per visualizzare il modello di dispositivo:

1. Fare clic su **Modelli di dispositivo** nel riquadro di spostamento sinistro dell'applicazione in IoT Central. 
    Nell'elenco Modelli di dispositivo sono presenti due modelli di dispositivo: **Flow meter** (Misuratore flusso) e **Smart Valve** (Valvola intelligente)

   ![Modello di dispositivo](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

2. Fare clic sul modello di dispositivo **Flow meter** (Misuratore flusso) e acquisire familiarità con le funzionalità del dispositivo 

     ![Modello di dispositivo Flow meter (Misuratore flusso)](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customizing-the-device-template"></a>Personalizzazione del modello di dispositivo

Provare a personalizzare gli elementi seguenti:
1. Passare a **Personalizza** nel menu del modello di dispositivo
2. Trovare il tipo di dati di telemetria `Temperature`
3. Aggiornare il campo **Nome visualizzato** da `Temperature` a `Reported temperature`
4. Aggiornare l'unità di misura oppure impostare *Valore minimo* e *Valore massimo*
5. Selezionare **Salva** per salvare le modifiche 

### <a name="add-a-cloud-property"></a>Aggiungere una proprietà cloud 
1. Passare a **Proprietà cloud** nel menu del modello di dispositivo
2. Aggiungere una nuova proprietà cloud facendo clic su **+ Aggiungi proprietà cloud**. 
    In IoT Central è possibile aggiungere una proprietà rilevante per il dispositivo. Un esempio di una proprietà cloud può essere una soglia di avviso specifica per l'area di installazione, informazioni sulle risorse, informazioni di manutenzione e così via. 
3. Selezionare **Salva** per salvare le modifiche 
 
### <a name="views"></a>Visualizzazioni 
Il modello di dispositivo di monitoraggio consumo idrico è dotato di visualizzazioni predefinite. Esplorare le visualizzazioni e, se necessario, aggiornarle. Le visualizzazioni definiscono il modo in cui gli operatori vedranno i dati del dispositivo, ma anche come immetteranno le proprietà cloud. 

  ![Visualizzazioni del modello di dispositivo](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>Pubblica 
Se sono state apportate modifiche, assicurarsi di selezionare **Pubblica** per pubblicare il modello di dispositivo. 

### <a name="create-a-new-device-template"></a>Creare un nuovo modello di dispositivo 
- Selezionare **+ Nuovo** per creare un nuovo modello di dispositivo e seguire il processo di creazione. Sarà possibile creare un modello di dispositivo personalizzato da zero oppure sceglierne uno dal catalogo dei dispositivi di Azure. 

## <a name="explore-simulated-devices"></a>Esplorare i dispositivi simulati
In IoT Central è possibile creare dispositivi simulati per testare il modello di dispositivo e l'applicazione. L'applicazione **Monitoraggio consumo idrico** ha due dispositivi simulati mappati ai modelli di dispositivo *Flow meter* (Misuratore flusso) e *Smart Valve* (Valvola intelligente) 

### <a name="to-view-the-devices"></a>Per visualizzare i dispositivi:
1. Passare a **Dispositivi** nel riquadro di spostamento a sinistra di IoT Central. 

   ![Dispositivi](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

2. Fare clic su **Smart Valve 1** (Valvola intelligente 1) 

    ![Dispositivo 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

3.  Nella scheda **Comandi** è possibile vedere i tre comandi del dispositivo *Open Valve* (Apri valvola), *Close Valve* (Chiudi valvola) e *Set Valve position* (Imposta posizione valvola), che sono funzionalità definite nel modello di dispositivo *Smart Valve* (Valvola intelligente). 
4. Esplorare le schede **Proprietà dispositivo** e **Device Dashboard** (Dashboard dispositivo). 

> [!NOTE]
> Si noti che tutte le schede sono state configurate dalle visualizzazioni del modello di dispositivo.

### <a name="add-new-devices"></a>Aggiungere nuovi dispositivi
* Aggiungere nuovi dispositivi facendo clic su **+ Nuovo** nella scheda **Dispositivi**. 

## <a name="explore-and-configure-rules"></a>Esplorare e configurare le regole

In Azure IoT Central è possibile creare regole per monitorare automaticamente i dati di telemetria del dispositivo e attivare azioni quando vengono soddisfatte una o più condizioni. Le azioni possono includere l'invio di notifiche tramite posta elettronica o l'attivazione di un'azione di Microsoft Flow o un'azione webhook per l'invio dei dati ad altri servizi.

L'applicazione **Monitoraggio consumo idrico** creata ha tre regole preconfigurate.

### <a name="to-view-rules"></a>Per visualizzare le regole:
1. Passare a **Regole** nel riquadro di spostamento a sinistra di IoT Central. 

   ![Regole](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

2. Selezionare **High water flow alert** (Avviso flusso acqua elevato), ossia una delle regole preconfigurate nell'applicazione.

     ![High water flow alert (Avviso flusso acqua elevato)](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    La regola `High flow alert` è configurata per controllare che la condizione `Acidity (pH)` sia `greater than` di `Max flow threshold`. Max flow threshold (Soglia massima flusso) è una proprietà cloud definita nel modello di dispositivo *Smart Valve* (Valvola intelligente). Il valore di `Max flow threshold` è impostato per istanza di dispositivo. 

Si creerà ora un'azione di posta elettronica.

Per aggiungere un'azione alla regola:

1. Selezionare **+ Posta elettronica**. 
1. Nel campo *Nome visualizzato* immettere **High water flow alert** (Avviso flusso acqua elevato) come nome visualizzato descrittivo dell'azione.
    * In **A** immettere l'indirizzo di posta elettronica associato all'account di IoT Central. 
1. Facoltativamente, immettere una nota da includere nel testo del messaggio di posta elettronica.
1. Selezionare **Fine** per completare l'azione.
1. Selezionare **Salva** per salvare e attivare la nuova regola. 

Quando la **condizione** configurata viene soddisfatta, si dovrebbe ricevere un messaggio di posta elettronica dopo pochi minuti.

> [!NOTE]
> L'applicazione invierà un messaggio ogni volta che viene soddisfatta una condizione. Selezionare **Disabilita** se non si vogliono più ricevere messaggi di posta elettronica dalla regola automatica. 
  
Per creare una nuova regola: 
- Selezionare **+ Nuovo** in **Regole** nel riquadro di spostamento a sinistra.

## <a name="configure-jobs"></a>Configurare i processi

In IoT Central i processi consentono di attivare gli aggiornamenti delle proprietà del dispositivo o cloud su più dispositivi. Oltre alle proprietà, è possibile usare i processi anche per attivare i comandi di dispositivo su più dispositivi. IoT Central automatizzerà il flusso di lavoro. 

1. Passare a **Processi** nel riquadro di spostamento a sinistra. 
2. Fare clic su **+ Nuovo** e configurare uno o più processi. 


## <a name="customize-your-application"></a>Personalizzare l'applicazione 
In qualità di autore, è possibile modificare diverse impostazioni per personalizzare l'esperienza utente nell'applicazione.

1.  Passare a **Amministrazione > Personalizzare l'applicazione**.
3. Usare il pulsante **Cambia** per scegliere un'immagine da caricare come **logo dell'applicazione**.
4. Usare il pulsante **Cambia** per scegliere un'immagine per l'**icona del browser** che verrà visualizzata nelle schede del browser.
5. In **Colori del browser** è anche possibile sostituire i colori predefiniti con codici colore esadecimali HTML.

   ![Personalizzazione dell'applicazione in Azure IoT Central](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1.  È anche possibile cambiare l'immagine dell'applicazione passando a **Amministrazione > Impostazioni applicazione** e selezionando il pulsante **Seleziona immagine** per scegliere un'immagine da caricare come immagine dell'applicazione. 
2. Infine, si può anche cambiare il **tema** facendo clic su **Impostazioni** nella testata dell'applicazione. 

  
## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminarla seguendo questa procedura:

1. Aprire la scheda Amministrazione nel riquadro sinistro dell'applicazione IoT Central. 
2. Selezionare Impostazioni applicazione e fare clic sul pulsante Elimina nella parte inferiore della pagina. 


## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sui [concetti di monitoraggio del consumo idrico](./concepts-waterconsumptionmonitoring-architecture.md)
