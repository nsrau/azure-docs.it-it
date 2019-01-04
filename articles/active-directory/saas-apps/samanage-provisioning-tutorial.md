---
title: 'Esercitazione: Configurare Samanage per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Samanage.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2018
ms.author: v-wingf-msft
ms.openlocfilehash: d3442710e1e1327dcafc1b4ed6617aeb7ff1bf0f
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322431"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Esercitazione: Configurare Samanage per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Samanage e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Samanage.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

*   Un tenant di Azure AD
*   Un [tenant di Samanage](https://www.samanage.com/pricing/) con il piano Professional
*   Account utente in Samanage con autorizzazioni di amministratore

> [!NOTE]
> L'integrazione del provisioning di Azure AD è basata sull'[API REST di Samanage](https://www.samanage.com/api/) disponibile per gli sviluppatori Samanage per gli account con il pacchetto Professional.

## <a name="adding-samanage-from-the-gallery"></a>Aggiunta di Samanage dalla raccolta
Prima di configurare Samanage per il provisioning utenti automatico con Azure AD, è necessario aggiungere Samanage dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Samanage dalla raccolta di applicazioni di Azure AD, eseguire queste operazioni:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Sezione Applicazioni aziendali][2]

3. Per aggiungere Samanage, fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Samanage**.

    ![Provisioning di Samanage](./media/samanage-provisioning-tutorial/AppSearch.png)

5. Nel pannello dei risultati selezionare **Samanage** e quindi fare clic sul pulsante **Aggiungi** per aggiungere Samanage al proprio elenco di applicazioni SaaS.

    ![Provisioning di Samanage](./media/samanage-provisioning-tutorial/AppSearchResults.png)

    ![Provisioning di Samanage](./media/samanage-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-samanage"></a>Assegnazione di utenti a Samanage

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a Samanage. Dopo aver definito questo aspetto, è possibile assegnare gli utenti e/o i gruppi a Samanage seguendo le istruzioni fornite qui:

*   [Assegnare un utente o gruppo a un'app aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-samanage"></a>Suggerimenti importanti per l'assegnazione di utenti a Samanage

*    I ruoli di Samanage vengono popolati oggi stesso in modo automatico e dinamico nell'interfaccia utente del portale di Azure. Prima di assegnare i ruoli Samanage agli utenti, assicurarsi di completare una sincronizzazione iniziale con Samanage per recuperare gli ultimi ruoli nel tenant Samanage.

*    È consigliabile assegnare un singolo utente di Azure AD a Samanage per testare la configurazione iniziale del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento, una volta completati i test.

*   Quando si assegna un utente a Samanage, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-samanage"></a>Configurazione del provisioning utenti automatico per Samanage

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Samanage in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Samanage, seguendo le istruzioni fornite nell'[esercitazione sull'accesso Single Sign-On per Samanage](samanage-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Samanage in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com) e passare a **Azure Active Directory > App aziendali > Tutte le applicazioni**.

2. Selezionare Samanage nell'elenco delle applicazioni SaaS.

    ![Provisioning di Samanage](./media/samanage-provisioning-tutorial/AppInstanceSearch.png)

3. Selezionare la scheda **Provisioning**.

    ![Provisioning di Samanage](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Provisioning di Samanage](./media/samanage-provisioning-tutorial/ProvisioningCredentials.png)

5. Nella sezione **Credenziali amministratore** completare i campi **Nome utente amministratore** e **Password amministratore** dell'account Samanage. Ecco alcuni esempi di questi valori:

    *   Nel campo **Nome utente amministratore** immettere il nome utente dell'account amministratore nel tenant di Samanage. Esempio: admin@contoso.com.

    *   Nel campo **Password amministratore** inserire la password dell'account amministratore corrispondente al nome utente amministratore.

6. Dopo aver completato i campi indicati nel passaggio 5, fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Samanage. Se la connessione non riesce, verificare che l'account Samanage abbia autorizzazioni di amministratore e riprovare.

    ![Provisioning di Samanage](./media/samanage-provisioning-tutorial/TestConnection.png)

7. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Provisioning di Samanage](./media/samanage-provisioning-tutorial/EmailNotification.png)

8. Fare clic su **Save**.

9. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Samanage** (Sincronizza utenti di Azure Active Directory con Samanage).

    ![Provisioning di Samanage](./media/samanage-provisioning-tutorial/UserMappings.png)

10. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a Samanage. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Samanage per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Provisioning di Samanage](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

11. Per consentire il mapping di gruppo, nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to Samanage** (Sincronizza gruppi di Azure Active Directory con Samanage).

    ![Provisioning di Samanage](./media/samanage-provisioning-tutorial/GroupMappings.png)

12. Impostare **Abilitato** su **Sì** per sincronizzare i gruppi. Esaminare gli attributi gruppo sincronizzati da Azure AD a Samanage nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Samanage per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Provisioning di Samanage](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Per abilitare il servizio di provisioning di Azure AD per Samanage, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Provisioning di Samanage](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

15. Definire gli utenti e/o i gruppi di cui si vuole effettuare il provisioning in Samanage selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**. Quando si sceglie l'opzione **Sincronizza tutti gli utenti e i gruppi**, prendere in considerazione le limitazioni descritte nella sezione **Limitazioni dei connettori** più avanti.

    ![Provisioning di Samanage](./media/samanage-provisioning-tutorial/ScopeSync.png)

16. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Provisioning di Samanage](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning di Azure AD in Samanage.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Se viene selezionata l'opzione **Sincronizza tutti gli utenti e i gruppi** ed è configurato un valore predefinito per l'attributo **roles** di Samanage, assicurarsi che il valore desiderato nel campo **Valore predefinito se Null (facoltativo)** sia espresso nel formato seguente **{"displayName":"role"}**, dove role indica il valore predefinito desiderato.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
