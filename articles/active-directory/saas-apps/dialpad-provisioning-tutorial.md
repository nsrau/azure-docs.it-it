---
title: 'Esercitazione: Configurare la cornetta per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e deprovisioning degli account utente in cornetta.
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
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 32e634bc089417aaa8080b30a5f77f663a3d8b33
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611772"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>Esercitazione: Configurare cornetta per il provisioning utenti automatico

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in cornetta e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e deprovisioning di utenti e/o gruppi digitare.

> [!NOTE]
>  L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).

> Questo connettore è attualmente in anteprima. Per altre informazioni sulle condizioni di Microsoft Azure generale di utilizzo per le funzionalità di anteprima, vedere [condizioni per l'utilizzo aggiuntive per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Un tenant cornetta](https://www.dialpad.com/pricing/).
* Un account utente in cornetta con autorizzazioni di amministratore.

## <a name="assign-users-to-dialpad"></a>Assegnare gli utenti a cornetta
Azure Active Directory Usa un concetto detto assegnazioni per determinare gli utenti che dovranno ricevere l'accesso alle App selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo di utenti e/o gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono accedere a cornetta. Dopo aver stabilito questo, è possibile assegnare tali utenti e/o gruppi digitare seguendo le istruzioni riportate qui:
 
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>Suggerimenti importanti per l'assegnazione di utenti a cornetta

 * È consigliabile che un singolo utente di Azure AD viene assegnato a digitare per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a digitare, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo di accesso predefinito vengono esclusi dal provisioning.

## <a name="setup-dialpad-for-provisioning"></a>Digitare il programma di installazione per il provisioning

Prima di configurare cornetta per provisioning utenti automatico con Azure AD, è necessario recuperare alcune informazioni di provisioning da digitare.

1. Accedi per i [cornetta Admin Console](https://dialpadbeta.com/login) e selezionare **impostazioni di amministrazione**. Assicurarsi che **My Company** sia selezionato nell'elenco a discesa. Passare a **Authentication > chiavi API**.

    ![Digitare aggiungere SCIM](media/dialpad-provisioning-tutorial/dialpad01.png)

2. Generare una nuova chiave facendo clic **aggiungere una chiave** e configurando le proprietà per il token segreto.

    ![Digitare aggiungere SCIM](media/dialpad-provisioning-tutorial/dialpad02.png)

    ![Digitare aggiungere SCIM](media/dialpad-provisioning-tutorial/dialpad03.png)

3. Scegliere il **fare clic per mostrare valore** pulsante per la chiave API creata di recente e copiare il valore visualizzato. Questo valore verrà immesso nel **Token segreto** campo nella scheda Provisioning dell'applicazione cornetta nel portale di Azure. 

    ![Digitare crea Token](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>Aggiungere cornetta dalla raccolta

Per configurare cornetta per provisioning utenti automatico con Azure AD, è necessario aggiungere cornetta dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere cornetta dalla raccolta di applicazioni di Azure AD, seguire i passaggi seguenti:**

1. Nel  **[portale di Azure](https://portal.azure.com)** , nel riquadro di spostamento sinistro, selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare la **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, immettere **cornetta**, selezionare **cornetta** nel pannello dei risultati.
    ![Digitare nell'elenco risultati](common/search-new-app.png)

5. Passare il **URL** evidenziato di seguito in un browser diverso. 

    ![Digitare aggiungere SCIM](media/dialpad-provisioning-tutorial/dialpad05.png)

6. Nell'angolo superiore destro, selezionare **Log In > usare cornetta online**.

    ![Digitare aggiungere SCIM](media/dialpad-provisioning-tutorial/dialpad06.png)

7. Digitare è un'app di openid Connect, scegliere di eseguire l'accesso a cornetta con l'account aziendale di Microsoft.

    ![Digitare aggiungere SCIM](media/dialpad-provisioning-tutorial/loginpage.png)

8. Dopo il completamento dell'autenticazione, accettare la richiesta di consenso per la pagina di consenso. Verrà aggiunti automaticamente all'applicazione nel tenant e si verrà reindirizzati al proprio account cornetta.

    ![Digitare aggiungere SCIM](media/dialpad-provisioning-tutorial/redirect.png)

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Configura provisioning utenti automatico per digitare

Questa sezione descrive i passaggi per configurare il provisioning di Azure AD del servizio per creare, aggiornare e disabilitare utenti e/o gruppi in cornetta base assegnazioni utente e/o di gruppo in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Per configurare il provisioning utenti automatico per digitare in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**, quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni, selezionare **cornetta**.

    ![Il collegamento cornetta nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda provisioning](common/provisioning-automatic.png)

5. Sotto il **le credenziali di amministratore** sezione di input `https://dialpad.com/scim` nelle **URL del Tenant**. Il valore è recuperato e salvato in precedenza da digitare in ingresso **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a cornetta. Se la connessione non riesce, verificare che l'account cornetta abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e Token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Notifica tramite posta elettronica](common/provisioning-notification-email.png)

7. Fare clic su **Save**.

8. Sotto il **mapping** sezione, selezionare **sincronizzazione Azure Active Directory agli utenti di digitare**.

    ![Mapping utente cornetta](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a cornetta nel **Mapping degli attributi** sezione. Gli attributi selezionati come **corrispondenti** le proprietà utilizzate per soddisfare gli account utente in cornetta per operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di digitare](media/dialpad-provisioning-tutorial/dialpad07.png)

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il provisioning del servizio per cornetta AD Azure, impostare il **stato del Provisioning** al **sul** nel **impostazioni** sezione.

    ![Lo stato di provisioning sia attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o gruppi di cui si vuole eseguire il provisioning in cornetta selezionando i valori desiderati in **ambito** nel **impostazioni** sezione.

    ![Il provisioning di ambito](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio di configurazione del Provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la **Dettagli sincronizzazione** sezione per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività, che descrivono tutte le azioni eseguite dal servizio su cornetta di provisioning di Azure AD provisioning.

Per altre informazioni su come leggere il log di provisioning di Azure AD, vedere [creazione di report sul provisioning degli account utente automatico](../manage-apps/check-status-user-account-provisioning.md)
##  <a name="connector-limitations"></a>Limitazioni dei connettori
* Digitare non supporta attualmente Rinomina gruppo. Ciò significa che tutte le modifiche per il **displayName** di un gruppo in Azure AD non essere aggiornate e riflessi in cornetta.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
