---
title: Gestire cache di Azure per Redis con l'interfaccia della riga di comando di Azure
description: "Esempi dell'interfaccia della riga di comando di Azure per la gestione della cache di Azure per redis: creare una cache, eliminare una cache, ottenere i dettagli della cache, il nome host, le porte e le chiavi e connettere un'app Web"
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/14/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 32530982bc2a9d1b5deb31b3bc71460462352258
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92536420"
---
# <a name="manage-azure-cache-for-redis-with-azure-cli"></a>Gestire cache di Azure per Redis con l'interfaccia della riga di comando di Azure

La tabella seguente include collegamenti a script Bash compilati tramite l'interfaccia della riga di comando di Azure.

| Create cache | Descrizione |
| ------------ | ----------- |
| [Creare una cache](./scripts/create-cache.md) | Crea un gruppo di risorse e un'istanza di Cache Redis di Azure di base. |
| [Creare una cache premium con il clustering](./scripts/create-premium-cache-cluster.md) | Crea un gruppo di risorse e una cache premium con clustering abilitato.|
| [Ottenere i dettagli della cache](./scripts/show-cache.md) | Ottiene i dettagli di un'istanza di Cache Redis di Azure, incluso lo stato del provisioning. |
| [Ottenere il nome host, le porte e le chiavi](./scripts/cache-keys-ports.md) | Ottiene il nome host, le porte e le chiavi per un'istanza di Cache Redis di Azure. |
|**App Web e cache**| **Descrizione**|
| [Connettere un'app Web a Cache Redis di Azure](./../app-service/scripts/cli-connect-to-redis.md) | Crea un'app Web di Azure e un'istanza di Cache Redis di Azure, quindi aggiunge i dettagli della connessione Redis alle impostazioni dell'app. |
|**Elimina cache**| **Descrizione** |
| [Eliminare una cache](./scripts/delete-cache.md) | Elimina un'istanza di Cache Redis di Azure  |

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) e [Introduzione all'interfaccia della riga di comando di Azure](/cli/azure/get-started-with-azure-cli).