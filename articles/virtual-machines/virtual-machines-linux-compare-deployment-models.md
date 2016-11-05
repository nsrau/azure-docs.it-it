---
title: Provider di calcolo, rete e archiviazione | Microsoft Docs
description: Panoramica sui provider di calcolo, rete e risorse di archiviazione (CRP, NRP e SRP) per applicazioni Linux nel modello di distribuzione Azure Resource Manager
services: virtual-machines-linux
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/19/2015
ms.author: tomfitz

---
# Provider di calcolo, rete e archiviazione di Azure per applicazioni Linux nel modello di distribuzione Azure Resource Manager
L'inclusione delle funzionalità di calcolo, rete e archiviazione nel modello di distribuzione Azure Resource Manager consentirà di semplificare in modo sostanziale la distribuzione e la gestione di applicazioni complesse in esecuzione in IaaS. Molte applicazioni richiedono una combinazione di risorse, tra cui una rete virtuale, un account di archiviazione, una macchina virtuale e un'interfaccia di rete. Il modello di distribuzione Azure Resource Manager offre la possibilità di creare un modello JSON per distribuire e gestire tutte queste risorse insieme come un'unica applicazione.

[!INCLUDE [virtual-machines-common-compare-deployment-models](../../includes/virtual-machines-common-compare-deployment-models.md)]

<!---HONumber=AcomDC_0824_2016-->