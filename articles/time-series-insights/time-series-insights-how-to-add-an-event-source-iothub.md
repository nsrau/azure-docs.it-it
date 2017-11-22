---
title: Come aggiungere un'origine evento hub IoT a un ambiente Azure Time Series Insights | Documentazione Microsoft
description: Questo articolo descrive come aggiungere un'origine evento connessa a un hub IoT all'ambiente Time Series Insights
services: time-series-insights
ms.service: time-series-insights
author: sandshadow
ms.author: edett
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: ed31a0e725d1e0863e9c4695d4eccb324f60678a
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="how-to-add-an-iot-hub-event-source-to-time-series-insights-environment"></a>Come aggiungere un'origine evento hub IoT a un ambiente Time Series Insights
Questo articolo descrive come usare il portale di Azure per aggiungere all'ambiente Time Series Insights un'origine evento che legge dati da un hub IoT.

## <a name="prerequisites"></a>Prerequisiti
- Creare un ambiente Time Series Insights Per altre informazioni, vedere [Creare un ambiente Azure Time Series Insights](time-series-insights-get-started.md). 
- Creare un hub IoT. Per altre informazioni sugli hub IoT, vedere [Creare un hub IoT usando il portale di Azure](../iot-hub/iot-hub-create-through-portal.md)
- Lo hub IoT deve ricevere eventi messaggio attivi.
- Creare un gruppo di consumer dedicato nell'hub IoT per l'utilizzo nell'ambiente Time Series Insights. Tutte le origini evento Time Series Insights devono avere un proprio gruppo di consumer dedicato che non sia condiviso con altri consumer. Se più lettori consumano eventi dello stesso gruppo di consumer, è probabile che tutti i lettori riscontrino errori. Per altre informazioni, vedere la [Guida per gli sviluppatori dell'hub IoT](../iot-hub/iot-hub-devguide.md).

