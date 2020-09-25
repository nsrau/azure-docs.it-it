---
title: Creare il nome di dominio completo per una macchina virtuale nel portale di Azure
description: Informazioni su come creare un nome di dominio completo (FQDN) per una macchina virtuale Linux nel portale di Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: deae22ad0c763e48df053d19beefba6054ed2767
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91331472"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Creare un nome di dominio completo nel portale di Azure per una macchina virtuale Linux

Quando si crea una macchina virtuale nel [portale di Azure](https://portal.azure.com), il portale crea automaticamente una risorsa IP pubblica per la macchina virtuale. Usare questo indirizzo IP per accedere in remoto alla VM. Anche se il portale non crea un [nome di dominio completo](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), o FQDN (Fully Qualified Domain Name), è possibile aggiungerne uno dopo aver creato la VM. Questo articolo illustra i passaggi per creare un nome DNS o un FQDN.

## <a name="create-a-fqdn"></a>Creare un nome di dominio completo
In questo articolo si presuppone che sia già stata creata una VM. Se necessario, è possibile [creare una macchina virtuale nel portale](quick-create-portal.md) o [con l'interfaccia della riga di comando di Azure](quick-create-cli.md). Dopo che la macchina virtuale è operativa, seguire questi passaggi:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

È ora possibile connettersi in modalità remota alla macchina virtuale usando questo nome DNS, ad esempio `ssh azureuser@mydns.westus.cloudapp.azure.com`.

## <a name="next-steps"></a>Passaggi successivi
Quando la macchina virtuale ha un indirizzo IP pubblico e un nome DNS, è possibile distribuire framework applicazioni o servizi comuni, ad esempio nginx, MongoDB, Docker e così via.

Per suggerimenti sulla creazione di distribuzioni di Azure, vedere la pagina relativa all'[uso di Azure Resource Manager](../../azure-resource-manager/management/overview.md).

