---
title: Usare il portale di Azure per creare argomenti e sottoscrizioni del bus di servizio
description: 'Guida introduttiva: Questa guida di avvio rapido illustra come creare un argomento del bus di servizio e le sottoscrizioni a tale argomento usando il portale di Azure.'
author: spelluru
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: 749ba3d2947dfd7defdf2549305f2a61c16ba713
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95803277"
---
# <a name="use-the-azure-portal-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Usare il portale di Azure per creare un argomento del bus di servizio e le sottoscrizioni all'argomento
In questa guida di avvio rapido si usa il portale di Azure per creare un argomento del Bus di servizio e quindi creare le sottoscrizioni a tale argomento. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Cosa sono gli argomenti e le sottoscrizioni del bus di servizio?
Gli argomenti e le sottoscrizioni del bus di servizio supportano un modello di comunicazione con messaggistica di *pubblicazione-sottoscrizione* . Quando si usano gli argomenti e le sottoscrizioni, i componenti di un'applicazione distribuita non comunicano direttamente l'uno con l'altro, ma scambiano messaggi tramite un argomento, che agisce da intermediario.

![Concetti relativi agli argomenti](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Diversamente dalle code del bus di servizio, in cui ogni messaggio viene elaborato da un unico consumer, gli argomenti e le sottoscrizioni offrono una forma di comunicazione "uno a molti" tramite un modello di pubblicazione-sottoscrizione. È possibile registrare più sottoscrizioni a un argomento. Quando un messaggio viene inviato a un argomento, viene reso disponibile affinché ogni sottoscrizione possa gestirlo o elaborarlo in modo indipendente. La sottoscrizione a un argomento è simile a una coda virtuale che riceve copie dei messaggi che sono stati inviati all'argomento. È possibile registrare regole di filtro per un argomento in base alla sottoscrizione in modo da poter filtrare/limitare i messaggi a un argomento ricevuti dalle diverse sottoscrizioni degli argomenti.

Gli argomenti e le sottoscrizioni del bus di servizio consentono scalabilità per elaborare grandi quantità di messaggi tra un numero elevato di utenti e applicazioni.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]

> [!NOTE]
> È possibile gestire le risorse del bus di servizio con [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer consente agli utenti di connettersi a uno spazio dei nomi del bus di servizio e di amministrare le entità di messaggistica in modo semplice. Lo strumento offre caratteristiche avanzate, tra cui funzionalità di importazione/esportazione o la possibilità di testare argomenti, code, sottoscrizioni, servizi di inoltro, hub di notifica e hub eventi. 

## <a name="next-steps"></a>Passaggi successivi
In questo articolo sono stati creati uno spazio dei nomi del bus di servizio, un argomento nello spazio dei nomi e tre sottoscrizioni dell'argomento. Per informazioni su come pubblicare messaggi nell'argomento e sottoscrivere messaggi da una sottoscrizione, vedere una delle guide di avvio rapido seguenti nella sezione **Pubblicare e sottoscrivere messaggi**. 

- [.NET](service-bus-dotnet-how-to-use-topics-subscriptions.md)
- [Java](service-bus-java-how-to-use-topics-subscriptions.md)
- [JavaScript](service-bus-nodejs-how-to-use-topics-subscriptions.md)
- [Python](service-bus-python-how-to-use-topics-subscriptions.md)
- [PHP](service-bus-php-how-to-use-topics-subscriptions.md)
- [Ruby](service-bus-ruby-how-to-use-topics-subscriptions.md)