---
title: 'Esercitazione: Configurare una nuova reliquia (versione limitata) per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a New Relic (versione limitata).
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 7fd0d976-4f70-4ce5-992e-3ea4ed8e5d60
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2020
ms.author: Zhchia
ms.openlocfilehash: 363b50a4835ead52b3a3a85978d4ea8210add7b2
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727346"
---
# <a name="tutorial-configure-new-reliclimited-release-for-automatic-user-provisioning"></a>Esercitazione: Configurare una nuova reliquia (rilascio limitato) per il provisioning automatico degli utentiTutorial: Configure New Relic(Limited Release) for automatic user provisioning

Questa esercitazione descrive i passaggi da eseguire sia in Nuova reliquia (versione limitata) che in Azure Active Directory (Azure AD) per configurare il provisioning automatico degli utenti. Quando si configura la configurazione, Azure AD esegue automaticamente il provisioning di utenti e gruppi in [Nuova reliquia](https://newrelic.com/) usando il servizio di provisioning di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in Nuova reliquia (versione limitata)
> * Rimuovere gli utenti in Nuova reliquia (rilascio limitato) quando non richiedono più l'accesso
> * Mantenere sincronizzati gli attributi utente tra Azure AD e Nuova relazione (versione limitata)Keep user attributes synchronized between Azure AD and New Relic(Limited Release)
> * Effettuare il provisioning di gruppi e appartenenze ai gruppi in Nuova reliquia (versione limitata)
> * [Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial) per New Relic(Versione limitata) (consigliato)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure ADAn Azure AD tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un account utente in Azure AD con [autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per configurare il provisioning (ad esempio Amministratore applicazioni, Amministratore applicazione cloud, Proprietario applicazione o Amministratore globale). 
* Uno o più account in New Relic(Versione limitata) a cui si desidera che gli utenti abbiano accesso. 

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Informazioni sul funzionamento del servizio di [provisioning.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinare chi riverrà [nell'ambito del provisioning.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Determinare i dati [di cui eseguire il mapping tra Azure AD e New Relic (versione limitata)](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-new-reliclimited-release-to-support-provisioning-with-azure-ad"></a>Passaggio 2. Configurare una nuova reliquia (versione limitata) per supportare il provisioning con Azure ADConfigure New Relic(Limited Release) to support provisioning with Azure AD

Collaborare con il rappresentante dell'account o ottenere supporto presso support.newrelic.com configurare SCIM e SSO per l'organizzazione. Dovrai fornire al rappresentante del tuo account:

- Nome dell'organizzazione
- Elenco di ID nuovo account Relic da associare all'organizzazione

Con queste informazioni, il rappresentante dell'account crea un record dell'organizzazione per te nel nostro nuovo sistema e associa i tuoi account all'organizzazione.

Il rappresentante dell'account fornisce le seguenti informazioni necessarie per configurare l'applicazione New Relic SCIM/SSO per il provider di identità:

- Endpoint SCIM (URL tenant)
- Token di connessione SCIM (token segreto)

Il token di connessione SCIM consente il provisioning degli utenti in New Relic, pertanto è necessario mantenere il valore protetto. Il rappresentante del tuo account ti trasferirà il token di connessione SCIM in modo sicuro.

## <a name="step-3-add-new-reliclimited-release-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere una nuova reliquia (versione limitata) dalla raccolta di applicazioni di Azure ADAdd New Relic(Limited Release) from the Azure AD application gallery

Aggiungere Nuova reliquia (versione limitata) dalla raccolta di applicazioni di Azure AD per iniziare a gestire il provisioning in New Relic(Limited Release). Se in precedenza è stata configurata New Relic(Limited Release) per SSO, è possibile utilizzare la stessa applicazione. Tuttavia, è consigliabile creare un'app separata durante il test iniziale dell'integrazione. Ulteriori informazioni sull'aggiunta di un'applicazione dalla raccolta [sono disponibili qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire chi riverrà nell'ambito del provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito degli utenti in base all'assegnazione all'applicazione e o in base agli attributi dell'utente/gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning nell'app in base all'assegnazione, è possibile usare la [procedura](../manage-apps/assign-user-or-group-access-portal.md) seguente per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning in base esclusivamente agli attributi dell'utente o del gruppo, è possibile utilizzare un filtro di ambito come descritto [di seguito.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 

* Quando si assegnano utenti e gruppi a Nuova reliquia (versione limitata), è necessario selezionare un ruolo diverso da **Accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non effettivamente autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli. 

* Inizia in piccolo. Esegui test con un piccolo set di utenti e gruppi prima di distribuirlo a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 


## <a name="step-5-configure-automatic-user-provisioning-to-new-reliclimited-release"></a>Passaggio 5. Configurare il provisioning automatico degli utenti in Nuova reliquia (versione limitata) 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-new-reliclimited-release-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per New Relic (versione limitata) in Azure AD:To configure automatic user provisioning for New Relic(Limited Release) in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Nuova reliquia (versione limitata)**.

    ![Collegamento di New Relic nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** `https://scim-provisioning.service.newrelic.com/scim/v2` immettere l'INPUT in URL tenant. Immettere il valore del token di autenticazione SCIM recuperato in precedenza in **Secret Token**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Nuova reliquia. Se la connessione non riesce, verificare che l'account New Relic disponga delle autorizzazioni di amministratore e riprovare.

    ![provisioning](./media/new-relic-limited-release-provisioning-tutorial/provisioning.png)

6. Nel campo **E-mail** di notifica immettere l'indirizzo di posta elettronica di una persona o di un gruppo che deve ricevere le notifiche di errore di provisioning e selezionare la casella di controllo Invia una **notifica tramite posta elettronica quando si verifica un errore.**

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **Mapping** selezionare **Sincronizza utenti di Azure Active Directory con nuova reliquia (versione limitata)**.

9. Esaminare gli attributi utente sincronizzati da Azure AD a Nuova reliquia (versione limitata) nella sezione **Mapping degli** attributi. Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in Nuova reliquia (versione limitata) per le operazioni di aggiornamento. Se si sceglie di modificare l'attributo di [destinazione corrispondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)è necessario assicurarsi che l'API New Relic(Limited Release) supporti il filtraggio degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|
   |---|---|
   |userName|string|
   |externalId|string|
   |active|Boolean|
   |emails[type eq "work"].value|string|
   |name.givenName|string|
   |nome.formattato|string|
   |timezone|string|

10. Nella sezione **Mapping** selezionare Sincronizza gruppi di **Azure Active Directory con nuova reliquia (versione limitata)**.

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Nuova reliquia (versione limitata) nella sezione **Mapping degli** attributi. Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare i gruppi in Nuova reliquia (versione limitata) per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      |Attributo|Type|
      |---|---|
      |displayName|string|
      |externalId|string|
      |Membri di|Riferimento|

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per Nuova reliquia (versione limitata), modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for New Relic(Limited Release), change the **Provisioning Status** to On in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in Nuova reliquia (versione limitata) scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

Questa operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni.** L'esecuzione del ciclo iniziale richiede più tempo rispetto ai cicli successivi, che si verificano approssimativamente ogni 40 minuti, purché sia in esecuzione il servizio di provisioning di Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:Once you've configured provisioning, use the following resources to monitor your deployment:

* Utilizzare i registri di [provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare quali utenti sono stati sottoposti a provisioning con successo o meno
* Controllare la barra di [avanzamento](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) per visualizzare lo stato del ciclo di provisioning e la sua vicinanza al completamento
* Se la configurazione di provisioning sembra essere in uno stato non integro, l'applicazione andrà in quarantena. Ulteriori informazioni sugli stati di quarantena [sono disponibili qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  


## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
