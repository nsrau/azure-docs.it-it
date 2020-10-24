---
title: "Esercitazione: configurare la sincronizzazione dell'identità di inoltro globale per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs"
description: Informazioni su come effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Global Relay Identity Sync.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 0c4a3bf0-d0a6-4eab-909b-6cf9f9234e4c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2020
ms.author: Zhchia
ms.openlocfilehash: c1e2d64bb30b6451e232c85a5892771157d32928
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92514402"
---
# <a name="tutorial-configure-global-relay-identity-sync-for-automatic-user-provisioning"></a>Esercitazione: configurare la sincronizzazione dell'identità di inoltro globale per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire sia nella sincronizzazione delle identità di inoltro globale sia nella Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Quando è configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi in [sincronizzazione identità inoltro globale](https://portalalpha1.globalrelay.com/) usando il servizio di provisioning Azure ad. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti nella sincronizzazione dell'identità dell'inoltro globale
> * Rimuovere gli utenti nella sincronizzazione dell'identità dell'inoltro globale quando non richiedono più l'accesso
> * Mantieni gli attributi utente sincronizzati tra Azure AD e sincronizzazione identità inoltro globale
> * Effettuare il provisioning di gruppi e appartenenze a gruppi in Global Relay Identity Sync

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un account utente in Azure AD con l'[autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Vedere le informazioni su [come funziona il servizio di provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determinare quali dati eseguire il [mapping tra Azure ad e la sincronizzazione dell'identità dell'inoltro globale](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-global-relay-identity-sync-to-support-provisioning-with-azure-ad"></a>Passaggio 2. Configurare la sincronizzazione dell'identità dell'inoltro globale per supportare il provisioning con Azure AD

Per ricevere l'URL del tenant, contattare il rappresentante del Global Relay Identity Sync. Questo valore verrà immesso nel campo **URL tenant** nella scheda provisioning dell'applicazione Global Relay Identity Sync nel portale di Azure.

## <a name="step-3-add-global-relay-identity-sync-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere sincronizzazione identità inoltro globale dalla raccolta di applicazioni di Azure AD

