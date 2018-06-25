---
title: Configurare un modello di dispositivo nell'applicazione Azure IoT Central | Microsoft Docs
description: Informazioni su come configurare un modello di dispositivo con misure, impostazioni, proprietà, regole e dashboard.
author: viv-liu
ms.author: viviali
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: bda056a75ae9d696dab389b85fe1bfb2935ee1a8
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261985"
---
# <a name="set-up-a-device-template"></a>Configurare un modello di dispositivo

Un modello di dispositivo è un progetto che definisce le caratteristiche e i comportamenti di un tipo di dispositivo che si connette a un'applicazione Microsoft Azure IoT Central.

Ad esempio, un generatore può creare un modello per un dispositivo a ventola con connessione IoT con:

- Misura dei dati di telemetria sulla temperatura

- Misura degli eventi di errore del motore della ventola

- Misura dello stato operativo della ventola

- Impostazione della velocità della ventola

- Proprietà location

- Regole per l'invio di avvisi

- Il dashboard offre uno sguardo complessivo sul dispositivo

Da questo modello di dispositivo, un operatore può creare e connettere dei dispositivi a ventola reali a cui assegnare nomi come **fan-1** e **fan-2**. Tutti questi dispositivi a ventola hanno misure, impostazioni, proprietà, regole e un dashboard che gli utenti dell'applicazione possono monitorare e gestire.

> [!NOTE]
Solo i generatori e gli amministratori possono creare, modificare ed eliminare i modelli di dispositivo. Qualsiasi utente può creare dispositivi nella pagina **Device Explorer** (Esplora dispositivi) di un modello di dispositivo esistente.

## <a name="create-a-new-device-template"></a>Creare un nuovo modello di dispositivo

1. Passare alla pagina **Application Builder** (Generatore applicazioni).

1. Per creare un modello vuoto, scegliere **Create Device Template** (Crea modello di dispositivo), quindi scegliere **Custom** (Personalizzato).

1. Immettere un nome per il nuovo modello di dispositivo e scegliere **Create** (Crea).

    ![Pagina Device details (Dettagli dispositivo)](./media/howto-set-up-template/devicedetailspage.png)

1. A questo punto ci si trova nella pagina **Device details** (Dettagli dispositivo) di un nuovo dispositivo simulato. Quando si crea un nuovo modello di dispositivo viene creato automaticamente un dispositivo simulato. Il suo compito è segnalare i dati e può essere controllato esattamente come un dispositivo reale.

Ora esaminare ciascuna delle schede della pagina **Device details** (Dettagli dispositivo).

## <a name="measurements"></a>Measurements (Misure)

Le misure sono i dati provenienti dal dispositivo. È possibile aggiungere più misure al modello di dispositivo per replicare le funzionalità del dispositivo. Attualmente, i dati di telemetria e gli eventi sono i tipi di misura supportati.

- Le misure di **Telemetry** (Telemetria) sono i punti dati numerici che il dispositivo raccoglie nel tempo e sono rappresentati come un flusso continuo. Un esempio è la temperatura.
- Le misure **Event** (Evento) sono dati temporizzati che hanno un particolare significato per il dispositivo. Gli eventi sono associati a un livello di gravità che ne indica l'importanza. Ad esempio, errore del motore della ventola
- Le misure **State** (Stato) rappresentano lo stato del dispositivo o dei relativi componenti nell'arco di un periodo di tempo. Ad esempio, la modalità della ventola può essere definita come modalità avente stato operativo o stato arrestato.

### <a name="create-a-telemetry-measurement"></a>Creare una misura di telemetria
Per aggiungere una nuova misura di telemetria, fare clic sul pulsante **+ New Measurement** (Nuova misura) che consente di aprire un modulo con le opzioni per selezionare il tipo di misura. Selezionare **Telemetry** (Telemetria) e immettere i dettagli nel modulo **Create Telemetry** (Crea dati di telemetria).

> [!NOTE]
> Quando si connette un dispositivo reale, prestare attenzione al nome della misura segnalata dal dispositivo. Il nome deve corrispondere esattamente al **Field Name** (Nome campo) della misura.

Ad esempio, è possibile aggiungere una nuova misura per i dati di telemetria della temperatura:

![Modulo delle misure](./media/howto-set-up-template/measurementsform.png)

Dopo aver scelto **Save** (Salva), la misura **Temperature** (Temperatura) viene visualizzata nell'elenco delle misure e un operatore può vedere la visualizzazione dei dati di temperatura che il dispositivo sta raccogliendo.

