---
title: Vulnerabilità rilevate da Azure Active Directory Identity Protection
description: Panoramica delle vulnerabilità rilevate da Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 446e2ef33b714afe06f24f3dbfc46bc6a42790b8
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335162"
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Vulnerabilità rilevate da Azure Active Directory Identity Protection

Le vulnerabilità sono punti deboli in un ambiente che possono essere sfruttati da un utente malintenzionato. È consigliabile che gli amministratori affrontino queste vulnerabilità per migliorare il comportamento di sicurezza dell'organizzazione.

![Vulnerabilità segnalate da Identity Protection](./media/vulnerabilities/identity-protection-vulnerabilities.png)

Le sezioni seguenti presentano una panoramica delle vulnerabilità segnalate da Identity Protection.

## <a name="multi-factor-authentication-registration-not-configured"></a>Registrazione di autenticazione a più fattori non configurata

Questa vulnerabilità consente di valutare la distribuzione dell'autenticazione a più fattori di Azure nell'organizzazione.

Azure Multifactor Authentication fornisce un secondo livello di sicurezza per l'autenticazione utente. Consente di proteggere l'accesso ai dati e alle applicazioni dell'utente, garantendo al tempo stesso una procedura di accesso semplice. Azure Multifactor Authentication offre opzioni di verifica facili da usare, ad esempio:

* Telefonata
* SMS
* Notifica dell'app per dispositivi mobili
* Codice di verifica OTP

È consigliabile richiedere l'autenticazione a più fattori di Azure per l'accesso degli utenti. L'autenticazione a più fattori gioca un ruolo chiave nei criteri di accesso condizionale basati sul rischio disponibili tramite Identity Protection.

Per altre informazioni, vedere [Informazioni su Azure Multi-Factor Authentication](../authentication/multi-factor-authentication.md).

## <a name="unmanaged-cloud-apps"></a>App cloud non gestite

Questa vulnerabilità consente di identificare le app per cloud non gestite all'interno dell'organizzazione.

Il personale IT spesso non è a conoscenza di tutte le applicazioni cloud nell'organizzazione. L'accesso non autorizzato ai dati aziendali, le possibili perdite di dati e altri rischi di sicurezza rappresentano una preoccupazione per gli amministratori.

Si consiglia di distribuire Cloud Discovery per individuare le applicazioni cloud non gestite e di gestire queste applicazioni usando Azure Active Directory.

Per altre informazioni, vedere [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="security-alerts-from-privileged-identity-management"></a>Avvisi di sicurezza da Privileged Identity Management

Questa vulnerabilità permette di identificare e risolvere gli avvisi sulle identità con privilegi all'interno dell'organizzazione.  

Per consentire agli utenti di eseguire operazioni con privilegi, le organizzazioni devono concedere agli utenti accessi con privilegi temporanei o permanenti in Azure AD, per le risorse di Azure o Office 365 o per altre app SaaS. Ognuno di questi utenti con privilegi aumenta la superficie di attacco dell'organizzazione. Questa vulnerabilità consente di identificare gli utenti che hanno un accesso con privilegi non necessario e di intraprendere le azioni appropriate per ridurre o eliminare il rischio.

Si consiglia alle organizzazioni di usare Azure AD Privileged Identity Management per gestire, controllare e monitorare le identità con privilegi in Azure AD e in altri Servizi online Microsoft, ad esempio Office 365 o Microsoft Intune.

Per altre informazioni, vedere [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md).

## <a name="see-also"></a>Vedere anche

[Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
