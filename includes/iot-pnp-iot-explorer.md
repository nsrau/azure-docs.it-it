---
author: ericmitt
ms.author: ericmitt
ms.service: iot-pnp
ms.topic: include
ms.date: 07/13/2020
ms.openlocfilehash: 3ab9161c006a88a254b247a921512698ed46fa95
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "87351822"
---
1. Aprire Azure IoT Explorer.

1. Nella pagina **Hub IoT**, se non è già stata aggiunta una connessione all'hub IoT, selezionare **+ Aggiungi connessione**. Immettere la stringa di connessione per l'hub IoT creato in precedenza e selezionare **Salva**.

1. Nella pagina **Impostazioni Plug and Play IoT** selezionare **+ Aggiungi > Cartella locale** e quindi selezionare la cartella locale *models* in cui sono stati salvati i file di modello.

1. Nella pagina **Hub IoT** fare clic sul nome dell'hub che si vuole usare. Viene visualizzato un elenco dei dispositivi registrati nell'hub IoT.

1. Fare clic sull'**ID dispositivo** del dispositivo creato in precedenza.

1. Il menu a sinistra mostra i diversi tipi di informazioni disponibili per il dispositivo.

1. Selezionare **Componenti Plug and Play IoT** per visualizzare le informazioni sul modello del dispositivo.

1. È possibile visualizzare i diversi componenti del dispositivo. Il componente predefinito e gli eventuali altri. Selezionare un componente da usare.

1. Selezionare la pagina **Telemetria** e quindi selezionare **Avvia** per visualizzare i dati di telemetria inviati dal dispositivo per questo componente.

1. Selezionare la pagina **Proprietà (solo lettura)** per visualizzare le proprietà di sola lettura segnalate per questo componente.

1. Selezionare la pagina **Properties (writable)** (Proprietà - accessibili in scrittura) per visualizzare le proprietà accessibili in scrittura che è possibile aggiornare per questo componente.

1. Selezionare una proprietà in base al **nome**, immettere un nuovo valore e selezionare **Update desired value** (Aggiorna valore desiderato).

1. Per visualizzare il nuovo valore, selezionare il pulsante **Aggiorna**.

1. Selezionare la pagina **Comandi** per visualizzare tutti i comandi per questo componente.

1. Selezionare il comando di cui si vuole testare l'impostazione del parametro, se presente. Selezionare **Invia comando** per chiamare il comando nel dispositivo. Nella finestra del prompt dei comandi in cui è in esecuzione il codice di esempio si vedrà che il dispositivo risponde al comando.
