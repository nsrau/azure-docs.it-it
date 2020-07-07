---
title: Distribuire il gateway self-hosted in Docker | Microsoft Docs
description: Informazioni su come distribuire un componente gateway self-hosted di gestione API di Azure in Docker
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: b9e990988770e8aca015ae8b1159bb4f5e50df57
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82205092"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Distribuire un gateway self-hosted di gestione API di Azure a Docker

Questo articolo illustra la procedura per la distribuzione del componente gateway self-hosted di gestione API di Azure in un ambiente docker.

> [!NOTE]
> L'hosting del gateway self-hosted in Docker è ideale per i casi d'uso di valutazione e sviluppo. Kubernetes è consigliato per l'uso in ambiente di produzione. Vedere [questo](how-to-deploy-self-hosted-gateway-kubernetes.md) documento per informazioni su come distribuire un gateway self-hosted in Kubernetes.

## <a name="prerequisites"></a>Prerequisiti

- Completare l'avvio rapido seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md)
- Creare un ambiente docker. [Docker per desktop](https://www.docker.com/products/docker-desktop) è un'opzione efficace per scopi di sviluppo e valutazione. Vedere la [documentazione di Docker](https://docs.docker.com) per informazioni su tutte le edizioni di Docker, sulle funzionalità e sulla documentazione completa su Docker.
- [Effettuare il provisioning di una risorsa gateway nell'istanza di gestione API](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> Il gateway self-hosted è incluso in un contenitore Docker basato su Linux x86-64.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Distribuire il gateway self-hosted in Docker

1. Selezionare **gateway** da **distribuzione e infrastruttura**.
2. Selezionare la risorsa del gateway che si intende distribuire.
3. Selezionare **distribuzione**.
4. Si noti che un token di accesso nella casella di testo **token** è stato generato automaticamente usando i valori predefiniti per la **scadenza** e la **chiave privata** . Se necessario, selezionare i valori desiderati in uno o entrambi i controlli per generare un nuovo token.
4. Assicurarsi che **Docker** sia selezionato in **script di distribuzione**.
5. Selezionare il collegamento file **env. conf** accanto all' **ambiente** per scaricare il file.
6. Selezionare l'icona di **copia** che si trova all'estremità destra della casella di testo **Esegui** per copiare il comando Docker negli Appunti.
7. Incollare il comando nella finestra terminale (o comando). Modificare i mapping delle porte e il nome del contenitore in base alle esigenze. Si noti che il comando presuppone che il file dell'ambiente scaricato sia presente nella directory corrente.
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. Eseguire il comando. Il comando indica all'ambiente Docker di eseguire il contenitore usando l' [immagine del contenitore](https://aka.ms/apim/sputnik/dhub) scaricata da Microsoft container Registry ed eseguire il mapping delle porte HTTP (8080) e HTTPS (8081) del contenitore alle porte 80 e 443 nell'host.
9. Eseguire il comando seguente per verificare se il contenitore del gateway è in esecuzione:
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:latest   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. Tornare a portale di Azure, fare clic su **Panoramica** e verificare che il contenitore del gateway self-hosted appena distribuito stia segnalando uno stato integro.

![stato del gateway](media/how-to-deploy-self-hosted-gateway-docker/status.png)

> [!TIP]
> Usare <code>console docker container logs <gateway-name></code> il comando per visualizzare uno snapshot del log del gateway self-hosted.
>
> Usare il <code>docker container logs --help</code> comando per visualizzare tutte le opzioni di visualizzazione del log.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sul gateway self-hosted, vedere [Panoramica del gateway self-hosted di gestione API di Azure](self-hosted-gateway-overview.md).
* [Configurare il nome di dominio personalizzato per il gateway self-hosted](api-management-howto-configure-custom-domain-gateway.md).
