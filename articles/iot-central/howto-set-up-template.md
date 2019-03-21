---
title: Configurare un modello di dispositivo nell'applicazione Azure IoT Central | Microsoft Docs
description: Informazioni su come configurare un modello di dispositivo con misure, impostazioni, proprietà, regole e un dashboard.
author: viv-liu
ms.author: viviali
ms.date: 01/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: ff73982b706fa2d353be818f5022de8fbfec42fb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58167440"
---
# <a name="set-up-a-device-template-new-ui-design"></a>Configurare un modello di dispositivo (nuova progettazione dell'interfaccia utente)

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

1. Passare alla pagina **Modelli di dispositivo**.

2. Per creare un modello, iniziare selezionando **+ nuovo**.

3. Per iniziare rapidamente, scegliere dai modelli predefiniti esistenti. In caso contrario, selezionare **Custom**, immettere un nome e fare clic su **crea** per compilare un modello personalizzato da zero.

   ![Libreria di modelli di dispositivo](./media/howto-set-up-template/newtemplate.png)

4. Quando si crea un modello personalizzato, vedere la **dettagli dispositivo** pagina per il nuovo modello di dispositivo. IoT Central crea automaticamente un dispositivo simulato quando si crea un modello di dispositivo. Un dispositivo simulato consente di testare il comportamento dell'applicazione prima di collegare un dispositivo reale.

Le sezioni seguenti descrivono ogni scheda della pagina **Modello di dispositivo**.

## <a name="measurements"></a>Misure

Le misure sono i dati provenienti dal dispositivo. È possibile aggiungere più misure al modello di dispositivo per replicare le funzionalità del dispositivo.

- Le misure di **Telemetry** (Telemetria) sono i punti dati numerici che il dispositivo raccoglie nel tempo. Sono rappresentati come un flusso continuo. Un esempio è la temperatura.
- Le misure **Event** (Evento) sono dati temporizzati che hanno un particolare significato per il dispositivo. Il livello di gravità rappresenta l'importanza di un evento. Un esempio è l'errore del motore della ventola.
- Le misure **State** (Stato) rappresentano lo stato del dispositivo o dei relativi componenti nell'arco di un periodo di tempo. Ad esempio, la modalità della ventola può essere definita come modalità avente stato **operativo** o stato **arrestato**.

### <a name="create-a-telemetry-measurement"></a>Creare una misura di telemetria

Per aggiungere una nuova misura i dati di telemetria, selezionare **+ nuova misura**, scegliere **telemetria** come la misurazione digitare e immettere i dettagli sul form.

> [!NOTE]
> I nomi dei campi nel modello di dispositivo devono corrispondere ai nomi delle proprietà nel codice dispositivo corrispondente, affinché la misura dei dati di telemetria venga visualizzata nell'applicazione quando un dispositivo reale è collegato. Eseguire la stessa operazione quando si configurano le impostazioni, le proprietà del dispositivo e i comandi mentre si continua a definire il modello di dispositivo nelle sezioni seguenti.

Ad esempio, è possibile aggiungere una nuova misura per i dati di telemetria della temperatura:

| Nome visualizzato        | Nome campo    |  Unità    | Min   |Max|
| --------------------| ------------- |-----------|-------|---|
| Temperatura         | temp          |  degC     |  0    |100|

![Modulo "Create Telemetry" (Crea dati di telemetria) con i dettagli per la misura della temperatura](./media/howto-set-up-template/measurementsform.png)

Dopo aver selezionato **Salva**, la misura **Temperature** (Temperatura) viene visualizzata nell'elenco delle misure. In breve tempo vengono visualizzati i dati della temperatura del dispositivo simulato.

> [!NOTE]
> Il tipo di dati della misura telemetrica è un numero in virgola mobile.

### <a name="create-an-event-measurement"></a>Creare una misura di evento

Per aggiungere una nuova misura di eventi, selezionare **+ nuova misura** e selezionare **evento** come tipo di unità di misura. Immettere i dettagli nel modulo **Crea evento**.

Specificare i dettagli **Display Name** (Nome visualizzato), **Field Name** (Nome campo) e **Severity** (Gravità) dell'evento. È possibile scegliere fra tre livelli di gravità: **Errore**, **Avviso** e **Informazioni**.

Ad esempio, è possibile aggiungere un nuovo evento **Fan Motor Error** (Errore motore ventola).

| Nome visualizzato        | Nome campo    |  Gravità predefinita |
| --------------------| ------------- |-----------|
| Errore motore ventola     | fanmotorerror |  Tipi di errore    |

![Modulo "Create Event" (Crea evento) con i dettagli per un evento motore ventola](./media/howto-set-up-template/eventmeasurementsform.png)

