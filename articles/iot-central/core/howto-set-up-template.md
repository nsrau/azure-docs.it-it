---
title: Definire un nuovo tipo di dispositivo IoT in Azure IoT Central | Microsoft Docs
description: Questo articolo illustra come creare un nuovo modello di dispositivo Azure Internet nell'applicazione IoT Central di Azure come generatore di soluzioni. Il creatore definisce i dati di telemetria, lo stato, le proprietà e i comandi per il tipo.
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom:
- contperfq1
- device-developer
ms.openlocfilehash: f5b3e461408242553822024bc59c56a3feb29c44
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94991061"
---
# <a name="define-a-new-iot-device-type-in-your-azure-iot-central-application"></a>Definire un nuovo tipo di dispositivo IoT nell'applicazione Azure IoT Central

*Le informazioni di questo articolo sono destinate a generatori di soluzioni e sviluppatori di dispositivi.*

Un modello di dispositivo è un progetto che definisce le caratteristiche e i comportamenti di un tipo di dispositivo che si connette a un' [applicazione IoT Central di Azure](concepts-app-templates.md).

Ad esempio, uno sviluppatore può creare un modello di dispositivo per una ventola connessa con le caratteristiche seguenti:

- Invia i dati di telemetria sulla temperatura
- Invia la proprietà sulla posizione
- Invia gli eventi di errore del motore della ventola
- Invia lo stato operativo della ventola
- Fornisce una proprietà della velocità della ventola scrivibile
- Fornisce un comando per riavviare il dispositivo
- Offre una visualizzazione complessiva del dispositivo tramite un dashboard

