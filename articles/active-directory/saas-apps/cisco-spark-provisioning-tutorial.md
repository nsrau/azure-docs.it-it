---
title: 'Esercitazione: configurare Cisco per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Cisco Spark.
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
ms.openlocfilehash: bb3b3061c15a661caff778ca5c0ec48b0434c718
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2018
ms.locfileid: "42146441"
---
# <a name="tutorial-configure-cisco-spark-for-automatic-user-provisioning"></a>Esercitazione: configurare Cisco Spark per il provisioning utenti automatico


L'obiettivo di questa esercitazione è di dimostrare la procedura da eseguire in Cisco Spark e Azure Active Directory per configurare Azure AD in modo da eseguire automaticamente il provisioning e il deprovisioning di utenti in Cisco Spark.


> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](./../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

*   Un tenant di Azure AD
*   Un tenant di Cisco Spark
*   Un account utente in Cisco Spark con autorizzazioni di amministratore


> [!NOTE]
> L'integrazione del provisioning di Azure AD si basa sul [servizio Web Cisco Spark](https://developer.webex.com/getting-started.html), disponibile per i team di Cisco Spark.

## <a name="adding-cisco-spark-from-the-gallery"></a>Aggiunta di Cisco Spark dalla raccolta
Prima di configurare Cisco Spark per il provisioning utenti automatico con Azure AD, è necessario aggiungerlo dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Cisco Spark dalla raccolta di applicazioni di Azure AD, eseguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Sezione Applicazioni aziendali][2]

3. Per aggiungere Cisco Spark, fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Cisco Spark**.

    ![Provisioning di Cisco Spark](./media/cisco-spark-provisioning-tutorial/AppSearch.png)

5. Nel riquadro dei risultati selezionare **Cisco Spark** e quindi fare clic sul pulsante **Aggiungi** per aggiungere Cisco Spark all'elenco delle applicazioni SaaS.

    ![Provisioning di Cisco Spark](./media/cisco-spark-provisioning-tutorial/AppSearchResults.png)

    ![Provisioning di Cisco Spark](./media/cisco-spark-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cisco-spark"></a>Assegnazione di utenti a Cisco Spark

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti in Azure AD devono accedere a Cisco Spark. Dopo averlo deciso, è possibile assegnare tali utenti a Cisco Spark seguendo le istruzioni riportate qui:

*   [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-spark"></a>Suggerimenti importanti per l'assegnazione di utenti a Cisco Spark

*   È consigliabile assegnare un singolo utente di Azure AD a Cisco Spark per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti aggiuntivi in un secondo momento.

*   Quando si assegna un utente a Cisco Spark, è necessario selezionare un qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-cisco-spark"></a>Configurazione del provisioning utenti automatico in Cisco Spark

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti in Cisco Spark in base alle assegnazioni di utenti in Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-cisco-spark-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Cisco Spark in Azure AD:


1. Accedere al [portale di Azure](https://portal.azure.com) e passare a **Azure Active Directory > App aziendali > Tutte le applicazioni**.

2. Selezionare Cisco Spark nell'elenco delle applicazioni SaaS.

    ![Provisioning di Cisco Spark](./media/cisco-spark-provisioning-tutorial/Successcenter2.png)

3. Selezionare la scheda **Provisioning**.

    ![Provisioning di Cisco Spark](./media/cisco-spark-provisioning-tutorial/ProvisioningTab.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Provisioning di Cisco Spark](./media/cisco-spark-provisioning-tutorial/ProvisioningCredentials.png)

5. Nella sezione **Credenziali amministratore** immettere l'**URL tenant** e il **Token segreto** del proprio account Cisco Spark.

    *   Nel campo **URL tenant** immettere l'URL dell'API SCIM di Cisco Spark per il tenant, nel formato `https://api.ciscospark.com/v1/scim/[Tenant ID]/`, dove `[Tenant ID]` è una stringa alfanumerica come descritto nel passaggio 6.

    *   Nel campo **Token segreto** immettere il token del segreto come descritto nel passaggio 6.

1. Per trovare i valori di **ID tenant** e **Token segreto** del proprio account Cisco Spark, accedere al [sito per sviluppatori di Cisco Spark](https://developer.webex.com/) con l'account amministratore. Dopo aver eseguito l'accesso:
    * Passare alla [pagina Getting Started](https://developer.webex.com/getting-started.html) (Introduzione)
    * Scorrere fino alla [sezione Authentication](https://developer.webex.com/getting-started.html#authentication) (Autenticazione) 
    ![Token di autenticazione di Cisco Spark](./media/cisco-spark-provisioning-tutorial/SecretToken.png)
    * La stringa alfanumerica riportata nella casella è il **token segreto**. Copiare il token negli Appunti
    * Passare alla [pagina Get My Own Details](https://developer.webex.com/endpoint-people-me-get.html) (Ottieni dettagli personali)
        * Verificare che la modalità di test sia attiva
        * Digitare la parola "Bearer" seguita da uno spazio e quindi incollare il token segreto nel campo Authorization (Autorizzazione) ![Token di autenticazione di Cisco Spark](./media/cisco-spark-provisioning-tutorial/GetMyDetails.png)
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

1. Dopo aver completato i campi indicati nel passaggio 5, fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Cisco Spark. Se la connessione non riesce, verificare che l'account Cisco Spark abbia autorizzazioni di amministratore e riprovare.

    ![Provisioning di Cisco Spark](./media/cisco-spark-provisioning-tutorial/TestConnection.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Provisioning di Cisco Spark](./media/cisco-spark-provisioning-tutorial/EmailNotification.png)

9. Fare clic su **Save**.

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Cisco Spark** (Sincronizza utenti di Azure Active Directory in Cisco Spark).

    ![Provisioning di Cisco Spark](./media/cisco-spark-provisioning-tutorial/UserMapping.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD a Cisco Spark nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **Matching** vengono usati per trovare le corrispondenze con gli account utente in Cisco Spark per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Provisioning di Cisco Spark](./media/cisco-spark-provisioning-tutorial/UserMappingAttributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../active-directory-saas-scoping-filters.md).

13. Per abilitare il servizio di provisioning di Azure AD per Cisco Spark, impostare **Stato del provisioning** su **Attivo** nella sezione **Impostazioni**.

    ![Provisioning di Cisco Spark](./media/cisco-spark-provisioning-tutorial/ProvisioningStatus.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Cisco Spark selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Provisioning di Cisco Spark](./media/cisco-spark-provisioning-tutorial/SyncScope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Provisioning di Cisco Spark](./media/cisco-spark-provisioning-tutorial/Save.png)


L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare l'avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning di Azure AD in Cisco Spark.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../active-directory-saas-provisioning-reporting.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Cisco Spark è attualmente nella fase EFT (Early Field Testing) di Cisco. Per altre informazioni, contattare il [team di supporto Cisco](https://www.webex.co.in/support/support-overview.html). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_03.png
