---
title: 'Esercitazione: Configurare la directory federata per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente nella directory federata.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 910aaac84dacb75cd76772a0bc2960d9bfa8bb70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057924"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>Esercitazione: Configurare la directory federata per il provisioning automatico degli utentiTutorial: Configure Federated Directory for automatic user provisioning

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Federated Directory e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e/o gruppi in Directory federata.

> [!NOTE]
>  L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Una directory federata](https://www.federated.directory/pricing).
* Un account utente in Directory federata con autorizzazioni di amministratore.

## <a name="assign-users-to-federated-directory"></a>Assegnazione di utenti alla directory federata
Azure Active Directory usa un concetto denominato assegnazioni per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere alla directory federata. Una volta deciso, è possibile assegnare questi utenti e/o gruppi alla directory federata seguendo le istruzioni riportate di seguito:

 * [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>Suggerimenti importanti per l'assegnazione di utenti a Directory federata
 * È consigliabile assegnare un singolo utente di Azure AD a Directory federata per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Directory federata, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo Accesso predefinito sono esclusi dal provisioning.
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>Configurare la directory federata per il provisioning

Prima di configurare La directory federata per il provisioning automatico degli utenti con Azure AD, è necessario abilitare il provisioning SCIM nella directory federata.

1. Accedere alla [Console di amministrazione](https://federated.directory/of) directory federata

    ![Esercitazione sulla directory federata](media/federated-directory-provisioning-tutorial/companyname.png)

2. Passare a **Directory > directory utente** e selezionare il tenant. 

    ![directory federata](media/federated-directory-provisioning-tutorial/ad-user-directories.png)

3.  Per generare un token di connessione permanente, passare a **Chiavi di directory > Crea nuova chiave.** 

    ![directory federata](media/federated-directory-provisioning-tutorial/federated01.png)

4. Creare una chiave di directory. 

    ![directory federata](media/federated-directory-provisioning-tutorial/federated02.png)
    

5. Copiare il valore del **token di accesso**. Questo valore verrà immesso nel campo **Token segreto** nella scheda Provisioning dell'applicazione Directory federata nel portale di Azure.This value will be entered in the Secret Token field in the Provisioning tab of your Federated Directory application in the Azure portal. 

    ![directory federata](media/federated-directory-provisioning-tutorial/federated03.png)
    
## <a name="add-federated-directory-from-the-gallery"></a>Aggiungi directory federata dalla raccolta

Per configurare La directory federata per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere Directory federata dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere Directory federata dalla raccolta di applicazioni di Azure AD, eseguire la procedura seguente:To add Federated Directory from the Azure AD application gallery, perform the following steps:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Directory federata**, selezionare **Directory federata** nel riquadro dei risultati.

    ![Directory federata nell'elenco dei risultati](common/search-new-app.png)

5. Passare **all'URL** evidenziato di seguito in un browser separato. 

    ![directory federata](media/federated-directory-provisioning-tutorial/loginpage1.png)

6. Fare clic su **LOG IN**.

    ![directory federata](media/federated-directory-provisioning-tutorial/federated04.png)

7.  Poiché Directory federata è un'app OpenIDConnect, scegliere di accedere a Federated Directory utilizzando l'account aziendale Microsoft.
    
    ![directory federata](media/federated-directory-provisioning-tutorial/loginpage3.png)
 
8. Dopo una corretta autenticazione, accettare la richiesta di consenso per la pagina di consenso. L'applicazione verrà quindi aggiunta automaticamente al tenant e verrà reindirizzato all'account della directory federata.

    ![directory federata Aggiungi SCIM](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>Configurazione del provisioning automatico degli utenti nella directory federata 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi nella directory federata in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per la directory federata in Azure AD:To configure automatic user provisioning for Federated Directory in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Directory federata**.

    ![Il collegamento Directory federata nell'elenco Applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** `https://api.federated.directory/v2/` immettere l'INPUT in URL tenant. Immettere il valore recuperato e salvato in precedenza dalla directory federata nel **token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi alla directory federata. Se la connessione non riesce, verificare che l'account della directory federata disponga delle autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

9. Fare clic su **Salva**.

10. Nella sezione **Mapping** selezionare **Sincronizza utenti di Azure Active Directory con la directory federata.**

    ![Esercitazione sulla directory federata](media/federated-directory-provisioning-tutorial/user-mappings.png)
    
    
11. Esaminare gli attributi utente sincronizzati da Azure AD a Directory federata nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in Directory federata per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Esercitazione sulla directory federata](media/federated-directory-provisioning-tutorial/user-attributes.png)
    

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per La directory federata, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for Federated Directory, change the **Provisioning Status** to On in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning nella directory federata scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD nella directory federata.

Per altre informazioni su come leggere i log di provisioning di Azure AD, vedere Creazione di report sul provisioning automatico degli [account utenteFor](../app-provisioning/check-status-user-account-provisioning.md) more information on how to read the Azure AD provisioning logs, see Reporting on automatic user account provisioning
## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
