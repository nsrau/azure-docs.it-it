---
title: Che cos'è un dispositivo ibrido aggiunto ad Azure AD?
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
ms.openlocfilehash: 15cdaba7d63d72aab25757e7ba6f5eadc48e026a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76512250"
---
# <a name="hybrid-azure-ad-joined-devices"></a>Dispositivi aggiunti all'identità ibrida di Azure AD

Per oltre un decennio, molte organizzazioni hanno usato l'aggiunta a un dominio ad Active Directory locale per consentire:

- Ai reparti IT di gestire i dispositivi di proprietà dell'azienda da una posizione centrale.
- Agli utenti di accedere ai dispositivi con il proprio account aziendale o dell'istituto di istruzione di Active Directory.

In genere, le organizzazioni con un footprint locale si basano su metodi di imaging per il provisioning dei dispositivi e spesso usano **Configuration Manager** o Criteri di **gruppo (GP)** per gestirli.

Se l'ambiente ha un footprint AD locale e si vogliono anche sfruttare le funzionalità offerte da Azure Active Directory, è possibile implementare dispositivi aggiunti all'identità ibrida di Azure AD. Questi dispositivi sono dispositivi aggiunti ad Active Directory locale e registrati con Azure Active Directory.These devices, are devices that are joined to your on-premises Active Directory and registered with your Azure Active Directory.

|   | Join ibrido ad Azure AD |
| --- | --- |
| **Definizione** | Aggiunta ad Active Directory locale e Azure AD che richiedono l'account dell'organizzazione per accedere al dispositivo |
| **Destinatari principali** | Adatto per organizzazioni ibride con l'infrastruttura ad locale esistente |
|   | Applicabile a tutti gli utenti di un'organizzazione |
| **Proprietà del dispositivo** | Organization |
| **Sistemi operativi** | Windows 10, 8.1 e 7 |
|   | Windows Server 2008/R2, 2012/R2, 2016 e 2019 |
| **Provisioning** | Windows 10, Windows Server 2016/2019 |
|   | Aggiunta al dominio tramite IT e aggiunta automatica tramite Azure AD Connect o configurazione ADFSDomain join by IT and autojoin via Azure AD Connect or ADFS config |
|   | Aggiunta al dominio tramite Windows Autopilot e aggiunta automatica tramite configurazione di Azure AD Connect o ADFSDomain join by Windows Autopilot and autojoin via Azure AD Connect or ADFS config |
|   | Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 e Windows Server 2008 R2 - Richiedi MSI |
| **Opzioni di accesso al dispositivo** | Account dell'organizzazione che utilizzano: |
|   | Password |
|   | Windows Hello for Business for Win10 |
| **Gestione del dispositivo** | Criteri di gruppo |
|   | Configuration Manager autonomo o co-gestione con Microsoft Intune |
| **Funzionalità principali** | SSO per le risorse cloud e locali |
|   | Accesso condizionale tramite aggiunta a un dominio o tramite Intune se co-gestitoConditional Access through Domain join or through Intune if co-managed |
|   | Reimpostazione password self-service e reimpostazione del PIN di Windows Hello nella schermata di blocco |
|   | Enterprise State Roaming tra più dispositivi |

![Dispositivi aggiunti all'identità ibrida di Azure AD](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>Scenari

Usare i dispositivi aggiunti ibridi di Azure AD se:Use Azure AD hybrid joined devices if:

- Sono state distribuite app Win32 in questi dispositivi che fanno affidamento sull'autenticazione di computer Active Directory.
- Si desidera continuare a utilizzare Criteri di gruppo per gestire la configurazione dei dispositivi.
- Si desidera continuare a utilizzare le soluzioni di imaging esistenti per distribuire e configurare i dispositivi.
- È necessario supportare i dispositivi Windows 7 e 8.1 di livello inferiore oltre a Windows 10

## <a name="next-steps"></a>Passaggi successivi

- [Pianificare l'implementazione dell'aggiunta ad Azure AD ibrido](hybrid-azuread-join-plan.md)
- [Gestire le identità dei dispositivi tramite il portale di AzureManage device identities using the Azure portal](device-management-azure-portal.md)
- [Gestire i dispositivi non aggiornati in Azure AD](manage-stale-devices.md)
