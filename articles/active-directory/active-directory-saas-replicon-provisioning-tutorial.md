---
title: 'Esercitazione: Configurare Replicon per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Replicon.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: v-ant
ms.openlocfilehash: 8d612012505ea43a3635650c6a38fe993b8e57f6
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="tutorial-configure-replicon-for-automatic-user-provisioning"></a>Esercitazione: Configurare Replicon per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Replicon e Azure Active Directory (Azure AD) per configurare Azure AD in modo da eseguire automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Replicon.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

*   Un tenant di Azure AD
*   Tenant di Replicon con piano [Plus](https://www.replicon.com/time-bill-pricing/) o superiore abilitato
*   Account utente in Replicon con autorizzazioni di amministratore

> [!NOTE]
> L'integrazione del provisioning di Azure AD è basata sull'[API di Replicon](https://www.replicon.com/help/developers), disponibile per i team di Replicon con piano Plus o superiore.

## <a name="adding-replicon-from-the-gallery"></a>Aggiunta di Replicon dalla raccolta
Prima di configurare Replicon per il provisioning utenti automatico con Azure AD, è necessario aggiungere Replicon dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Replicon dalla raccolta di applicazioni di Azure AD, eseguire queste operazioni:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Sezione Applicazioni aziendali][2]
    
3. Per aggiungere Replicon, fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Replicon**.

    ![Ricerca dell'applicazione Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconAppSearch.png)

5. Nel pannello dei risultati selezionare **Replicon** e quindi fare clic sul pulsante **Aggiungi** per aggiungere Replicon al proprio elenco di applicazioni SaaS.

    ![Risultati della ricerca di Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconAppSearchResults.png)

    ![Creazione dell'applicazione Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconAppCreate.png)


## <a name="assigning-users-to-replicon"></a>Assegnazione di utenti a Replicon

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a Replicon. Dopo aver definito questo aspetto, è possibile assegnare gli utenti e/o i gruppi a Replicon seguendo le istruzioni fornite qui:

*   [Assegnare un utente o gruppo a un'app aziendale](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-replicon"></a>Suggerimenti importanti per l'assegnazione di utenti a Replicon

*   È consigliabile assegnare un singolo utente di Azure AD a Replicon per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

*   Quando si assegna un utente a Replicon, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-replicon"></a>Configurazione del provisioning utenti automatico per Replicon 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Replicon in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Replicon, seguendo le istruzioni fornite nell'[esercitazione sull'accesso Single Sign-On per Replicon](active-directory-saas-replicon-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-replicon-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Replicon in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com) e passare a **Azure Active Directory > App aziendali > Tutte le applicazioni**.

2. Selezionare Replicon nell'elenco delle applicazioni SaaS.

    ![Provisioning per Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/Replicon2.png)

3. Selezionare la scheda **Provisioning**.

    ![Provisioning per Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconProvisioningTab.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Provisioning per Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/Replicon1.png)

5. Nella sezione **Credenziali amministratore** completare i campi **Nome utente amministratore**, **Password amministratore**, **Identificatore società** e **Dominio** dell'account Replicon. Ecco alcuni esempi di questi valori:

    *   Nel campo **Nome utente amministratore** immettere il nome utente dell'account amministratore nel tenant di Replicon. Esempio: contosoadmin.

    *   Nel campo **Password amministratore** inserire la password corrispondente al nome utente amministratore.

    *   Nel campo **Identificatore società** immettere l'ID società del tenant di Replicon. Esempio: l'ID società basato sull'account di accesso seguente è Contoso.

    ![Accesso a Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconLogin.png)

    *   Nel campo **Dominio** immettere il dominio come descritto nel passaggio 6.
    
6. Ottenere **serviceEndpointRootURL** per l'account del tenant di Replicon in base alla procedura descritta in [Replicon Service Help](https://www.replicon.com/help/determining-the-url-for-your-service-calls) (Guida del servizio Replicon). Al momento di ottenere l'URL, il **dominio** sarà il sottodominio di **serviceEndpointRootURL**, come evidenziato. 

    ![Provisioning per Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconEndpoint.png)

7. Dopo aver completato i campi indicati nel passaggio 5, fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Replicon. Se la connessione non riesce, verificare che l'account Replicon abbia autorizzazioni di amministratore e riprovare.

    ![Provisioning per Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconTestConnection.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Provisioning per Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconNotificationEmail.png)

9. Fare clic su **Save**.

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Replicon** (Sincronizza utenti di Azure Active Directory con Replicon).
    
    ![Provisioning per Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconUserMapping.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD a Replicon nella sezione **Mapping degli attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Replicon per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Provisioning per Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconUserMappingAtrributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](./active-directory-saas-scoping-filters.md).

13. Per abilitare il servizio di provisioning di Azure AD per Replicon, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Provisioning per Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconProvisioningStatus.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Replicon selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Provisioning per Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/UserGroupSelection.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Provisioning per Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/SaveProvisioning.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning di Azure AD in Replicon.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](./active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](active-directory-enterprise-apps-manage-provisioning.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_03.png
