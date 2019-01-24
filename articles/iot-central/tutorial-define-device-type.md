---
title: Definire un nuovo tipo di dispositivo in Azure IoT Central | Microsoft Docs
description: Questa esercitazione illustra come definire un nuovo tipo di dispositivo nell'applicazione Azure IoT Central. L'utente definisce i dati di telemetria, lo stato, le proprietà e le impostazioni per il tipo.
author: dominicbetts
ms.author: dobett
ms.date: 10/30/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: d50059fb6fe03309609c91b56fb3763bad32e46b
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54199970"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application"></a>Esercitazione: Definire un nuovo tipo di dispositivo nell'applicazione Azure IoT Central

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

3. Per iniziare a creare una nuova applicazione Azure IoT Central, scegliere **Nuova applicazione**:

    ![Pagina Application Manager di Azure IoT Central](./media/tutorial-define-device-type/iotcentralhome.png)

4. Per creare una nuova applicazione Azure IoT Central:
    
    * Scegliere **Versione di valutazione**. Non è necessaria una sottoscrizione di Azure per creare una versione di valutazione dell'applicazione.
    
       Per altre informazioni su directory e sottoscrizioni, vedere la [guida introduttiva per la creazione di un'applicazione](quick-deploy-iot-central.md).
    
    * Scegliere **Applicazione personalizzata**.
    
    * È facoltativamente possibile scegliere un nome descrittivo per l'applicazione, ad esempio **Condizionatori Contoso**. Azure IoT Central genera un prefisso URL univoco. È possibile modificare questo prefisso URL in modo da renderlo più facile da ricordare.
    
    * Scegliere **Create**.

    ![Pagina Crea applicazione di Azure IoT Central](./media/tutorial-define-device-type/iotcentralcreate.png)

    Per altre informazioni, vedere la [guida introduttiva per la creazione di un'applicazione](quick-deploy-iot-central.md).

## <a name="create-a-new-custom-device-template"></a>Creare un nuovo modello di dispositivo personalizzato

In quanto creatore, l'utente può creare e modificare modelli di dispositivo nell'applicazione. Quando si crea un modello di dispositivo, Azure IoT Central genera un dispositivo simulato dal modello. Il dispositivo simulato genera dati di telemetria che consentono di testare il comportamento dell'applicazione prima di collegare un dispositivo fisico.

Per aggiungere un nuovo modello di dispositivo all'applicazione, è necessario passare alla pagina **Application Builder** (Generatore applicazioni). Per farlo, scegliere **Application builder** (Generatore applicazioni) nel menu di spostamento a sinistra.

![Pagina Application Builder (Generatore applicazioni)](./media/tutorial-define-device-type/builderhome.png)

## <a name="add-a-device-and-define-telemetry"></a>Aggiungere un dispositivo e definire i dati di telemetria

La procedura seguente mostra come creare un nuovo modello di dispositivo **Condizionatore connesso** per i dispositivi che inviano all'applicazione dati di telemetria sulla temperatura:

1. Nella pagina **Application Builder**, scegliere **Create Device Template** (Crea modello di dispositivo):

    ![Pagina Application Builder, Create Device Template](./media/tutorial-define-device-type/builderhomedevices.png)

2. Nella pagina **Modelli di dispositivo**, scegliere **Personalizzato**. Un modello di dispositivo **Personalizzato** consente di definire tutte le caratteristiche e i comportamenti del condizionatore connesso:

    ![Dispositivi](./media/tutorial-define-device-type/builderhomedevicescustom.png)

3. Nella pagina **Nuovo modello di dispositivo**, immettere **Condizionatore connesso** come nome del dispositivo e quindi scegliere **Crea**. È anche possibile caricare un'immagine del dispositivo che sia visibile agli operatori in Device Explorer:

    ![Dispositivo personalizzato](./media/tutorial-define-device-type/createcustomdevice.png)

4. Nel modello di dispositivo **Condizionatore connesso**, assicurarsi di trovarsi nella pagina **Misure** dove si definiscono i dati di telemetria. Ogni modello di dispositivo che viene definito dispone di pagine separate in cui è possibile:

    * Specificare le misure, ad esempio dati di telemetria, evento e stato, inviate dal dispositivo.
    
    * Definire le impostazioni usate per controllare il dispositivo.
    
    * Definire le proprietà che sono i metadati del dispositivo.

    * Definire i comandi da eseguire direttamente nel dispositivo.
    
    * Definire le regole associate al dispositivo.
    
    * Personalizzare il dashboard del dispositivo per gli operatori.

    Quando si definisce il modello di dispositivo, scegliere prima **Modifica modello** per modificare il modello. Al termine, scegliere **Fine**. 

    ![Misure del condizionatore](./media/tutorial-define-device-type/airconmeasurements.png)

    > [!NOTE]
    > Per modificare il nome del dispositivo o del modello di dispositivo, fare clic sul testo nella parte superiore della pagina.

5. Per aggiungere la misura di telemetria della temperatura, scegliere **Nuova misura**. Quindi scegliere **Telemetria** come tipo di misura:

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

    È anche possibile scegliere un colore per la visualizzazione dei dati di telemetria. Per salvare la definizione dei dati di telemetria, scegliere **Salva**:

    ![Configurare la simulazione di Temperatura](./media/tutorial-define-device-type/temperaturesimulation.png)

7. Dopo un breve periodo di tempo, la pagina **Misure** mostra un grafico dei dati di telemetria sulla temperatura dal condizionatore connesso simulato. Usare i controlli per gestire la visibilità e l'aggregazione o per modificare la definizione dei dati di telemetria:

    ![Visualizzare la simulazione di temperatura](./media/tutorial-define-device-type/viewsimulation.png)

8. È anche possibile personalizzare il grafico usando i controlli **Riga**, **In pila**, e **Modifica intervallo di tempo**:

    ![Personalizzare il grafico](./media/tutorial-define-device-type/customizechart.png)

## <a name="define-event-measurement"></a>Definire la misura Evento

È possibile usare Evento per definire dati temporizzati inviati al dispositivo, in modo che indichino eventi significativi come un errore o il guasto di un componente. Come per le misure di telemetria, Azure IoT Central consente di simulare eventi di dispositivo che permettono di testare il comportamento dell'applicazione prima di collegare un dispositivo fisico. Si definiscono le misurazioni di evento per il tipo di dispositivo nella vista **Misure**.

1. Per aggiungere la misura dell'evento **Errore motore ventola**, scegliere **Nuova misura**. Quindi scegliere **Evento** come tipo di misura:

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

    Per salvare la definizione dell'evento, scegliere **Salva**:

    ![Configurare la misura Evento](./media/tutorial-define-device-type/eventconfiguration.png)

3. Dopo un breve periodo di tempo, la pagina **Misure** mostra un grafico degli eventi generato casualmente dal condizionatore connesso simulato. Usare i controlli per gestire la visibilità o per modificare la definizione dell'evento:

    ![Visualizzare una simulazione dell'evento](./media/tutorial-define-device-type/eventview.png)

1. Per visualizzare altri dettagli sull'evento, fare clic sull'evento nel grafico:

    ![Visualizzare i dettagli evento](./media/tutorial-define-device-type/eventviewdetail.png)

## <a name="define-state-measurement"></a>Definire una misura Stato

È possibile usare Stato per definire e visualizzare lo stato del dispositivo o del suo componente in un periodo di tempo. Come per le misure di telemetria, Azure IoT Central consente di simulare uno stato del dispositivo che permette di testare il comportamento dell'applicazione prima di collegare un dispositivo fisico. Si definiscono le misurazioni di stato per il tipo di dispositivo nella vista **Misure**.

1. Per aggiungere la misura **Modalità ventola**, scegliere **Nuova misura**. Quindi scegliere **Stato** come tipo di misura:

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

    Per salvare la definizione della misura di stato, scegliere **Salva**:

    ![Configurare una misura Stato](./media/tutorial-define-device-type/stateconfiguration.png)

3. Dopo un breve periodo di tempo, la pagina **Misure** mostra un grafico degli stati generato casualmente dal condizionatore connesso simulato. Usare i controlli per gestire la visibilità o per modificare la definizione dello stato:

    ![Visualizzare una simulazione di stato](./media/tutorial-define-device-type/stateview.png)

4. Nel caso in cui siano presenti troppi punti dati inviati dal dispositivo in un periodo limitato, la misura stato viene visualizzata con un oggetto visivo diverso, come illustrato di seguito. Fare clic sul grafico per visualizzare tutti i punti dati all'interno di tale periodo di tempo in ordine cronologico. È anche possibile restringere l'intervallo di tempo per visualizzare la misura più in dettaglio.

    ![Visualizzare i dettagli di stato](./media/tutorial-define-device-type/stateviewdetail.png)

## <a name="settings-properties-and-commands"></a>Impostazioni, proprietà e comandi

Impostazioni, proprietà, e comandi sono valori diversi definiti in un modello di dispositivo e associati a ogni singolo dispositivo:

* Le _impostazioni_ sono usate per inviare dati di configurazione dall'applicazione a un dispositivo. Ad esempio, un operatore può usare un'impostazione per modificare l'intervallo dei dati di telemetria di un dispositivo da due secondi a cinque secondi. Quando un operatore modifica un'impostazione, questa viene contrassegnata come in sospeso nell'interfaccia utente finché il dispositivo invia un acknowledgement di attivazione della modifica.

* Le _proprietà_ sono usate per definire i metadati associati al dispositivo. Sono disponibili due categorie di proprietà:
    
    * Le _proprietà dell'applicazione_ sono usate per registrare informazioni sul dispositivo nell'applicazione. È ad esempio possibile usare le proprietà dell'applicazione per registrare la posizione di un dispositivo e la data dell'ultimo utilizzo. Queste proprietà vengono archiviate nell'applicazione e non sono sincronizzate con il dispositivo. Un operatore può assegnare valori alle proprietà.

    * Le _proprietà dispositivo_ sono usate per permettere a un dispositivo di inviare valori di proprietà all'applicazione. Queste proprietà possono essere modificate solo dal dispositivo. Per un operatore, le proprietà dispositivo sono di sola lettura. In questo scenario relativo a un condizionatore connesso, il numero di serie del dispositivo e la versione del firmware sono proprietà del dispositivo segnalate dal dispositivo. 
    
    Per altre informazioni, vedere [Proprietà][lnk-define-template] nella guida pratica alla configurazione di un modello di dispositivo.

* I _comandi_ si usano per gestire il dispositivo in modalità remota dall'applicazione. È possibile eseguire i comandi direttamente sul dispositivo dal cloud per controllare i dispositivi. Un operatore, ad esempio, può eseguire un comando come un riavvio, per riavviare immediatamente il dispositivo.

## <a name="use-settings"></a>Usare le impostazioni

Le *impostazioni* sono usate per permettere a un operatore di inviare dati di configurazione a un dispositivo. In questa sezione si aggiunge un'impostazione al modello di dispositivo **Condizionatore connesso** che consente a un operatore di impostare la temperatura di destinazione del condizionatore.

1. Passare alla pagina **Impostazioni** per il modello di dispositivo **Condizionatore connesso**:

    ![Prepararsi per aggiungere un'impostazione](./media/tutorial-define-device-type/deviceaddsetting.png)

    È possibile creare impostazioni di tipi diversi, ad esempio numeri o testo.

2. Scegliere **Numero** per aggiungere un'impostazione numerica al dispositivo.

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

    Quindi scegliere **Salva**:

    ![Configurare l'impostazione Imposta temperatura](./media/tutorial-define-device-type/configuresetting.png)

    > [!NOTE]
    > Quando il dispositivo conferma una modifica dell'impostazione, l'impostazione passa allo stato **sincronizzato**.

4. È possibile personalizzare il layout della pagina **Impostazioni** spostando e ridimensionando i riquadri delle impostazioni:

    ![Personalizzare il layout delle impostazioni](./media/tutorial-define-device-type/settingslayout.png)

## <a name="use-properties"></a>Usare le proprietà 

Le *proprietà dell'applicazione* sono usate per archiviare informazioni sul dispositivo nell'applicazione. In questa sezione si aggiungono le proprietà dell'applicazione al modello di dispositivo **Connected Air Conditioner** (Condizionatore connesso) per archiviare la posizione del dispositivo e la data dell'ultimo utilizzo. Tenere presente che sono entrambe proprietà modificabili del dispositivo. Sono anche presenti proprietà del dispositivo di sola lettura segnalate dal dispositivo che non possono essere modificate, ad esempio il numero di serie del dispositivo e la versione del firmware.
 
1. Passare alla pagina **Proprietà** per il modello di dispositivo **Condizionatore connesso**:

    ![Prepararsi per aggiungere una proprietà](./media/tutorial-define-device-type/deviceaddproperty.png)

    È possibile creare proprietà del dispositivo di tipi diversi, ad esempio numeri o testo. Per aggiungere una proprietà di posizione al modello di dispositivo, scegliere **Posizione**.

1. Per configurare la proprietà della posizione, usare le informazioni nella tabella seguente:

    | Campo                | Valore                |
    | -------------------- | -------------------- |
    | Nome visualizzato         | Località             |
    | Nome campo           | location             |
    | Valore iniziale        | Seattle, WA          |
    | DESCRIZIONE          | Posizione dispositivo      |

    Mantenere i valori predefiniti per gli altri campi.

    ![Configurare le proprietà del dispositivo](./media/tutorial-define-device-type/configureproperties.png)

    Scegliere **Salva**.

1. Per aggiungere una proprietà relativa alla data dell'ultimo utilizzo al modello di dispositivo, scegliere **Data**.

1. Per configurare la proprietà relativa alla data dell'ultimo utilizzo, usare le informazioni nella tabella seguente:

    | Campo                | Valore                   |
    | -------------------- | ----------------------- |
    | Nome visualizzato         | Data ultimo utilizzo       |
    | Nome campo           | serviceDate             |
    | Valore iniziale        | 1/1/2018                |
    | DESCRIZIONE          | Ultimo utilizzo           |

    ![Configurare le proprietà del dispositivo](./media/tutorial-define-device-type/configureproperties2.png)

    Scegliere **Salva**.

5. È possibile personalizzare il layout della pagina **Proprietà** spostando e ridimensionando i riquadri delle proprietà:

    ![Personalizzare il layout delle proprietà](./media/tutorial-define-device-type/propertieslayout.png)

1. Per aggiungere una proprietà del dispositivo, ad esempio la versione del firmware, al modello di dispositivo, scegliere **Device Property** (Proprietà dispositivo).

1.  Per configurare la versione del firmware, usare le informazioni nella tabella seguente:

    | Campo                | Valore                   |
    | -------------------- | ----------------------- |
    | Nome visualizzato         | Versione del firmware        |
    | Nome campo           | firmwareVersion         |
    | Tipo di dati            | text                    |
    | DESCRIZIONE          | Versione del firmware del condizionatore |

    ![Configurare la versione del firmware](./media/tutorial-define-device-type/configureproperties3.png)
    
    Scegliere **Salva**.

1. Per aggiungere una proprietà del dispositivo, ad esempio un numero di serie, al modello di dispositivo, scegliere **Device Property** (Proprietà dispositivo).

1. Per configurare il numero di serie, usare le informazioni nella tabella seguente:

    | Campo                | Valore                   |
    | -------------------- | ----------------------- |
    | Nome visualizzato         | Numero di serie           |
    | Nome campo           | serialNumber            |
    | Tipo di dati            | text                    |
    | DESCRIZIONE          | Numero di serie del condizionatore  |

    ![Configurare il numero di serie](./media/tutorial-define-device-type/configureproperties4.png)
    
    Scegliere **Salva**.
    
    > [!NOTE]
    > La proprietà del dispositivo viene inviata dal dispositivo all'applicazione. I valori della versione del firmware e del numero di serie verranno aggiornati quando il dispositivo reale si connetterà ad IoT Central.

## <a name="use-commands"></a>Usare i comandi

I _comandi_ si usano per consentire a un operatore di eseguire i comandi direttamente sul dispositivo. In questa sezione si aggiunge un comando al modello di dispositivo **Connected Air Conditioner** (Condizionatore connesso) che consente a un operatore di ripetere un determinato messaggio sul display del condizionatore connesso.

1. Passare alla pagina **Comandi** per il modello di dispositivo **Connected Air Conditioner** (Condizionatore connesso) per modificare il modello. 

1. Fare clic su **New Command** (Nuovo comando) per aggiungere un comando al dispositivo e iniziare a configurare il nuovo comando.

   È possibile creare comandi di tipi diversi a seconda dei requisiti. 

1. Per configurare il nuovo comando, usare le informazioni nella tabella seguente:

    | Campo                | Valore           |
    | -------------------- | -----------     |
    | Nome visualizzato         | Echo Command (Comando eco)    |
    | Nome campo           | echo (eco)            |
    | Timeout predefinito      | 30              |
    | Tipo schermo         | text            |
    | DESCRIZIONE          | Comando del dispositivo  |  

    È possibile aggiungere altri input al comando facendo clic su **+** per **Input Fields** (Campi di input).

    ![Prepararsi per aggiungere un'impostazione](media/tutorial-define-device-type/commandsecho1.png)

     Scegliere **Salva**.

1. È possibile personalizzare il layout della pagina **Comandi** spostando e ridimensionando i riquadri dei comandi:

    ![Personalizzare il layout delle impostazioni](media/tutorial-define-device-type/commandstileresize1.png)

## <a name="view-your-simulated-device"></a>Visualizzare il dispositivo simulato

Dopo aver definito il modello di dispositivo **Condizionatore connesso**, è possibile personalizzare il relativo **Dashboard** per includere le misure, le impostazioni e le proprietà definite. È quindi possibile visualizzare in anteprima il dashboard come un operatore:

1. Scegliere la pagina **Dashboard** per il modello di dispositivo **Condizionatore connesso**:

    ![Dashboard del condizionatore connesso](./media/tutorial-define-device-type/aircondashboards.png)

1. Scegliere **Grafico a linee** per aggiungere il componente al **Dashboard**:

    ![Componenti del dashboard](./media/tutorial-define-device-type/dashboardcomponents1.png)

1. Configurare il componente **Grafico a linee** usando le informazioni nella tabella seguente:

    | Impostazione      | Valore       |
    | ------------ | ----------- |
    | Title        | Temperatura |
    | Intervallo di tempo   | Ultimi 30 minuti |
    | Misure     | temperatura (scegliere **Visibilità** accanto a **temperatura**) |

    ![Impostazioni Grafico a linee](./media/tutorial-define-device-type/linechartsettings.png)

    Quindi scegliere **Salva**.

1. Configurare il componente **Event History** (Cronologia degli eventi) usando le informazioni nella tabella seguente:

    | Impostazione      | Valore       |
    | ------------ | ----------- |
    | Title        | Eventi |
    | Intervallo di tempo   | Ultimi 30 minuti |
    | Misure     | Errore motore ventola (scegliere **Visibilità** accanto a **Errore motore ventola**) |

    ![Impostazioni Grafico a linee](./media/tutorial-define-device-type/dashboardeventchartsetting.png)

    Quindi scegliere **Salva**.

1. Configurare il componente **Cronologia dello stato** usando le informazioni nella tabella seguente:

    | Impostazione      | Valore       |
    | ------------ | ----------- |
    | Title        | Modalità ventola |
    | Intervallo di tempo   | Ultimi 30 minuti |
    | Misure | Modalità ventola (scegliere **Visibilità** accanto a **Modalità ventola**) |

    ![Impostazioni Grafico a linee](./media/tutorial-define-device-type/dashboardstatechartsetting.png)

    Quindi scegliere **Salva**.

1. Per aggiungere l'impostazione Imposta temperatura al dashboard, scegliere **Settings and properties** (Impostazioni e proprietà). Fare clic su **Aggiungi/Rimuovi** per aggiungere le impostazioni o le proprietà che si vuole visualizzare nel dashboard. 

    ![Componenti del dashboard](./media/tutorial-define-device-type/dashboardcomponents4.png)

1. Configurare il componente **Impostazioni e proprietà** usando le informazioni nella tabella seguente:

    | Impostazione                 | Valore         |
    | ----------------------- | ------------- |
    | Title                   | Impostare la temperatura di destinazione |
    | Impostazioni e proprietà | Imposta temperatura |

    Le impostazioni e le proprietà definite in precedenza nelle pagine Settings and Properties (Impostazioni e proprietà) vengono visualizzate nelle colonne disponibili. 

    ![Configurare le impostazioni delle proprietà relative alla temperatura](./media/tutorial-define-device-type/propertysettings4.png)

    Scegliere **OK**.

1. Per aggiungere il numero di serie del dispositivo al dashboard, scegliere **Settings and Properties** (Impostazioni e proprietà):

    ![Componenti del dashboard](./media/tutorial-define-device-type/dashboardcomponents3.png)

1. Configurare il componente **Impostazioni e proprietà** usando le informazioni nella tabella seguente:

    | Impostazione                 | Valore         |
    | ----------------------- | ------------- |
    | Title                   | Numero di serie |
    | Impostazioni e proprietà | Numero di serie |

    ![Impostazioni di proprietà del numero di serie](./media/tutorial-define-device-type/propertysettings5.png)

    Scegliere **OK**.

1. Per aggiungere la versione del firmware del dispositivo al dashboard, scegliere **Settings and Properties** (Impostazioni e proprietà):

    ![Componenti del dashboard](./media/tutorial-define-device-type/dashboardcomponents4.png)

1. Configurare il componente **Impostazioni e proprietà** usando le informazioni nella tabella seguente:

    | Impostazione                 | Valore            |
    | ----------------------- | ---------------- |
    | Title                   | Versione del firmware |
    | Impostazioni e proprietà | Versione del firmware |

    ![Impostazioni di proprietà del numero di serie](./media/tutorial-define-device-type/propertysettings6.png)

    Scegliere **OK**.

1. Per visualizzare il dashboard come operatore, disattivare **Modifica modello** in alto a destra nella pagina.

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

[lnk-define-template]: /azure/iot-central/howto-set-up-template#properties