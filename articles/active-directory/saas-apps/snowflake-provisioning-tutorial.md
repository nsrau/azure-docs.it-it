---
title: 'Esercitazione: Configurare Snowflake per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in Snowflake.Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to Snowflake.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f9ce85f4-0992-4bc6-8276-4b2efbce8dcb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2c5d91894ba35233f3fbebffdff9104edcfdd27b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063152"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Esercitazione: Configurare Snowflake per il provisioning automatico degli utentiTutorial: Configure Snowffor automatic user provisioning

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Snowflake e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e/o gruppi in Snowflake.The objective of this tutorial is to demonstrate the steps to be performed in Snowflake and Azure Active Directory (Azure AD) to configure Azure AD to automatically provision and de-provisioning users and/or groups to Snowflake.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Un inquilino fiocco](https://www.Snowflake.com/pricing/)di neve .
* Un account utente in Snowflake con autorizzazioni di amministratore.

## <a name="assigning-users-to-snowflake"></a>Assegnazione di utenti a Fiocco di neve

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a Snowflake. Una volta deciso, è possibile assegnare questi utenti e/o gruppi a Snowflake seguendo le istruzioni qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-snowflake"></a>Suggerimenti importanti per l'assegnazione di utenti a Snowflake

* È consigliabile assegnare un singolo utente di Azure AD a Snowflake per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Snowflake, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="setup-snowflake-for-provisioning"></a>Configurare Snowflake per il provisioning

Prima di configurare Snowflake per il provisioning automatico degli utenti con Azure AD, è necessario abilitare il provisioning SCIM su Snowflake.

1. Accedi alla Console di amministrazione di Snowflake. Immettere la query illustrata di seguito nel foglio di lavoro evidenziato e fare clic su **Esegui**.

    ![Console di amministrazione fiocco di neve](media/Snowflake-provisioning-tutorial/image00.png)

2.  Verrà generato un token di accesso SCIM per il tenant di Snowflake. Per recuperarlo, clicca sul link evidenziato qui sotto.

    ![Fiocco di neve Aggiungi SCIM](media/Snowflake-provisioning-tutorial/image01.png)

3. Copiare il valore del token generato e fare clic su **Fine**. Questo valore verrà immesso nel campo **Token segreto** nella scheda Provisioning dell'applicazione Snowflake nel portale di Azure.This value will be entered in the Secret Token field in the Provisioning tab of your Snowflake application in the Azure portal.

    ![Fiocco di neve Aggiungi SCIM](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="add-snowflake-from-the-gallery"></a>Aggiungi Fiocco di neve dalla galleria

Per configurare Snowflake per il provisioning automatico degli utenti con Azure AD, è necessario aggiungereSnowflake dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere Snowflake dalla raccolta di applicazioni di Azure AD, eseguire la procedura seguente:To add Snowflake from the Azure AD application gallery, perform the following steps:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Fiocco**di neve , selezionare **Fiocco** di neve nel riquadro dei risultati, quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Snowflake nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-snowflake"></a>Configurazione del provisioning automatico degli utenti su Snowflake 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Snowflake in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Fiocco di neve , seguendo le istruzioni fornite [nell'esercitazione single sign-on Snowflake](Snowflake-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per Snowflake in Azure AD:To configure automatic user provisioning for Snowflake in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco applicazioni selezionare **Snowflake**.

    ![Collegamento di Snowflake nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione Credenziali amministratore `https://<Snowflake Account URL>/scim/v2` immettere l'URL del tenant. Un esempio dell'URL del tenant:`https://acme.snowflakecomputing.com/scim/v2`

6. Immettere il valore del token di **autenticazione SCIM** recuperato in precedenza in **Secret Token**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Fiocco di neve. Se la connessione non riesce, verificare che l'account Snowflake disponga delle autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

7. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

8. Fare clic su **Salva**.

9. Nella sezione **Mapping** selezionare Sincronizza utenti di **Azure Active Directory con Snowflake**.

    ![Mappature degli utenti a fiocco di neve](media/Snowflake-provisioning-tutorial/user-mapping.png)

10. Esaminare gli attributi utente sincronizzati da Azure AD a Snowflake nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in Snowflake per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente fiocco di neve](media/Snowflake-provisioning-tutorial/user-attribute.png)

11. Nella sezione **Mapping** selezionare Sincronizza gruppi di **Azure Active Directory con fiocco**di neve.

    ![Mappature dei gruppi di fiocchi di neve](media/Snowflake-provisioning-tutorial/group-mapping.png)

12. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Snowflake nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare i gruppi in Fiocco di neve per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi del gruppo Fiocco di neve](media/Snowflake-provisioning-tutorial/group-attribute.png)

13. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Per abilitare il servizio di provisioning di Azure AD per Snowflake, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for Snowflake, change the **Provisioning Status** to On in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

15. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in Snowflake scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.** Se questa opzione non è disponibile, configurare i campi obbligatori in Credenziali amministratore, fare clic su **Salva** e aggiornare la pagina. 

    ![Ambito di provisioning](common/provisioning-scope.png)

16. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

    L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Snowflake.You can use the Synchronization Details section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on Snowflake.

    Per altre informazioni su come leggere i log di provisioning di Azure AD, vedere Creazione di report sul provisioning automatico degli [account utenteFor](../app-provisioning/check-status-user-account-provisioning.md) more information on how to read the Azure AD provisioning logs, see Reporting on automatic user account provisioning

## <a name="connector-limitations"></a>Limitazioni dei connettori

* I token SCIM generati da Snowflake scadono tra 6 mesi. Tieni presente che questi devono essere aggiornati prima di scadere per consentire alle sincronizzazioni di provisioning di continuare a funzionare. 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi
* [Scopri come esaminare i log e ottenere report sulle attività](../app-provisioning/check-status-user-account-provisioning.md)di provisioning.
