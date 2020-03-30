---
title: Distribuire il gateway di gestione delle API di Azure self-hosted in Docker Documenti Microsoft
description: Informazioni su come distribuire un gateway di Gestione API di Azure self-hosted in Docker
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
ms.openlocfilehash: b5acda30f7f4a01e1b7b6ac82652452c3c40e3bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768504"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Distribuire un gateway self-hosted di Gestione API di Azure in DockerDeploy an Azure API Management self-hosted gateway to Docker

Questo articolo illustra i passaggi per la distribuzione del gateway di Gestione API di Azure self-hosted in un ambiente Docker.This article provides the steps for deploying self-hosted Azure API Management gateway into a Docker environment.

> [!NOTE]
> La funzionalità gateway self-hosted è in anteprima. Durante l'anteprima, il gateway self-hosted è disponibile solo nei livelli Developer e Premium senza costi aggiuntivi. Il livello sviluppatore è limitato a una singola distribuzione di gateway self-hosted.

## <a name="prerequisites"></a>Prerequisiti

- Completare la guida introduttiva seguente: [Creare un'istanza](get-started-create-service-instance.md) di Gestione API di AzureComplete the following quickstart: Create an Azure API Management instance
- Creare un ambiente Docker.Create a Docker environment. [Docker for Desktop](https://www.docker.com/products/docker-desktop) è una buona opzione per scopi di sviluppo e valutazione. Per informazioni su tutte le edizioni di Docker, le relative funzionalità e la documentazione completa su Docker stessa, vedere la documentazione di [Docker.](https://docs.docker.com)
- [Effettuare il provisioning di una risorsa gateway nell'istanza di Gestione APIProvision a gateway resource in your API Management instance](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> Il gateway self-hosted è incluso in un pacchetto Docker basato su Linux x86-64.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Distribuire il gateway self-hosted in Docker

1. Selezionare **Gateway** in **Impostazioni**.
2. Selezionare la risorsa gateway che si desidera distribuire.
3. Selezionare **Distribuzione**.
4. Si noti che un nuovo token nella casella di testo **Token** è stato generato automaticamente utilizzando i valori predefiniti **Di scadenza** e **Chiave segreta.** Regolare uno o entrambi, se lo si desidera, e selezionare **Genera** per creare un nuovo token.
4. Assicurarsi che **L'opzione Docker** sia selezionata in **Script di distribuzione**.
5. Selezionare **env.conf** file accanto al **ambiente** per scaricare il file.
6. Selezionare l'icona di **copia** situata all'estremità destra della casella di testo **Esegui** per salvare il comando Docker negli Appunti.
7. Incollare il comando nella finestra del terminale (o del comando). Modificare i mapping delle porte e il nome del contenitore in base alle esigenze. Si noti che il comando prevede che il file di ambiente scaricato sia presente nella directory corrente.
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. Eseguire il comando. Il comando indica all'ambiente Docker di eseguire il contenitore, utilizzando l'immagine del gateway self-hosted scaricata dal Registro di sistema del contenitore Microsoft, e di eseguire il mapping delle porte HTTP (8080) e HTTPS (8081) del contenitore alle porte 80 e 443 nell'host.
9. Eseguire il comando seguente per verificare che il pod del gateway sia in esecuzione:
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:beta   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. Tornare al portale di Azure e verificare che il nodo del gateway appena distribuito stia segnalando lo stato integro.

![stato del gateway](media/api-management-howto-deploy-self-hosted-gateway-to-docker/status.png)

> [!TIP]
> Utilizzare <code>console docker container logs <gateway-name></code> il comando per visualizzare uno snapshot del log del gateway indipendente.
>
> Utilizzare <code>docker container logs --help</code> il comando per visualizzare tutte le opzioni di visualizzazione dei registri.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sul gateway self-hosted, vedere Panoramica del gateway self-hosted di Gestione API di Azure.To learn more about the self-hosted gateway, see [Azure API Management self-hosted gateway overview](self-hosted-gateway-overview.md).
* [Configurare il nome di dominio personalizzato per il gateway self-hosted](api-management-howto-configure-custom-domain-gateway.md).
