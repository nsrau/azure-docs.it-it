---
title: 'Esercitazione: Configurare Dialpad per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Dialpad.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: b88e618da3f8a23c0517aaeb251e54bf559fc468
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358474"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>Esercitazione: Configurare Dialpad per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Dialpad e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Dialpad.

> [!NOTE]
>  L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).

> Questo connettore è attualmente disponibile in anteprima. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Un tenant di Dialpad](https://www.dialpad.com/pricing/).
* Un account utente in Dialpad con autorizzazioni di amministratore.

## <a name="assign-users-to-dialpad"></a>Assegnare utenti a Dialpad
Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle assegnazioni. Nel contesto del provisioning utenti automatico vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a Dialpad. Dopo averlo stabilito, è possibile assegnare gli utenti e/o i gruppi a Dialpad seguendo le istruzioni fornite qui:
 
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>Suggerimenti importanti per l'assegnazione di utenti a Dialpad

 * È consigliabile assegnare un singolo utente di Azure AD a Dialpad per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Dialpad, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning.

## <a name="setup-dialpad-for-provisioning"></a>Configurare Dialpad per il provisioning

Prima di configurare Dialpad per il provisioning utenti automatico con Azure AD, sarà necessario recuperare alcune informazioni di provisioning da Dialpad.

1. Accedere alla [console di amministrazione di Dialpad](https://dialpadbeta.com/login) e selezionare **Admin settings** (Impostazioni di amministrazione). Assicurarsi che nel menu a discesa sia selezionato **My Company** (La mia società). Passare a **Authentication > API Keys** (Autenticazione > Chiavi API).

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad01.png" alt-text="Screenshot della console di amministrazione di Dialpad in cui sono evidenziate l'icona delle impostazioni e le opzioni My Company, Authentication e API keys e My Company è selezionata." border="false":::

2. Generare una nuova chiave facendo clic su **Add a key** (Aggiungi chiave) e configurando le proprietà del token segreto.

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad02.png" alt-text="Screenshot della pagina API keys nella console di amministrazione di Dialpad. L'opzione Add a key è evidenziata." border="false":::

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad03.png" alt-text="Screenshot della pagina Edit API keys nella console di amministrazione di Dialpad. Il pulsante Save è evidenziato." border="false":::

3. Fare clic sul pulsante **Click to show value** (Fare clic per mostrare il valore) accanto alla chiave API appena creata e copiare il valore visualizzato. Questo valore verrà immesso nel campo **Token segreto** nella scheda Provisioning dell'applicazione Dialpad nel portale di Azure. 

    ![Creazione del token in Dialpad](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>Aggiungere Dialpad dalla raccolta

Prima di configurare Dialpad per il provisioning utenti automatico con Azure AD, è necessario aggiungere Dialpad dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Dialpad dalla raccolta di applicazioni di Azure AD, eseguire queste operazioni:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Dialpad**, quindi selezionare **Dialpad** nel pannello dei risultati.
    ![Dialpad nell'elenco risultati](common/search-new-app.png)

5. Passare all'**URL** evidenziato di seguito in una finestra del browser separata. 

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad05.png" alt-text="Screenshot di una pagina che visualizza informazioni sull'app Dialpad. Sotto URL è evidenziato un indirizzo." border="false":::

6. Nell'angolo in alto a destra selezionare **Log In > Use Dialpad online** (Accedi > Usa Dialpad online).

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad06.png" alt-text="Screenshot del sito Web Dialpad. Il comando Log in è evidenziato e la relativa scheda è aperta. È evidenziata anche la voce Use Dialpad online." border="false":::

7. Poiché Dialpad è un'app OpenIDConnect, scegliere di accedere a Dialpad usando l'account aziendale Microsoft.

    :::image type="content" source="media/dialpad-provisioning-tutorial/loginpage.png" alt-text="Screenshot della pagina Start making calls nel sito Web Dialpad. Il pulsante Log in with Office 365 è evidenziato." border="false":::

8. Dopo l'autenticazione, accettare la richiesta di consenso nella pagina di consenso. L'applicazione verrà quindi aggiunta automaticamente al tenant e si verrà reindirizzati all'account Dialpad.

    :::image type="content" source="media/dialpad-provisioning-tutorial/redirect.png" alt-text="Screenshot di una pagina di autenticazione Microsoft che informa che l'app Dialpad ha richiesto l'accesso ad alcuni dati. Il pulsante Accept è evidenziato." border="false":::

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Configurare il provisioning utenti automatico in Dialpad

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Dialpad in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Dialpad in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Dialpad**.

    ![Collegamento di Dialpad nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatico evidenziata.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere `https://dialpad.com/scim` in **URL tenant**. Immettere il valore recuperato e salvato in precedenza da Dialpad in **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Dialpad. Se la connessione non riesce, verificare che l'account Dialpad abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Dialpad** (Sincronizza utenti di Azure Active Directory in Dialpad).

    ![Mapping utenti di Dialpad](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Dialpad nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per l'abbinamento con gli account utente in Dialpad per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di Dialpad](media/dialpad-provisioning-tutorial/dialpad07.png)

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning di Azure AD per Dialpad, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si vuole effettuare il provisioning in Dialpad selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti al report delle attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Dialpad.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere [Creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).
##  <a name="connector-limitations"></a>Limitazioni dei connettori
* Dialpad non supporta attualmente la ridenominazione dei gruppi. Questo significa che eventuali modifiche all'attributo **displayName** di un gruppo in Azure AD non verranno aggiornate e applicate in Dialpad.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
