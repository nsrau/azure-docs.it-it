---
title: Modificare la password dell'account del connettore Azure AD | Microsoft Docs
description: Questo argomento illustra come ripristinare l'account Azure AD Connector.
services: active-directory
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
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 7f6b623cc5f864106dc2f119308370e80014a4c2
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611025"
---
# <a name="change-the-azure-ad-connector-account-password"></a>Cambiare la password dell'account del connettore di Azure AD
L'account di Azure AD Connector dovrebbe essere gratuito. Questo argomento descrive come reimpostare le credenziali in caso di necessità, Ad esempio, se un amministratore globale ha per errore, reimpostare la password dell'account usando PowerShell.

## <a name="reset-the-credentials"></a>Reimpostazione delle credenziali
Se l'account Azure AD Connector non è in grado di contattare Azure AD a causa di problemi di autenticazione, è possibile reimpostare la password.

1. Effettuare l'accesso al server di sincronizzazione Azure AD Connect e avviare PowerShell.
2. Eseguire `Add-ADSyncAADServiceAccount`.
   ![Cmdlet PowerShell addadsyncaadserviceaccount](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Fornire le credenziali di amministratore globale di Azure Active Directory.

Questo cmdlet reimposta la password dell'account del servizio e la aggiorna sia in Azure AD, sia nel motore di sincronizzazione.

## <a name="known-issues-these-steps-can-solve"></a>Problemi noti che possono essere risolti con questi passaggi
Questa sezione è un elenco di errori segnalati dai clienti che sono stati corretti da una reimpostazione delle credenziali nell'account del connettore Azure AD.

---
Evento 6900 si è verificato un errore imprevisto durante l'elaborazione di una notifica di modifica della password: AADSTS70002: errore durante la convalida delle credenziali. AADSTS50054: Per l'autenticazione verrà usata la password precedente.

---
Evento 659 errore durante il recupero della configurazione di sincronizzazione dei criteri password. Microsoft. IdentityModel. clients. ActiveDirectory. AdalServiceException: AADSTS70002: errore durante la convalida delle credenziali. AADSTS50054: Per l'autenticazione verrà usata la password precedente.

## <a name="next-steps"></a>Passaggi successivi
**Argomenti generali**

* [Servizio di sincronizzazione Azure AD Connect: Comprendere e personalizzare la sincronizzazione](how-to-connect-sync-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md)
