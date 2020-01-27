---
title: 'Esercitazione: Creare un recinto virtuale e monitorare i dispositivi su una mappa | Mappe di Microsoft Azure'
description: Informazioni su come configurare un recinto virtuale e monitorare i dispositivi in relazione al recinto virtuale usando il servizio spaziale di Mappe di Microsoft Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: a88f03adab3beaea75ec2fa9a1c6f59b09739025
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76153141"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Esercitazione: Configurare un recinto virtuale con Mappe di Azure

Questa esercitazione illustra i passaggi di base per la configurazione di un recinto virtuale con Mappe di Azure. Si consideri uno scenario in cui il direttore di un cantiere di costruzione deve monitorare i potenziali macchinari pericolosi. Il responsabile deve assicurarsi che i macchinari rimangano all'interno delle aree di costruzione complessive. Questa area di costruzione complessiva è un parametro rigido. Le normative richiedono che i macchinari rientrino in questo parametro e che le violazioni vengano segnalate al direttore dei lavori.  

Verrà usata l'API Caricamento dati di Mappe di Azure per archiviare un recinto virtuale e si userà l'API Recinto virtuale di Mappe di Azure per verificare la posizione dei macchinari rispetto al recinto virtuale. Sia l'API Caricamento dati che l'API Recinto virtuale sono incluse in Mappe di Azure. Si userà inoltre Griglia di eventi di Azure per trasmettere i risultati del recinto virtuale e configurare una notifica in base ai risultati del recinto virtuale stesso. Per altre informazioni su Griglia di eventi, vedere [Griglia di eventi di Azure](https://docs.microsoft.com/azure/event-grid/overview).

In questa esercitazione verrà illustrato come:

> [!div class="checklist"]
> * Caricare l'area del recinto virtuale nel servizio dati di Mappe di Azure usando l'API Caricamento dati.
> *   Configurare una griglia di eventi per gestire gli eventi del recinto virtuale.
> *   Impostare il gestore di eventi del recinto virtuale.
> *   Configurare gli avvisi in risposta agli eventi del recinto virtuale usando App per la logica.
> *   Usare le API del servizio del recinto virtuale di Mappe di Azure per verificare se un asset di costruzione si trova nel cantiere o meno.


## <a name="prerequisites"></a>Prerequisites

### <a name="create-an-azure-maps-account"></a>Creare un account di Mappe di Azure 

Creare una sottoscrizione dell'account Mappe di Azure nel piano tariffario S1 seguendo le istruzioni riportate in [Creare un account](quick-demo-map-app.md#create-an-account-with-azure-maps). I passaggi descritti in [Ottenere la chiave primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account) illustrano come recuperare la chiave primaria dell'account. Per altre informazioni sull'autenticazione in Mappe di Azure, vedere [Gestire l'autenticazione in Mappe di Azure](./how-to-manage-authentication.md).

## <a name="upload-geofences"></a>Caricare i recinti virtuali

Si presuppone che il recinto virtuale principale sia subsite1, per cui è impostata una data di scadenza. È possibile creare più recinti virtuali annidati a seconda delle esigenze. Questi set di recinti consentono di tenere traccia delle diverse aree del cantiere all'interno del cantiere di costruzione complessivo. È ad esempio possibile che subsite1 sia il luogo in cui si svolgono i lavori durante le settimane da 1 a 4 del piano dei lavori e subsite2 sia il luogo in cui si svolgono i lavori durante le settimane da 5 a 7. Tutti questi recinti possono essere caricati come singolo set di dati all'inizio del progetto ed essere usati per tenere traccia delle regole in base al tempo e allo spazio. 

Per caricare il recinto virtuale per il cantiere con l'API Caricamento dati si userà l'applicazione Postman. Installare l'[applicazione postman](https://www.getpostman.com/) e creare un account gratuito. 

