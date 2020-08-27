---
ms.openlocfilehash: 67d90836c382728f989ab2cb4fde4d81bac9eb25
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690947"
---
Per questa esercitazione sono necessarie le risorse di Azure seguenti:

* Hub IoT
* Account di archiviazione
* Account Servizi multimediali di Azure
* Macchina virtuale Linux in Azure, con [runtime IoT Edge](../../../../../iot-edge/how-to-install-iot-edge-linux.md) installato

Per questo argomento di avvio rapido è consigliabile usare lo [script di configurazione delle risorse di Analisi video live](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) per distribuire le risorse necessarie nella sottoscrizione di Azure. A questo scopo, attenersi alla procedura seguente:

1. Aprire [Azure Cloud Shell](https://shell.azure.com).
1. Se si usa Cloud Shell per la prima volta, verrà chiesto di selezionare una sottoscrizione, oltre che di creare un account di archiviazione e una condivisione di File di Microsoft Azure. Selezionare **Crea risorsa di archiviazione** per creare un account di archiviazione per le informazioni della sessione Cloud Shell. Questo account di archiviazione è distinto da quello che verrà creato dallo script per l'uso con l'account di Servizi multimediali di Azure.
1. Nel menu a discesa sul lato sinistro della finestra di Cloud Shell selezionare **Bash** come ambiente.

    ![Selezione dell'ambiente](../../../media/quickstarts/env-selector.png)
1. Eseguire il comando seguente.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    Se lo script viene completato correttamente, verranno visualizzate tutte le risorse necessarie nella sottoscrizione.
1. Al termine dello script, selezionare le parentesi graffe per esporre la struttura della cartella. Vengono visualizzati alcuni file nella directory *~/clouddrive/lva-sample*. I file rilevanti per questo avvio rapido sono:

     * ***~/clouddrive/lva-sample/edge-deployment/.env*** - Questo file contiene le proprietà usate da Visual Studio Code per distribuire i moduli in un dispositivo perimetrale.
     * ***~/clouddrive/lva-sample/appsetting.json*** - Visual Studio Code usa questo file per l'esecuzione del codice di esempio.
     
    Questi file saranno necessari per configurare l'ambiente di sviluppo in Visual Studio Code nella sezione successiva. È consigliabile copiarli in un file locale per il momento.
    
    ![Impostazioni app](../../../media/quickstarts/clouddrive.png)
    