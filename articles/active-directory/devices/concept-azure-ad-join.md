---
title: Cosa è un dispositivo aggiunto ad Azure AD
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
ms.openlocfilehash: 8e4521cb67ae95a1cd4a3e728a13e43bfd5773ab
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83737028"
---
# <a name="azure-ad-joined-devices"></a>Dispositivi aggiunti ad Azure AD

L'aggiunta ad Azure AD è destinata alle organizzazioni che vogliono essere basate prima di tutto o solo sul cloud. Qualsiasi organizzazione può distribuire dispositivi aggiunti ad Azure AD indipendentemente dalle dimensioni o dal settore. L'aggiunta ad Azure AD funziona anche in un ambiente ibrido e può consentire l'accesso alle app e alle risorse sia sul cloud sia locali.

|   | Aggiunta ad Azure AD |
| --- | --- |
| **Definizione** | Aggiunto solo a un'istanza di Azure AD che richiede l'account aziendale per accedere al dispositivo |
| **Destinatari principali** | Adatto a organizzazioni ibride e basate solo sul cloud. |
|   | Applicabile a tutti gli utenti di un'organizzazione |
| **Proprietà del dispositivo** | Organization |
| **Sistemi operativi** | Tutti i dispositivi Windows 10 |
| **Provisioning** | Self-service: Configurazione guidata o impostazioni di Windows |
|   | Registrazione in blocco |
|   | Windows Autopilot |
| **Opzioni di accesso del dispositivo** | Account aziendale che usa: |
|   | Password |
|   | Windows Hello for Business |
|   | Chiavi di sicurezza FIDO2.0 (anteprima) |
| **Gestione del dispositivo** | Mobile Device Management (esempio: Microsoft Intune) |
|   | Co-gestione con Microsoft Intune e Microsoft Endpoint Configuration Manager |
| **Funzionalità principali** | SSO per le risorse cloud e locali |
|   | Accesso condizionale tramite la registrazione MDM e la valutazione della conformità MDM |
|   | Reimpostazione della password self-service e ripristino del PIN di Windows Hello nella schermata di blocco |
|   | Enterprise State Roaming su più dispositivi |

L'accesso ai dispositivi aggiunti ad Azure AD viene eseguito tramite un account di Azure AD aziendale. L'accesso alle risorse nell'organizzazione può essere ulteriormente limitato in base a tale account di Azure AD e ai [criteri di accesso condizionale](../conditional-access/howto-conditional-access-policy-compliant-device.md) applicati all'identità del dispositivo.

Gli amministratori possono proteggere e controllare ulteriormente i dispositivi aggiunti ad Azure AD usando strumenti di Mobile Device Management (MDM) come Microsoft Intune o usando Microsoft Endpoint Configuration Manager in scenari di co-gestione. Questi strumenti forniscono un mezzo per applicare le configurazioni necessarie per l'organizzazione, ad esempio la crittografia della risorsa di archiviazione, la complessità delle password, le installazioni software e gli aggiornamenti software. Gli amministratori possono rendere le applicazioni dell'organizzazione disponibili nei dispositivi aggiunti ad Azure AD usando Configuration Manager per [gestire le app dal Microsoft Store per le aziende e la formazione](/configmgr/apps/deploy-use/manage-apps-from-the-windows-store-for-business).

L'aggiunta ad Azure AD può essere eseguita usando opzioni self-service come la Configurazione guidata, la registrazione in blocco o [Windows Autopilot](/intune/enrollment-autopilot).

I dispositivi aggiunti ad Azure AD possono comunque mantenere l'accesso Single Sign-On per le risorse locali che si trovano nella rete dell'organizzazione. I dispositivi aggiunti ad Azure AD possono comunque eseguire l'autenticazione ai server locali, ad esempio file, applicazioni di stampa e altre applicazioni.

## <a name="scenarios"></a>Scenari

Anche se l'aggiunta ad Azure AD è destinata soprattutto alle organizzazioni prive di un'infrastruttura Active Directory di Windows Server locale, è ovviamente possibile usarla in scenari in cui:

- Si vuole passare all'infrastruttura basata su cloud mediante Azure AD e MDM come Intune.
- Non è possibile usare un'aggiunta a un dominio locale, ad esempio se è necessario avere il controllo di dispositivi mobili come tablet e telefoni.
- Gli utenti hanno soprattutto necessità di accedere a Office 365 o ad altre app SaaS integrate con Azure AD.
- Si vuole gestire un gruppo di utenti in Azure AD invece che in Active Directory, ad esempio lavoratori stagionali, terzisti o studenti.
- Si vogliono fornire funzionalità di join ai lavoratori in succursali remote con un'infrastruttura locale limitata.

È possibile configurare dispositivi aggiunti ad Azure AD per dispositivi Windows 10.

L'obiettivo dei dispositivi aggiunti ad Azure AD è di semplificare:

- Distribuzioni Windows di dispositivi di proprietà dell'azienda
- Accesso ad app e risorse aziendali da qualsiasi dispositivo Windows
- Gestione basata su cloud di dispositivi di proprietà dell'azienda
- Accesso degli utenti ai dispositivi tramite il proprio account aziendale o dell'istituto di istruzione di Active Directory.

![Dispositivi aggiunti ad Azure AD](./media/concept-azure-ad-join/azure-ad-joined-device.png)

È possibile distribuire Aggiunta ad Azure AD usando uno dei metodi seguenti:

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot)
- [Distribuzione in blocco](/intune/windows-bulk-enroll)
- [Esperienza self-service](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>Passaggi successivi

- [Pianificare l'implementazione dell'aggiunta ad Azure AD](azureadjoin-plan.md)
- [Come gestire il gruppo di amministratori locale nei dispositivi aggiunti ad Azure AD](assign-local-admin.md)
- [Gestire le identità dei dispositivi usando il portale di Azure](device-management-azure-portal.md)
- [Gestire i dispositivi non aggiornati in Azure AD](manage-stale-devices.md)
