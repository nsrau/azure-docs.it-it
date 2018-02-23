---
title: Configurare i criteri di accesso condizionale basato su dispositivo di Azure Active Directory | Microsoft Docs
description: Informazioni su come configurare i criteri di accesso condizionale basato su dispositivo di Azure Active Directory.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 2354a8bf81189f70bb8d0d63c3df3236403c11fc
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="configure-azure-active-directory-device-based-conditional-access-policies"></a>Configurare i criteri di accesso condizionale basato su dispositivo di Azure Active Directory

Con l'[accesso condizionale di Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), è possibile ottimizzare la modalità in cui gli utenti autorizzati possono accedere alle risorse. Ad esempio, è possibile limitare l'accesso a determinate risorse a dispositivi attendibili. Un criterio di accesso condizionale che richiede un dispositivo attendibile è noto anche come criterio di accesso condizionale basato su dispositivo.

Questo argomento fornisce informazioni su come configurare i criteri di accesso condizionale basati su dispositivo per le applicazioni connesse ad Azure AD. 


## <a name="before-you-begin"></a>Prima di iniziare

Collegamenti di accesso condizionale basato su dispositivo **Accesso condizionale ad Azure AD** e **Gestione dei dispositivi AD Azure insieme**. Se non si ha ancora familiarità con una di queste aree, è consigliabile leggere innanzitutto gli argomenti seguenti:

- **[Accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal.md)**: questo argomento offre una panoramica concettuale di accesso condizionale e la terminologia correlata.

- **[Introduzione alla gestione dei dispositivi in Azure Active Directory](device-management-introduction.md)**: questo argomento offre una panoramica delle diverse opzioni a disposizione per connettere i dispositivi con Azure AD. 


## <a name="trusted-devices"></a>Dispositivi attendibili

In un mondo in cui i dispositivi mobili e il cloud hanno sempre più importanza, Azure Active Directory consente ovunque l'accesso Single Sign-On a dispositivi, app e servizi. Per alcune risorse nel proprio ambiente, la concessione dell'accesso agli utenti appropriati potrebbe non essere sufficiente. Oltre agli utenti appropriati, potrebbe anche essere necessario l'uso di un dispositivo attendibile per accedere a una risorsa. Nell'ambiente in uso, è possibile definire cos'è un dispositivo attendibile sulla base dei componenti seguenti:

- Le [piattaforme del dispositivo](active-directory-conditional-access-conditions.md#device-platforms)
- Se un dispositivo è conforme
- Se un dispositivo è aggiunto al dominio 

Le [piattaforme del dispositivo](active-directory-conditional-access-conditions.md#device-platforms) sono caratterizzate dal sistema operativo in esecuzione sul dispositivo. Nei criteri di accesso condizionale basato su dispositivo, è possibile limitare l'accesso a determinate risorse solo a piattaforme specifiche del dispositivo.



In un criterio di accesso condizionale basato su dispositivo, è possibile richiedere ai dispositivi attendibili di essere contrassegnati come conforme.

![App cloud](./media/active-directory-conditional-access-policy-connected-applications/24.png)

I dispositivi possono essere contrassegnati come conformi nella directory da:

- Intune 
- Un sistema gestito di dispositivi mobili di terze parti che gestisce i dispositivi Windows 10 tramite l'integrazione di Azure AD 
 
  

Solo i dispositivi connessi ad Azure AD possono essere contrassegnati come conformi. Per connettere un dispositivo ad Azure Active Directory, sono disponibili le opzioni seguenti: 

- Registrazione in Azure AD
- Aggiunta ad Azure AD
- Aggiunta all'identità ibrida di Azure AD

    ![App cloud](./media/active-directory-conditional-access-policy-connected-applications/26.png)

Se si dispone di un footprint di Active Directory (AD) locale, è possibile che i dispositivi non connessi ad Azure AD ma che fanno parte di Active Directory devono essere considerati attendibile.

![App cloud](./media/active-directory-conditional-access-policy-connected-applications/25.png)


## <a name="next-steps"></a>Passaggi successivi

Prima di configurare un criterio di accesso condizionale basato su dispositivo nell'ambiente in uso, è necessario esaminare [Procedure consigliate per l'accesso condizionale in Azure Active Directory](active-directory-conditional-access-best-practices.md).

