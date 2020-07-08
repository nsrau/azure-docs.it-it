---
title: 'Stato di salvataggio permanente in Linux: griglia di eventi di Azure IoT Edge | Microsoft Docs'
description: Mantieni i metadati in Linux
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 12655d2ceb4a1124376d9bddf82194472c98ebb9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77086663"
---
# <a name="persist-state-in-linux"></a>Mantieni stato in Linux

Gli argomenti e le sottoscrizioni create nel modulo di griglia di eventi vengono archiviati nel contenitore file system per impostazione predefinita. Senza persistenza, se il modulo viene ridistribuito, tutti i metadati creati andranno perduti. Per mantenere i dati tra le distribuzioni e i riavvii, è necessario salvare in modo permanente i dati all'esterno del contenitore file system.

Per impostazione predefinita, solo i metadati sono persistenti e gli eventi rimangono archiviati in memoria per migliorare le prestazioni. Seguire la sezione relativa agli eventi di persistenza per abilitare la persistenza degli eventi.

Questo articolo illustra i passaggi per distribuire il modulo di griglia di eventi con la persistenza nelle distribuzioni di Linux.

> [!NOTE]
>Il modulo di griglia di eventi viene eseguito come utente con privilegi limitati con UID `2000` e nome `eventgriduser` .

## <a name="persistence-via-volume-mount"></a>Persistenza tramite montaggio del volume

 I [volumi Docker](https://docs.docker.com/storage/volumes/) vengono usati per mantenere i dati tra le distribuzioni. È possibile consentire a Docker di creare automaticamente un volume denominato nell'ambito della distribuzione del modulo di griglia di eventi. Questa opzione è l'opzione più semplice. È possibile specificare il nome del volume da creare nella sezione **binds** come indicato di seguito:

```json
  {
     "HostConfig": {
          "Binds": [
                 "<your-volume-name-here>:/app/metadataDb"
           ]
      }
   }
```

>[!IMPORTANT]
>Non modificare la seconda parte del valore di binding. Punta a una posizione specifica all'interno del modulo. Per il modulo di griglia di eventi in Linux è necessario **/app/metadataDb**.

La configurazione seguente, ad esempio, comporterà la creazione del volume **egmetadataDbVol** in cui i metadati verranno resi permanente.

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ],
  "HostConfig": {
    "Binds": [
      "egmetadataDbVol:/app/metadataDb",
      "egdataDbVol:/app/eventsDb"
    ],
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

Anziché montare un volume, è possibile creare una directory nel sistema host e montare tale directory.

## <a name="persistence-via-host-directory-mount"></a>Persistenza tramite montaggio directory host

Anziché un volume Docker, è anche possibile montare una cartella host.

1. Per prima cosa, creare un utente con nome **eventgriduser** e ID **2000** nel computer host eseguendo il comando seguente:

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. Creare una directory nell'host file system eseguendo il comando seguente.

   ```sh
   md <your-directory-name-here>
   ```

    Ad esempio, se si esegue il comando seguente, viene creata una directory denominata **myhostdir**.

    ```sh
    md /myhostdir
    ```
1. Successivamente, rendere il proprietario di **eventgriduser** di questa cartella eseguendo il comando seguente.

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    Ad esempio,

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. Usare **Binding** per montare la directory e ridistribuire il modulo di griglia di eventi da portale di Azure.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb",
                "<your-directory-name-here>:/app/eventsDb",
             ]
         }
    }
    ```

    Ad esempio,

    ```json
    {
          "Env": [
            "inbound__serverAuth__tlsPolicy=strict",
            "inbound__serverAuth__serverCert__source=IoTEdge",
            "inbound__clientAuth__sasKeys__enabled=false",
            "inbound__clientAuth__clientCert__enabled=true",
            "inbound__clientAuth__clientCert__source=IoTEdge",
            "inbound__clientAuth__clientCert__allowUnknownCA=true",
            "outbound__clientAuth__clientCert__enabled=true",
            "outbound__clientAuth__clientCert__source=IoTEdge",
            "outbound__webhook__httpsOnly=true",
            "outbound__webhook__skipServerCertValidation=false",
            "outbound__webhook__allowUnknownCA=true"
          ],
          "HostConfig": {
                "Binds": [
                  "/myhostdir:/app/metadataDb",
                  "/myhostdir2:/app/eventsDb"
                ],
                "PortBindings": {
                      "4438/tcp": [
                        {
                          "HostPort": "4438"
                        }
                      ]
                }
          }
    }
    ```

    >[!IMPORTANT]
    >Non modificare la seconda parte del valore di binding. Punta a una posizione specifica all'interno del modulo. Per il modulo di griglia di eventi in Linux, deve essere **/app/metadataDb** e **/app/eventsDb**


## <a name="persist-events"></a>Mantieni eventi

Per abilitare la persistenza degli eventi, è necessario abilitare prima la persistenza dei metadati tramite il montaggio del volume o il montaggio della directory host usando le sezioni precedenti.

Aspetti importanti da notare sugli eventi di salvataggio permanente:

* Gli eventi di salvataggio permanente sono abilitati per ogni sottoscrizione di evento ed è il consenso esplicito dopo che è stato montato un volume o una directory.
* La persistenza degli eventi è configurata in una sottoscrizione di eventi in fase di creazione e non può essere modificata dopo la creazione della sottoscrizione di eventi. Per abilitare o disabilitare la persistenza degli eventi, è necessario eliminare e ricreare la sottoscrizione di eventi.
* Il mantenimento degli eventi è quasi sempre più lento rispetto alle operazioni di memoria, ma la differenza di velocità dipende in modo estremamente dalle caratteristiche dell'unità. Il compromesso tra velocità e affidabilità è inerente a tutti i sistemi di messaggistica, ma in genere si limita a essere visibile su larga scala.

Per abilitare la persistenza degli eventi in una sottoscrizione di eventi, impostare su `persistencePolicy` `true` :

 ```json
        {
          "properties": {
            "persistencePolicy": {
              "isPersisted": "true"
            },
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-url>"
              }
            }
          }
        }
 ```
