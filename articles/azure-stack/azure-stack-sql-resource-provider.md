---
title: Utilizzo di database SQL in Azure Stack | Microsoft Docs
description: Informazioni su come distribuire i database SQL come servizio in Azure Stack e le azioni rapide per distribuire l'adapter di provider di risorse di SQL Server.
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
ms.openlocfilehash: eccbd502e1fcd50524f7627abf0c7cb675672a98
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182273"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Usare database SQL in Microsoft Azure Stack

Usare il provider di risorse di SQL Server per offrire database SQL come un servizio del [Azure Stack](azure-stack-poc.md). Dopo avere installato il provider di risorse e connetterla a una o più istanze di SQL Server, gli utenti possono creare:

- Database per le app native del cloud.
- Siti Web che usano SQL.
- Carichi di lavoro che usano SQL.

Il provider di risorse non fornisce tutti i database dalla capacità di gestione di [Database SQL di Azure](https://azure.microsoft.com/services/sql-database/). Ad esempio, i pool elastici che allocano automaticamente le risorse non sono supportati. Tuttavia, il provider di risorse supporta Analogamente creare, leggere, aggiornare ed eliminazione (CRUD) su un database di SQL Server. 

## <a name="sql-resource-provider-adapter-architecture"></a>Architettura dell'adapter SQL resource provider

Il provider di risorse include i componenti seguenti:

- **La macchina virtuale SQL resource provider adapter (VM)**, ovvero una VM Windows Server che esegue i servizi del provider.
- **Il provider di risorse**, che elabora le richieste e le risorse di database gli accessi.
- **Server che ospitano SQL Server**, che forniscono la capacità per i database denominato server di hosting.

È necessario creare almeno un'istanza di SQL Server o fornire l'accesso alle istanze di SQL Server esterne.

> [!NOTE]
> Hosting di server che vengono installati in Azure Stack i sistemi integrati devono essere creati da una sottoscrizione tenant. E non possono essere creati dalla sottoscrizione provider predefinito. Che devono essere creati dal portale tenant o tramite PowerShell con l'accesso appropriato. Tutti i server di hosting sono fatturabili macchine virtuali e devono avere le licenze. L'amministratore del servizio può essere il proprietario della sottoscrizione tenant.

## <a name="next-steps"></a>Passaggi successivi

[Distribuire il provider di risorse SQL Server](azure-stack-sql-resource-provider-deploy.md)
