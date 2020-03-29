---
title: Che cos'è un dispositivo aggiunto ad Azure AD?
description: Informazioni su come le identità dei dispositivi semplificano la gestione dei dispositivi che accedono alle risorse nell'ambiente.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40f89fbe19b93601f9e0525f0387e402bd175fe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672672"
---
# <a name="azure-ad-joined-devices"></a>Dispositivi aggiunti ad Azure AD

L'aggiunta ad Azure AD è destinata alle organizzazioni che vogliono essere con la prima del cloud o solo nel cloud. Qualsiasi organizzazione può distribuire dispositivi aggiunti ad Azure AD indipendentemente dalle dimensioni o dal settore. L'aggiunta ad Azure AD funziona anche in un ambiente ibrido, consentendo l'accesso alle app e alle risorse cloud e locali.

|   | Aggiunta ad Azure AD |
| --- | --- |
| **Definizione** | Aggiunta solo ad Azure AD che richiede l'accesso al dispositivo da parte dell'account aziendale |
| **Destinatari principali** | Adatto sia per le organizzazioni solo cloud che per le organizzazioni ibride. |
|   | Applicabile a tutti gli utenti di un'organizzazione |
| **Proprietà del dispositivo** | Organization |
| **Sistemi operativi** | Tutti i dispositivi Windows 10 |
| **Provisioning** | Self-service: Configurazione configurazione o impostazioni di Windows |
|   | Registrazione in blocco |
|   | Windows Autopilot |
| **Opzioni di accesso al dispositivo** | Account dell'organizzazione che utilizzano: |
|   | Password |
|   | Windows Hello for Business |
|   | Chiavi di protezione FIDO2.0 (anteprima)FIDO2.0 security keys (preview) |
| **Gestione del dispositivo** | Gestione dei dispositivi mobili (esempio: Microsoft Intune)Mobile Device Management (example: Microsoft Intune) |
|   | Co-gestione con Microsoft Intune e Gestione configurazione endpoint Microsoft |
| **Funzionalità principali** | SSO per le risorse cloud e locali |
|   | Accesso condizionale tramite la registrazione MDM e la valutazione della conformità MDMConditional Access through MDM enrollment and MDM compliance evaluation |
|   | Reimpostazione password self-service e reimpostazione del PIN di Windows Hello nella schermata di blocco |
|   | Enterprise State Roaming tra più dispositivi |

I dispositivi aggiunti ad Azure AD hanno eseguito l'accesso all'uso di un account Azure AD dell'organizzazione. L'accesso alle risorse nell'organizzazione può essere ulteriormente limitato in base all'account di Azure AD e [ai criteri](../conditional-access/overview.md) di accesso condizionale applicati all'identità del dispositivo.

Gli amministratori possono proteggere e controllare ulteriormente i dispositivi aggiunti ad Azure AD usando strumenti di gestione dei dispositivi mobili (MDM) come Microsoft Intune o in scenari di co-gestione tramite Microsoft Endpoint Configuration Manager.Administrators can secure and further control Azure AD joined devices using Mobile Device Management (MDM) tools like Microsoft Intune or in co-management scenarios using Microsoft Endpoint Configuration Manager. Questi strumenti forniscono un mezzo per applicare le configurazioni richieste dall'organizzazione, ad esempio richiedere la crittografia dell'archiviazione, la complessità delle password, le installazioni software e gli aggiornamenti software. Gli amministratori possono rendere le applicazioni dell'organizzazione disponibili per i dispositivi aggiunti ad Azure AD usando Configuration Manager per [gestire le app da Microsoft Store per le aziende e la formazione](/configmgr/apps/deploy-use/manage-apps-from-the-windows-store-for-business).

L'aggiunta ad Azure AD può essere eseguita usando opzioni self-service come la Configurazione in conto lavoro, la registrazione in blocco o [Windows Autopilot](/intune/enrollment-autopilot).

I dispositivi aggiunti ad Azure AD possono comunque mantenere l'accesso Single Sign-On alle risorse locali quando si trovano nella rete dell'organizzazione. I dispositivi aggiunti ad Azure AD possono comunque eseguire l'autenticazione in server locali, ad esempio file, stampa e altre applicazioni.

## <a name="scenarios"></a>Scenari

Anche se l'aggiunta ad Azure AD è destinata soprattutto alle organizzazioni prive di un'infrastruttura Active Directory di Windows Server locale, è ovviamente possibile usarla in scenari in cui:

- Si vuole passare all'infrastruttura basata su cloud mediante Azure AD e MDM come Intune.
- Non è possibile usare un'aggiunta a un dominio locale, ad esempio se è necessario avere il controllo di dispositivi mobili come tablet e telefoni.
- Gli utenti hanno soprattutto necessità di accedere a Office 365 o ad altre app SaaS integrate con Azure AD.
- Si vuole gestire un gruppo di utenti in Azure AD invece che in Active Directory, Questo scenario può essere applicato, ad esempio, a lavoratori stagionali, appaltatori o studenti.
- Si vogliono fornire funzionalità di join ai lavoratori in succursali remote con un'infrastruttura locale limitata.

È possibile configurare dispositivi aggiunti ad Azure AD per dispositivi Windows 10.

L'obiettivo dei dispositivi aggiunti ad Azure AD è di semplificare:

- Distribuzioni Windows di dispositivi di proprietà dell'azienda
- Accesso ad app e risorse aziendali da qualsiasi dispositivo Windows
- Gestione basata su cloud di dispositivi di proprietà dell'azienda
- Gli utenti possono accedere ai propri dispositivi con i propri account aziendali o aziendali di Active Directory sincronizzati.

![Dispositivi aggiunti ad Azure AD](./media/concept-azure-ad-join/azure-ad-joined-device.png)

È possibile distribuire Aggiunta ad Azure AD usando uno dei metodi seguenti:

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot)
- [Distribuzione in blocco](/intune/windows-bulk-enroll)
- [Esperienza self-service](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>Passaggi successivi

- [Pianificare l'implementazione dell'aggiunta ad Azure AD](azureadjoin-plan.md)
- [Come gestire il gruppo di amministratori locale nei dispositivi aggiunti ad Azure AD](assign-local-admin.md)
- [Gestire le identità dei dispositivi tramite il portale di AzureManage device identities using the Azure portal](device-management-azure-portal.md)
- [Gestire i dispositivi non aggiornati in Azure AD](manage-stale-devices.md)
