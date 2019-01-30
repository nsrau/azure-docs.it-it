---
title: Note sulla versione di Azure Stack MySQL resource provider 1.1.30.0 | Microsoft Docs
description: Informazioni sulle novità nell'aggiornamento più recente MySQL di Azure Stack resource provider, inclusi i problemi noti e posizione di download.
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
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 7f44e8c2c4587ecfdb3bd5eb4304789674da96f3
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55252006"
---
# <a name="mysql-resource-provider-11330--release-notes"></a>Note sulla versione di MySQL resource provider 1.1.33.0

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Queste note sulla versione vengono descritti i miglioramenti e problemi noti nella versione del provider di risorse 1.1.33.0 MySQL.

## <a name="build-reference"></a>Riferimento alla compilazione
Scaricare il provider di risorse MySQL binario e quindi eseguire il programma di autoestrazione per estrarre il contenuto in una directory temporanea. Il provider di risorse dispone di uno Stack di Azure corrispondente minimo di compilazione. La versione minima dello Stack di Azure necessaria per installare questa versione del provider di risorse MySQL è elencata di seguito:

> |Versione minima di Azure Stack|Versione del provider di risorse MySQL|
> |-----|-----|
> |Versione 1808 (1.1808.0.97)|[RP MySQL versione 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> Applicare l'aggiornamento di Azure Stack supportato minimo per il sistema integrato Azure Stack o distribuire più recente Azure Stack Development Kit (ASDK) prima di distribuire la versione più recente del provider di risorse MySQL.

## <a name="new-features-and-fixes"></a>Nuove funzionalità e correzioni
Questa versione del provider di risorse MySQL di Azure Stack include le correzioni e i miglioramenti seguenti:

### <a name="fixes"></a>Correzioni
- **Estensione del portale del provider di risorse MySQL potrebbe scegliere la sottoscrizione errata**. Il provider di risorse MySQL Usa chiamate ad Azure Resource Manager per determinare la prima sottoscrizione di amministratore del servizio da usare, che potrebbero non essere la *sottoscrizione del Provider predefinito*. In tal caso, il provider di risorse MySQL non funziona normalmente. 

- **MySQL server di hosting non sono ospitati i database.** Database creati dall'utente potrebbero non essere presente quando si visualizzano le risorse del tenant per i server di hosting MySQL.

- **Distribuzione del provider (1.1.30.0) risorse MySQL precedente potrebbe non riuscire se TLS 1.2 non è abilitato**. Aggiornare il provider di risorse MySQL 1.1.33.0 per abilitare TLS 1.2 quando si distribuisce il provider di risorse, l'aggiornamento del provider di risorse o la rotazione dei segreti. 

- **Si verifica un errore di rotazione segreta del provider di risorse MySQL**. Problema risolto generando il codice di errore seguente durante la rotazione dei segreti: ` New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>Problemi noti 

- **SKU MySQL può richiedere fino a un'ora siano visibili nel portale di**. Operazione può richiedere un'ora per i nuovi SKU creata sia visibile per l'uso durante la creazione di nuovi database MySQL. 

    **Soluzione alternativa**: No.

- **Riutilizzare gli account di accesso MySQL**. Tentativo di creare un nuovo MySQL account di accesso con lo stesso nome utente come un account di accesso esistente nella stessa sottoscrizione comporterà riutilizzo all'account di accesso e la password esistente. 

    **Soluzione alternativa**: Usare nomi utente diversi durante la creazione di nuovi account di accesso nella stessa sottoscrizione o creare gli account di accesso con lo stesso nome utente in diverse sottoscrizioni.

- **Gli account di accesso condiviso di MySQL causare un'incoerenza dei dati**. Se un account di accesso MySQL condiviso per più database MySQL nella stessa sottoscrizione, la modifica della password di account di accesso causerà un'incoerenza dei dati.

    **Soluzione alternativa**: Usare sempre gli account di accesso diversi per i database diversi nella stessa sottoscrizione.


### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Problemi noti per gli amministratori Cloud Azure Stack operativo
Vedere la documentazione nel [note sulla versione di Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni sui provider di risorse MySQL](azure-stack-mysql-resource-provider.md).

[Preparare la distribuzione del provider di risorse MySQL](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Eseguire l'aggiornamento da una versione precedente del provider di risorse MySQL](azure-stack-mysql-resource-provider-update.md). 