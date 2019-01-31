---
title: Vulnerabilità rilevate da Azure Active Directory Identity Protection | Documentazione Microsoft
description: Panoramica delle vulnerabilità rilevate da Azure Active Directory Identity Protection
services: active-directory
keywords: Azure Active Directory Identity Protection, sicurezza, Cloud Discovery, gestione applicazioni, rischio, livello di rischio, vulnerabilità, criteri di sicurezza
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: 92233a5b-cb34-4d28-88cc-d5d29c0f3256
ms.service: active-directory
ms.subservice: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 41635ce1d03709874298731d2da3ef63dd3cd02c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55191824"
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Vulnerabilità rilevate da Azure Active Directory Identity Protection
Le vulnerabilità sono punti deboli in un ambiente che possono essere sfruttati da un utente malintenzionato. È consigliabile risolvere le vulnerabilità per migliorare le condizioni di sicurezza dell'organizzazione e impedire che vengano sfruttate da utenti malintenzionati.


![vulnerabilità](./media/vulnerabilities/101.png "vulnerabilità")



Le sezioni seguenti presentano una panoramica delle vulnerabilità segnalate da Identity Protection.

## <a name="multi-factor-authentication-registration-not-configured"></a>Registrazione per l'autenticazione a più fattori non configurata
Questa vulnerabilità consente di controllare la distribuzione di Azure Multi-Factor Authentication all'interno dell'organizzazione. 

L'autenticazione a più fattori di Azure fornisce un secondo livello di sicurezza per l'autenticazione utente. Consente di proteggere l'accesso ai dati e alle applicazioni dell'utente, garantendo al tempo stesso una procedura di accesso semplice. Offre autenticazione avanzata tramite una gamma di semplici opzioni di verifica, ad esempio una telefonata, un SMS, una notifica dell'app mobile o un codice di verifica e token OATH di terze parti.

È consigliabile richiedere l'autenticazione a più fattori di Azure per l'accesso degli utenti. L'autenticazione a più fattori svolge un ruolo chiave nei criteri di accesso condizionale basati sul rischio disponibili tramite Identity Protection.

Per altre informazioni, vedere [Informazioni su Azure Multi-Factor Authentication](../authentication/multi-factor-authentication.md).

## <a name="unmanaged-cloud-apps"></a>App per cloud non gestite
Questa vulnerabilità consente di identificare le app per cloud non gestite all'interno dell'organizzazione.

Nelle aziende moderne, i reparti IT spesso non sono a conoscenza di tutte le applicazioni cloud usate per lavoro dagli utenti dell'organizzazione. L'accesso non autorizzato ai dati aziendali, le possibili perdite di dati e altri rischi di sicurezza rappresentano una preoccupazione per gli amministratori. 

È consigliabile distribuire Cloud Discovery per identificare le applicazioni cloud non gestite e gestirle con Azure Active Directory.

Per altre informazioni, vedere [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="security-alerts-from-privileged-identity-management"></a>Avvisi di sicurezza di Privileged Identity Management
Questa vulnerabilità permette di identificare e risolvere gli avvisi sulle identità con privilegi all'interno dell'organizzazione.  

Per consentire agli utenti di eseguire operazioni con privilegi, le organizzazioni devono concedere agli utenti accessi con privilegi temporanei o permanenti in Azure AD, per le risorse di Azure o Office 365 o per altre app SaaS. Ognuno di questi utenti con privilegi aumenta la superficie di attacco dell'organizzazione. Questa vulnerabilità consente di identificare gli utenti che hanno un accesso con privilegi non necessario e di intraprendere le azioni appropriate per ridurre o eliminare il rischio. 

È consigliabile usare Azure AD Privileged Identity Management nell'organizzazione per gestire, controllare e monitorare le identità con privilegi e il relativo accesso alle risorse in Azure AD e in altri Microsoft Online Services, ad esempio Office 365 o Microsoft Intune.

Per altre informazioni, vedere [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md). 

## <a name="see-also"></a>Vedere anche 

[Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

