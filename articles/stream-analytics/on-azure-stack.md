---
title: Eseguire analisi di flusso di Azure in Azure Stack (anteprima)
description: Creare un processo Edge di analisi di flusso di Azure e distribuirlo nell'hub Azure Stack tramite il runtime di IoT Edge.
ms.service: stream-analytics
author: raan
ms.author: raan
ms.reviewer: mamccrea
ms.topic: how-to
ms.date: 08/21/2020
ms.custom: seodec18
ms.openlocfilehash: 1fe035d99f8a5962406d5aae3f093d71d432b310
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88860982"
---
# <a name="run-azure-stream-analytics-on-azure-stack-preview"></a>Eseguire analisi di flusso di Azure in Azure Stack (anteprima)

> [!IMPORTANT]
> Questa funzionalità è disponibile in anteprima e non è consigliabile usarla nell'ambiente di produzione.

È possibile eseguire analisi di flusso di Azure nell'hub Azure Stack come modulo di IoT Edge. Le configurazioni sono state aggiunte al modulo IoT Edge che consente di interagire con l'archiviazione BLOB, gli hub eventi e gli hub Internet in esecuzione in una sottoscrizione Hub Azure Stack consentendo l'utilizzo di URL personalizzati in ogni Azure Stack dell'hub.

Con analisi di flusso in Azure Stack, è possibile creare architetture realmente ibride per l'elaborazione di flussi nel cloud privato e autonomo, che può essere connesso o disconnesso con app native del cloud usando servizi di Azure coerenti in locale. 

Questo articolo illustra come eseguire lo streaming dei dati dall'hub o dall'hub eventi in un altro hub eventi o in un archivio BLOB in una sottoscrizione di hub Azure Stack ed elaborarli con l'analisi di flusso.

## <a name="set-up-environments"></a>Impostare gli ambienti

Analisi di flusso di Azure è un servizio ibrido nell'hub Azure Stack. Si tratta di un modulo IoT Edge configurato in Azure, che può essere eseguito nell'hub Azure Stack.  

Se non si ha familiarità con Azure Stack Hub o IoT Edge, seguire le istruzioni riportate di seguito per configurare gli ambienti.

### <a name="prepare-the-azure-stack-hub-environment"></a>Preparare l'ambiente dell'hub Azure Stack

Creare una sottoscrizione di Azure Stack Hub. Per ulteriori informazioni, vedere l' [esercitazione per la creazione di una sottoscrizione di Hub Azure stack.](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services/)

