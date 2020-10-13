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
ms.openlocfilehash: d9c8f5a75fea6ddd85026407fe4784c8a4589e33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89278310"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Confronto degli strumenti di integrazione directory per la soluzione ibrida di gestione delle identità
Nel corso degli anni gli strumenti di integrazione di directory sono aumentati e si sono evoluti.  


- [FIM](/previous-versions/windows/desktop/forefront-2010/ff182370(v=vs.100)) e [MIM](/microsoft-identity-manager/microsoft-identity-manager-2016) sono ancora supportati e consentono principalmente la sincronizzazione tra sistemi locali.   Il [connettore FIM Windows Azure ad](/previous-versions/mim/dn511001(v=ws.10)) è supportato sia in FIM che in MIM, ma non è consigliato per le nuove distribuzioni: i clienti con origini locali come note o SAP HCM devono usare MIM per popolare Active Directory Domain Services (ad DS) e quindi usare la sincronizzazione Azure AD Connect o Azure ad Connect il provisioning cloud per la sincronizzazione da servizi di dominio Active directory a Azure ad.
- [Azure ad Connect Sync](how-to-connect-sync-whatis.md) incorpora i componenti e le funzionalità precedentemente rilasciati in DirSync e Azure ad Sync, per la sincronizzazione tra foreste di servizi di dominio Active directory e Azure ad.  
- [Azure ad Connect provisioning cloud](../cloud-provisioning/what-is-cloud-provisioning.md) è un nuovo agente Microsoft per la sincronizzazione da servizi di dominio Active directory a Azure ad, utile per scenari come la fusione e l'acquisizione, in cui le foreste di Active Directory della società acquisita sono isolate dalle foreste di Active Directory della società.

Per altre informazioni sulle differenze tra Azure AD Connect Sync e Azure AD Connect il provisioning nel cloud, vedere l'articolo [che cos'è Azure ad Connect provisioning nel cloud?](../cloud-provisioning/what-is-cloud-provisioning.md)

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).