Aggiungere la sincronizzazione dell'identità del relè globale dalla raccolta di applicazioni Azure AD per iniziare a gestire il provisioning nella sincronizzazione delle identità del relay globale. Altre informazioni sull'aggiunta di un'applicazione dalla raccolta sono disponibili [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-global-relay-identity-sync"></a>Passaggio 5. Configurare il provisioning utenti automatico in sincronizzazione identità inoltro globale 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi nell'App Global Relay Identity Sync in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-global-relay-identity-sync-in-azure-ad"></a>Per configurare il provisioning utenti automatico per la sincronizzazione dell'identità dell'inoltro globale in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Global Relay Identity Sync**.

    ![Collegamento Global Relay Identity Sync nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning automatica](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere l' **URL del tenant**di Global Relay Identity Sync. Fare clic su **Test connessione** per assicurarsi che Azure ad possibile connettersi a Global Relay Identity Sync. Se la connessione non riesce, verificare che l'account di sincronizzazione delle identità di inoltro globale disponga delle autorizzazioni di amministratore e contattare il rappresentante del relè globale per risolvere il problema.

    ![Pulsante autorizzazione](media/global-relay-identity-sync-provisioning-tutorial/authorization.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory Users to Global Relay Identity Sync**.

9. Esaminare gli attributi utente sincronizzati da Azure AD a Global Relay Identity Sync nella sezione **attribute-mapping** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente nella sincronizzazione dell'identità dell'inoltro globale per le operazioni di aggiornamento. Se si sceglie di modificare l' [attributo di destinazione corrispondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), è necessario assicurarsi che l'API di sincronizzazione delle identità di inoltro globale supporti il filtraggio degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|
   |---|---|
   |userName|string|
   |active|Boolean|
   |displayName|string|
   |title|string|
   |emails[type eq "work"].value|string|
   |preferredLanguage|string|
   |name.givenName|string|
   |name.familyName|string|
   |name.formatted|string|
   |addresses[type eq "work"].formatted|string|
   |addresses[type eq "work"].streetAddress|string|
   |emails[type eq "work"].value|string|
   |addresses[type eq "work"].locality|string|
   |addresses[type eq "work"].region|string|
   |addresses[type eq "work"].postalCode|string|
   |addresses[type eq "work"].country|string|
   |indirizzi [tipo EQ "other"]. formattato|string|
   |phoneNumbers[type eq "work"].value|string|
   |phoneNumbers[type eq "mobile"].value|string|
   |phoneNumbers[type eq "fax"].value|string|
   |externalId|string|
   |name.honorificPrefix|string|
   |nome. honorificSuffix|string|
   |nickName|string|
   |userType|string|
   |locale|string|
   |timezone|string|
   |messaggi di posta elettronica [digitare EQ "Home"]. valore|Stringa|
   |messaggi di posta elettronica [digitare EQ "other"]. Value|Stringa|
   |phoneNumbers [digitare EQ "Home"]. valore|Stringa|
   |phoneNumbers [digitare EQ "other"]. Value|Stringa|
   |phoneNumbers [digitare EQ "pager"]. valore|Stringa|
   |indirizzi [digitare EQ "Home"]. streetAddress|Stringa|
   |indirizzi [digitare EQ "Home"]. località|Stringa|
   |indirizzi [digitare EQ "Home"]. Region|Stringa|
   |indirizzi [digitare EQ "Home"]. postalCode|Stringa|
   |indirizzi [digitare EQ "Home"]. Country|Stringa|
   |indirizzi [digitare EQ "Home"]. formattato|Stringa|
   |indirizzi [digitare EQ "other"]. streetAddress|Stringa|
   |indirizzi [digitare EQ "other"]. località|Stringa|
   |indirizzi [digitare EQ "other"]. Region|Stringa|
   |indirizzi [tipo EQ "other"]. postalCode|Stringa|
   |indirizzi [digitare EQ "other"]. Country|Stringa|
   |ruoli [Primary EQ "true"]. display|Stringa|
   |ruoli [Primary EQ "true"]. Type|string|
   |roles[primary eq "True"].value|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Informazioni di riferimento|
   |urn: IETF: params: SCIM: schemas: Extension: GlobalRelay: 2.0: utente: proxyAddresses|Stringa|
   |urn: IETF: params: SCIM: schemas: Extension: GlobalRelay: 2.0: utente: extensionAttribute1|Stringa|
   |urn: IETF: params: SCIM: schemas: Extension: GlobalRelay: 2.0: utente: extensionAttribute2|Stringa|
   |urn: IETF: params: SCIM: schemas: Extension: GlobalRelay: 2.0: utente: extensionAttribute3|Stringa|
   |urn: IETF: params: SCIM: schemas: Extension: GlobalRelay: 2.0: utente: extensionAttribute4|Stringa|
   |urn: IETF: params: SCIM: schemas: Extension: GlobalRelay: 2.0: utente: extensionAttribute5|Stringa|
   |urn: IETF: params: SCIM: schemas: Extension: GlobalRelay: 2.0: utente: extensionAttribute6|Stringa|
   |urn: IETF: params: SCIM: schemas: Extension: GlobalRelay: 2.0: utente: extensionAttribute7|Stringa|
   |urn: IETF: params: SCIM: schemas: Extension: GlobalRelay: 2.0: utente: extensionAttribute8|Stringa|
   |urn: IETF: params: SCIM: schemas: Extension: GlobalRelay: 2.0: utente: extensionAttribute9|Stringa|
   |urn: IETF: params: SCIM: schemas: Extension: GlobalRelay: 2.0: utente: extensionAttribute10|Stringa|
   |urn: IETF: params: SCIM: schemas: Extension: GlobalRelay: 2.0: utente: extensionAttribute11|Stringa|
   |urn: IETF: params: SCIM: schemas: Extension: GlobalRelay: 2.0: utente: extensionAttribute12|Stringa|
   |urn: IETF: params: SCIM: schemas: Extension: GlobalRelay: 2.0: utente: extensionAttribute13|Stringa|
   |urn: IETF: params: SCIM: schemas: Extension: GlobalRelay: 2.0: utente: extensionAttribute14|Stringa|
   |urn: IETF: params: SCIM: schemas: Extension: GlobalRelay: 2.0: utente: extensionAttribute15|Stringa|



10. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to Global Relay Identity Sync**.

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Global Relay Identity Sync nella sezione **attribute-mapping** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in sincronizzazione identità inoltro globale per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      |Attributo|Type|
      |---|---|
      |displayName|string|
      |Membri di|Informazioni di riferimento|

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per la sincronizzazione dell'identità dell'inoltro globale, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in sincronizzazione identità inoltro globale scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo di sincronizzazione iniziale richiede più tempo dei cicli successivi, che verranno eseguiti ogni 40 minuti circa quando il servizio di provisioning di Azure AD è in esecuzione. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

1. Usare i [log di provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
2. Controllare l'[indicatore di stato](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
3. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
