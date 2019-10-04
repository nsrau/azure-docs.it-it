---
title: Informazioni sulla federazione con Azure AD | Microsoft Docs
description: Informazioni sulla federazione con Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52f9832a4307ea9519df448f192eeca70043a6d6
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035179"
---
# <a name="what-is-federation-with-azure-ad"></a>Informazioni sulla federazione con Azure AD

Per federazione si intende una raccolta di domini che hanno stabilito una relazione di trust. Il livello di attendibilità può variare, ma in genere include l'autenticazione e quasi sempre l'autorizzazione. Una federazione tipica può includere numerose organizzazioni che hanno stabilito una relazione di trust per l'accesso condiviso per un set di risorse.

È possibile definire la federazione per l'ambiente locale con Azure AD e usare tale federazione per l'autenticazione e l'autorizzazione.  Questo metodo di accesso garantisce che qualsiasi operazione di autenticazione utente venga eseguita in locale.  Questo metodo consente agli amministratori di implementare livelli di controllo di accesso più rigorosi. La federazione tramite AD FS e PingFederate è disponibile.

![Identità federata](./media/whatis-hybrid-identity/federated-identity.png)


> [!TIP]
> Se si decide di usare la federazione con Active Directory Federation Services (AD FS), è possibile configurare facoltativamente la sincronizzazione dell'hash delle password come backup in caso di errore dell'infrastruttura di AD FS.


## <a name="next-steps"></a>Fasi successive

- [Informazioni sull'identità ibrida](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/whatis-hybrid-identity)
- [Informazioni su Azure AD Connect e Connect Health](whatis-azure-ad-connect.md)
- [Informazioni sulla sincronizzazione dell'hash delle password](whatis-phs.md)
- [Informazioni sulla federazione](whatis-fed.md)
- [Informazioni su Single Sign-On](how-to-connect-sso.md)
- [Funzionamento della federazione](how-to-connect-fed-whatis.md)
- [Federazione con PingFederate](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)
