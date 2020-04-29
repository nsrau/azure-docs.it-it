---
title: "Esercitazione: configurare l'inviato per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs"
description: Informazioni su come effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a inviato.
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
ms.date: 06/3/2019
ms.author: Zhchia
ms.openlocfilehash: 68e17ba1dd5981e565e56d6c8137f77d33ad755b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393512"
---
# <a name="tutorial-configure-envoy-for-automatic-user-provisioning"></a>Esercitazione: configurare l'inviato per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire sia in inviato che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Se configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi in un [inviato](https://envoy.com/pricing/) usando il servizio di provisioning Azure ad. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti nell'inviato
> * Rimuovere gli utenti nell'inviato quando non richiedono più l'accesso
> * Mantieni gli attributi utente sincronizzati tra Azure AD e inviato
> * Effettuare il provisioning di gruppi e appartenenze a gruppi nell'inviato
> * [Single Sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-tutorial) per l'invio (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un account utente in Azure AD con l' [autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per la configurazione del provisioning, ad esempio amministratore dell'applicazione, amministratore di applicazioni cloud, proprietario dell'applicazione o amministratore globale. 
* [Tenant di invio](https://envoy.com/pricing/).
* Un account utente in inviato con autorizzazioni di amministratore.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Informazioni sul [funzionamento del servizio di provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determinare chi sarà nell' [ambito per il provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determinare quali dati eseguire il [mapping tra Azure ad e l'inviato](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-envoy-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare l'inviato per supportare il provisioning con Azure AD

1. Accedere alla console di [amministrazione di invio](https://dashboard.envoy.com/login). Fare clic su **Integrations (integrazioni**).

    ![Integrazioni degli inviati](media/envoy-provisioning-tutorial/envoy01.png)

2. Fare clic su **Installa** per l' **integrazione di Microsoft Azure scim**.

    ![Installazione dell'inviato](media/envoy-provisioning-tutorial/envoy02.png)

3. Fare clic su **Salva** per **sincronizzare tutti gli utenti**. 

    ![Salva inviato](media/envoy-provisioning-tutorial/envoy03.png)

4. Copiare il **token di porta OAuth**. Questo valore verrà immesso nel campo **token segreto** nella scheda provisioning dell'applicazione inviato nel portale di Azure.
    
    ![OAUTH inviato](media/envoy-provisioning-tutorial/envoy04.png)

## <a name="step-3-add-envoy-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere un inviato dalla raccolta di applicazioni Azure AD

Aggiungere un inviato dalla raccolta di applicazioni Azure AD per iniziare a gestire il provisioning per l'invio. Se in precedenza è stato installato un inviato per SSO, è possibile utilizzare la stessa applicazione. È tuttavia consigliabile creare un'applicazione separata quando si testa inizialmente l'integrazione. Altre informazioni sull'aggiunta di un'applicazione dalla raccolta sono disponibili [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire chi sarà nell'ambito per il provisioning 

Il servizio Azure AD provisioning consente di definire l'ambito di chi verrà eseguito il provisioning in base all'assegnazione all'applicazione e o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile usare i [passaggi](../manage-apps/assign-user-or-group-access-portal.md) seguenti per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito di cui verrà eseguito il provisioning basato esclusivamente sugli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Quando si assegnano utenti e gruppi a un inviato, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti con il ruolo di accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli. 

* Avviare Small. Eseguire il test con un piccolo set di utenti e gruppi prima di distribuirlo a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-envoy"></a>Passaggio 5. Configurare il provisioning utenti automatico in inviato 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-envoy-in-azure-ad"></a>Per configurare il provisioning utenti automatico per l'invio in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Envoy**.

    ![Collegamento di Envoy nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **provisioning** .

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere `https://app.envoy.com/scim/v2` in **URL tenant**. Immettere il valore del **token di porta OAuth** recuperato in precedenza nel **token segreto**. Fare clic su **Test connessione** per verificare che Azure ad possibile connettersi all'inviato. Se la connessione non riesce, verificare che l'account inviato disponga delle autorizzazioni di amministratore e riprovare.

   ![provisioning](./media/envoy-tutorial/provisioning.png)

6. Nel campo **messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che deve ricevere le notifiche degli errori di provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica quando si verifica un errore** .

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a inviato**.

9. Esaminare gli attributi utente sincronizzati da Azure AD a inviato nella sezione **attribute-mapping** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in inviato per le operazioni di aggiornamento. Se si sceglie di modificare l' [attributo di destinazione corrispondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), sarà necessario assicurarsi che l'API di invio supporti il filtraggio degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|
   |---|---|
   |userName|Stringa|
   |externalId|Stringa|
   |displayName|Stringa|
   |title|Stringa|
   |emails[type eq "work"].value|Stringa|
   |preferredLanguage|Stringa|
   |department|Stringa|
   |indirizzi [digitare EQ "Work"]. Country|Stringa|
   |indirizzi [digitare EQ "Work"]. località|Stringa|
   |indirizzi [digitare EQ "Work"]. Region|Stringa|
   |addresses[type eq "work"].postalCode|Stringa|
   |indirizzi [tipo EQ "lavoro"]. formattato|Stringa|
   |addresses[type eq "work"].streetAddress|Stringa|
   |name.givenName|Stringa|
   |name.familyName|Stringa|
   |nome. formattato|Stringa|
   |phoneNumbers[type eq "mobile"].value|Stringa|
   |phoneNumbers[type eq "work"].value|Stringa|
   |locale|Stringa|

10. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory gruppi a inviato**.

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a inviato nella sezione **attribute-mapping** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in inviato per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      |Attributo|Type|
      |---|---|
      |displayName|Stringa|
      |externalId|Stringa|
      |Membri di|Riferimento|

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per l'inviato, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in inviato scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

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