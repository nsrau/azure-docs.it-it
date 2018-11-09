---
title: Configurare un modello di dispositivo nell'applicazione Azure IoT Central | Microsoft Docs
description: Informazioni su come configurare un modello di dispositivo con misure, impostazioni, proprietà, regole e un dashboard.
author: viv-liu
ms.author: viviali
ms.date: 10/26/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 61bc9da45ac420e5683be1ea3ad253eae9c0ba5a
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158942"
---
# <a name="set-up-a-device-template"></a>Configurare un modello di dispositivo

Un modello di dispositivo è un progetto che definisce le caratteristiche e i comportamenti di un tipo di dispositivo che si connette a un'applicazione Azure IoT Central.

Ad esempio, un generatore può creare un modello per un dispositivo a ventola con connessione IoT con:

- Misura dei dati di telemetria sulla temperatura

- Misura degli eventi di errore del motore della ventola

- Misura dello stato operativo della ventola

- Impostazione della velocità della ventola

- Proprietà location

- Regole per l'invio di avvisi

- Il dashboard offre una visione complessiva del dispositivo

Da questo modello di dispositivo, un operatore può creare e connettere dei dispositivi a ventola reali a cui assegnare nomi come **fan-1** e **fan-2**. Tutti questi dispositivi a ventola hanno misure, impostazioni, proprietà, regole e un dashboard che gli utenti dell'applicazione possono monitorare e gestire.

> [!NOTE]
> Solo i generatori e gli amministratori possono creare, modificare ed eliminare i modelli di dispositivo. Qualsiasi utente può creare dispositivi nella pagina **Device Explorer** (Esplora dispositivi) di un modello di dispositivo esistente.

## <a name="create-a-device-template"></a>Creare un modello di dispositivo

1. Passare alla pagina **Application Builder** (Generatore applicazioni).

2. Per creare un modello vuoto, selezionare **Create Device Template** (Crea modello di dispositivo), quindi selezionare **Custom** (Personalizzato).

3. Immettere un nome (ad es. Refrigerator-1) per il nuovo modello di dispositivo e selezionare **Create** (Crea).

   ![Pagina dei dettagli dispositivo con "Refrigerator" come nome del modello](./media/howto-set-up-template/devicedetailspage.png)

4. A questo punto ci si trova nella pagina **Device details** (Dettagli dispositivo) di un nuovo dispositivo simulato. Quando si crea un modello di dispositivo viene creato automaticamente un dispositivo simulato. Il suo compito è segnalare i dati e può essere controllato esattamente come un dispositivo reale.

Ora esaminare ciascuna delle schede della pagina **Device details** (Dettagli dispositivo).

## <a name="measurements"></a>Misure

Le misure sono i dati provenienti dal dispositivo. È possibile aggiungere più misure al modello di dispositivo per replicare le funzionalità del dispositivo.

- Le misure di **Telemetry** (Telemetria) sono i punti dati numerici che il dispositivo raccoglie nel tempo. Sono rappresentati come un flusso continuo. Un esempio è la temperatura.
- Le misure **Event** (Evento) sono dati temporizzati che hanno un particolare significato per il dispositivo. Il livello di gravità rappresenta l'importanza di un evento. Un esempio è l'errore del motore della ventola.
- Le misure **State** (Stato) rappresentano lo stato del dispositivo o dei relativi componenti nell'arco di un periodo di tempo. Ad esempio, la modalità della ventola può essere definita come modalità avente stato **operativo** o stato **arrestato**.

### <a name="create-a-telemetry-measurement"></a>Creare una misura di telemetria
Per aggiungere una nuova misura di telemetria, selezionare **Modifica modello** e quindi fare clic sul pulsante **+ Nuova misura**. Selezionare **Telemetry** (Telemetria) come tipo di misura e immettere i dettagli nel modulo **Create Telemetry** (Crea dati di telemetria).

> [!NOTE]
> I nomi dei campi nel modello di dispositivo devono corrispondere ai nomi delle proprietà nel codice dispositivo corrispondente, affinché la misura dei dati di telemetria venga visualizzata nell'applicazione quando un dispositivo reale è collegato. Eseguire la stessa operazione quando si configurano le impostazioni, le proprietà del dispositivo e i comandi mentre si continua a definire il modello di dispositivo nelle sezioni seguenti.

Ad esempio, è possibile aggiungere una nuova misura per i dati di telemetria della temperatura:
| Nome visualizzato        | Nome campo    |  Unità    | Min   |Max|
| --------------------| ------------- |-----------|-------|---|
| Temperatura         | temp          |  degC     |  0    |100|

