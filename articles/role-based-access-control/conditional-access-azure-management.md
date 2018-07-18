---
title: Gestire l'accesso alla gestione di Azure con accesso condizionale in Azure Active Directory
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
ms.date: 09/22/2017
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 083cb4eb84746f4a61b51f3573a0bf66110fe1ee
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37435049"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Gestire l'accesso alla gestione di Azure con accesso condizionale

L'accesso condizionale di Azure Active Directory (Azure AD) controlla l'accesso alle app cloud in base alle condizioni specificate dall'utente. Per consentire l'accesso, vengono creati dei criteri di accesso condizionale che consentono o bloccano l'accesso se i requisiti del criterio vengono o non vengono soddisfatti. 

In genere, l'accesso condizionale viene usato per controllare l'accesso alle app cloud. È inoltre possibile configurare i criteri per controllare l'accesso alla gestione di Azure in base a determinate condizioni (ad esempio, il rischio di accesso, il percorso o il dispositivo) e applicare dei requisiti come l'autenticazione a più fattori.

Per creare un criterio per la gestione di Azure, selezionare **Gestione di Microsoft Azure** in **App cloud** quando si sceglie l'app a cui applicare il criterio.

![Accesso condizionale per la gestione di Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

Il criterio creato si applica a tutti gli endpoint di gestione di Azure, tra cui il portale di Azure classico, il portale di Azure, il provider di Azure Resource Manager, le API Gestione dei servizi classiche e Azure PowerShell.

> [!CAUTION]
> È importante comprendere il funzionamento dell'accesso condizionale prima di configurare un criterio per gestire l'accesso alla gestione di Azure. Assicurarsi di non creare condizioni che potrebbero bloccare il proprio accesso al portale.

Per altre informazioni su come configurare e usare l'accesso condizionale, vedere [Accesso condizionale in Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).