Per valutare Azure Stack Hub sul proprio server, è possibile usare il Azure Stack Development Kit (Gabriele).  Per altre informazioni su Gabriele, vedere Panoramica di [Gabriele](https://docs.microsoft.com/azure-stack/asdk/).

### <a name="install-the-iot-edge-runtime"></a>Installare il runtime IoT Edge.

Per eseguire analisi di flusso di Azure nell'hub Azure Stack, il dispositivo deve avere il runtime IoT Edge e deve avere la connettività di rete all'hub Azure Stack o essere una macchina virtuale in esecuzione nell'hub Azure Stack. Il runtime di IoT Edge consente l'integrazione dei processi Edge di analisi di flusso con archiviazione di Azure e hub eventi di Azure in esecuzione nell'hub Azure Stack. Per ulteriori informazioni, vedere [analisi di flusso di Azure in IOT Edge](stream-analytics-edge.md) 

Oltre ad avere accesso di rete alle risorse dell'hub Azure Stack, il dispositivo IoT Edge (o macchina virtuale) deve accedere all'hub di Azure Internet nel cloud pubblico di Azure per configurare il modulo di analisi di flusso. 

Le guide seguenti illustrano come configurare il runtime di IoT Edge nel dispositivo o nella VM:

* [Installare il runtime Azure IoT Edge in Windows](../iot-edge/how-to-install-iot-edge-windows.md)
* [Installare il runtime Azure IoT Edge in sistemi Linux basati su Debian](../iot-edge/how-to-install-iot-edge-linux.md)


## <a name="create-an-azure-stream-analytics-edge-job"></a>Creare un processo Edge di analisi di flusso di Azure

I processi Edge di Analisi di flusso di Azure vengono eseguiti in contenitori distribuiti in dispositivi Azure IoT Edge. Sono costituiti da due parti:
* Una parte nel cloud responsabile della definizione del processo: gli utenti definiscono input, output, query e altre impostazioni (eventi non in ordine e così via) nel cloud.
* Un modulo in esecuzione nei dispositivi IoT. Contiene il motore di Analisi di flusso di Azure e riceve la definizione del processo dal cloud.

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

Quando si crea un processo di Analisi di flusso di Azure da eseguire in un dispositivo IoT Edge, è necessario archiviarlo in modo che possa essere chiamato dal dispositivo. È possibile usare un account di archiviazione di Azure esistente o crearne uno nuovo.
1. Nella portale di Azure passare a **Crea una risorsa > archiviazione > account di archiviazione: BLOB, file, tabelle, code**.
2. Specificare i valori seguenti per creare l'account di archiviazione:

   | Campo | Valore |
   | --- | --- |
   | Nome | Immettere un nome univoco per l'account di archiviazione. |
   | Location | Scegliere una località vicina.|
   | Subscription | Scegliere la stessa sottoscrizione dell'hub IoT.|
   | Gruppo di risorse | Si consiglia di usare lo stesso gruppo di risorse per tutte le risorse di test create durante il [IOT Edge guide introduttive](https://docs.microsoft.com/azure/iot-edge/quickstart) ed esercitazioni. Ad esempio, **IoTEdgeResources**. |

3. Mantenere i valori predefiniti per gli altri campi e selezionare **Crea**.


### <a name="create-a-new-job"></a>Creare un nuovo processo

1. Nella portale di Azure passare a **Crea una risorsa > Internet delle cose > processo di analisi di flusso**.
2. Specificare i valori seguenti per creare l'account di archiviazione:

   | Campo | Valore |
   | --- | --- |
   | Nome processo | Dare un nome al processo. Ad esempio, **IoTEdgeJob** |
   | Subscription | Scegliere la stessa sottoscrizione dell'hub IoT.|
   | Gruppo di risorse | Si consiglia di usare lo stesso gruppo di risorse per tutte le risorse di test create durante il [IOT Edge guide introduttive](https://docs.microsoft.com/azure/iot-edge/quickstart) ed esercitazioni. Ad esempio, **IoTEdgeResources**. |
   | Location | Scegliere una località vicina. |
   | Ambiente host | Selezionare **Edge**. |

3. Selezionare **Crea**.

### <a name="configure-your-job"></a>Configurare il processo

Dopo avere creato il processo di Analisi di flusso nel portale di Azure, è possibile configurarlo con un input, un output e una query da eseguire sui dati di cui viene eseguito il pass-through. È possibile specificare manualmente gli input da un hub o da un hub eventi in una sottoscrizione di hub Azure Stack.

1. Passare al processo di Analisi di flusso nel portale di Azure.
2. In **Configura**selezionare **Impostazioni account di archiviazione** e scegliere l'account di archiviazione creato nel passaggio precedente.
   > [!div class="mx-imgBorder"]
   > [Impostazione dell'account di archiviazione del ![ ](media/on-azure-stack/storage-account-settings.png) processo ](media/on-azure-stack/storage-account-settings.png#lightbox)
3. In **topologia processo**selezionare **input** e quindi **Aggiungi flusso.**
4. Scegliere **Hub**eventi, **Hub eventi**o **Hub Edge** dall'elenco a discesa. 
5. Se l'input è un hub eventi o un hub Internet in una sottoscrizione Hub Azure Stack, specificare le informazioni manualmente, come illustrato di seguito.

   #### <a name="event-hub"></a>Hub eventi

   | Campo | Valore |
   | --- | --- |
   | Alias di input | Nome descrittivo che viene usato nella query del processo per fare riferimento a questo input. |
   | Spazio dei nomi del bus di servizio | Lo spazio dei nomi è un contenitore per un set di entità di messaggistica. Quando si crea un nuovo hub eventi, viene creato anche lo spazio dei nomi. (Esempio: *SB:// <Event Hub Name> . eventhub.Shanghai.azurestack.Corp.Microsoft.com*) |
   | Nome dell'hub eventi | Nome dell'hub eventi da usare come input. |
   | Nome criteri hub eventi | Criteri di accesso condiviso che consentono di accedere all'hub eventi. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. Il valore di questa opzione viene inserito automaticamente, a meno che non si selezioni l'opzione per specificare le impostazioni dell'hub eventi manualmente. |
   | Chiave criteri hub eventi | Chiave di accesso condiviso usata per autorizzare l'accesso all'hub eventi. Il valore di questa opzione viene inserito automaticamente, a meno che non si selezioni l'opzione per specificare le impostazioni dell'hub eventi manualmente. È possibile trovarlo nelle impostazioni dell'hub eventi. |
   | Gruppo di consumer dell'hub eventi (facoltativo) | È vivamente consigliato usare un gruppo di consumer distinto per ogni processo di Analisi di flusso. Questa stringa identifica il gruppo di consumer da usare per l'inserimento di dati dall'hub eventi. Se non è specificato alcun gruppo di consumer, il processo di Analisi di flusso usa il gruppo di consumer $Default. |
   | Conteggio partizioni | Partition count è il numero di partizioni in un hub eventi. |

   > [!div class="mx-imgBorder"]
   > [![Input ](media/on-azure-stack/event-hub-input.png) dell'hub eventi](media/on-azure-stack/event-hub-input.png#lightbox)

   #### <a name="iot-hub"></a>Hub IoT

   | Campo | Valore |
   | --- | --- |
   | Alias di input | Nome descrittivo che viene usato nella query del processo per fare riferimento a questo input. |
   | Hub IoT | Nome dell'hub IoT da usare come input. (Esempio:* <IoT Hub Name> . Shanghai.azurestack.Corp.Microsoft.com*) |
   | Nome dei criteri di accesso condiviso | Criteri di accesso condiviso che consentono di accedere all'hub IoT. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. |
   | Chiave criteri di accesso condiviso | Chiave di accesso condiviso usata per autorizzare l'accesso all'hub IoT. Il valore di questa opzione viene inserito automaticamente, a meno che non si selezioni l'opzione per specificare le impostazioni dell'hub IoT manualmente. |
   | Gruppo di consumer (facoltativo) | È vivamente consigliato usare un gruppo di consumer differente per ogni processo di Analisi di flusso. Il gruppo di consumer viene usato per inserire dati dall'hub IoT. Analisi di flusso usa il gruppo di consumer $Default se non diversamente specificato. |
   | Conteggio partizioni | Partition count è il numero di partizioni in un hub eventi. |

   > [!div class="mx-imgBorder"]
   > [Input dell'hub Internet ![ ](media/on-azure-stack/iot-hub-input.png)](media/on-azure-stack/iot-hub-input.png#lightbox)

6. Mantenere i valori predefiniti per gli altri campi e selezionare Salva.
7. In Topologia processo aprire Output, quindi selezionare Aggiungi.
8. Scegliere archiviazione BLOB, Hub eventi o Hub Edge dall'elenco a discesa.
9. Se l'output è un hub eventi o un archivio BLOB in una sottoscrizione Hub Azure Stack, specificare le informazioni manualmente, come illustrato di seguito.

   #### <a name="event-hub"></a>Hub eventi

   | Campo | Valore |
   | --- | --- |
   | Alias di output | Nome descrittivo usato nelle query per indirizzare l'output delle query a questo hub eventi. |
   | Spazio dei nomi del bus di servizio | Contenitore per un set di entità di messaggistica. Quando è stato creato un nuovo hub eventi, è stato creato anche uno spazio dei nomi del bus di servizio. (Esempio: *SB:// <Event Hub Name> . eventhub.Shanghai.azurestack.Corp.Microsoft.com*) |
   | Nome dell'hub eventi | Nome dell'output dell'hub eventi. |
   | Nome criteri hub eventi | Criteri di accesso condivisi che è possibile creare nella scheda Configura dell'hub eventi. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. |
   | Chiave criteri hub eventi | Chiave di accesso condivisa usata per autenticare l'accesso allo spazio dei nomi dell'hub eventi. |

   > [!div class="mx-imgBorder"]
   > [![Output ](media/on-azure-stack/event-hub-output.png) dell'hub eventi](media/on-azure-stack/event-hub-output.png#lightbox)

   #### <a name="blob-storage"></a>Archiviazione BLOB 

   | Campo | Valore |
   | --- | --- |
   | Alias di output | Nome descrittivo usato nelle query per indirizzare l'output delle query a questa archiviazione BLOB. |
   | Account di archiviazione | Nome dell'account di archiviazione in cui si sta inviando l'output. (Esempio: * <Storage Account Name> . blob.Shanghai.azurestack.Corp.Microsoft.com*) |
   | Chiave dell'account di archiviazione | Chiave privata associata all'account di archiviazione. Il valore di questa opzione viene inserito automaticamente, a meno che non si selezioni l'opzione per specificare le impostazioni dell'archiviazione BLOB manualmente. |

> [!NOTE]
> Il formato parquet non è supportato per i processi Edge nell'hub Azure Stack. Per le righe minime e il tempo massimo, usare 0 o lasciarle vuote.


## <a name="deploy-stream-analytics-on-a-vm-or-device-connected-to-azure-stack"></a>Distribuire analisi di flusso in una macchina virtuale o in un dispositivo connesso a Azure Stack

1. Nella portale di Azure aprire l'hub Internet. Passare a **IOT Edge** e fare clic sul dispositivo (macchina virtuale) di destinazione per la distribuzione.
2. Selezionare **Imposta moduli**. Quindi selezionare **+ Aggiungi** e scegliere **modulo di analisi di flusso di Azure**. 
3. Selezionare la sottoscrizione e il processo Edge di analisi di flusso di Steam che è stato creato. Fare clic su **Salva** e selezionare **Avanti: Route**.

   > [!div class="mx-imgBorder"]
   > [![Aggiungi moduli ](media/on-azure-stack/edge-modules.png)](media/on-azure-stack/edge-modules.png#lightbox)

4. Fare clic su **Verifica + crea >**.
5. Nel passaggio **Verifica e crea** selezionare **Crea**. 
   > [!div class="mx-imgBorder"]
   > [![Manifesto ](media/on-azure-stack/module-content.png)](media/on-azure-stack/module-content.png#lightbox)
6. Verificare che il modulo sia stato aggiunto all'elenco.
   > [!div class="mx-imgBorder"]
   > [![Pagina ](media/on-azure-stack/edge-deployment.png) distribuzione](media/on-azure-stack/edge-deployment.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi
- [Analisi di flusso di Azure in IoT Edge](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge)
- [Sviluppare processi Edge di analisi di flusso](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
