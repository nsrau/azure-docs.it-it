---
title: Configurare località denominate in Azure Active Directory | Microsoft Docs
description: Informazioni su come configurare località denominate.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.subservice: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: df45ab0a7b1729ae6c1602c9769cd5b6da26f6ac
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014349"
---
# <a name="quickstart-configure-named-locations-in-azure-active-directory"></a>Guida introduttiva: Configurare località denominate in Azure Active Directory

Con le località denominate è possibile etichettare gli intervalli di indirizzi IP attendibili all'interno dell'organizzazione. Azure AD usa le località denominate per:
- Rilevare i falsi positivi nei [rilevamenti dei rischi](concept-risk-events.md). L'accesso da una posizione attendibile riduce il rischio di accesso dell'utente.   
- Configurare l'[Accesso condizionale basato sulla località](../conditional-access/location-condition.md).

In questa guida introduttiva si apprenderà come configurare le località denominate nell'ambiente in uso.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione introduttiva, sono necessari gli elementi seguenti:

* Un tenant di Azure AD. Iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/trial/get-started-active-directory/). 
* Un utente con il ruolo di amministratore globale per il tenant.
* Un intervallo IP stabilito e attendibile nell'organizzazione. L'intervallo IP deve essere in formato **CIDR (Classless Inter-Domain Routing)** .

## <a name="configure-named-locations"></a>Configurare località denominate

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel riquadro sinistro selezionare **Azure Active Directory**, quindi selezionare **Accesso condizionale** nella sezione **Sicurezza**.

    ![Scheda Accesso condizionale](./media/quickstart-configure-named-locations/entrypoint.png)

3. Nella pagina **Accesso condizionale** selezionare **Località denominate** e quindi **Nuova località**.

    ![Località denominate](./media/quickstart-configure-named-locations/namedlocation.png)

6. Compilare il modulo nella pagina nuova. 

   * Nella casella **Nome** digitare un nome per la località denominata.
   * Nella casella **Intervalli IP** digitare l'intervallo IP in formato CIDR.  
   * Fare clic su **Create**(Crea).
    
     ![Pannello Nuovo](./media/quickstart-configure-named-locations/61.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere:

- [Accesso condizionale di Azure AD](../active-directory-conditional-access-azure-portal.md).
- [Condizioni della posizione nell'Accesso condizionale di Azure AD](../conditional-access/location-condition.md)
- [Report sugli accessi a rischio](concept-risky-sign-ins.md).  
