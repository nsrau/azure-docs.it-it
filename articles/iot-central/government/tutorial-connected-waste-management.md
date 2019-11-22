---
title: "Esercitazione: Creare un'app di gestione rifiuti connessa con Azure IoT Central"
description: "Esercitazione: Informazioni su come creare un'applicazione di gestione rifiuti connessa usando i modelli di applicazione di Azure IoT Central."
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 38da444779a56f39d4119f3797ddb5bd2f31aeae
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112649"
---
# <a name="tutorial-create-a-connected-waste-management-application-in-iot-central"></a>Esercitazione: Creare un'applicazione di gestione rifiuti connessa in IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Questa esercitazione illustra come creare un'applicazione di gestione rifiuti connessa di Azure IoT Central dal modello di applicazione IoT Central **Gestione rifiuti connessa**. 

Nel corso dell'esercitazione si apprenderà come: 

> [!div class="checklist"]
> * Usare il modello **Gestione rifiuti connessa** di Azure IoT Central per creare un'applicazione di gestione rifiuti connessa
> * Esplorare e personalizzare il dashboard dell'operatore 
> * Esplorare il modello di dispositivo cassonetto connesso
> * Esplorare i dispositivi simulati
> * Esplorare e configurare le regole
> * Configurare i processi
> * Modificare la personalizzazione dell'applicazione tramite white-label

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario:
-  Una sottoscrizione di Azure (consigliata). Facoltativamente, è possibile usare una versione di valutazione gratuita valida per 7 giorni. Se non si ha una sottoscrizione di Azure, è possibile crearne una nella [pagina di iscrizione ad Azure](https://aka.ms/createazuresubscription).

## <a name="create-connected-waste-management-app-in-iot-central"></a>Creare l'app di gestione rifiuti connessa in IoT Central

In questa sezione si userà il modello **Gestione rifiuti connessa** di Azure IoT Central per creare un'applicazione di gestione rifiuti connessa in IoT Central.

Per creare una nuova applicazione di gestione dei rifiuti connessa in Azure IoT Central:  

1. Passare alla [home page di Azure IoT Central](https://aka.ms/iotcentral).

    Se si ha una sottoscrizione di Azure, accedere con le credenziali usate per tale sottoscrizione, altrimenti accedere usando un account Microsoft:

    ![Immettere l'account dell'organizzazione](./media/tutorial-connectedwastemanagement/sign-in.png)

2. Fare clic su **Compila** nel riquadro sinistro e selezionare la scheda **Enti governativi**. Il riquadro Enti governativi mostra diversi modelli di applicazioni per enti pubblici.

    ![Compilare modelli di applicazione per enti pubblici](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. Selezionare il modello di applicazione **Gestione rifiuti connessa**. Questo modello include un modello di dispositivo cassonetto connesso di esempio, dispositivi simulati, un dashboard dell'operatore e regole di monitoraggio preconfigurate.    

2. Fare clic su **Crea app** per aprire il modulo di creazione **Nuova applicazione**, che contiene i campi seguenti:
    * **Nome applicazione**. Il nome predefinito dell'applicazione è *Gestione rifiuti connessa*, seguito da una stringa ID univoca generata da IoT Central. Se si preferisce, scegliere un nome applicazione descrittivo. È possibile cambiare il nome dell'applicazione anche in un secondo momento.
    * **URL**. Facoltativamente, è possibile scegliere l'URL desiderato. Si può anche cambiare l'URL in un secondo momento. 
    * Se si ha una sottoscrizione di Azure, immettere i valori relativi a *directory, sottoscrizione di Azure e area*. Se non si ha una sottoscrizione, è possibile abilitare la **versione di valutazione gratuita di 7 giorni** e completare le informazioni di contatto richieste.  

    Per altre informazioni su directory e sottoscrizioni, vedere la [guida introduttiva per la creazione di un'applicazione](../preview/quick-deploy-iot-central.md).

5. Fare clic sul pulsante **Crea** nella parte inferiore della pagina. 

    ![Pagina di creazione dell'applicazione Gestione rifiuti connessa di Azure IoT Central](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)

6. È stata così creata un'app di gestione rifiuti connessa con il modello **Gestione rifiuti connessa** di Azure IoT Central. 

Congratulazioni! L'applicazione appena creata è preconfigurata con gli elementi seguenti:
* Dashboard operatore di esempio
* Modelli predefiniti di dispositivo cassonetto connesso di esempio
* Dispositivi cassonetto connesso simulati
* Regole e processi preconfigurati
* Personalizzazione di esempio tramite white-label 

L'applicazione può essere modificata in qualsiasi momento. È quindi il momento di esplorare l'applicazione e personalizzarla.  

## <a name="explore-and-customize-operator-dashboard"></a>Esplorare e personalizzare il dashboard dell'operatore 
Dopo la creazione dell'applicazione, si apre il **dashboard di Gestione rifiuti connessa Wide World**.

   ![Dashboard di Gestione rifiuti connessa](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

Tra le attività di compilazione, è possibile creare e personalizzare le visualizzazioni nel dashboard per gli operatori. Prima di procedere con la personalizzazione, è opportuno esplorare il dashboard. 

> [!NOTE]
> Tutti i dati visualizzati nel dashboard sono basati sui dati del dispositivo simulato, descritti nella sezione successiva. 

Il dashboard è costituito da diversi tipi di riquadri:

* ***Riquadro dell'immagine dell'ente di gestione rifiuti Wide World***: il primo riquadro nel dashboard è quello dell'immagine dell'ente di gestione rifiuti fittizio Wide World. È possibile personalizzare il riquadro con la propria immagine o rimuoverlo del tutto. 

* ***Riquadro dell'immagine del cassonetto***: è possibile usare riquadri di immagini e contenuti per creare una rappresentazione visiva del dispositivo monitorato insieme a del testo descrittivo. 

* ***Riquadro dei KPI del livello di colmo***: il riquadro visualizza un valore segnalato da un sensore del *livello di colmo* di un cassonetto. Il sensore *Fill level* (Livello di colmo) e altri sensori come *Odor meter* (Misuratore odori) o *Weight* (Peso) in un cassonetto possono essere monitorati in remoto. Un operatore può eseguire un'azione, ad esempio inviare un camion della raccolta di rifiuti. 

*  ***Mappa dell'area di monitoraggio rifiuti***: la mappa usa Mappe di Azure, che è possibile configurare direttamente in Azure IoT Central. Il riquadro della mappa visualizza la posizione del dispositivo. Passare il puntatore del mouse sulla mappa e provare i controlli sulla mappa, ad esempio zoom avanti, zoom indietro o espansione.

     ![Mappa del dashboard di Gestione rifiuti connessa](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* ***Grafico a barre dei livelli di colmo, odori e peso**: è possibile visualizzare i dati di telemetria di uno o più dispositivi in un grafico a barre. È anche possibile espandere il grafico a barre.  

  ![Grafico a barre del dashboard di Gestione rifiuti connessa](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Riquadro dei contenuti di Field Service**: il dashboard include un collegamento per l'integrazione con Dynamics 365 for Field Service dall'applicazione di Azure IoT Central. Ad esempio, è possibile usare Dynamics 365 for Field Service per creare ticket per l'invio di servizi di raccolta rifiuti. 


### <a name="customize-dashboard"></a>Personalizzare il dashboard 

Tra le attività di compilazione, è possibile personalizzare le visualizzazioni nel dashboard per gli operatori. Provare questa procedura:
1. Fare clic su **Modifica** per personalizzare il **dashboard di gestione rifiuti connessa Wide World**. È possibile personalizzare il dashboard facendo clic sul menu **Modifica**. Una volta impostato il dashboard in modalità di **modifica**, è possibile aggiungere nuovi riquadri o configurare quelli esistenti. 

    ![Dashboard in modalità di modifica](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

2. È anche possibile fare clic su **+ Nuovo** per creare un nuovo dashboard e configurarlo da zero. È possibile creare più dashboard e spostarsi tra di essi dal menu Dashboard. 

## <a name="explore-connected-waste-bin-device-template"></a>Esplorare il modello di dispositivo cassonetto connesso

Un modello di dispositivo in Azure IoT Central definisce le funzionalità di un dispositivo, che possono essere costituite da telemetria, proprietà e comandi. A livello di compilazione, è possibile definire modelli di dispositivo che rappresentano le funzionalità dei dispositivi connessi. 
 

L'applicazione **Gestione rifiuti connessa** include un modello di dispositivo cassonetto connesso di esempio.

Per visualizzare il modello di dispositivo:

1. Fare clic su **Modelli di dispositivo** nel riquadro di spostamento sinistro dell'applicazione in IoT Central. 

    ![Modello di dispositivo](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

2. Nell'elenco Modelli di dispositivo è presente il modello **Connected Waste Bin** (Cassonetto connesso). Aprirlo facendo clic sul nome.

3. Acquisire familiarità con le funzionalità del modello di dispositivo. Come si può vedere, il modello definisce sensori come *Fill level* (Livello di colmo), *Odor meter* (Misuratore odori), *Weight* (Peso), *Location* (Posizione) e altri.

   ![Modello di dispositivo](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)

   

### <a name="customizing-the-device-template"></a>Personalizzazione del modello di dispositivo

Provare a personalizzare gli elementi seguenti:
1. Passare a **Personalizza** nel menu del modello di dispositivo
2. Trovare il tipo di dati di telemetria `Odor meter`
3. Aggiornare il campo **Nome visualizzato** da `Odor meter` a `Odor level`
4. Si può anche provare a cambiare l'unità di misura oppure impostare *Valore minimo* e *Valore massimo*
5. Selezionare **Salva** per salvare le modifiche 

### <a name="add-a-cloud-property"></a>Aggiungere una proprietà cloud 

1. Passare a **Proprietà cloud** nel menu del modello di dispositivo
2. Aggiungere una nuova proprietà cloud facendo clic su **+ Aggiungi proprietà cloud**. In IoT Central è possibile aggiungere una proprietà rilevante per il dispositivo, ma che non si prevede venga inviata da un dispositivo. Un esempio di una proprietà cloud può essere una soglia di avviso specifica per l'area di installazione, informazioni sulle risorse, informazioni di manutenzione e così via. 
3. Selezionare **Salva** per salvare le modifiche 
 
### <a name="views"></a>Visualizzazioni 
* Il modello di dispositivo cassonetto connesso include visualizzazioni predefinite. Esplorare le visualizzazioni e, se necessario, aggiornarle. Le visualizzazioni definiscono il modo in cui gli operatori vedranno i dati del dispositivo, ma anche come immetteranno le proprietà cloud. 

  ![Visualizzazioni del modello di dispositivo](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>Pubblica 

* Se sono state apportate modifiche, assicurarsi di selezionare **Pubblica** per pubblicare il modello di dispositivo. 

### <a name="create-a-new-device-template"></a>Creare un nuovo modello di dispositivo 

* Selezionare **+ Nuovo** per creare un nuovo modello di dispositivo e seguire il processo di creazione. Sarà possibile creare un modello di dispositivo personalizzato da zero oppure sceglierne uno dal catalogo dei dispositivi di Azure. 

## <a name="explore-simulated-devices"></a>Esplorare i dispositivi simulati

In IoT Central è possibile creare dispositivi simulati per testare il modello di dispositivo e l'applicazione. 

L'applicazione **Gestione rifiuti connessa** è dotata di due dispositivi simulati mappati al modello di dispositivo cassonetto connesso. 

### <a name="to-view-the-devices"></a>Per visualizzare i dispositivi:

1. Passare a **Dispositivi** nel riquadro di spostamento a sinistra di IoT Central. 

   ![Dispositivi](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

2. Selezionare e fare clic sul dispositivo Connected Waste Bin (Cassonetto connesso).  

     ![Dispositivo 1](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

3. Passare alla scheda **Proprietà cloud** e provare ad aggiornare il valore di `Bin full alert threshold` da `95` a `100`. 
* Esplorare le schede **Proprietà dispositivo** e **Device Dashboard** (Dashboard dispositivo). 

> [!NOTE]
> Si noti che tutte le schede sono state configurate dalle **visualizzazioni del modello di dispositivo**.

### <a name="add-new-devices"></a>Aggiungere nuovi dispositivi

* È possibile aggiungere nuovi dispositivi facendo clic su **+ Nuovo** nella scheda **Dispositivi**. 

## <a name="explore-and-configure-rules"></a>Esplorare e configurare le regole

In Azure IoT Central è possibile creare regole per monitorare automaticamente i dati di telemetria del dispositivo e attivare azioni quando vengono soddisfatte una o più condizioni. Le azioni possono includere l'invio di notifiche tramite posta elettronica, l'attivazione di un'azione di Microsoft Flow o un'azione webhook per l'invio dei dati ad altri servizi.

L'applicazione **Gestione rifiuti connessa** ha quattro regole di esempio.

### <a name="to-view-rules"></a>Per visualizzare le regole:
1. Passare a **Regole** nel riquadro di spostamento a sinistra di IoT Central.

   ![Regole](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

2. Selezionare **Bin full alert** (Avviso cassonetto pieno)

     ![Bin full alert (Avviso cassonetto pieno)](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 3. La regola `Bin full alert` controlla quando la **condizione** `Fill level is greater than or equal to Bin full alert threshold`.

    `Bin full alert threshold` è una *proprietà cloud* definita nel modello di dispositivo `Connected waste bin`. 

Si creerà ora un'azione di posta elettronica.

### <a name="create-an-email-action"></a>Creare un'azione di posta elettronica
Per configurare un'azione di posta elettronica nell'elenco di azioni della regola:
1. Selezionare **+ Posta elettronica**. 
2. Nel campo *Nome visualizzato* immettere **Bin full alert** (Avviso cassonetto pieno) come nome visualizzato descrittivo dell'azione.
3. In **A** immettere l'indirizzo di posta elettronica associato all'account di IoT Central. 
4. Facoltativamente, immettere una nota da includere nel testo del messaggio di posta elettronica.
5. Selezionare **Fine** per completare l'azione.
6. Selezionare **Salva** per salvare e attivare la nuova regola. 

Quando la **condizione** configurata viene soddisfatta, si dovrebbe ricevere un messaggio di posta elettronica.

> [!NOTE]
> L'applicazione invierà un messaggio ogni volta che viene soddisfatta una condizione. Selezionare **Disabilita** se non si vogliono più ricevere messaggi di posta elettronica dalla regola automatica. 
  
Per creare una nuova regola: 
1. Selezionare **+ Nuovo** in **Regole** nel riquadro di spostamento a sinistra.

## <a name="configure-jobs"></a>Configurare i processi

In IoT Central i processi consentono di attivare gli aggiornamenti delle proprietà del dispositivo o cloud su più dispositivi. Oltre alle proprietà, è possibile usare i processi anche per attivare i comandi di dispositivo su più dispositivi. IoT Central automatizzerà il flusso di lavoro. 

1. Passare a **Processi** nel riquadro di spostamento a sinistra. 
2. Fare clic su **+ Nuovo** e configurare uno o più processi. 


## <a name="customize-your-application"></a>Personalizzare l'applicazione 

In qualità di autore, è possibile modificare diverse impostazioni per personalizzare l'esperienza utente nell'applicazione.

### <a name="to-change-the-application-theme"></a>Per cambiare il tema dell'applicazione:

1. Passare a **Amministrazione > Personalizzare l'applicazione**.
3. Usare il pulsante **Cambia** per scegliere un'immagine da caricare come **logo dell'applicazione**.
4. Usare il pulsante **Cambia** per scegliere un'immagine per l'**icona del browser** che verrà visualizzata nelle schede del browser.
5. In **Colori del browser** è anche possibile sostituire i colori predefiniti con codici colore esadecimali HTML.

   ![Personalizzazione dell'applicazione in Azure IoT Central](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

6. È anche possibile cambiare l'immagine dell'applicazione passando a **Amministrazione > Impostazioni applicazione** e selezionando il pulsante **Seleziona immagine** per scegliere un'immagine da caricare come immagine dell'applicazione.
7. Infine, si può anche cambiare il **tema** facendo clic su **Impostazioni** nella testata dell'applicazione.

  
## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminarla seguendo questa procedura:

1. Aprire la scheda Amministrazione nel riquadro sinistro dell'applicazione IoT Central.
2. Selezionare Impostazioni applicazione e fare clic sul pulsante Elimina nella parte inferiore della pagina.

  

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sui [concetti di gestione rifiuti connessa](./concepts-connectedwastemanagement-architecture.md)
