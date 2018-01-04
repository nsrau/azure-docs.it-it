---
title: "Risoluzione dei problemi relativi a dispositivi di livello inferiore aggiunti all'identità ibrida di Azure Active Directory | Microsoft Docs"
description: "Risoluzione dei problemi relativi a dispositivi di livello inferiore aggiunti all'identità ibrida di Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: f1b92c604e20198714e9697bf4d08b3f71f23ae3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Risoluzione dei problemi relativi a dispositivi di livello inferiore aggiunti all'identità ibrida di Azure Active Directory 

Questo argomento è applicabile solo ai seguenti dispositivi: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Per Windows 10 o Windows Server 2016, vedere [Risoluzione dei problemi relativi a dispositivi Windows 10 e Windows Server 2016 aggiunti all'identità ibrida di Azure Active Directory](device-management-troubleshoot-hybrid-join-windows-current.md).

Questo argomento presuppone che siano stati [configurati dispositivi aggiunti all'identità ibrida di Azure Active Directory](device-management-hybrid-azuread-joined-devices-setup.md) per supportare gli scenari seguenti:

- Accesso condizionale basato su dispositivo

- [Roaming aziendale delle impostazioni](active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello for Business](active-directory-azureadjoin-passport-deployment.md) (Configurare Windows Hello for Business) 





Questo argomento fornisce indicazioni sulla risoluzione di potenziali problemi.  

**Informazioni utili:** 

- Il numero massimo di dispositivi per utente è incentrato sui dispositivi. Ad esempio: se *jdoe* e *jharnett* accedono a questo dispositivo, viene creata una registrazione separata (DeviceID) per ciascuno di questi utenti nella scheda di informazioni **UTENTE**.  

- La registrazione/aggiunta iniziale dei dispositivi è configurata in modo da eseguire un tentativo di accesso o blocco/sblocco. Potrebbero esserci 5 minuti di ritardo causati da un'attività dell'utilità di pianificazione. 

- Una reinstallazione del sistema operativo o l'annullamento e la ripetizione manuale della registrazione potrebbe creare una nuova registrazione in Azure AD e causare la presenza di più voci nella scheda Info UTENTE nel portale di Azure. 


## <a name="step-1-retrieve-the-registration-status"></a>Passaggio 1: Recuperare lo stato della registrazione 

**Per verificare lo stato della registrazione**  

1. Aprire il prompt dei comandi come amministratore 

2. Digitare `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /i"`.

Questo comando consente di visualizzare una finestra di dialogo che fornisce altri dettagli relativi allo stato delle aggiunte.

![Aggiunta all'area di lavoro per Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/01.png)


## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Passaggio 2: Valutare lo stato delle aggiunte all'identità ibrida di Azure AD 

Se l'aggiunta all'identità ibrida di Azure AD non è stata completata correttamente, la finestra di dialogo fornisce dettagli sul problema che si è verificato.

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
  
**Le cause più comuni di un'aggiunta all'identità ibrida di Azure AD non riuscita sono:** 

- Il computer non è presente nella rete interna dell'organizzazione o c'è una VPN senza connessione su un controller di dominio locale AD.

- Si è connessi al computer con un account computer locale. 

- Problemi di configurazione del servizio: 

  - Il server federativo è stato configurato per supportare **WIAORMULTIAUTHN**. 

  - Non è presente alcun oggetto Punto di connessione del servizio che punti al nome di dominio verificato in Azure AD nella foresta AD a cui appartiene il computer.

  - Un utente ha raggiunto il limite di dispositivi. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le [domande frequenti sulla gestione dei dispositivi](device-management-faq.md)  
