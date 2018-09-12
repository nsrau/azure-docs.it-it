---
title: 'Esercitazione: Configurare Cisco per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Cisco Webex.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: v-wingf
ms.openlocfilehash: 8f5af3cba01e925591c9d90ea0e96ed78b2823e2
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2018
ms.locfileid: "44348379"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>Esercitazione: Configurare Cisco Webex per il provisioning utenti automatico


Questa esercitazione descrive la procedura da eseguire in Cisco Webex e Azure Active Directory (Azure AD) per configurare Azure AD in modo da eseguire automaticamente il provisioning e il deprovisioning di utenti in Cisco Webex.


> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

*   Un tenant di Azure AD
*   Tenant di Cisco Webex
*   Un account utente in Cisco Webex con autorizzazioni di amministratore


> [!NOTE]
> L'integrazione del provisioning di Azure AD si basa sul [servizio Web Cisco Webex](https://developer.webex.com/getting-started.html), disponibile per i team di Cisco Webex.

## <a name="adding-cisco-webex-from-the-gallery"></a>Aggiunta di Cisco Webex dalla raccolta
Prima di configurare Cisco Webex per il provisioning utenti automatico con Azure AD, è necessario aggiungere Cisco Webex dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Cisco Webex dalla raccolta di applicazioni di Azure AD, eseguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Sezione Applicazioni aziendali][2]

3. Per aggiungere Cisco Webex, fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Cisco Webex**.

    ![Provisioning di Cisco Webex](./media/cisco-webex-provisioning-tutorial/AppSearch.png)

5. Nel riquadro dei risultati selezionare **Cisco Webex** e quindi fare clic sul pulsante **Aggiungi** per aggiungere Cisco Webex all'elenco delle applicazioni SaaS.

    ![Provisioning di Cisco Webex](./media/cisco-webex-provisioning-tutorial/AppSearchResults.png)

    ![Provisioning di Cisco Webex](./media/cisco-webex-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cisco-webex"></a>Assegnazione di utenti a Cisco Webex

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti in Azure AD devono poter accedere a Cisco Webex. Una volta deciso questo, è possibile assegnare tali utenti a Cisco Webex seguendo le istruzioni riportate nell'articolo seguente:

*   [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>Suggerimenti importanti per l'assegnazione di utenti a Cisco Webex

*   È consigliabile assegnare un singolo utente di Azure AD a Cisco Webex per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti aggiuntivi in un secondo momento.

*   Quando si assegna un utente a Cisco Webex, è necessario selezionare un qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>Configurazione del provisioning utenti automatico in Cisco Webex

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti in Cisco Webex in base alle relative assegnazioni in Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Cisco Webex in Azure AD:


1. Accedere al [portale di Azure](https://portal.azure.com) e passare a **Azure Active Directory > App aziendali > Tutte le applicazioni**.

2. Selezionare Cisco Webex nell'elenco delle applicazioni SaaS.

    ![Provisioning di Cisco Webex](./media/cisco-webex-provisioning-tutorial/Successcenter2.png)

3. Selezionare la scheda **Provisioning**.

    ![Provisioning di Cisco Webex](./media/cisco-webex-provisioning-tutorial/ProvisioningTab.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Provisioning di Cisco Webex](./media/cisco-webex-provisioning-tutorial/ProvisioningCredentials.png)

5. Nella sezione **Credenziali amministratore** immettere l'**URL tenant** e il **Token segreto** del proprio account Cisco Webex.

    *   Nel campo **URL tenant** immettere l'URL dell'API SCIM di Cisco Webex per il tenant, nel formato `https://api.webex.com/v1/scim/[Tenant ID]/`, dove `[Tenant ID]` è una stringa alfanumerica come descritto nel passaggio 6.

    *   Nel campo **Token segreto** immettere il token del segreto come descritto nel passaggio 6.

1. Per trovare i valori di **ID tenant** e **Token segreto** del proprio account Cisco Webex, accedere al [sito per sviluppatori Cisco Webex](https://developer.webex.com/) con l'account amministratore. Dopo aver eseguito l'accesso -
    * Passare alla pagina [Getting Started](https://developer.webex.com/getting-started.html) (Introduzione)
    * Scorrere fino alla sezione [Authentication](https://developer.webex.com/getting-started.html#authentication) (Autenticazione)
    ![Token di autenticazione di Cisco Webex](./media/cisco-webex-provisioning-tutorial/SecretToken.png)
    * La stringa alfanumerica riportata nella casella è il **token segreto**. Copiare il token negli Appunti
    * Passare alla [pagina Get My Own Details](https://developer.webex.com/endpoint-people-me-get.html) (Ottieni dettagli personali)
        * Verificare che la modalità di test sia attiva
        * Digitare la parola "Bearer" seguita da uno spazio e quindi incollare il token segreto nel campo Authorization (Autorizzazione) ![Token di autenticazione di Cisco Webex](./media/cisco-webex-provisioning-tutorial/GetMyDetails.png)
        * Fare clic su Run (Esegui)
    * Nel testo della risposta a destra l'**ID tenant** compare come "orgId":

    ```json
    {
        "id": "(...)",
        "emails": [
            "admin.user@contoso.com"
        ],
        "displayName": "John Smith",
        "nickName": "John",
        "orgId": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
        (...)
    }
    ```

1. Dopo aver completato i campi indicati nel passaggio 5, fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Cisco Webex. Se la connessione non riesce, verificare che l'account Cisco Webex abbia autorizzazioni di amministratore e riprovare.

    ![Provisioning di Cisco Webex](./media/cisco-webex-provisioning-tutorial/TestConnection.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Provisioning di Cisco Webex](./media/cisco-webex-provisioning-tutorial/EmailNotification.png)

9. Fare clic su **Save**.

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Cisco Webex** (Sincronizza utenti di Azure Active Directory in Cisco Webex).

    ![Provisioning di Cisco Webex](./media/cisco-webex-provisioning-tutorial/UserMapping.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD a Cisco Webex nella sezione **Mapping degli attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Cisco Webex per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Provisioning di Cisco Webex](./media/cisco-webex-provisioning-tutorial/UserMappingAttributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per Cisco Webex, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Provisioning di Cisco Webex](./media/cisco-webex-provisioning-tutorial/ProvisioningStatus.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Cisco Webex selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Provisioning di Cisco Webex](./media/cisco-webex-provisioning-tutorial/SyncScope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Provisioning di Cisco Webex](./media/cisco-webex-provisioning-tutorial/Save.png)


L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning di Azure AD in Cisco Webex.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Cisco Webex è attualmente nella fase EFT (Early Field Testing) di Cisco. Per altre informazioni, contattare il [team di supporto Cisco](https://www.webex.co.in/support/support-overview.html). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cisco-webex-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cisco-webex-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cisco-webex-provisioning-tutorial/tutorial_general_03.png