Dopo aver installato l'app Postman, seguire questi passaggi per caricare il recinto virtuale del cantiere usando l'API Caricamento dati in Mappe di Azure.

1. Aprire l'app Postman e fare clic su New | Create new (Nuovo | Crea nuovo) e selezionare Request (Richiesta). Immettere un nome richiesta per Upload geofence data (Carica dati recinto virtuale), selezionare una raccolta o una cartella per il salvataggio, quindi fare clic su Salva (Save).

    ![Caricare i recinti virtuali usando Postman](./media/tutorial-geofence/postman-new.png)

2. Selezionare il metodo HTTP POST nella scheda Builder (Generatore) e immettere l'URL seguente per effettuare una richiesta POST.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    Il parametro GEOJSON nel percorso URL rappresenta il formato dei dati caricati.

3. Fare clic su **Params** (Parametri) e immettere le coppie chiave/valore seguenti da usare per l'URL della richiesta POST. Sostituire {subscription-key} con la chiave di sottoscrizione primaria di Mappe di Azure, nota anche come chiave primaria.
   
    ![Parametri per il caricamento dei dati (recinto virtuale) in Postman](./media/tutorial-geofence/postman-key-vals.png)

4. Fare clic su **Body** (Corpo), quindi selezionare il formato di input non elaborato e scegliere JSON come formato di input nell'elenco a discesa. Specificare il codice JSON seguente come dati da caricare:

   ```JSON
   {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13393688201903,
                  47.63829579223815
                ],
                [
                  -122.13389128446579,
                  47.63782047131512
                ],
                [
                  -122.13240802288054,
                  47.63783312249837
                ],
                [
                  -122.13238388299942,
                  47.63829037035086
                ],
                [
                  -122.13393688201903,
                  47.63829579223815
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "1"
          }
        },
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13374376296996,
                  47.63784758098976
                ],
                [
                  -122.13277012109755,
                  47.63784577367854
                ],
                [
                  -122.13314831256866,
                  47.6382813338708
                ],
                [
                  -122.1334782242775,
                  47.63827591198201
                ],
                [
                  -122.13374376296996,
                  47.63784758098976
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "2",
            "validityTime": {
              "expiredTime": "2019-01-15T00:00:00",
              "validityPeriod": [
                {
                  "startTime": "2019-01-08T01:00:00",
                  "endTime": "2019-01-08T17:00:00",
                  "recurrenceType": "Daily",
                  "recurrenceFrequency": 1,
                  "businessDayOnly": true
                }
              ]
            }
          }
        }
      ]
   }
   ```

