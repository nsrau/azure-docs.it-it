---
title: Come aggiungere un'origine evento dell'hub eventi in Azure Time Series Insights | Microsoft Docs
description: Questo articolo descrive come aggiungere un'origine evento connessa a un hub eventi all'ambiente Time Series Insights.
services: time-series-insights
ms.service: time-series-insights
author: sandshadow
ms.author: edett
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/21/2017
ms.openlocfilehash: c07c847784eb13c62e350e9c655e027e7df696a3
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2017
---
# <a name="how-to-add-an-event-hub-event-source-to-time-series-insights-environment"></a>Come aggiungere un'origine evento hub eventi a un ambiente Time Series Insights

Questo articolo descrive come usare il portale di Azure per aggiungere all'ambiente Time Series Insights un'origine evento che legge dati da un hub eventi.

## <a name="prerequisites"></a>prerequisiti
- Creare un ambiente Time Series Insights Per altre informazioni, vedere [Creare un ambiente Azure Time Series Insights](time-series-insights-get-started.md). 
- Creare un hub eventi. Per altre informazioni sugli hub eventi, vedere [Creare uno spazio dei nomi di Hub eventi e un hub eventi usando il portale di Azure](../event-hubs/event-hubs-create.md).
- Lo hub eventi deve ricevere eventi messaggio attivi. Per altre informazioni, vedere [Inviare eventi a Hub eventi di Azure usando .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Creare un gruppo di consumer dedicato in Hub eventi per l'utilizzo nell'ambiente Time Series Insights. Tutte le origini evento Time Series Insights devono avere un proprio gruppo di consumer dedicato che non sia condiviso con altri consumer. Se più lettori consumano eventi dello stesso gruppo di consumer, è probabile che tutti i lettori riscontrino errori. Si noti che è presente anche un limite di 20 gruppi di utenti per Hub eventi. Per informazioni dettagliate, vedere la [Guida alla programmazione di Hub eventi](../event-hubs/event-hubs-programming-guide.md).

## <a name="add-a-new-event-source"></a>Aggiungere una nuova origine evento
1. Accedere al [portale di Azure](https://portal.azure.com).

2. Individuare l'ambiente Time Series Insights esistente. Fare clic su **Tutte le risorse** nel menu sul lato sinistro del portale di Azure. Selezionare l'ambiente Time Series Insights.

3. Sotto l'intestazione **Topologia dell'ambiente** fare clic su **Origini eventi**.

   ![+ Aggiungi origini eventi](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png)

4. Fare clic su **+ Aggiungi**.

5. Specificare un **Nome dell'origine evento** univoco per questo ambiente Time Series Insights, ad esempio **event-stream**.

   ![Compilare i primi tre parametri del modulo.](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png)

6. Per **Origine** selezionare **Hub eventi**.

7. Selezionare l'**Opzione di importazione** appropriata. 
   - Se per una delle sottoscrizioni disponibili esiste già un hub eventi, scegliere **Usare l'hub eventi dalle sottoscrizioni disponibili**. Questo è l'approccio più semplice.
   - Scegliere **Specificare le impostazioni dell'hub eventi manualmente** se l'hub eventi è esterno alle sottoscrizioni o si vogliono scegliere opzioni avanzate. 

8. La tabella seguente illustra le proprietà obbligatorie se si seleziona l'opzione **Usare l'hub eventi dalle sottoscrizioni disponibili**:

   ![Dettagli hub eventi e sottoscrizione](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)

   | Proprietà | DESCRIZIONE |
   | --- | --- |
   | ID sottoscrizione | Selezionare la sottoscrizione in cui l'hub eventi è stato creato.
   | Spazio dei nomi del bus di servizio | Selezionare lo spazio dei nomi del bus di servizio che contiene l'hub eventi.
   | Nome hub eventi | Selezionare il nome dell'hub eventi.
   | Nome criteri hub eventi | Selezionare i criteri di accesso condiviso, che possono essere creati nella scheda Configura dell'hub eventi. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. I criteri di accesso condiviso per l'origine evento *devono* disporre di autorizzazioni di **lettura**.
   | Chiave criteri hub eventi | Il valore della chiave può essere prepopolato.
   | Gruppo di consumer dell'hub eventi | Il gruppo di consumer per la lettura degli eventi dall'hub eventi. È consigliabile usare un gruppo di consumer dedicato per l'origine evento. |
   | Formato di serializzazione eventi | JSON è l'unico formato di serializzazione attualmente disponibile. I messaggi relativi agli eventi devono essere in questo formato. In caso contrario, non è possibile leggere i dati. |
   | Nome della proprietà Timestamp | Per determinare questo valore, è necessario comprendere il formato dei dati del messaggio inviato all'hub eventi. Questo valore è il **nome** della proprietà evento specifica nei dati del messaggio che si vuole usare come timestamp dell'evento. Il valore fa distinzione tra maiuscole e minuscole. Se lasciato vuoto, come timestamp dell'evento viene usato il **tempo di accodamento dell'evento** all'interno dell'origine evento. |


9. La tabella seguente illustra le proprietà obbligatorie se si seleziona l'opzione **Specificare le impostazioni dell'hub eventi manualmente**:

   | Proprietà | DESCRIZIONE |
   | --- | --- |
   | ID sottoscrizione | La sottoscrizione in cui l'hub eventi è stato creato.
   | Gruppo di risorse | Gruppo di risorse in cui è stato creato questo hub eventi.
   | Spazio dei nomi del bus di servizio | Uno spazio dei nomi Service Bus è un contenitore per un set di entità di messaggistica. Quando si crea un nuovo Hub eventi, viene inoltre creato uno spazio dei nomi Service Bus.
   | Nome hub eventi | Nome dell'hub eventi. Al momento della creazione, all'hub eventi è stato anche assegnato un nome specifico.
   | Nome criteri hub eventi | Criteri di accesso condiviso che possono essere creati nella scheda Configura dell'hub eventi. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. I criteri di accesso condiviso per l'origine evento *devono* disporre di autorizzazioni di **lettura**.
   | Chiave criteri hub eventi | Chiave di accesso condivisa usata per autenticare l'accesso allo spazio dei nomi del bus di servizio. Digitare la chiave primaria o secondaria qui.
   | Gruppo di consumer dell'hub eventi | Il gruppo di consumer per leggere gli eventi dall'hub eventi. È consigliabile usare un gruppo di consumer dedicato per l'origine evento.
   | Formato di serializzazione eventi | JSON è l'unico formato di serializzazione attualmente disponibile. I messaggi relativi agli eventi devono essere in questo formato. In caso contrario, non è possibile leggere i dati. |
   | Nome della proprietà Timestamp | Per determinare questo valore, è necessario comprendere il formato dei dati del messaggio inviato all'hub eventi. Questo valore è il **nome** della proprietà evento specifica nei dati del messaggio che si vuole usare come timestamp dell'evento. Il valore fa distinzione tra maiuscole e minuscole. Se lasciato vuoto, come timestamp dell'evento viene usato il **tempo di accodamento dell'evento** all'interno dell'origine evento. |


10. Selezionare **Crea** per aggiungere la nuova origine evento.
   
   ![Click Create](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png)

   Dopo la creazione dell'origine evento, Time Series Insights inizierà automaticamente a trasmettere i dati nell'ambiente.


### <a name="add-a-consumer-group-to-your-event-hub"></a>Aggiungere un gruppo di consumer all'hub eventi
I gruppi di consumer vengono usati dalle applicazioni per eseguire il pull dei dati da hub eventi di Azure. Per leggere in modo affidabile i dati dall'hub eventi, specificare un gruppo di consumer dedicato per l'uso esclusivo da parte di questo ambiente Time Series Insights.

Per aggiungere un nuovo gruppo di consumer all'hub eventi, eseguire questa procedura:
1. Nel portale di Azure individuare e aprire l'hub eventi.

2. Sotto l'intestazione **Entità** selezionare **Gruppi di consumer**.

   ![Hub eventi: aggiungere un gruppo di consumer](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png)

3. Selezionare **+ Gruppo di consumer** per aggiungere un nuovo gruppo di consumer. 

4. Nella pagina **Gruppi di consumer** specificare un nuovo **Nome** univoco.  Usare questo nome al momento di creare una nuova origine evento nell'ambiente Time Series Insights.

5. Selezionare **Crea** per creare il nuovo gruppo di consumer.

## <a name="next-steps"></a>Passaggi successivi
- [Definire criteri di accesso ai dati](time-series-insights-data-access.md) per proteggere i dati.
- [Inviare eventi](time-series-insights-send-events.md) all'origine evento.
- Accedere all'ambiente in [Time Series Insights Explorer](https://insights.timeseries.azure.com).