![Grafico delle misure](./media/howto-set-up-template/measurementsgraph.png)

### <a name="create-an-event-measurement"></a>Creare una misura di evento
Per aggiungere una nuova misura di evento, fare clic sul pulsante **+ New Measurement** (Nuova misura) che consente di aprire un modulo con le opzioni per selezionare il tipo di misura. Selezionare **Event** (Evento) e immettere i dettagli nel modulo **Create Event** (Crea evento).

All'interno del modulo specificare **Display Name** (Nome visualizzato), **Field Name** (Nome campo) e **Severity** (Gravità) dell'evento. È possibile scegliere tra tre livelli di gravità: **Error** (Errore), **Warning** (Avviso) e **Information** (Informazioni).  

Ad esempio, è possibile aggiungere un nuovo evento 'Fan Motor Error'.

![Modulo delle misure evento](./media/howto-set-up-template/eventmeasurementsform.png)

Dopo aver scelto **Save** (Salva), la misura **Fan Motor Error** viene visualizzata nell'elenco delle misure e un operatore può vedere la visualizzazione dei dati di evento che il dispositivo sta inviando.

![Grafico delle misure evento](./media/howto-set-up-template/eventmeasurementschart.png)

Per visualizzare ulteriori dettagli sull'evento, fare clic sull'icona evento nel grafico:

![Dettaglio delle misure evento](./media/howto-set-up-template/eventmeasurementsdetail.png)


### <a name="create-a-state-measurement"></a>Creare una misura di stato
Per aggiungere una nuova misura di stato, fare clic sul pulsante **+ New Measurement** (Nuova misura) che consente di aprire un modulo con le opzioni per selezionare il tipo di misura. Selezionare **State** (Stato) e immettere i dettagli nel modulo **Create State** (Crea stato).

All'interno del modulo specificare **Display Name** (Nome visualizzato), **Field Name** (Nome campo) e i possibili **valori** dello stato. Ogni **valore** può anche essere associato a un nome visualizzato che verrà usato per mostrare il valore in tabelle e grafici.

Ad esempio, è possibile aggiungere un nuovo stato 'Fan Mode' con due valori possibili che il dispositivo può inviare: **operating** (operativo) e **stopped** (arrestato).

![Modulo delle misure stato](./media/howto-set-up-template/statemeasurementsform.png)

Dopo aver scelto **Save** (Salva), la misura stato **Fan Mode** (Modalità ventola) viene visualizzata nell'elenco delle misure e l'operatore può vedere la visualizzazione dei dati di stato che il dispositivo sta inviando.

![Grafico delle misure stato](./media/howto-set-up-template/statemeasurementschart.png)

Nel caso in cui siano presenti troppi punti dati inviati dal dispositivo in un periodo limitato, la misura stato viene visualizzata con un oggetto visivo diverso, come illustrato di seguito. Fare clic sul grafico per visualizzare tutti i punti dati all'interno di tale periodo di tempo in ordine cronologico. È anche possibile restringere l'intervallo di tempo per visualizzare la misura tracciata sul grafico.

![Dettaglio delle misure stato](./media/howto-set-up-template/statemeasurementsdetail.png)


## <a name="settings"></a>Impostazioni

Le impostazioni consentono di controllare un dispositivo. Consentono agli operatori dell'applicazione di inviare dati al dispositivo. È possibile aggiungere più impostazioni al modello di dispositivo che verranno visualizzate come riquadri nella scheda **Settings** (Impostazioni) e che gli operatori potranno utilizzare. È possibile aggiungere sei tipi di impostazioni: number (numero), text (testo), date (data), toggle (attivazione/disattivazione), pick list (elenco di selezione) e section label (etichetta sezione).

> [!NOTE]
> Quando si connette un dispositivo reale, prestare attenzione al nome dell'impostazione segnalata dal dispositivo. Il nome deve corrispondere esattamente al **Field Name** (Nome campo) dell'impostazione.

Le impostazioni possono presentare uno di tre stati possibili. Questi stati sono segnalati dal dispositivo.

- **Synced** (Sincronizzato): il dispositivo è stato modificato per riflettere il valore dell'impostazione.

- **Pending** (In sospeso): il dispositivo sta applicando il valore dell'impostazione.

- **Error** (Errore): il dispositivo ha restituito un errore.

Ad esempio, è possibile aggiungere una nuova impostazione di velocità ventola:

![Modulo delle impostazioni](./media/howto-set-up-template/settingsform.png)

