---
title: Che cos'è il provisioning tra directory con Azure Active Directory? | Microsoft Docs
description: Panoramica del provisioning di identità tra directory.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cbca2d00d10b6595b8a1d13251dd88c65667541
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135007"
---
# <a name="what-is-inter-directory-provisioning"></a>Che cos'è il provisioning tra directory?

Una directory è un'infrastruttura di informazioni condivisa che consente di individuare, gestire, amministrare e organizzare elementi e risorse di rete.  Le applicazioni che usano servizi directory sono ad esempio Microsoft Active Directory e Azure AD.  Con le identità, i sistemi di directory determinano ad esempio chi ha accesso a cosa e chi è autorizzato a usare specifiche risorse.

Il provisioning tra directory di un'identità viene effettuato tra due diversi sistemi di servizi directory.   Lo scenario più comune per il provisioning tra directory è quando un utente già presente in Active Directory viene sottoposto a provisioning in Azure AD. Questo tipo provisioning può essere effettuato da agenti come il servizio di sincronizzazione di Azure AD Connect o il provisioning cloud di Azure AD Connect.

Il provisioning tra directory consente di creare ambienti di [identità ibride](../hybrid/whatis-hybrid-identity.md).


## <a name="what-types-of-inter-directory-provisioning-does-azure-ad-support"></a>Quali tipi di provisioning tra directory sono supportati da Azure AD

Azure AD attualmente supporta tre metodi per effettuare il provisioning tra directory. Questi metodi sono:

- [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md): lo strumento Microsoft progettato per soddisfare e realizzare identità ibride, tra cui il provisioning tra directory da Active Directory ad Azure AD.

- [Provisioning cloud di Azure AD Connect](../cloud-provisioning/what-is-cloud-provisioning.md): un nuovo agente Microsoft progettato per soddisfare e realizzare gli obiettivi di identità ibride.  Offre un'esperienza di provisioning tra directory leggera tra Active Directory e Azure AD.

- [Microsoft Identity Manager](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016): la soluzione Microsoft di gestione delle identità e degli accessi in locale che consente di gestire utenti, credenziali, criteri e accessi all'interno dell'organizzazione. Inoltre, Microsoft Identity Manager offre un provisioning tra directory avanzato per realizzare ambienti di identità ibride per Active Directory, Azure AD e altre directory.

### <a name="key-benefits"></a>Vantaggi principali

Le funzionalità del provisioning tra directory offrono alle aziende i seguenti vantaggi significativi:

- [Sincronizzazione dell'hash delle password](../hybrid/whatis-phs.md): metodo di accesso che consente di sincronizzare un hash di una password utente AD locale con Azure AD.
- [Autenticazione pass-through](../hybrid/how-to-connect-pta.md): metodo di accesso che consente agli utenti di usare la stessa password in locale e nel cloud, ma non richiede l'infrastruttura aggiuntiva di un ambiente federato.
- [Integrazione della federazione](../hybrid/how-to-connect-fed-whatis.md): può essere usata per configurare un ambiente ibrido usando un'infrastruttura AD FS locale. Fornisce anche funzionalità di gestione di AD FS, ad esempio rinnovo dei certificati e distribuzioni aggiuntive di server AD FS.
- [Sincronizzazione](../hybrid/how-to-connect-sync-whatis.md): è responsabile della creazione di utenti, gruppi e altri oggetti.  Deve anche garantire che le informazioni sulle identità per utenti e gruppi locali corrispondano a quelle nel cloud.  Questa sincronizzazione include anche gli hash delle password.
- [Monitoraggio dell'integrità](../hybrid/whatis-hybrid-identity-health.md): può offrire un monitoraggio affidabile e una posizione centralizzata nel portale di Azure per visualizzare questa attività. 


## <a name="next-steps"></a>Passaggi successivi 
- [Che cos'è la gestione del ciclo di vita delle identità?](what-is-identity-lifecycle-management.md)
- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è il provisioning basato su risorse umane?](what-is-hr-driven-provisioning.md)
- [Che cos'è il provisioning di app?](what-is-app-provisioning.md)
