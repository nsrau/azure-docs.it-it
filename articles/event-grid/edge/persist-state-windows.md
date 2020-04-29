---
title: 'Stato di salvataggio permanente in Windows: griglia di eventi di Azure IoT Edge | Microsoft Docs'
description: Mantieni stato in Windows
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: c2bae3bd268dba8efdf23ae314671b17a2c89420
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77086619"
---
# <a name="persist-state-in-windows"></a>Mantieni stato in Windows

Gli argomenti e le sottoscrizioni create nel modulo di griglia di eventi vengono archiviati nel contenitore file system per impostazione predefinita. Senza persistenza, se il modulo viene ridistribuito, tutti i metadati creati andranno perduti. Per mantenere i dati tra le distribuzioni e i riavvii, è necessario salvare in modo permanente i dati all'esterno del contenitore file system. 

Per impostazione predefinita, solo i metadati sono persistenti e gli eventi rimangono archiviati in memoria per migliorare le prestazioni. Seguire la sezione relativa agli eventi di persistenza per abilitare la persistenza degli eventi.

Questo articolo illustra i passaggi necessari per distribuire il modulo di griglia di eventi con la persistenza nelle distribuzioni di Windows.

> [!NOTE]
>Il modulo di griglia di eventi viene eseguito come utente con privilegi limitati **ContainerUser** in Windows.

## <a name="persistence-via-volume-mount"></a>Persistenza tramite montaggio del volume

I [volumi Docker](https://docs.docker.com/storage/volumes/) vengono usati per mantenere i dati tra le distribuzioni. Per montare un volume, è necessario crearlo usando i comandi di Docker, concedere le autorizzazioni in modo che il contenitore possa leggere, scrivere su di esso e quindi distribuire il modulo.

1. Per creare un volume, eseguire il comando seguente:

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

   Esempio di output:-

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
1. Aggiungere il gruppo **Users** al valore a cui punta **mountpoint** come segue:
    1. Avviare Esplora file.
    1. Passare alla cartella a cui punta **mountpoint**.
    1. Fare clic con il pulsante destro del mouse su, quindi scegliere **Proprietà**.
    1. Selezionare **sicurezza**.
    1. In * nome gruppo o utente selezionare **modifica**.
    1. Selezionare **Aggiungi**, immettere `Users`, selezionare **Controlla nomi**e quindi fare clic su **OK**.
    1. In *autorizzazioni per utenti*selezionare **modifica**, quindi fare clic su **OK**.
1. Usare le **associazioni** per montare il volume e ridistribuire il modulo di griglia di eventi da portale di Azure

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
   >Non modificare la seconda parte del valore di binding. Punta a una posizione specifica nel modulo. Per il modulo di griglia di eventi in Windows, deve essere **C\\:\\app metadataDb**.


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

## <a name="persistence-via-host-directory-mount"></a>Persistenza tramite montaggio directory host

Anziché montare un volume, è possibile creare una directory nel sistema host e montare tale directory.

1. Creare una directory nel file System host eseguendo il comando seguente.

   ```sh
   mkdir <your-directory-name-here>
   ```

   Ad esempio,

   ```sh
   mkdir C:\myhostdir
   ```
1. Usare **Binding** per montare la directory e ridistribuire il modulo di griglia di eventi da portale di Azure.

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
    >Non modificare la seconda parte del valore di binding. Punta a una posizione specifica nel modulo. Per il modulo di griglia di eventi in Windows, deve essere **C:\\app\\metadataDb**.

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
## <a name="persist-events"></a>Mantieni eventi

Per abilitare la persistenza degli eventi, è necessario abilitare prima la persistenza degli eventi tramite il montaggio del volume o il montaggio della directory host usando le sezioni precedenti.

Aspetti importanti da notare sugli eventi di salvataggio permanente:

* Gli eventi di salvataggio permanente sono abilitati per ogni sottoscrizione di evento ed è il consenso esplicito dopo che è stato montato un volume o una directory.
* La persistenza degli eventi è configurata in una sottoscrizione di eventi in fase di creazione e non può essere modificata dopo la creazione della sottoscrizione di eventi. Per abilitare o disabilitare la persistenza degli eventi, è necessario eliminare e ricreare la sottoscrizione di eventi.
* Il mantenimento degli eventi è quasi sempre più lento rispetto alle operazioni di memoria, ma la differenza di velocità dipende in modo estremamente dalle caratteristiche dell'unità. Il compromesso tra velocità e affidabilità è inerente a tutti i sistemi di messaggistica, ma diventa notevolmente evidente su larga scala.

Per abilitare la persistenza degli eventi in una sottoscrizione `persistencePolicy` di `true`eventi, impostare su:

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