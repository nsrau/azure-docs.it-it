---
title: Risoluzione dei problemi relativi a dispositivi di livello inferiore aggiunti all'identità ibrida di Azure Active Directory | Microsoft Docs
description: Risoluzione dei problemi relativi a dispositivi di livello inferiore aggiunti all'identità ibrida di Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: d41e83c11f33b0bcbe4ea632332f2cd8bb12313f
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714113"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Risoluzione dei problemi relativi a dispositivi di livello inferiore aggiunti all'identità ibrida di Azure Active Directory 

Questo articolo è applicabile solo ai dispositivi seguenti: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Per Windows 10 o Windows Server 2016, vedere [Risoluzione dei problemi relativi a dispositivi Windows 10 e Windows Server 2016 aggiunti all'identità ibrida di Azure Active Directory](device-management-troubleshoot-hybrid-join-windows-current.md).

Questo articolo presuppone che siano stati [configurati dispositivi aggiunti all'identità ibrida di Azure Active Directory](device-management-hybrid-azuread-joined-devices-setup.md) per supportare gli scenari seguenti:

- Accesso condizionale basato su dispositivo

- [Roaming aziendale delle impostazioni](active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello for Business](active-directory-azureadjoin-passport-deployment.md) (Configurare Windows Hello for Business) 





Questo articolo fornisce indicazioni sulla risoluzione di potenziali problemi.  

**Informazioni utili:** 

- Il numero massimo di dispositivi per utente è incentrato sui dispositivi. Ad esempio: se *jdoe* e *jharnett* accedono a questo dispositivo, viene creata una registrazione separata (DeviceID) per ciascuno di questi utenti nella scheda di informazioni **UTENTE**.  

- La registrazione/aggiunta iniziale dei dispositivi è configurata in modo da eseguire un tentativo di accesso o blocco/sblocco. Potrebbero esserci 5 minuti di ritardo causati da un'attività dell'utilità di pianificazione. 

- Una reinstallazione del sistema operativo o la ripetizione manuale della registrazione potrebbe creare una nuova registrazione in Azure AD e causare la presenza di più voci nella scheda di informazioni UTENTE nel portale di Azure. 

## <a name="step-1-retrieve-the-registration-status"></a>Passaggio 1: Recuperare lo stato della registrazione 

**Per verificare lo stato della registrazione**  

1. Accedere con l'account utente che ha eseguito l'aggiunta all'identità ibrida di Azure AD.

2. Aprire il prompt dei comandi come amministratore 

3. Digitare `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`.

Questo comando consente di visualizzare una finestra di dialogo che fornisce altri dettagli relativi allo stato delle aggiunte.

![Aggiunta all'area di lavoro per Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/01.png)


## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Passaggio 2: Valutare lo stato delle aggiunte all'identità ibrida di Azure AD 

Se l'aggiunta all'identità ibrida di Azure AD non è stata completata correttamente, la finestra di dialogo fornisce dettagli sul problema che si è verificato.

**I problemi più comuni sono:**

- Una configurazione errata di AD FS o Azure AD

    ![Aggiunta all'area di lavoro per Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/02.png)

- Non si è connessi come utente di dominio

    ![Aggiunta all'area di lavoro per Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/03.png)
    
    Questo problema può verificarsi per diversi motivi:
    
    - L'utente che ha eseguito l'accesso non è un utente di dominio, ad esempio è un utente locale. L'aggiunta all'identità ibrida di Azure AD su dispositivi di livello inferiore è supportata solo per utenti di dominio.
    
    - Autoworkplace.exe non è in grado di eseguire automaticamente l'autenticazione con Azure AD o AD FS. Ciò potrebbe essere dovuto a problemi di connettività di rete in uscita agli URL di Azure AD. È possibile inoltre che l'autenticazione a più fattori (MFA) sia abilitata/configurata per l'utente e WIAORMUTLIAUTHN non sia configurato sul server federativo. È anche possibile che la pagina di individuazione dell'area di autenticazione principale sia in attesa dell'interazione dell'utente, impedendo ad **autoworkplace.exe** di ottenere automaticamente un token.
    
    - L'organizzazione usa il Single Sign-On di Azure AD, `https://autologon.microsoftazuread-sso.com` o `https://aadg.windows.net.nsatc.net` non sono presenti nelle impostazioni Intranet di IE del dispositivo e l'opzione **Consenti aggiornamenti alla barra di stato tramite script** non è abilitata per l'area Intranet.

- È stata raggiunta una quota

    ![Aggiunta all'area di lavoro per Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/04.png)

- Il servizio non risponde 

    ![Aggiunta all'area di lavoro per Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/05.png)

È inoltre possibile trovare le informazioni sullo stato nel registro eventi in **Registri applicazioni e servizi\Microsoft-Workplace Join**
  
**Le cause più comuni di un'aggiunta all'identità ibrida di Azure AD non riuscita sono:** 

- Il computer non è connesso alla rete interna dell'organizzazione, né a una VPN con connessione al controller di dominio AD locale.

- Si è connessi al computer con un account computer locale. 

- Problemi di configurazione del servizio: 

  - Il server federativo è stato configurato per supportare **WIAORMULTIAUTHN**. 

  - Nella foresta del computer non è presente alcun oggetto Punto di connessione del servizio che punti al nome di dominio verificato in Azure AD 

  - Un utente ha raggiunto il limite di dispositivi. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le [domande frequenti sulla gestione dei dispositivi](device-management-faq.md)  
