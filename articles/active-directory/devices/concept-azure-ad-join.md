---
title: Che cos'è un dispositivo Azure AD aggiunto?
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
ms.openlocfilehash: 24ec4373bceb3cc3c9e5be2c7a0dab1f62197b3c
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76512199"
---
# <a name="azure-ad-joined-devices"></a>Dispositivi aggiunti ad Azure AD

Azure AD join è destinato alle organizzazioni che vogliono essere cloud-First o solo cloud. Qualsiasi organizzazione può distribuire Azure AD dispositivi aggiunti indipendentemente dalle dimensioni o dal settore. Azure AD join funziona anche in un ambiente ibrido, consentendo l'accesso alle risorse e alle app cloud e locali.

|   | Aggiunta ad Azure AD |
| --- | --- |
| **Definizione** | Aggiunto solo a Azure AD che richiede l'account aziendale per accedere al dispositivo |
| **Destinatari primari** | Adatto per organizzazioni ibride e solo cloud. |
|   | Applicabile a tutti gli utenti di un'organizzazione |
| **Proprietà del dispositivo** | Organizzazione |
| **Sistemi operativi** | Tutti i dispositivi Windows 10 |
| **Provisioning** | Self-Service: impostazioni o OOBE di Windows |
|   | Registrazione in blocco |
|   | Windows Autopilot |
| **Opzioni di accesso del dispositivo** | Account aziendali con: |
|   | Password |
|   | Windows Hello for Business |
|   | Chiavi di sicurezza di FIDO 2.0 (anteprima) |
| **Gestione del dispositivo** | Gestione dei dispositivi mobili (ad esempio: Microsoft Intune) |
|   | Co-gestione con Microsoft Intune e Microsoft endpoint Configuration Manager |
| **Funzionalità principali** | SSO per le risorse cloud e locali |
|   | Accesso condizionale tramite la registrazione MDM e la valutazione della conformità MDM |
|   | Reimpostazione della password self-service e ripristino del PIN di Windows Hello nella schermata di blocco |
|   | Enterprise State Roaming tra dispositivi |

Azure AD dispositivi aggiunti sono connessi a utilizzando un account di Azure AD aziendale. L'accesso alle risorse dell'organizzazione può essere ulteriormente limitato in base al Azure AD account e ai [criteri di accesso condizionale](../conditional-access/overview.md) applicati all'identità del dispositivo.

Gli amministratori possono proteggere e controllare ulteriormente Azure AD dispositivi aggiunti usando strumenti di gestione di dispositivi mobili (MDM) come Microsoft Intune o in scenari di co-gestione usando Microsoft endpoint Configuration Manager. Questi strumenti forniscono un mezzo per applicare le configurazioni necessarie per l'organizzazione, ad esempio la necessità di crittografare l'archiviazione, la complessità delle password, le installazioni software e gli aggiornamenti software. Gli amministratori possono rendere disponibili le applicazioni dell'organizzazione per Azure AD i dispositivi aggiunti usando Configuration Manager per [gestire le app dal Microsoft Store per le aziende e la formazione](https://docs.microsoft.com/configmgr/apps/deploy-use/manage-apps-from-the-windows-store-for-business).

Azure AD join può essere eseguito usando opzioni self-service come la configurazione guidata, la registrazione in blocco o [Windows Autopilot](https://docs.microsoft.com/intune/enrollment-autopilot).

Azure AD dispositivi aggiunti possono comunque mantenere Single Sign-On l'accesso alle risorse locali quando si trovano nella rete dell'organizzazione. I dispositivi Azure AD aggiunti possono comunque eseguire l'autenticazione nei server locali, ad esempio file, stampa e altre applicazioni.

## <a name="scenarios"></a>Scenari

Anche se l'aggiunta ad Azure AD è destinata soprattutto alle organizzazioni prive di un'infrastruttura Active Directory di Windows Server locale, è ovviamente possibile usarla in scenari in cui:

- Si vuole passare all'infrastruttura basata su cloud mediante Azure AD e MDM come Intune.
- Non è possibile usare un'aggiunta a un dominio locale, ad esempio se è necessario avere il controllo di dispositivi mobili come tablet e telefoni.
- Gli utenti hanno soprattutto necessità di accedere a Office 365 o ad altre app SaaS integrate con Azure AD.
- Si vuole gestire un gruppo di utenti in Azure AD invece che in Active Directory, Questo scenario può essere applicato, ad esempio, a lavoratori stagionali, terzisti o studenti.
- Si vogliono fornire funzionalità di join ai lavoratori in succursali remote con un'infrastruttura locale limitata.

È possibile configurare dispositivi aggiunti ad Azure AD per dispositivi Windows 10.

L'obiettivo dei dispositivi aggiunti ad Azure AD è di semplificare:

- Distribuzioni Windows di dispositivi di proprietà dell'azienda
- Accesso ad app e risorse aziendali da qualsiasi dispositivo Windows
- Gestione basata su cloud di dispositivi di proprietà dell'azienda
- Gli utenti possono accedere ai propri dispositivi con la loro Azure AD o sincronizzati Active Directory account aziendali o dell'Istituto di istruzione.

![Dispositivi aggiunti ad Azure AD](./media/concept-azure-ad-join/azure-ad-joined-device.png)

È possibile distribuire Aggiunta ad Azure AD usando uno dei metodi seguenti:

- [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot)
- [Distribuzione in blocco](https://docs.microsoft.com/intune/windows-bulk-enroll)
- [Esperienza self-service](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>Passaggi successivi

- [Pianificare l'implementazione dell'aggiunta ad Azure AD](azureadjoin-plan.md)
- [Come gestire il gruppo Administrators locale nei dispositivi Azure AD aggiunti](assign-local-admin.md)
- [Gestire le identità dei dispositivi usando il portale di Azure](device-management-azure-portal.md)
- [Gestire i dispositivi non aggiornati in Azure AD](manage-stale-devices.md)
