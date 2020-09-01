---
title: Visualizzare l'entità servizio di un'identità gestita usando PowerShell-Azure AD
description: Istruzioni dettagliate su come visualizzare l'entità servizio di un'identità gestita tramite PowerShell.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a9da4689a1b5579f90a1df0feb487e50d57a9d98
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269217"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Visualizzare l'entità servizio di un'identità gestita tramite PowerShell

Le identità gestite per le risorse di Azure offrono ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

In questo articolo viene descritto come visualizzare l'entità servizio di un'identità gestita tramite PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md).
- Se non si ha già un account Azure, è possibile [registrarsi per ottenerne uno gratuito](https://azure.microsoft.com/free/).
- Abilitare l'[identità assegnata dal sistema nella macchina virtuale](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) o nell'[applicazione](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity).
- Installare la versione più recente di [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="view-the-service-principal"></a>Visualizzare l'entità servizio

Il comando seguente mostra come visualizzare l'entità servizio di una macchina virtuale o un'applicazione in cui è abilitata un'identità assegnata. Sostituire `<VM or application name>` con i propri valori.

```powershell
Get-AzADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla visualizzazione delle entità servizio di Azure AD usando PowerShell, vedere [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).