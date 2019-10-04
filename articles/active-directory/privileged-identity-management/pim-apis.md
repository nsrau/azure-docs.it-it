---
title: API Microsoft Graph per PIM (anteprima) - Azure Active Directory | Microsoft Docs
description: Fornisce informazioni sull'uso delle API Microsoft Graph per Azure AD Privileged Identity Management (PIM, anteprima).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 11/13/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45c7f42d536880f2578c62c6c4866b21be1cc9dc
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804567"
---
# <a name="microsoft-graph-apis-for-pim-preview"></a>API Microsoft Graph per PIM (anteprima)

Per la maggior parte delle attività che si possono eseguire in Azure Active Directory (Azure AD) Privileged Identity Management (PIM) con il portale di Azure, è anche possibile usare le [API Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/overview). Questo articolo descrive alcuni concetti importanti quando si usano le API Microsoft Graph per PIM. Per informazioni dettagliate sulle API Microsoft Graph, esaminare le [informazioni di riferimento sulle API di Azure Active Directory Privileged Identity Management](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root).

> [!IMPORTANT]
> Le API nella versione beta in Microsoft Graph sono disponibili in anteprima e sono soggetti a modifiche. L'uso di queste API nelle applicazioni di produzione non è supportato.

## <a name="required-permissions"></a>Autorizzazioni necessarie

Per chiamare le API Microsoft Graph per PIM, è necessario avere **uno o più** delle autorizzazioni seguenti:

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>Impostare le autorizzazioni

Per permettere alle applicazioni di chiamare le API Microsoft Graph per PIM, devono avere le autorizzazioni richieste. Il modo più semplice per specificare le autorizzazioni richieste consiste nell'usare il [framework di consenso di Azure AD](../develop/consent-framework.md).

### <a name="set-permissions-in-graph-explorer"></a>Impostare le autorizzazioni in Graph Explorer

Se si usa Graph Explorer per testare le chiamate, è possibile specificare le autorizzazioni nello strumento.

1. Accedere a [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) come amministratore globale.

1. Fare clic su **Autorizzazioni di modifica**.

    ![Graph Explorer - Autorizzazioni di modifica](./media/pim-apis/graph-explorer.png)

1. Aggiungere segni di spunta accanto alle autorizzazioni che si vogliono includere. `PrivilegedAccess.ReadWrite.AzureAD` non è ancora disponibile in Graph Explorer.

    ![Graph Explorer - Autorizzazioni di modifica](./media/pim-apis/graph-explorer-modify-permissions.png)

1. Fare clic su **Autorizzazioni di modifica** per applicare le modifiche alle autorizzazioni.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni di riferimento sulle API di Azure Active Directory Privileged Identity Management](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)