Dopo aver selezionato **Salva**, la misura **Fan Motor Error** (Errore motore ventola) viene visualizzata nell'elenco delle misure. In breve tempo vengono visualizzati i dati dell'evento del dispositivo simulato.

Per visualizzare ulteriori dettagli su un evento, selezionare l'icona di eventi nel grafico:

![Dettagli per l'evento "Fan Motor Error" (Errore motore ventola)](./media/howto-set-up-template/eventmeasurementsdetail.png)

> [!NOTE]
> I dati della misurazione dell'evento sono di tipo stringa.

### <a name="create-a-state-measurement"></a>Creare una misura di stato

Per aggiungere una nuova misura lo stato, selezionare la **+ nuova misura** e selezionare **stato** come tipo di unità di misura. Immettere i dettagli nel modulo **Create State** (Create state).

Specificare i valori **Display Name** (Nome visualizzato), **Field Name** e **Values** (Valori) dello stato. Ogni valore può anche essere associato a un nome visualizzato che verrà usato per mostrare il valore in tabelle e grafici.

Ad esempio, è possibile aggiungere un nuovo stato **Fan Mode** (Modalità ventola) con due valori possibili che il dispositivo può inviare: **operating** (operativo) e **stopped** (arrestato).

| Nome visualizzato | Nome campo    |  Valore 1   | Nome visualizzato | Valore 2    |Nome visualizzato  | 
| -------------| ------------- |----------- | -------------| -----------| -------------|
| Modalità ventola     | fanmode       |  1         | In funzione    |     0      | Arrestato      |

![Modulo "Edit State" (Modifica stato) con i dettagli per la modalità ventola](./media/howto-set-up-template/statemeasurementsform.png)

Dopo aver selezionato **Salva**, la misura **Fan Mode** (Modalità ventola) viene visualizzata nell'elenco delle misure. In breve tempo vengono visualizzati i dati dello stato del dispositivo simulato.

Se il dispositivo invia troppi punti dati in un periodo limitato, la misurazione dello stato viene visualizzata con un oggetto visivo diverso. Selezionare il grafico per visualizzare tutti i punti dati all'interno di tale periodo di tempo in ordine cronologico. È anche possibile restringere l'intervallo di tempo per visualizzare la misura tracciata sul grafico.

> [!NOTE]
> I dati della misurazione dello stato sono di tipo stringa.

## <a name="settings"></a>Impostazioni

Le impostazioni consentono di controllare un dispositivo. Consentono agli operatori di inviare dati al dispositivo. È possibile aggiungere più impostazioni al modello di dispositivo che verranno visualizzate come riquadri nella scheda **Settings** (Impostazioni) e che gli operatori potranno utilizzare. È possibile aggiungere molti tipi di impostazioni: number (numero), text (testo), date (data), toggle (attivazione/disattivazione), pick list (elenco di selezione) e section label (etichetta sezione).

Le impostazioni possono presentare uno di tre stati possibili. Il dispositivo segnala questi stati.

- **Sincronizzato**: il dispositivo è stato modificato per riflettere il valore dell'impostazione.

- **Pending**: il dispositivo sta applicando il valore dell'impostazione.

- **Errore**: il dispositivo ha restituito un errore.

Ad esempio, è possibile aggiungere una nuova impostazione della velocità della ventola selezionando **le impostazioni** e immettendo nella nuova **numero** impostazione:

| Nome visualizzato  | Nome campo    |  Unità  | Decimali |Initial|
| --------------| ------------- |---------| ---------|---- |
| Velocità della ventola     | fanSpeed      | RPM     | 2        | 0   |

![Modulo "Configure Number" (Configurare numero) con i dettagli delle impostazioni di velocità](./media/howto-set-up-template/settingsform.png)

Dopo aver selezionato **Salva**, l'impostazione **Fan Speed** (Velocità ventola) viene visualizzata come riquadro. Un operatore può usare l'impostazione nella pagina **Device Explorer** per modificare la velocità della ventola del dispositivo.

## <a name="properties"></a>Properties

Le proprietà sono i metadati associati al dispositivo, ad esempio posizione del dispositivo e numero di serie. Aggiungere più proprietà al modello di dispositivo, che verranno visualizzate come riquadri nella scheda **Properties** (Proprietà). Una proprietà può avere un tipo, ad esempio un numero, un testo, una data, un'alternanza, una proprietà del dispositivo, un'etichetta o una posizione. Un operatore può specificare i valori delle proprietà quando crea un dispositivo e modificare questi valori in qualsiasi momento. Le proprietà del dispositivo sono di sola lettura e vengono inviate dal dispositivo all'applicazione. Un operatore non può modificare le proprietà del dispositivo. Quando si connette un dispositivo reale, il riquadro delle proprietà del dispositivo viene aggiornato nell'applicazione.

Sono disponibili due categorie di proprietà:

- Le _proprietà del dispositivo_ segnalate dal dispositivo all'applicazione IoT Central. Le proprietà del dispositivo sono valori di sola lettura riportati dal dispositivo e vengono aggiornate nell'applicazione una volta collegato un dispositivo reale.
- Le _proprietà dell'applicazione_ che vengono archiviate nell'applicazione e che possono essere modificate dall'operatore. Il dispositivo non riconosce le proprietà dell'applicazione.

È ad esempio possibile aggiungere la data dell'ultima manutenzione del dispositivo come nuova proprietà **Date** (Data) (una proprietà dell'applicazione) nella scheda **Properties** (Proprietà):

