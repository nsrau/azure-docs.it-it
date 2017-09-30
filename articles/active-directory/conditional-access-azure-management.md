---
title: Gestire l'accesso alla gestione di Azure con accesso condizionale in Azure Active Directory
description: Informazioni su come usare l'accesso condizionale in Azure AD per gestire l'accesso alla gestione di Azure.
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: bryanla
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/22/2017
ms.author: skwan
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: d4fa31d664209ba7fea9ee85773d0ab9efb81bed
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="manage-access-to-azure-management-with-conditional-access"></a>Gestire l'accesso alla gestione di Azure con accesso condizionale

L'accesso condizionale di Azure Active Directory (Azure AD) controlla l'accesso alle app cloud in base alle condizioni specificate dall'utente. Per consentire l'accesso, vengono creati dei criteri di accesso condizionale che consentono o bloccano l'accesso se i requisiti del criterio vengono o non vengono soddisfatti. 

In genere, l'accesso condizionale viene usato per controllare l'accesso alle app cloud. È inoltre possibile configurare i criteri per controllare l'accesso alla gestione di Azure in base a determinate condizioni (ad esempio, il rischio di accesso, il percorso o il dispositivo) e applicare dei requisiti come l'autenticazione a più fattori.

Per creare un criterio per la gestione di Azure, selezionare **Gestione di Microsoft Azure** in **App cloud** quando si sceglie l'app a cui applicare il criterio.

![Accesso condizionale per la gestione di Azure](./media/conditional-access-azure-mgmt.png)

Il criterio creato si applica a tutti gli endpoint della gestione di Azure, tra cui il portale di Azure classico, il portale di Azure, il provider di Azure Resource Manager, le API di gestione dei servizi classiche e PowerShell.

> [!CAUTION]
> È importante comprendere il funzionamento dell'accesso condizionale prima di configurare un criterio per gestire l'accesso alla gestione di Azure. Assicurarsi di non creare condizioni che potrebbero bloccare il proprio accesso al portale.

Per altre informazioni su come configurare e usare l'accesso condizionale, vedere [Accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal.md).
