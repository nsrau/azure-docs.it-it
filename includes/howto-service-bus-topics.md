---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: 7b05f3d8bcca5f26161f4c362078fa134518cafd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627660"
---
## <a name="what-are-service-bus-topics-and-subscriptions"></a>Cosa sono gli argomenti e le sottoscrizioni del bus di servizio?
Gli argomenti e le sottoscrizioni del bus di servizio supportano un modello di comunicazione con messaggistica di *pubblicazione-sottoscrizione* . Quando si usano gli argomenti e le sottoscrizioni, i componenti di un'applicazione distribuita non comunicano direttamente l'uno con l'altro, ma scambiano messaggi tramite un argomento, che agisce da intermediario.

![Concetti relativi agli argomenti](./media/howto-service-bus-topics/sb-topics-01.png)

Diversamente dalle code del bus di servizio, in cui ogni messaggio viene elaborato da un unico consumer, gli argomenti e le sottoscrizioni offrono una forma di comunicazione di tipo "uno-a-molti", che usa un modello di pubblicazione/sottoscrizione. È possibile registrare più sottoscrizioni a un argomento. Quando un messaggio viene inviato a un argomento, viene reso disponibile affinché ogni sottoscrizione possa gestirlo o elaborarlo in modo indipendente.

La sottoscrizione a un argomento è simile a una coda virtuale che riceve copie dei messaggi che sono stati inviati all'argomento. È possibile registrare regole di filtro per un argomento in base alla sottoscrizione. Le regole di filtro consentono di filtrare o limitare quali messaggi relativi a un argomento vengono ricevuti dalle diverse sottoscrizioni dell'argomento.

Gli argomenti e le sottoscrizioni del bus di servizio consentono la scalabilità e l'elaborazione di grandi quantità di messaggi tra un numero elevato di utenti e applicazioni.

[Azure portal]: https://portal.azure.com
[create-namespace]: ./media/howto-service-bus-topics/create-namespace.png
[connection-info]: ./media/howto-service-bus-topics/connection-info.png
[connection-string]: ./media/howto-service-bus-topics/connection-string.png


