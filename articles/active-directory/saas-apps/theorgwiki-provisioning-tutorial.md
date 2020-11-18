---
title: 'Esercitazione: Configurare TheOrgWiki per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in TheOrgWiki.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 8238b9902aafcabc079c551a0eabc7170042209a
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357624"
---
# <a name="tutorial-configure-theorgwiki-for-automatic-user-provisioning"></a>Esercitazione: Configurare TheOrgWiki per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in TheOrgWiki e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in TheOrgWiki.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Un tenant di OrgWiki](https://www.theorgwiki.com/welcome/).
* Un account utente in TheOrgWiki con autorizzazioni di amministratore.

## <a name="assign-users-to-theorgwiki"></a>Assegnare utenti a TheOrgWiki

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle assegnazioni. Nel contesto del provisioning utenti automatico vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a TheOrgWiki. Dopo averlo stabilito, è possibile assegnare gli utenti e/o i gruppi a TheOrgWiki seguendo le istruzioni fornite qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-theorgwiki"></a>Suggerimenti importanti per l'assegnazione di utenti a TheOrgWiki

* È consigliabile assegnare un singolo utente di Azure AD a TheOrgWiki per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a TheOrgWiki, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="set-up-theorgwiki-for-provisioning"></a>Configurare TheOrgWiki per il provisioning

Prima di configurare TheOrgWiki per il provisioning utenti automatico con Azure AD, è necessario abilitare il provisioning SCIM in TheOrgWiki.

1. Accedere alla [console di amministrazione di TheOrgWiki](https://www.theorgwiki.com/login/). Fare clic su **Admin Console** (Console di amministrazione).

    ![Screenshot della finestra OrgWiki con l'avatar dell'utente e Admin Console evidenziati.](media/theorgwiki-provisioning-tutorial/login.png)

2. In Admin Console (Console di amministrazione) fare clic sulla scheda **Settings** (Impostazioni). 

    ![Screenshot della console di amministrazione di TheOrgWiki con la scheda Settings evidenziata.](media/theorgwiki-provisioning-tutorial/settings.png)
    
3. Passare a **Service Accounts** (Account del servizio).

    ![Screenshot della pagina Service Accounts nella console di amministrazione di TheOrgWiki.](media/theorgwiki-provisioning-tutorial/serviceaccount.png)

4. Fare clic su **+Service Account** (+Account del servizio). In **Service Account Type** (Tipo di account del servizio) selezionare **Token Based** (Basato su token). Fare clic su **Salva**.

    ![Screenshot della finestra di dialogo New Service Account con le opzioni Service Account Type, Token Based e Save evidenziate.](media/theorgwiki-provisioning-tutorial/auth.png)

5.  Copiare il valore di **Active Tokens** (Token attivi). Questo valore verrà immesso nel campo Token segreto nella scheda Provisioning dell'applicazione TheOrgWiki nel portale di Azure.
     
    ![Screenshot della finestra di dialogo Manage Tokens for SCIM provisioning.](media/theorgwiki-provisioning-tutorial/token.png)

## <a name="add-theorgwiki-from-the-gallery"></a>Aggiungere TheOrgWiki dalla raccolta

Per configurare TheOrgWiki per il provisioning utenti automatico con Azure AD, è necessario aggiungere TheOrgWiki dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **TheOrgWiki**, quindi selezionare **TheOrgWiki** nel pannello dei risultati. 

    ![TheOrgWiki nell'elenco risultati](common/search-new-app.png)

5. Fare clic sul pulsante **Sign-up for TheOrgWiki** (Iscrizione per TheOrgWiki) per essere reindirizzati alla pagina di accesso di TheOrgWiki. 

    ![Screenshot della pagina di accesso di TheOrgWiki con l'URL evidenziato](media/theorgwiki-provisioning-tutorial/image00.png)

6.  Nell'angolo superiore destro selezionare **Login** (Accedi).

    ![Screenshot dell'angolo superiore destro della pagina di accesso con l'opzione Login evidenziata.](media/theorgwiki-provisioning-tutorial/image02.png)

7. Poiché TheOrgWiki è un'app OpenIDConnect, scegliere di accedere a TheOrgWiki usando l'account aziendale Microsoft.

    ![Screenshot della pagina di accesso di The Org Wiki con l'opzione Sign in with Microsoft evidenziata.](media/theorgwiki-provisioning-tutorial/image03.png)
    
8. Dopo l'autenticazione, l'applicazione verrà aggiunta automaticamente al tenant e si verrà reindirizzati all'account TheOrgWiki.

    ![Aggiunta di SCIM in TheOrgWiki](media/theorgwiki-provisioning-tutorial/image04.png)

## <a name="configure-automatic-user-provisioning-to-theorgwiki"></a>Configurare il provisioning utenti automatico in TheOrgWiki 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TheOrgWiki in base alle assegnazioni di utenti e/o gruppi in Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-theorgwiki-in-azure-ad"></a>Per configurare il provisioning utenti automatico per TheOrgWiki in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **TheOrgWiki**.

    ![Collegamento di TheOrgWiki nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatico evidenziata.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere `https://<TheOrgWiki Subdomain        value>.theorgwiki.com/api/v2/scim/v2/` in **URL tenant**. 

    Esempio: `https://test1.theorgwiki.com/api/v2/scim/v2/`

> [!NOTE]
> Il valore di **Subdomain Value** può essere impostato solo durante il processo di iscrizione iniziale a TheOrgWiki.
 
6. Nel campo **Token segreto** immettere il valore del token che è stato recuperato in precedenza da TheOrgWiki. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a TheOrgWiki. Se la connessione non riesce, verificare che l'account TheOrgWiki abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

7. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

8. Fare clic su **Salva**.

9. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to TheOrgWiki** (Sincronizza utenti di Azure Active Directory in TheOrgWiki).

    ![Mapping utente in TheOrgWiki](media/theorgwiki-provisioning-tutorial/usermapping.png)

10. Esaminare gli attributi utente sincronizzati da Azure AD a TheOrgWiki nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per l'abbinamento con gli account utente in TheOrgWiki per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di TheOrgWiki](media/theorgwiki-provisioning-tutorial/userattribute.png).

11. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Per abilitare il servizio di provisioning di Azure AD per TheOrgWiki, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

13. Definire gli utenti e/o i gruppi di cui si vuole effettuare il provisioning in TheOrgWiki selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

14. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo rispetto a quelle successive. Per altre informazioni sul tempo necessario per effettuare il provisioning di utenti e/o gruppi, vedere [Quanto tempo sarà necessario per eseguire il provisioning degli utenti?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

È possibile usare la sezione **Stato corrente** per monitorare lo stato di avanzamento e selezionare i collegamenti al report delle attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in TheOrgWiki. Per altre informazioni, vedere [Controllare lo stato del provisioning utenti](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Per leggere i log di provisioning di Azure AD, vedere [Creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md).