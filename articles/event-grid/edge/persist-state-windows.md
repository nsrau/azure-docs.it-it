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
ms.openlocfilehash: 485c6d4a92539a2ba67aece319c68d31649e8045
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992262"
---
# <a name="persist-state-in-windows"></a>Mantieni stato in Windows

Gli argomenti e le sottoscrizioni create nel modulo di griglia di eventi vengono archiviati per impostazione predefinita nel contenitore file system. Senza persistenza, se il modulo viene ridistribuito, tutti i metadati creati andranno perduti. Per mantenere i dati tra le distribuzioni, è necessario salvare in modo permanente i dati all'esterno del contenitore file system. Attualmente, vengono mantenuti solo i metadati. Gli eventi vengono archiviati in memoria. Se il modulo griglia di eventi viene ridistribuito o riavviato, eventuali eventi non recapitati andranno persi.

Questo articolo illustra i passaggi necessari per distribuire il modulo di griglia di eventi con la persistenza nelle distribuzioni di Windows.

> [!NOTE]
>Il modulo di griglia di eventi viene eseguito come utente con privilegi limitati **ContainerUser** in Windows.

## <a name="persistence-via-volume-mount"></a>Persistenza tramite montaggio del volume

I [volumi Docker](https://docs.docker.com/storage/volumes/) vengono usati per mantenere i dati tra le distribuzioni. Per montare un volume, è necessario crearlo usando i comandi di Docker, concedere le autorizzazioni in modo che il contenitore possa leggere, scrivere su di esso e quindi distribuire il modulo. Non è previsto alcun provisioning per la creazione automatica di un volume con le autorizzazioni necessarie per Windows. Deve essere creato prima della distribuzione.

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
    1. Scegliere **Sicurezza**.
    1. In * nome gruppo o utente selezionare **modifica**.
    1. Selezionare **Aggiungi**, immettere `Users`, selezionare **Controlla nomi**e quindi fare clic su **OK**.
    1. In *autorizzazioni per utenti*selezionare **modifica**, quindi fare clic su **OK**.
1. Usare le **associazioni** per montare il volume e ridistribuire il modulo di griglia di eventi da portale di Azure

   Ad esempio,

    ```json
        {
              "Env": [
                "inbound:serverAuth:tlsPolicy=strict",
                "inbound:serverAuth:serverCert:source=IoTEdge",
                "inbound:clientAuth:sasKeys:enabled=false",
                "inbound:clientAuth:clientCert:enabled=true",
                "inbound:clientAuth:clientCert:source=IoTEdge",
                "inbound:clientAuth:clientCert:allowUnknownCA=true",
                "outbound:clientAuth:clientCert:enabled=true",
                "outbound:clientAuth:clientCert:source=IoTEdge",
                "outbound:webhook:httpsOnly=true",
                "outbound:webhook:skipServerCertValidation=false",
                "outbound:webhook:allowUnknownCA=true"
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
   >Non modificare la seconda parte del valore di binding. Punta a una posizione specifica nel modulo. Per il modulo di griglia di eventi in Windows, deve essere **C:\\app\\metadataDb**.


    Ad esempio,

    ```json
    {
        "Env": [
            "inbound:serverAuth:tlsPolicy=strict",
            "inbound:serverAuth:serverCert:source=IoTEdge",
            "inbound:clientAuth:sasKeys:enabled=false",
            "inbound:clientAuth:clientCert:enabled=true",
            "inbound:clientAuth:clientCert:source=IoTEdge",
            "inbound:clientAuth:clientCert:allowUnknownCA=true",
            "outbound:clientAuth:clientCert:enabled=true",
            "outbound:clientAuth:clientCert:source=IoTEdge",
            "outbound:webhook:httpsOnly=true",
            "outbound:webhook:skipServerCertValidation=false",
            "outbound:webhook:allowUnknownCA=true"
         ],
         "HostConfig": {
            "Binds": [
                "myeventgridvol:C:\\app\\metadataDb"
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

In alternativa, è possibile scegliere di creare una directory sul sistema host e montare tale directory.

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
            "inbound:serverAuth:tlsPolicy=strict",
            "inbound:serverAuth:serverCert:source=IoTEdge",
            "inbound:clientAuth:sasKeys:enabled=false",
            "inbound:clientAuth:clientCert:enabled=true",
            "inbound:clientAuth:clientCert:source=IoTEdge",
            "inbound:clientAuth:clientCert:allowUnknownCA=true",
            "outbound:clientAuth:clientCert:enabled=true",
            "outbound:clientAuth:clientCert:source=IoTEdge",
            "outbound:webhook:httpsOnly=true",
            "outbound:webhook:skipServerCertValidation=false",
            "outbound:webhook:allowUnknownCA=true"
         ],
         "HostConfig": {
            "Binds": [
                "C:\\myhostdir:C:\\app\\metadataDb"
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
