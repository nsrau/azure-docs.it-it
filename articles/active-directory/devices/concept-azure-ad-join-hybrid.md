---
title: Che cos'è un'identità ibrida di Azure AD, aggiunto al dispositivo?
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
ms.openlocfilehash: 5c57180ba10322cb790c05b3f8f48043ca08b545
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462746"
---
# <a name="hybrid-azure-ad-joined-devices"></a>Dispositivi aggiunti all'identità ibrida di Azure AD

Per oltre un decennio, molte organizzazioni hanno usato l'aggiunta a un dominio ad Active Directory locale per consentire:

- Ai reparti IT di gestire i dispositivi di proprietà dell'azienda da una posizione centrale.
- Agli utenti di accedere ai dispositivi con il proprio account aziendale o dell'istituto di istruzione di Active Directory.

In genere le aziende con un footprint locale si basano su metodi di creazione dell'immagine per effettuare il provisioning dei dispositivi e, per gestirli, usano **System Center Configuration Manager (SCCM)** o **Criteri di gruppo**.

Se l'ambiente ha un footprint AD locale e si vogliono anche sfruttare le funzionalità offerte da Azure Active Directory, è possibile implementare dispositivi aggiunti all'identità ibrida di Azure AD. Questi dispositivi sono dispositivi aggiunti ad Active Directory in locale e registrati con Azure Active Directory.

|   | Aggiunta ad Azure AD ibrido |
| --- | --- |
| **Definizione** | Aggiunto in locale AD e Azure AD che richiedono account aziendale per accedere al dispositivo |
| **Destinatari principali** | Adatto per le organizzazioni ibride esistenti AD un'infrastruttura locale |
|   | Applicabile a tutti gli utenti in un'organizzazione |
| **Proprietà del dispositivo** | Organizzazione |
| **Sistemi operativi** | Windows 10, 8.1 e 7 |
|   | Windows Server 2008 o R2, 2012 o R2, 2016 e 2019 |
| **Provisioning** | Windows 10, Windows Server 2016/2019 |
|   | Aggiunta a un dominio dall'IT e join automatico tramite Azure AD Connect o ad FS config |
|   | Aggiunta a un dominio Windows Autopilot e join automatico tramite configurazione di Azure AD Connect o ad FS |
|   | Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 e Windows Server 2008 R2 - richiedono identità del servizio gestito |
| **Opzioni di accesso dispositivo** | Account aziendali usando: |
|   | Password |
|   | Windows Hello for Business per Windows 10 |
| **Gestione del dispositivo** | Criteri di gruppo |
|   | System Center Configuration Manager autonomo o CO-gestione con Microsoft Intune |
| **Funzionalità principali** | Accesso SSO alle risorse cloud e locali |
|   | Di accesso condizionale tramite aggiunta a un dominio o tramite Intune se CO-gestiti |
|   | Reimpostazione self-service di reimpostazione della Password e Windows Hello PIN nella schermata di blocco |
|   | Enterprise State Roaming tra dispositivi |

![Dispositivi aggiunti all'identità ibrida di Azure AD](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>Scenari

I dispositivi aggiunti all'identità ibrida di Azure AD Usa se:

- Sono state distribuite app Win32 in questi dispositivi che fanno affidamento sull'autenticazione di computer Active Directory.
- Si desidera continuare a usare criteri di gruppo per gestire la configurazione di dispositivo.
- Si desidera continuare a usare soluzioni di creazione di immagini esistenti per distribuire e configurare i dispositivi.
- È necessario supportare 8.1 dispositivi oltre a Windows 10 e versioni precedenti Windows 7

## <a name="next-steps"></a>Passaggi successivi

- [Pianificare l'implementazione dell'aggiunta ad Azure AD ibrido](hybrid-azuread-join-plan.md)
- [Gestire le identità dei dispositivi nel portale di Azure](device-management-azure-portal.md)
- [Gestire i dispositivi non aggiornati in Azure AD](manage-stale-devices.md)
