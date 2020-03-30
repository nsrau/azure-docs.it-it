---
title: Persistenza dello stato in Linux - Griglia di eventi di Azure Edge Documenti Microsoft
description: Metadati persistenti in LinuxPersist metadata in Linux
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 12655d2ceb4a1124376d9bddf82194472c98ebb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086663"
---
# <a name="persist-state-in-linux"></a>Stato persistente in LinuxPersist state in Linux

Gli argomenti e le sottoscrizioni creati nel modulo Griglia di eventi vengono archiviati nel file system contenitore per impostazione predefinita. Senza persistenza, se il modulo viene ridistribuito, tutti i metadati creati andrebbero persi. Per mantenere i dati tra distribuzioni e riavvii, è necessario rendere persistenti i dati all'esterno del file system del contenitore.

Per impostazione predefinita, solo i metadati vengono mantenuti e gli eventi vengono comunque archiviati in memoria per migliorare le prestazioni. Seguire la sezione eventi persistenti per abilitare anche la persistenza degli eventi.

In questo articolo vengono illustrati i passaggi per distribuire il modulo Griglia di eventi con persistenza nelle distribuzioni Linux.This article provides the steps to deploy the Event Grid module with persistence in Linux deployments.

> [!NOTE]
>Il modulo Griglia di eventi viene eseguito `2000` come `eventgriduser`utente con privilegi limitati con UID e name .

## <a name="persistence-via-volume-mount"></a>Persistenza tramite montaggio del volume

 [I volumi Docker](https://docs.docker.com/storage/volumes/) vengono utilizzati per mantenere i dati tra le distribuzioni. È possibile consentire a Docker di creare automaticamente un volume denominato come parte della distribuzione del modulo Griglia di eventi. Questa opzione è l'opzione più semplice. È possibile specificare il nome del volume da creare nella sezione **Associazioni** come segue:

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
>Non modificare la seconda parte del valore di associazione. Punta a una posizione specifica all'interno del modulo. Per il modulo Griglia di eventi in Linux, deve essere **/app/metadataDb**.

Ad esempio, la configurazione seguente comporterà la creazione del volume **egmetadataDbVol** in cui i metadati verranno mantenuti.

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

Anziché montare un volume, è possibile creare una directory nel sistema host e montarla.

## <a name="persistence-via-host-directory-mount"></a>Persistenza tramite montaggio della directory host

Invece di un volume docker, hai anche la possibilità di montare una cartella host.

1. Creare innanzitutto un utente con nome **eventgriduser** e ID **2000** nel computer host eseguendo il comando seguente:

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. Creare una directory nel file system host eseguendo il comando seguente.

   ```sh
   md <your-directory-name-here>
   ```

    Ad esempio, l'esecuzione del comando seguente creerà una directory denominata **myhostdir**.

    ```sh
    md /myhostdir
    ```
1. Successivamente, rendere **eventgriduser** proprietario di questa cartella eseguendo il comando seguente.

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    Ad esempio,

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. Usare Binding per montare la directory e ridistribuire il modulo Griglia di eventi dal portale di Azure.Use **Binds** to mount the directory and redeploy the Event Grid module from Azure portal.

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
    >Non modificare la seconda parte del valore di associazione. Punta a una posizione specifica all'interno del modulo. Per il modulo Griglia di eventi su linux, deve essere **/app/metadataDb** e **/app/eventsDb**


## <a name="persist-events"></a>Eventi persistentiPersist events

Per abilitare la persistenza degli eventi, è innanzitutto necessario abilitare la persistenza dei metadati tramite il montaggio del volume o il montaggio della directory host utilizzando le sezioni precedenti.

Aspetti importanti da notare sugli eventi persistenti:Important things to note about persisting events:

* Gli eventi di persistenza sono abilitati per ogni sottoscrizione di eventi e vengono attivati un consenso esplicito dopo l'installazione di un volume o di una directory.
* La persistenza degli eventi viene configurata in una sottoscrizione di eventi in fase di creazione e non può essere modificata dopo la creazione della sottoscrizione di eventi. Per attivare/disattivare la persistenza degli eventi, è necessario eliminare e ricreare la sottoscrizione di eventi.
* La persistenza degli eventi è quasi sempre più lenta rispetto alle operazioni di memoria, tuttavia la differenza di velocità dipende fortemente dalle caratteristiche dell'unità. Il compromesso tra velocità e affidabilità è inerente a tutti i sistemi di messaggistica, ma in genere diventa solo un notevole su larga scala.

Per abilitare la persistenza `persistencePolicy` degli `true`eventi in una sottoscrizione di eventi, impostare su:

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