| Nome visualizzato  | Nome campo | Valore iniziale   |
| --------------| -----------|-----------------|
| Ultimo utilizzo      | lastServiced        | 01/29/2019     |

![Modulo "Configure Last Serviced" (Configura ultima manutenzione) della scheda "Properties" (Proprietà)](./media/howto-set-up-template/propertiesform.png)

Dopo aver selezionato **Save** (Salva), la data dell'ultima manutenzione del dispositivo viene visualizzata come riquadro.

Dopo aver creato un riquadro è possibile modificare il valore della proprietà dell'applicazione in **Device Explorer**.

### <a name="create-a-location-property-through-azure-maps"></a>Creare una proprietà di posizione con Mappe di Azure

È possibile fornire un contesto geografico per i dati di posizione in Azure IoT Central e mappare qualsiasi coordinata di latitudine e di longitudine di un indirizzo stradale. Oppure è possibile eseguire il mapping delle coordinate di latitudine e longitudine. Mappe di Azure abilita questa funzionalità in IoT Central.

Esistono due tipi di proprietà di posizione che è possibile aggiungere:

- **Location as an Application property** (Posizione come proprietà dell'applicazione) che viene archiviata nell'applicazione. Il dispositivo non riconosce le proprietà dell'applicazione.
- **Location as a Device property** (Posizione come proprietà del dispositivo) che il dispositivo segnala all'applicazione.

#### <a name="add-location-as-an-application-property"></a>Aggiungere la posizione come proprietà dell'applicazione

È possibile creare una proprietà di posizione come proprietà dell'applicazione usando Mappe di Azure nell'applicazione IoT Central. È ad esempio possibile aggiungere l'indirizzo di installazione del dispositivo:

1. Passare alla scheda **Properties** (Proprietà).

2. Nella libreria selezionare **Location** (Posizione).

3. Configurare **Display Name** (Nome visualizzato), **Nome campo** (Field Name) e facoltativamente **Initial Value** (Valore iniziale) per la posizione.

    | Nome visualizzato  | Nome campo | Valore iniziale |
    | --------------| -----------|---------| 
    | Indirizzo di installazione | installAddress | Microsoft, 1 Microsoft Way, Redmond, WA 98052   |

   ![Modulo "Configure Location" (Configura posizione) con i dettagli per la posizione](./media/howto-set-up-template/locationcloudproperty2.png)

   Esistono due formati supportati per aggiungere una posizione:
   - **Location come indirizzo**
   - **Location come coordinate**

4. Selezionare **Salva**. Un operatore può aggiornare il valore della posizione in **Device Explorer**.

#### <a name="add-location-as-a-device-property"></a>Aggiunta della posizione come proprietà del dispositivo

È possibile creare una proprietà di posizione come proprietà del dispositivo che il dispositivo segnala. Ad esempio, se si vuole tenere traccia della posizione del dispositivo:

1. Passare alla scheda **Properties** (Proprietà).

2. Selezionare **Device Property** (Proprietà dispositivo) nella libreria.

3. Configurare il nome visualizzato e il nome di campo, quindi scegliere **Location** (Posizione) come tipo di dati:

    | Nome visualizzato  | Nome campo | Tipo di dati |
    | --------------| -----------|-----------|
    | Posizione dispositivo | deviceLocation | location  |

   > [!NOTE]
   > I nomi dei campi devono corrispondere ai nomi delle proprietà nel codice del dispositivo corrispondente

   ![Modulo "Configure Device Properties" (Configura proprietà dispositivo) con i dettagli per la posizione](./media/howto-set-up-template/locationdeviceproperty2.png)

Una volta connesso il dispositivo reale, la posizione aggiunta come proprietà del dispositivo viene aggiornata con il valore inviato dal dispositivo. Ora che è stata configurata la proprietà di posizione, sarà possibile [aggiungere una mappa per visualizzare la posizione nel dashboard del dispositivo](#add-an-azure-maps-location-in-the-dashboard).

## <a name="commands"></a>Comandi:

I comandi vengono utilizzati per gestire un dispositivo in remoto. Consentono agli operatori di eseguire i comandi nel dispositivo. È possibile aggiungere più comandi al modello di dispositivo che verranno visualizzati come riquadri nella scheda **Commands** (Comandi) e che gli operatori potranno usare. In qualità di generatore del dispositivo, si ha la possibilità di definire comandi in base alle esigenze.

In cosa differisce un comando da un'impostazione?

* **Impostazione**: un'impostazione è una configurazione che si vuole applicare a un dispositivo. Si vuole che il dispositivo renda permanente tale configurazione fino alla successiva modifica. Ad esempio, si vuole impostare la temperatura del freezer e si desidera mantenere l'impostazione anche quando il freezer viene riavviato.

* **Comando**: i comandi si usano per eseguire istantaneamente un comando nel dispositivo in modalità remota da IoT Central. Se non c'è un dispositivo connesso, il comando va in timeout e non viene eseguito. Ad esempio, si desidera riavviare un dispositivo.

Ad esempio, è possibile aggiungere una nuova **Echo** comando selezionando la **comandi** scheda, quindi selezionando **+ nuovo comando**e immettendo i dettagli del nuovo comando:

| Nome visualizzato  | Nome campo | Timeout predefinito | Tipo di dati |
| --------------| -----------|---------------- | --------- |
| Echo Command (Comando eco)  | echo (eco)       |  30             | text      |

![Modulo "Configure Command" (Configura comando) con i dettagli per Echo](./media/howto-set-up-template/commandsecho.png)

Dopo aver selezionato **Save** (Salva), il comando **Echo** (Eco) viene visualizzato come riquadro ed è pronto per essere usato da **Device Explorer** quando viene collegato il dispositivo reale. I nomi dei campi del comando devono corrispondere ai nomi delle proprietà nel codice dispositivo corrispondente, affinché i comandi vengano eseguiti correttamente.

## <a name="rules"></a>Regole

Le regole consentono agli operatori di monitorare i dispositivi quasi in tempo reale. Le regole chiamano automaticamente azioni, ad esempio l'invio di un messaggio di posta elettronica quando la regola viene attivata. Vi è un solo tipo di regola disponibile al momento:

- La **regola di telemetria** che viene attivata quando la telemetria del dispositivo selezionato supera una soglia specificata. [Altre informazioni sulle regole di telemetria](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>dashboard

Il dashboard è dove l'operatore può visualizzare informazioni su un dispositivo. In qualità di generatore, è possibile aggiungere riquadri a questa pagina per aiutare gli operatori a comprendere come si comporta il dispositivo. È possibile aggiungere più riquadri del dashboard al modello di dispositivo. È possibile aggiungere più tipi di riquadri del dashboard, ad esempio immagine, grafico a linee, grafico a barre, indicatore di prestazioni chiave, impostazioni e proprietà ed etichetta.

È ad esempio possibile aggiungere un riquadro **Settings and Properties** (Impostazioni e proprietà) per mostrare una selezione dei valori correnti di impostazioni e proprietà selezionando la scheda **Dashboard** e il riquadro dalla raccolta:

![Modulo "Configure Device Details" (Configura dettagli dispositivo) con i dettagli per impostazioni e proprietà](./media/howto-set-up-template/dashboardsettingsandpropertiesform.png)

Questo un operatore visualizza il dashboard in **Device Explorer**, ora può vedere il riquadro.

### <a name="add-an-azure-maps-location-in-the-dashboard"></a>Aggiungere una posizione di Mappe di Azure nel dashboard

Se è stata configurata una proprietà location, è possibile visualizzare la posizione usando una mappa nel dashboard del dispositivo.

1. Passare alla scheda **Dashboard**.

1. Nel dashboard del dispositivo selezionare **Map** (Mappa) nella libreria.

1. Assegnare un titolo alla mappa. L'esempio seguente presenta il titolo **Installation Location** (Posizione dell'installazione). Scegliere quindi la proprietà di posizione configurata in precedenza nella scheda **Properties** (Proprietà). Nell'esempio seguente è selezionato **Installation address** (Indirizzo di installazione).

   ![Modulo "Configure Map" (Configura mappa) con i dettagli per titolo e proprietà](./media/howto-set-up-template/locationcloudproperty5map.png)

4. Selezionare **Salva**. Il riquadro Map (Mappa) ora mostra la posizione selezionata.

Sarà possibile ridimensionare la mappa alle dimensioni desiderate. Ora, quando un operatore visualizza il dashboard in **Device Explorer**, sono visibili tutti i riquadri del dashboard che sono stati configurati, inclusa la mappa della posizione.

## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce la procedura per configurare un modello di dispositivo nell'applicazione Azure IoT Central, è possibile:

> [!div class="nextstepaction"]
> [Creare una nuova versione di un modello di dispositivo](howto-version-devicetemplate.md)