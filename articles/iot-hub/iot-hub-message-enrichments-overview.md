---
title: Panoramica degli arricchimenti dei messaggi dell'hub Azure
description: In questo articolo vengono illustrati gli arricchimenti dei messaggi, che consentono all'hub delle cose di contrassegnare i messaggi con informazioni aggiuntive prima che i messaggi vengano inviati all'endpoint designato.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: a623c8d3ff755338ac8b40faa970f2f007115a02
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74144869"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages"></a>Arricchimento dei messaggi per i messaggi dell'hub da dispositivo a cloud

I miglioramenti apportati ai *messaggi sono la* possibilità dell'hub Internet di *contrassegnare* i messaggi con informazioni aggiuntive prima che i messaggi vengano inviati all'endpoint designato. Un motivo per utilizzare gli arricchimenti dei messaggi consiste nell'includere dati che possono essere utilizzati per semplificare l'elaborazione downstream. Ad esempio, l'arricchimento dei messaggi di telemetria del dispositivo con un tag del dispositivo gemello può ridurre il carico sui clienti per effettuare chiamate API dei dispositivi gemelli per queste informazioni.

![Flusso di arricchimenti messaggi](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

Un arricchimento del messaggio ha tre elementi chiave:

* Nome o chiave di arricchimento

* Valore

* Uno o più [endpoint](iot-hub-devguide-endpoints.md) per i quali deve essere applicata l'arricchimento.

La **chiave** è una stringa. Una chiave può contenere solo caratteri alfanumerici o i caratteri speciali seguenti: il segno meno (`-`), il carattere di sottolineatura (`_`) e il punto (`.`).

Il **valore** può essere uno degli esempi seguenti:

* Qualsiasi stringa statica. Non sono consentiti valori dinamici quali le condizioni, la logica, le operazioni e le funzioni. Se, ad esempio, si sviluppa un'applicazione SaaS utilizzata da più clienti, è possibile assegnare un identificatore a ogni cliente e rendere tale identificatore disponibile nell'applicazione. Quando l'applicazione è in esecuzione, l'hub di tutti i messaggi di telemetria del dispositivo viene timbrato con l'identificatore del cliente, rendendo possibile l'elaborazione dei messaggi in modo diverso per ogni cliente.

* Nome dell'hub Internet delle cose che invia il messaggio. Questo valore è *$iothubname*.

* Informazioni del dispositivo gemello, ad esempio il percorso. Gli esempi sono *$Twin. Tags. Field* e *$Twin. Tags. Latitudine*.

   > [!NOTE]
   > Al momento, solo $iothubname, $twin. Tags, $twin. Properties. desired e $twin. Properties. Reports sono variabili supportate per l'arricchimento dei messaggi.

Gli arricchimenti dei messaggi vengono aggiunti come proprietà dell'applicazione ai messaggi inviati agli endpoint scelti.  

## <a name="applying-enrichments"></a>Applicazione di arricchimenti

I messaggi possono provenire da qualsiasi origine dati supportata dal [routing dei messaggi dell'hub](iot-hub-devguide-messages-d2c.md)Internet, inclusi gli esempi seguenti:

* telemetria dei dispositivi, ad esempio temperatura o pressione
* notifiche di modifiche del dispositivo gemello: modifiche nel dispositivo gemello
* eventi del ciclo di vita del dispositivo, ad esempio quando il dispositivo viene creato o eliminato

È possibile aggiungere arricchimenti ai messaggi che vengono indirizzati all'endpoint predefinito di un hub delle cose o ai messaggi che vengono indirizzati a endpoint personalizzati, ad esempio archiviazione BLOB di Azure, una coda del bus di servizio o un argomento del bus di servizio.

È possibile aggiungere arricchimenti ai messaggi che vengono pubblicati in griglia di eventi selezionando l'endpoint come griglia di eventi. Viene creata una route predefinita nell'hub degli indirizzi Internet per la telemetria dei dispositivi, in base alla sottoscrizione di griglia di eventi. Questa singola route può gestire tutte le sottoscrizioni di griglia di eventi. È possibile configurare gli arricchimenti per la griglia di eventi ENT [punto dopo aver creato la sottoscrizione di griglia di eventi per la telemetria del dispositivo. Per altre informazioni, vedere [Hub e griglia di eventi](iot-hub-event-grid.md).

Gli arricchimenti vengono applicati per ogni endpoint. Se si specificano cinque arricchimenti da contrassegnare per un endpoint specifico, tutti i messaggi che passano a tale endpoint vengono contrassegnati con gli stessi cinque arricchimenti.

È possibile configurare gli arricchimenti usando i metodi seguenti:

| **Metodo** | **Comando** |
| ----- | -----| 
| di Microsoft Azure | [Portale di Azure](https://portal.azure.com) | Vedere l' [esercitazione relativa all'arricchimento dei messaggi](tutorial-message-enrichments.md) | 
| Interfaccia della riga di comando di Azure   | [AZ all hub Message-arricchimento](https://docs.microsoft.com/cli/azure/iot/hub/message-enrichment?view=azure-cli-latest) |
| Azure PowerShell | [Add-AzIotHubMessageEnrichment](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubmessageenrichment?view=azps-2.8.0) |

L'aggiunta di arricchimenti dei messaggi non consente di aggiungere latenza al routing dei messaggi.

Per provare ad arricchire i messaggi, vedere l' [esercitazione sull'arricchimento dei messaggi](tutorial-message-enrichments.md)

## <a name="limitations"></a>Limitazioni

* È possibile aggiungere fino a 10 arricchimenti per l'hub di tutti gli hub nel livello standard o Basic. Per gli hub Internet nel livello gratuito, è possibile aggiungere fino a 2 arricchimenti.

* In alcuni casi, se si applica un arricchimento con un valore impostato su un tag o una proprietà nel dispositivo gemello, il valore verrà timbrato come valore stringa. Se, ad esempio, un valore di arricchimento è impostato su $twin. Tags. Field, i messaggi verranno contrassegnati con la stringa "$twin. Tags. Field" invece del valore di tale campo dal dispositivo gemello. Questo problema si verifica nei casi seguenti:

   * L'hub Internet delle cose è nel livello Basic. Gli hub Internet del livello Basic non supportano i dispositivi gemelli.

   * L'hub Internet delle cose è nel livello standard, ma il dispositivo che invia il messaggio non ha un dispositivo gemello.

   * L'hub Internet delle cose è nel livello standard, ma il percorso del dispositivo gemello usato per il valore dell'arricchimento non esiste. Se, ad esempio, il valore di arricchimento è impostato su $twin. Tags. location e il dispositivo gemello non dispone di una proprietà location in Tags, il messaggio viene contrassegnato con la stringa "$twin. Tags. location". 

* Gli aggiornamenti a un dispositivo gemello possono richiedere fino a cinque minuti per essere riflessi nel valore di arricchimento corrispondente.

* Le dimensioni totali dei messaggi, incluse le arricchimenti, non possono superare 256 KB. Se le dimensioni di un messaggio superano 256 KB, l'hub delle cose eliminerà il messaggio. È possibile usare le [metriche dell'hub](iot-hub-metrics.md) Internet per identificare ed eseguire il debug degli errori quando i messaggi vengono eliminati. Ad esempio, è possibile monitorare D2C. telemetria. uscita. non è valido.

* I miglioramenti apportati ai messaggi non si applicano agli eventi di modifica di dispositivi gemelli digitali (parte del [plug and Play di anteprima pubblica](../iot-pnp/overview-iot-plug-and-play.md)).

## <a name="pricing"></a>Prezzi

Gli arricchimenti dei messaggi sono disponibili senza costi aggiuntivi. Attualmente viene addebitato un addebito quando si invia un messaggio a un hub Internet. Viene addebitata una sola volta per il messaggio, anche se il messaggio viene indirizzato a più endpoint.

## <a name="next-steps"></a>Passaggi successivi

Vedere questi articoli per altre informazioni sul routing dei messaggi a un hub Internet:

* [Esercitazione sugli arricchimenti di messaggi](tutorial-message-enrichments.md)

* [Usare il routing dei messaggi dell'hub Internet per inviare messaggi da dispositivo a cloud a endpoint diversi](iot-hub-devguide-messages-d2c.md)

* [Esercitazione: routing dell'hub Internet](tutorial-routing.md)
