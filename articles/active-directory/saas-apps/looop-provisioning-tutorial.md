---
title: 'Esercitazione: Configurare Looop per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Looop.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: 528003ac482da6f254bf437321c70c389d23844b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94835035"
---
# <a name="tutorial-configure-looop-for-automatic-user-provisioning"></a>Esercitazione: Configurare Looop per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Looop e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Looop.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant di Looop](https://www.looop.co/pricing/)
* Un account utente in Looop con autorizzazioni di amministratore.

## <a name="assign-users-to-looop"></a>Assegnare utenti a Looop

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle assegnazioni. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a Looop. Dopo averlo stabilito, è possibile assegnare gli utenti e/o i gruppi a Looop seguendo le istruzioni fornite qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-looop"></a>Suggerimenti importanti per l'assegnazione di utenti a Looop

* È consigliabile assegnare un singolo utente di Azure AD a Looop per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Looop, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="set-up-looop-for-provisioning"></a>Configurare Looop per il provisioning

Prima di configurare Looop per il provisioning utenti automatico con Azure AD, sarà necessario recuperare alcune informazioni di provisioning da Looop.

1. Accedere alla [console di amministrazione di Looop](https://app.looop.co/#/login) e selezionare **Account**. In **Account Settings** (Impostazioni account) selezionare **Authentication** (Autenticazione).

    :::image type="content" source="media/looop-provisioning-tutorial/admin.png" alt-text="Screenshot della console di amministrazione di Looop. La scheda Account è evidenziata e aperta. In Account settings l'opzione Authentication è evidenziata." border="false":::

2. Per generare un nuovo token, fare clic su **Reset Token** (Reimposta token) in **SCIM Integration** (Integrazione SCIM).

    :::image type="content" source="media/looop-provisioning-tutorial/resettoken.png" alt-text="Screenshot della sezione SCIM Integration di una pagina della console di amministrazione di Looop. Il pulsante Reset token è evidenziato." border="false":::

3. Copiare il valore di **SCIM Endpoint** (Endpoint SCIM) e **Token**. Questi valori verranno immessi nei campi **URL tenant** e **Token segreto** nella scheda Provisioning dell'applicazione Looop nel portale di Azure. 

    ![Creazione del token in Looop](media/looop-provisioning-tutorial/token.png)

## <a name="add-looop-from-the-gallery"></a>Aggiungere Looop dalla raccolta

Per configurare Looop per il provisioning utenti automatico con Azure AD, è necessario aggiungere Looop dalla raccolta di applicazioni di Azure AD all'elenco di applicazioni SaaS gestite.

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Looop** e quindi selezionare **Looop** nel pannello dei risultati. 

    ![Looop nell'elenco risultati](common/search-new-app.png)

5. Selezionare il pulsante **Sign-up for Looop** (Iscrizione a Looop) per essere reindirizzati alla pagina di accesso di Looop. 

    ![Aggiunta di OIDC in Looop](media/looop-provisioning-tutorial/signup.png)

6. Poiché Looop è un'app OpenIDConnect, scegliere di accedere a Looop usando l'account aziendale Microsoft.

    ![Accesso OIDC a Looop](media/looop-provisioning-tutorial/msftlogin.png)

7. Una volta eseguita l'autenticazione, l'utente accetta la richiesta di consenso nella pagina di consenso. L'applicazione verrà quindi aggiunta automaticamente al tenant e si verrà reindirizzati all'account Looop.

    ![Consenso OIDC per Looop](media/looop-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-looop"></a>Configurare il provisioning utenti automatico per Looop 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Looop in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-looop-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Looop in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Looop**.

    ![Collegamento di Looop nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatica evidenziata.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere `https://<organisation_domain>.looop.co/scim/v2` in **URL tenant**. Ad esempio, `https://demo.looop.co/scim/v2` Immettere il valore recuperato e salvato in precedenza da Looop in **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Looop. Se la connessione non riesce, verificare che l'account Looop abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Looop** (Sincronizza utenti di Azure Active Directory con Looop).

    ![Mapping utente di Looop](media/looop-provisioning-tutorial/usermappings.png)

9. Esaminare gli attributi utente sincronizzati tra Azure AD e Looop nella sezione **Mapping di attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Looop per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|Supportato per il filtro|
   |---|---|---|
   |userName|string|&check;|
   |active|Boolean|
   |emails[type eq "work"].value|string|
   |name.givenName|string|
   |name.familyName|string|
   |externalId|string|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:area|string|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:custom_1|string|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:custom_2|string|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:custom_3|string|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:department|string|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:employee_id|string|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:location|string|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:position|string|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:startAt|string|

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to Meta Networks Connector** (Sincronizza gruppi di Azure Active Directory in Meta Networks Connector).

    ![Mapping dei gruppi di Looop](media/looop-provisioning-tutorial/groupmappings.png)

11. Nella sezione **Mapping attributi** esaminare gli attributi di gruppo che vengono sincronizzati da Azure AD a Meta Networks Connector. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per l'abbinamento con i gruppi in Meta Networks Connector per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    |Attributo|Type|Supportato per il filtro|
    |---|---|---|
    |displayName|string|&check;|
    |Membri di|Informazioni di riferimento|
    |externalId|string|


10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning di Azure AD per Looop, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Looop selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning di Azure AD in Looop.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)


