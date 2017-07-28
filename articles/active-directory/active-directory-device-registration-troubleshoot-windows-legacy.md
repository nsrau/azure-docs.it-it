---
title: Risoluzione dei problemi di registrazione automatica di computer aggiunti al dominio Azure AD per client di livello inferiore di Windows | Documentazione Microsoft
description: Risoluzione dei problemi di registrazione automatica di computer aggiunti al dominio Azure AD per client di livello inferiore di Windows.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 49af838d750ba61694bdb28a83ee6e531627527d
ms.contentlocale: it-it
ms.lasthandoff: 03/10/2017

---
# <a name="troubleshooting-auto-registration-of-domain-joined-computers-to-azure-ad-for-windows-down-level-clients"></a>Risoluzione dei problemi di registrazione automatica di computer aggiunti al dominio in Azure AD per client di livello inferiore di Windows 

Questo argomento è applicabile solo ai seguenti client: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Per Windows 10 o Windows Server 2016, vedere [Risoluzione dei problemi di registrazione automatica di computer aggiunti al dominio Azure AD per client di livello inferiore di Windows 10 e Windows Server 2016](active-directory-device-registration-troubleshoot-windows.md).

Questo argomento presuppone che la registrazione automatica dei dispositivi aggiunti a un dominio sia stata configurata come descritto in [Come configurare la registrazione automatica dei dispositivi aggiunti al dominio di Windows con Azure Active Directory](active-directory-device-registration-get-started.md).
 
Questo argomento fornisce indicazioni sulla risoluzione di potenziali problemi.  
Alcuni aspetti da tenere presente per risultati positivi: 

- La registrazione di questi client su Azure AD è per utente/dispositivo. Ad esempio: se jdoe e jharnett accedono a questo dispositivo, viene creata una registrazione separata (DeviceID) per ciascuno di questi utenti nella scheda Info UTENTE.  

- La registrazione di questi client predefiniti è configurata per essere verificata all'accesso o al blocco/sblocco ed è possibile attivare un ritardo di 5 minuti mediante l'Utilità di pianificazione. 

- Una reinstallazione del sistema operativo o l'annullamento e la ripetizione manuale della registrazione potrebbe creare una nuova registrazione in Azure AD e avrà come risultato la presenza di più voci nella scheda Info UTENTE nel portale di Azure. 


## <a name="step-1-retrieve-the-registration-status"></a>Passaggio 1: Recuperare lo stato della registrazione 

**Per verificare lo stato della registrazione**  

1. Aprire il prompt dei comandi come amministratore 

2. Digitare `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /i"`.

Questo comando consente di visualizzare una finestra di dialogo che fornisce altri dettagli relativi allo stato delle aggiunte.

![Aggiunta all'area di lavoro per Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/01.png)


## <a name="step-2-evaluate-the-registration-status"></a>Passaggio 2: Valutare lo stato della registrazione 

Se l'aggiunta non è avvenuta correttamente, la finestra di dialogo fornisce dettagli sul problema che si è verificato.

**I problemi più comuni sono:**

- Una configurazione errata di AD FS o Azure AD

    ![Aggiunta all'area di lavoro per Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/02.png)

- Non si è connessi come utente di dominio

    ![Aggiunta all'area di lavoro per Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/03.png)

- È stata raggiunta una quota

    ![Aggiunta all'area di lavoro per Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/04.png)

- Il servizio non risponde 

    ![Aggiunta all'area di lavoro per Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/05.png)

È inoltre possibile trovare le informazioni sullo stato nel registro eventi in **Registri applicazioni e servizi\Microsoft-Workplace Join**.
  
**Le cause più comuni di una registrazione non riuscita sono:** 

- Il computer non è presente nella rete interna dell'organizzazione o c'è una VPN senza connessione su un controller di dominio locale AD.

- Si è connessi al computer con un account computer locale. 

- Problemi di configurazione del servizio: 

  - Il server federativo è stato configurato per supportare **WIAORMULTIAUTHN**. 

  - Non è presente alcun oggetto Punto di connessione del servizio che punti al nome di dominio verificato in Azure AD nella foresta AD a cui appartiene il computer.

  - Un utente ha raggiunto il limite di dispositivi. Vedere Introduzione a Registrazione dispositivo Azure Active Directory.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere [Domande frequenti sulla registrazione automatica dei dispositivi](active-directory-device-registration-faq.md) 

