---
title: 'PowerShell: abilitare TDE per BYOK - Istanza gestita di database SQL di Azure | Microsoft Docs'
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
manager: craigg
ms.date: 04/19/2019
ms.openlocfilehash: c2c4bd7bffd923430d0817cb6ea975f4c1596623
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729158"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault-preview"></a>Gestire Transparent Data Encryption in un'istanza gestita usando la propria chiave di Azure Key Vault (anteprima)

Questo esempio di script di PowerShell Configura Transparent Data Encryption (TDE) in uno scenario Bring Your Own Key (anteprima) per istanza gestita SQL Azure, usando una chiave di Azure Key Vault. Per altre informazioni su TDE con il supporto Bring Your Own Key (BYOK), vedere [Bring Your Own Key di TDE per SQL Azure](../transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites"></a>Prerequisiti

- Un'istanza gestita esistente. Visualizzare [istanza gestita di usare PowerShell per creare un Database SQL di Azure](sql-database-create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Usando entrambe PowerShell in locale o Azure Cloud Shell richiede PowerShell AZ 1.1.1 o una successiva versione di anteprima. Se è necessario eseguire l'aggiornamento, vedere [modulo di installare Azure PowerShell](/powershell/azure/install-az-ps), o eseguire il seguente script di esempio per installare il modulo.

`Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease -Force`

Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="sample-scripts"></a>Script di esempio

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Per altri esempi, vedere tra gli [script di PowerShell per database SQL di Azure](../sql-database-powershell-samples.md).
