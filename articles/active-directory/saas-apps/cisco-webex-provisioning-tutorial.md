---
title: 'Esercitazione: Configurare Cisco Webex per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Cisco Webex.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: f4089d690602aa761b1942bd3d538e2ef305aa19
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73570374"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>Esercitazione: Configurare Cisco Webex per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Cisco Webex e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il deprovisioning di utenti in Cisco Webex.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Tenant di Cisco Webex](https://www.webex.com/pricing/index.html).
* Un account utente in Cisco Webex con autorizzazioni di amministratore.

## <a name="adding-cisco-webex-from-the-gallery"></a>Aggiunta di Cisco Webex dalla raccolta

Prima di configurare Cisco Webex per il provisioning utenti automatico con Azure AD, è necessario aggiungere Cisco Webex dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Cisco Webex dalla raccolta di applicazioni di Azure AD, eseguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Cisco Webex**, selezionare **Cisco Webex** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Cisco Webex nell'elenco risultati](common/search-new-app.png)

## <a name="assigning-users-to-cisco-webex"></a>Assegnazione di utenti a Cisco Webex

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti in Azure AD devono poter accedere a Cisco Webex. Una volta deciso questo, è possibile assegnare tali utenti a Cisco Webex seguendo le istruzioni riportate nell'articolo seguente:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>Suggerimenti importanti per l'assegnazione di utenti a Cisco Webex

* È consigliabile assegnare un singolo utente di Azure AD a Cisco Webex per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti aggiuntivi in un secondo momento.

* Quando si assegna un utente a Cisco Webex, è necessario selezionare un qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>Configurazione del provisioning utenti automatico in Cisco Webex

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti in Cisco Webex in base alle relative assegnazioni in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Cisco Webex in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com), selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Cisco Webex**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Cisco Webex**.

    ![Collegamento di Cisco Webex nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Provisioning di Cisco Webex](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Provisioning di Cisco Webex](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere l'**URL tenant** e il **token segreto** dell'account Cisco Webex.

    ![Provisioning di Cisco Webex](./media/cisco-webex-provisioning-tutorial/secrettoken1.png)

6.  Nel campo **URL tenant** immettere un valore in formato `https://api.ciscoweb.com/v1/scim/[OrgId]`. Per ottenere il valore di `[OrgId]`, accedere a [Cisco Webex Control Hub](https://admin.webex.com/login). Fare clic sul nome dell'organizzazione in basso a sinistra e copiare il valore di **Organization ID** (ID organizzazione). 

    * Per ottenere il valore del **token segreto**, passare a questo [URL](https://idbroker.webex.com/idb/saml2/jsp/doSSO.jsp?type=login&goto=https%3A%2F%2Fidbroker.webex.com%2Fidb%2Foauth2%2Fv1%2Fauthorize%3Fresponse_type%3Dtoken%26client_id%3DC4ca14fe00b0e51efb414ebd45aa88c1858c3bfb949b2405dba10b0ca4bc37402%26redirect_uri%3Dhttp%253A%252F%252Flocalhost%253A3000%252Fauth%252Fcode%26scope%3Dspark%253Apeople_read%2520spark%253Apeople_write%2520Identity%253ASCIM%26state%3Dthis-should-be-a-random-string-for-security-purpose). Nella pagina di accesso di Webex visualizzata, accedere con l'account di amministratore di Cisco Webex completo per l'organizzazione. Viene visualizzata una pagina di errore che informa che non è possibile raggiungere il sito. Si tratta di un comportamento normale.

        ![Provisioning di Cisco Webex](./media/cisco-webex-provisioning-tutorial/test.png)
 
    * Copiare il valore del token di connessione generato dall'URL, come illustrato di seguito. Questo token è valido per 365 giorni.
        
        ![Provisioning di Cisco Webex](./media/cisco-webex-provisioning-tutorial/test1.png)

7. Dopo aver completato i campi indicati nel passaggio 5, fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Cisco Webex. Se la connessione non riesce, verificare che l'account Cisco Webex abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)
   
8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

9. Fare clic su **Save**.

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Cisco Webex** (Sincronizza utenti di Azure Active Directory in Cisco Webex).

    ![Provisioning di Cisco Webex](./media/cisco-webex-provisioning-tutorial/usermapping.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD a Cisco Webex nella sezione **Mapping degli attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Cisco Webex per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Provisioning di Cisco Webex](./media/cisco-webex-provisioning-tutorial/usermappingattributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per Cisco Webex, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Cisco Webex selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning di Azure AD in Cisco Webex.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Cisco Webex è attualmente nella fase EFT (Early Field Testing) di Cisco. Per altre informazioni, contattare il [team di supporto Cisco](https://www.webex.co.in/support/support-overview.html). 
* Per altre informazioni sulla configurazione di Cisco Webex, vedere la documentazione Cisco disponibile [qui](https://help.webex.com/en-us/aumpbz/Synchronize-Azure-Active-Directory-Users-into-cisco-webex-Control-Hub).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)