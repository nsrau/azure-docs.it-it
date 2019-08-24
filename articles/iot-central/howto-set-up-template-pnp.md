---
title: Configurare un modello di dispositivo nell'applicazione Azure IoT Central | Microsoft Docs
description: Informazioni su come configurare un modello di dispositivo con misure, impostazioni, proprietà, regole e un dashboard.
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: fd7cd8f73f602953573b7e6edab32bf2a6aecc05
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69998129"
---
# <a name="set-up-and-manage-a-device-template-preview-features"></a>Configurare e gestire un modello di dispositivo (funzionalità di anteprima)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Un modello di dispositivo è un progetto che definisce le caratteristiche e i comportamenti di un tipo di dispositivo che si connette a un'applicazione Azure IoT Central.

Un generatore, ad esempio, può creare un modello di dispositivo per una ventola connessa con le caratteristiche seguenti:

- Invia la telemetria della temperatura
- Invia la proprietà Location
- Invia eventi di errore del motore ventola
- Invia lo stato operativo della ventola
- Proprietà velocità della ventola scrivibile
- Comando per riavviare il dispositivo
- Il dashboard offre una visione complessiva del dispositivo

Da questo modello di dispositivo, un operatore può creare e connettere dispositivi fan reali. Tutti questi ventilatori hanno misurazioni, proprietà e comandi usati dagli operatori per monitorare e gestire quindi. Gli operatori usano i dashboard e i moduli del dispositivo per interagire con i dispositivi della ventola.

> [!NOTE]
> Solo i generatori e gli amministratori possono creare, modificare ed eliminare i modelli di dispositivo. Qualsiasi utente può creare dispositivi nella pagina **dispositivi** da modelli di dispositivo esistenti.

Internet delle cose [plug and Play](../iot-pnp/overview-iot-plug-and-play.md) consente ai IOT Central di integrare i dispositivi senza scrivere codice dispositivo incorporato. Il nucleo del Plug and Play Internet è uno schema del modello di funzionalità del dispositivo che descrive le funzionalità del dispositivo. In un'applicazione IoT Central anteprima, i modelli di dispositivo usano questi Plug and Play i modelli di funzionalità del dispositivo.

In qualità di generatore sono disponibili diverse opzioni per la creazione di modelli di dispositivo:

