---
title: Utilizzo di database SQL Azure stack | Documenti Microsoft
description: Informazioni su come distribuire i database SQL come servizio in Azure Stack e la procedura per distribuire l'adapter di provider di risorse di SQL Server.
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
ms.openlocfilehash: 55d0e51606e8768a01c0b5a7766dbafe24d97a0d
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2018
ms.locfileid: "36307826"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Utilizzare i database SQL nello Stack di Microsoft Azure

Utilizzare l'adapter di provider di risorse di SQL Server API per esporre i database SQL come un servizio del [Azure Stack](azure-stack-poc.md). Dopo aver installato il provider di risorse e connetterla a uno o più istanze di SQL Server, gli utenti possono creare:

- Database per le app cloud nativo.
- Siti Web che utilizzano SQL.
- Carichi di lavoro che usano SQL.

Il provider di risorse non fornisce tutti i database capacità di gestione di [Database SQL di Azure](https://azure.microsoft.com/services/sql-database/). Ad esempio, i pool elastici che allocano automaticamente le risorse non sono supportati. Tuttavia, il provider di risorse supporta simile creare, leggere, aggiornare ed eliminazione (CRUD) su un database di SQL Server. Per ulteriori informazioni sul provider di risorse API, vedere [Windows Azure Pack SQL Server Resource Provider REST API Reference](https://msdn.microsoft.com/library/dn528529.aspx).

>[!NOTE]
API del provider di risorse SQL Server non è compatibile con il Database SQL di Azure.

## <a name="sql-resource-provider-adapter-architecture"></a>Architettura dell'adapter SQL resource provider

Il provider di risorse include i componenti seguenti:

- **La macchina virtuale SQL resource provider adapter (VM)**, ovvero una VM di Windows Server che esegue i servizi di provider.
- **Il provider di risorse**, che elabora le richieste e accede alle risorse del database.
- **I server che ospitano SQL Server**, che forniscono capacità per i database denominato server di hosting.

È necessario creare almeno un'istanza di SQL Server o fornire l'accesso alle istanze di SQL Server esterne.

> [!NOTE]
> I server che vengono installati nello Stack di Azure di hosting sistemi integrati devono essere creati da una sottoscrizione tenant. Non possono essere creati dalla sottoscrizione provider predefinito. Essi devono essere creati dal portale tenant o usando PowerShell con l'accesso appropriato. Tutti i server di hosting sono fatturabili macchine virtuali e devono disporre delle licenze. L'amministratore del servizio può essere il proprietario della sottoscrizione tenant.

## <a name="next-steps"></a>Passaggi successivi

[Distribuire il provider di risorse di SQL Server](azure-stack-sql-resource-provider-deploy.md)
