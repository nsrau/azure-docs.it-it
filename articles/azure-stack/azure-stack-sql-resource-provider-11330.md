---
title: Note sulla versione di Azure Stack SQL resource provider 1.1.30.0 | Microsoft Docs
description: Informazioni sulle novità nell'aggiornamento più recente SQL di Azure Stack resource provider, inclusi i problemi noti e posizione di download.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: jeffgilb
ms.reviewer: georgel
ms.openlocfilehash: edca29cfe53f7aa313eb1178a6a2ef19aa7178d0
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2019
ms.locfileid: "54159622"
---
# <a name="sql-resource-provider-11330-release-notes"></a>Note sulla versione SQL resource provider 1.1.33.0

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Queste note sulla versione vengono descritti i miglioramenti e problemi noti nella versione del provider di risorse 1.1.33.0 SQL.

## <a name="build-reference"></a>Riferimento alla compilazione
Scaricare il provider di risorse SQL binario e quindi eseguire il programma di autoestrazione per estrarre il contenuto in una directory temporanea. Il provider di risorse dispone di uno Stack di Azure corrispondente minimo di compilazione. La versione minima dello Stack di Azure necessaria per installare questa versione del provider di risorse SQL è elencata di seguito:

> |Versione minima di Azure Stack|Versione del provider di risorse SQL|
> |-----|-----|
> |Versione 1808 (1.1808.0.97)|[SQL RP versione 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> Applicare l'aggiornamento di Azure Stack supportato minimo per il sistema integrato Azure Stack o distribuire più recente Azure Stack Development Kit (ASDK) prima di distribuire la versione più recente del provider di risorse SQL.

## <a name="new-features-and-fixes"></a>Nuove funzionalità e correzioni
Questa versione del provider di risorse SQL di Azure Stack include le correzioni e i miglioramenti seguenti:

### <a name="fixes"></a>Correzioni
- **Estensione del portale del provider di risorse SQL potrebbe scegliere la sottoscrizione errata**. Il provider di risorse SQL Usa chiamate ad Azure Resource Manager per determinare la prima sottoscrizione di amministratore del servizio da usare, che potrebbero non essere la *sottoscrizione del Provider predefinito*. In tal caso, il provider di risorse SQL non funziona normalmente. 

- **Server di hosting SQL non sono elencati i database ospitati.** Database creati dall'utente potrebbero non essere presente quando si visualizzano le risorse del tenant per i server di hosting SQL.

- **Distribuzione del provider (1.1.30.0) risorse SQL precedente potrebbe non riuscire se TLS 1.2 non è abilitato**. Aggiornare il provider di risorse SQL 1.1.33.0 per abilitare TLS 1.2 quando si distribuisce il provider di risorse, l'aggiornamento del provider di risorse o la rotazione dei segreti. 

- **Si verifica un errore di rotazione segreta SQL resource provider**. Problema risolto generando il codice di errore seguente durante la rotazione dei segreti: ` New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>Problemi noti 

- **SKU SQL può richiedere fino a un'ora siano visibili nel portale di**. Possono trascorrere a un'ora per i nuovi SKU creata sia visibile per l'uso durante la creazione di nuovi database SQL. 

    **Soluzione alternativa**: No.

- **Riutilizzare gli account di accesso SQL**. Tentativo di creare un nuovo SQL l'accesso con lo stesso nome utente come un account di accesso esistente nella stessa sottoscrizione comporterà riutilizzo all'account di accesso e la password esistente. 

    **Soluzione alternativa**: Usare nomi utente diversi durante la creazione di nuovi account di accesso nella stessa sottoscrizione o creare gli account di accesso con lo stesso nome utente in diverse sottoscrizioni.

- **Gli account di accesso condiviso di SQL causare un'incoerenza dei dati**. Se un account di accesso SQL condiviso per più database SQL nella stessa sottoscrizione, la modifica della password di account di accesso causerà un'incoerenza dei dati.

    **Soluzione alternativa**: Usare sempre gli account di accesso diversi per i database diversi nella stessa sottoscrizione.

- **Provider di risorse SQL non riesce ad aggiungere i listener di SQL Server Always On**. Quando si usa l'indirizzo IP del listener del SQL Server Always sul Listener, il provider di risorse della macchina virtuale di SQL non è possibile risolvere il nome del listener host.

    **Soluzione alternativa**: Assicurarsi di che DNS funzioni correttamente per risolvere l'indirizzo IP del listener per nome host del listener.

### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Problemi noti per gli amministratori Cloud Azure Stack operativo
Vedere la documentazione nel [note sulla versione di Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni sul provider di risorse SQL](azure-stack-sql-resource-provider.md).

[Preparare la distribuzione il provider di risorse SQL](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Aggiornare il provider di risorse SQL da una versione precedente](azure-stack-sql-resource-provider-update.md). 