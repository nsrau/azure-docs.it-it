---
title: 'Esercitazione: Configura Directory federata per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e deprovisioning degli account utente in Directory federata.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 19f5690a6852161abce2565a8c4a52ce86ff5187
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840589"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>Esercitazione: Configura Directory federata per il provisioning utenti automatico

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire nella Directory federata e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare il provisioning e deprovisioning di utenti e/o gruppi alla Directory federata automaticamente.

> [!NOTE]
>  L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni di Microsoft Azure generale di utilizzo per le funzionalità di anteprima, vedere [condizioni per l'utilizzo aggiuntive per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Oggetto Directory federata](https://www.federated.directory/pricing).
* Un account utente nella Directory federata con autorizzazioni di amministratore.

## <a name="assign-users-to-federated-directory"></a>Assegnare gli utenti alla Directory federata
Azure Active Directory Usa un concetto detto assegnazioni per determinare gli utenti che dovranno ricevere l'accesso alle App selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo di utenti e/o gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono accedere alla Directory federata. Dopo aver stabilito questo, è possibile assegnare tali utenti e/o gruppi alla Directory federata seguendo le istruzioni riportate qui:

 * [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>Suggerimenti importanti per l'assegnazione di utenti alla Directory federata
 * È consigliabile che un singolo utente di Azure AD viene assegnato alla Directory federata per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente alla Directory federata, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo di accesso predefinito vengono esclusi dal provisioning.
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>Impostare Directory federata per il provisioning

Prima di configurare Directory federata per il provisioning utenti automatico con Azure AD, è necessario abilitare il provisioning SCIM sulla Directory federata.

1. Accedi a di [federato Console di amministrazione di Directory](https://federated.directory/of)

    ![Esercitazione di Directory federata](media/federated-directory-provisioning-tutorial/companyname.png)

2. Passare a **directory > directory degli utenti** e selezionare il tenant. 

    ![directory federata](media/federated-directory-provisioning-tutorial/ad-user-directories.png)

3.  Per generare un token di connessione permanente, passare a **chiavi Directory > Crea nuova chiave.** 

    ![directory federata](media/federated-directory-provisioning-tutorial/federated01.png)

4. Creare una chiave di directory. 

    ![directory federata](media/federated-directory-provisioning-tutorial/federated02.png)
    

5. Copiare il valore del **token di accesso**. Questo valore verrà immesso nel **Token segreto** campo nella scheda Provisioning dell'applicazione federata Directory nel portale di Azure. 

    ![directory federata](media/federated-directory-provisioning-tutorial/federated03.png)
    
## <a name="add-federated-directory-from-the-gallery"></a>Aggiungere Directory federata dalla raccolta

Per configurare la Directory federata per provisioning utenti automatico con Azure AD, è necessario aggiungere Directory federata dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Directory federata dalla raccolta di applicazioni di Azure AD, seguire i passaggi seguenti:**

1. Nel  **[portale di Azure](https://portal.azure.com)** , nel riquadro di spostamento sinistro, selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare la **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, immettere **Directory federata**, selezionare **Directory federata** nel pannello dei risultati.

    ![Directory federata nell'elenco risultati](common/search-new-app.png)

5. Passare il **URL** evidenziato di seguito in un browser diverso. 

    ![directory federata](media/federated-directory-provisioning-tutorial/loginpage1.png)

6. Fare clic su **ACCEDI**.

    ![directory federata](media/federated-directory-provisioning-tutorial/federated04.png)

7.  Come Directory federata è un'app di openid Connect, scegliere di eseguire l'accesso alla Directory federata con l'account aziendale di Microsoft.
    
    ![directory federata](media/federated-directory-provisioning-tutorial/loginpage3.png)
 
8. Dopo il completamento dell'autenticazione, accettare la richiesta di consenso per la pagina di consenso. Verrà aggiunti automaticamente all'applicazione nel tenant e si verrà reindirizzati all'account di Directory federata.

    ![directory federata Aggiungi SCIM](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>Configurazione del provisioning utenti automatico in Directory federata 

Questa sezione descrive i passaggi per configurare il provisioning di Azure AD del servizio per creare, aggiornare e disabilitare utenti e/o i gruppi nella Directory federato basato su utente e/o le assegnazioni dei gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>Per configurare il provisioning utenti automatico per la Directory federata di Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**, quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni, selezionare **Directory federata**.

    ![Collegamento di Directory federata nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda provisioning](common/provisioning-automatic.png)

5. Sotto il **credenziali di amministratore** sezione `https://api.federated.directory/v2/` URL del Tenant. Il valore che è stato recuperato e salvato in precedenza dalla Directory federati in di input **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi alla Directory federata. Se la connessione non riesce, verificare che l'account di Directory federata abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e Token](common/provisioning-testconnection-tenanturltoken.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Notifica tramite posta elettronica](common/provisioning-notification-email.png)

9. Fare clic su **Save**.

10. Sotto il **mapping** sezione, selezionare **sincronizzare Azure Active Directory agli utenti di Directory federata**.

    ![Esercitazione di Directory federata](media/federated-directory-provisioning-tutorial/user-mappings.png)
    
    
11. Esaminare gli attributi utente sincronizzati da Azure AD alla Directory federati nel **Mapping degli attributi** sezione. Gli attributi selezionati come **corrispondenti** le proprietà utilizzate per soddisfare gli account utente nella Directory federata per operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Esercitazione di Directory federata](media/federated-directory-provisioning-tutorial/user-attributes.png)
    

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il provisioning del servizio per la Directory federata di Azure AD, impostare il **stato del Provisioning** al **sul** nel **impostazioni** sezione.

    ![Lo stato di provisioning sia attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o gruppi di cui si vuole eseguire il provisioning in Directory federata selezionando i valori desiderati in **ambito** nel **impostazioni** sezione.

    ![Il provisioning di ambito](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio di configurazione del Provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la **Dettagli sincronizzazione** sezione per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività, che descrivono tutte le azioni eseguite dal provisioning del servizio nella Directory federata di Azure AD provisioning.

Per altre informazioni su come leggere il log di provisioning di Azure AD, vedere [creazione di report sul provisioning degli account utente automatico](../manage-apps/check-status-user-account-provisioning.md)
## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)