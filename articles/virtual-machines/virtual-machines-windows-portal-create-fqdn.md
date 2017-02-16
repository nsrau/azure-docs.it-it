---
title: Creare un nome di dominio completo per una VM nel portale di Azure | Microsoft Docs
description: Informazioni su come creare un nome di dominio completo o FQDN per un Gestore risorse basato su macchina virtuale nel portale di Azure.
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/14/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: f556fd0318accc19f0fa56fa7f2a8716ee6f1c02
ms.openlocfilehash: 72d176803151af5b3c5da50f1f2d72c0dd27f44a


---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal"></a>Creare un nome di dominio completo nel portale di Azure.
Quando si crea una macchina virtuale nel [portale di Azure](https://portal.azure.com) usando il modello di distribuzione Azure Resource Manager, il portale crea automaticamente una risorsa IP pubblica per la macchina virtuale. Usare questo indirizzo IP per accedere in remoto alla VM. Anche se, per impostazione predefinita, il portale non crea un [nome di dominio completo](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), o FQDN (Fully Qualified Domain Name), è possibile crearne uno dopo aver creato la VM. Questo articolo illustra i passaggi per creare un nome DNS o un FQDN.

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../includes/virtual-machines-common-portal-create-fqdn.md)]

È ora possibile connettersi in modalità remota alla macchina virtuale usando questo nome DNS, ad esempio per Remote Desktop Protocol (RDP).

## <a name="next-steps"></a>Passaggi successivi
Quando la VM ha un indirizzo IP pubblico e un nome DNS, è possibile distribuire framework applicazioni o servizi comuni, ad esempio IIS, SQL o SharePoint.

Per suggerimenti sulla creazione di distribuzioni di Azure, vedere la pagina relativa all'[uso di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).




<!--HONumber=Nov16_HO3-->


