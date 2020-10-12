---
title: Che cos'è un dispositivo ibrido Azure AD aggiunto?
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
ms.openlocfilehash: 259a1324c412dad40d32a8b8e026d84e6f5aa066
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85554925"
---
# <a name="hybrid-azure-ad-joined-devices"></a>Dispositivi aggiunti all'identità ibrida di Azure AD

Per oltre un decennio, molte organizzazioni hanno usato l'aggiunta a un dominio ad Active Directory locale per consentire:

- Ai reparti IT di gestire i dispositivi di proprietà dell'azienda da una posizione centrale.
- Agli utenti di accedere ai dispositivi con il proprio account aziendale o dell'istituto di istruzione di Active Directory.

In genere, le organizzazioni con un footprint locale si basano su metodi di creazione dell'immagine per il provisioning dei dispositivi e spesso usano **Configuration Manager** o **criteri di gruppo** per gestirli.

Se l'ambiente ha un footprint AD locale e si vogliono anche sfruttare le funzionalità offerte da Azure Active Directory, è possibile implementare dispositivi aggiunti all'identità ibrida di Azure AD. Questi dispositivi sono dispositivi aggiunti al Active Directory locale e registrati con l'Azure Active Directory.

| Join ibrido ad Azure AD | Descrizione |
| --- | --- |
| **Definizione** | Aggiunto ad Active Directory locale e Azure AD che richiede l'account aziendale per accedere al dispositivo |
| **Destinatari principali** | Adatto per organizzazioni ibride con infrastruttura di Active Directory locale esistente |
|   | Applicabile a tutti gli utenti di un'organizzazione |
| **Proprietà del dispositivo** | Organization |
| **Sistemi operativi** | Windows 10, 8,1 e 7 |
|   | Windows Server 2008/R2, 2012/R2, 2016 e 2019 |
| **Provisioning** | Windows 10, Windows Server 2016/2019 |
|   | Aggiunta a un dominio e aggiunta automatica tramite la configurazione di Azure AD Connect o ADFS |
|   | Aggiunta a un dominio di Windows Autopilot e autojoin tramite la configurazione di Azure AD Connect o ADFS |
|   | Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 e Windows Server 2008 R2-Richiedi MSI |
| **Opzioni di accesso del dispositivo** | Account aziendale che usa: |
|   | Password |
|   | Windows Hello for business per WIN10 |
| **Gestione del dispositivo** | Criteri di gruppo |
|   | Configuration Manager autonomo o co-gestione con Microsoft Intune |
| **Funzionalità principali** | SSO per le risorse cloud e locali |
|   | Accesso condizionale tramite aggiunta a un dominio o Intune se co-gestito |
|   | Reimpostazione della password self-service e ripristino del PIN di Windows Hello nella schermata di blocco |
|   | Enterprise State Roaming su più dispositivi |

![Dispositivi aggiunti all'identità ibrida di Azure AD](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>Scenari

Usare Azure AD dispositivi aggiunti ibridi se:

- Sono state distribuite app Win32 in questi dispositivi che fanno affidamento sull'autenticazione di computer Active Directory.
- Si vuole continuare a usare Criteri di gruppo per gestire la configurazione del dispositivo.
- Si vuole continuare a usare le soluzioni di imaging esistenti per distribuire e configurare i dispositivi.
- È necessario supportare i dispositivi Windows 7 e 8,1 di livello inferiore, oltre a Windows 10

## <a name="next-steps"></a>Passaggi successivi

- [Pianificare l'implementazione dell'aggiunta ad Azure AD ibrido](hybrid-azuread-join-plan.md)
- [Gestire le identità dei dispositivi usando il portale di Azure](device-management-azure-portal.md)
- [Gestire i dispositivi non aggiornati in Azure AD](manage-stale-devices.md)
