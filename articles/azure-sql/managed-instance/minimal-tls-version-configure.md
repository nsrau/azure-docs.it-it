---
title: Configurare un'istanza minima gestita della versione TLS
description: Informazioni su come configurare una versione minima di TLS per l'istanza gestita
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: ''
ms.date: 05/25/2020
ms.openlocfilehash: 17d430946f3cba1aa4680d1eaf8979fa4338bc22
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92788401"
---
# <a name="configure-minimal-tls-version-in-azure-sql-managed-instance"></a>Configurare la versione minima di TLS in Azure SQL Istanza gestita
L'impostazione della versione [Transport Layer Security minima (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) consente ai clienti di controllare la versione di TLS usata dalle istanza gestita SQL di Azure.

Al momento sono supportati TLS 1.0, 1.1 e TLS 1.2. L'impostazione Versione minima TLS assicura il supporto delle nuove versioni di TLS. Ad esempio, la scelta di una versione di TLS successiva a 1.1 indica che vengono accettate solo le connessioni con TLS 1.1 e 1.2, mentre quelle con TLS 1.0 vengono rifiutate. Dopo il test per confermare che le applicazioni lo supportano, è consigliabile impostare una versione minima di TLS su 1,2, perché include correzioni per le vulnerabilità rilevate nelle versioni precedenti ed è la versione più recente di TLS supportata in Azure SQL Istanza gestita.

Per i clienti con applicazioni che si basano su versioni precedenti di TLS, è consigliabile impostare la versione minima di TLS in base ai requisiti delle applicazioni. Per i clienti le cui applicazioni usano connessioni non crittografate, è consigliabile non impostare una versione minima di TLS. 

Per altre informazioni, vedere [Considerazioni su TLS per la connettività a Database SQL](../database/connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

Dopo aver impostato la versione minima di TLS, i tentativi di accesso dei client che usano una versione di TLS inferiore a quella minima del server avranno esito negativo, con l'errore seguente:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>Impostare la versione minima di TLS tramite PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato da Database SQL di Azure, ma tutte le attività di sviluppo future sono incentrate sul modulo Az.Sql. Per informazioni su questi cmdlet, vedere [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici. Per lo script seguente è necessario il [modulo di Azure PowerShell](/powershell/azure/install-az-ps).

Lo script di PowerShell seguente mostra come `Get` e `Set` la proprietà della **versione minima di TLS** a livello di istanza:

```powershell
#Get the Minimal TLS Version property
(Get-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group).MinimalTlsVersion

# Update Minimal TLS Version Property
Set-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>Impostare la versione minima di TLS tramite l'interfaccia della riga di comando di Azure

> [!IMPORTANT]
> Tutti gli script di questa sezione richiedono l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Interfaccia della riga di comando di Azure in una shell Bash

Il seguente script dell'interfaccia della riga di comando mostra come cambiare l'impostazione **Versione minima TLS** in una shell Bash:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql mi show -n sql-instance-name -g resource-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql mi update -n sql-instance-name -g resource-group --set minimalTlsVersion="1.2"
```