---
title: Configurare un modello di dispositivo nell'applicazione Azure IoT Central | Microsoft Docs
description: Informazioni su come configurare un modello di dispositivo con misure, impostazioni, proprietà, regole e un dashboard.
author: viv-liu
ms.author: viviali
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: c4df07174a5d8826acd7682fa3035fcfd201c5c9
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72953096"
---
# <a name="set-up-a-device-template"></a>Configurare un modello di dispositivo

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Un modello di dispositivo è un progetto che definisce le caratteristiche e i comportamenti di un tipo di dispositivo che si connette a un'applicazione Azure IoT Central.

Un generatore, ad esempio, può creare un modello di dispositivo per una ventola connessa con le caratteristiche seguenti:

- Misura dei dati di telemetria sulla temperatura
- Misurazione della posizione
- Misura degli eventi di errore del motore della ventola
- Misura dello stato operativo della ventola
- Impostazione della velocità della ventola
- Regole per l'invio di avvisi
- Il dashboard offre una visione complessiva del dispositivo

Da questo modello di dispositivo, un operatore può creare e connettere dei dispositivi a ventola reali a cui assegnare nomi come **fan-1** e **fan-2**. Tutti questi dispositivi a ventola hanno misure, impostazioni, proprietà, regole e un dashboard che gli utenti dell'applicazione possono monitorare e gestire.

> [!NOTE]
> Solo i generatori e gli amministratori possono creare, modificare ed eliminare i modelli di dispositivo. Qualsiasi utente può creare dispositivi nella pagina **Device Explorer** (Esplora dispositivi) di un modello di dispositivo esistente.

## <a name="create-a-device-template"></a>Creare un modello di dispositivo

1. Passare alla pagina **Modelli di dispositivo**.

2. Per creare un modello, iniziare selezionando **+ nuovo**.

3. Per iniziare rapidamente, scegliere tra i modelli predefiniti esistenti. In caso contrario, selezionare **personalizzato**, immettere un nome e fare clic su **Crea** per creare un modello personalizzato da zero.

   ![Libreria di modelli di dispositivo](./media/howto-set-up-template/newtemplate.png)

4. Quando si crea un modello personalizzato, viene visualizzata la pagina dei **Dettagli del dispositivo** per il nuovo modello di dispositivo. IoT Central crea automaticamente un dispositivo simulato quando si crea un modello di dispositivo. Un dispositivo simulato consente di testare il comportamento dell'applicazione prima di collegare un dispositivo reale.

Le sezioni seguenti descrivono ogni scheda della pagina **Modello di dispositivo**.

## <a name="measurements"></a>Measurements (Misure)

Le misure sono i dati provenienti dal dispositivo. È possibile aggiungere più misure al modello di dispositivo per replicare le funzionalità del dispositivo.

- Le misure di **Telemetry** (Telemetria) sono i punti dati numerici che il dispositivo raccoglie nel tempo. Sono rappresentati come un flusso continuo. Un esempio è la temperatura.
- Le misure **Event** (Evento) sono dati temporizzati che hanno un particolare significato per il dispositivo. Il livello di gravità rappresenta l'importanza di un evento. Un esempio è l'errore del motore della ventola.
- Le misure **State** (Stato) rappresentano lo stato del dispositivo o dei relativi componenti nell'arco di un periodo di tempo. Ad esempio, la modalità della ventola può essere definita come modalità avente stato **operativo** o stato **arrestato**.
- Le misurazioni della **posizione** sono le coordinate della longitudine e della latitudine del dispositivo in un periodo di tempo in. Una ventola, ad esempio, può essere spostata da una posizione a un'altra.

### <a name="create-a-telemetry-measurement"></a>Creare una misura di telemetria

Per aggiungere una nuova misura di telemetria, selezionare **+ nuova misurazione**, scegliere **telemetria** come tipo di misura e immettere i dettagli sul modulo.

> [!NOTE]
> I nomi dei campi nel modello di dispositivo devono corrispondere ai nomi delle proprietà nel codice dispositivo corrispondente, affinché la misura dei dati di telemetria venga visualizzata nell'applicazione quando un dispositivo reale è collegato. Eseguire la stessa operazione quando si configurano le impostazioni, le proprietà del dispositivo e i comandi mentre si continua a definire il modello di dispositivo nelle sezioni seguenti.

Ad esempio, è possibile aggiungere una nuova misura per i dati di telemetria della temperatura:

