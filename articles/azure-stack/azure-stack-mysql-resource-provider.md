---
title: Utilizzare i database MySQL come PaaS nello Stack di Azure | Documenti Microsoft
description: Informazioni su come distribuire il Provider di risorse MySQL e fornire i database MySQL come servizio nello Stack di Azure.
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
ms.date: 06/21/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 24ba595413cde07c420a94de234d7926e0eb0e7f
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2018
ms.locfileid: "36310050"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Utilizzare i database MySQL in Microsoft Azure Stack

È possibile distribuire il provider di risorse MySQL API da utilizzare i database MySQL distribuiti nello Stack di Azure. Per ulteriori informazioni sul provider di risorse API, vedere [Windows Azure Pack MySQL Resource Provider REST API Reference](https://msdn.microsoft.com/library/dn528442.aspx).

Database MySQL sono comuni nei siti Web e supportano molte piattaforme di sito Web. Ad esempio, è possibile creare siti Web di WordPress usando la piattaforma di App Web come un componente aggiuntivo del servizio (PaaS).

Dopo aver distribuito il provider di risorse, è possibile:

* Creazione di MySQL Server e database che utilizzano modelli di distribuzione Azure Resource Manager.
* Specificare i database MySQL come servizio.  

## <a name="mysql-resource-provider-adapter-architecture"></a>Architettura dell'adapter provider risorse MySQL

Il provider di risorse include i componenti seguenti:

* **La macchina virtuale MySQL resource provider adapter (VM)**, ovvero una VM di Windows Server che esegue i servizi di provider.
* **Il provider di risorse**, che elabora le richieste e accede alle risorse del database.
* **I server che ospitano il MySQL Server**, che forniscono capacità per i database che vengono chiamati i server di hosting. È possibile creare manualmente le istanze di MySQL o fornire l'accesso alle istanze di MySQL esterne. Il [raccolta Guida introduttiva di Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) dispone di un modello di esempio che è possibile usare per:

  * Creare un server MySQL.
  * Scaricare e distribuire un MySQL Server da Azure Marketplace.

> [!NOTE]
> I server che vengono installati nello Stack di Azure di hosting sistemi integrati devono essere creati da una sottoscrizione tenant. Non possono essere creati dalla sottoscrizione provider predefinito. Essi devono essere creati dal portale tenant o da una sessione di PowerShell con un accesso appropriato. Tutti i server di hosting sono fatturabili macchine virtuali e devono disporre delle licenze. L'amministratore del servizio può essere il proprietario della sottoscrizione tenant.

### <a name="required-privileges"></a>Privilegi necessari

L'account di sistema deve disporre dei privilegi seguenti:

* **Database:** creare, eliminare
* **Account di accesso:** creare, impostare, drop, concedere, revocare  

## <a name="next-steps"></a>Passaggi successivi

[Distribuire il provider di risorse MySQL](azure-stack-mysql-resource-provider-deploy.md)
