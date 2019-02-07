---
title: "Servizio di sincronizzazione Azure AD Connect: come gestire l'account del servizio Azure Active Directory | Microsoft Docs"
description: Questo argomento illustra come ripristinare l'account del servizio Azure AD.
services: active-directory
keywords: AADSTS70002, AADSTS50054, Come reimpostare la password dell'account del servizio di sincronizzazione Azure AD Connect
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: c542ba1c105d2fcdb74d5f8b1af1ecddade65a20
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55488736"
---
# <a name="azure-ad-connect-sync-how-to-manage-the-azure-ad-service-account"></a>Servizio di sincronizzazione Azure AD Connect: come gestire l'account del servizio Azure Active Directory
L'account di servizio utilizzato da Azure Active Directory Connector è progettato per non richiedere manutenzione. Questo argomento descrive come reimpostare le credenziali in caso di necessità, ad esempio se un amministratore globale ha erroneamente reimpostato la password dell'account del servizio con PowerShell.

## <a name="reset-the-credentials"></a>Reimpostazione delle credenziali
Se l'account del servizio definito in Azure Active Directory Connector non riesce a contattare Azure Active Directory a causa di problemi di autenticazione, è possibile reimpostare la password.

1. Effettuare l'accesso al server di sincronizzazione Azure AD Connect e avviare PowerShell.
2. Eseguire `Add-ADSyncAADServiceAccount`.  
   ![Cmdlet PowerShell addadsyncaadserviceaccount](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Fornire le credenziali di amministratore globale di Azure Active Directory.

Questo cmdlet reimposta la password dell'account del servizio e la aggiorna sia in Azure AD, sia nel motore di sincronizzazione.

## <a name="known-issues-these-steps-can-solve"></a>Problemi noti che possono essere risolti con questi passaggi
Questa sezione riporta un elenco di errori segnalati dai clienti che sono stati corretti reimpostando le credenziali nell'account del servizio Azure AD.

- - -
Evento 6900  
Il server ha rilevato un errore imprevisto durante l'elaborazione di una notifica di modifica password:  
AADSTS70002: Errore di convalida delle credenziali. AADSTS50054: Per l'autenticazione è stata usata la password precedente.

- - -
Evento 659  
Errore durante il recupero della configurazione della sincronizzazione dei criteri password. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: Errore di convalida delle credenziali. AADSTS50054: Per l'autenticazione è stata usata la password precedente.

## <a name="next-steps"></a>Passaggi successivi
**Argomenti generali**

* [Servizio di sincronizzazione Azure AD Connect: comprendere e personalizzare la sincronizzazione](how-to-connect-sync-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md)