| Nome visualizzato        | Nome campo    |  Unità    | Min   |Max|
| --------------------| ------------- |-----------|-------|---|
| Temperatura         | temp          |  degC     |  0    |100|

![Modulo "Create Telemetry" (Crea dati di telemetria) con i dettagli per la misura della temperatura](./media/howto-set-up-template/measurementsform.png)

Dopo aver selezionato **Salva**, la misura **Temperature** (Temperatura) viene visualizzata nell'elenco delle misure. In breve tempo vengono visualizzati i dati della temperatura del dispositivo simulato.

Quando si visualizzano i dati di telemetria, è possibile scegliere tra le opzioni di aggregazione seguenti: Average, Minimum, Maximum, Sum e count. Viene selezionata l'opzione **media** come aggregazione predefinita nel grafico.

> [!NOTE]
> Il tipo di dati della misura telemetrica è un numero in virgola mobile.

### <a name="create-an-event-measurement"></a>Creare una misura di evento

Per aggiungere una nuova misurazione evento, selezionare **+ nuova misurazione** e selezionare **evento** come tipo di misurazione. Immettere i dettagli nel modulo **Crea evento**.

Specificare i dettagli **Display Name** (Nome visualizzato), **Field Name** (Nome campo) e **Severity** (Gravità) dell'evento. È possibile scegliere fra tre livelli di gravità: **Error** (Errore), **Warning** (Avviso) e **Information** (Informazioni).

Ad esempio, è possibile aggiungere un nuovo evento **Fan Motor Error** (Errore motore ventola).

| Nome visualizzato        | Nome campo    |  Gravità predefinita |
| --------------------| ------------- |-----------|
| Errore motore ventola     | fanmotorerror |  Errore    |

![Modulo "Create Event" (Crea evento) con i dettagli per un evento motore ventola](./media/howto-set-up-template/eventmeasurementsform.png)

Dopo aver selezionato **Salva**, la misura **Fan Motor Error** (Errore motore ventola) viene visualizzata nell'elenco delle misure. In breve tempo vengono visualizzati i dati dell'evento del dispositivo simulato.

Per visualizzare ulteriori dettagli su un evento, selezionare l'icona dell'evento nel grafico:

