---
title: Confronto degli strumenti di integrazione directory per la soluzione ibrida di gestione delle identità | Microsoft Docs
description: Questa pagina contiene tabelle complete che confrontano i vari strumenti che possono essere usati per l'integrazione directory.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/07/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 052d99a819aee415d5e7ad6dc00b8c786af0f636
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811088"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Confronto degli strumenti di integrazione directory per la soluzione ibrida di gestione delle identità
Nel corso degli anni gli strumenti di integrazione di directory sono aumentati e si sono evoluti.  


- [FIM](https://docs.microsoft.com/previous-versions/windows/desktop/forefront-2010/ff182370%28v%3dvs.100%29) e [MIM](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016) sono ancora supportati e abilitano principalmente la sincronizzazione tra sistemi locali.   [FIM Windows Azure AD Connector](https://docs.microsoft.com/previous-versions/mim/dn511001(v=ws.10)?redirectedfrom=MSDN) è supportato sia in FIM che in MIM, ma non è consigliato per le nuove distribuzioni: i clienti con origini locali come Notes o SAP HCM devono usare MIM per popolare Servizi di dominio Active Directory e quindi usare anche la sincronizzazione di Azure AD Connect o il provisioning cloud di Azure AD Connect per la sincronizzazione da Servizi di dominio Active Directory ad Azure AD.
- [La sincronizzazione](how-to-connect-sync-whatis.md) di Azure AD Connect include i componenti e le funzionalità rilasciati in precedenza in DirSync e Azure AD Sync, per la sincronizzazione tra foreste di Servizi di dominio Active Directory e Azure AD.  
- [Il provisioning cloud](../cloud-provisioning/what-is-cloud-provisioning.md) di Azure AD Connect è un nuovo agente Microsoft per la sincronizzazione da Servizi di dominio Active Directory ad Azure AD, utile per scenari quali la fusione e l'acquisizione in cui le foreste di Active Directory della società acquisita sono isolate dalle foreste di Active Directory della società padre.

Per altre informazioni sulle differenze tra la sincronizzazione di Azure AD Connect e il provisioning cloud di Azure AD Connect, vedere l'articolo [Che cos'è](../cloud-provisioning/what-is-cloud-provisioning.md) il provisioning cloud di Azure AD Connect?

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).

