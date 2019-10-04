---
title: 'Esercitazione: Configurare fiocco di neve per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in fiocco di neve.
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
ms.openlocfilehash: 04d9ec8cad2404466d2df649df4d5c461768b76f
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68693658"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Esercitazione: Configurare fiocco di neve per il provisioning utenti automatico

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in fiocco di neve e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in fiocco di neve.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per ulteriori informazioni sulle condizioni per l'utilizzo di Microsoft Azure generali per le funzionalità di anteprima, vedere le [condizioni per l'utilizzo supplementari per](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)le anteprime di Microsoft Azure.

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Un tenant A fiocco di neve](https://www.Snowflake.com/pricing/).
* Un account utente in fiocco di pagina con autorizzazioni di amministratore.

## <a name="assigning-users-to-snowflake"></a>Assegnazione di utenti a fiocco di neve

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a fiocco di neve. Dopo aver stabilito questo, è possibile assegnare questi utenti e/o gruppi a fiocco di neve seguendo le istruzioni riportate qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-snowflake"></a>Suggerimenti importanti per l'assegnazione di utenti a fiocco di neve

* Per testare la configurazione del provisioning utenti automatico, è consigliabile assegnare un singolo Azure AD utente a fiocco di neve. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a fiocco di neve, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="setup-snowflake-for-provisioning"></a>Imposta fiocco di neve per il provisioning

Prima di configurare il provisioning utenti automatico con Azure AD, sarà necessario abilitare il provisioning di SCIM in fiocco di neve.

> [!NOTE]
> Questa integrazione è attualmente in anteprima privata a fiocco di neve. Se si vuole abilitare questa funzionalità nell'account fiocco di neve, contattare il rappresentante di fiocco di neve.

1. Accedere alla console di amministrazione di fiocco di neve. Immettere la query riportata di seguito nell'area di lavoro evidenziata e fare clic su **Esegui**.

    ![Console di amministrazione di fiocco di neve](media/Snowflake-provisioning-tutorial/image00.png)

2.  Verrà generato un token di accesso SCIM per il tenant di fiocco di neve. Per recuperarlo, fare clic sul collegamento evidenziato di seguito.

    ![Fiocco di SCIM Add](media/Snowflake-provisioning-tutorial/image01.png)

3. Copiare il valore del token generato e fare clic su **fine**. Questo valore verrà immesso nel campo **token segreto** nella scheda provisioning dell'applicazione fiocco di neve nell'portale di Azure.

    ![Fiocco di SCIM Add](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="add-snowflake-from-the-gallery"></a>Aggiungere fiocco di neve dalla raccolta

Per configurare il provisioning utenti automatico con Azure AD, è necessario addSnowflake dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere fiocco di neve dalla raccolta di applicazioni Azure AD, seguire questa procedura:**

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere fiocco di **neve**, selezionare fiocco di **neve** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Snowflake nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-snowflake"></a>Configurazione del provisioning utenti automatico in fiocco di neve 

Questa sezione illustra i passaggi necessari per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in fiocco di neve in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-on basato su SAML per fiocco di neve, seguendo le istruzioni fornite nell' [esercitazione sull'accesso Single Sign-on](Snowflake-tutorial.md)a fiocco di neve. L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>Per configurare il provisioning utenti automatico per fiocco di neve in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco applicazioni selezionare **Snowflake**.

    ![Collegamento di Snowflake nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione credenziali amministratore immettere `https://<Snowflake Account URL>/scim/v2` in URL tenant. Esempio di URL del tenant:`https://acme.snowflakecomputing.com/scim/v2`

6. Immettere il valore del **token di autenticazione scim** recuperato in precedenza in **token segreto**. Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a fiocco di neve. Se la connessione non riesce, verificare che l'account fiocco di neve disponga di autorizzazioni di amministratore e riprovare.

    ![URL tenant + token](common/provisioning-testconnection-tenanturltoken.png)

7. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

8. Fare clic su **Save**.

9. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a fiocco di neve**.

    ![Mapping utente a fiocco di neve](media/Snowflake-provisioning-tutorial/user-mapping.png)

10. Esaminare gli attributi utente sincronizzati da Azure AD a fiocco di neve nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in fiocco di neve per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente fiocco di neve](media/Snowflake-provisioning-tutorial/user-attribute.png)

11. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory gruppi a fiocco di neve**.

    ![Mapping dei gruppi fiocco di neve](media/Snowflake-provisioning-tutorial/group-mapping.png)

12. Esaminare gli attributi di gruppo sincronizzati da Azure AD a fiocco di neve nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in fiocco di neve per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi gruppo fiocco di neve](media/Snowflake-provisioning-tutorial/group-attribute.png)

13. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Per abilitare il servizio di provisioning Azure AD per fiocco di neve, impostare **stato** del provisioning **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

15. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in fiocco di neve scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito del provisioning](common/provisioning-scope.png)

16. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

    L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning di Azure ad in fiocco di neve.

    Per altre informazioni su come leggere i log di provisioning di Azure AD, vedere [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Limitazioni dei connettori

* I token SCIM generati a fiocco di neve scadono entro 6 mesi. Tenere presente che questi elementi devono essere aggiornati prima che scadano per consentire il provisioning delle sincronizzazioni per continuare a funzionare. 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md).
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi
* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md).