## <a name="add-a-new-event-source"></a>Aggiungere una nuova origine evento
1. Accedere al [portale di Azure](https://portal.azure.com).

2. Individuare l'ambiente Time Series Insights esistente. Fare clic su **Tutte le risorse** nel menu sul lato sinistro del portale di Azure. Selezionare l'ambiente Time Series Insights.

3. Sotto l'intestazione **Topologia dell'ambiente** fare clic su **Origini eventi**.
   ![+ Aggiungi origini eventi](media/time-series-insights-how-to-add-an-event-source-iothub/1-event-sources.png)

4. Fare clic su **+ Aggiungi**.

5. Specificare un **Nome dell'origine evento** univoco per questo ambiente Time Series Insights, ad esempio **event-stream**.

   ![Compilare i primi tre parametri del modulo.](media/time-series-insights-how-to-add-an-event-source-iothub/2-import-option.png)

6. Per **Origine** selezionare **Hub IoT**.

7. Selezionare l'**Opzione di importazione** appropriata. 
   - Se per una delle sottoscrizioni disponibili esiste già un hub IoT, scegliere **Usare l'hub IoT dalle sottoscrizioni disponibili**. Questo è l'approccio più semplice.
   - Scegliere **Specificare le impostazioni dell'hub IoT manualmente** se l'hub IoT è esterno alle sottoscrizioni o si vogliono scegliere opzioni avanzate. 

8. La tabella seguente illustra le proprietà obbligatorie se si seleziona l'opzione **Usare l'hub IoT dalle sottoscrizioni disponibili**:

   ![Dettagli hub eventi e sottoscrizione](media/time-series-insights-how-to-add-an-event-source-iothub/3-new-event-source.png)

   | Proprietà | Descrizione |
   | --- | --- |
   | ID sottoscrizione | Selezionare la sottoscrizione in cui l'hub IoT è stato creato.
   | Nome dell'hub IoT | Selezionare il nome dell'hub IoT.
   | Nome criterio dell'hub IoT | Selezionare il criterio di accesso condiviso che si può trovare nella scheda Impostazioni hub IoT. Ogni criterio di accesso condiviso ha un nome, autorizzazioni impostate e chiavi di accesso. Il criterio di accesso condiviso per l'origine evento *deve* avere le autorizzazioni per la **connessione al servizio**.
   | Chiave criteri hub IoT | La chiave è prepopolata.
   | Gruppo di consumer dell'hub IoT | Il gruppo di consumer per la lettura degli eventi dall'hub IoT. È consigliabile usare un gruppo di consumer dedicato per l'origine evento.
   | Formato di serializzazione eventi | JSON è l'unico formato di serializzazione attualmente disponibile. I messaggi relativi agli eventi devono essere in questo formato. In caso contrario, non è possibile leggere i dati. |
   | Nome della proprietà Timestamp | Per determinare questo valore, è necessario comprendere il formato dei dati del messaggio inviato all'hub IoT. Questo valore è il **nome** della proprietà evento specifica nei dati del messaggio che si vuole usare come timestamp dell'evento. Il valore fa distinzione tra maiuscole e minuscole. Se lasciato vuoto, come timestamp dell'evento viene usato il **tempo di accodamento dell'evento** all'interno dell'origine evento. |

9. La tabella seguente illustra le proprietà obbligatorie se si seleziona l'opzione **Specificare le impostazioni dell'hub IoT manualmente**:

   | Proprietà | Descrizione |
   | --- | --- |
   | ID sottoscrizione | La sottoscrizione in cui l'hub IoT è stato creato.
   | Gruppo di risorse | Nome del gruppo di risorse in cui è stato creato questo hub IoT.
   | Nome dell'hub IoT | Il nome dell'hub IoT. Al momento della creazione, all'hub IoT è stato anche assegnato un nome specifico.
   | Nome criterio dell'hub IoT | Il criterio di accesso condiviso che può essere creato nella scheda Impostazioni hub IoT. Ogni criterio di accesso condiviso ha un nome, autorizzazioni impostate e chiavi di accesso. Il criterio di accesso condiviso per l'origine evento *deve* avere le autorizzazioni per la **connessione al servizio**.
   | Chiave criteri hub IoT | Chiave di accesso condiviso usata per autenticare l'accesso allo spazio dei nomi del bus di servizio. Digitare la chiave primaria o secondaria qui.
   | Gruppo di consumer dell'hub IoT | Il gruppo di consumer per la lettura degli eventi dall'hub IoT. È consigliabile usare un gruppo di consumer dedicato per l'origine evento.
   | Formato di serializzazione eventi | JSON è l'unico formato di serializzazione attualmente disponibile. I messaggi relativi agli eventi devono essere in questo formato. In caso contrario, non è possibile leggere i dati. |
   | Nome della proprietà Timestamp | Per determinare questo valore, è necessario comprendere il formato dei dati del messaggio inviato all'hub IoT. Questo valore è il **nome** della proprietà evento specifica nei dati del messaggio che si vuole usare come timestamp dell'evento. Il valore fa distinzione tra maiuscole e minuscole. Se lasciato vuoto, come timestamp dell'evento viene usato il **tempo di accodamento dell'evento** all'interno dell'origine evento. |

10. Selezionare **Crea** per aggiungere la nuova origine evento.

   ![Fare clic su Crea](media/time-series-insights-how-to-add-an-event-source-iothub/4-create-button.png)

   Dopo la creazione dell'origine evento, Time Series Insights inizierà automaticamente a trasmettere i dati nell'ambiente.

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Aggiungere un gruppo di consumer dell'hub IoT
I gruppi di consumer vengono usati dalle applicazioni per eseguire il pull dei dati da hub IoT di Azure. Per leggere in modo affidabile i dati dall'hub IoT, specificare un gruppo di consumer dedicato per l'uso esclusivo da parte di questo ambiente Time Series Insights.

Per aggiungere un nuovo gruppo di consumer all'hub IoT, eseguire questa procedura:
1. Nel portale di Azure individuare e aprire l'hub IoT.

2. Sotto l'intestazione **Messaggistica** selezionare **Endpoint**. 

   ![Aggiungere un gruppo di consumer](media/time-series-insights-how-to-add-an-event-source-iothub/5-add-consumer-group.png)

3. Selezionare l'endpoint **Eventi**. Si aprirà la pagina **Proprietà**.

4. Sotto l'intestazione **Gruppi di consumer** specificare un nuovo nome univoco per il gruppo di consumer. Usare questo nome al momento di creare una nuova origine evento nell'ambiente Time Series Insights.

5. Selezionare **Salva** per salvare il nuovo gruppo di consumer.

## <a name="next-steps"></a>Passaggi successivi
- [Definire criteri di accesso ai dati](time-series-insights-data-access.md) per proteggere i dati.
- [Inviare eventi](time-series-insights-send-events.md) all'origine evento.
- Accedere all'ambiente in [Time Series Insights Explorer](https://insights.timeseries.azure.com).
