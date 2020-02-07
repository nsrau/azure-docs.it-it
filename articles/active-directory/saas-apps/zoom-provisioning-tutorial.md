---
title: 'Esercitazione: configurare lo zoom per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente per lo zoom.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: cd832a9dfec4680222d2c985f49aba499a56aaac
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062775"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>Esercitazione: configurare lo zoom per il provisioning utenti automatico

Questa esercitazione illustra i passaggi da eseguire in zoom e Azure Active Directory (Azure AD) per configurare Azure AD per eseguire automaticamente il provisioning e il deprovisioning di utenti e/o gruppi per lo zoom.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisites

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Tenant di zoom](https://zoom.us/pricing)
* Un account utente in zoom con autorizzazioni di amministratore

## <a name="add-zoom-from-the-gallery"></a>Aggiungere lo zoom dalla raccolta

Prima di configurare lo zoom per il provisioning utenti automatico con Azure AD, è necessario aggiungere lo zoom dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere zoom dalla raccolta di applicazioni di Azure AD, seguire questa procedura:**

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Zoom**, selezionare **Zoom** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Zoom nell'elenco risultati](common/search-new-app.png)

## <a name="assign-users-to-zoom"></a>Assegnare gli utenti a zoom

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a zoom. Dopo aver stabilito questo, è possibile assegnare gli utenti e/o i gruppi per eseguire lo zoom seguendo le istruzioni riportate qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zoom"></a>Suggerimenti importanti per l'assegnazione di utenti a zoom

* È consigliabile assegnare un singolo Azure AD utente per eseguire lo zoom per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a zoom, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configure-automatic-user-provisioning-to-zoom"></a>Configurare il provisioning utenti automatico per lo zoom 

In questa sezione vengono illustrati i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti o gruppi in zoom in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare la Single Sign-On basata su SAML per lo zoom, seguendo le istruzioni fornite nell' [esercitazione Zoom Single Sign-on](zoom-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Configurare il provisioning utenti automatico per lo zoom in Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Zoom**.

    ![Collegamento di Zoom nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere `https://api.zoom.us/scim` nell' **URL tenant**. Per recuperare il **token segreto** dell'account zoom, seguire la procedura dettagliata descritta nel passaggio 6.

6. Accedere alla console di [amministrazione dello zoom](https://zoom.us/signin). Passare a **Advanced > zoom for Developers** nel riquadro di spostamento a sinistra.

    ![Integrazioni zoom](media/zoom-provisioning-tutorial/zoom01.png)

    Passare a **Manage (Gestisci** ) nell'angolo superiore destro della pagina. 

    ![Installazione zoom](media/zoom-provisioning-tutorial/zoom02.png)

    Passare all'app Azure AD creata. 
    
    ![App zoom](media/zoom-provisioning-tutorial/zoom03.png)

    Selezionare **credenziali dell'app** nel riquadro di spostamento a sinistra.

    ![App zoom](media/zoom-provisioning-tutorial/zoom04.png)

    Recuperare il valore del token JWT mostrato di seguito e immetterlo nel campo **token segreto** in Azure ad. Se è necessario un nuovo token non in scadenza, sarà necessario riconfigurare l'ora di scadenza che genererà automaticamente un nuovo token. 

    ![Installazione zoom](media/zoom-provisioning-tutorial/zoom05.png)

7. Quando si popolano i campi indicati nel passaggio 5, fare clic su **Test connessione** per assicurarsi che Azure ad possibile connettersi a zoom. Se la connessione non riesce, verificare che l'account di zoom disponga di autorizzazioni di amministratore e riprovare.

    ![token](common/provisioning-testconnection-tenanturltoken.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

9. Fare clic su **Salva**.

10. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti per ingrandire**.

    ![Mapping utente zoom](media/zoom-provisioning-tutorial/zoom-user-mapping.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD per ingrandire la sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in zoom per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.
    
     ![Mapping utente zoom](media/zoom-provisioning-tutorial/zoom-user-attributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per lo zoom, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .
    
    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio Azure ad provisioning in zoom.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Lo zoom non supporta il provisioning per i gruppi.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
