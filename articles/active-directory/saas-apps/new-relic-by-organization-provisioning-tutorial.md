---
title: 'Esercitazione: configurare New Relic by Organization per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a New Relic dall'organizzazione.
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
ms.openlocfilehash: 3197ac3033c6550b72bdfdf39fd23c55dda20d90
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82134234"
---
# <a name="tutorial-configure-new-relic-by-organization-for-automatic-user-provisioning"></a>Esercitazione: configurare New Relic by Organization per il provisioning utenti automatico

Questa esercitazione descrive i passaggi che è necessario eseguire in entrambe le nuove reliquie per organizzazione e Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Quando la configurazione viene eseguita, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi a [New Relic by Organization](https://newrelic.com/) usando il servizio di provisioning Azure ad. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Crea utenti in New Relic per organizzazione
> * Rimuovere gli utenti in New Relic dall'organizzazione quando non richiedono più l'accesso
> * Mantieni gli attributi utente sincronizzati tra Azure AD e New Relic per organizzazione
> * Effettuare il provisioning di gruppi e appartenenze a gruppi in New Relic per organizzazione
> * [Single Sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial) a New Relic by Organization (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un account utente in Azure AD con l' [autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per la configurazione del provisioning, ad esempio amministratore dell'applicazione, amministratore di applicazioni cloud, proprietario dell'applicazione o amministratore globale. 
* Uno o più account di New Relic per organizzazione a cui si vuole che gli utenti abbiano accesso. 

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Informazioni sul [funzionamento del servizio di provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determinare chi sarà nell' [ambito per il provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determinare quali dati eseguire il [mapping tra Azure ad e New Relic dall'organizzazione](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-new-relic-by-organization-to-support-provisioning-with-azure-ad"></a>Passaggio 2. Configurare New Relic by Organization per supportare il provisioning con Azure AD

Collaborare con il rappresentante dell'account o ottenere supporto in support.newrelic.com per configurare SCIM e SSO per la propria organizzazione. Sarà necessario fornire al rappresentante dell'account:

- Nome dell'organizzazione
- Elenco di ID di account di New Relic da associare all'organizzazione

Con queste informazioni, il rappresentante dell'account crea automaticamente un record dell'organizzazione nel nuovo sistema e associa gli account all'organizzazione.

Il rappresentante dell'account fornisce le informazioni seguenti che è necessario configurare l'applicazione SCIM/SSO di New Relic per il provider di identità:

- Endpoint SCIM (URL tenant)
- Bearer token SCIM (token Secret)

Il bearer token SCIM consente il provisioning degli utenti in una nuova reliquia, quindi Mantieni il valore protetto. Il rappresentante dell'account trasferirà il bearer token di SCIM in modo sicuro.

## <a name="step-3-add-new-relic-by-organization-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere New Relic dall'organizzazione dalla raccolta di applicazioni Azure AD

Aggiungere New Relic by Organization dalla raccolta di applicazioni Azure AD per iniziare a gestire il provisioning in New Relic dall'organizzazione. Se in precedenza è stato configurato New Relic per organizzazione per SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'applicazione separata quando si testa inizialmente l'integrazione. Altre informazioni sull'aggiunta di un'applicazione dalla raccolta sono disponibili [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire chi sarà nell'ambito per il provisioning 

Il servizio Azure AD provisioning consente di definire l'ambito di chi verrà eseguito il provisioning in base all'assegnazione all'applicazione e o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile usare i [passaggi](../manage-apps/assign-user-or-group-access-portal.md) seguenti per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito di cui verrà eseguito il provisioning basato esclusivamente sugli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Quando si assegnano utenti e gruppi a New Relic dall'organizzazione, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti con il ruolo di accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli. 

* Avviare Small. Eseguire il test con un piccolo set di utenti e gruppi prima di distribuirlo a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-new-relic-by-organization"></a>Passaggio 5. Configurare il provisioning utenti automatico in New Relic per organizzazione 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-new-relic-by-organization-in-azure-ad"></a>Per configurare il provisioning utenti automatico per New Relic dall'organizzazione in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **New Relic by Organization**.

    ![Collegamento di New Relic nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **provisioning** .

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere `https://scim-provisioning.service.newrelic.com/scim/v2` in URL tenant. Immettere il valore del token di autenticazione SCIM recuperato in precedenza in **token segreto**. Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a New Relic. Se la connessione non riesce, verificare che l'account New Relic disponga di autorizzazioni di amministratore e riprovare.

    ![provisioning](./media/new-relic-by-organization-provisioning-tutorial/provisioning.png)

6. Nel campo **messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che deve ricevere le notifiche degli errori di provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica quando si verifica un errore** .

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a New Relic by Organization**.

9. Esaminare gli attributi utente sincronizzati da Azure AD a New Relic by Organization nella sezione **attribute-mapping** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in New Relic per organizzazione per le operazioni di aggiornamento. Se si sceglie di modificare l' [attributo di destinazione corrispondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), è necessario assicurarsi che l'API New Relic by Organization supporti il filtraggio degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|
   |---|---|
   |userName|string|
   |externalId|string|
   |active|Boolean|
   |emails[type eq "work"].value|string|
   |name.givenName|string|
   |nome. formattato|string|
   |timezone|string|

10. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to New Relic by Organization**.

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a New Relic by Organization nella sezione **attribute-mapping** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in New Relic per organizzazione per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      |Attributo|Type|
      |---|---|
      |displayName|string|
      |externalId|string|
      |Membri di|Informazioni di riferimento|

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per New Relic dall'organizzazione, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in New Relic by Organization scegliendo i valori desiderati in **ambito** nella sezione **Settings** .

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

Questa operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti nell' **ambito** nella sezione **Impostazioni** . Il ciclo iniziale richiede più tempo rispetto ai cicli successivi, che si verificano approssimativamente ogni 40 minuti, a condizione che sia in esecuzione il servizio di provisioning Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

* Usare i [log di provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare gli utenti di cui è stato effettuato il provisioning con esito positivo o negativo
* Controllare l' [indicatore di stato per visualizzare](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) lo stato del ciclo di provisioning e il modo in cui terminarlo
* Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entra in quarantena. Altre informazioni sugli stati di quarantena sono disponibili [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  


## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
