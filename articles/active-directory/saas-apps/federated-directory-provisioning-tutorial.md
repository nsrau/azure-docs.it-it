---
title: 'Esercitazione: Configurare Federated Directory per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Federated Directory.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 8ca7654d930247f70d85cbc20fbbeb961223f05f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998363"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>Esercitazione: Configurare Federated Directory per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Federated Directory e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Federated Directory.

> [!NOTE]
>  L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Un'istanza di Federated Directory](https://www.federated.directory/pricing).
* Un account utente in Federated Directory con autorizzazioni di amministratore.

## <a name="assign-users-to-federated-directory"></a>Assegnare gli utenti a Federated Directory
Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle assegnazioni. Nel contesto del provisioning utenti automatico vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a Federated Directory. Dopo aver definito questo aspetto, è possibile assegnare gli utenti e/o i gruppi a Federated Directory seguendo le istruzioni riportate di seguito:

 * [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>Suggerimenti importanti per l'assegnazione di utenti a Federated Directory
 * È consigliabile assegnare un singolo utente di Azure AD a Federated Directory per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Federated Directory, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning.
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>Configurare Federated Directory per il provisioning

Prima di configurare Federated Directory per il provisioning utenti automatico con Azure AD, è necessario abilitare il provisioning SCIM in Federated Directory.

1. Accedere alla [console di amministrazione di Federated Directory](https://federated.directory/of)

    :::image type="content" source="media/federated-directory-provisioning-tutorial/companyname.png" alt-text="Screenshot della console di amministrazione di Federated Directory che mostra un campo per l'immissione del nome della società. Sono anche visibili i pulsanti per l'accesso." border="false":::

2. Passare a **Directories > User directories** (Directory > Directory utente) e selezionare il tenant. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/ad-user-directories.png" alt-text="Screenshot della console di amministrazione di Federated Directory, con le opzioni Directories e Federated Directory Azure AD Test evidenziate." border="false":::

3.  Per generare un token di connessione permanente, passare a **Directory Keys > Create New Key** (Chiavi directory > Crea nuova chiave). 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated01.png" alt-text="Screenshot della pagina Directory Keys della console di amministrazione di Federated Directory. Il pulsante Create new key è evidenziato." border="false":::

4. Creare una chiave della directory. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated02.png" alt-text="Screenshot della pagina Create directory key della console di amministrazione di Federated Directory, con i campi Name e Description e il pulsante Create key." border="false":::
    

5. Copiare il valore del **token di accesso**. Questo valore verrà immesso nel campo **Token segreto** nella scheda Provisioning dell'applicazione Federated Directory nel portale di Azure. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated03.png" alt-text="Screenshot di una pagina della console di amministrazione di Federated Directory. Sono visibili un segnaposto del token di accesso e un nome di chiave, una descrizione e un'autorità emittente." border="false":::
    
## <a name="add-federated-directory-from-the-gallery"></a>Aggiungere Federated Directory dalla raccolta

Per configurare Federated Directory per il provisioning utenti automatico con Azure AD, è necessario aggiungere Federated Directory dalla raccolta di applicazioni di Azure AD all'elenco di applicazioni SaaS gestite.

**Per aggiungere Federated Directory dalla raccolta di applicazioni di Azure AD, eseguire queste operazioni:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Federated Directory**, quindi selezionare **Federated Directory** nel pannello dei risultati.

    ![Federated Directory nell'elenco dei risultati](common/search-new-app.png)

5. Passare all'**URL** evidenziato di seguito in una finestra del browser separata. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/loginpage1.png" alt-text="Screenshot di una pagina del portale di Azure che visualizza informazioni su Federated Directory. Il valore URL è evidenziato." border="false":::

6. Fare clic su **LOG IN** (Accedi).

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated04.png" alt-text="Screenshot del menu principale nel sito Federated Directory. Il pulsante Log in è evidenziato." border="false":::

7.  Poiché Federated Directory è un'app OpenIDConnect, scegliere di accedere con l'account aziendale Microsoft.
    
    :::image type="content" source="media/federated-directory-provisioning-tutorial/loginpage3.png" alt-text="Screenshot della pagina SCIM AD test nel sito Federated Directory. L'opzione per l'accesso con l'account Microsoft è evidenziata." border="false":::
 
8. Dopo l'autenticazione, accettare la richiesta di consenso nella pagina. L'applicazione verrà quindi aggiunta automaticamente al tenant e si verrà reindirizzati all'account Federated Directory.

    ![Aggiunta di SCIM in Federated Directory](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>Configurazione del provisioning utenti automatico in Federated Directory 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Federated Directory in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Federated Directory in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Federated Directory**.

    ![Collegamento a Federated Directory nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni di gestione con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatico evidenziata.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere `https://api.federated.directory/v2/` in URL tenant. Immettere il valore recuperato e salvato in precedenza da Federated Directory in **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Federated Directory. Se la connessione non riesce, verificare che l'account Federated Directory abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

9. Fare clic su **Salva**.

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Federated Directory** (Sincronizza utenti di Azure Active Directory con Federated Directory).

    :::image type="content" source="media/federated-directory-provisioning-tutorial/user-mappings.png" alt-text="Screenshot della sezione Mapping. In Name l'opzione Synchronize Azure Active Directory Users to Federated Directory è evidenziata." border="false":::
    
    
11. Esaminare gli attributi utente sincronizzati tra Azure AD e Federated Directory nella sezione **Mapping di attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Federated Directory per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    :::image type="content" source="media/federated-directory-provisioning-tutorial/user-attributes.png" alt-text="Screenshot della pagina Mapping di attributi. In una tabella sono elencati gli attributi di Azure Active Directory e di Federated Directory con lo stato di corrispondenza." border="false":::
    

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per Federated Directory, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui effettuare il provisioning in Federated Directory selezionando i valori appropriati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning di Azure AD in Federated Directory.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere [Creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md)
## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
