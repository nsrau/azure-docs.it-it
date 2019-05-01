---
title: 'Azure AD Connect: passaggi successivi e come gestire Azure AD Connect | Microsoft Docs'
description: Informazioni su come estendere la configurazione predefinita e attività operative per Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: c18bee36-aebf-4281-b8fc-3fe14116f1a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c204029557a73dc3f02015afb92c0fdbf0d4d50e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64571343"
---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Passaggi successivi e come gestire Azure AD Connect
Usare le procedure operative descritte in questo articolo per personalizzare Azure Active Directory Connect al fine di soddisfare le esigenze e i requisiti dell'organizzazione.  

## <a name="add-additional-sync-admins"></a>Aggiungere altri amministratori di sincronizzazione
Per impostazione predefinita, solo l'utente che ha eseguito l'installazione e gli amministratori locali possono gestire il motore di sincronizzazione installato. Per fare in modo che altre persone possano accedere al motore di sincronizzazione e gestirlo, trovare il gruppo denominato ADSyncAdmins nel server locale e aggiungere gli utenti desiderati a questo gruppo.

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>Assegnare licenze agli utenti di Azure AD Premium ed Enterprise Mobility Suite
Dopo avere sincronizzato gli utenti nel cloud, occorre assegnare loro una licenza in modo che possano usare le app cloud come Office 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Per assegnare una licenza Azure AD Premium o Enterprise Mobility Suite

1. Accedere al portale di Azure come amministratore.
2. A sinistra, selezionare **Active Directory**.
3. Nella pagina **Active Directory** fare doppio clic sulla directory con gli utenti da configurare.
4. Nella parte superiore della pagina della directory selezionare **Licenses**.
5. Nella pagina **Licenze** selezionare **Active Directory Premium** o **Enterprise Mobility Suite** e quindi fare clic su **Assegna**.
6. Nella finestra di dialogo selezionare gli utenti a cui assegnare le licenze, quindi fare clic sull'icona con il segno di spunta per salvare le modifiche.

## <a name="verify-the-scheduled-synchronization-task"></a>Verificare l'attività di sincronizzazione pianificata
Usare il portale di Azure per controllare lo stato di una sincronizzazione.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Per verificare l'attività di sincronizzazione pianificata
1. Accedere al portale di Azure come amministratore.
2. A sinistra, selezionare **Active Directory**.
3. A sinistra, selezionare **Azure AD Connect**
4. Nella parte superiore della pagina, si noti l'ultima sincronizzazione.

![Ora sincronizzazione directory](./media/how-to-connect-post-installation/verify2.png)

## <a name="start-a-scheduled-synchronization-task"></a>Avviare un'attività di sincronizzazione pianificata
Se è necessario eseguire un'attività di sincronizzazione, è possibile farlo:

1. Fare doppio clic sul collegamento sul desktop di Azure AD Connect per avviare la procedura guidata.
2. Fare clic su **Configure**.
3. Nella schermata di attività, selezionare la **personalizzare le opzioni di sincronizzazione** e fare clic su **successivo**
4. Immettere le credenziali di Azure AD
5. Fare clic su **Avanti**. Fare clic su **Avanti**.  Fare clic su **Avanti**.
5.  Nel **possa essere configurato** schermata, assicurarsi che le **avvia il processo di sincronizzazione al termine della configurazione** casella è selezionata.
6.  Fare clic su **Configure**.

Per altre informazioni sull'utilità di pianificazione della sincronizzazione di Azure AD Connect, vedere [Utilità di pianificazione di Azure AD Connect](how-to-connect-sync-feature-scheduler.md).

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Attività aggiuntive disponibili in Azure AD Connect
Dopo l'installazione iniziale di Azure AD Connect è sempre possibile riavviare la procedura guidata dalla pagina iniziale di Azure AD Connect o dal collegamento sul dekstop.  Si noterà che la riesecuzione della procedura guidata fornisce alcune nuove opzioni sotto forma di Attività aggiuntive.  

La tabella seguente include un riepilogo di tali attività e una breve descrizione di ognuna di esse.

![Elenco delle attività aggiuntive](./media/how-to-connect-post-installation/addtasks2.png)

| Attività aggiuntive | DESCRIZIONE |
| --- | --- |
|**Impostazioni di privacy**|Consente di visualizzare i dati di telemetria sono condiviso con Microsoft.|
|**Visualizza la configurazione corrente**|Consente di visualizzare la soluzione Azure AD Connect corrente.  Include impostazioni generali, directory sincronizzate e impostazioni di sincronizzazione. |
| **Personalizzazione delle opzioni di sincronizzazione** |Consente di modificare la configurazione corrente, come l'aggiunta di altre foreste di Active Directory alla configurazione o l'attivazione di opzioni di sincronizzazione, ad esempio writeback di utenti, gruppi, dispositivi o password. |
|**Configurare le opzioni del dispositivo**|Opzioni di dispositivo disponibili per la sincronizzazione|
|**Aggiorna lo schema della directory**|Consente di aggiungere nuovi oggetti di directory in locale per la sincronizzazione|
|**Configurare la modalità di gestione temporanea** |Informazioni relative alla gestione temporanea non vengono sincronizzate immediatamente e non vengono esportate in Azure AD o in Active Directory locale.  Con questa funzionalità è possibile visualizzare in anteprima le sincronizzazioni prima che si verifichino. |
|**Modifica accesso dell'utente**|Modificare il metodo di autenticazione gli utenti usano per accedere|
|**Gestire federation**|Gestire l'infrastruttura AD FS, rinnovare i certificati e aggiungere i server AD FS|
|**Risolvere i problemi**|Guida alla risoluzione dei problemi di Azure AD Connect|

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).
