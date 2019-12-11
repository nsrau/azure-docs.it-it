---
title: Informazioni sulle versioni dei modelli di dispositivo per le app Azure IoT Central | Microsoft Docs
description: Eseguire iterazioni con i modelli di dispositivo creando nuove versioni, senza determinare conseguenze per i dispositivi collegati
author: sarahhubbard
ms.author: sahubbar
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 1f2ecb37ebe156b1eb092bda95f296c39c9e2baf
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974965"
---
# <a name="create-a-new-device-template-version-preview-features"></a>Creare una nuova versione del modello di dispositivo (funzionalità di anteprima)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Azure IoT Central consente lo sviluppo rapido di applicazioni IoT. È possibile eseguire rapidamente l'iterazione delle progettazioni dei modelli di dispositivo aggiungendo, modificando o eliminando funzionalità, visualizzazioni e personalizzazioni del dispositivo. Dopo aver pubblicato il modello di dispositivo, il modello di funzionalità del dispositivo Mostra come **pubblicato** con icone di blocco accanto al modello. Per apportare modifiche al modello di funzionalità del dispositivo, sarà necessario creare una nuova versione del modello di dispositivo. Nel frattempo le proprietà, le personalizzazioni e le visualizzazioni del cloud possono essere modificate in qualsiasi momento senza dover eseguire la versione del modello di dispositivo. Dopo aver salvato una qualsiasi di queste modifiche, è possibile pubblicare il modello di dispositivo per rendere disponibili le ultime modifiche per l'operatore da visualizzare in Device Explorer.

> [!NOTE]
> Per ulteriori informazioni su come creare un modello di dispositivo, vedere [configurare e gestire un modello di dispositivo](howto-set-up-template.md)

## <a name="add-customizations-to-the-device-template-without-versioning"></a>Aggiungere personalizzazioni al modello di dispositivo senza controllo delle versioni

È possibile modificare determinati elementi delle funzionalità del dispositivo senza dover eseguire la versione del modello e delle interfacce del dispositivo. Ad esempio, alcuni di questi campi includono il nome visualizzato, il tipo semantico, il valore minimo, il valore massimo, le posizioni decimali, il colore, l'unità, l'unità di visualizzazione, il commento e la descrizione. Per aggiungere una di queste personalizzazioni:

1. Passare alla pagina **modelli di dispositivo** .
1. Selezionare il modello di dispositivo che si desidera personalizzare.
1. Scegliere la scheda **Personalizza** .
1. Tutte le funzionalità definite nel modello di funzionalità del dispositivo verranno elencate qui. Tutti i campi che è possibile modificare possono essere salvati e usati nell'applicazione, senza dover eseguire la versione del modello di dispositivo. Se sono presenti campi di cui si vuole modificare la modalità di sola lettura, è necessario eseguire la versione del modello di dispositivo per modificarli. Selezionare un campo che si desidera modificare e immettere i nuovi valori.
1. Fare clic su **Salva** Ora questi valori eseguiranno l'override di qualsiasi elemento salvato inizialmente nel modello di dispositivo e verrà usato nell'applicazione.

## <a name="versioning-a-device-template"></a>Controllo delle versioni di un modello di dispositivo

La creazione di una nuova versione del modello di dispositivo creerà una versione bozza del modello in cui è possibile modificare il modello di funzionalità del dispositivo. Tutte le interfacce pubblicate rimarranno pubblicate fino a quando non saranno con versioni singole. Per modificare un'interfaccia pubblicata, è innanzitutto necessario creare una nuova versione del modello di dispositivo.

Il modello di dispositivo deve essere sottoposto a versione solo quando si tenta di modificare una parte del modello di funzionalità del dispositivo che non è possibile modificare nella sezione personalizzazioni del modello di dispositivo. 

Per la versione di un modello di dispositivo:

1. Passare alla pagina **modelli di dispositivo** .
1. Selezionare il modello di dispositivo che si sta provando a eseguire la versione.
1. Fare clic sul pulsante **Version (versione** ) nella parte superiore della pagina e assegnare al modello un nuovo nome. È stato suggerito un nuovo nome che può essere modificato.
1. Fare clic su **Create**(Crea).
1. A questo punto il modello di dispositivo è in modalità bozza. Si noterà che le interfacce sono ancora bloccate ed è necessario eseguire il controllo delle versioni singolarmente per la modifica. 

### <a name="versioning-an-interface"></a>Controllo delle versioni di un'interfaccia

Il controllo delle versioni di un'interfaccia consente di aggiungere, aggiornare e rimuovere le funzionalità all'interno dell'interfaccia già creata. 

Per la versione di un'interfaccia:

1. Passare alla pagina **modelli di dispositivo** .
1. Selezionare il modello di dispositivo in modalità bozza.
1. Selezionare l'interfaccia in modalità pubblicata che si desidera modificare.
1. Fare clic sul pulsante **Version (versione** ) nella parte superiore della pagina Interface (interfaccia). 
1. Fare clic su **Create**(Crea).
1. Ora l'interfaccia è in modalità bozza. Sarà possibile aggiungere o modificare le funzionalità dell'interfaccia senza suddividere le personalizzazioni e le visualizzazioni esistenti. 

> [!NOTE]
> Non è possibile modificare il controllo delle versioni o modificare le interfacce standard pubblicate da Azure. Queste interfacce standard vengono usate per la certificazione dei dispositivi.

> [!NOTE]
> Una volta pubblicata l'interfaccia, non è possibile eliminare le funzionalità anche in modalità bozza. Le funzionalità possono essere modificate o aggiunte all'interfaccia solo in modalità bozza.


## <a name="migrate-a-device-across-device-template-versions"></a>Eseguire la migrazione di un dispositivo fra diverse versioni del modello di dispositivo

È possibile creare più versioni del modello di dispositivo. Nel corso del tempo si avranno più dispositivi connessi che usano questi modelli di dispositivo. È possibile eseguire la migrazione dei dispositivi da una versione del modello di dispositivo a un'altra. I passaggi seguenti descrivono come eseguire la migrazione di un dispositivo:

1. Passare alla pagina **Device Explorer** .
1. Selezionare il dispositivo di cui eseguire la migrazione a un'altra versione.
1. Scegliere **Esegui migrazione**.
1. Selezionare il modello di dispositivo con il numero di versione in cui si vuole eseguire la migrazione del dispositivo e scegliere **Esegui migrazione**.

![Come eseguire la migrazione di un dispositivo](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce la procedura per usare le versioni dei modelli di dispositivo nell'applicazione Azure IoT Central, il prossimo passaggio suggerito è:

> [!div class="nextstepaction"]
> [Come creare regole dei dati di telemetria](tutorial-create-telemetry-rules.md)
