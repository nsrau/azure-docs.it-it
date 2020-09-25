---
title: 'PowerShell: abilitare la crittografia Bring your own key (BYOK)'
titleSuffix: Azure SQL Managed Instance
description: Informazioni su come configurare Istanza gestita di Azure SQL per iniziare a usare il Transparent Data Encryption BYOK (Bring your own key) per la crittografia inattiva tramite PowerShell.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, sstein
ms.date: 08/25/2020
ms.openlocfilehash: 34a849fde315b45bdb1df577cf26c91f458abd72
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91323228"
---
# <a name="transparent-data-encryption-in-sql-managed-instance-using-your-own-key-from-azure-key-vault"></a>Transparent Data Encryption in SQL Istanza gestita uso della propria chiave da Azure Key Vault

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Questo esempio di script di PowerShell configura Transparent Data Encryption (Transparent Data Encryption) con una chiave gestita dal cliente per Istanza gestita SQL di Azure, usando una chiave da Azure Key Vault. Questo scenario viene spesso definito uno scenario BYOK (Bring your own key) per Transparent Data Encryption. Per altre informazioni, vedere [Transparent Data Encryption SQL di Azure con chiave gestita dal cliente](../../database/transparent-data-encryption-byok-overview.md).

## <a name="prerequisites"></a>Prerequisiti

- Un'istanza gestita esistente. Vedere [usare PowerShell per creare un'istanza gestita](create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

L'uso di PowerShell in locale o con Azure Cloud Shell richiede Azure PowerShell 2.3.2 o una versione successiva. Se è necessario eseguire l'aggiornamento, vedere [install Azure PowerShell Module](/powershell/azure/install-az-ps)o eseguire lo script di esempio seguente per installare il modulo per l'utente corrente:

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="sample-scripts"></a>Script di esempio 

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/).

Altri esempi di script di PowerShell per l'istanza gestita di SQL sono disponibili in [Script di PowerShell per Istanza gestita di SQL di Azure](../../database/powershell-script-content-guide.md).