![Modulo "Create Telemetry" (Crea dati di telemetria) con i dettagli per la misura della temperatura](./media/howto-set-up-template/measurementsform.png)

Dopo aver selezionato **Done** (Fatto), la misura **Temperature** (Temperatura) viene visualizzata nell'elenco delle misure. In breve tempo è possibile vedere la visualizzazione dei dati della temperatura generati dal dispositivo simulato. Quando si crea un modello di dispositivo, dal modello viene generato un dispositivo simulato, che consente di verificare il comportamento dell'applicazione prima che un dispositivo fisico/reale venga connesso.


> [!NOTE]
  Il tipo di dati della misura telemetrica è un numero in virgola mobile.

### <a name="create-an-event-measurement"></a>Creare una misura di evento
Per aggiungere una nuova misura di evento selezionare **Modifica modello** e quindi fare clic sul pulsante **+ Nuova misura**. Selezionare **Event** (Evento) come tipo di misura e immettere i dettagli nel modulo **Create Event** (Crea evento).

Specificare i dettagli **Display Name** (Nome visualizzato), **Field Name** (Nome campo) e **Severity** (Gravità) dell'evento. È possibile scegliere fra tre livelli di gravità: **Error** (Errore), **Warning** (Avviso) e **Information** (Informazioni).  

Ad esempio, è possibile aggiungere un nuovo evento **Fan Motor Error** (Errore motore ventola).

| Nome visualizzato        | Nome campo    |  Gravità predefinita | 
| --------------------| ------------- |-----------|
| Errore motore ventola     | fanmotorerror |  Tipi di errore    | 

![Modulo "Create Event" (Crea evento) con i dettagli per un evento motore ventola](./media/howto-set-up-template/eventmeasurementsform.png)

Dopo aver selezionato **Done** (Fatto), la misura **Fan Motor Error** (Errore motore ventola) viene visualizzata nell'elenco delle misure. Un operatore può vedere la visualizzazione dei dati dell'evento che il dispositivo sta inviando.

![Grafico della misura di evento](./media/howto-set-up-template/eventmeasurementschart.png)

Per visualizzare altri dettagli sull'evento selezionare l'icona evento nel grafico.

