---
title: 'PowerShell: abilitare BYOK Transparent Data Encryption-Istanza gestita di database SQL di Azure '
description: Informazioni su come configurare un'istanza gestita di SQL di Azure per iniziare a usare Transparent Data Encryption (TDE) per BYOK per la crittografia dei dati inattivi tramite PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/05/2019
ms.openlocfilehash: ddffda5229c9c0d33c563e3ae7b4a884f0f92dff
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691403"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Gestire Transparent Data Encryption in un'istanza gestita usando una chiave personalizzata di Azure Key Vault

Questo esempio di script di PowerShell configura Transparent Data Encryption (Transparent Data Encryption) con chiave gestita dal cliente per Istanza gestita SQL di Azure, usando una chiave da Azure Key Vault. Questo scenario viene spesso definito Bring Your Own Key scenario per Transparent Data Encryption. Per altre informazioni su Transparent Data Encryption con chiave gestita dal cliente, vedere Transparent Data [encryption Bring your own key to Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites"></a>Prerequisiti

- Istanza gestita esistente. Vedere [usare PowerShell per creare un'istanza gestita di database SQL di Azure](sql-database-create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

L'uso di PowerShell in locale o con Azure Cloud Shell richiede AZ PowerShell 2.3.2 o una versione successiva. Se è necessario eseguire l'aggiornamento, vedere [install Azure PowerShell Module](/powershell/azure/install-az-ps)o eseguire lo script di esempio seguente per installare il modulo per l'utente corrente:

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="sample-scripts"></a>Script di esempio

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Per altri esempi, vedere tra gli [script di PowerShell per database SQL di Azure](../sql-database-powershell-samples.md).
