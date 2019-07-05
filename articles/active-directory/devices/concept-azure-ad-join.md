---
title: Che cos'è Azure AD, aggiunto al dispositivo?
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
ms.openlocfilehash: 4af3aea7218ea8792bb66188e8df7baf9f460b0b
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462811"
---
# <a name="azure-ad-joined-devices"></a>Dispositivi aggiunti ad Azure AD

Aggiunta ad Azure AD è destinata alle organizzazioni che vogliono essere incentrato sul cloud o solo cloud. Tutte le organizzazioni possono distribuire i dispositivi aggiunti ad Azure AD, indipendentemente dalla dimensione o del settore. Aggiunta ad Azure AD funziona anche in un ambiente ibrido, abilitando l'accesso alle App cloud sia in locale e risorse.

|   | Aggiunta ad Azure AD |
| --- | --- |
| **Definizione** | Aggiunti solo ad Azure AD che richiedono account aziendale per accedere al dispositivo |
| **Destinatari principali** | Adatta per entrambi solo cloud e le organizzazioni ibride. |
|   | Applicabile a tutti gli utenti in un'organizzazione |
| **Proprietà del dispositivo** | Organizzazione |
| **Sistemi operativi** | Tutti i dispositivi Windows 10 |
| **Provisioning** | Funzionalità Self-Service: La configurazione guidata di Windows o le impostazioni |
|   | Registrazione in blocco |
|   | Windows Autopilot |
| **Opzioni di accesso dispositivo** | Account aziendali usando: |
|   | Password |
|   | Windows Hello for Business |
|   | Chiavi di sicurezza FIDO2.0 (anteprima) |
| **Gestione del dispositivo** | Gestione dei dispositivi mobili (esempio: Microsoft Intune) |
|   | CO-gestione con Microsoft Intune e System Center Configuration Manager |
| **Funzionalità principali** | Accesso SSO alle risorse cloud e locali |
|   | Accesso condizionale tramite la registrazione MDM e valutazione della conformità MDM |
|   | Reimpostazione self-service di reimpostazione della Password e Windows Hello PIN nella schermata di blocco |
|   | Enterprise State Roaming tra dispositivi |

Dispositivi aggiunti ad AD Azure effettuati l'accesso all'uso di un organizzazione account Azure AD. Accesso alle risorse dell'organizzazione possa essere ulteriormente limitato basato sull'account Azure AD e [criteri di accesso condizionale](../conditional-access/overview.md) applicato all'identità del dispositivo.

Gli amministratori possono proteggere e ulteriormente utilizzando gli strumenti di gestione dei dispositivi mobili (MDM), ad esempio Microsoft Intune o in scenari di CO-gestione usando System Center Configuration Manager in dispositivi aggiunti a un controllo di Azure AD. Questi strumenti offrono un mezzo per applicare le configurazioni necessarie dell'organizzazione, ad esempio che richiedono archiviazione deve essere crittografato, la complessità delle password, le installazioni software e gli aggiornamenti software. Gli amministratori possono rendere disponibili per i dispositivi aggiunti ad Azure AD con le applicazioni dell'organizzazione [System Center Configuration Manager e il Microsoft Store per le aziende](https://docs.microsoft.com/sccm/apps/deploy-use/manage-apps-from-the-windows-store-for-business).

Aggiunta ad Azure AD può essere effettuata utilizzando le opzioni di self-service, ad esempio il fuori casella esperienza (OOBE), la registrazione in blocco, oppure [Windows Autopilot](https://docs.microsoft.com/intune/enrollment-autopilot).

Dispositivi aggiunti ad AD Azure possono mantenere accesso single sign-on alle risorse locali quando si trovano sulla rete dell'organizzazione. I dispositivi aggiunti ad Azure AD possono comunque eseguire l'autenticazione ai server locali, ad esempio file, stampa e altre applicazioni.

## <a name="scenarios"></a>Scenari

Anche se l'aggiunta ad Azure AD è destinata soprattutto alle organizzazioni prive di un'infrastruttura Active Directory di Windows Server locale, è ovviamente possibile usarla in scenari in cui:

- Si vuole passare all'infrastruttura basata su cloud mediante Azure AD e MDM come Intune.
- Non è possibile usare un'aggiunta a un dominio locale, ad esempio se è necessario avere il controllo di dispositivi mobili come tablet e telefoni.
- Gli utenti hanno soprattutto necessità di accedere a Office 365 o ad altre app SaaS integrate con Azure AD.
- Si vuole gestire un gruppo di utenti in Azure AD invece che in Active Directory, Questo scenario è applicabile, ad esempio, a lavoratori stagionali, terzisti o studenti.
- Si vogliono fornire funzionalità di join ai lavoratori in succursali remote con un'infrastruttura locale limitata.

È possibile configurare dispositivi aggiunti ad Azure AD per dispositivi Windows 10.

L'obiettivo dei dispositivi aggiunti ad Azure AD è di semplificare:

- Distribuzioni Windows di dispositivi di proprietà dell'azienda
- Accesso ad app e risorse aziendali da qualsiasi dispositivo Windows
- Gestione basata su cloud di dispositivi di proprietà dell'azienda
- Agli utenti di accedere ai propri dispositivi con loro Azure AD o sincronizzati Active Directory di lavoro o gli account dell'istituto di istruzione.

![Dispositivi aggiunti ad Azure AD](./media/concept-azure-ad-join/azure-ad-joined-device.png)

È possibile distribuire Aggiunta ad Azure AD usando uno dei metodi seguenti:

- [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot)
- [Distribuzione in blocco](https://docs.microsoft.com/intune/windows-bulk-enroll)
- [Esperienza self-service](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>Passaggi successivi

- [Pianificare l'implementazione di join Azure AD](azureadjoin-plan.md)
- [Dispositivi aggiunti all'identità come gestire il gruppo di amministratori locali in Azure AD](assign-local-admin.md)
- [Gestire le identità dei dispositivi nel portale di Azure](device-management-azure-portal.md)
- [Gestire i dispositivi non aggiornati in Azure AD](manage-stale-devices.md)
