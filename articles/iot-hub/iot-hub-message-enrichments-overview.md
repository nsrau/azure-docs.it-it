---
title: Panoramica dei miglioramenti di messaggi dell'IoT Hub di Azure
description: Panoramica dei miglioramenti di messaggio per i messaggi dell'IoT Hub di Azure
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 13e35ab93fc37541548785c6355489eaf3a3efc2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66754558"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages-preview"></a>Miglioramenti di messaggio per i messaggi da dispositivo a cloud dell'IoT Hub (anteprima)

*Miglioramenti dei messaggi* è la capacità dell'IoT Hub per *timbro* messaggi con informazioni aggiuntive prima che i messaggi vengono inviati all'endpoint designato. Uno dei motivi per utilizzare miglioramenti messaggio consiste nell'includere dati che possono essere utilizzati per semplificare l'elaborazione downstream. Ad esempio, il miglioramento dei messaggi di telemetria da dispositivo con un tag di dispositivo gemello può ridurre il carico sui clienti a effettuare chiamate API per ottenere queste informazioni dispositivo gemello.

![Flusso dei miglioramenti di messaggi](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

Un arricchimento dei messaggi presenta tre elementi chiave:

* Abilitare l'arricchimento dei nome o la chiave

* Un valore

* Uno o più [endpoint](iot-hub-devguide-endpoints.md) per cui deve essere applicato l'arricchimento.

La chiave può essere qualsiasi stringa.

Il valore può essere uno degli esempi seguenti:

* Qualsiasi stringa statica. I valori dinamici, ad esempio le condizioni, per la logica, operazioni e funzioni non sono consentiti. Ad esempio, se si sviluppa un'applicazione SaaS che viene usata da clienti diversi, è possibile assegnare un identificatore a ogni cliente e rendere disponibili nell'applicazione di tale identificatore. Quando viene eseguita l'applicazione, l'IoT Hub verrà contrassegnare il dispositivo i messaggi di telemetria con identificatore del cliente, rendendo possibile l'elaborazione dei messaggi in modo diverso per ogni cliente.

* Informazioni dal dispositivo gemello, ad esempio il relativo percorso. Esempi sarebbe *$twin.tags.field* e *$twin.tags.latitude*.

* Il nome dell'hub IoT invia il messaggio. Questo valore è *$iothubname*.

## <a name="applying-enrichments"></a>Miglioramenti di applicazione

I messaggi possono provenire da qualsiasi origine dati supportata da [routing dei messaggi dell'IoT Hub](iot-hub-devguide-messages-d2c.md), inclusi gli esempi seguenti:

* telemetria del dispositivo, ad esempio temperatura o pressione
* notifiche di modifica dispositivo gemello: le modifiche nel dispositivo gemello
* eventi del ciclo di vita di dispositivo, ad esempio quando il dispositivo viene creato o eliminato

È possibile aggiungere i miglioramenti per i messaggi che verranno all'endpoint predefinito di un IoT Hub, o che vengono indirizzati a endpoint personalizzati, ad esempio archiviazione Blob di Azure, una coda del Bus di servizio o un argomento del Bus di servizio.

È anche possibile aggiungere i miglioramenti ai messaggi che vengono pubblicati selezionando l'endpoint come griglia di eventi a griglia di eventi. Per altre informazioni, vedere [Iot Hub e griglia di eventi](iot-hub-event-grid.md).

Miglioramenti vengono applicate per ogni endpoint. Se si specificano cinque miglioramenti da aggiungere per un endpoint specifico, tutti i messaggi inviati a tale endpoint sono contrassegnati con i miglioramenti di cinque stesso.

Per informazioni su come provare miglioramenti di messaggio, vedere il [esercitazione miglioramenti di messaggio](tutorial-message-enrichments.md)

## <a name="limitations"></a>Limitazioni

* È possibile aggiungere fino a 10 miglioramenti per l'IoT Hub per gli hub nel livello standard o basic. Per gli hub IoT nel livello gratuito, è possibile aggiungere fino a 2 miglioramenti.

* In alcuni casi, se si sta applicando un arricchimento con un valore impostato su un tag o proprietà nel dispositivo gemello, il valore verrà contrassegnato come valore stringa. Ad esempio, se un valore di arricchimento è impostato su $twin.tags.field, i messaggi verranno contrassegnati con la stringa "$twin.tags.field" anziché il valore del campo da un gemello. Ciò si verifica nei casi seguenti:

   * L'IoT Hub è nel livello basic. Gli hub IoT di livello Basic non supportano i dispositivi gemelli.

   * L'IoT Hub è nel livello standard, ma il dispositivo che invia il messaggio non dispone di alcun dispositivo gemello.

   * L'IoT Hub è nel livello standard, ma il percorso del dispositivo gemello usato per il valore dell'arricchimento non esiste. Ad esempio, se il valore di arricchimento è impostato su $twin.tags.location e il dispositivo gemello non ha una proprietà di posizione con tag, il messaggio è contrassegnato dal con la stringa "$twin.tags.location". 

* Gli aggiornamenti a un dispositivo gemello possono richiedere fino a cinque minuti per essere riflessa nel valore arricchimento corrispondente.

* La dimensione totale dei messaggi, inclusi i miglioramenti, non può superare i 256 KB. Se una dimensione del messaggio supera i 256 KB, l'IoT Hub eliminerà il messaggio. È possibile usare [le metriche dell'IoT Hub](iot-hub-metrics.md) identificare ed eseguire il debug degli errori quando i messaggi vengono eliminati. Ad esempio, è possibile monitorare d2c.telemetry.egress.invalid.

## <a name="pricing"></a>Prezzi

Miglioramenti di messaggio sono disponibili senza alcun costo aggiuntivo. Attualmente, vengono addebitati quando si invia un messaggio a un IoT Hub. Viene addebitato solo una volta per il messaggio, anche se il messaggio viene inviato a più endpoint.

## <a name="availability"></a>Disponibilità

Questa funzionalità è disponibile in anteprima ed è disponibile in tutte le aree ad eccezione degli Stati Uniti orientali, Stati Uniti occidentali, Europa occidentale [Azure per enti pubblici](/azure/azure-government/documentation-government-welcome), [Azure Cina 21Vianet](/azure/china), e [Azure Germania](https://azure.microsoft.com/global-infrastructure/germany/).

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti per altre informazioni sul routing dei messaggi a un IoT Hub:

* [Usare il routing dei messaggi dell'IoT Hub per inviare messaggi da dispositivo a cloud a endpoint diversi](iot-hub-devguide-messages-d2c.md)

* [Esercitazione: Routing dell'IoT Hub](tutorial-routing.md)