---
title: Distribuire il gateway di gestione API di Azure self-hosted in Docker | Microsoft Docs
description: Informazioni su come distribuire un gateway di gestione API di Azure self-hosted in Docker
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: fc7e0f7e4e0e80a030a437bc4f0f13360595f32e
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747700"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Distribuire un gateway self-hosted di gestione API di Azure a Docker

Questo articolo illustra la procedura per la distribuzione del gateway di gestione API di Azure self-hosted in un ambiente docker.

> [!NOTE]
> La funzionalità gateway self-hosted è in anteprima. Durante l'anteprima, il gateway self-hosted è disponibile solo nei livelli Developer e Premium senza costi aggiuntivi. Il livello Developer è limitato a una singola distribuzione del gateway self-hosted.

## <a name="prerequisites"></a>Prerequisiti

- Completare la guida introduttiva seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md)
- Creare un ambiente docker. [Docker per desktop](https://www.docker.com/products/docker-desktop) è un'opzione efficace per scopi di sviluppo e valutazione. Vedere la [documentazione di Docker](https://docs.docker.com) per informazioni su tutte le edizioni di Docker, sulle funzionalità e sulla documentazione completa su Docker.
- [Effettuare il provisioning di una risorsa gateway nell'istanza di gestione API](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> Il gateway self-hosted è incluso in un contenitore Docker basato su Linux.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Distribuire il gateway self-hosted in Docker

1. Selezionare **gateway** in **Impostazioni**.
2. Selezionare la risorsa del gateway che si intende distribuire.
3. Selezionare **distribuzione**.
4. Si noti che per l'utente è stato generato automaticamente un nuovo token nella casella di testo **token** usando i valori predefiniti per la **scadenza** e la **chiave privata** . Se lo si desidera, modificare uno o entrambi gli elementi e selezionare **genera** per creare un nuovo token.
4. Assicurarsi che **Docker** sia selezionato in **script di distribuzione**.
5. Selezionare il collegamento file **env. conf** accanto all' **ambiente** per scaricare il file.
6. Selezionare l'icona di **copia** che si trova all'estremità destra della casella di testo **Esegui** per salvare il comando Docker negli Appunti.
7. Incollare il comando nella finestra terminale (o comando). Modificare i mapping delle porte e il nome del contenitore in base alle esigenze. Si noti che il comando prevede che il file dell'ambiente scaricato sia presente nella directory corrente.
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. Eseguire il comando. Il comando indica all'ambiente Docker di eseguire il contenitore, usando l'immagine del gateway self-hosted scaricata da Microsoft Container Registry ed eseguire il mapping delle porte HTTP (8080) e HTTPS (8081) del contenitore alle porte 80 e 443 nell'host.
9. Eseguire il comando seguente per verificare che il pod del gateway sia in esecuzione:
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:beta   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. Tornare a portale di Azure e verificare che il nodo del gateway appena distribuito stia segnalando lo stato integro.

![stato del gateway](media/api-management-howto-deploy-self-hosted-gateway-to-docker/status.png)

> [!TIP]
> Usare <code>console docker container logs <gateway-name></code> comando per visualizzare uno snapshot del log del gateway self-hosted.
>
> Usare <code>docker container logs --help</code> comando per visualizzare tutte le opzioni di visualizzazione del log.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sul gateway self-hosted, vedere [Panoramica del gateway self-hosted di gestione API di Azure](self-hosted-gateway-overview.md).
* [Configurare il nome di dominio personalizzato per il gateway self-hosted](api-management-howto-configure-custom-domain-gateway.md).