![Dettagli per l'evento "Fan Motor Error" (Errore motore ventola)](./media/howto-set-up-template/eventmeasurementsdetail.png)

> [!NOTE]
  Il tipo di dati della misura dell'evento è di tipo stringa.

### <a name="create-a-state-measurement"></a>Creare una misura di stato
Per aggiungere una nuova misura di stato selezionare **Modifica modello** e quindi fare clic sul pulsante **+ Nuova misura**. Selezionare **State** (Stato) come tipo di misura e immettere i dettagli nel modulo **Create State** (Crea stato).

Specificare i valori **Display Name** (Nome visualizzato), **Field Name** e **Values** (Valori) dello stato. Ogni valore può anche essere associato a un nome visualizzato che verrà usato per mostrare il valore in tabelle e grafici.

Ad esempio, è possibile aggiungere un nuovo stato **Fan Mode** (Modalità ventola) con due valori possibili che il dispositivo può inviare: **operating** (operativo) e **stopped** (arrestato).


| Nome visualizzato | Nome campo    |  Valore 1   | Nome visualizzato | Valore 2    |Nome visualizzato  | 
| -------------| ------------- |----------- | -------------| -----------| -------------|
| Modalità ventola     | fanmode       |  1         | In funzione    |     0      | Arrestato      |

![Modulo "Edit State" (Modifica stato) con i dettagli per la modalità ventola](./media/howto-set-up-template/statemeasurementsform.png)

Dopo aver selezionato **Done** (Fatto), la misura **Fan Mode** (Modalità ventola) viene visualizzata nell'elenco delle misure. L'operatore può vedere la visualizzazione dei dati di stato che il dispositivo sta inviando.

![Grafico della misura di stato](./media/howto-set-up-template/statemeasurementschart.png)

Se il dispositivo invia troppi punti dati in un periodo limitato, la misura di stato viene visualizzata con un oggetto visivo diverso, come mostrato nella schermata seguente. Fare clic sul grafico per visualizzare tutti i punti dati all'interno di tale periodo di tempo in ordine cronologico. È anche possibile restringere l'intervallo di tempo per visualizzare la misura tracciata sul grafico.

> [!NOTE]
  Il tipo di dati della misura dello stato è di tipo stringa.

## <a name="settings"></a>Impostazioni

Le impostazioni consentono di controllare un dispositivo. Consentono agli operatori dell'applicazione di inviare dati al dispositivo. È possibile aggiungere più impostazioni al modello di dispositivo che verranno visualizzate come riquadri nella scheda **Settings** (Impostazioni) e che gli operatori potranno utilizzare. È possibile aggiungere molti tipi di impostazioni: number (numero), text (testo), date (data), toggle (attivazione/disattivazione), pick list (elenco di selezione) e section label (etichetta sezione). 

Le impostazioni possono presentare uno di tre stati possibili. Il dispositivo segnala questi stati.

- **Synced** (Sincronizzato): il dispositivo è stato modificato per riflettere il valore dell'impostazione.

- **Pending** (In sospeso): il dispositivo sta applicando il valore dell'impostazione.

- **Error** (Errore): il dispositivo ha restituito un errore.

Ad esempio è possibile aggiungere una nuova impostazione della velocità della ventola selezionando **Edit Template** (Modifica modello) e inserendo la nuova impostazione **Number** (Numero):

| Nome visualizzato  | Nome campo    |  Unità  | Decimali |Initial|
| --------------| ------------- |---------| ---------|---- |
| Velocità della ventola     | fanSpeed      | RPM     | 2        | 0   |

![Modulo "Configure Number" (Configurare numero) con i dettagli delle impostazioni di velocità](./media/howto-set-up-template/settingsform.png)

Dopo aver selezionato **Save** (Salva), l'impostazione **Fan speed** (Velocità ventola) viene visualizzata come riquadro ed è pronta per essere utilizzata per modificare la velocità del dispositivo.

Dopo la creazione di un riquadro selezionare **Done** (Fatto) nella parte superiore destra della schermata. Una volta collegato il dispositivo reale all'applicazione, il valore impostazione cambierà in sincronizzazione.

![Scheda "Settings" (Impostazioni) con l'opzione "Design Mode" (Modalità progettazione) per il riquadro](./media/howto-set-up-template/settingstile.png)

## <a name="properties"></a>Properties

Le proprietà sono i metadati associati al dispositivo, ad esempio posizione del dispositivo e numero di serie. È possibile aggiungere più proprietà al modello di dispositivo, che verranno visualizzate come riquadri nella scheda **Properties** (Proprietà). È possibile aggiungere molti tipi di proprietà: number (numero), text (testo), date (data), toggle (attivazione/disattivazione), device property (proprietà dispositivo), label (etichetta) e location (posizione). Un operatore può specificare i valori delle proprietà quando crea un dispositivo e modificare questi valori in qualsiasi momento. Tuttavia, le proprietà del dispositivo sono di sola lettura e vengono inviate dal dispositivo all'applicazione e non possono essere modificate dall'operatore. Quando è connesso il dispositivo reale, il riquadro delle proprietà del dispositivo viene aggiornato nell'applicazione. 

Sono disponibili due categorie di proprietà:

- Le proprietà **Device** (Dispositivo) segnalate dal dispositivo all'applicazione IoT Central. Sono valori di sola lettura riportati dal dispositivo e saranno aggiornati nell'applicazione una volta collegato il dispositivo reale. 
- Proprietà **Application** (Applicazione) che vengono archiviate esclusivamente nell'applicazione e che possono essere modificate dall'operatore. Il dispositivo non riconosce le proprietà dell'applicazione.

Ad esempio è possibile aggiungere una posizione del dispositivo come nuova proprietà **Text** (Testo) (una proprietà dell'applicazione) selezionando **Edit Template** (Modifica modello) e inserendo la nuova proprietà:

| Nome visualizzato  | Nome campo | Taglio degli spazi iniziali  | Taglio degli spazi finali  | Maiuscole/minuscole| Lunghezza minima | Lunghezza massima |
| --------------| -----------|---------| ---------|---- |----|----|
| Località      | loc        | Off     |  Off     | mixed  | 0 | 100|

![Modulo "Configure Text" (Configura testo) della scheda "Properties" (Proprietà)](./media/howto-set-up-template/propertiesform.png)

Dopo aver selezionato **Save** (Salva), device location (posizione dispositivo) viene visualizzato come riquadro:

![Riquadro Location (Posizione)](./media/howto-set-up-template/propertiestile.png)

Dopo aver creato un riquadro è possibile modificare il valore della proprietà dell'applicazione. In primo luogo selezionare **Done** (Fatto) nella parte superiore destra della schermata.

### <a name="create-a-location-property-through-azure-maps"></a>Creare una proprietà di posizione con Mappe di Azure
È possibile fornire un contesto geografico per i dati di posizione in Azure IoT Central e mappare qualsiasi coordinata di latitudine e di longitudine di un indirizzo stradale. Oppure è possibile mappare semplicemente le coordinate di latitudine e longitudine. Mappe di Azure abilita questa funzionalità in IoT Central.

Esistono due tipi di proprietà di posizione che è possibile aggiungere:
- **Location as an Application property** (Posizione come proprietà dell'applicazione) che è archiviata esclusivamente nell'applicazione. Il dispositivo non riconosce le proprietà dell'applicazione.
- **Location as a Device property** (Posizione come proprietà del dispositivo) che il dispositivo segnala all'applicazione.

#### <a name="add-location-as-an-application-property"></a>Aggiungere la posizione come proprietà dell'applicazione 
È possibile creare una proprietà di posizione come proprietà dell'applicazione usando Mappe di Azure nell'applicazione IoT Central. Ad esempio, è possibile aggiungere l'indirizzo di installazione del dispositivo. 

1. Nella scheda **Proprietà** selezionare **Modifica modello**.

   ![Scheda "Properties" (Proprietà) con la modalità progettazione attivata](./media/howto-set-up-template/locationcloudproperty1.png)

2. Nella libreria selezionare **Location** (Posizione).
3. Configurare **Display Name** (Nome visualizzato), **Nome campo** (Field Name) e facoltativamente **Initial Value** (Valore iniziale) per la posizione. 

    | Nome visualizzato  | Nome campo | Valore iniziale |
    | --------------| -----------|---------| 
    | Indirizzo di installazione | insta_address | Microsoft, 1 Microsoft Way, Redmond, WA 98052   |

   ![Modulo "Configure Location" (Configura posizione) con i dettagli per la posizione](./media/howto-set-up-template/locationcloudproperty2.png)

   Esistono due formati supportati per aggiungere una posizione:
   - **Location come indirizzo**
   - **Location come coordinate** 

4. Selezionare **Salva** e **Done** (Fatto). Un operatore può aggiornare il valore della posizione nel formato del campo della posizione. 

#### <a name="add-location-as-a-device-property"></a>Aggiunta della posizione come proprietà del dispositivo 

È possibile creare una proprietà di posizione come proprietà del dispositivo che il dispositivo segnala. Ad esempio, se si vuole tenere traccia della posizione del dispositivo:

1. Nella scheda **Proprietà** selezionare **Modifica modello**.

   ![Scheda "Properties" (Proprietà) con la modalità progettazione attivata](./media/howto-set-up-template/locationdeviceproperty1.png)

2. Selezionare **Device Property** (Proprietà dispositivo) nella libreria.
3. Configurare il nome visualizzato e il nome di campo, quindi scegliere **Location** (Posizione) come tipo di dati. 

    | Nome visualizzato  | Nome campo | Tipo di dati |
    | --------------| -----------|-----------| 
    | Posizione dispositivo | deviceLoc| location  |

   > [!NOTE]
   > I nomi dei campi devono corrispondere ai nomi delle proprietà nel codice del dispositivo corrispondente

   ![Modulo "Configure Device Properties" (Configura proprietà dispositivo) con i dettagli per la posizione](./media/howto-set-up-template/locationdeviceproperty2.png)

Una volta connesso il dispositivo reale, la posizione aggiunta come una proprietà del dispositivo verrà aggiornata con il valore inviato dal dispositivo. L'indirizzo dell'installazione, la posizione aggiunta come una proprietà dell'applicazione, è un riquadro modificabile. Ora che è stata configurata la proprietà di posizione, sarà possibile [aggiungere una mappa per visualizzare la posizione nel dashboard del dispositivo](#add-an-azure-maps-location-in-the-dashboard).

## <a name="commands"></a>Comandi:

I comandi vengono utilizzati per gestire un dispositivo in remoto. Consentono agli operatori dell'applicazione di eseguire istantaneamente comandi sul dispositivo. È possibile aggiungere più comandi al modello di dispositivo che verranno visualizzati come riquadri nella scheda **Commands** (Comandi) e che gli operatori potranno usare. In qualità di generatore del dispositivo, si ha la possibilità di definire comandi in base alle esigenze.

In cosa differisce un comando da un'impostazione? 

* **Impostazione**: un'impostazione è una configurazione che si vuole applicare a un dispositivo e si desidera che il dispositivo mantenga fino a quando non si decide di modificarla. Ad esempio, si vuole impostare la temperatura del freezer e si desidera mantenere l'impostazione anche quando il freezer viene riavviato. 

* **Comando**: i comandi si usano per esegue istantaneamente un comando nel dispositivo in modalità remota da IoT Central. Se non c'è un dispositivo connesso, il comando va in timeout e non viene eseguito. Ad esempio, si desidera riavviare un dispositivo.  


Ad esempio è possibile aggiungere un nuovo comando **Echo** (Eco) selezionando **Editing Template** (Modello di modifica), quindi fare clic su **+ New Command** (+ Nuovo comando) e immettendo il nuovo comando:

| Nome visualizzato  | Nome campo | Timeout predefinito | Tipo di dati |
| --------------| -----------|---------------- | --------- | 
| Echo Command (Comando eco)  | echo (eco)       |  30             | text      |

![Modulo "Configure Command" (Configura comando) con i dettagli per Echo](./media/howto-set-up-template/commandsecho.png)

Dopo aver selezionato **Save** (Salva) e **Done** (Fatto), il comando **Echo** (Eco) viene visualizzato come riquadro ed è pronto per essere usato per eseguire l'eco del dispositivo una volta collegato il dispositivo reale. I nomi dei campi del comando devono corrispondere ai nomi delle proprietà nel codice dispositivo corrispondente, affinché i comandi vengano eseguiti correttamente.


## <a name="rules"></a>Regole

Le regole consentono agli operatori di monitorare i dispositivi quasi in tempo reale. Le regole chiamano automaticamente azioni, ad esempio l'invio di un messaggio di posta elettronica quando la regola viene attivata. Vi è un solo tipo di regola disponibile al momento:

- La **regola di telemetria** che viene attivata quando la telemetria del dispositivo selezionato supera una soglia specificata. [Altre informazioni sulle regole di telemetria](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>dashboard

Il dashboard è dove l'operatore può visualizzare informazioni su un dispositivo. In qualità di generatore, è possibile aggiungere riquadri a questa pagina per aiutare gli operatori a comprendere come si comporta il dispositivo. È possibile aggiungere più riquadri del dashboard al modello di dispositivo. È possibile aggiungere più tipi di riquadri del dashboard: image (immagine), line chart (grafico a linee), bar chart (grafico a barre), KPI (indicatore KPI), settings and properties (impostazioni e proprietà) e label (etichetta).

Ad esempio è possibile aggiungere un riquadro **Settings and Properties** (Impostazioni e proprietà) per mostrare una selezione dei valori correnti di impostazioni e proprietà selezionando **Modifica modello** e il riquadro dalla raccolta:

![Modulo "Configure Device Details" (Configura dettagli dispositivo) con i dettagli per impostazioni e proprietà](./media/howto-set-up-template/dashboardsettingsandpropertiesform.png)

Quando un operatore visualizza il dashboard, ora potrà vedere questo riquadro con le proprietà e le impostazioni del dispositivo:

![Scheda "Dashboard" con visualizzate le impostazioni e le proprietà per il riquadro](./media/howto-set-up-template/dashboardtile.png)

### <a name="add-an-azure-maps-location-in-the-dashboard"></a>Aggiungere una posizione di Mappe di Azure nel dashboard

Se in precedenza è stata configurata una proprietà di posizione in [Creare una proprietà di posizione con Mappe di Azure](#create-a-location-property-through-azure-maps), è possibile visualizzare la posizione usando una mappa nel dashboard del dispositivo.

1. Nella scheda **Dashboard** selezionare **Modifica modello**.

   ![Scheda "Dashboard" con la modalità progettazione attivata](./media/howto-set-up-template/locationcloudproperty4map.png)

2. Nel dashboard del dispositivo selezionare **Map** (Mappa) nella libreria. 
3. Dargli un titolo. L'esempio seguente ha il titolo Installation Location (Percorso di installazione), quindi scegliere la proprietà della posizione configurata in precedenza nella scheda Properties (Proprietà). Nell'esempio riportato di seguito è selezionato **Installation address** (Indirizzo di installazione).

   ![Modulo "Configure Map" (Configura mappa) con i dettagli per titolo e proprietà](./media/howto-set-up-template/locationcloudproperty5map.png)

4. Selezionare **Salva**. Il riquadro Map (Mappa) ora mostra la posizione selezionata. 

   ![Riquadro Map (Mappa) con la posizione selezionata](./media/howto-set-up-template/locationcloudproperty6map.png) 

Sarà possibile ridimensionare la mappa alle dimensioni desiderate. Ora, quando un operatore visualizza il dashboard, può vedere tutti i riquadri del dashboard che sono stati configurati, inclusa la mappa della posizione.

## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce la procedura per configurare un modello di dispositivo nell'applicazione Azure IoT Central, è possibile:

> [!div class="nextstepaction"]
> [Creare una nuova versione di un modello di dispositivo](howto-version-devicetemplate.md)
