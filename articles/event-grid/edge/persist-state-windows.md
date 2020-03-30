---
title: Rendere persistente lo stato in Windows - Azure Event Grid IoT Edge Documenti Microsoft
description: Stato persistente in WindowsPersist state in Windows
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: c2bae3bd268dba8efdf23ae314671b17a2c89420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086619"
---
# <a name="persist-state-in-windows"></a>Stato persistente in WindowsPersist state in Windows

Gli argomenti e le sottoscrizioni creati nel modulo Griglia di eventi vengono archiviati nel file system contenitore per impostazione predefinita. Senza persistenza, se il modulo viene ridistribuito, tutti i metadati creati andrebbero persi. Per mantenere i dati tra distribuzioni e riavvii, è necessario rendere persistenti i dati all'esterno del file system del contenitore. 

Per impostazione predefinita, solo i metadati vengono mantenuti e gli eventi vengono comunque archiviati in memoria per migliorare le prestazioni. Seguire la sezione eventi persistenti per abilitare anche la persistenza degli eventi.

In questo articolo vengono illustrati i passaggi necessari per distribuire il modulo Griglia di eventi con persistenza nelle distribuzioni di Windows.This article provides the steps needed to deploy Event Grid module with persistence in Windows deployments.

> [!NOTE]
>Il modulo Griglia di eventi viene eseguito come utente con privilegi limitati ContainerUser in Windows.The Event Grid module runs as a low-privileged user **ContainerUser** in Windows.

## <a name="persistence-via-volume-mount"></a>Persistenza tramite montaggio del volume

[I volumi Docker](https://docs.docker.com/storage/volumes/) vengono utilizzati per mantenere i dati tra le distribuzioni. Per montare un volume, è necessario crearlo utilizzando i comandi docker, concedere le autorizzazioni in modo che il contenitore possa leggere, scrivere e quindi distribuire il modulo.

1. Creare un volume eseguendo il comando seguente:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    Ad esempio,

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. Ottenere la directory host a cui viene eseguito il mapping del volume eseguendo il comando seguente

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    Ad esempio,

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   Output di esempio:-

   ```json
   [
          {
            "CreatedAt": "2019-07-30T21:20:59Z",
            "Driver": "local",
            "Labels": {},
            "Mountpoint": "C:\\ProgramData\\iotedge-moby\u000bolumes\\myeventgridvol\\_data",
            "Name": "myeventgridvol",
            "Options": {},
            "Scope": "local"
          }
   ]
   ```
1. Aggiungere il gruppo **Users** al valore indicato da **Mountpoint** come segue:
    1. Avviare Esplora file.
    1. Passare alla cartella indicata da **Mountpoint**.
    1. Fare clic con il pulsante destro del mouse e scegliere **Proprietà**.
    1. Selezionare **Sicurezza**.
    1. In : nomi di gruppo o di utenti selezionare **Modifica**.
    1. Selezionare **Aggiungi**, immettere `Users`, selezionare **Controlla nomi**e scegliere **OK**.
    1. In *Autorizzazioni per gli utenti*selezionare **Modifica**e quindi **OK**.
1. Usare Associa per montare questo volume e ridistribuire il modulo Griglia di eventi dal portale di AzureUse **Binds** to mount this volume and redeploy Event Grid module from Azure portal

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
                  "<your-volume-name-here>:C:\\app\\metadataDb"
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
   >Non modificare la seconda parte del valore di associazione. Punta a una posizione specifica nel modulo. Per il modulo Griglia di eventi nelle finestre, deve essere **C:\\\\app metadataDb**.


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
                "myeventgridvol:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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

## <a name="persistence-via-host-directory-mount"></a>Persistenza tramite montaggio della directory host

Anziché montare un volume, è possibile creare una directory nel sistema host e montarla.

1. Creare una directory nel file system host eseguendo il comando seguente.

   ```sh
   mkdir <your-directory-name-here>
   ```

   Ad esempio,

   ```sh
   mkdir C:\myhostdir
   ```
1. Usare Binding per montare la directory e ridistribuire il modulo Griglia di eventi dal portale di Azure.Use **Binds** to mount your directory and redeploy the Event Grid module from Azure portal.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:C:\\app\\metadataDb"
             ]
         }
    }
    ```

    >[!IMPORTANT]
    >Non modificare la seconda parte del valore di associazione. Punta a una posizione specifica nel modulo. Per il modulo Griglia di eventi nelle finestre, deve essere **C:\\\\app metadataDb**.

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
                "C:\\myhostdir:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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
## <a name="persist-events"></a>Eventi persistentiPersist events

Per abilitare la persistenza degli eventi, è innanzitutto necessario abilitare la persistenza degli eventi tramite il montaggio del volume o il montaggio della directory host utilizzando le sezioni precedenti.

Aspetti importanti da notare sugli eventi persistenti:Important things to note about persisting events:

* Gli eventi di persistenza sono abilitati per ogni sottoscrizione di eventi e vengono attivati un consenso esplicito dopo l'installazione di un volume o di una directory.
* La persistenza degli eventi viene configurata in una sottoscrizione di eventi in fase di creazione e non può essere modificata dopo la creazione della sottoscrizione di eventi. Per attivare/disattivare la persistenza degli eventi, è necessario eliminare e ricreare la sottoscrizione di eventi.
* La persistenza degli eventi è quasi sempre più lenta rispetto alle operazioni di memoria, tuttavia la differenza di velocità dipende fortemente dalle caratteristiche dell'unità. Il compromesso tra velocità e affidabilità è inerente a tutti i sistemi di messaggistica, ma diventa solo evidente su larga scala.

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