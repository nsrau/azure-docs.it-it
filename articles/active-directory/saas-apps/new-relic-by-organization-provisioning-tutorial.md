---
title: 'Esercitazione: Configurare New Relic by Organization per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a New Relic by Organization.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: Zhchia
ms.openlocfilehash: 6d196f7037fe9c0209e66e9d6e9ab25bdf807b59
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181903"
---
# <a name="tutorial-configure-new-relic-by-organization-for-automatic-user-provisioning"></a>Esercitazione: Configurare New Relic by Organization per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire sia in New Relic by Organization che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Una volta configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi per [New Relic by Organization](https://newrelic.com/) usando il servizio di provisioning di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in New Relic by Organization
> * Rimuovere utenti da New Relic by Organization quando non richiedono più l'accesso
> * Mantenere gli attributi utente sincronizzati tra Azure AD e New Relic by Organization
> * Effettuare il provisioning di gruppi e appartenenza a gruppi in New Relic by Organization
> * [Accesso Single Sign-On](./new-relic-limited-release-tutorial.md) a New Relic by Organization (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un account utente in Azure AD con l'[autorizzazione](../roles/permissions-reference.md) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale. 
* Uno o più account di New Relic by Organization a cui si vuole che gli utenti abbiano accesso. 

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Vedere le informazioni su [come funziona il servizio di provisioning](../app-provisioning/user-provisioning.md).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determinare quali dati [mappare tra Azure AD e New Relic by Organization](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-new-relic-by-organization-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare New Relic by Organization per supportare il provisioning con Azure AD

Collaborare con il rappresentante dell'account o ottenere supporto in support.newrelic.com per configurare SCIM e SSO per la propria organizzazione. Sarà necessario fornire al rappresentante dell'account quanto segue:

- Nome dell'organizzazione
- Elenco di ID account di New Relic da associare all'organizzazione

Con queste informazioni, il rappresentante dell'account crea un record dell'organizzazione nel nuovo sistema e associa gli account all'organizzazione.

Il rappresentante dell'account fornisce le informazioni seguenti necessarie per configurare l'applicazione SCIM/SSO di New Relic per il provider di identità:

- Endpoint SCIM (URL tenant)
- Token di connessione SCIM (token segreto)

Il token di connessione di SCIM consente il provisioning degli utenti in New Relic, quindi mantenere il valore protetto. Il rappresentante dell'account trasferirà il token di connessione di SCIM in modo sicuro.

## <a name="step-3-add-new-relic-by-organization-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere New Relic by Organization dalla raccolta di applicazioni di Azure AD

Aggiungere New Relic by Organization dalla raccolta di applicazioni di Azure AD per iniziare a gestire il provisioning in New Relic by Organization. Se New Relic by Organization è stato configurato in precedenza per l'accesso SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quando si assegnano utenti e gruppi a New Relic by Organization, è necessario selezionare un ruolo diverso da **Accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](../develop/howto-add-app-roles-in-azure-ad-apps.md) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-new-relic-by-organization"></a>Passaggio 5. Configurare il provisioning utenti automatico per New Relic by Organization 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-new-relic-by-organization-in-azure-ad"></a>Per configurare il provisioning utenti automatico per New Relic by Organization in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **New Relic by Organization**.

    ![Collegamento di New Relic nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatico evidenziata.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere `https://scim-provisioning.service.newrelic.com/scim/v2` in URL tenant. Immettere il valore del token di autenticazione SCIM recuperato in precedenza in **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a New Relic. Se la connessione non riesce, verificare che l'account New Relic abbia autorizzazioni di amministratore e riprovare.

    ![Screenshot che mostra la finestra di dialogo Credenziali amministratore, in cui è possibile immettere l'URL del tenant e il token segreto.](./media/new-relic-by-organization-provisioning-tutorial/provisioning.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to New Relic by Organization** (Sincronizza utenti di Azure Active Directory in New Relic by Organization).

9. Esaminare gli attributi utente sincronizzati da Azure AD a New Relic by Organization nella sezione **Mapping degli attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in New Relic by Organization per le operazioni di aggiornamento. Se si sceglie di modificare l'[attributo di destinazione corrispondente](../app-provisioning/customize-application-attributes.md), sarà necessario assicurarsi che l'API New Relic by Organization supporti il filtro degli utenti basato su tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|
   |---|---|
   |userName|string|
   |externalId|string|
   |active|Boolean|
   |emails[type eq "work"].value|string|
   |name.givenName|string|
   |name.formatted|string|
   |timezone|string|

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to New Relic by Organization** (Sincronizza gruppi di Azure Active Directory in New Relic by Organization).

11. Esaminare gli attributi gruppo sincronizzati da Azure AD a New Relic by Organization nella sezione **Mapping degli attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in New Relic by Organization per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      |Attributo|Type|
      |---|---|
      |displayName|string|
      |externalId|string|
      |Membri di|Informazioni di riferimento|

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per New Relic by Organization, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in New Relic by Organization selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo di sincronizzazione iniziale richiede più tempo dei cicli successivi, che verranno eseguiti ogni 40 minuti circa quando il servizio di provisioning di Azure AD è in esecuzione. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

* Usare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
* Controllare l'[indicatore di stato](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
* Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](../app-provisioning/application-provisioning-quarantine-status.md).  


## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)