Dopo aver scelto **Save** (Salva), l'impostazione **Fan speed** (Velocità ventola) viene visualizzata come riquadro ed è pronta per essere utilizzata per modificare la velocità del dispositivo.

> [!NOTE]
> Dopo aver creato un nuovo riquadro, provare la nuova impostazione. In primo luogo, disattivare la modalità di progettazione nella parte superiore destra della schermata:

![Riquadro Settings (Impostazioni)](./media/howto-set-up-template/settingstile.png)

## <a name="properties"></a>Properties

Le proprietà sono i metadati associati al dispositivo, ad esempio posizione dispositivo e numero di serie. È possibile aggiungere più proprietà al modello di dispositivo, che verranno visualizzate come riquadri nella scheda **Properties** (Proprietà). Un operatore può specificare i valori delle proprietà quando crea un nuovo dispositivo e modificare questi valori in qualsiasi momento. Esistono sei tipi di proprietà che si possono aggiungere: number (numero), text (testo), date (data), toggle (attivazione/disattivazione), device property (proprietà dispositivo) e label (etichetta).

Sono disponibili due tipi di proprietà:

- **Device properties** (Proprietà dispositivo) sono proprietà segnalate dal dispositivo.
- **Application properties** (Proprietà applicazione) sono proprietà archiviate esclusivamente nell'applicazione. Il dispositivo non ha alcuna conoscenza delle proprietà dell'applicazione.

> [!NOTE]
> Per le proprietà del dispositivo, quando si connette un dispositivo reale, prestare attenzione al nome della proprietà segnalata dal dispositivo. Il nome deve corrispondere esattamente al **Field Name** (Nome campo) della proprietà. Per le proprietà dell'applicazione, il nome del campo può essere un nome qualsiasi, purché sia univoco nel modello di dispositivo.

Ad esempio, è possibile aggiungere device location (posizione dispositivo) come nuova proprietà:

![Modulo delle proprietà](./media/howto-set-up-template/propertiesform.png)

Dopo aver scelto **Save** (Salva), device location (posizione dispositivo) viene visualizzato come riquadro:

![Riquadro Properties (Proprietà)](./media/howto-set-up-template/propertiestile.png)

> [!NOTE]
> Dopo aver creato un nuovo riquadro, è possibile modificare il valore della proprietà. In primo luogo, disattivare la modalità progettazione nella parte superiore destra della schermata.

### <a name="create-a-location-property-powered-by-azure-maps"></a>Creare una proprietà Location con tecnologia Mappe di Azure
È possibile fornire un contesto geografico per i dati di posizione in Azure IoT Central e mappare qualsiasi coordinata di latitudine e di longitudine di un indirizzo stradale o mappare semplicemente le coordinate di latitudine e longitudine. Questa funzionalità in Azure IoT Central usa le Mappe di Azure.

