---
title: Creare un recinto virtuale con Mappe di Azure | Microsoft Docs
description: Configurare un recinto virtuale con Mappe di Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 112d0bd4b6802179692d0d177775027e552d1170
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58085321"
---
# <a name="set-up-a-geofence-by-using-azure-maps"></a>Configurare un recinto virtuale con Mappe di Azure

Questa esercitazione illustra i passaggi di base per la configurazione di un recinto virtuale con Mappe di Azure. Lo scenario esaminato in questa esercitazione consente ai direttori di cantiere di monitorare l'uscita di macchinari potenzialmente pericolosi all'esterno delle aree di cantiere designate. Un cantiere comporta macchinari costosi e il rispetto di normative specifiche. Richiede in genere che i macchinari rimangano all'interno del cantiere e non escano senza autorizzazione.

Si userà l'API Caricamento dati di Mappe di Azure per archiviare un recinto virtuale, quindi si userà l'API Recinto virtuale di Mappe di Azure per verificare la posizione dei macchinari rispetto al recinto virtuale. Si userà Griglia di eventi di Azure per trasmettere i risultati del recinto virtuale e configurare una notifica in base ai risultati del recinto virtuale stesso.
Per altre informazioni su Griglia di eventi, vedere [Griglia di eventi di Azure](https://docs.microsoft.com/azure/event-grid/overview).
In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Caricare l'area del recinto virtuale nel servizio dati di Mappe di Azure usando l'API Caricamento dati.
> *   Configurare una griglia di eventi per gestire gli eventi del recinto virtuale.
> *   Impostare il gestore di eventi del recinto virtuale.
> *   Configurare gli avvisi in risposta agli eventi del recinto virtuale usando App per la logica.
> *   Usare le API del servizio del recinto virtuale di Mappe di Azure per verificare se un asset di costruzione si trova nel cantiere o meno.


## <a name="prerequisites"></a>Prerequisiti

### <a name="create-an-azure-maps-account"></a>Creare un account di Mappe di Azure 

Per completare i passaggi di questa esercitazione è prima necessario vedere [Gestire l'account e le chiavi](how-to-manage-account-keys.md) per creare e gestire la sottoscrizione dell'account con il piano tariffario S1.

## <a name="upload-geofences"></a>Caricare i recinti virtuali

Per caricare il recinto virtuale per il cantiere con l'API Caricamento dati si userà l'applicazione Postman. Ai fini di questa esercitazione si presuppone che ci sia un'area di cantiere generale che rappresenta un parametro fisso che i macchinari edili non devono violare. La violazione di questo recinto rappresenta un'infrazione grave che viene segnalata al direttore di cantiere. È possibile usare un set ottimizzato di recinti aggiuntivi per tenere traccia delle diverse aree del cantiere generale in base al piano lavori. Si può presupporre che il recinto virtuale principale abbia una sottoarea subsite1 con una data di scadenza definita. È possibile creare più recinti virtuali annidati a seconda delle esigenze. È ad esempio possibile che la sottoarea subsite1 sia il luogo in cui si svolgono i lavori durante le settimane 1-4 del piano lavori e la sottoarea subsite2 sia il luogo in cui si svolgono i lavori durante le settimane 5-7. Tutti questi recinti possono essere caricati come singolo set di dati all'inizio del progetto e usati per tenere traccia delle regole nel tempo e nello spazio. Per altre informazioni sul formato dati del recinto virtuale, vedere [Dati GeoJSON del recinto virtuale](https://docs.microsoft.com/azure/azure-maps/geofence-geojson). Per altre informazioni sul caricamento dei dati nel servizio Mappe di Azure, vedere [Documentazione dell'API Caricamento dati](https://docs.microsoft.com/rest/api/maps/data/uploadpreview).

Aprire l'app Postman e seguire questi passaggi per caricare il recinto virtuale del cantiere usando Mappe di Azure e l'API Caricamento dati.

1. Aprire l'app Postman e fare clic su New | Create new (Nuovo | Crea nuovo) e selezionare Request (Richiesta). Immettere un nome richiesta per Upload geofence data (Carica dati recinto virtuale), selezionare una raccolta o una cartella per il salvataggio, quindi fare clic su Salva (Save).

    ![Caricare i recinti virtuali usando Postman](./media/tutorial-geofence/postman-new.png)

2. Selezionare il metodo HTTP POST nella scheda Builder (Generatore) e immettere l'URL seguente per effettuare una richiesta POST.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    Il parametro GEOJSON nel percorso URL rappresenta il formato dei dati caricati.

3. Fare clic su **Params** (Parametri) e immettere le coppie chiave/valore seguenti da usare per l'URL della richiesta POST. Sostituire il valore subscription-key con la chiave di sottoscrizione di Mappe di Azure.
   
    ![Parametri chiave-valore di Postman](./media/tutorial-geofence/postman-key-vals.png)

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

5. Fare clic su Send (Invia) e quindi esaminare l'intestazione della risposta. L'intestazione di posizione contiene l'URI per accedere o scaricare i dati per un uso futuro. Include anche un valore `udId` univoco per i dati caricati.

   ```HTTP
   https://atlas.microsoft.com/mapData/{udId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

## <a name="set-up-an-event-handler"></a>Configurare un gestore di eventi

Per informare il direttore di cantiere in merito agli eventi di ingresso e uscita è necessario creare un gestore di eventi che riceva le notifiche.

Verranno creati due servizi di [App per la logica](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) per gestire gli eventi di ingresso e uscita. In App per la logica verranno anche creati trigger di evento attivati da questi eventi. L'obiettivo è inviare avvisi, in questo caso messaggi di posta elettronica, al direttore di cantiere ogni volta che i macchinari entrano o escono dal cantiere. La figura seguente illustra la creazione di un'app per la logica per l'evento di ingresso nel recinto virtuale. È possibile crearne un'altra allo stesso modo per l'evento di uscita.
È possibile vedere tutti i [gestori di eventi supportati](https://docs.microsoft.com/azure/event-grid/event-handlers) per altre informazioni.

1. Creare un'app per la logica nel portale di Azure

   ![Creare app per la logica](./media/tutorial-geofence/logic-app.png)

2. Selezionare un trigger di richiesta HTTP e quindi selezionare "Send and email" (Invia una e-mail) come azione nel connettore di Outlook
  
   ![Schema di App per la logica](./media/tutorial-geofence/logic-app-schema.png)

3. Salvare l'app per la logica per generare l'endpoint dell'URL HTTP e copiare l'URL HTTP.

   ![Endpoint di App per la logica](./media/tutorial-geofence/logic-app-endpoint.png)


## <a name="create-an-azure-maps-events-subscription"></a>Creare una sottoscrizione di Eventi di Mappe di Azure

Mappe di Azure supporta tre tipi di eventi. I tipi di eventi supportati da Mappe di Azure sono disponibili [qui](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps). Si creeranno due sottoscrizioni diverse, una per gli eventi di ingresso e l'altra per gli eventi di uscita.

Seguire questi passaggi per creare una sottoscrizione per gli eventi di ingresso del recinto virtuale. Allo stesso modo è possibile creare una sottoscrizione per gli eventi di uscita del recinto virtuale.

1. Passare al proprio account di Mappe di Azure tramite [questo collegamento al portale](https://ms.portal.azure.com/#@microsoft.onmicrosoft.com/dashboard/) e selezionare la scheda Eventi.

   ![Eventi di Mappe di Azure](./media/tutorial-geofence/events-tab.png)

2. Per creare una sottoscrizione per gli eventi, selezionare Sottoscrizione di eventi dalla pagina degli eventi.

   ![Sottoscrizione per Eventi di Mappe di Azure](./media/tutorial-geofence/create-event-subscription.png)

3. Assegnare un nome alla sottoscrizione e selezionare il tipo di eventi di ingresso. Selezionare ora Web Hook come "Tipo di endpoint" e copiare l'endpoint dell'URL HTTP dell'app per la logica in "Endpoint"

   ![Sottoscrizione per gli eventi](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>Usare l'API Recinto virtuale

È possibile usare l'API Recinto virtuale per verificare se un **dispositivo** (i macchinari fanno parte dello stato) si trova all'interno o all'esterno di un recinto virtuale. Per comprendere meglio l'API GET Recinto virtuale, verranno eseguite query per i diversi luoghi in cui è transitato un macchinario specifico. La figura seguente illustra cinque posizioni di un particolare macchinario edile con un **ID dispositivo** univoco in ordine cronologico. Ognuna di queste cinque posizioni viene usata per valutare la variazione di stato di ingresso e uscita rispetto al recinto virtuale. Se si verifica una variazione di stato, il servizio del recinto virtuale attiva un evento che viene inviato all'app per la logica dalla griglia di eventi. Di conseguenza, il direttore di cantiere riceverà la notifica di ingresso o uscita corrispondente tramite una e-mail.

> [!Note]
> Lo scenario e il comportamento descritti in precedenza si basano sullo stesso **ID dispositivo**, quindi riflettono le cinque posizioni come nella figura seguente.

![Mappa del recinto virtuale](./media/tutorial-geofence/geofence.png)

Nell'app Postman aprire una nuova scheda nella stessa raccolta creata in precedenza. Selezionare il metodo HTTP GET nella scheda Builder (Generatore):

Di seguito sono riportate cinque richieste HTTP GET dell'API Geofencing, con le diverse coordinate di posizione dei macchinari in ordine cronologico. Ogni richiesta è seguita dal corpo della risposta.
 
1. Posizione 1:
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![Query recinto virtuale 1](./media/tutorial-geofence/geofence-query1.png)

   Se si osserva la risposta precedente, la distanza negativa rispetto al recinto virtuale principale indica che il macchinario si trova all'interno del recinto virtuale, mentre la differenza positiva rispetto al recinto virtuale della sottoarea indica che il macchinario si trova all'esterno del recinto virtuale della sottoarea. 

2. Posizione 2: 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![Query recinto virtuale 2](./media/tutorial-geofence/geofence-query2.png)

   Se si osserva attentamente la risposta JSON precedente, il macchinario si trova all'esterno della sottoarea, ma all'interno del recinto principale. Non vengono attivati eventi e non vengono inviate e-mail.

3. Posizione 3: 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Query recinto virtuale 3](./media/tutorial-geofence/geofence-query3.png)

   Si è verificata una variazione di stato e ora il macchinario si trova all'interno del recinto virtuale principale e del recinto della sottoarea. Verrà pubblicato un evento e verrà inviata una e-mail di notifica al direttore di cantiere.

4. Posizione 4: 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![Query recinto virtuale 4](./media/tutorial-geofence/geofence-query4.png)

   Se si osserva attentamente la risposta corrispondente, si può notare che non vengono pubblicati eventi anche se il macchinario è uscito dal recinto virtuale della sottoarea. Se si osserva il tempo specificato dall'utente nella richiesta GET, si può notare che il recinto virtuale della sottoarea è scaduto e il macchinario si trova ancora all'interno del recinto virtuale principale. È anche visibile l'ID geometria del recinto virtuale della sottoarea sotto `expiredGeofenceGeometryId` nel corpo della risposta.


5. Posizione 5:
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Query recinto virtuale 5](./media/tutorial-geofence/geofence-query5.png)

   Si può vedere che il macchinario è uscito dal recinto virtuale principale del cantiere. Verrà pubblicato un evento in quanto si tratta di una grave violazione e verrà inviata una e-mail di avviso critico al direttore del cantiere.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come configurare un recinto virtuale caricandolo nel servizio dati di Mappe di Azure con l'API Caricamento dati. Si è anche appreso come usare Griglia di eventi di Mappe di Azure per la sottoscrizione e la gestione di eventi di recinto virtuale. 

* Vedere [Gestire tipi di contenuto in App per la logica di Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type) per informazioni su come usare App per la logica per analizzare codice JSON e compilare una logica più complessa.
* Per altre informazioni sui gestori di eventi in Griglia di eventi, vedere [Gestori di eventi supportati in Griglia di eventi](https://docs.microsoft.com/azure/event-grid/event-handlers).
