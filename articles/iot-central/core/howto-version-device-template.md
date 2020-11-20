---
title: Informazioni sulle versioni dei modelli di dispositivo per le app Azure IoT Central | Microsoft Docs
description: Eseguire iterazioni con i modelli di dispositivo creando nuove versioni, senza determinare conseguenze per i dispositivi collegati
author: dominicbetts
ms.author: dobett
ms.date: 11/06/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 93545c63013c95e3db498b079061da3d9b189efd
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94986447"
---
# <a name="create-a-new-device-template-version"></a>Creare una nuova versione di un modello di dispositivo

*Le informazioni di questo articolo sono destinate a generatori di soluzioni e sviluppatori di dispositivi.*

Un modello di dispositivo include uno schema che descrive il modo in cui un dispositivo interagisce con IoT Central. Queste interazioni includono i dati di telemetria, le proprietà e i comandi. Sia il dispositivo che l'applicazione IoT Central si basano su una conoscenza condivisa di questo schema per lo scambio di informazioni. È possibile apportare solo modifiche limitate allo schema senza suddividere il contratto. per questo motivo la maggior parte delle modifiche dello schema richiede una nuova versione del modello di dispositivo. Il controllo delle versioni del modello di dispositivo consente ai dispositivi meno recenti di continuare con la versione dello schema che comprende, mentre i dispositivi più recenti o aggiornati utilizzano una versione dello schema successiva.

Lo schema in un modello di dispositivo viene definito nel modello di dispositivo e nelle relative interfacce. I modelli di dispositivo includono altre informazioni, ad esempio le proprietà del cloud, le personalizzazioni di visualizzazione e le visualizzazioni. Se si apportano modifiche a queste parti del modello di dispositivo che non definiscono il modo in cui il dispositivo scambia i dati con IoT Central, non è necessario eseguire la versione del modello.

Prima che un operatore possa usarlo, è sempre necessario pubblicare un modello di dispositivo aggiornato. IoT Central impedisce la pubblicazione di modifiche di rilievo in un modello di dispositivo senza prima eseguire il controllo delle versioni del modello.

> [!NOTE]
> Per ulteriori informazioni su come creare un modello di dispositivo, vedere [configurare e gestire un modello di dispositivo](howto-set-up-template.md)

## <a name="versioning-rules"></a>Regole di controllo delle versioni