Esistono due tipi di proprietà Location che è possibile aggiungere:
- **Location as an Application property** (Posizione come proprietà dell'applicazione) che verrà archiviata esclusivamente nell'applicazione. Il dispositivo non ha alcuna conoscenza delle proprietà dell'applicazione.
- **Location as a Device property** (Posizione come proprietà del dispositivo) che verrà segnalata dal dispositivo.

####<a name="adding-location-as-an-application-property"></a>Aggiunta di Location come proprietà dell'applicazione 
È possibile creare una proprietà Location come proprietà dell'applicazione usando Mappe di Azure nell'applicazione Azure IoT Central. Ad esempio, è possibile aggiungere l'indirizzo di installazione del dispositivo. 

1. Passare alla scheda Device Property (Proprietà dispositivo); verificare che sia abilitata Modalità progettazione.

![Proprietà Location](./media/howto-set-up-template/locationcloudproperty1.png)

2. Nella scheda Property (Proprietà), fare clic su Location.
3. Configurare Nome visualizzato, Nome campo e facoltativamente il valore iniziale di Location. 

![Formato della proprietà Location](./media/howto-set-up-template/locationcloudproperty2.png)

Esistono due formati supportati per aggiungere una posizione:
- **Location come indirizzo**
- **Location come coordinate** 

4. Fare clic su Save. 

![Campo Proprietà location](./media/howto-set-up-template/locationcloudproperty3.png)

Un operatore può aggiornare il valore della posizione nel formato del campo della posizione. 

####<a name="adding-location-as-a-device-property"></a>Aggiunta di Location come una proprietà del dispositivo 

È possibile creare una proprietà Location come una proprietà del dispositivo che viene segnalata dal dispositivo.
Ad esempio, se si desidera tenere traccia il percorso del dispositivo.

1.  Passare alla scheda Device Property (Proprietà dispositivo); verificare che sia abilitata Modalità progettazione.
2.  Fare clic su Device Property (Proprietà dispositivo) dalla libreria.

![Campo Proprietà Location](./media/howto-set-up-template/locationdeviceproperty1.png)

3.  Configurare il nome visualizzato, il nome del campo e scegliere "location" come tipo di dati. 

> [!NOTE]
Il nome del campo deve corrispondere esattamente al nome della proprietà fornito dal dispositivo. 

![Campo Proprietà Location](./media/howto-set-up-template/locationdeviceproperty2.png)

![Vista operatore della proprietà Location](./media/howto-set-up-template/locationdeviceproperty2.png)

Ora che è stata configurata la proprietà Location, sarà possibile aggiungere una mappa per visualizzare la posizione nel Dashboard del dispositivo. Vedere come [aggiungere un percorso a Mappe di Azure nel Dashboard](howto-set-up-template.md).




## <a name="rules"></a>Regole

Le regole consentono agli operatori di monitorare i dispositivi quasi in tempo reale. Le regole richiamano automaticamente **azioni**, ad esempio l'invio di un messaggio di posta elettronica quando la regola viene attivata. Vi è un tipo di regola disponibile al momento:

- **Telemetry rule** (Regola di telemetria): una regola di telemetria viene attivata quando la telemetria del dispositivo selezionato supera una soglia specificata. Altre informazioni sulle [regole di telemetria](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>dashboard

Il dashboard è dove l'operatore può visualizzare informazioni su un dispositivo. In qualità di generatore, è possibile aggiungere riquadri a questa pagina per aiutare gli operatori a comprendere come si comporta il dispositivo. È possibile aggiungere più riquadri del dashboard al modello di dispositivo. Esistono sei tipi di riquadri del dashboard che è possibile aggiungere: image (immagine), chart (grafico), bar chart (grafico a barre), KPI (indicatore KPI), settings and properties (impostazioni e proprietà) e label (etichetta).

Ad esempio, è possibile aggiungere un riquadro **Settings and Properties** (Impostazioni e proprietà) per mostrare una selezione dei valori correnti di impostazioni e proprietà:

![Modulo dei dettagli del dispositivo nel dashboard](./media/howto-set-up-template/dashboardsettingsandpropertiesform.png)

Quando un operatore visualizza il dashboard, ora potrà vedere questo riquadro con le proprietà e le impostazioni del dispositivo:

![Riquadro Dashboard](./media/howto-set-up-template/dashboardtile.png)

### <a name="add-location-azure-map-in-dashboard"></a>Aggiungere un percorso a Mappe di Azure nel Dashboard

Se è stata configurata una proprietà Location come illustrato nei passaggi [Creare una proprietà Location con tecnologia Mappe di Azure]((howto-set-up-template.md), sarà possibile visualizzare la posizione usando una mappa direttamente nel dashboard del dispositivo.

1.  Passare alla scheda Device Dashboard (Dashboard dispositivo); verificare che sia abilitata Modalità progettazione.
2.  Su Device Dashboard (Dashboard dispositivo), selezionare Mappa dalla libreria. 

![Selezione percorso dashboard di Mappe di Azure](./media/howto-set-up-template/locationcloudproperty4map.png)

3.  Assegnare un titolo e scegliere la proprietà Location configurata in precedenza come parte della proprietà del dispositivo.

![Configurazione percorso dashboard di Mappe di Azure](./media/howto-set-up-template/locationcloudproperty5map.png)

4.  Salvare e nel riquadro della mappa verrà visualizzata la posizione selezionata. 

![Visualizzazione percorso dashboard di Mappe di Azure](./media/howto-set-up-template/locationcloudproperty6map.png) 

Sarà possibile ridimensionare la mappa alle dimensioni desiderate.

Ora quando un operatore visualizza il dashboard, è possibile visualizzare tutti i riquadri del Dashboard che sono stati configurati includendo una mappa della posizione.

![Dashboard percorso dashboard di Mappe di Azure](./media/howto-set-up-template/locationcloudproperty7map.png) 



## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce la procedura per configurare un modello di dispositivo nell'applicazione Azure IoT Central, il prossimo passaggio suggerito è:

> [!div class="nextstepaction"]
> [Creare una nuova versione di un modello di dispositivo](howto-version-devicetemplate.md)
