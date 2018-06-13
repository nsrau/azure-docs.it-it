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
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: b1cc1fad6b0831bcf0bab5ba4f37b753c3cf33ca
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2018
ms.locfileid: "33207266"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Utilizzare i database SQL nello Stack di Microsoft Azure
Utilizzare l'adapter di provider di risorse di SQL Server per esporre i database SQL come servizio di [Azure Stack](azure-stack-poc.md). Dopo aver installato il provider di risorse e connetterla a uno o più istanze di SQL Server, gli utenti possono creare:
- Database per le app cloud nativo.
- Siti Web basati su SQL.
- Carichi di lavoro basati su SQL.
Non è necessario eseguire il provisioning di una macchina virtuale (VM) che ospita SQL Server ogni volta.

Il provider di risorse non supporta tutte le funzionalità di gestione di database di [Database SQL di Azure](https://azure.microsoft.com/services/sql-database/). Ad esempio, pool di Database elastici e la possibilità di comporre su e giù automaticamente le prestazioni del database non sono disponibili. Tuttavia, risorse provider supportati simile creare, leggere, aggiornare ed eliminazione (CRUD). L'API non è compatibile con il Database SQL.

## <a name="sql-resource-provider-adapter-architecture"></a>Architettura dell'adapter SQL resource provider
Il provider di risorse è costituito da tre componenti:

- **L'adapter di provider di risorse SQL VM**, che è una macchina virtuale di Windows che esegue i servizi di provider.
- **Il provider di risorse stesso**, che elabora le richieste di provisioning ed espone le risorse di database.
- **I server che ospitano SQL Server**, che forniscono capacità per i database denominato server di hosting.

È necessario creare istanze di uno (o più) di SQL Server e/o fornire l'accesso alle istanze di SQL Server esterne.

> [!NOTE]
> I server che vengono installati nello Stack di Azure di hosting sistemi integrati devono essere creati da una sottoscrizione tenant. Non possono essere creati dalla sottoscrizione provider predefinito. Essi devono essere creati dal portale tenant o da una sessione di PowerShell con un accesso appropriato. Tutti i server di hosting sono addebitabile macchine virtuali e devono disporre delle licenze appropriate. L'amministratore del servizio può essere il proprietario della sottoscrizione tenant.


## <a name="next-steps"></a>Passaggi successivi

[Distribuire il provider di risorse di SQL Server](azure-stack-sql-resource-provider-deploy.md)
