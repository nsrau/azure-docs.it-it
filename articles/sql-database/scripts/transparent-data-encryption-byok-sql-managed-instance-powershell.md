---
title: 'PowerShell: Enable BYOK TDE - Azure SQL Database Managed Instance '
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73691403"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Gestire Transparent Data Encryption in un'istanza gestita usando una chiave personalizzata di Azure Key Vault

Questo esempio di script di PowerShell configura Transparent Data Encryption (TDE) con chiave gestita dal cliente per l'istanza gestita SQL di Azure, usando una chiave dell'insieme di credenziali delle chiavi di Azure.This PowerShell script example configures Transparent Data Encryption (TDE) with customer-managed key for Azure SQL Managed Instance, using a key from Azure Key Vault. Questo è spesso indicato come uno scenario Bring Your Own Key per TDE. Per altre informazioni sul TDE con chiave gestita dal cliente, vedere [TDE Bring Your Own Key to Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites"></a>Prerequisiti

- Un'istanza gestita esistente. Vedere [Usare PowerShell per creare un'istanza gestita del database SQL di Azure.See Use PowerShell to create an Azure SQL Database managed instance](sql-database-create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

L'uso di PowerShell in locale o di Azure Cloud Shell richiede l'utilizzo di PowerShell 2.3.2 o versione successiva. Se è necessario eseguire l'aggiornamento, vedere Installare il modulo di Azure PowerShell o eseguire lo script di esempio seguente per installare il modulo per l'utente corrente:If you need to upgrade, see [Install Azure PowerShell module](/powershell/azure/install-az-ps), or run the below sample script to install the module for the current user:

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="sample-scripts"></a>Script di esempio

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Per altri esempi, vedere tra gli [script di PowerShell per database SQL di Azure](../sql-database-powershell-samples.md).
