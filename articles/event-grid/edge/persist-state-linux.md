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
ms.openlocfilehash: 3506399537fe2cb16014ceb3429bce5aeee8cb69
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73100344"
---
# <a name="persist-state-in-linux"></a>Mantieni stato in Linux

Gli argomenti e le sottoscrizioni create nel modulo di griglia di eventi vengono archiviati per impostazione predefinita nel contenitore file system. Senza persistenza, se il modulo viene ridistribuito, tutti i metadati creati andranno perduti. Attualmente solo i metadati sono salvati in permanenza. Gli eventi vengono archiviati in memoria. Se il modulo griglia di eventi viene ridistribuito o riavviato, eventuali eventi non recapitati andranno persi.

Questo articolo illustra i passaggi per distribuire il modulo di griglia di eventi con la persistenza nelle distribuzioni di Linux.

> [!NOTE]
>Il modulo di griglia di eventi viene eseguito come utente con privilegi limitati con `2000` UID e `eventgriduser`nome.

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
      "egmetadataDbVol:/app/metadataDb"
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

In alternativa, è possibile creare un volume Docker usando i comandi client di Docker. 

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
                "<your-directory-name-here>:/app/metadataDb"
             ]
         }
    }
    ```

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
                  "/myhostdir:/app/metadataDb"
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
    >Non modificare la seconda parte del valore di binding. Punta a una posizione specifica all'interno del modulo. Per il modulo di griglia di eventi in Linux è necessario **/app/Metadata**.
