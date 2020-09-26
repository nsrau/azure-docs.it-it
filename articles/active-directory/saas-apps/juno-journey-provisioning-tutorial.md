---
title: 'Esercitazione: Configurare Juno Journey per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Juno Journey.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 04/16/2020
ms.author: Zhchia
ms.openlocfilehash: aa875877d4017409b961eedda2af281622c45cf9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91312340"
---
# <a name="tutorial-configure-juno-journey-for-automatic-user-provisioning"></a>Esercitazione: Configurare Juno Journey per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire sia in Juno Journey che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Una volta configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi per [Juno Journey](https://www.junojourney.com/) tramite il servizio di provisioning di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in Juno Journey
> * Rimuovere utenti in Juno Journey quando l'accesso non è più necessario
> * Gestione della sincronizzazione degli attributi utente tra Azure AD e Juno Journey
> * [Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial) per Juno Journey (consigliato)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un account utente in Azure AD con l'[autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale. 
*  Un [tenant di Juno Journey](https://www.junojourney.com/getstarted).
*  Un account utente in Juno Journey con autorizzazioni di amministratore.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Informazioni su [come funziona il servizio di provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determinare i dati da [mappare tra Azure AD e Juno Journey](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-juno-journey-to-support-provisioning-with-azure-ad"></a>Passaggio 2. Configurare Juno Journey per il supporto del provisioning con Azure AD

1. Per **Token segreto** e **URL tenant** contattare il team di supporto di Juno Journey support team all'indirizzo support@the-juno.com. Questo valore verrà immesso nei campi **Token segreto** e **URL tenant** rispettivamente, nella scheda Provisioning dell'applicazione Juno Journey nel portale di Azure. 

## <a name="step-3-add-juno-journey-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere Juno Journey dalla raccolta di applicazioni Azure AD

Aggiungere Juno Journey dalla raccolta di applicazioni di Azure AD per iniziare a gestire il provisioning in Juno Journey. Se Juno Journey è stato configurato in precedenza per l'accesso SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Quando si assegnano utenti e gruppi a Juno Journey, è necessario selezionare un ruolo diverso da **Accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-juno-journey"></a>Passaggio 5. Configurare il provisioning utenti automatico per Juno Journey 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-juno-journey-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Juno Journey in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Juno Journey**.

    ![Collegamento a Juno Journey nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni Gestisci con l'opzione di provisioning denominata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa modalità di provisioning con l'opzione automatica chiamata.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere il valore di URL tenant recuperato in precedenza in **URL tenant**. Immettere il valore del token segreto recuperato in precedenza in **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Juno Journey. Se la connessione non riesce, verificare che l'account Juno Journey abbia autorizzazioni di amministratore e riprovare.

    ![provisioning](./media/juno-journey-provisioning-tutorial/provisioning.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Juno Journey** (Sincronizza utenti di Azure Active Directory in Juno Journey).

9. Esaminare gli attributi utente sincronizzati da Azure AD a Juno Journey nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Juno Journey per le operazioni di aggiornamento. Se si sceglie di modificare l'[attributo di destinazione corrispondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), sarà necessario assicurarsi che l'API Juno Journey supporti il filtro degli utenti basato su tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Variabile|Type|
   |---|---|
   |userName|string|
   |externalId|string|
   |displayName|string|
   |title|string|
   |active|Boolean|
   |preferredLanguage|string|
   |emails[type eq "work"].value|string|
   |addresses[type eq "work"].country|string|
   |addresses[type eq "work"].region|string|
   |addresses[type eq "work"].locality|string|
   |addresses[type eq "work"].postalCode|string|
   |addresses[type eq "work"].formatted|string|
   |addresses[type eq "work"].streetAddress|string|
   |name.givenName|string|
   |name.familyName|string|
   |name.middleName|string|
   |name.formatted|string|
   |phoneNumbers[type eq "fax"].value|string|
   |phoneNumbers[type eq "mobile"].value|string|
   |phoneNumbers[type eq "work"].value|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|string|
   urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|string|
   urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|string|
   urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|string|


10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning di Azure AD per Juno Journey, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Juno Journey selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo di sincronizzazione iniziale richiede più tempo dei cicli successivi, che verranno eseguiti ogni 40 minuti circa quando il servizio di provisioning di Azure AD è in esecuzione. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

* Usare i [log di provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo
* Controllare l'[indicatore di stato](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento
* Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
