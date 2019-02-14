---
title: Come visualizzare l'entità servizio di un'identità gestita tramite PowerShell
description: Istruzioni dettagliate su come visualizzare l'entità servizio di un'identità gestita tramite PowerShell.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bf6687adc367ef32550c4cb70a667050e54298c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56200752"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Visualizzare l'entità servizio di un'identità gestita tramite PowerShell

Le identità gestite per le risorse di Azure offrono ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

In questo articolo viene descritto come visualizzare l'entità servizio di un'identità gestita tramite PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md).
- Se non si ha già un account Azure, è possibile [registrarsi per ottenerne uno gratuito](https://azure.microsoft.com/free/).
- Abilitare l'[identità assegnata dal sistema nella macchina virtuale](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) o nell'[applicazione](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).
- Installare la versione più recente di [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="view-the-service-principal"></a>Visualizzare l'entità servizio

Il comando seguente mostra come visualizzare l'entità servizio di una macchina virtuale o un'applicazione in cui è abilitata un'identità assegnata. Sostituire `<VM or application name>` con i propri valori.

```PowerShell
Get-AzADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla visualizzazione delle entità servizio di Azure AD usando PowerShell, vedere [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).