- Progettare il modello di dispositivo in IoT Central e quindi implementare il modello di funzionalità del dispositivo nel codice del dispositivo.
- Importare un modello di funzionalità del dispositivo dal [Catalogo dei dispositivi Azure Certified per](https://aka.ms/iotdevcat) Internet e quindi aggiungere le proprietà del cloud, le personalizzazioni e i dashboard necessari per l'applicazione IoT Central.
- Creare un modello di funzionalità del dispositivo con Visual Studio Code. Implementare il codice del dispositivo dal modello. Importare manualmente il modello di funzionalità del dispositivo nell'applicazione IoT Central e quindi aggiungere le proprietà del cloud, le personalizzazioni e i dashboard necessari per l'applicazione IoT Central.
- Creare un modello di funzionalità del dispositivo con Visual Studio Code. Implementare il codice del dispositivo dal modello e connettere il dispositivo reale all'applicazione IoT Central usando una connessione del dispositivo. IoT Central trova e importa il modello di funzionalità del dispositivo dal repository pubblico. È quindi possibile aggiungere le proprietà del cloud, le personalizzazioni e i dashboard necessari all'applicazione IoT Central per il modello di dispositivo.

## <a name="create-a-device-template-from-the-device-catalog"></a>Creare un modello di dispositivo dal Catalogo dispositivi

In qualità di generatore, è possibile iniziare rapidamente a creare la propria soluzione usando un dispositivo Plug and Play certificato elencato nel [Catalogo dei dispositivi Azure](https://catalog.azureiotsolutions.com/alldevices). IoT Central si integra con il catalogo dispositivi per consentire l'importazione di un modello di funzionalità del dispositivo da uno qualsiasi di questi dispositivi Plug and Play certificati. Per creare un modello di dispositivo da uno di questi dispositivi in IoT Central:

1. Passare alla pagina **modelli di dispositivo** nell'applicazione IoT Central.
1. Selezionare **+ nuovo**, quindi selezionare uno dei dispositivi Plug and Play certificati dal catalogo elencati di seguito. IoT Central crea un modello di dispositivo basato su questo modello di funzionalità del dispositivo.
1. Aggiungere le proprietà, le personalizzazioni o le visualizzazioni del cloud al modello di dispositivo.
1. Selezionare **pubblica** per pubblicare il modello di dispositivo per renderlo disponibile agli operatori per la visualizzazione e la connessione dei dispositivi.

## <a name="create-a-device-template-from-scratch"></a>Creare un modello di dispositivo da zero

Un modello di dispositivo contiene:

- _Modello di funzionalità del dispositivo_ che specifica i dati di telemetria, le proprietà e i comandi implementati dal dispositivo. Queste funzionalità sono organizzate in una o più interfacce.
- _Proprietà del cloud_ che definiscono le informazioni che l'applicazione IoT Central archivia sui dispositivi. Una proprietà cloud, ad esempio, potrebbe registrare i dati che sono stati serviti da un dispositivo. Queste informazioni non vengono mai condivise con il dispositivo.
- Le personalizzazioni consentono al generatore di eseguire l'override di alcune definizioni nel modello di funzionalità del dispositivo. Il generatore potrebbe, ad esempio, eseguire l'override del nome di una proprietà del dispositivo. I nomi delle proprietà vengono visualizzati in IoT Central i dashboard e i form.
- _Dashboard e moduli_ consentono al generatore di creare un'interfaccia utente che consente agli operatori di monitorare e gestire i dispositivi connessi all'applicazione.

Per creare un modello di dispositivo in IoT Central:

1. Passare alla pagina **modelli di dispositivo** nell'applicazione IoT Central.
1. Selezionare **+ nuovo**, quindi selezionare **personalizzato**.
1. Immettere un nome per il modello, ad esempio **sensore ambientale**.
1. Premere **Invio**. IoT Central crea un modello di dispositivo vuoto.

## <a name="manage-a-device-template"></a>Gestire un modello di dispositivo

È possibile rinominare o eliminare un modello dalla home page del modello.

Dopo aver aggiunto un modello di funzionalità del dispositivo al modello, è possibile pubblicarlo. Non è possibile connettere un dispositivo in base a questo modello perché gli operatori possano visualizzare nella pagina **dispositivi** fino a quando non è stato pubblicato il modello.

## <a name="create-a-capability-model"></a>Creare un modello di funzionalità

Per creare un modello di funzionalità del dispositivo, è possibile:

- Usare IoT Central per creare un modello personalizzato da zero.
- Importare un modello da un file JSON. Un generatore di dispositivi potrebbe aver usato Visual Studio Code per creare un modello di funzionalità del dispositivo per l'applicazione.
- Selezionare uno dei dispositivi dal Catalogo dispositivi. Questa opzione importa il modello di funzionalità del dispositivo pubblicato dal produttore per questo dispositivo. Un modello di funzionalità del dispositivo importato come questo viene pubblicato automaticamente.

## <a name="manage-a-capability-model"></a>Gestire un modello di funzionalità

Dopo aver creato un modello di funzionalità del dispositivo, è possibile:

- Aggiungere interfacce al modello. Un modello deve avere almeno un'interfaccia.
- Modificare i metadati del modello, ad esempio ID, spazio dei nomi e nome.
- Eliminare il modello.

## <a name="create-an-interface"></a>Creare un'interfaccia

Una funzionalità del dispositivo deve avere almeno un'interfaccia. Un'interfaccia è una raccolta riutilizzabile di funzionalità.

Per creare un'interfaccia:

1. Passare al modello di funzionalità del dispositivo e scegliere **+ Aggiungi interfaccia**.

1. Nella pagina **selezionare un'interfaccia** è possibile:

    - Creare un'interfaccia personalizzata da zero.
    - Importa un'interfaccia esistente da un file. Un generatore di dispositivi potrebbe aver usato Visual Studio Code per creare un'interfaccia per il dispositivo.
    - Scegliere una delle interfacce standard, ad esempio l'interfaccia **informazioni sul dispositivo** . Le interfacce standard specificano le funzionalità comuni a molti dispositivi. Queste interfacce standard vengono pubblicate da Microsoft Azure e non possono essere modificate o modificate.

1. Dopo aver creato un'interfaccia, scegliere **modifica identità** per modificare il nome visualizzato dell'interfaccia.

1. Se si sceglie di creare un'interfaccia personalizzata da zero, è possibile aggiungere le funzionalità del dispositivo. Le funzionalità del dispositivo sono la telemetria, le proprietà e i comandi.

### <a name="telemetry"></a>Telemetria

La telemetria è un flusso di valori inviati dal dispositivo, in genere da un sensore. Un sensore, ad esempio, potrebbe segnalare la temperatura di ambiente.

La tabella seguente illustra le impostazioni di configurazione per una funzionalità di telemetria:

| Campo | Descrizione |
| ----- | ----------- |
| Nome visualizzato | Nome visualizzato per il valore di telemetria usato nei dashboard e nei moduli. |
| Name | Nome del campo nel messaggio di telemetria. IoT Central genera un valore per questo campo dal nome visualizzato, ma è possibile scegliere il proprio valore, se necessario. |
| Tipo di funzionalità | Telemetria. |
| Tipo semantico | Tipo semantico dei dati di telemetria, ad esempio temperatura, stato o evento. La scelta del tipo semantico determina quali dei campi seguenti sono disponibili. |
| Schema | Tipo di dati di telemetria, ad esempio Double, String o Vector. Le scelte disponibili sono determinate dal tipo semantico. Lo schema non è disponibile per i tipi di semantica di stato e di evento. |
| severity | Disponibile solo per il tipo di semantica dell'evento. **Errore**, **informazioni**o **avviso**. |
| Valori di stato | Disponibile solo per il tipo semantico di stato. Definire i valori di stato possibili, ognuno dei quali ha un nome visualizzato, un nome, un tipo di enumerazione e un valore. |
| Unità | Unità per il valore di telemetria,ad esempio **%** mph, o  **&deg;C**. |
| Unità di visualizzazione | Unità di visualizzazione da usare nei dashboard e nei moduli. |
| Commento | Eventuali commenti sulla funzionalità di telemetria. |
| Descrizione | Descrizione della funzionalità di telemetria. |

### <a name="properties"></a>Properties

Le proprietà rappresentano valori temporizzati. Ad esempio, un dispositivo potrebbe usare una proprietà per segnalare la temperatura di destinazione che sta tentando di raggiungere. È possibile impostare proprietà scrivibili da IoT Central.

La tabella seguente illustra le impostazioni di configurazione per una funzionalità di proprietà:

| Campo | Descrizione |
| ----- | ----------- |
| Nome visualizzato | Nome visualizzato per il valore della proprietà usato nei dashboard e nei form. |
| Name | Nome della proprietà. IoT Central genera un valore per questo campo dal nome visualizzato, ma è possibile scegliere il proprio valore, se necessario. |
| Tipo di funzionalità | Proprietà. |
| Tipo semantico | Tipo semantico della proprietà, ad esempio temperatura, stato o evento. La scelta del tipo semantico determina quali dei campi seguenti sono disponibili. |
| Schema | Tipo di dati della proprietà, ad esempio Double, String o Vector. Le scelte disponibili sono determinate dal tipo semantico. Lo schema non è disponibile per i tipi di semantica di stato e di evento. |
| Scrivibile | Se la proprietà non è scrivibile, il dispositivo può segnalare i valori delle proprietà IoT Central. Se la proprietà è scrivibile, il dispositivo può segnalare i valori delle proprietà a IoT Central e IoT Central può inviare gli aggiornamenti delle proprietà al dispositivo.
| severity | Disponibile solo per il tipo di semantica dell'evento. **Errore**, **informazioni**o **avviso**. |
| Valori di stato | Disponibile solo per il tipo semantico di stato. Definire i valori di stato possibili, ognuno dei quali ha un nome visualizzato, un nome, un tipo di enumerazione e un valore. |
| Unità | Unità per il valore della proprietà, adesempio mph **%** , o  **&deg;C**. |
| Unità di visualizzazione | Unità di visualizzazione da usare nei dashboard e nei moduli. |
| Commento | Eventuali commenti sulla funzionalità della proprietà. |
| Descrizione | Descrizione della funzionalità della proprietà. |

### <a name="commands"></a>Comandi:

È possibile chiamare i comandi del dispositivo da IoT Central. I comandi passano facoltativamente i parametri al dispositivo e ricevono una risposta dal dispositivo. Ad esempio, è possibile chiamare un comando per riavviare un dispositivo in 10 secondi.

La tabella seguente illustra le impostazioni di configurazione per una funzionalità del comando:

| Campo | Descrizione |
| ----- | ----------- |
| Nome visualizzato | Nome visualizzato per il comando utilizzato nei dashboard e nei form. |
| Name | Nome del comando. IoT Central genera un valore per questo campo dal nome visualizzato, ma è possibile scegliere il proprio valore, se necessario. |
| Tipo di funzionalità | Comando |
| Comando | SynchronousExecutionType. |
| Commento | Eventuali commenti sulla funzionalità del comando. |
| Descrizione | Descrizione della funzionalità del comando. |
| Richiesta | Se abilitata, una definizione del parametro della richiesta, tra cui: Name, nome visualizzato, schema, unità e unità di visualizzazione. |
| Risposta | Se abilitata, una definizione della risposta del comando tra cui: nome, nome visualizzato, schema, unità e unità di visualizzazione. |

## <a name="manage-an-interface"></a>Gestire un'interfaccia

Se l'interfaccia non è stata pubblicata, è possibile modificare le funzionalità definite dall'interfaccia. Una volta pubblicata l'interfaccia, sarà necessario creare una nuova versione del modello di dispositivo e la versione dell'interfaccia per apportare le modifiche. Le modifiche che non richiedono il controllo delle versioni, ad esempio i nomi visualizzati o le unità, possono essere apportate nella sezione **Personalizza** .

È anche possibile esportare l'interfaccia come file JSON se si vuole riutilizzarla in un altro modello di funzionalità.

## <a name="add-cloud-properties"></a>Aggiungere le proprietà del cloud

Usare le proprietà del cloud per archiviare le informazioni sui dispositivi in IoT Central. Le proprietà del cloud non vengono mai inviate a un dispositivo. Ad esempio, è possibile usare le proprietà del cloud per archiviare il nome del cliente che ha installato il dispositivo o la data dell'ultimo servizio del dispositivo.

La tabella seguente illustra le impostazioni di configurazione per una proprietà cloud:

| Campo | Descrizione |
| ----- | ----------- |
| Nome visualizzato | Nome visualizzato per il valore della proprietà cloud utilizzato nei dashboard e nei form. |
| Name | Nome della proprietà cloud. IoT Central genera un valore per questo campo dal nome visualizzato, ma è possibile scegliere il proprio valore, se necessario. |
| Tipo semantico | Tipo semantico della proprietà, ad esempio temperatura, stato o evento. La scelta del tipo semantico determina quali dei campi seguenti sono disponibili. |
| Schema | Tipo di dati della proprietà cloud, ad esempio Double, String o Vector. Le scelte disponibili sono determinate dal tipo semantico. |

## <a name="add-customizations"></a>Aggiungi personalizzazioni

Utilizzare le personalizzazioni quando è necessario modificare un'interfaccia importata o aggiungere funzionalità specifiche di IoT Central a una funzionalità. È possibile personalizzare solo i campi che non interrompono la compatibilità dell'interfaccia. Ad esempio, è possibile:

- Personalizzare il nome visualizzato e le unità di una funzionalità.
- Aggiungere un colore predefinito da utilizzare quando il valore viene visualizzato in un grafico.
- Specificare i valori iniziale, minimo e massimo per una proprietà.

Non è possibile personalizzare il tipo di funzionalità o il nome della funzionalità. Se sono presenti modifiche che non è possibile apportare nella sezione **Personalizza** , è necessario eseguire la versione del modello e dell'interfaccia del dispositivo per modificare la funzionalità.

### <a name="generate-default-views"></a>Genera visualizzazioni predefinite

La generazione di visualizzazioni predefinite è un modo rapido per iniziare a visualizzare le informazioni importanti sul dispositivo. Per il modello di dispositivo sono generate fino a tre visualizzazioni predefinite:

1. I **comandi** forniranno una visualizzazione con i comandi del dispositivo e consentiranno all'operatore di inviarli al dispositivo.
1. **Panoramica** fornirà una visualizzazione con i dati di telemetria dei dispositivi, visualizzando grafici e metriche.
1. Informazioni su fornirà una visualizzazione con le informazioni sul dispositivo, visualizzando le proprietà **del** dispositivo.

Dopo aver selezionato **Genera visualizzazioni predefinite** , si noterà che sono state aggiunte automaticamente nella sezione **viste** del modello di dispositivo.

## <a name="add-dashboards"></a>Aggiungere Dashboard

Aggiungere i dashboard a un modello di dispositivo per consentire agli operatori di visualizzare un dispositivo usando grafici e metriche. È possibile avere più dashboard per un modello di dispositivo.

Per aggiungere un dashboard a un modello di dispositivo:

1. Passare al modello di dispositivo e selezionare **visualizzazioni**.
1. Quindi scegliere **la visualizzazione del dispositivo**.
1. Immettere un nome per il dashboard in **nome dashboard**.
1. Aggiungere riquadri al dashboard dall'elenco di riquadri statici, proprietà, proprietà cloud, telemetria e comandi. Trascinare e rilasciare i riquadri che si desidera aggiungere al dashboard.
1. Per tracciare più valori di telemetria in un singolo riquadro del grafico, selezionare i valori di telemetria e quindi fare clic su **combina**.
1. Configurare ogni riquadro aggiunto per personalizzare la modalità di visualizzazione dei dati selezionando l'icona a forma di ingranaggio oppure selezionando il pulsante **modifica configurazione** nel riquadro del grafico.
1. Disporre e ridimensionare i riquadri nel dashboard.
1. Salvare le modifiche.

### <a name="configure-preview-device-to-view-dashboard"></a>Configurare il dispositivo di anteprima per visualizzare il dashboard

Per visualizzare e testare il dashboard, è possibile selezionare **Configura dispositivo di anteprima**, che consente di visualizzare il dashboard mentre l'operatore rileva dopo la pubblicazione. Questa opzione consente di verificare che le visualizzazioni mostrino i dati corretti. È possibile scegliere tra nessun dispositivo di anteprima, il dispositivo di test reale configurato per il modello di dispositivo o un dispositivo esistente nell'applicazione usando l'ID del dispositivo.

## <a name="add-forms"></a>Aggiungi moduli

Aggiungere moduli a un modello di dispositivo per consentire agli operatori di gestire un dispositivo visualizzando e impostando le proprietà. Gli operatori possono solo modificare le proprietà del cloud e le proprietà dei dispositivi scrivibili. È possibile avere più moduli per un modello di dispositivo.

Per aggiungere un modulo a un modello di dispositivo:

1. Passare al modello di dispositivo e selezionare **visualizzazioni**.
1. Scegliere quindi **modifica dati del dispositivo e del cloud**.
1. Immettere un nome per il form in **nome modulo**.
1. Consente di selezionare il numero di colonne da utilizzare per il layout del modulo.
1. Aggiungere proprietà a una sezione esistente nel form oppure selezionare proprietà e scegliere **Aggiungi sezione**. Usare le sezioni per raggruppare le proprietà nel form. È possibile aggiungere un titolo a una sezione.
1. Configurare ogni proprietà nel form per personalizzarne il comportamento.
1. Disporre le proprietà nel form.
1. Salvare le modifiche.

## <a name="publish-a-device-template"></a>Pubblicare un modello di dispositivo

Prima di poter connettere un dispositivo che implementa il modello di funzionalità del dispositivo, è necessario pubblicare il modello di dispositivo.

Dopo la pubblicazione di un modello di dispositivo, è possibile apportare solo modifiche limitate al modello di funzionalità del dispositivo. Per modificare un'interfaccia, è necessario [creare e pubblicare una nuova versione](./howto-version-device-template-pnp.md).

Per pubblicare un modello di dispositivo, passare al modello di dispositivo e selezionare **pubblica**.

Dopo la pubblicazione di un modello di dispositivo, un operatore può accedere alla pagina **dispositivi** e aggiungere i dispositivi reali o simulati che usano il modello di dispositivo. È possibile continuare a modificare e salvare il modello di dispositivo quando si apportano modifiche. Tuttavia, quando si desidera effettuare il push di queste modifiche nell'operatore per visualizzare nella pagina **dispositivi** , è necessario selezionare **pubblica** ogni volta.

## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce la procedura per configurare un modello di dispositivo nell'applicazione Azure IoT Central, è possibile:

> [!div class="nextstepaction"]
> [Creare una nuova versione](howto-version-device-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
> [del modello di dispositivo connettere un dispositivo MXChip DevKit all'applicazione Azure IOT Central](howto-connect-devkit-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
