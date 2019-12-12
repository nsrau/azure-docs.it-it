---
title: Definire un nuovo tipo di dispositivo IoT in Azure IoT Central | Microsoft Docs
description: Questa esercitazione illustra come creare un nuovo modello di dispositivo Azure IoT nell'applicazione Azure IoT Central. Il creatore definisce i dati di telemetria, lo stato, le proprietà e i comandi per il tipo.
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 2127bec4d5fdf0d3bf76fb31c548eab98f910d42
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979020"
---
# <a name="tutorial-define-a-new-iot-device-type-in-your-azure-iot-central-application-preview-features"></a>Esercitazione: Definire un nuovo tipo di dispositivo IoT nell'applicazione Azure IoT Central (funzionalità in anteprima)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Un modello di dispositivo è un progetto che definisce le caratteristiche e i comportamenti di un tipo di dispositivo che si connette a un'applicazione Azure IoT Central.

Ad esempio, uno sviluppatore può creare un modello di dispositivo per una ventola connessa con le caratteristiche seguenti:

- Invia i dati di telemetria sulla temperatura
- Invia la proprietà sulla posizione
- Invia gli eventi di errore del motore della ventola
- Invia lo stato operativo della ventola
- Fornisce una proprietà della velocità della ventola scrivibile
- Fornisce un comando per riavviare il dispositivo
- Offre una visualizzazione complessiva del dispositivo tramite un dashboard

Da questo modello di dispositivo, un operatore può creare e connettere dispositivi a ventola reali. Tutte queste ventole dispongono di misurazioni, proprietà e comandi usati dagli operatori per monitorarle e gestirle. Gli operatori usano i dashboard e i moduli del dispositivo per interagire con i dispositivi della ventola.

> [!NOTE]
> Solo i generatori e gli amministratori possono creare, modificare ed eliminare i modelli di dispositivo. Qualsiasi utente può creare dispositivi nella pagina **Dispositivi** dai modelli di dispositivi esistenti.

[Plug and Play IoT](../../iot-pnp/overview-iot-plug-and-play.md) consente a IoT Central di integrare dispositivi senza dover scrivere il codice del dispositivo incorporato. La parte centrale di Plug and Play IoT Internet è costituita da uno schema del modello di funzionalità di dispositivo che descrive le funzionalità dei dispositivi. In un'applicazione IoT Central (anteprima) i modelli di dispositivo usano questi modelli di funzionalità di dispositivo di Plug and Play IoT.

I generatori hanno a disposizione numerose opzioni per creare modelli di dispositivo:

- Progettare il modello di dispositivo in IoT Central e quindi implementarne il modello di funzionalità di dispositivo nel codice del dispositivo.
- Importare un modello di funzionalità di dispositivo dal [catalogo dei dispositivi Azure Certified per IoT](https://aka.ms/iotdevcat). Aggiungere quindi al modello di dispositivo le proprietà cloud, le personalizzazioni e i dashboard necessari per l'applicazione IoT Central.
- Creare un modello di funzionalità di dispositivo con Visual Studio Code. Implementare il codice del dispositivo dal modello. Importare manualmente il modello di funzionalità di dispositivo nell'applicazione IoT Central e quindi aggiungere eventuali personalizzazioni, dashboard e proprietà cloud necessari per l'applicazione IoT Central.
- Creare un modello di funzionalità di dispositivo con Visual Studio Code. Implementare il codice del dispositivo dal modello e connettere il dispositivo reale all'applicazione IoT Central, tramite una connessione di tipo device-first. IoT Central trova e importa automaticamente il modello di funzionalità di dispositivo dal repository pubblico. È quindi possibile aggiungere al modello di dispositivo le proprietà cloud, le personalizzazioni e i dashboard necessari per l'applicazione IoT Central.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario [creare un'applicazione Azure IoT Central](quick-deploy-iot-central.md).

## <a name="create-a-device-template-from-the-device-catalog"></a>Creare un modello di dispositivo dal catalogo dispositivi

È possibile iniziare rapidamente a creare la soluzione usando un dispositivo Plug and Play IoT certificato. Vedere l'elenco nel [catalogo dei dispositivi Azure IoT](https://catalog.azureiotsolutions.com/alldevices). IoT Central è integrato con il catalogo dispositivi per consentire l'importazione di un modello di funzionalità di dispositivo da uno qualsiasi di questi dispositivi Plug and Play IoT certificati. Per creare un modello di dispositivo da uno di questi dispositivi in IoT Central:

1. Passare alla pagina **Modelli di dispositivo** nell'applicazione IoT Central.
1. Selezionare **+ Nuovo**, quindi selezionare uno dei dispositivi Plug and Play IoT certificati dal catalogo. IoT Central crea un modello di dispositivo in base a questo modello di funzionalità di dispositivo.
1. Aggiungere le proprietà cloud, le personalizzazioni o le visualizzazioni al modello di dispositivo.
1. Selezionare **Pubblica** per rendere disponibile il modello agli operatori per la visualizzazione e la connessione dei dispositivi.

## <a name="create-a-device-template-from-scratch"></a>Creare un modello di dispositivo da zero

Un modello di dispositivo contiene:

- Un _modello di funzionalità di dispositivo_ che specifica i dati di telemetria, le proprietà e i comandi implementati dal dispositivo. Queste funzionalità sono organizzate in una o più interfacce.
- _Proprietà cloud_ che definiscono le informazioni che l'applicazione IoT Central archivia sui dispositivi. Una proprietà cloud, ad esempio, potrebbe registrare i dati relativi all'ultimo intervento effettuato sul dispositivo. Queste informazioni non vengono mai condivise con il dispositivo.
- Le _personalizzazioni_ consentono allo sviluppatore di eseguire l'override di alcune delle definizioni nel modello di funzionalità di dispositivo. Lo sviluppatore può, ad esempio, eseguire l'override del nome di una proprietà del dispositivo. I nomi delle proprietà vengono visualizzati nei dashboard e nei moduli di IoT Central.
- _Dashboard e moduli_ consentono allo sviluppatore di creare un'interfaccia utente che gli operatori possono usare per monitorare e gestire i dispositivi connessi all'applicazione.

Per creare un modello di dispositivo in IoT Central:

1. Passare alla pagina **Modelli di dispositivo** nell'applicazione IoT Central.
1. Selezionare **+ Nuovo** > **Personalizzato**.
1. Immettere un nome per il modello, ad esempio **sensore ambientale**.
1. Premere **Invio**. IoT Central crea un modello di dispositivo vuoto.

## <a name="manage-a-device-template"></a>Gestire un modello di dispositivo

È possibile rinominare o eliminare un modello dalla home page del modello.

Dopo aver aggiunto un modello di funzionalità di dispositivo al modello, è possibile pubblicarlo. Fino a quando il modello non viene pubblicato, non è possibile connettere un dispositivo in base a questo modello per consentire agli operatori di visualizzarlo nella pagina **Dispositivi**.

## <a name="create-a-capability-model"></a>Creare un modello di funzionalità

Per creare un modello di funzionalità di dispositivo, è possibile:

- Usare IoT Central per creare un modello personalizzato da zero.
- Importare un modello da un file JSON. Uno sviluppatore di dispositivi potrebbe aver usato Visual Studio Code per creare un modello di funzionalità di dispositivo per l'applicazione.
- Selezionare uno dei dispositivi dal catalogo dispositivi. Questa opzione importa il modello di funzionalità di dispositivo pubblicato dal produttore per questo dispositivo. Un modello di funzionalità di dispositivo importato come questo caso viene pubblicato automaticamente.

## <a name="manage-a-capability-model"></a>Gestire un modello di funzionalità

Dopo aver creato un modello di funzionalità di dispositivo, è possibile:

- Aggiungere interfacce al modello. Un modello deve avere almeno un'interfaccia.
- Modificare i metadati del modello, ad esempio ID, spazio dei nomi e nome.
- Eliminare il modello.

## <a name="create-an-interface"></a>Creare un'interfaccia

Una funzionalità di dispositivo deve avere almeno un'interfaccia. Un'interfaccia è una raccolta riutilizzabile di funzionalità.

Per creare un'interfaccia:

1. Passare al modello di funzionalità di dispositivo e scegliere **+ Aggiungi interfaccia**.

1. Nella pagina **Selezionare un'interfaccia** è possibile:

    - Creare un'interfaccia personalizzata da zero.
    - Importare un'interfaccia esistente da un file. Uno sviluppatore di dispositivi potrebbe aver usato Visual Studio Code per creare un'interfaccia per il dispositivo.
    - Scegliere una delle interfacce standard, ad esempio l'interfaccia **Informazioni sul dispositivo**. Le interfacce standard specificano le funzionalità comuni a molti dispositivi. Queste interfacce standard vengono pubblicate da Azure IoT e non è possibile modificarle o creare versioni.

1. Dopo aver creato un'interfaccia, scegliere **Modifica identità** per modificare il nome visualizzato dell'interfaccia.

1. Se si sceglie di creare un'interfaccia personalizzata da zero, è possibile aggiungere le funzionalità di dispositivo desiderate. Le funzionalità di dispositivo sono costituite da telemetria, proprietà e comandi.

### <a name="telemetry"></a>Telemetria

La telemetria è un flusso di valori inviati dal dispositivo, in genere da un sensore. Un sensore, ad esempio, potrebbe segnalare la temperatura ambiente.

La tabella seguente illustra le impostazioni di configurazione per una funzionalità di telemetria:

| Campo | DESCRIZIONE |
| ----- | ----------- |
| Nome visualizzato | Nome visualizzato per il valore di telemetria usato nei dashboard e nei moduli. |
| NOME | Nome del campo nel messaggio di telemetria. IoT Central genera un valore per questo campo dal nome visualizzato, ma è possibile scegliere un valore personalizzato, se necessario. |
| Tipo di funzionalità | Telemetria. |
| Tipo semantico | Tipo semantico dei dati di telemetria, ad esempio temperatura, stato o evento. La scelta del tipo semantico determina quali tra i campi seguenti sono disponibili. |
| SCHEMA | Tipo di dati di telemetria, ad esempio double, stringa o vettore. Le scelte disponibili sono determinate dal tipo semantico. Lo schema non è disponibile per i tipi semantici stato ed evento. |
| Gravità | Disponibile solo per il tipo semantico evento. I livelli di gravità sono **Errore**, **Informazioni** o **Avviso**. |
| Valori di stato | Disponibile solo per il tipo semantico stato. Definisce i valori di stato possibili, ognuno dei quali ha un nome visualizzato, un nome, un tipo di enumerazione e un valore. |
| Unità | Unità per il valore di telemetria, ad esempio **mph**, **%** o **&deg;C**. |
| Unità di visualizzazione | Unità di visualizzazione da usare nei dashboard e nei moduli. |
| Comment | Eventuali commenti sulla funzionalità di telemetria. |
| DESCRIZIONE | Descrizione della funzionalità di telemetria. |

### <a name="properties"></a>Properties

Le proprietà rappresentano valori temporizzati. Ad esempio, un dispositivo potrebbe usare una proprietà per segnalare la temperatura di destinazione che sta tentando di raggiungere. È possibile impostare proprietà scrivibili da IoT Central.

La tabella seguente illustra le impostazioni di configurazione per una funzionalità della proprietà:

| Campo | DESCRIZIONE |
| ----- | ----------- |
| Nome visualizzato | Nome visualizzato per il valore della proprietà usato nei dashboard e nei moduli. |
| NOME | Nome della proprietà. IoT Central genera un valore per questo campo dal nome visualizzato, ma è possibile scegliere un valore personalizzato, se necessario. |
| Tipo di funzionalità | Proprietà. |
| Tipo semantico | Tipo semantico della proprietà, ad esempio temperatura, stato o evento. La scelta del tipo semantico determina quali tra i campi seguenti sono disponibili. |
| SCHEMA | Tipo di dati della proprietà, ad esempio double, stringa o vettore. Le scelte disponibili sono determinate dal tipo semantico. Lo schema non è disponibile per i tipi semantici stato ed evento. |
| Scrivibile | Se la proprietà non è scrivibile, il dispositivo può segnalare i valori della proprietà a IoT Central. Se la proprietà è scrivibile, il dispositivo può segnalare i valori della proprietà a IoT Central e IoT Central può inviare gli aggiornamenti sulla proprietà al dispositivo.
| Gravità | Disponibile solo per il tipo semantico evento. I livelli di gravità sono **Errore**, **Informazioni** o **Avviso**. |
| Valori di stato | Disponibile solo per il tipo semantico stato. Definisce i valori di stato possibili, ognuno dei quali ha un nome visualizzato, un nome, un tipo di enumerazione e un valore. |
| Unità | Unità per il valore della proprietà, ad esempio **mph**, **%** o **&deg;C**. |
| Unità di visualizzazione | Unità di visualizzazione da usare nei dashboard e nei moduli. |
| Comment | Eventuali commenti sulla funzionalità della proprietà. |
| DESCRIZIONE | Descrizione della funzionalità della proprietà. |

### <a name="commands"></a>Comandi:

È possibile chiamare i comandi del dispositivo da IoT Central. I comandi passano facoltativamente i parametri al dispositivo e ricevono una risposta dal dispositivo. Ad esempio, è possibile chiamare un comando per riavviare un dispositivo entro 10 secondi.

La tabella seguente illustra le impostazioni di configurazione per una funzionalità di comando:

| Campo | DESCRIZIONE |
| ----- | ----------- |
| Nome visualizzato | Nome visualizzato per il comando usato nei dashboard e nei moduli. |
| NOME | Nome del comando. IoT Central genera un valore per questo campo dal nome visualizzato, ma è possibile scegliere un valore personalizzato, se necessario. |
| Tipo di funzionalità | Comando. |
| Comando | `SynchronousExecutionType`. |
| Comment | Eventuali commenti sulla funzionalità del comando. |
| DESCRIZIONE | Descrizione della funzionalità del comando. |
| Richiesta | Se abilitata, una definizione del parametro della richiesta, tra cui: nome, nome visualizzato, schema, unità e unità di visualizzazione. |
| Risposta | Se abilitata, una definizione della risposta del comando, tra cui: nome, nome visualizzato, schema, unità e unità di visualizzazione. |

## <a name="manage-an-interface"></a>Gestire un'interfaccia

Se l'interfaccia non è stata pubblicata, è possibile modificare le funzionalità definite dall'interfaccia. Una volta pubblicata l'interfaccia, per apportare modifiche sarà necessario creare una nuova versione del modello di dispositivo e applicare il controllo delle versioni all'interfaccia. Le modifiche che non richiedono il controllo delle versioni, ad esempio i nomi visualizzati o le unità, possono essere apportate nella sezione **Personalizza**.

È anche possibile esportare l'interfaccia come file JSON se si vuole riutilizzarla in un altro modello di funzionalità.

## <a name="add-cloud-properties"></a>Aggiungere proprietà cloud

Usare le proprietà cloud per archiviare le informazioni sui dispositivi in IoT Central. Le proprietà cloud non vengono mai inviate a un dispositivo. Ad esempio, è possibile usare le proprietà cloud per archiviare il nome del cliente che ha installato il dispositivo o la data dell'ultimo intervento sul dispositivo.

La tabella seguente illustra le impostazioni di configurazione per una proprietà cloud:

| Campo | DESCRIZIONE |
| ----- | ----------- |
| Nome visualizzato | Nome visualizzato per il valore della proprietà cloud usato nei dashboard e nei moduli. |
| NOME | Nome della proprietà cloud. IoT Central genera un valore per questo campo dal nome visualizzato, ma è possibile scegliere un valore personalizzato, se necessario. |
| Tipo semantico | Tipo semantico della proprietà, ad esempio temperatura, stato o evento. La scelta del tipo semantico determina quali tra i campi seguenti sono disponibili. |
| SCHEMA | Tipo di dati della proprietà cloud, ad esempio double, stringa o vettore. Le scelte disponibili sono determinate dal tipo semantico. |

## <a name="add-customizations"></a>Aggiungere personalizzazioni

Usare le personalizzazioni quando è necessario modificare un'interfaccia importata o aggiungere funzionalità specifiche di IoT Central a una funzionalità. È possibile personalizzare solo i campi che non compromettono la compatibilità dell'interfaccia. Ad esempio, è possibile:

- Personalizzare il nome visualizzato e le unità di una funzionalità.
- Aggiungere un colore predefinito da usare quando il valore viene visualizzato in un grafico.
- Specificare i valori iniziale, minimo e massimo per una proprietà.

Non è possibile personalizzare il tipo o il nome della funzionalità. Se occorre apportare modifiche non consentite nella sezione **Personalizza**, è necessario creare una nuova versione del modello di dispositivo e dell'interfaccia per modificare la funzionalità.

### <a name="generate-default-views"></a>Generare le visualizzazioni predefinite

La generazione di visualizzazioni predefinite è un modo rapido per visualizzare le informazioni importanti sul dispositivo. Per il modello di dispositivo possono essere generate fino a tre visualizzazioni predefinite:

- **Comandi**: fornisce una visualizzazione con i comandi del dispositivo e consente all'operatore di inviarli al dispositivo.
- **Panoramica**: fornisce una visualizzazione con i dati di telemetria del dispositivo, visualizzando grafici e metriche.
- **Informazioni su**: fornisce una visualizzazione con le informazioni sul dispositivo, mostrandone le proprietà.

Dopo la selezione di **Genera visualizzazioni predefinite**, le visualizzazioni verranno aggiunte automaticamente nella sezione **Visualizzazioni** del modello di dispositivo.

## <a name="add-dashboards"></a>Aggiungere dashboard

Aggiungere dashboard a un modello di dispositivo per consentire agli operatori di visualizzare un dispositivo usando grafici e metriche. Possono essere presenti più dashboard per un modello di dispositivo.

Per aggiungere un dashboard a un modello di dispositivo:

1. Passare al modello di dispositivo e selezionare **Visualizzazioni**.
1. Scegliere quindi **Visualizzazione del dispositivo**.
1. Immettere un nome per il dashboard in **Nome del dashboard**.
1. Aggiungere al dashboard i riquadri selezionati dall'elenco di riquadri statici, proprietà, proprietà cloud, telemetria e comandi. Trascinare e rilasciare i riquadri da aggiungere al dashboard.
1. Per tracciare più valori di telemetria in un singolo riquadro del grafico, selezionare i valori di telemetria, quindi selezionare **Combina**.
1. Configurare ogni riquadro aggiunto per personalizzare la modalità di visualizzazione dei dati. A tale scopo, selezionare l'icona a forma di ingranaggio oppure selezionare **Modifica configurazione** nel riquadro del grafico.
1. Disporre e ridimensionare i riquadri nel dashboard.
1. Salvare le modifiche.

### <a name="configure-preview-device-to-view-dashboard"></a>Configurare l'anteprima del dispositivo per visualizzare il dashboard

Per visualizzare e testare il dashboard, selezionare **Configura anteprima dispositivo**. Sarà possibile visualizzare il dashboard come lo vede l'operatore dopo la pubblicazione. Usare questa opzione per assicurarsi che le visualizzazioni mostrino i dati corretti. È possibile scegliere tra le opzioni seguenti:

- Nessuna anteprima del dispositivo.
- Il dispositivo di test reale configurato per il modello di dispositivo.
- Un dispositivo esistente nell'applicazione in base all'ID dispositivo.

## <a name="add-forms"></a>Aggiungere moduli

Aggiungere moduli a un modello di dispositivo per consentire agli operatori di gestire un dispositivo visualizzando e impostando le proprietà. Gli operatori possono solo modificare le proprietà cloud e le proprietà dei dispositivi scrivibili. Possono essere presenti più moduli per un modello di dispositivo.

Per aggiungere un modulo a un modello di dispositivo:

1. Passare al modello di dispositivo e selezionare **Visualizzazioni**.
1. Scegliere **Modifica dei dati del dispositivo e del cloud**.
1. Immettere un nome per il modulo in **Nome del modulo**.
1. Selezionare il numero di colonne da usare per il layout del modulo.
1. Aggiungere le proprietà a una sezione esistente nel modulo oppure selezionare le proprietà e scegliere **Aggiungi sezione**. Usare le sezioni per raggruppare le proprietà nel modulo. È possibile aggiungere un titolo a una sezione.
1. Configurare ogni proprietà nel modulo per personalizzarne il comportamento.
1. Disporre le proprietà nel modulo.
1. Salvare le modifiche.

## <a name="publish-a-device-template"></a>Pubblicare un modello di dispositivo

Prima di poter connettere un dispositivo che implementa il modello di funzionalità di dispositivo, è necessario pubblicare il modello di dispositivo.

Dopo la pubblicazione di un modello di dispositivo, è possibile apportare solo modifiche limitate al modello di funzionalità di dispositivo. Per modificare un'interfaccia, è necessario [creare e pubblicare una nuova versione](./howto-version-device-template.md).

Per pubblicare un modello di dispositivo, passare al modello di dispositivo e selezionare **Pubblica**.

Dopo la pubblicazione di un modello di dispositivo, un operatore può accedere alla pagina **Dispositivi** e aggiungere i dispositivi reali o simulati che usano il modello. È possibile continuare a modificare e salvare il modello di dispositivo man mano che si apportano le modifiche. Per eseguire il push di queste modifiche affinché possano essere visualizzate dall'operatore nella pagina **Dispositivi**, è necessario selezionare **Pubblica** ogni volta.

## <a name="define-a-new-iot-gateway-device-type-preview-features"></a>Definire un nuovo tipo di dispositivo gateway IoT (funzionalità in anteprima)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Questa esercitazione illustra come usare un modello di dispositivo gateway per definire un nuovo tipo di dispositivo IoT nell'applicazione IoT Central. 

In questa sezione viene creato un modello di dispositivo **Edifici intelligenti**. Un dispositivo gateway per edifici intelligenti:

* Invia dati di telemetria, ad esempio temperatura e occupazione.
* Risponde alle proprietà scrivibili quando vengono aggiornate nel cloud, ad esempio l'intervallo di invio dei dati di telemetria.
* Risponde ai comandi, ad esempio per la reimpostazione della temperatura.
* Consente le relazioni con altri modelli di funzionalità di dispositivo.

### <a name="create-iot-device-templates"></a>Creare modelli di dispositivo IoT

Ecco come creare modelli di dispositivo IoT: 

1. Nel riquadro di spostamento a sinistra selezionare **Modelli di dispositivo**. Selezionare **+ Nuovo**, quindi il riquadro **Dispositivo IoT** e infine il riquadro del sensore di occupazione. Selezionare **Avanti: Personalizza**.

   ![Screenshot della pagina Modelli di dispositivo e delle opzioni](./media/tutorial-define-iot-device-type/gateway-downstream-new.png)

1. Nella pagina **Rivedi** selezionare **Crea**. 

   ![Screenshot della pagina Rivedi](./media/tutorial-define-iot-device-type/gateway-downstream-review.png)

1. Il nuovo modello di dispositivo è stato creato. 

   ![Screenshot del nuovo modello di dispositivo](./media/tutorial-define-iot-device-type/occupancy-sensor.png)

Ecco come creare un modello di dispositivo per il sensore S1:

1. Nel riquadro di spostamento a sinistra selezionare **Modelli di dispositivo**. Selezionare **+ Nuovo**, quindi il riquadro **Dispositivo IoT** e infine il riquadro del sensore di occupazione. Selezionare **Avanti: Personalizza**.

   ![Screenshot della pagina Modelli di dispositivo e delle opzioni](./media/tutorial-define-iot-device-type/s1-sensor.png)

1. Nella pagina **Rivedi** selezionare **Crea**. 

   ![Screenshot della pagina Rivedi](./media/tutorial-define-iot-device-type/s1-review.png)

1. Il nuovo modello di dispositivo è stato creato. 

   ![Screenshot del nuovo modello di dispositivo](./media/tutorial-define-iot-device-type/s1-template.png)

## <a name="create-an-iot-gateway-device-template"></a>Creare un modello di dispositivo gateway IoT

È possibile scegliere di creare un modello di dispositivo gateway IoT. Il dispositivo gateway ha relazioni con i dispositivi downstream che si connettono a IoT Central tramite il dispositivo gateway. 

### <a name="downstream-device-relationships-with-gateway-device"></a>Relazioni dei dispositivi downstream con il dispositivo gateway

I dispositivi IoT possono connettersi a un dispositivo gateway IoT.

![Diagramma della relazione tra il dispositivo gateway e i dispositivi downstream](./media/tutorial-define-iot-device-type/gatewaypattern.png)

Gli sviluppatori possono creare e modificare modelli di dispositivo gateway IoT nell'applicazione. Dopo la pubblicazione di un modello di dispositivo, è possibile connettere dispositivi reali che implementano il modello di dispositivo.

### <a name="select-a-device-template-type"></a>Selezionare un tipo di modello di dispositivo 

Per aggiungere un nuovo modello di dispositivo all'applicazione:

1. Selezionare la scheda **Modelli di dispositivo** nel riquadro sinistro.

   ![Screenshot della pagina Modelli di dispositivo](./media/tutorial-define-iot-device-type/devicetemplate.png)

1. Fare clic su **+ Nuovo** per iniziare a creare un nuovo modello di dispositivo.

   ![Screenshot della pagina Modelli di dispositivo, con la voce Nuovo evidenziata](./media/tutorial-define-iot-device-type/devicetemplatenew.png)

   ![Screenshot della pagina Personalizza dispositivo](./media/tutorial-define-iot-device-type/gateway-review.png)

1. Nella pagina **Selezionare il tipo di modello** selezionare **Azure IoT** e quindi fare clic su **Avanti: Personalizza**.

   ![Screenshot della pagina Selezionare il tipo di modello](./media/tutorial-define-iot-device-type/gateway-customize.png)

1. Selezionare la casella di controllo Gateway e fare clic su **Crea**.

   ![Screenshot della pagina Personalizza dispositivo con la voce Gateway evidenziata](./media/tutorial-define-iot-device-type/gateway-review.png)

1. Nella pagina Rivedi selezionare **Crea**. 

1. Immettere il nome del modello del gateway, **Smart Building Gateway Template**. Selezionare il riquadro **Personalizza**.

1. Aggiungere un'interfaccia standard **Informazioni sul dispositivo**.

### <a name="add-relationships"></a>Aggiungere relazioni

È possibile aggiungere relazioni downstream ai modelli di funzionalità di dispositivo per i dispositivi da connettere al dispositivo gateway.

Creare le relazioni con i modelli di funzionalità di dispositivo downstream. Selezionare **Salva**.

![Screenshot del modello di gateway per edifici intelligenti con varie opzioni evidenziate](./media/tutorial-define-iot-device-type/gateway-occupancy-s1-rel.png)

### <a name="add-cloud-properties"></a>Aggiungere proprietà cloud

Un modello di dispositivo può includere proprietà cloud. Le proprietà cloud esistono solo nell'applicazione IoT Central e non vengono mai inviate a o ricevute da un dispositivo.

1. Selezionare **Proprietà cloud** >  **+ Aggiungi proprietà cloud**. Usare le informazioni nella tabella seguente per aggiungere proprietà cloud al modello di dispositivo.

    | Nome visualizzato      | Tipo semantico | SCHEMA |
    | ----------------- | ------------- | ------ |
    | Data ultimo utilizzo | Nessuna          | Data   |
    | Nome del cliente     | Nessuna          | string |

2. Selezionare **Salva**.

### <a name="add-customizations"></a>Aggiungere personalizzazioni

Usare le personalizzazioni per modificare un'interfaccia o aggiungere funzionalità specifiche di IoT Central a una funzionalità che non richiede la creazione di una nuova versione del modello di funzionalità di dispositivo. È possibile personalizzare i campi quando il modello di funzionalità è in uno stato di bozza o pubblicato. È possibile personalizzare solo i campi che non compromettono la compatibilità dell'interfaccia. Ad esempio, è possibile:

- Personalizzare il nome visualizzato e le unità di una funzionalità.
- Aggiungere un colore predefinito da usare quando il valore viene visualizzato in un grafico.
- Specificare i valori iniziale, minimo e massimo per una proprietà.

Non è possibile personalizzare il tipo o il nome della funzionalità.

Al termine della personalizzazione, fare clic su **Salva**.

### <a name="create-views"></a>Creare viste

Il creatore può personalizzare un'applicazione in modo da visualizzare le informazioni sul dispositivo sensore ambientale utili all'operatore. Le personalizzazioni consentono all'operatore di gestire i sensori ambientali connessi all'applicazione. È possibile creare due tipi di visualizzazioni che un operatore può usare per interagire con i dispositivi:

* Moduli per visualizzare e modificare le proprietà del dispositivo e cloud.
* Dashboard per visualizzare i dispositivi.

### <a name="generate-default-views"></a>Generare le visualizzazioni predefinite

Se si seleziona **Genera visualizzazioni predefinite**, è possibile generare i dashboard **Panoramica** e **Informazioni su**. 

## <a name="publish-a-device-template"></a>Pubblicare un modello di dispositivo

Prima di poter creare un sensore ambientale simulato o di connettere un sensore ambientale reale, è necessario pubblicare il modello di dispositivo.

Per pubblicare un modello di dispositivo:

1. Passare al modello di dispositivo dalla pagina **Modelli di dispositivo**.

2. Selezionare **Pubblica**.

3. Nella finestra di dialogo **Publish a Device Template** (Pubblica un modello di dispositivo) scegliere **Pubblica**.

Dopo la pubblicazione, un modello di dispositivo viene visualizzato nella pagina **Dispositivi** ed è visibile all'operatore. In un modello di dispositivo pubblicato non è possibile modificare un modello di funzionalità di dispositivo senza creare una nuova versione. È tuttavia possibile apportare aggiornamenti a proprietà cloud, personalizzazioni e visualizzazioni in un modello di dispositivo pubblicato. Questi aggiornamenti non comportano la creazione di una nuova versione. Dopo aver apportato le modifiche, selezionare **Pubblica** per eseguire il push delle modifiche all'operatore.

## <a name="create-a-gateway-simulated-device"></a>Creare un dispositivo gateway simulato

In Esplora dispositivi creare un gateway per edifici intelligenti simulato. 

![Screenshot della finestra di dialogo Crea nuovo dispositivo](./media/tutorial-define-iot-device-type/smartbuildingdevice.png)

## <a name="create-downstream-simulated-devices"></a>Creare dispositivi simulati downstream

In Esplora dispositivi creare un sensore di occupazione simulato. 

![Screenshot della finestra di dialogo Crea nuovo dispositivo](./media/tutorial-define-iot-device-type/occupancydevice.png)

In Esplora dispositivi creare un sensore S1 simulato. 

![Screenshot della finestra di dialogo Crea nuovo dispositivo](./media/tutorial-define-iot-device-type/s1device.png)

## <a name="add-downstream-devices-relationships-to-a-gateway-device"></a>Aggiungere le relazioni dei dispositivi downstream a un dispositivo gateway

Selezionare il sensore S1 e il sensore di occupazione e fare clic su **Connetti al gateway**. 

![Screenshot del sensore di occupazione, con l'opzione Connetti al gateway evidenziata](./media/tutorial-define-iot-device-type/connecttogateway.png)

Selezionare il modello di dispositivo gateway e l'istanza del dispositivo gateway e selezionare **Aggiungi**.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

* Creare un nuovo gateway IoT come modello di dispositivo.
* Creare proprietà cloud.
* Creare personalizzazioni.
* Definire una visualizzazione per i dati di telemetria del dispositivo.
* Aggiungere relazioni.
* Pubblicare il modello di dispositivo.

Successivamente, sarà possibile:

> [!div class="nextstepaction"]
> [Connettere un dispositivo](tutorial-connect-pnp-device.md)