Da questo modello di dispositivo, un operatore può creare e connettere dispositivi a ventola reali. Tutte queste ventole dispongono di misurazioni, proprietà e comandi usati dagli operatori per monitorarle e gestirle. Gli operatori usano i dashboard e i moduli del [dispositivo](#add-dashboards) per interagire con i dispositivi della ventola. Uno sviluppatore di dispositivi usa il modello per comprendere il modo in cui il dispositivo interagisce con l'applicazione. Per altre informazioni, vedere payload di dati di [telemetria, proprietà e comandi](concepts-telemetry-properties-commands.md).

> [!NOTE]
> Solo i generatori e gli amministratori possono creare, modificare ed eliminare i modelli di dispositivo. Qualsiasi utente può creare dispositivi nella pagina **Dispositivi** dai modelli di dispositivi esistenti.

In un'applicazione IoT Central, un modello di dispositivo usa un modello di dispositivo per descrivere le funzionalità di un dispositivo. I generatori hanno a disposizione numerose opzioni per creare modelli di dispositivo:

- Progettare il modello di dispositivo in IoT Central, quindi [implementare il modello di dispositivo nel codice del dispositivo](concepts-telemetry-properties-commands.md).
- Importare un modello di dispositivo dal [Catalogo dei dispositivi Azure Certified per](https://aka.ms/iotdevcat)l'it. Personalizzare il modello di dispositivo in modo che siano necessari in IoT Central.
- Creare un modello di dispositivo con il [linguaggio DTDL (Digital Gemini Definition Language)-versione 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). Visual Studio Code ha un'estensione che supporta la creazione di modelli di DTDL. Per altre informazioni, vedere [Installare e usare gli strumenti di creazione di DTDL](../../iot-pnp/howto-use-dtdl-authoring-tools.md). Pubblicare quindi il modello nel repository del modello pubblico. Per altre informazioni, vedere [repository del modello di dispositivo](../../iot-pnp/concepts-model-repository.md). Implementare il codice del dispositivo dal modello e connettere il dispositivo reale all'applicazione IoT Central. IoT Central trova e importa il modello di dispositivo dal repository pubblico e genera un modello di dispositivo. È quindi possibile aggiungere al modello di dispositivo le proprietà cloud, le personalizzazioni e i dashboard necessari per l'applicazione IoT Central.
- Creare un modello di dispositivo usando DTDL. Implementare il codice del dispositivo dal modello. Importare manualmente il modello di dispositivo nell'applicazione IoT Central e quindi aggiungere le proprietà del cloud, le personalizzazioni e i dashboard necessari per l'applicazione IoT Central.

È anche possibile aggiungere modelli di dispositivo a un'applicazione IoT Central usando l' [API REST](/learn/modules/manage-iot-central-apps-with-rest-api/) o l' [interfaccia](howto-manage-iot-central-from-cli.md)della riga di comando.

Alcuni [modelli di applicazione](concepts-app-templates.md) includono già modelli di dispositivo utili nello scenario supportato dal modello di applicazione. Vedere ad esempio l' [architettura di analisi in archivio](../retail/store-analytics-architecture.md).

## <a name="create-a-device-template-from-the-device-catalog"></a>Creare un modello di dispositivo dal catalogo dispositivi

In qualità di generatore, è possibile iniziare rapidamente a creare la soluzione usando un dispositivo certificato. Vedere l'elenco nel [catalogo dei dispositivi Azure IoT](https://catalog.azureiotsolutions.com/alldevices). IoT Central si integra con il catalogo dispositivi, in modo da poter importare un modello di dispositivo da uno qualsiasi dei dispositivi certificati. Per creare un modello di dispositivo da uno di questi dispositivi in IoT Central:

1. Passare alla pagina **modelli di dispositivo** nell'applicazione IoT Central.
1. Selezionare **+ nuovo**, quindi selezionare uno dei dispositivi certificati dal catalogo. IoT Central crea un modello di dispositivo basato su questo modello di dispositivo.
1. Aggiungere le proprietà cloud, le personalizzazioni o le visualizzazioni al modello di dispositivo.
1. Selezionare **Pubblica** per rendere disponibile il modello agli operatori per la visualizzazione e la connessione dei dispositivi.

## <a name="create-a-device-template-from-scratch"></a>Creare un modello di dispositivo da zero

Un modello di dispositivo contiene:

- _Modello di dispositivo_ che specifica i dati di telemetria, le proprietà e i comandi implementati dal dispositivo. Queste funzionalità sono organizzate in uno o più componenti.
- _Proprietà cloud_ che definiscono le informazioni che l'applicazione IoT Central archivia sui dispositivi. Una proprietà cloud, ad esempio, potrebbe registrare i dati relativi all'ultimo intervento effettuato sul dispositivo. Queste informazioni non vengono mai condivise con il dispositivo.
- Le _personalizzazioni_ consentono al generatore di eseguire l'override di alcune definizioni nel modello di dispositivo. Lo sviluppatore può, ad esempio, eseguire l'override del nome di una proprietà del dispositivo. I nomi delle proprietà vengono visualizzati nei dashboard e nei moduli di IoT Central.
- _Dashboard e moduli_ consentono allo sviluppatore di creare un'interfaccia utente che gli operatori possono usare per monitorare e gestire i dispositivi connessi all'applicazione.

Per creare un modello di dispositivo in IoT Central:

1. Passare alla pagina **Modelli di dispositivo** nell'applicazione IoT Central.
1. Selezionare **+ nuovo**  >  **dispositivo**. Selezionare quindi **Next: Personalizza**.
1. Immettere un nome per il modello, ad esempio **termostato**. Quindi selezionare **Avanti: verifica** e quindi selezionare **Crea**.
1. IoT Central crea un modello di dispositivo vuoto e consente di scegliere di creare un modello personalizzato da zero o importare un modello DTDL.

## <a name="manage-a-device-template"></a>Gestire un modello di dispositivo

È possibile rinominare o eliminare un modello dalla home page del modello.

Dopo aver aggiunto un modello di dispositivo al modello, è possibile pubblicarlo. Fino a quando il modello non viene pubblicato, non è possibile connettere un dispositivo in base a questo modello per consentire agli operatori di visualizzarlo nella pagina **Dispositivi**.

## <a name="create-a-capability-model"></a>Creare un modello di funzionalità

Per creare un modello di dispositivo, è possibile:

- Usare IoT Central per creare un modello personalizzato da zero.
- Importare un modello DTDL da un file JSON. Un generatore di dispositivi potrebbe aver usato Visual Studio Code per creare un modello di dispositivo per l'applicazione.
- Selezionare uno dei dispositivi dal catalogo dispositivi. Questa opzione importa il modello di dispositivo pubblicato dal produttore per questo dispositivo. Un modello di dispositivo importato come questo viene pubblicato automaticamente.

## <a name="manage-a-capability-model"></a>Gestire un modello di funzionalità

Dopo aver creato un modello di dispositivo, è possibile:

- Aggiungere componenti al modello. Un modello deve contenere almeno un componente.
- Modificare i metadati del modello, ad esempio ID, spazio dei nomi e nome.
- Eliminare il modello.

## <a name="create-a-component"></a>Creare un componente

Un modello di dispositivo deve avere almeno un componente predefinito. Un componente è una raccolta riutilizzabile di funzionalità.

Per creare un componente:

1. Passare al modello di dispositivo e scegliere **+ Aggiungi componente**.

1. Nella pagina **Aggiungi interfaccia componente** è possibile:

    - Creare un componente personalizzato da zero.
    - Importa un componente esistente da un file DTDL. Un generatore di dispositivi potrebbe aver usato Visual Studio Code per creare un'interfaccia componente per il dispositivo.

1. Dopo aver creato un componente, scegliere **modifica identità** per modificare il nome visualizzato del componente.

1. Se si sceglie di creare un componente personalizzato da zero, è possibile aggiungere le funzionalità del dispositivo. Le funzionalità di dispositivo sono costituite da telemetria, proprietà e comandi.

### <a name="telemetry"></a>Telemetria

La telemetria è un flusso di valori inviati dal dispositivo, in genere da un sensore. Un sensore, ad esempio, potrebbe segnalare la temperatura ambiente.

La tabella seguente illustra le impostazioni di configurazione per una funzionalità di telemetria:

| Campo | Descrizione |
| ----- | ----------- |
| Nome visualizzato | Nome visualizzato per il valore di telemetria usato nei dashboard e nei moduli. |
| Nome | Nome del campo nel messaggio di telemetria. IoT Central genera un valore per questo campo dal nome visualizzato, ma è possibile scegliere un valore personalizzato, se necessario. Questo campo deve essere alfanumerico. |
| Tipo di funzionalità | Telemetria. |
| Tipo semantico | Tipo semantico dei dati di telemetria, ad esempio temperatura, stato o evento. La scelta del tipo semantico determina quali tra i campi seguenti sono disponibili. |
| SCHEMA | Tipo di dati di telemetria, ad esempio double, stringa o vettore. Le scelte disponibili sono determinate dal tipo semantico. Lo schema non è disponibile per i tipi semantici stato ed evento. |
| Gravità | Disponibile solo per il tipo semantico evento. I livelli di gravità sono **Errore**, **Informazioni** o **Avviso**. |
| Valori di stato | Disponibile solo per il tipo semantico stato. Definisce i valori di stato possibili, ognuno dei quali ha un nome visualizzato, un nome, un tipo di enumerazione e un valore. |
| Unità | Unità per il valore di telemetria, ad esempio **mph**, **%** o **&deg; C**. |
| Unità di visualizzazione | Unità di visualizzazione da usare nei dashboard e nei moduli. |
| Commento | Eventuali commenti sulla funzionalità di telemetria. |
| Descrizione | Descrizione della funzionalità di telemetria. |

### <a name="properties"></a>Proprietà

Le proprietà rappresentano valori temporizzati. Ad esempio, un dispositivo potrebbe usare una proprietà per segnalare la temperatura di destinazione che sta tentando di raggiungere. È possibile impostare le proprietà scrivibili da IoT Central.

La tabella seguente illustra le impostazioni di configurazione per una funzionalità della proprietà:

| Campo | Descrizione |
| ----- | ----------- |
| Nome visualizzato | Nome visualizzato per il valore della proprietà usato nei dashboard e nei moduli. |
| Nome | Nome della proprietà. IoT Central genera un valore per questo campo dal nome visualizzato, ma è possibile scegliere un valore personalizzato, se necessario. Questo campo deve essere alfanumerico. |
| Tipo di funzionalità | Proprietà. |
| Tipo semantico | Tipo semantico della proprietà, ad esempio temperatura, stato o evento. La scelta del tipo semantico determina quali tra i campi seguenti sono disponibili. |
| SCHEMA | Tipo di dati della proprietà, ad esempio double, stringa o vettore. Le scelte disponibili sono determinate dal tipo semantico. Lo schema non è disponibile per i tipi semantici stato ed evento. |
| Scrivibile | Se la proprietà non è scrivibile, il dispositivo può segnalare i valori delle proprietà IoT Central. Se la proprietà è scrivibile, il dispositivo può segnalare i valori delle proprietà a IoT Central e IoT Central può inviare gli aggiornamenti delle proprietà al dispositivo.
| Gravità | Disponibile solo per il tipo semantico evento. I livelli di gravità sono **Errore**, **Informazioni** o **Avviso**. |
| Valori di stato | Disponibile solo per il tipo semantico stato. Definisce i valori di stato possibili, ognuno dei quali ha un nome visualizzato, un nome, un tipo di enumerazione e un valore. |
| Unità | Unità per il valore della proprietà, ad esempio **mph**, **%** o **&deg; C**. |
| Unità di visualizzazione | Unità di visualizzazione da usare nei dashboard e nei moduli. |
| Commento | Eventuali commenti sulla funzionalità della proprietà. |
| Descrizione | Descrizione della funzionalità della proprietà. |

### <a name="commands"></a>Comandi

È possibile chiamare i comandi del dispositivo da IoT Central. I comandi passano facoltativamente i parametri al dispositivo e ricevono una risposta dal dispositivo. Ad esempio, è possibile chiamare un comando per riavviare un dispositivo entro 10 secondi.

La tabella seguente illustra le impostazioni di configurazione per una funzionalità di comando:

| Campo | Descrizione |
| ----- | ----------- |
| Nome visualizzato | Nome visualizzato per il comando usato nei dashboard e nei moduli. |
| Nome | Nome del comando. IoT Central genera un valore per questo campo dal nome visualizzato, ma è possibile scegliere un valore personalizzato, se necessario. Questo campo deve essere alfanumerico. |
| Tipo di funzionalità | Comando. |
| Commento | Eventuali commenti sulla funzionalità del comando. |
| Descrizione | Descrizione della funzionalità del comando. |
| Richiesta | Se abilitata, una definizione del parametro della richiesta, tra cui: nome, nome visualizzato, schema, unità e unità di visualizzazione. |
| Risposta | Se abilitata, una definizione della risposta del comando, tra cui: nome, nome visualizzato, schema, unità e unità di visualizzazione. |

Per altre informazioni sul modo in cui i dispositivi implementano i comandi, vedere [comandi di telemetria, proprietà e payload del comando > comandi e comandi con esecuzione prolungata](concepts-telemetry-properties-commands.md#commands).

#### <a name="offline-commands"></a>Comandi offline

È possibile scegliere i comandi della coda se un dispositivo è attualmente offline abilitando l'opzione **queue if offline** per un comando nel modello di dispositivo.

Questa opzione Usa i messaggi da cloud a dispositivo dell'hub Internet per inviare notifiche ai dispositivi. Per altre informazioni, vedere l'articolo relativo all'hub Internet per l' [invio di messaggi da cloud a dispositivo](../../iot-hub/iot-hub-devguide-messages-c2d.md).

Messaggi da cloud a dispositivo:

- Sono notifiche unidirezionali al dispositivo dalla soluzione.
- Garantire il recapito di messaggi at-least-once. L'hub tutto rende permanente i messaggi da cloud a dispositivo nelle code per ogni dispositivo, garantendo la resilienza in caso di problemi di connettività e di dispositivo.
- Richiedere al dispositivo di implementare un gestore di messaggi per elaborare il messaggio da cloud a dispositivo.

> [!NOTE]
> Questa opzione è disponibile solo nell'interfaccia utente Web IoT Central. Questa impostazione non è inclusa se si esporta un modello o un componente dal modello di dispositivo.

## <a name="manage-a-component"></a>Gestire un componente

Se il componente non è stato pubblicato, è possibile modificare le funzionalità definite dal componente. Dopo aver pubblicato il componente, se si desidera apportare modifiche, è necessario creare una nuova versione del modello di dispositivo e [la versione del componente](howto-version-device-template.md). Le modifiche che non richiedono il controllo delle versioni, ad esempio i nomi visualizzati o le unità, possono essere apportate nella sezione **Personalizza**.

È anche possibile esportare il componente come file JSON se si vuole riutilizzarlo in un altro modello di funzionalità.

## <a name="add-cloud-properties"></a>Aggiungere proprietà cloud

Usare le proprietà cloud per archiviare le informazioni sui dispositivi in IoT Central. Le proprietà cloud non vengono mai inviate a un dispositivo. Ad esempio, è possibile usare le proprietà cloud per archiviare il nome del cliente che ha installato il dispositivo o la data dell'ultimo intervento sul dispositivo.

La tabella seguente illustra le impostazioni di configurazione per una proprietà cloud:

| Campo | Descrizione |
| ----- | ----------- |
| Nome visualizzato | Nome visualizzato per il valore della proprietà cloud usato nei dashboard e nei moduli. |
| Nome | Nome della proprietà cloud. IoT Central genera un valore per questo campo dal nome visualizzato, ma è possibile scegliere un valore personalizzato, se necessario. |
| Tipo semantico | Tipo semantico della proprietà, ad esempio temperatura, stato o evento. La scelta del tipo semantico determina quali tra i campi seguenti sono disponibili. |
| SCHEMA | Tipo di dati della proprietà cloud, ad esempio double, stringa o vettore. Le scelte disponibili sono determinate dal tipo semantico. |

## <a name="add-customizations"></a>Aggiungere personalizzazioni

Utilizzare le personalizzazioni quando è necessario modificare un componente importato o aggiungere funzionalità specifiche di IoT Central a una funzionalità. È possibile personalizzare solo i campi che non interrompono la compatibilità con i componenti. Ad esempio, è possibile:

- Personalizzare il nome visualizzato e le unità di una funzionalità.
- Aggiungere un colore predefinito da usare quando il valore viene visualizzato in un grafico.
- Specificare i valori iniziale, minimo e massimo per una proprietà.

Non è possibile personalizzare il tipo o il nome della funzionalità. Se sono presenti modifiche che non è possibile apportare nella sezione **Personalizza** , è necessario eseguire la versione del modello e del componente del dispositivo per modificare la funzionalità.

### <a name="generate-default-views"></a>Generare le visualizzazioni predefinite

La generazione di visualizzazioni predefinite è un modo rapido per visualizzare le informazioni importanti sul dispositivo. Per il modello di dispositivo possono essere generate fino a tre visualizzazioni predefinite:

- **Comandi**: una visualizzazione con i comandi del dispositivo e consente all'operatore di inviarli al dispositivo.
- **Panoramica**: una visualizzazione con i dati di telemetria dei dispositivi, che Visualizza grafici e metriche.
- Informazioni su: visualizzazione con informazioni sul dispositivo, visualizzazione delle proprietà **del** dispositivo.

Dopo aver selezionato **Genera visualizzazioni predefinite**, si noterà che sono state aggiunte automaticamente nella sezione **viste** del modello di dispositivo.

## <a name="add-dashboards"></a>Aggiungere dashboard

Aggiungere dashboard a un modello di dispositivo per consentire agli operatori di visualizzare un dispositivo usando grafici e metriche. Possono essere presenti più dashboard per un modello di dispositivo.

Per aggiungere un dashboard a un modello di dispositivo:

1. Passare al modello di dispositivo e selezionare **visualizzazioni**.
1. Scegliere quindi **Visualizzazione del dispositivo**.
1. Immettere un nome per il dashboard in **Nome del dashboard**.
1. Aggiungere al dashboard i riquadri selezionati dall'elenco di riquadri statici, proprietà, proprietà cloud, telemetria e comandi. Trascinare e rilasciare i riquadri da aggiungere al dashboard.
1. Per tracciare più valori di telemetria in un singolo riquadro del grafico, selezionare i valori di telemetria e quindi fare clic su **combina**.
1. Configurare ogni riquadro aggiunto per personalizzare la modalità di visualizzazione dei dati. Per accedere a questa opzione, selezionare l'icona dell'ingranaggio oppure selezionare **modifica configurazione** nel riquadro del grafico.
1. Disporre e ridimensionare i riquadri nel dashboard.
1. Salvare le modifiche.

### <a name="configure-preview-device-to-view-dashboard"></a>Configurare l'anteprima del dispositivo per visualizzare il dashboard

Per visualizzare e testare il dashboard, selezionare **Configura anteprima dispositivo**. Questa funzionalità consente di visualizzare il dashboard mentre l'operatore lo vede dopo la pubblicazione. Usare questa funzionalità per verificare che le visualizzazioni mostrino i dati corretti. È possibile scegliere tra le opzioni seguenti:

- Nessuna anteprima del dispositivo.
- Il dispositivo di test reale configurato per il modello di dispositivo.
- Un dispositivo esistente nell'applicazione in base all'ID dispositivo.

## <a name="add-forms"></a>Aggiungere moduli

Aggiungere moduli a un modello di dispositivo per consentire agli operatori di gestire un dispositivo visualizzando e impostando le proprietà. Gli operatori possono solo modificare le proprietà del cloud e le proprietà dei dispositivi scrivibili. Possono essere presenti più moduli per un modello di dispositivo.

Per aggiungere un modulo a un modello di dispositivo:

1. Passare al modello di dispositivo e selezionare **visualizzazioni**.
1. Scegliere **Modifica dei dati del dispositivo e del cloud**.
1. Immettere un nome per il modulo in **Nome del modulo**.
1. Selezionare il numero di colonne da usare per il layout del modulo.
1. Aggiungere le proprietà a una sezione esistente nel modulo oppure selezionare le proprietà e scegliere **Aggiungi sezione**. Usare le sezioni per raggruppare le proprietà nel modulo. È possibile aggiungere un titolo a una sezione.
1. Configurare ogni proprietà nel modulo per personalizzarne il comportamento.
1. Disporre le proprietà nel modulo.
1. Salvare le modifiche.

## <a name="publish-a-device-template"></a>Pubblicare un modello di dispositivo

Prima di poter connettere un dispositivo che implementa il modello di dispositivo, è necessario pubblicare il modello di dispositivo.

Dopo la pubblicazione di un modello di dispositivo, è possibile apportare solo modifiche limitate al modello di dispositivo. Per modificare un componente, è necessario [creare e pubblicare una nuova versione](./howto-version-device-template.md).

Per pubblicare un modello di dispositivo, passare al modello di dispositivo e selezionare **pubblica**.

Dopo la pubblicazione di un modello di dispositivo, un operatore può accedere alla pagina **Dispositivi** e aggiungere i dispositivi reali o simulati che usano il modello. È possibile continuare a modificare e salvare il modello di dispositivo man mano che si apportano le modifiche. Per eseguire il push di queste modifiche affinché possano essere visualizzate dall'operatore nella pagina **Dispositivi**, è necessario selezionare **Pubblica** ogni volta.

## <a name="next-steps"></a>Passaggi successivi

Per gli sviluppatori di dispositivi, un passaggio successivo suggerito consiste nel leggere il [controllo delle versioni dei modelli di dispositivo](./howto-version-device-template.md).