![Dettagli per l'evento "Fan Motor Error" (Errore motore ventola)](./media/howto-set-up-template/eventmeasurementsdetail.png)

> [!NOTE]
> I dati della misurazione dell'evento sono di tipo stringa.

### <a name="create-a-state-measurement"></a>Creare una misura di stato

Per aggiungere una nuova misurazione dello stato, selezionare il pulsante **+ nuova misurazione** e selezionare **stato** come tipo di misurazione. Immettere i dettagli nel modulo **Create State** (Create state).

Specificare i valori **Display Name** (Nome visualizzato), **Field Name** e **Values** (Valori) dello stato. Ogni valore può anche essere associato a un nome visualizzato che verrà usato per mostrare il valore in tabelle e grafici.

Ad esempio, è possibile aggiungere un nuovo stato **Fan Mode** (Modalità ventola) con due valori possibili che il dispositivo può inviare: **operating** (operativo) e **stopped** (arrestato).

| Nome visualizzato | Nome campo    |  Valore 1   | Nome visualizzato | Valore 2    |Nome visualizzato  |
| -------------| ------------- |----------- | -------------| -----------| -------------|
| Modalità ventola     | fanmode       |  1         | In funzione    |     0      | Arrestata      |

![Modulo "Edit State" (Modifica stato) con i dettagli per la modalità ventola](./media/howto-set-up-template/statemeasurementsform.png)

Dopo aver selezionato **Salva**, la misura **Fan Mode** (Modalità ventola) viene visualizzata nell'elenco delle misure. In breve tempo vengono visualizzati i dati dello stato del dispositivo simulato.

Se il dispositivo invia troppi punti dati in un periodo limitato, la misurazione dello stato viene visualizzata con un oggetto visivo diverso. Selezionare il grafico per visualizzare tutti i punti dati entro tale periodo di tempo in ordine cronologico. È anche possibile restringere l'intervallo di tempo per visualizzare la misura tracciata sul grafico.

> [!NOTE]
> I dati della misurazione dello stato sono di tipo stringa.

### <a name="create-a-location-measurement"></a>Creare una misurazione della posizione

Per aggiungere una nuova misurazione del percorso, selezionare **+ nuova misurazione**, scegliere **percorso** come tipo di misura e immettere i dettagli sul modulo **Crea misura** .

Ad esempio, è possibile aggiungere una nuova misurazione della telemetria del percorso:

| Nome visualizzato        | Nome campo    |
| --------------------| ------------- |
| Percorso Asset      |  assetloc     |

![Modulo "Crea percorso" con i dettagli relativi alla misurazione della posizione](./media/howto-set-up-template/locationmeasurementsform.png)

Dopo aver selezionato **Salva**, la misurazione della **posizione** viene visualizzata nell'elenco delle misurazioni. In breve, viene visualizzata la visualizzazione dei dati del percorso dal dispositivo simulato.

Quando si Visualizza il percorso, è possibile scegliere tra le opzioni seguenti: posizione più recente e cronologia località. La **cronologia della località** viene applicata solo nell'intervallo di tempo selezionato.

Il tipo di dati della misurazione del percorso è un oggetto che contiene Longitudine, latitudine e un'altitudine facoltativa. Il frammento di codice seguente mostra la struttura JavaScript:

```javascript
assetloc: {
  lon: floating point number,
  lat: floating point number,
  alt?: floating point number
}
```

Una volta connesso il dispositivo reale, il percorso aggiunto come misura viene aggiornato con il valore inviato dal dispositivo. Dopo aver configurato la misurazione della posizione, è possibile [aggiungere una mappa per visualizzare il percorso nel dashboard del dispositivo](#add-a-location-measurement-in-the-dashboard).

## <a name="settings"></a>Impostazioni

Le impostazioni consentono di controllare un dispositivo. Consentono agli operatori di inviare dati al dispositivo. È possibile aggiungere più impostazioni al modello di dispositivo che verranno visualizzate come riquadri nella scheda **Settings** (Impostazioni) e che gli operatori potranno utilizzare. È possibile aggiungere diversi tipi di impostazioni: numero, testo, data, interruttore e etichetta di sezione.

Le impostazioni possono presentare uno di tre stati possibili. Il dispositivo segnala questi stati.

- **Synced** (Sincronizzato): il dispositivo è stato modificato per riflettere il valore dell'impostazione.

- **Pending** (In sospeso): il dispositivo sta applicando il valore dell'impostazione.

- **Error** (Errore): il dispositivo ha restituito un errore.

Ad esempio, è possibile aggiungere una nuova impostazione della velocità della ventola selezionando **Impostazioni** e immettendo nell'impostazione nuovo **numero** :

| Nome visualizzato  | Nome campo    |  Unità  | Decimali |Initial|
| --------------| ------------- |---------| ---------|---- |
| Velocità della ventola     | fanSpeed      | RPM     | 2        | 0   |

![Modulo "Configure Number" (Configurare numero) con i dettagli delle impostazioni di velocità](./media/howto-set-up-template/settingsform.png)

Dopo aver selezionato **Salva**, l'impostazione **Fan Speed** (Velocità ventola) viene visualizzata come riquadro. Un operatore può usare l'impostazione nella pagina **Device Explorer** per modificare la velocità della ventola del dispositivo.

## <a name="properties"></a>properties

Le proprietà sono metadati associati al dispositivo, ad esempio la posizione e il numero di serie di un dispositivo fisso. Aggiungere più proprietà al modello di dispositivo visualizzate come riquadri nella scheda **Proprietà** . Una proprietà ha un tipo, ad esempio numero, testo, data, interruttore, proprietà del dispositivo, etichetta o posizione fissa. Un operatore specifica i valori per le proprietà durante la creazione di un dispositivo e può modificare questi valori in qualsiasi momento. Le proprietà del dispositivo sono di sola lettura e vengono inviate dal dispositivo all'applicazione. Un operatore non può modificare le proprietà del dispositivo. Quando un dispositivo reale si connette, il riquadro delle proprietà del dispositivo viene aggiornato nell'applicazione.

Sono disponibili due categorie di proprietà:

- Le _proprietà del dispositivo_ segnalate dal dispositivo all'applicazione IoT Central. Le proprietà del dispositivo sono valori di sola lettura riportati dal dispositivo e vengono aggiornate nell'applicazione una volta collegato un dispositivo reale.
- Le _proprietà dell'applicazione_ che vengono archiviate nell'applicazione e che possono essere modificate dall'operatore. Le proprietà dell'applicazione vengono archiviate solo nell'applicazione e non vengono mai visualizzate da un dispositivo.

È ad esempio possibile aggiungere la data dell'ultima manutenzione del dispositivo come nuova proprietà **Date** (Data) (una proprietà dell'applicazione) nella scheda **Properties** (Proprietà):

| Nome visualizzato  | Nome campo | Valore iniziale   |
| --------------| -----------|-----------------|
| Ultimo utilizzo      | lastServiced        | 01/29/2019     |

![Modulo "Configure Last Serviced" (Configura ultima manutenzione) della scheda "Properties" (Proprietà)](./media/howto-set-up-template/propertiesform.png)

Dopo aver selezionato **Save** (Salva), la data dell'ultima manutenzione del dispositivo viene visualizzata come riquadro.

Dopo aver creato un riquadro è possibile modificare il valore della proprietà dell'applicazione in **Device Explorer**.

### <a name="create-a-location-property"></a>Creare una proprietà Location

È possibile assegnare un contesto geografico ai dati della località in Azure IoT Central ed eseguire il mapping di qualsiasi coordinate di latitudine e longitudine o di un indirizzo via. Mappe di Azure abilita questa funzionalità in IoT Central.

Esistono due tipi di proprietà di posizione che è possibile aggiungere:

- **Location as an Application property** (Posizione come proprietà dell'applicazione) che viene archiviata nell'applicazione. Le proprietà dell'applicazione vengono archiviate solo nell'applicazione e non vengono mai visualizzate da un dispositivo.
- **Location as a Device property** (Posizione come proprietà del dispositivo) che il dispositivo segnala all'applicazione. Questo tipo di proprietà è più adatto per una posizione statica.

> [!NOTE]
> Il percorso come proprietà non registra una cronologia. Se si desidera la cronologia, utilizzare una misurazione della posizione.

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

Una volta connesso il dispositivo reale, il percorso aggiunto come proprietà del dispositivo viene aggiornato con il valore inviato dal dispositivo. Dopo aver configurato la proprietà location, è possibile [aggiungere una mappa per visualizzare il percorso nel dashboard del dispositivo](#add-a-location-property-in-the-dashboard).

## <a name="commands"></a>Comandi:

I comandi vengono utilizzati per gestire un dispositivo in remoto. Consentono agli operatori di eseguire i comandi nel dispositivo. È possibile aggiungere più comandi al modello di dispositivo che verranno visualizzati come riquadri nella scheda **Commands** (Comandi) e che gli operatori potranno usare. In qualità di generatore del dispositivo, si ha la possibilità di definire comandi in base alle esigenze.

In cosa differisce un comando da un'impostazione?

- **Impostazione**: un'impostazione è una configurazione che si vuole applicare a un dispositivo. Si vuole che il dispositivo renda permanente tale configurazione fino alla successiva modifica. Ad esempio, si vuole impostare la temperatura del freezer e si desidera mantenere l'impostazione anche quando il freezer viene riavviato.

- **Comando**: i comandi si usano per esegue istantaneamente un comando nel dispositivo in modalità remota da IoT Central. Se non c'è un dispositivo connesso, il comando va in timeout e non viene eseguito. Ad esempio, si desidera riavviare un dispositivo.

Ad esempio, è possibile aggiungere un nuovo comando **echo** selezionando la scheda **comandi** , quindi selezionando **+ nuovo comando**e immettendo i dettagli del nuovo comando:

| Nome visualizzato  | Nome campo | Timeout predefinito | Tipo di dati |
| --------------| -----------|---------------- | --------- |
| Echo Command (Comando eco)  | echo (eco)       |  30             | text      |

![Modulo "Configure Command" (Configura comando) con i dettagli per Echo](./media/howto-set-up-template/commandsecho1.png)

Dopo aver selezionato **Save** (Salva), il comando **Echo** (Eco) viene visualizzato come riquadro ed è pronto per essere usato da **Device Explorer** quando viene collegato il dispositivo reale. I nomi dei campi del comando devono corrispondere ai nomi delle proprietà nel codice del dispositivo corrispondente affinché i comandi vengano eseguiti correttamente.

[Ecco il collegamento al codice del dispositivo C di esempio.](https://github.com/Azure/iot-central-firmware/blob/ad40358906aeb8f2040a822ba5292df866692c16/MXCHIP/mxchip_advanced/src/AzureIOTClient.cpp#L34)

## <a name="rules"></a>Regole

Le regole consentono agli operatori di monitorare i dispositivi quasi in tempo reale. Le regole chiamano automaticamente azioni, ad esempio l'invio di un messaggio di posta elettronica quando la regola viene attivata. Vi è un solo tipo di regola disponibile al momento:

- La **regola di telemetria** che viene attivata quando la telemetria del dispositivo selezionato supera una soglia specificata. [Altre informazioni sulle regole di telemetria](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>dashboard

Il dashboard è il punto in cui un operatore passa a visualizzare le informazioni relative a un dispositivo. In qualità di generatore, i riquadri vengono aggiunti a questa pagina per aiutare gli operatori a comprendere il comportamento del dispositivo. È possibile aggiungere più tipi di riquadri del dashboard, ad esempio immagine, grafico a linee, grafico a barre, indicatore di prestazioni chiave, impostazioni e proprietà ed etichetta.

È ad esempio possibile aggiungere un riquadro **Settings and Properties** (Impostazioni e proprietà) per mostrare una selezione dei valori correnti di impostazioni e proprietà selezionando la scheda **Dashboard** e il riquadro dalla raccolta:

![Modulo "Configure Device Details" (Configura dettagli dispositivo) con i dettagli per impostazioni e proprietà](./media/howto-set-up-template/dashboardsettingsandpropertiesform1.png)

Questo un operatore visualizza il dashboard in **Device Explorer**, ora può vedere il riquadro.

### <a name="add-a-location-measurement-in-the-dashboard"></a>Aggiungere una misurazione del percorso nel dashboard

Se è stata configurata una misurazione della posizione, è possibile visualizzare la località con una mappa nel dashboard del dispositivo. Per le misurazioni della posizione, è possibile tracciare la cronologia della posizione.

1. Passare alla scheda **Dashboard**.

1. Nel dashboard del dispositivo selezionare **Map** (Mappa) nella libreria.

1. Assegnare un titolo alla mappa. Nell'esempio seguente è presente la **posizione corrente del dispositivo**titolo. Quindi scegliere la misurazione della posizione configurata in precedenza nella scheda **misurazioni** . Nell'esempio seguente è selezionata la misurazione **percorso Asset** :

   ![Modulo "Configure Map" (Configura mappa) con i dettagli per titolo e proprietà](./media/howto-set-up-template/locationcloudproperty5map.png)

1. Selezionare **Salva**. Il riquadro Map (Mappa) ora mostra la posizione selezionata.

È possibile ridimensionare il riquadro mappa. Quando un operatore Visualizza il dashboard nella **Device Explorer**, sono visibili tutti i riquadri del dashboard configurati, inclusa la mappa della località.

### <a name="add-a-location-property-in-the-dashboard"></a>Aggiungere una proprietà location nel dashboard

Se è stata configurata una proprietà location, è possibile visualizzare la località con una mappa nel dashboard del dispositivo.

1. Passare alla scheda **Dashboard**.

1. Nel dashboard del dispositivo selezionare **Map** (Mappa) nella libreria.

1. Assegnare un titolo alla mappa. Nell'esempio seguente è presente la **posizione corrente del dispositivo**titolo. Quindi scegliere la proprietà Location configurata in precedenza nella scheda **Properties (proprietà** ). Nell'esempio seguente è selezionata la misurazione del **percorso del dispositivo** :

   ![Configurare il form mappa con i dettagli relativi a titolo e proprietà](./media/howto-set-up-template/locationcloudproperty6map.png)

1. Selezionare **Salva**. Il riquadro Map (Mappa) ora mostra la posizione selezionata.

È possibile ridimensionare il riquadro mappa. Quando un operatore Visualizza il dashboard nella **Device Explorer**, sono visibili tutti i riquadri del dashboard configurati, inclusa la mappa della località.

Per altre informazioni su come usare i riquadri in Azure IoT Central, vedere [usare i riquadri del dashboard](howto-use-tiles.md).

## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce la procedura per configurare un modello di dispositivo nell'applicazione Azure IoT Central, è possibile:

- [Creare una nuova versione di un modello di dispositivo](howto-version-device-template.md)
- [Connect an MXChip IoT DevKit device to your Azure IoT Central application](howto-connect-devkit.md) (Connettere un dispositivo MXChip IoT DevKit all'applicazione Azure IoT Central)
- [Connettere un'applicazione client generica all'applicazione IoT Central di Azure (node. js)](howto-connect-nodejs.md)
