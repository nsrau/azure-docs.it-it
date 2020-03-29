---
title: Panoramica degli arricchimenti dei messaggi dell'hub IoT di AzureOverview of Azure IoT Hub message enrichments
description: Questo articolo mostra gli arricchimenti dei messaggi, che consentono all'hub IoT di eliminare i messaggi con informazioni aggiuntive prima che i messaggi vengano inviati all'endpoint designato.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: c3dbd01faf61c164c88f09b0da03c07be4abd187
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75429115"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages"></a>Arricchimenti di messaggi per i messaggi dell'hub IoT da dispositivo a cloudMessage enrichmentments for device-to-cloud IoT Hub messages

*Gli arricchimenti* dei messaggi sono la capacità dell'hub IoT di *eliminare* i messaggi con informazioni aggiuntive prima che i messaggi vengano inviati all'endpoint designato. Uno dei motivi per utilizzare gli arricchimenti dei messaggi consiste nell'includere dati che possono essere utilizzati per semplificare l'elaborazione a valle. Ad esempio, l'arricchimento dei messaggi di telemetria del dispositivo con un tag device twin può ridurre il carico sui clienti per effettuare chiamate API del dispositivo gemello per queste informazioni.

![Flusso degli arricchimenti dei messaggiMessage enrichmentmentments flow](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

L'arricchimento di un messaggio ha tre elementi chiave:

* Nome o chiave dell'arricchimento

* Valore

* Uno o più endpoint per [i](iot-hub-devguide-endpoints.md) quali deve essere applicato l'arricchimento.

La **chiave** è una stringa. Una chiave può contenere solo caratteri alfanumerici o questi caratteri speciali: trattino (`-`), carattere di sottolineatura (`_`) e punto ( ).`.`

Il **valore** può essere uno dei seguenti esempi:

* Qualsiasi stringa statica. Non sono consentiti valori dinamici come condizioni, logica, operazioni e funzioni. Ad esempio, se si sviluppa un'applicazione SaaS utilizzata da più clienti, è possibile assegnare un identificatore a ogni cliente e rendere disponibile tale identificatore nell'applicazione. Quando l'applicazione viene eseguita, l'hub IoT contrassegnerà i messaggi di telemetria del dispositivo con l'identificatore del cliente, rendendo possibile l'elaborazione dei messaggi in modo diverso per ogni cliente.

* Nome dell'hub IoT che invia il messaggio. Questo valore è *$iothubname*.

* Informazioni dal dispositivo gemello, ad esempio il relativo percorso. Esempi sono *$twin.tags.field* e *$twin.tags.latitude*.

   > [!NOTE]
   > Al momento, solo $iothubname, $twin.tags, $twin.properties.desired e $twin.properties.reported sono variabili supportate per l'arricchimento dei messaggi.

Gli arricchimenti dei messaggi vengono aggiunti come proprietà dell'applicazione ai messaggi inviati agli endpoint scelti.  

## <a name="applying-enrichments"></a>Applicazione di arricchimenti

I messaggi possono provenire da qualsiasi origine dati supportata dal routing dei [messaggi dell'hub IoT](iot-hub-devguide-messages-d2c.md), inclusi gli esempi seguenti:

* telemetria del dispositivo, ad esempio temperatura o pressione
* notifiche di modifica del dispositivo gemello - modifiche nel dispositivo gemello
* eventi del ciclo di vita del dispositivo, ad esempio quando il dispositivo viene creato o eliminato

È possibile aggiungere arricchimenti ai messaggi che vengono inviati all'endpoint predefinito di un hub IoT o ai messaggi instradati a endpoint personalizzati, ad esempio l'archiviazione BLOB di Azure, una coda del bus di servizio o un argomento del bus di servizio.

È possibile aggiungere arricchimenti ai messaggi pubblicati in Griglia di eventi selezionando l'endpoint come Griglia di eventi. Creiamo una route predefinita nell'hub IoT per la telemetria del dispositivo, in base alla sottoscrizione di Griglia di eventi. Questa singola route può gestire tutte le sottoscrizioni di Griglia di eventi. È possibile configurare gli arricchimenti per l'endpoint della griglia di eventi dopo aver creato la sottoscrizione della griglia di eventi ai dati di telemetria del dispositivo. Per ulteriori informazioni, consultate [Hub e Griglia di eventi.](iot-hub-event-grid.md)

Gli arricchimenti vengono applicati per endpoint. Se si specificano cinque arricchimenti da timbrare per un endpoint specifico, tutti i messaggi diretti a tale endpoint vengono contrassegnati con gli stessi cinque arricchimenti.

Gli arricchimenti possono essere configurati utilizzando i seguenti metodi:

| **Metodo** | **Comando** |
| ----- | -----| 
| Portale | [Portale di Azure](https://portal.azure.com) | Vedere [l'esercitazione degli arricchimenti dei](tutorial-message-enrichments.md) messaggi | 
| Interfaccia della riga di comando di Azure   | [az iot hub message-enrichment (arricchimento dei messaggi dell'hub)](https://docs.microsoft.com/cli/azure/iot/hub/message-enrichment?view=azure-cli-latest) |
| Azure PowerShell | [Add-AzIotHubMessageEnrichment](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubmessageenrichment?view=azps-2.8.0) |

L'aggiunta di arricchimenti di messaggi non aggiunge latenza al routing dei messaggi.

Per provare gli arricchimenti dei messaggi, vedere l'esercitazione degli arricchimenti dei messaggiTo try out message [enrichmentments,](tutorial-message-enrichments.md) see the message enrichments tutorial

## <a name="limitations"></a>Limitazioni

* È possibile aggiungere fino a 10 arricchimenti per hub IoT per tali hub nel livello standard o di base. Per gli hub IoT nel livello gratuito, puoi aggiungere fino a 2 arricchimenti.

* In alcuni casi, se si applica un arricchimento con un valore impostato a un tag o a una proprietà nel dispositivo gemello, il valore verrà contrassegnato come valore stringa. Ad esempio, se un valore di arricchimento è impostato su $twin.tags.field, i messaggi verranno contrassegnati con la stringa "$twin.tags.field" anziché il valore di tale campo dal gemello. Ciò si verifica nei seguenti casi:

   * L'hub IoT è nel livello di base. Gli hub IoT di livello base non supportano i dispositivi gemelli.

   * L'hub IoT si trova nel livello standard, ma il dispositivo che invia il messaggio non ha un dispositivo gemello.

   * L'hub IoT si trova nel livello standard, ma il percorso del dispositivo gemello usato per il valore dell'arricchimento non esiste. Ad esempio, se il valore di arricchimento è impostato su $twin.tags.location e il dispositivo gemello non dispone di una proprietà location sotto i tag, il messaggio viene contrassegnato con la stringa "$twin.tags.location". 

* Gli aggiornamenti a un dispositivo gemello possono richiedere fino a cinque minuti per essere riflessi nel valore di arricchimento corrispondente.

* La dimensione totale dei messaggi, inclusi gli arricchimenti, non può superare i 256 KB. Se la dimensione di un messaggio supera i 256 KB, l'hub IoT interromperà il messaggio. Puoi usare [le metriche dell'hub IoT](iot-hub-metrics.md) per identificare ed eseguire il debug degli errori quando i messaggi vengono eliminati. Ad esempio, è possibile monitorare d2c.telemetry.egress.invalid.

* Gli arricchimenti di messaggi non si applicano agli eventi di modifica a doppia modifica digitali (parte [dell'anteprima pubblica IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md)).

## <a name="pricing"></a>Prezzi

Gli arricchimenti dei messaggi sono disponibili senza alcun costo aggiuntivo. Attualmente, l'addebito viene effettuato quando invii un messaggio a un hub IoT. L'addebito viene effettuato una sola volta per il messaggio, anche se il messaggio viene ripercorso a più endpoint.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul routing dei messaggi a un hub IoT, consulta questi articoli:

* [Esercitazione sull'arricchimento dei messaggiMessage enrichmentmentments tutorial](tutorial-message-enrichments.md)

* [Usare il routing dei messaggi dell'hub IoT per inviare messaggi da dispositivo a cloud a endpoint diversiUse IoT Hub message routing to send device-to-cloud messages to different endpoints](iot-hub-devguide-messages-d2c.md)

* [Esercitazione: routing dell'hub IoTTutorial: IoT Hub routing](tutorial-routing.md)