5. Fare clic su Send (Invia) e quindi esaminare l'intestazione della risposta. In caso di esito positivo, l'intestazione **Location** conterrà l'URI dello stato. Il formato dell'URI dello stato sarà il seguente. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Copiare l'URI dello stato e aggiungere la chiave di sottoscrizione. Il formato dell'URI dello stato deve essere simile a quello riportato di seguito. Si noti che nel formato seguente è necessario modificare {subscription-key}, incluse le parentesi { }, con la chiave di sottoscrizione.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Per ottenere l'`udId`, aprire una nuova scheda nell'app Postman e selezionare il metodo HTTP GET nella scheda Builder (Generatore) ed effettuare una richiesta GET per l'URI dello stato ottenuto nel passaggio precedente. Se il caricamento dei dati è riuscito, si riceverà il valore di udId nel corpo della risposta. Copiare l'udId per un uso successivo.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```

## <a name="set-up-an-event-handler"></a>Configurare un gestore di eventi

In questa sezione viene creato un gestore eventi che riceve le notifiche. Il gestore eventi deve notificare al direttore dei lavori gli eventi di ingresso e uscita di qualsiasi macchinario.

Verranno creati due servizi di [App per la logica](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) per gestire gli eventi di ingresso e uscita. Quando vengono attivati gli eventi nelle app per la logica, vengono attivati più eventi in sequenza. Verranno quindi inviati avvisi, in questo caso messaggi di posta elettronica, al direttore dei lavori. La figura seguente illustra la creazione di un'app per la logica per l'evento di ingresso nel recinto virtuale. È possibile crearne un'altra allo stesso modo per l'evento di uscita. È possibile vedere tutti i [gestori di eventi supportati](https://docs.microsoft.com/azure/event-grid/event-handlers) per altre informazioni.

1. Creare un'app per la logica nel portale di Azure

   ![Creare app per la logica di Azure per gestire gli eventi del recinto virtuale](./media/tutorial-geofence/logic-app.png)

2. Dal menu Impostazioni dell'app per la logica passare a **Progettazione app per la logica**

3. Selezionare un trigger di richiesta HTTP e quindi selezionare "Nuovo passaggio". Nel connettore per Outlook selezionare "Invia un messaggio di posta elettronica" come azione
  
   ![Schema di App per la logica](./media/tutorial-geofence/logic-app-schema.png)

4. Compilare i campi per l'invio di un messaggio di posta elettronica. Lasciare vuoto il campo relativo all'URL HTTP, che verrà generato automaticamente dopo aver fatto clic su "Salva"

   ![Generare un endpoint delle app per la logica](./media/tutorial-geofence/logic-app-endpoint.png)

5. Salvare l'app per la logica per generare l'endpoint dell'URL HTTP e copiare l'URL HTTP.

## <a name="create-an-azure-maps-events-subscription"></a>Creare una sottoscrizione di Eventi di Mappe di Azure

Mappe di Azure supporta tre tipi di eventi. I tipi di eventi supportati da Mappe di Azure sono disponibili [qui]. https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps Sono necessarie due sottoscrizioni di eventi diverse, una per gli eventi di ingresso e una per gli eventi di uscita.

Seguire questi passaggi per creare una sottoscrizione per gli eventi di ingresso del recinto virtuale. Allo stesso modo è possibile creare una sottoscrizione per gli eventi di uscita del recinto virtuale.

1. Passare all'account Mappe di Azure. Nel dashboard selezionare Sottoscrizioni. Fare clic sul nome della sottoscrizione e selezionare **eventi** dal menu Impostazioni.

   ![Passare all'account eventi di Mappe di Azure](./media/tutorial-geofence/events-tab.png)

2. Per creare una sottoscrizione per gli eventi, selezionare Sottoscrizione di eventi dalla pagina degli eventi.

   ![Creare una sottoscrizione di Eventi di Mappe di Azure](./media/tutorial-geofence/create-event-subscription.png)

3. Assegnare un nome alla sottoscrizione e selezionare il tipo di eventi di ingresso. Selezionare Webhook per "Tipo di endpoint". Fare clic su "Seleziona endpoint" e copiare l'endpoint URL HTTP dell'app per la logica in "{Endpoint}"

   ![Dettagli della sottoscrizione per Eventi di Mappe di Azure](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>Usare l'API Recinto virtuale

È possibile usare l'API Recinto virtuale per verificare se un **dispositivo**, in questo caso i macchinari, si trovano all'interno o all'esterno di un recinto virtuale. L'API GET per il recinto virtuale eseguirà query su diversi luoghi in cui è transitato un macchinario specifico nel tempo. La figura seguente illustra cinque posizioni con cinque macchinari da costruzione. 

> [!Note]
> Lo scenario e il comportamento si basano sullo stesso **ID dispositivo**, quindi riflettono le cinque posizioni come nella figura seguente.

"deviceId" è un ID univoco che viene specificato per il dispositivo nella richiesta GET, quando si esegue una query sulla relativa posizione. Quando si esegue una richiesta asincrona all'**API GET di ricerca recinto virtuale**, "deviceId" consente di pubblicare eventi di recinto virtuale per tale dispositivo, in relazione al recinto virtuale specificato. In questa esercitazione sono state inviate richieste asincrone all'API con un "deviceId" univoco. Le richieste nell'esercitazione vengono eseguite in ordine cronologico, come nel diagramma. La proprietà "isEventPublished" nella risposta viene pubblicata ogni volta che un dispositivo entra o esce dal recinto virtuale. Non è necessario registrare un dispositivo per eseguire questa esercitazione.

Si osservi il diagramma. Ognuna di queste cinque posizioni viene usata per valutare la variazione di stato di ingresso e uscita rispetto al recinto virtuale. Se si verifica una variazione di stato, il servizio del recinto virtuale attiva un evento che viene inviato all'app per la logica dalla griglia di eventi. Di conseguenza, il direttore dei lavori riceverà la notifica di ingresso o uscita corrispondente tramite posta elettronica.

![Mappa del recinto virtuale in Mappe di Azure](./media/tutorial-geofence/geofence.png)

Nell'app Postman aprire una nuova scheda nella stessa raccolta creata in precedenza. Selezionare il metodo HTTP GET nella scheda Builder (Generatore):

Di seguito sono riportate cinque richieste HTTP GET dell'API Recinto virtuale, con le diverse coordinate di posizione del macchinario in ordine cronologico. Ogni richiesta è seguita dal corpo della risposta.
 
1. Posizione 1:
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![Query recinto virtuale 1](./media/tutorial-geofence/geofence-query1.png)

   Nella risposta precedente, la distanza negativa dal recinto virtuale principale indica che il macchinario si trova all'interno del recinto virtuale. La distanza positiva dal recinto virtuale della sottoarea indica che il macchinario si trova al di fuori del recinto virtuale della sottoarea. 

2. Posizione 2: 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![Query recinto virtuale 2](./media/tutorial-geofence/geofence-query2.png)

   Se si osserva attentamente la risposta JSON precedente, il macchinario si trova all'esterno della sottoarea, ma all'interno del recinto principale. Non viene attivato alcun evento e non viene inviato alcun messaggio di posta elettronica.

3. Posizione 3: 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Query recinto virtuale 3](./media/tutorial-geofence/geofence-query3.png)

   Si è verificata una variazione di stato e ora il macchinario si trova all'interno del recinto virtuale principale e del recinto della sottoarea. Questa variazione genera la pubblicazione di un evento e verrà inviata una e-mail di notifica al direttore dei lavori.

4. Posizione 4: 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![Query recinto virtuale 4](./media/tutorial-geofence/geofence-query4.png)

   Se si osserva attentamente la risposta corrispondente, si può notare che non vengono pubblicati eventi anche se il macchinario è uscito dal recinto virtuale della sottoarea. Se si osserva l'ora specificata dall'utente nella richiesta GET, si può notare che il recinto virtuale del sito secondario è scaduta e il macchinario si trova ancora all'interno del recinto virtuale principale. È anche visibile l'ID geometria del recinto virtuale della sottoarea sotto `expiredGeofenceGeometryId` nel corpo della risposta.


5. Posizione 5:
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Query recinto virtuale 5](./media/tutorial-geofence/geofence-query5.png)

   Si può vedere che il macchinario è uscito dal recinto virtuale principale del cantiere. Viene pubblicato un evento e viene inviato un messaggio di posta elettronica di avviso al responsabile dei lavori.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come configurare un recinto virtuale caricandolo in Mappe di Azure e nel servizio dati con l'API Caricamento dati. Si è anche appreso come usare Griglia di eventi di Mappe di Azure per la sottoscrizione e la gestione di eventi di recinto virtuale. 

* Vedere [Gestire tipi di contenuto in App per la logica di Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type) per informazioni su come usare App per la logica per analizzare codice JSON e compilare una logica più complessa.
* Per altre informazioni sui gestori di eventi in Griglia di eventi, vedere [Gestori di eventi supportati in Griglia di eventi](https://docs.microsoft.com/azure/event-grid/event-handlers).
