---
title: Esercitazione sul modello di app Centro di micro-adempimenti | Microsoft Docs
description: Esercitazione sul modello di applicazione Centro di micro-adempimenti per Azure IoT Central
author: avneet723
ms.author: avneets
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 01/09/2020
ms.openlocfilehash: 28afa98313ef1c9577e90ed161a1d20cbf5faf75
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91440202"
---
# <a name="tutorial-deploy-and-walk-through-a-micro-fulfillment-center-application-template"></a>Esercitazione: Distribuire ed esaminare un modello di applicazione Centro di micro-adempimenti

In questa esercitazione si userà il modello di applicazione Centro di micro-adempimenti di Azure IoT Central per creare una soluzione per la vendita al dettaglio. Si apprenderà come distribuire il modello, che cosa contiene e quali operazioni è opportuno eseguire successivamente.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa serie di esercitazioni, è necessaria una sottoscrizione di Azure. Facoltativamente, è possibile usare una versione di valutazione gratuita valida per 7 giorni. Se non si ha una sottoscrizione di Azure, è possibile crearne una nella [pagina di iscrizione ad Azure](https://aka.ms/createazuresubscription).

## <a name="create-an-application"></a>Creare un'applicazione 
In questa sezione viene creata una nuova applicazione Azure IoT Central da un modello. Questa applicazione verrà usata in tutta la serie di esercitazioni per creare una soluzione completa.

Per creare una nuova applicazione Azure IoT Central:

1. Passare al [sito Web di gestione delle applicazioni Azure IoT Central](https://aka.ms/iotcentral).
1. Se si ha una sottoscrizione di Azure, accedere con le credenziali usate per tale sottoscrizione, in caso contrario accedere con un account Microsoft:

   ![Screenshot della finestra di dialogo per l'accesso con un account Microsoft](./media/tutorial-in-store-analytics-create-app/sign-in.png)

1. Per iniziare a creare una nuova applicazione Azure IoT Central, selezionare **Nuova applicazione**.

1. Selezionare **Vendita al dettaglio**.  Nella pagina relativa alla vendita al dettaglio sono visualizzati diversi modelli di applicazione di vendita al dettaglio.

Per creare una nuova applicazione Centro di micro-adempimenti che usa le funzionalità di anteprima:  
1. Selezionare il modello di applicazione **Centro di micro-adempimenti**. Questo modello include i modelli di tutti i dispositivi usati nell'esercitazione. Offre anche un dashboard operatore per il monitoraggio delle condizioni all'interno del centro di adempimenti, nonché delle condizioni dei trasportatori robotizzati. 

    ![Screenshot della pagina Crea un'applicazione IoT di Azure IoT Central](./media/tutorial-micro-fulfillment-center-app/iotc-retail-homepage-mfc.png)
    
1. Facoltativamente, scegliere un **nome applicazione** descrittivo. Il modello di applicazione si basa sulla società fittizia Northwind Traders. 

    >[!NOTE]
    >Se si usa un nome applicazione descrittivo, è comunque necessario usare un valore univoco per l'URL dell'applicazione.

1. Se si ha una sottoscrizione di Azure, immettere la directory, la sottoscrizione di Azure e l'area. Se non si ha una sottoscrizione, è possibile abilitare la versione di valutazione gratuita di 7 giorni e completare le informazioni di contatto necessarie.  

    Per altre informazioni su directory e sottoscrizioni, vedere la guida di avvio rapido [Creare un'applicazione Azure IoT Central (funzionalità di anteprima)](../preview/quick-deploy-iot-central.md).

1. Selezionare **Create** (Crea).

    ![Screenshot della pagina Nuova applicazione di Azure IoT Central](./media/tutorial-micro-fulfillment-center-app/iotc-retail-create-app-mfc.png)

## <a name="walk-through-the-application"></a>Esaminare l'applicazione 

Al termine della distribuzione del modello di app, verrà visualizzato il **dashboard del centro di micro-adempimenti di Northwind Traders**. Northwind Traders è un rivenditore fittizio che ha un centro di micro-adempimenti gestito in questa applicazione Azure IoT Central. Il dashboard operatore mostra le informazioni e i dati di telemetria relativi ai dispositivi inclusi nel modello nonché un set di comandi, processi e azioni che è possibile eseguire. Il dashboard presenta una divisione logica in due sezioni. A sinistra è possibile monitorare le condizioni ambientali all'interno della struttura di adempimenti, mentre a destra si può monitorare l'integrità di un trasportatore robotizzato all'interno della struttura.  

Nel dashboard è possibile eseguire queste operazioni:
   * Visualizzare i dati di telemetria dei dispositivi, come il numero di prelievi, il numero di ordini elaborati e proprietà come lo stato del sistema della struttura.  
   * Visualizzare la planimetria e la posizione dei trasportatori robotizzati all'interno della struttura di adempimenti.
   * Attivare comandi, ad esempio la reimpostazione del sistema di controllo, l'aggiornamento del firmware dei trasportatori e la riconfigurazione della rete.

     ![Screenshot della metà superiore del dashboard del centro di micro-adempimenti di Northwind Traders.](./media/tutorial-micro-fulfillment-center-app/mfc-dashboard1.png)
   * Visualizzare un esempio del dashboard che può essere usato da un operatore per monitorare le condizioni all'interno del centro di adempimenti. 
   * Monitorare l'integrità dei payload in esecuzione nel dispositivo gateway all'interno del centro di adempimenti.    

     ![Screenshot della metà inferiore del dashboard del centro di micro-adempimenti di Northwind Traders.](./media/tutorial-micro-fulfillment-center-app/mfc-dashboard2.png)

## <a name="device-template"></a>Modello di dispositivo
Se si seleziona la scheda Modelli di dispositivo, si noterà che il modello include due tipi diversi di dispositivo: 
   * **Operatore robotico**: questo modello di dispositivo rappresenta la definizione di un trasportatore robotizzato funzionante che è stato distribuito nella struttura di adempimenti ed esegue le operazioni appropriate di immagazzinamento e recupero. Se si seleziona il modello, si noterà che il robot invia dati sul dispositivo, come la temperatura e la posizione dell'asse, e proprietà come lo stato del trasportatore robotizzato. 
   * **Monitoraggio delle condizioni della struttura**: questo modello di dispositivo rappresenta una raccolta di dispositivi che consente di monitorare le condizioni ambientali, nonché il dispositivo gateway che ospita vari carichi di lavoro perimetrali per supportare il centro di adempimenti. Il dispositivo invia dati di telemetria come la temperatura, il numero di prelievi e il numero di ordini, nonché informazioni sullo stato e l'integrità dei carichi di lavoro di calcolo in esecuzione nell'ambiente. 

     ![Modelli di dispositivo in Centro di micro-adempimenti](./media/tutorial-micro-fulfillment-center-app/device-templates.png)

Se si seleziona la scheda Gruppi di dispositivi, si noterà anche che per questi modelli di dispositivo vengono creati automaticamente gruppi di dispositivi.

## <a name="rules"></a>Regole
Nella scheda **Regole** si noterà che il modello di applicazione include una regola di esempio per il monitoraggio delle condizioni di temperatura del trasportatore robotizzato. È possibile usare questa regola per avvisare l'operatore se un robot specifico nella struttura si sta surriscaldando e deve essere portato offline per la manutenzione. 

Usare la regola di esempio come spunto per definire regole più appropriate per le funzioni dell'azienda.

![Screenshot della scheda Regole](./media/tutorial-micro-fulfillment-center-app/rules.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminare il modello di applicazione. Passare ad **Amministrazione** > **Impostazioni dell'applicazione** e selezionare **Elimina**.

![Screenshot della pagina Impostazioni dell'applicazione in Centro di micro-adempimenti](./media/tutorial-micro-fulfillment-center-app/delete.png)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su:
> [!div class="nextstepaction"]
> [Architettura della soluzione del centro di micro-adempimenti](./architecture-micro-fulfillment-center.md)
* Altre informazioni sui [modelli per la vendita al dettaglio di Azure IoT Central](./overview-iot-central-retail.md)
* Altre informazioni con una [panoramica di Azure IoT Central](../preview/overview-iot-central.md)