In questa sezione vengono riepilogate le regole di controllo delle versioni applicabili ai modelli di dispositivo. Sia i modelli di dispositivo che le interfacce hanno numeri di versione. Il frammento di codice seguente mostra il modello di dispositivo per un dispositivo termostato. Il modello di dispositivo ha una singola interfaccia. È possibile visualizzare il numero di versione alla fine del `@id` campo.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    // ...
  ]
}
```

Per visualizzare queste informazioni nell'interfaccia utente di IoT Central, selezionare **Visualizza identità** nell'editor modello di dispositivo:

:::image type="content" source="media/howto-version-device-template/view-identity.png" alt-text="Visualizzare l'identità di un'interfaccia per visualizzare il numero di versione":::

Nell'elenco seguente vengono illustrate le regole che determinano quando è necessario creare una nuova versione:

* Dopo la pubblicazione di un modello di dispositivo, non è possibile rimuovere le interfacce, neanche in una nuova versione del modello di dispositivo.
* Dopo la pubblicazione di un modello di dispositivo, è possibile aggiungere un'interfaccia se si crea una nuova versione del modello di dispositivo.
* Dopo la pubblicazione di un modello di dispositivo, è possibile sostituire un'interfaccia con una versione più recente se si crea una nuova versione del modello di dispositivo. Se, ad esempio, il modello di dispositivo Sensor V1 usa l'interfaccia termostati V1, è possibile creare un modello di dispositivo Sensor V2 che usa l'interfaccia termostati V2.
* Dopo la pubblicazione di un'interfaccia, non è possibile rimuovere il contenuto dell'interfaccia, neanche in una nuova versione del modello di dispositivo.
* Dopo la pubblicazione di un'interfaccia, è possibile aggiungere elementi al contenuto di un'interfaccia se si crea una nuova versione dell'interfaccia e del modello di dispositivo. Gli elementi che è possibile aggiungere all'interfaccia includono i dati di telemetria, le proprietà e i comandi.
* Dopo la pubblicazione di un'interfaccia, è possibile apportare modifiche non dello schema agli elementi esistenti nell'interfaccia se si crea una nuova versione dell'interfaccia e del modello di dispositivo. Le parti non dello schema di un elemento di interfaccia includono il nome visualizzato e il tipo semantico. Le parti dello schema di un elemento di interfaccia che non è possibile modificare sono il nome, il tipo di funzionalità e lo schema.

Le sezioni seguenti illustrano alcuni esempi di modifica dei modelli di dispositivo in IoT Central.

## <a name="customize-the-device-template-without-versioning"></a>Personalizzare il modello di dispositivo senza controllo delle versioni

È possibile modificare determinati elementi delle funzionalità del dispositivo senza dover eseguire la versione del modello e delle interfacce del dispositivo. Ad esempio, alcuni di questi campi includono il nome visualizzato, il tipo semantico, il valore minimo, il valore massimo, le posizioni decimali, il colore, l'unità, l'unità di visualizzazione, il commento e la descrizione. Per aggiungere una di queste personalizzazioni:

1. Passare alla pagina **modelli di dispositivo** .
1. Selezionare il modello di dispositivo che si desidera personalizzare.
1. Scegliere la scheda **Personalizza** .
1. Tutte le funzionalità definite nel modello di dispositivo sono elencate qui. È possibile modificare, salvare e usare tutti questi campi senza dover eseguire la versione del modello di dispositivo. Se sono presenti campi di cui si vuole modificare la modalità di sola lettura, è necessario eseguire la versione del modello di dispositivo per modificarli. Selezionare un campo che si desidera modificare e immettere i nuovi valori.
1. Selezionare **Salva**. Ora questi valori eseguono l'override di qualsiasi elemento salvato inizialmente nel modello di dispositivo e vengono usati nell'applicazione.

## <a name="version-a-device-template"></a>Versione di un modello di dispositivo

La creazione di una nuova versione del modello di dispositivo crea una versione bozza del modello in cui è possibile modificare il modello di dispositivo. Tutte le interfacce pubblicate rimangono pubblicate fino a quando non ne viene eseguita la versione singolarmente. Per modificare un'interfaccia pubblicata, creare prima di tutto una nuova versione del modello di dispositivo.

Eseguire la versione del modello di dispositivo solo quando si sta provando a modificare una parte del modello di dispositivo che non è possibile modificare nella sezione personalizzazioni.

Per eseguire la versione di un modello di dispositivo:

1. Passare alla pagina **modelli di dispositivo** .
1. Selezionare il modello di dispositivo che si sta provando a eseguire la versione.
1. Selezionare il pulsante **Version (versione** ) nella parte superiore della pagina e assegnare al modello un nuovo nome. IoT Central suggerisce un nuovo nome, che è possibile modificare.
1. Selezionare **Crea**.
1. A questo punto il modello di dispositivo è in modalità bozza. È possibile osservare che le interfacce sono ancora bloccate. Versione delle interfacce che si desidera modificare.

## <a name="version-an-interface"></a>Versione di un'interfaccia

Il controllo delle versioni di un'interfaccia consente di aggiungere, aggiornare e rimuovere le funzionalità all'interno dell'interfaccia già creata.

Per eseguire la versione di un'interfaccia:

1. Passare alla pagina **modelli di dispositivo** .
1. Selezionare il modello di dispositivo in modalità bozza.
1. Selezionare l'interfaccia in modalità pubblicata che si desidera modificare.
1. Selezionare il pulsante **Version (versione** ) nella parte superiore della pagina Interface (interfaccia).
1. Selezionare **Crea**.
1. Ora l'interfaccia è in modalità bozza. È possibile aggiungere o modificare le funzionalità dell'interfaccia senza suddividere le personalizzazioni e le visualizzazioni esistenti.

## <a name="migrate-a-device-across-versions"></a>Eseguire la migrazione di un dispositivo tra le versioni

È possibile creare più versioni del modello di dispositivo. Nel corso del tempo, si avranno più dispositivi connessi usando questi modelli di dispositivo. È possibile eseguire la migrazione dei dispositivi da una versione del modello di dispositivo a un'altra. I passaggi seguenti descrivono come eseguire la migrazione di un dispositivo:

1. Andare alla pagina **Dispositivi**.
1. Selezionare il dispositivo di cui eseguire la migrazione a un'altra versione.
1. Scegliere **migrazione**:  :::image type="content" source="media/howto-version-device-template/migrate-device.png" alt-text="scegliere l'opzione per avviare la migrazione di un dispositivo":::
1. Selezionare il modello di dispositivo con il numero di versione in cui si vuole eseguire la migrazione del dispositivo e selezionare **migrazione**.

## <a name="next-steps"></a>Passaggi successivi

Se si è un operatore o un generatore di soluzioni, un passaggio successivo suggerito consiste nell'apprendere [come gestire i dispositivi](./howto-manage-devices.md).

Per gli sviluppatori di dispositivi, un passaggio successivo suggerito consiste nel leggere informazioni sui [dispositivi Azure IOT Edge e IOT Central di Azure](./concepts-iot-edge.md).
