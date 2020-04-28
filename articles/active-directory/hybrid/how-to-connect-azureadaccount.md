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
ms.openlocfilehash: 0ea151ee79fccd66f1d9422744d8f57829677ec0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67204522"
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
Evento 6900  
Il server ha rilevato un errore imprevisto durante l'elaborazione di una notifica di modifica password:  
AADSTS70002: Errore di convalida delle credenziali. AADSTS50054: Per l'autenticazione verrà usata la password precedente.

---
Evento 659  
Errore durante il recupero della configurazione della sincronizzazione dei criteri password. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: Errore di convalida delle credenziali. AADSTS50054: Per l'autenticazione verrà usata la password precedente.

## <a name="next-steps"></a>Passaggi successivi
**Argomenti generali**

* [Servizio di sincronizzazione Azure AD Connect: Comprendere e personalizzare la sincronizzazione](how-to-connect-sync-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md)

