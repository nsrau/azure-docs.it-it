---
title: Risoluzione dei problemi - Azure Event Grid IoT Edge Documenti Microsoft
description: Risoluzione dei problemi in Griglia di eventi su Edge Edge.Troubleshooting in Event Grid on IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/24/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 95181d0eb23d5956b2c6af52c77f85714b107345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73100152"
---
# <a name="common-issues"></a>Problemi comuni

Se si verificano problemi con Griglia di eventi di Azure in IoT Edge nell'ambiente, usare questo articolo come guida per la risoluzione dei problemi e la risoluzione.

## <a name="view-event-grid-module-logs"></a>Visualizzare i registri dei moduli Griglia di eventi

Per risolvere i problemi, potrebbe essere necessario accedere ai registri dei moduli Griglia di eventi. A tale scopo, nella macchina virtuale in cui è distribuito il modulo eseguire il comando seguente:To do this, on the VM where the module is deployed run the following command:

In Windows,

```sh
docker -H npipe:////./pipe/iotedge_moby_engine container logs eventgridmodule
```

Su Linux,

```sh
sudo docker logs eventgridmodule
```

## <a name="unable-to-make-https-requests"></a>Impossibile effettuare richieste HTTPS

* Verificare innanzitutto che il modulo Griglia di eventi sia **inbound:serverAuth:tlsPolicy** impostato su **strict** o **enabled**.

* Se le comunicazioni da modulo a modulo, assicurarsi che si sta effettuando la chiamata sulla porta **4438** e il nome del modulo corrisponde a ciò che viene distribuito. 

  Ad esempio, se il modulo Griglia di eventi è stato distribuito con nome **eventgridmodule,** l'URL deve essere **https://eventgridmodule:4438**. Assicurarsi che l'uso dell'involucro e il numero di porta siano corretti.
    
* Se proviene da un modulo non IoT, assicurarsi che la porta Griglia di eventi sia mappata al computer host durante la distribuzione, ad esempio,

    ```json
    "HostConfig": {
                "PortBindings": {
                  "4438/tcp": [
                    {
                        "HostPort": "4438"
                    }
                  ]
                }
     }
    ```

## <a name="unable-to-make-http-requests"></a>Impossibile effettuare richieste HTTP

* Verificare innanzitutto che il modulo Griglia di eventi sia **iningresso:serverAuth:tlsPolicy** impostato su **enabled** o **disabled**.

* Se le comunicazioni da modulo a modulo, assicurarsi che si sta effettuando la chiamata sulla porta **5888** e il nome del modulo corrisponde a ciò che viene distribuito. 

  Ad esempio, se il modulo Griglia di eventi è stato distribuito con nome **eventgridmodule,** l'URL deve essere **http://eventgridmodule:5888**. Assicurarsi che l'uso dell'involucro e il numero di porta siano corretti.
    
* Se proviene da un modulo non IoT, assicurarsi che la porta Griglia di eventi sia mappata al computer host durante la distribuzione, ad esempio,

    ```json
    "HostConfig": {
                "PortBindings": {
                  "5888/tcp": [
                    {
                        "HostPort": "5888"
                    }
                  ]
                }
    }
    ```

## <a name="certificate-chain-was-issued-by-an-authority-thats-not-trusted"></a>La catena di certificati è stata emessa da un'autorità non attendibile

Per impostazione predefinita, il modulo Griglia di eventi è configurato per autenticare i client con il certificato emesso dal daemon di sicurezza IoT Edge.By default, Event Grid module is configured to authenticate clients with certificate issued by the IoT Edge security daemon. Assicurarsi che il client stia presentando un certificato radicato a questa catena.

La classe **IoTSecurity** in [https://github.com/Azure/event-grid-iot-edge](https://github.com/Azure/event-grid-iot-edge) mostra come recuperare i certificati dal daemon IoT Edge Security e usarli per configurare le chiamate in uscita.

Se si tratta di un ambiente non di produzione, è possibile disattivare l'autenticazione client. Fare riferimento a [Sicurezza e autenticazione](security-authentication.md) per informazioni dettagliate su come eseguire questa operazione.

## <a name="debug-events-not-received-by-subscriber"></a>Eventi di debug non ricevuti dal server di sottoscrizioneDebug Events not received by subscriber

Motivi tipici per questo sono:

* L'evento non è mai stato pubblicato con successo. Un StatusCode HTTP pari a 200(OK) deve essere ricevuto durante la pubblicazione di un evento nel modulo Griglia di eventi.

* Controllare la sottoscrizione di eventi per verificare:
    * L'URL dell'endpoint è valido
    * Eventuali filtri nella sottoscrizione non causano l'eliminazione dell'evento.

* Verificare se il modulo del sottoscrittore è in esecuzione

* Accedere alla macchina virtuale in cui è distribuito il modulo Griglia di eventi e visualizzarne i log.

* Attivare la registrazione per recapito impostando **broker:logDeliverySuccess, true** e ridistribuendo il modulo Griglia di eventi e ritentando la richiesta. L'attivazione della registrazione per recapito può influire sulla velocità effettiva e sulla latenza, pertanto una volta completato il debug, è consigliabile riportare il problema in **broker:logDeliverySuccess, false** e ridistribuire il modulo Griglia di eventi.

* Attiva le metriche impostando **le metriche:reportertype-console** e ridistribuisci il modulo Griglia di eventi. Tutte le operazioni successive determineranno la registrazione delle metriche nella console del modulo Griglia di eventi, che può essere utilizzata per eseguire ulteriormente il debug. È consigliabile attivare le metriche solo per il debug e una volta completate per disattivarle impostando **metrics:reportertype-none** e ridistribuendo il modulo Griglia di eventi.

## <a name="next-steps"></a>Passaggi successivi

Segnalare eventuali problemi, suggerimenti sull'utilizzo di [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues)Griglia di eventi in IoT Edge all'indirizzo .