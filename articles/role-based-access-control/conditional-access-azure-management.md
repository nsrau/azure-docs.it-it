---
title: Gestire l'accesso alla gestione di Azure con accesso condizionale in Azure Active Directory
description: Informazioni sull'uso dell'accesso condizionale in Azure AD per gestire l'accesso alla gestione di Azure.
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
ms.date: 01/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 4ee58835a5055f5382cefa3a049424ffe97a01c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122954"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Gestire l'accesso alla gestione di Azure con accesso condizionale

Accesso condizionale in Azure Active Directory (Azure AD) controlla l'accesso alle App cloud basate su condizioni specifiche che preferisci. Per consentire l'accesso, si crea criteri di accesso condizionale che consentono o bloccano l'accesso in base se vengono soddisfatti i requisiti nei criteri. 

In genere, si usare l'accesso condizionale per controllare l'accesso alle App cloud. È inoltre possibile configurare i criteri per controllare l'accesso alla gestione di Azure in base a determinate condizioni (ad esempio, il rischio di accesso, il percorso o il dispositivo) e applicare dei requisiti come l'autenticazione a più fattori.

Per creare un criterio per la gestione di Azure, selezionare **Gestione di Microsoft Azure** in **App cloud** quando si sceglie l'app a cui applicare il criterio.

![Accesso condizionale per la gestione di Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

Il criterio creato si applica a tutti gli endpoint di gestione di Azure, tra cui il portale di Azure, il provider di Azure Resource Manager, le API Gestione dei servizi classiche, Azure PowerShell e il portale di amministrazione delle sottoscrizioni di Visual Studio. Si noti che il criterio si applica ad Azure PowerShell che chiama l'API di Azure Resource Manager. Non si applica ad [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2) che chiama Microsoft Graph.

> [!CAUTION]
> Assicurarsi di che comprendere funzionamento dell'accesso condizionale prima di configurare un criterio per gestire l'accesso alla gestione di Azure. Assicurarsi di non creare condizioni che potrebbero bloccare il proprio accesso al portale.

Per altre informazioni su come configurare e usare l'accesso condizionale, vedere [accesso condizionale in Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).