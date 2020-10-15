---
title: Come integrare RabbitMQ con il bus di servizio di Azure
description: Guida dettagliata per l'integrazione di RabbitMQ con il bus di servizio di Azure
services: Azure Service Bus
author: videlalvaro
ms.topic: how-to
ms.service: service-bus
ms.date: 07/02/2020
ms.author: alvidela
ms.openlocfilehash: 6366824b8dc7f63f99ebda2a542d95d3eb1c6146
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91301106"
---
# <a name="how-to-integrate-rabbitmq-with-azure-service-bus"></a>Come integrare RabbitMQ con il bus di servizio di Azure

In questa guida si apprenderà come inviare messaggi da RabbitMQ al bus di servizio di Azure.

Ecco alcuni scenari in cui è possibile usare queste funzionalità:

- **Configurazioni di Edge**: è presente una configurazione perimetrale in cui si inviano messaggi a RabbitMQ, ma si vuole inoltrare tali messaggi al [bus di servizio di Azure](./service-bus-messaging-overview.md) per un'ulteriore elaborazione, quindi è possibile usare molte delle funzionalità di [Big Data di Azure](/azure/architecture/guide/architecture-styles/big-data).
- **Cloud ibrido**: la tua azienda ha appena acquisito una terza parte che usa RabbitMQ per le proprie esigenze di messaggistica. Si trovano in un cloud diverso. Durante la transizione ad Azure è già possibile iniziare a condividere i dati mediante il bridging di RabbitMQ con il bus di servizio di Azure.
- **Integrazione di terze parti**: una terza parte USA RabbitMQ come broker e vuole inviare i propri dati a Microsoft, ma sono esterni all'organizzazione. È possibile fornire loro una chiave di firma di accesso condiviso per consentire l'accesso a un set limitato di code del bus di servizio di Azure in cui è possibile inviare i messaggi a.

L'elenco viene eseguito, ma è possibile risolvere la maggior parte di questi casi di utilizzo colmando RabbitMQ in Azure.

