---
title: Gestire l'accesso alla gestione di Azure con l'accesso condizionale in Azure ADManage access to Azure management with Conditional Access in Azure AD
description: Informazioni sull'uso dell'accesso condizionale in Azure AD per gestire l'accesso alla gestione di Azure.Learn about using Conditional Access in Azure AD to manage access to Azure management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: f3341f1c30a1581b8507652c322c00581e3972aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137405"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Gestire l'accesso alla gestione di Azure con accesso condizionaleManage access to Azure management with Conditional Access

> [!CAUTION]
> Assicurarsi di aver compreso il funzionamento di Accesso condizionale prima di configurare un criterio per gestire l'accesso alla gestione di Azure.Make sure you understand how Conditional Access works before setting up a policy to manage access to Azure management. Assicurarsi di non creare condizioni che potrebbero bloccare il proprio accesso al portale.

L'accesso condizionale in Azure Active Directory (Azure AD) controlla l'accesso alle app cloud in base a condizioni specifiche specificate. Per consentire l'accesso, creare criteri di accesso condizionale che consentono o impediscono l'accesso in base al fatto che i requisiti nei criteri siano soddisfatti o meno. 

In genere, si usa l'accesso condizionale per controllare l'accesso alle app cloud. È inoltre possibile configurare i criteri per controllare l'accesso alla gestione di Azure in base a determinate condizioni (ad esempio, il rischio di accesso, il percorso o il dispositivo) e applicare dei requisiti come l'autenticazione a più fattori.

Per creare un criterio per la gestione di Azure, selezionare **Gestione di Microsoft Azure** in **App cloud** quando si sceglie l'app a cui applicare il criterio.

![Accesso condizionale per la gestione di Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

I criteri creati si applicano a tutti gli endpoint di gestione di Azure, inclusi gli elementi seguenti:The policy you create applies to all Azure management endpoints, including the following:

- Portale di Azure
- Provider di Azure Resource Manager
- API di gestione dei servizi classici
- Azure PowerShell
- Portale di amministrazione delle sottoscrizioni di Visual StudioVisual Studio subscriptions administrator portal
- Azure DevOps
- Portale di Azure Data FactoryAzure Data Factory portal

Si noti che il criterio si applica ad Azure PowerShell che chiama l'API di Azure Resource Manager. Non si applica ad [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2) che chiama Microsoft Graph.


Per altre informazioni su come configurare e usare l'accesso condizionale, vedere [Accesso condizionale in Azure Active Directory.](../active-directory/active-directory-conditional-access-azure-portal.md)
