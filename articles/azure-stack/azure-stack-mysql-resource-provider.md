---
title: Usare i database MySQL come PaaS in Azure Stack | Microsoft Docs
description: Informazioni su come distribuire il Provider di risorse MySQL e fornire i database MySQL come servizio in Azure Stack.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: e610f946868940f9fde66932bedec1dc998f390b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56203880"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Usare i database MySQL in Microsoft Azure Stack

Database MySQL vengono comunemente usati con siti Web e supportano molte piattaforme di sito Web. Ad esempio, è possibile creare siti Web WordPress mediante il componente aggiuntivo (PaaS) di provider di servizi App risorsa.

Dopo aver distribuito il provider di risorse, è possibile:

* Creare MySQL Server e database con modelli di distribuzione Azure Resource Manager.
* Specificare i database MySQL come servizio.  

## <a name="mysql-resource-provider-adapter-architecture"></a>Architettura adapter del provider di risorse MySQL

Il provider di risorse include i componenti seguenti:

* **La macchina virtuale MySQL resource provider adapter (VM)**, ovvero una VM Windows Server che esegue il provider di servizi.
* **Il provider di risorse**, che elabora le richieste e le risorse di database gli accessi.
* **Server che ospitano il MySQL Server**, che forniscono la capacità per i database che vengono chiamati i server di hosting. È possibile creare manualmente le istanze di MySQL o fornire l'accesso alle istanze di MySQL esterne. Il [raccolta di avvio rapido di Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) dispone di un modello di esempio che è possibile usare per:

  * Creare un server MySQL.
  * Scaricare e distribuire un MySQL Server da Azure Marketplace.

> [!NOTE]
> Hosting di server che vengono installati in Azure Stack i sistemi integrati devono essere creati da una sottoscrizione tenant. E non possono essere creati dalla sottoscrizione provider predefinito. Essi devono essere creati dal portale tenant o da una sessione di PowerShell con un accesso appropriato. Tutti i server di hosting sono fatturabili macchine virtuali e devono avere le licenze. L'amministratore del servizio può essere il proprietario della sottoscrizione tenant.

### <a name="required-privileges"></a>Privilegi necessari

L'account di sistema deve disporre dei privilegi seguenti:

* **Database:** creare, eliminare
* **Account di accesso:** creare, impostare, drop, concedere, revocare  

## <a name="next-steps"></a>Passaggi successivi

[Distribuire il provider di risorse MySQL](azure-stack-mysql-resource-provider-deploy.md)
