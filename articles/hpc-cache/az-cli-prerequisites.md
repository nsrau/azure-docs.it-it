---
title: Prerequisiti dell'interfaccia della riga di comando di Azure per cache HPC di Azure
description: Procedura di installazione prima di poter usare l'interfaccia della riga di comando di Azure per creare o modificare una cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 13f45c96a830110bd0f4a2d4a2b422921d7a2e31
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654457"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Configurare l'interfaccia della riga di comando di Azure per Cache HPC di Azure

Seguire questi passaggi per preparare l'ambiente prima di usare l'interfaccia della riga di comando di Azure per creare o gestire una cache HPC di Azure.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - La cache HPC di Azure richiede la versione 2,7 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="set-default-resource-group-optional"></a>Imposta il gruppo di risorse predefinito (facoltativo)

Per la maggior parte dei comandi della cache HPC è necessario passare il gruppo di risorse della cache. È possibile impostare il gruppo di risorse predefinito usando il comando [AZ Configure](/cli/azure/reference-index#az-configure).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver installato l'estensione dell'interfaccia della riga di comando di Azure ed eseguito l'accesso, è possibile usare l'interfaccia della riga di comando di Azure per creare e gestire i sistemi

* [Creare una cache HPC di Azure](hpc-cache-create.md)
* [Documentazione dell'interfaccia della riga di comando di Azure HPC-cache](/cli/azure/ext/hpc-cache/hpc-cache)
