---
title: Gestire l'accesso alla gestione di Azure con accesso condizionale in Azure AD
description: Informazioni su come usare l'accesso condizionale in Azure AD per gestire l'accesso alla gestione di Azure.
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
ms.openlocfilehash: 2a52635dbaa7a76034f3a535b099320a901e8c07
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758776"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Gestire l'accesso alla gestione di Azure con accesso condizionale

> [!CAUTION]
> È importante comprendere il funzionamento dell'accesso condizionale prima di configurare un criterio per gestire l'accesso alla gestione di Azure. Assicurarsi di non creare condizioni che potrebbero bloccare il proprio accesso al portale.

L'accesso condizionale di Azure Active Directory (Azure AD) controlla l'accesso alle app cloud in base alle condizioni specificate dall'utente. Per consentire l'accesso, vengono creati dei criteri di accesso condizionale che consentono o bloccano l'accesso se i requisiti del criterio vengono o non vengono soddisfatti. 

In genere, l'accesso condizionale viene usato per controllare l'accesso alle app cloud. È inoltre possibile configurare i criteri per controllare l'accesso alla gestione di Azure in base a determinate condizioni (ad esempio, il rischio di accesso, il percorso o il dispositivo) e applicare dei requisiti come l'autenticazione a più fattori.

Per creare un criterio per la gestione di Azure, selezionare **Gestione di Microsoft Azure** in **App cloud** quando si sceglie l'app a cui applicare il criterio.

![Accesso condizionale per la gestione di Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

Il criterio creato si applica a tutti gli endpoint di gestione di Azure, inclusi i seguenti:

- Portale di Azure
- Provider di Azure Resource Manager
- API classiche di gestione del servizio
- Azure PowerShell
- Portale di amministratore delle sottoscrizioni di Visual Studio
- Azure DevOps
- Portale di Azure Data Factory

Si noti che il criterio si applica ad Azure PowerShell che chiama l'API di Azure Resource Manager. Non si applica ad [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2) che chiama Microsoft Graph.

Per altre informazioni su come configurare un criterio di esempio per abilitare l'accesso condizionale per la gestione di Microsoft Azure, vedere l'articolo [Accesso condizionale: Richiedere l'autenticazione a più fattori per la gestione di Azure](../active-directory/conditional-access/howto-conditional-access-policy-azure-management.md).