Per prima cosa è necessario creare un account Azure gratuito iscrivendosi [qui](https://azure.microsoft.com/free/)

Dopo aver eseguito l'accesso al proprio account, passare alla [portale di Azure](https://portal.azure.com/) e creare un nuovo [spazio dei nomi](./service-bus-create-namespace-portal.md)del bus di servizio di Azure. Gli spazi dei nomi sono i contenitori di ambito in cui i componenti di messaggistica saranno attivi, ad esempio code e argomenti.

## <a name="adding-a-new-azure-service-bus-namespace"></a>Aggiunta di un nuovo spazio dei nomi del bus di servizio

In portale di Azure fare clic sul pulsante con il segno più grande per aggiungere una nuova risorsa

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-resource.png" alt-text="Crea risorsa":::

Quindi selezionare integrazione e fare clic su bus di servizio di Azure per creare uno spazio dei nomi di messaggistica:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/integration.png" alt-text="Crea risorsa":::

Verrà richiesto di immettere le informazioni sullo spazio dei nomi. Selezionare la sottoscrizione di Azure da usare. Se non si dispone di un [gruppo di risorse](../azure-resource-manager/management/manage-resource-groups-portal.md), è possibile crearne uno nuovo.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace.png" alt-text="Crea risorsa":::

Usare `rabbitmq` per `Namespace name` , ma potrebbe essere qualsiasi elemento desiderato. Quindi impostare `East US` per il percorso. Scegliere `Basic` come livello di prezzo.

Se tutto è andato bene, viene visualizzata la schermata di conferma seguente:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace-confirm.png" alt-text="Crea risorsa":::

Quindi, tornando al portale di Azure verrà visualizzato il nuovo `rabbitmq` spazio dei nomi. Fare clic su di esso per accedere alla risorsa, in modo da potervi aggiungere una coda.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/resource-view-with-namespace.png" alt-text="Crea risorsa":::

## <a name="creating-our-azure-service-bus-queue"></a>Creazione della coda del bus di servizio di Azure

Ora che è disponibile lo spazio dei nomi del bus di servizio di Azure, fare clic sul `Queues` pulsante a sinistra, in `Entities` , per poter aggiungere una nuova coda:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-queue.png" alt-text="Crea risorsa":::

Il nome della coda sarà `from-rabbitmq` esattamente come un promemoria per la provenienza dei messaggi. È possibile lasciare tutte le altre opzioni come predefinite, ma è possibile modificarle in base alle esigenze dell'app.

## <a name="enabling-the-rabbitmq-shovel-plugin"></a>Abilitazione del plug-in RabbitMQ pala

Per inviare messaggi da RabbitMQ al bus di servizio di Azure, verrà usato il plug-in di [Spalato](https://www.rabbitmq.com/shovel.html) fornito con RabbitMQ. Con questo comando è possibile abilitare il plug-in e la relativa interfaccia visiva:

```bash
rabbitmq-plugins enable rabbitmq_shovel_management
```

>Potrebbe essere necessario eseguire il comando come radice.

È ora possibile ottenere le credenziali necessarie per la connessione di RabbitMQ ad Azure.

## <a name="connecting-rabbitmq-to-azure-service-bus"></a>Connessione di RabbitMQ al bus di servizio di Azure

È necessario creare un criterio di [accesso condiviso](../storage/common/storage-sas-overview.md) (SAS) per la coda, in modo che RabbitMQ possa pubblicare messaggi. Un criterio di firma di accesso condiviso consente di specificare quale entità esterna è autorizzata a eseguire con la risorsa. Il concetto è che RabbitMQ è in grado di inviare messaggi, ma non di ascoltare o gestire la coda.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-sas-policy.png" alt-text="Crea risorsa":::

Selezionare la `Send` casella e quindi fare clic su `Create` per applicare i criteri di firma di accesso condiviso.

Una volta creato il criterio, fare clic su di esso per visualizzare la **stringa di connessione primaria**. Verrà usato per consentire a RabbitMQ di comunicare con il bus di servizio di Azure:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/sas-policy-key.png" alt-text="Crea risorsa":::

Prima di poter usare la stringa di connessione, è necessario convertirla nel formato di connessione AMQP di RabbitMQ. Quindi, passare allo [strumento di conversione della stringa di connessione](https://red-mushroom-0f7446a0f.azurestaticapps.net/) e incollare la stringa di connessione nel modulo, fare clic su Converti. Si otterrà una stringa di connessione RabbitMQ Ready. Il sito Web esegue tutti gli elementi locali del browser in modo che i dati non vengano inviati in rete. È possibile accedere al codice sorgente in [GitHub](https://github.com/videlalvaro/connstring_to_amqp).

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/converter.png" alt-text="Crea risorsa":::

Aprire quindi il plug-in di gestione di RabbitMQ nel browser `http://localhost:15672/#/dynamic-shovels` e passare a `Admin -> Shovel Management` , in cui è possibile aggiungere la nuova paletta che verrà inviata per l'invio di messaggi da una coda di RabbitMQ alla coda del bus di servizio di Azure.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/add-shovel.png" alt-text="Crea risorsa":::

Chiamare la pala `azure` e scegliere `AMQP 0.9.1` come protocollo di origine. Nello screenshot abbiamo `amqp://` , ovvero l'URI predefinito che ci connette a un server RabbitMQ locale. Assicurarsi di adattarlo alla distribuzione corrente.

Sul lato coda di elementi, è possibile usare `azure` come nome della coda. Se la coda non esiste, verrà creata da RabbitMQ. È anche possibile scegliere il nome di una coda già esistente. È possibile lasciare le altre opzioni come predefinite.

Quindi, a `destination` parte gli elementi, scegliere `AMQP 1.0` come protocollo. Nel `URI` campo immettere la stringa di connessione ottenuta nel passaggio precedente. la stringa di connessione di Azure è stata convertita nel formato RabbitMQ. Avrà un aspetto simile al seguente:

```
amqps://rabbitmq-shovel:StringOfRandomChars@rabbitmq.servicebus.windows.net:5671/?sasl=plain
```

Nel `Address` campo immettere il nome della coda del bus di **servizio di Azure**, in questo caso è stato chiamato `from-rabbitmq` . Fare clic su `Add Shovel` e la configurazione deve essere pronta per iniziare a ricevere messaggi.

## <a name="publishing-messages-from-rabbitmq-to-azure-service-bus"></a>Pubblicazione di messaggi da RabbitMQ al bus di servizio di Azure

Nell'interfaccia di gestione di RabbitMQ è possibile passare a `Queues` , selezionare la `azure` coda e cercare il `Publish message` pannello. Verrà visualizzato un modulo che consente di pubblicare i messaggi direttamente nella coda. Per questo esempio, aggiungeremo solo `fist message` come `Payload` e hit `Publish Message` :

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/first-message.png" alt-text="Crea risorsa":::

Tornare ad Azure e controllare la coda. Fare clic `Service Bus Explorer` nel riquadro a sinistra, quindi fare clic sul pulsante _Visualizza_ . Se tutto è andato bene, la coda avrà ora un messaggio. Yay, congratulazioni!

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/service-bus-queue.png" alt-text="Crea risorsa":::

Assicurarsi però che il messaggio sia quello inviato da RabbitMQ. Selezionare la `Peek` scheda e fare clic sul `Peek` pulsante per recuperare gli ultimi messaggi nella coda. Fare clic sul messaggio per esaminarne il contenuto. Verrà visualizzata una schermata simile all'immagine seguente, in cui `first message` è elencato.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/peek.png" alt-text="Crea risorsa":::

## <a name="lets-recap"></a>Riepilogo

alla fase di test. Il risultato è stato molto elevato. È stato gestito per ricevere i messaggi da RabbitMQ al bus di servizio di Azure. riepilogare i passaggi:

1. Creare uno spazio dei nomi del bus di servizio di Azure
2. Aggiungere una coda allo spazio dei nomi
3. Aggiungere un criterio SAS alla coda
4. Ottenere la stringa di connessione della coda
5. Abilitare il plug-in RabbitMQ pala & l'interfaccia di gestione
6. Converte la stringa di connessione del bus di servizio di Azure nel formato AMQP di RabbitMQ
7. Aggiungere una nuova paletta a RabbitMQ & connetterla al bus di servizio di Azure
8. Pubblica messaggi

Seguendo i passaggi precedenti, sono state integrate le aree dell'organizzazione esterne ad Azure. Il plug-in di Spalato ha consentito di inviare messaggi da RabbitMQ al bus di servizio di Azure. Si tratta di vantaggi enormi, perché ora è possibile consentire a terze parti attendibili di connettere le proprie app alla distribuzione di Azure.

Alla fine, la messaggistica riguarda l'abilitazione delle connessioni e con questa tecnica abbiamo appena aperto una nuova.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul [bus di servizio di Azure](./service-bus-messaging-overview.md)
- Altre informazioni sul [supporto di AMQP 1,0 nel bus di servizio](./service-bus-amqp-overview.md)
