---
title: Informazioni sulle versioni dei modelli di dispositivo per le app Azure IoT Central | Microsoft Docs
description: Eseguire iterazioni con i modelli di dispositivo creando nuove versioni, senza determinare conseguenze per i dispositivi collegati
author: sarahhubbard
ms.author: sahubbar
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 37c7bc99881c8d1106c8464cfe18c9e63b8a1b02
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756732"
---
# <a name="create-a-new-device-template-version"></a>Creare una nuova versione di un modello di dispositivo

*Questo articolo si applica ai generatori di soluzioni e agli sviluppatori di dispositivi.*

Azure IoT Central consente lo sviluppo rapido di applicazioni IoT. È possibile scorrere rapidamente i progetti di modelli di dispositivo aggiungendo, modificando o eliminando funzionalità, visualizzazioni e personalizzazioni del dispositivo. Dopo aver pubblicato il modello di dispositivo, il modello di funzionalità del dispositivo viene visualizzato come Pubblicato con le icone **di** blocco accanto al modello. Per apportare modifiche al modello di funzionalità del dispositivo, è necessario creare una nuova versione del modello di dispositivo. Nel frattempo le proprietà cloud, le personalizzazioni e le visualizzazioni possono essere modificate in qualsiasi momento senza dover eseguire il versioning del modello di dispositivo. Dopo aver salvato una di queste modifiche, è possibile pubblicare il modello di dispositivo per rendere le modifiche più recenti disponibili per la visualizzazione da parte dell'operatore in Esplora dispositivi.

> [!NOTE]
> Per altre informazioni su come creare un modello di dispositivo, vedere [Configurare e gestire un modello](howto-set-up-template.md) di dispositivoTo learn more about how to create a device template see Set up and manage a device template

## <a name="add-customizations-to-the-device-template-without-versioning"></a>Aggiungere personalizzazioni al modello di dispositivo senza controllo delle versioniAdd customizations to the device template without versioning

Alcuni elementi delle funzionalità del dispositivo possono essere modificati senza la necessità di eseguire la versione del modello e delle interfacce del dispositivo. Ad esempio, alcuni di questi campi includono il nome visualizzato, il tipo semantico, il valore minimo, il valore massimo, le posizioni decimali, il colore, l'unità, l'unità di visualizzazione, il commento e la descrizione. Per aggiungere una di queste personalizzazioni:

1. Vai alla pagina **Modelli di dispositivo.**
1. Selezionare il modello di dispositivo che si desidera personalizzare.
1. Scegliere la scheda **Personalizza.**
1. Tutte le funzionalità definite nel modello di funzionalità del dispositivo verranno elencate qui. Tutti i campi che è possibile modificare qui possono essere salvati e utilizzati in tutta l'applicazione, senza la necessità di prendere in base al modello di dispositivo. Se ci sono campi che si desidera modificare che sono di sola lettura, è necessario prendere in versione il modello del dispositivo per modificarli. Selezionare un campo che si desidera modificare e immettere i nuovi valori.
1. Fare clic su **Salva**. Ora questi valori sostituiranno tutto ciò che è stato inizialmente salvato nel modello di dispositivo e verrà utilizzato in tutta l'applicazione.

## <a name="versioning-a-device-template"></a>Controllo delle versioni di un modello di dispositivoVersioning a device template

La creazione di una nuova versione del modello di dispositivo creerà una versione bozza del modello in cui è possibile modificare il modello di funzionalità del dispositivo. Tutte le interfacce pubblicate rimarranno pubblicate fino a quando non avranno lo controllo delle versioni individuali. Per modificare un'interfaccia pubblicata, è innanzitutto necessario creare una nuova versione del modello di dispositivo.

Il modello di dispositivo deve essere sottoposto a controllo delle versioni solo quando si tenta di modificare una parte del modello di funzionalità del dispositivo che non è possibile modificare nella sezione delle personalizzazioni del modello di dispositivo. 

Per eseguire la versione di un modello di dispositivo:

1. Vai alla pagina **Modelli di dispositivo.**
1. Selezionare il modello di dispositivo che si sta tentando di versione.
1. Fare clic sul pulsante **Versione** nella parte superiore della pagina e assegnare un nuovo nome al modello. Abbiamo suggerito un nuovo nome per voi che può essere modificato.
1. Fare clic su **Crea**.
1. Ora il modello di dispositivo è in modalità bozza. Vedrai che le interfacce sono ancora bloccate e devono essere sottoposte a controllo delle versioni individuali per essere modificate. 

### <a name="versioning-an-interface"></a>Controllo delle versioni di un'interfaccia

Il controllo delle versioni di un'interfaccia consente di aggiungere, aggiornare e rimuovere le funzionalità all'interno dell'interfaccia già creata. 

Per la versione di un'interfaccia:

1. Vai alla pagina **Modelli di dispositivo.**
1. Selezionare il modello di dispositivo in modalità bozza.
1. Selezionare l'interfaccia in modalità pubblicata che si desidera utilizzare e modificare.
1. Fare clic sul pulsante **Versione** nella parte superiore della pagina dell'interfaccia. 
1. Fare clic su **Crea**.
1. Ora la tua interfaccia è in modalità bozza. Sarà possibile aggiungere o modificare funzionalità all'interfaccia senza interrompere le personalizzazioni e le visualizzazioni esistenti. 

> [!NOTE]
> Le interfacce standard pubblicate da Azure IoT non possono essere sottoposte a controllo delle versioni o modificate. Queste interfacce standard vengono utilizzate per la certificazione del dispositivo.

> [!NOTE]
> Una volta che l'interfaccia è stata pubblicata, non è possibile eliminare nessuna delle funzionalità anche in modalità bozza. Le funzionalità possono essere modificate o aggiunte all'interfaccia solo in modalità bozza.


## <a name="migrate-a-device-across-device-template-versions"></a>Eseguire la migrazione di un dispositivo fra diverse versioni del modello di dispositivo

È possibile creare più versioni del modello di dispositivo. Nel corso del tempo si avranno più dispositivi connessi che usano questi modelli di dispositivo. È possibile eseguire la migrazione dei dispositivi da una versione del modello di dispositivo a un'altra. I passaggi seguenti descrivono come eseguire la migrazione di un dispositivo:

1. Passare alla pagina **Esplora dispositivi.**
1. Selezionare il dispositivo di cui eseguire la migrazione a un'altra versione.
1. Scegliere **Migra**.
1. Selezionare il modello di dispositivo con il numero di versione in cui si desidera eseguire la migrazione del dispositivo e scegliere **Migra**.

![Come eseguire la migrazione di un dispositivo](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>Passaggi successivi

Se sei uno sviluppatore di dispositivi, un passaggio successivo consigliato consiste nel leggere informazioni sui [dispositivi Azure IoT Edge e Azure IoT Central](./concepts-iot-edge.md).
