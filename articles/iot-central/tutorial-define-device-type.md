---
title: Definire un nuovo tipo di dispositivo in Azure IoT Central | Microsoft Docs
description: Questa esercitazione illustra come definire un nuovo tipo di dispositivo nell'applicazione Azure IoT Central. L'utente definisce i dati di telemetria, lo stato, le proprietà e le impostazioni per il tipo.
author: dominicbetts
ms.author: dobett
ms.date: 01/28/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: e4c5942dfba62a2c869e4eeceb9018fc926d9de9
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259543"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application-new-ui-design"></a>Esercitazione: Definire un nuovo tipo di dispositivo nell'applicazione Azure IoT Central (nuova interfaccia utente)

Questa esercitazione illustra come usare un modello di dispositivo per definire un nuovo tipo di dispositivo nell'applicazione Microsoft Azure IoT Central. Un modello di dispositivo definisce i dati di telemetria, lo stato, le proprietà e le impostazioni per il tipo di dispositivo.

Per permettere all'utente di testare l'applicazione prima di collegare un dispositivo vero e proprio, IoT Central genera un dispositivo simulato dal modello quando lo si crea.

In questa esercitazione, si crea un modello di dispositivo per un **condizionatore connesso**. Un condizionatore connesso:

* Invia dati di telemetria, ad esempio temperatura e umidità.
* Segnala lo stato, ad esempio se è acceso o spento.
* Ha proprietà del dispositivo come il numero di serie e la versione del firmware.
* Ha impostazioni come la temperatura di destinazione.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un nuovo modello di dispositivo
> * Aggiungere dati di telemetria al dispositivo
> * Visualizzare dati di telemetria simulati
> * Definire una misura di evento
> * Visualizzare eventi simulati
> * Definire una misura di stato
> * Visualizzare uno stato simulato
> * Usare impostazioni e proprietà
> * Usare i comandi
> * Visualizzare il dispositivo simulato nel dashboard

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessaria un'applicazione Azure IoT Central. Se è stato completato l'avvio rapido [Creare un'applicazione Azure IoT Central](quick-deploy-iot-central.md), è possibile usare nuovamente l'applicazione creata nella guida. In caso contrario, completare i passaggi seguenti per creare un'applicazione Azure IoT Central vuota:

1. Passare alla pagina [Application Manager](https://aka.ms/iotcentral) (Gestione applicazioni) di Azure IoT Central.

2. Immettere l'indirizzo di posta elettronica e la password usati per accedere alla sottoscrizione di Azure:

    ![Immettere l'account dell'organizzazione](./media/tutorial-define-device-type/sign-in.png)

3. Per iniziare a creare una nuova applicazione Azure IoT Central, selezionare **Nuova applicazione**:

    ![Pagina Application Manager di Azure IoT Central](./media/tutorial-define-device-type/iotcentralhome.png)

4. Per creare una nuova applicazione Azure IoT Central:
    
   * Scegliere **Versione di valutazione**. Non è necessaria una sottoscrizione di Azure per creare una versione di valutazione dell'applicazione.
    
      Per altre informazioni su directory e sottoscrizioni, vedere la [guida introduttiva per la creazione di un'applicazione](quick-deploy-iot-central.md).
    
   * Scegliere **Applicazione personalizzata**.
    
   * È facoltativamente possibile scegliere un nome descrittivo per l'applicazione, ad esempio **Condizionatori Contoso**. Azure IoT Central genera un prefisso URL univoco. È possibile modificare questo prefisso URL in modo da renderlo più facile da ricordare.
    
   * Selezionare **Create**.

     ![Pagina Crea applicazione di Azure IoT Central](./media/tutorial-define-device-type/iotcentralcreate.png)

     Per altre informazioni, vedere la [guida introduttiva per la creazione di un'applicazione](quick-deploy-iot-central.md).

## <a name="create-a-device-template"></a>Creare un modello di dispositivo

In quanto creatore, l'utente può creare e modificare modelli di dispositivo nell'applicazione. Quando si crea un modello di dispositivo, Azure IoT Central genera un dispositivo simulato dal modello. Il dispositivo simulato genera dati di telemetria che consentono di testare il comportamento dell'applicazione prima di collegare un dispositivo reale.

Per aggiungere un nuovo modello di dispositivo all'applicazione, è necessario passare alla pagina **Modelli di dispositivo**. A tale scopo, selezionare **Modelli di dispositivo** nel menu di spostamento sinistro.

![Pagina Modelli di dispositivo](./media/tutorial-define-device-type/devicetemplates.png)

## <a name="add-a-device-template"></a>Aggiungere un modello di dispositivo

La procedura seguente mostra come creare un nuovo modello di dispositivo **Condizionatore connesso** per i dispositivi che inviano all'applicazione dati di telemetria sulla temperatura:

1. Nella pagina **Modelli di dispositivo**, selezionare **+ Nuovo**:

    ![Pagina Modelli di dispositivo, Crea modello di dispositivo](./media/tutorial-define-device-type/newtemplate.png)

2. La pagina visualizza i modelli che è possibile scegliere.

    ![Raccolta di modelli di dispositivo](./media/tutorial-define-device-type/devicetemplatelibrary.png)

3. Selezionare **Personalizzato**, immettere **Condizionatore connesso** come nome del modello di dispositivo e quindi selezionare **Crea**. È anche possibile caricare un'immagine del dispositivo che sia visibile agli operatori in Device Explorer:

    ![Dispositivo personalizzato](./media/tutorial-define-device-type/createcustomdevice.png)

4. Nel modello di dispositivo **Condizionatore connesso**, assicurarsi di trovarsi nella scheda **Misure** dove si definiscono i dati di telemetria. Ogni modello di dispositivo che viene definito dispone di schede separate in cui è possibile:

   * Specificare le _misure_, ad esempio dati di telemetria, evento e stato, inviate dal dispositivo.

   * Definire le _impostazioni_ usate per controllare il dispositivo.

   * Definire le _proprietà_ che sono i metadati del dispositivo.

   * Definire i _comandi_ da eseguire direttamente nel dispositivo.

   * Definire le _regole_ associate al dispositivo.

   * Personalizzare il _dashboard_ del dispositivo per gli operatori.

     ![Misure del condizionatore](./media/tutorial-define-device-type/airconmeasurements.png)

     > [!NOTE]
     > Per cambiare il nome del modello di dispositivo, fare clic sul nome del modello nella parte superiore della pagina.

5. Per aggiungere la misurazione dei dati di telemetria della temperatura, selezionare **+ Nuova misurazione**. Quindi scegliere **Telemetria** come tipo di misura:

    ![Misure del condizionatore connesso](./media/tutorial-define-device-type/airconmeasurementsnew.png)

6. Ogni tipo di dati di telemetria definito per un modello di dispositivo include [opzioni di configurazione](howto-set-up-template.md), ad esempio:

   * Opzioni di visualizzazione.

   * Dettagli dei dati di telemetria.

   * Parametri di simulazione.

     Per configurare i dati di telemetria **Temperatura**, usare le informazioni nella tabella seguente:

     | Impostazione              | Valore         |
     | -------------------- | -----------   |
     | Nome visualizzato         | Temperatura   |
     | Nome campo           | temperatura   |
     | Unità                | F             |
     | Min                  | 60            |
     | Max                  | 110           |
     | Cifre decimali       | 0             |

     È anche possibile scegliere un colore per la visualizzazione dei dati di telemetria. Per salvare la definizione dei dati di telemetria, selezionare **Salva**:

     ![Configurare la simulazione di Temperatura](./media/tutorial-define-device-type/temperaturesimulation.png)

7. Dopo un breve periodo di tempo, la scheda **Misure** mostra un grafico dei dati di telemetria sulla temperatura dal condizionatore connesso simulato. Usare i controlli per gestire la visibilità e l'aggregazione o per modificare la definizione dei dati di telemetria:
 
    > [!NOTE]
    > Per la telemetria, l'aggregazione predefinita è impostata su **Media**. 

    ![Visualizzare la simulazione di temperatura](./media/tutorial-define-device-type/viewsimulation.png)

8. È anche possibile personalizzare il grafico usando i controlli **Riga**, **In pila**, e **Modifica intervallo di tempo**:

    ![Personalizzare il grafico](./media/tutorial-define-device-type/customizechart.png)

## <a name="add-an-event-measurement"></a>Aggiungere una misura di evento

Usare gli eventi per definire dati temporizzati che il dispositivo invia quando si verifica un evento come un errore o il guasto di un componente. Azure IoT Central consente di simulare eventi di dispositivo che permettono di testare il comportamento dell'applicazione prima di collegare un dispositivo reale. Definire le misurazioni di evento per il modello di dispositivo nella vista **Misure**.

1. Per aggiungere la misurazione dell'evento **Errore motore ventola** selezionare **+ Nuova misurazione**. Quindi scegliere **Evento** come tipo di misura:

    ![Misure del condizionatore connesso](./media/tutorial-define-device-type/eventnew.png)

2. Ogni tipo di evento definito per un modello di dispositivo include [opzioni di configurazione](howto-set-up-template.md), ad esempio:

   * Nome visualizzato.

   * Nome campo.

   * Gravità.

     Per configurare l'evento **Errore motore ventola**, usare le informazioni nella tabella seguente:

     | Impostazione              | Valore             |
     | -------------------- | -----------       |
     | Nome visualizzato         | Errore motore ventola   |
     | Nome campo           | fanmotorerr       |
     | Gravità             | Tipi di errore             |

     Per salvare la definizione dell'evento, selezionare **Salva**:

     ![Configurare la misura Evento](./media/tutorial-define-device-type/eventconfiguration.png)

3. Dopo un breve periodo di tempo, la scheda **Misure** mostra un grafico degli eventi generato casualmente dal condizionatore connesso simulato. Usare i controlli per gestire la visibilità o per modificare la definizione dell'evento:

    ![Visualizzare una simulazione dell'evento](./media/tutorial-define-device-type/eventview.png)

1. Per visualizzare altri dettagli sull'evento, selezionarlo nel grafico:

    ![Visualizzare i dettagli evento](./media/tutorial-define-device-type/eventviewdetail.png)

## <a name="define-a-state-measurement"></a>Definire una misura di stato

È possibile usare lo stato per definire e visualizzare lo stato del dispositivo o del suo componente in un periodo di tempo. Azure IoT Central può simulare lo stato del dispositivo per permettere di testare il comportamento dell'applicazione prima di collegare un dispositivo reale. Si definiscono le misurazioni di stato per il tipo di dispositivo nella vista **Misure**.

1. Per aggiungere una misurazione di stato **Modalità ventola**, fare clic su **+ Nuova misurazione**. Quindi scegliere **Stato** come tipo di misura:

    ![Misure di stato del condizionatore connesso](./media/tutorial-define-device-type/statenew.png)

2. Ogni tipo di stato definito per un modello di dispositivo include [opzioni di configurazione](howto-set-up-template.md), ad esempio:

   * Nome visualizzato.

   * Nome campo.

   * Valori con etichette di visualizzazione facoltative.

   * Colore per ogni valore.

     Per configurare lo stato **Modalità ventola**, usare le informazioni nella tabella seguente:

     | Impostazione              | Valore             |
     | -------------------- | -----------       |
     | Nome visualizzato         | Modalità ventola          |
     | Nome campo           | fanmode           |
     | Valore                | 1                 |
     | Etichetta di visualizzazione        | In funzione         |
     | Valore                | 0                 |
     | Etichetta di visualizzazione        | Arrestato           |

     Per salvare la definizione della misurazione di stato, selezionare **Salva**:

     ![Configurare una misura Stato](./media/tutorial-define-device-type/stateconfiguration.png)

3. Dopo un breve periodo di tempo, la scheda **Misure** mostra un grafico degli stati generato casualmente dal condizionatore connesso simulato. Usare i controlli per gestire la visibilità o per modificare la definizione dello stato:

    ![Visualizzare una simulazione di stato](./media/tutorial-define-device-type/stateview.png)

4. Se sono presenti troppi punti dati inviati dal dispositivo in un periodo limitato, la misura stato viene visualizzata con un oggetto visivo diverso. Selezionare il grafico per visualizzare tutti i punti dati all'interno di tale periodo di tempo in ordine cronologico. È anche possibile restringere l'intervallo di tempo per visualizzare la misura più in dettaglio.

## <a name="settings-properties-and-commands"></a>Impostazioni, proprietà e comandi

Impostazioni, proprietà, e comandi sono valori diversi definiti in un modello di dispositivo e associati a ogni singolo dispositivo:

* Le _impostazioni_ sono usate per inviare dati di configurazione dall'applicazione a un dispositivo. Ad esempio, un operatore può usare un'impostazione per modificare l'intervallo dei dati di telemetria di un dispositivo da due secondi a cinque secondi. Quando un operatore modifica un'impostazione, questa viene contrassegnata come in sospeso nell'interfaccia utente finché il dispositivo risponde con un riconoscimento.

* Le _proprietà_ sono usate per definire i metadati associati al dispositivo. Sono disponibili due categorie di proprietà:
    
  * Le _proprietà dell'applicazione_ sono usate per registrare informazioni sul dispositivo nell'applicazione. È ad esempio possibile usare le proprietà dell'applicazione per registrare la posizione di un dispositivo e la data dell'ultimo utilizzo. Queste proprietà vengono archiviate nell'applicazione e non sono sincronizzate con il dispositivo. Un operatore può assegnare valori alle proprietà.

  * Le _proprietà dispositivo_ sono usate per permettere a un dispositivo di inviare valori di proprietà all'applicazione. Queste proprietà possono essere modificate solo dal dispositivo. Per un operatore, le proprietà dispositivo sono di sola lettura. In questo scenario relativo a un condizionatore connesso, il numero di serie del dispositivo e la versione del firmware sono proprietà del dispositivo segnalate dal dispositivo.
    
    Per altre informazioni, vedere [Proprietà](howto-set-up-template.md#properties) nella guida pratica alla configurazione di un modello di dispositivo.

* I _comandi_ si usano per gestire il dispositivo in modalità remota dall'applicazione. È possibile eseguire i comandi direttamente sul dispositivo dal cloud per controllare i dispositivi. Un operatore, ad esempio, può eseguire un comando come un riavvio, per riavviare immediatamente il dispositivo.

## <a name="use-settings"></a>Usare le impostazioni

Le *impostazioni* sono usate per permettere a un operatore di inviare dati di configurazione a un dispositivo. In questa sezione si aggiunge un'impostazione al modello di dispositivo **Condizionatore connesso** che consente a un operatore di impostare la temperatura di destinazione del condizionatore.

1. Passare alla scheda **Impostazioni** per il modello di dispositivo **Condizionatore connesso**.

2. È possibile creare impostazioni di tipi diversi, ad esempio numeri o testo. Selezionare **Numero** per aggiungere un'impostazione numerica al dispositivo.

3. Per configurare l'impostazione **Imposta temperatura**, usare le informazioni nella tabella seguente:

    | Campo                | Valore           |
    | -------------------- | -----------     |
    | Nome visualizzato         | Imposta temperatura |
    | Nome campo           | setTemperature  |
    | Unità di misura      | F               |
    | Cifre decimali       | 1               |
    | Valore minimo        | 20              |
    | Valore massimo        | 200             |
    | Valore iniziale        | 80              |
    | DESCRIZIONE          | Impostare la temperatura di destinazione per il condizionatore |

    Selezionare quindi **Salva**:

    ![Configurare l'impostazione Imposta temperatura](./media/tutorial-define-device-type/configuresetting.png)

    > [!NOTE]
    > Quando il dispositivo conferma una modifica dell'impostazione, l'impostazione passa allo stato **sincronizzato**.

4. È possibile personalizzare il layout della scheda **Impostazioni** spostando e ridimensionando i riquadri delle impostazioni:

    ![Personalizzare il layout delle impostazioni](./media/tutorial-define-device-type/settingslayout.png)

## <a name="use-properties"></a>Usare le proprietà

Le *proprietà dell'applicazione* sono usate per archiviare informazioni sul dispositivo nell'applicazione. In questa sezione si aggiungono le proprietà dell'applicazione al modello di dispositivo **Connected Air Conditioner** (Condizionatore connesso) per archiviare la posizione del dispositivo e la data dell'ultimo utilizzo. Queste proprietà sono modificabili nell'applicazione. Il dispositivo segnala anche proprietà quali il numero di serie e la versione del firmware che sono di sola lettura nell'applicazione.

1. Passare alla scheda **Proprietà** per il modello di dispositivo **Condizionatore connesso**.

1. È possibile creare proprietà del dispositivo di tipi diversi, ad esempio numeri o testo. Per aggiungere una proprietà di posizione al modello di dispositivo, scegliere **Posizione**. Per configurare la proprietà della posizione, usare le informazioni nella tabella seguente:

    | Campo                | Valore                |
    | -------------------- | -------------------- |
    | Nome visualizzato         | Località             |
    | Nome campo           | location             |
    | Valore iniziale        | Seattle, WA          |
    | DESCRIZIONE          | Posizione dispositivo      |

    Mantenere i valori predefiniti per gli altri campi.

    ![Configurare le proprietà del dispositivo](./media/tutorial-define-device-type/configureproperties.png)

    Selezionare **Salva**.

1. Per aggiungere una proprietà relativa alla data dell'ultimo utilizzo al modello di dispositivo, scegliere **Data**.

1. Per configurare la proprietà relativa alla data dell'ultimo utilizzo, usare le informazioni nella tabella seguente:

    | Campo                | Valore                   |
    | -------------------- | ----------------------- |
    | Nome visualizzato         | Data ultimo utilizzo       |
    | Nome campo           | serviceDate             |
    | Valore iniziale        | 1/1/2019                |
    | DESCRIZIONE          | Ultimo utilizzo           |

    ![Configurare le proprietà del dispositivo](./media/tutorial-define-device-type/configureproperties2.png)

    Selezionare **Salva**.

1. È possibile personalizzare il layout della scheda **Proprietà** spostando e ridimensionando i riquadri delle proprietà.

1. Per aggiungere una proprietà del dispositivo, ad esempio la versione del firmware, al modello di dispositivo, scegliere **Device Property** (Proprietà dispositivo).

1. Per configurare la versione del firmware, usare le informazioni nella tabella seguente:

    | Campo                | Valore                   |
    | -------------------- | ----------------------- |
    | Nome visualizzato         | Versione del firmware        |
    | Nome campo           | firmwareVersion         |
    | Tipo di dati            | text                    |
    | DESCRIZIONE          | Versione del firmware del condizionatore |

    ![Configurare la versione del firmware](./media/tutorial-define-device-type/configureproperties3.png)

    Selezionare **Salva**.

1. Per aggiungere una proprietà del dispositivo, ad esempio un numero di serie, al modello di dispositivo, scegliere **Device Property** (Proprietà dispositivo).

1. Per configurare il numero di serie, usare le informazioni nella tabella seguente:

    | Campo                | Valore                   |
    | -------------------- | ----------------------- |
    | Nome visualizzato         | Numero di serie           |
    | Nome campo           | serialNumber            |
    | Tipo di dati            | text                    |
    | DESCRIZIONE          | Numero di serie del condizionatore  |

    ![Configurare il numero di serie](./media/tutorial-define-device-type/configureproperties4.png)

    Selezionare **Salva**.

    > [!NOTE]
    > La proprietà del dispositivo viene inviata dal dispositivo all'applicazione. I valori della versione del firmware e del numero di serie verranno aggiornati quando il dispositivo reale si connetterà ad IoT Central.

## <a name="use-commands"></a>Usare i comandi

I _comandi_ si usano per consentire a un operatore di eseguire i comandi direttamente sul dispositivo. In questa sezione si aggiunge un comando al modello di dispositivo **Connected Air Conditioner** (Condizionatore connesso) che consente a un operatore di ripetere un determinato messaggio sul display del condizionatore connesso.

1. Passare alla scheda **Comandi** per il modello di dispositivo **Connected Air Conditioner** (Condizionatore connesso) per modificare il modello.

1. Selezionare **+ Nuovo comando** per aggiungere un comando al dispositivo e iniziare a configurare il nuovo comando.

1. Per configurare il nuovo comando, usare le informazioni nella tabella seguente:

    | Campo                | Valore           |
    | -------------------- | -----------     |
    | Nome visualizzato         | Echo Command (Comando eco)    |
    | Nome campo           | echo (eco)            |
    | Timeout predefinito      | 30              |
    | Tipo schermo         | text            |
    | DESCRIZIONE          | Comando del dispositivo  |  

    È possibile aggiungere altri input al comando selezionando **+** per **Campi di input**.

    ![Prepararsi per aggiungere un'impostazione](./media/tutorial-define-device-type/commandsecho1.png)

     Selezionare **Salva**.

1. È possibile personalizzare il layout della scheda **Comandi** spostando e ridimensionando i riquadri dei comandi.

## <a name="view-your-simulated-device"></a>Visualizzare il dispositivo simulato

Dopo aver definito il modello di dispositivo **Condizionatore connesso**, è possibile personalizzare il relativo **Dashboard** per includere le misure, le impostazioni e le proprietà definite. È quindi possibile visualizzare in anteprima il dashboard come un operatore:

1. Scegliere la scheda **Dashboard** per il modello di dispositivo **Condizionatore connesso**.

1. Fare clic su **Grafico a linee** per aggiungere il componente al **Dashboard**.

1. Configurare il componente **Grafico a linee** usando le informazioni nella tabella seguente:

    | Impostazione      | Valore       |
    | ------------ | ----------- |
    | Title        | Temperatura |
    | Intervallo di tempo   | Ultimi 30 minuti |
    | Misure     | Temperatura (selezionare **Visibilità** accanto a **Temperatura**) |

    ![Impostazioni Grafico a linee](./media/tutorial-define-device-type/linechartsettings.png)

    Selezionare quindi **Salva**.

1. Selezionare il componente **Cronologia eventi** usando le informazioni nella tabella seguente:

    | Impostazione      | Valore       |
    | ------------ | ----------- |
    | Title        | Eventi motore ventola |
    | Intervallo di tempo   | Ultimi 30 minuti |
    | Misure     | Errore motore ventola (selezionare **Visibilità** accanto a **Errore motore ventola**) |

    ![Impostazioni del grafico dell'evento](./media/tutorial-define-device-type/dashboardeventchartsetting.png)

    Selezionare quindi **Salva**.

1. Configurare il componente **Cronologia dello stato** usando le informazioni nella tabella seguente:

    | Impostazione      | Valore       |
    | ------------ | ----------- |
    | Title        | Modalità ventola |
    | Intervallo di tempo   | Ultimi 30 minuti |
    | Misure | Modalità ventola (selezionare **Visibilità** accanto a **Modalità ventola**) |

    ![Impostazioni Grafico a linee](./media/tutorial-define-device-type/dashboardstatechartsetting.png)

    Selezionare quindi **Salva**.

1. Per aggiungere le impostazioni e le proprietà del dispositivo al dashboard, scegliere **Impostazioni e proprietà**. Selezionare **Aggiungi/Rimuovi** per aggiungere le impostazioni o le proprietà che si vogliono visualizzare nel dashboard.

1. Configurare il componente **Impostazioni e proprietà** usando le informazioni nella tabella seguente:

    | Impostazione                 | Valore         |
    | ----------------------- | ------------- |
    | Title                   | Proprietà dei dispositivi |
    | Impostazioni e proprietà | Imposta temperatura<br/>Numero di serie<br/>Versione del firmware |

    Le impostazioni e le proprietà definite in precedenza nelle pagine **Impostazioni e proprietà** vengono visualizzate in **Colonne disponibili**.

    ![Configurare le impostazioni delle proprietà relative alla temperatura](./media/tutorial-define-device-type/propertysettings4.png)

    Selezionare quindi **Salva**.

1. È ora possibile vedere i dati simulati per il Condizionatore connesso nel dashboard. È possibile modificare i riquadri e il layout per il dashboard:

    ![Visualizzare il dashboard](./media/tutorial-define-device-type/dashboard.png)

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Creare un nuovo modello di dispositivo
> * Aggiungere dati di telemetria al dispositivo
> * Visualizzare dati di telemetria simulati
> * Definire gli eventi dispositivo
> * Visualizzare eventi simulati
> * Definire lo stato
> * Visualizzare uno stato simulato
> * Usare impostazioni e proprietà
> * Usare i comandi
> * Visualizzare il dispositivo simulato nel dashboard

Dopo aver definito un modello di dispositivo nell'applicazione Azure IoT Central, i passaggi successivi consigliati sono i seguenti:

* [Configurare le regole e le azioni per i dispositivi](tutorial-configure-rules.md)
* [Personalizzare la visualizzazione dell'operatore](tutorial-customize-operator.md)