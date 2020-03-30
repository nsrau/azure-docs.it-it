---
title: 'Esercitazione: Configurare la tastiera per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in Dialpad.Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to Dialpad.
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
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 9f39277644547a625d87a39681f0c5520996cbd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058366"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>Esercitazione: Configurare Dialpad per il provisioning automatico degli utentiTutorial: Configure Dialpad for automatic user provisioning

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Dialpad e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e/o gruppi in Dialpad.

> [!NOTE]
>  L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).

> Questo connettore è attualmente disponibile in anteprima. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Un tenant Dialpad](https://www.dialpad.com/pricing/).
* Un account utente in Dialpad con autorizzazioni di amministratore.

## <a name="assign-users-to-dialpad"></a>Assegnazione di utenti a Tastiera
Azure Active Directory usa un concetto denominato assegnazioni per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a Dialpad. Una volta deciso, puoi assegnare questi utenti e/o gruppi a Dialpad seguendo le istruzioni qui:
 
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>Suggerimenti importanti per l'assegnazione di utenti a Dialpad

 * È consigliabile assegnare un singolo utente di Azure AD a Dialpad per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Dialpad, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo Accesso predefinito sono esclusi dal provisioning.

## <a name="setup-dialpad-for-provisioning"></a>Configurazione della tastiera per il provisioning

Prima di configurare Dialpad per il provisioning automatico degli utenti con Azure AD, è necessario recuperare alcune informazioni di provisioning da Dialpad.

1. Accedi alla [Console di amministrazione di Dialpad](https://dialpadbeta.com/login) e seleziona **Impostazioni di amministrazione.** Assicurati che **la mia azienda** sia selezionata dall'elenco a discesa. Passare a **Chiavi API > di autenticazione**.

    ![Tastiera Aggiungi SCIM](media/dialpad-provisioning-tutorial/dialpad01.png)

2. Generare una nuova chiave facendo clic su **Aggiungi una chiave** e configurando le proprietà del token segreto.

    ![Tastiera Aggiungi SCIM](media/dialpad-provisioning-tutorial/dialpad02.png)

    ![Tastiera Aggiungi SCIM](media/dialpad-provisioning-tutorial/dialpad03.png)

3. Fare clic sul pulsante **Fare clic per visualizzare** il valore per la chiave API creata di recente e copiare il valore visualizzato. Questo valore verrà immesso nel campo **Token segreto** nella scheda Provisioning dell'applicazione Dialpad nel portale di Azure.This value will be entered in the Secret Token field in the Provisioning tab of your Dialpad application in the Azure portal. 

    ![Token di creazione tastiera](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>Aggiungere Dialpad dalla raccolta

Per configurare Dialpad per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere Dialpad dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere Dialpad dalla raccolta di applicazioni di Azure AD, eseguire la procedura seguente:To add Dialpad from the Azure AD application gallery, perform the following steps:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, inserisci **Dialpad**, seleziona **Tastiera** nel pannello dei risultati.
    ![Tastiera nell'elenco dei risultati](common/search-new-app.png)

5. Passare **all'URL** evidenziato di seguito in un browser separato. 

    ![Tastiera Aggiungi SCIM](media/dialpad-provisioning-tutorial/dialpad05.png)

6. Nell'angolo in alto a destra, seleziona **Accedi > Usa tastiera online**.

    ![Tastiera Aggiungi SCIM](media/dialpad-provisioning-tutorial/dialpad06.png)

7. Poiché Dialpad è un'app OpenIDConnect, scegli di accedere a Dialpad usando il tuo account aziendale Microsoft.

    ![Tastiera Aggiungi SCIM](media/dialpad-provisioning-tutorial/loginpage.png)

8. Dopo una corretta autenticazione, accettare la richiesta di consenso per la pagina di consenso. L'applicazione verrà quindi aggiunta automaticamente al tenant e verrà reindirizzato all'account Dialpad.

    ![Tastiera Aggiungi SCIM](media/dialpad-provisioning-tutorial/redirect.png)

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Configurare il provisioning automatico degli utenti su Dialpad

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Tastiera in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per Dialpad in Azure AD:To configure automatic user provisioning for Dialpad in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Dialpad**.

    ![Il collegamento Tastiera nell'elenco Applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali di** `https://dialpad.com/scim` amministratore immettere **l'input**in URL tenant. Immettere il valore recuperato e salvato in precedenza da Tastiera in **Secret Token**. Fare clic su Test connessione per verificare che Azure AD possa connettersi a Dialpad.Click **Test Connection** to ensure Azure AD can connect to Dialpad. Se la connessione non riesce, verificare che l'account Dialpad disponga delle autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare Sincronizza utenti di **Azure Active Directory con Tastiera**.

    ![Mapping utenti di dial-pad](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Dialpad nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in Dialpad per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente tastiera](media/dialpad-provisioning-tutorial/dialpad07.png)

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning di Azure AD per Dialpad, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for Dialpad, change the **Provisioning Status** to On in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in Dialpad scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Dialpad.You can use the Synchronization Details section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on Dialpad.

Per altre informazioni su come leggere i log di provisioning di Azure AD, vedere Creazione di report sul provisioning automatico degli [account utenteFor](../app-provisioning/check-status-user-account-provisioning.md) more information on how to read the Azure AD provisioning logs, see Reporting on automatic user account provisioning
##  <a name="connector-limitations"></a>Limitazioni dei connettori
* Dialpad non supporta le ridenominazioni di gruppo oggi. Ciò significa che le modifiche apportate al **displayName** di un gruppo in Azure AD non verranno aggiornate e riflesse in Dialpad.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
