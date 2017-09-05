---
title: Configurazione dei criteri di accesso condizionale basato su dispositivo di Azure Active Directory | Microsoft Docs
description: Informazioni su come configurare i criteri di accesso condizionale basato su dispositivo di Azure Active Directory.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: a26c40351c6b982fd90acb4bf06220ef3f79f399
ms.contentlocale: it-it
ms.lasthandoff: 08/23/2017

---
# <a name="configure-azure-active-directory-device-based-conditional-access-policies"></a>Configurazione dei criteri di accesso condizionale basato su dispositivo di Azure Active Directory

Con l'[accesso condizionale di Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), è possibile ottimizzare come gli utenti autorizzati possono accedere alle risorse. Ad esempio, possibile limitare l'accesso a determinate risorse a dispositivi attendibili. Per criterio di accesso condizionale che richiede un dispositivo attendibile si intende anche un criterio di accesso condizionale basato su dispositivo.

Questo argomento fornisce informazioni su come configurare i criteri di accesso condizionale basati su dispositivo per le applicazioni connesse ad Azure AD. 


## <a name="before-you-begin"></a>Prima di iniziare

Collegamenti di accesso condizionale basato su dispositivo **Accesso condizionale ad Azure AD** e **Gestione dei dispositivi AD Azure insieme**. Se non si ha ancora familiarità con una di queste aree, è consigliabile leggere innanzitutto i seguenti argomenti:

- **[Accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal.md)**: questo argomento offre una panoramica concettuale di accesso condizionale e la terminologia correlata.

- **[Introduzione alla gestione dei dispositivi in Azure Active Directory](device-management-introduction.md)**: questo argomento offre una panoramica delle diverse opzioni a disposizione per connettere i dispositivi con Azure AD. 


## <a name="trusted-devices"></a>Dispositivi attendibili

In un mondo in cui i dispositivi mobili e il cloud hanno sempre più importanza, Azure Active Directory consente ovunque l'accesso Single Sign-On a dispositivi, app e servizi. Per alcune risorse nel proprio ambiente, la concessione dell'accesso agli utenti appropriati potrebbe non essere sufficiente. Oltre agli utenti appropriati, si potrebbe anche richiedere l'utilizzo di un dispositivo attendibile per accedere a una risorsa. Nell'ambiente in uso, è possibile definire su cosa di basa un dispositivo attendibile sui componenti seguenti:

- Le [piattaforme del dispositivo](active-directory-conditional-access-azure-portal.md#device-platforms) su un dispositivo
- Se un dispositivo è conforme
- Se un dispositivo è aggiunto al dominio 

Le [piattaforme del dispositivo](active-directory-conditional-access-azure-portal.md#device-platforms) sono caratterizzate dal sistema operativo in esecuzione sul dispositivo. Nei criteri di accesso condizionale basato su dispositivo, è possibile limitare l'accesso a determinate risorse a specifiche piattaforme del dispositivo.



In un criterio di accesso condizionale basato su dispositivo, è possibile richiedere ai dispositivi attendibili di essere contrassegnati come conforme.

![App cloud](./media/active-directory-conditional-access-policy-connected-applications/24.png)

I dispositivi possono essere contrassegnati come conformi nella directory da:

- Intune 
- Un sistema di gestione di dispositivi mobili di terze parti che si integra con Azure AD  

Solo i dispositivi connessi ad Azure AD possono essere contrassegnati come conformi. Per connettere un dispositivo ad Azure Active Directory, sono disponibili le opzioni seguenti: 

- Registrazione in Azure AD
- Aggiunta ad Azure AD
- Aggiunta all'identità ibrida di Azure AD

    ![App cloud](./media/active-directory-conditional-access-policy-connected-applications/26.png)

Se si dispone di un footprint di Active Directory (AD) locale, è possibile che i dispositivi non connessi ad Azure AD ma che fanno parte di Active Directory devono essere considerati attendibile.

![App cloud](./media/active-directory-conditional-access-policy-connected-applications/25.png)


## <a name="next-steps"></a>Passaggi successivi

Prima di configurare un criterio di accesso condizionale basato su dispositivo nell'ambiente in uso, è necessario esaminare [Procedure consigliate per l'accesso condizionale in Azure Active Directory](active-directory-conditional-access-best-practices.md).


