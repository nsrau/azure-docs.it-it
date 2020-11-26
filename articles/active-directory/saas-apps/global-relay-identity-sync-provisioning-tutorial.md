---
title: 'Esercitazione: Configurare Global Relay Identity Sync per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Global Relay Identity Sync.
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
ms.topic: tutorial
ms.date: 10/22/2020
ms.author: Zhchia
ms.openlocfilehash: d003a512ebde626b8726dfccc58110e53f1cd467
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96180915"
---
# <a name="tutorial-configure-global-relay-identity-sync-for-automatic-user-provisioning"></a>Esercitazione: Configurare Global Relay Identity Sync per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire sia in Global Relay Identity Sync che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Una volta configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi per Global Relay Identity Sync usando il servizio di provisioning di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creazione di utenti in Global Relay Identity Sync
> * Rimozione di utenti in Global Relay Identity Sync quando l'accesso non è più necessario
> * Mantenimento della sincronizzazione degli attributi utente tra Azure AD e Global Relay Identity Sync
> * Provisioning di gruppi e appartenenze a gruppi in Global Relay Identity Sync


> [!NOTE]
> Il connettore per il provisioning di Global Relay Identity Sync usa un metodo di autorizzazione SCIM che non è più supportato a causa di problemi di sicurezza. In Global Relay sono in corso le attività necessarie per passare a un metodo di autorizzazione più sicuro.

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un account utente in Azure AD con l'[autorizzazione](../roles/permissions-reference.md) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Vedere le informazioni su [come funziona il servizio di provisioning](../app-provisioning/user-provisioning.md).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determinare quali dati [mappare tra Azure AD e Global Relay Identity Sync](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-global-relay-identity-sync-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare Global Relay Identity Sync per supportare il provisioning con Azure AD

Contattare il rappresentante di Global Relay Identity Sync per ricevere l'URL del tenant che verrà immesso nel campo **URL tenant** nella scheda Provisioning dell'applicazione Global Relay Identity Sync nel portale di Azure.

## <a name="step-3-add-global-relay-identity-sync-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere Global Relay Identity Sync dalla raccolta di applicazioni di Azure AD

Aggiungere Global Relay Identity Sync dalla raccolta di applicazioni di Azure AD per iniziare a gestire il provisioning in Global Relay Identity Sync. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-global-relay-identity-sync"></a>Passaggio 5. Configurare il provisioning utenti automatico per Global Relay Identity Sync 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Global Relay Identity Sync in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-global-relay-identity-sync-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Global Relay Identity Sync in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Global Relay Identity Sync**.

    ![Collegamento per Global Relay Identity Sync nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Automatico nella scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere l'**URL tenant** di Global Relay Identity Sync. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Global Relay Identity Sync. Se la connessione non riesce, verificare che l'account Global Relay Identity Sync disponga delle autorizzazioni di amministratore e contattare il rappresentante di Global Relay per risolvere il problema.

    ![Pulsante Autorizzazione](media/global-relay-identity-sync-provisioning-tutorial/authorization.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Global Relay Identity Sync** (Sincronizza utenti di Azure Active Directory in Global Relay Identity Sync).

9. Esaminare gli attributi utente sincronizzati da Azure AD a Global Relay Identity Sync nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Global Relay Identity Sync per le operazioni di aggiornamento. Se si sceglie di modificare l'[attributo di destinazione corrispondente](../app-provisioning/customize-application-attributes.md), sarà necessario assicurarsi che l'API Global Relay Identity Sync supporti il filtro degli utenti basato su tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|
   |---|---|
   |userName|string|
   |active|Boolean|
   |displayName|string|
   |title|string|
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
   |addresses[type eq "other"].formatted|string|
   |phoneNumbers[type eq "work"].value|string|
   |phoneNumbers[type eq "mobile"].value|string|
   |phoneNumbers[type eq "fax"].value|string|
   |externalId|string|
   |name.honorificPrefix|string|
   |name.honorificSuffix|string|
   |nickName|string|
   |userType|string|
   |locale|string|
   |timezone|string|
   |emails[type eq "home"].value|string|
   |emails[type eq "other"].value|string|
   |phoneNumbers[type eq "home"].value|string|
   |phoneNumbers[type eq "other"].value|string|
   |phoneNumbers[type eq "pager"].value|string|
   |addresses[type eq "home"].streetAddress|string|
   |addresses[type eq "home"].locality|string|
   |addresses[type eq "home"].region|string|
   |addresses[type eq "home"].postalCode|string|
   |addresses[type eq "home"].country|string|
   |addresses[type eq "home"].formatted|string|
   |addresses[type eq "other"].streetAddress|string|
   |addresses[type eq "other"].locality|string|
   |addresses[type eq "other"].region|string|
   |addresses[type eq "other"].postalCode|string|
   |addresses[type eq "other"].country|string|
   |roles[primary eq "True"].display|string|
   |roles[primary eq "True"].type|string|
   |roles[primary eq "True"].value|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Informazioni di riferimento|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:proxyAddresses|string|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:extensionAttribute1|string|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:extensionAttribute2|string|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:extensionAttribute3|string|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:extensionAttribute4|string|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:extensionAttribute5|string|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:extensionAttribute6|string|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:extensionAttribute7|string|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:extensionAttribute8|string|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:extensionAttribute9|string|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:extensionAttribute10|string|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:extensionAttribute11|string|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:extensionAttribute12|string|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:extensionAttribute13|string|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:extensionAttribute14|string|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:extensionAttribute15|string|



10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to Global Relay Identity Sync** (Sincronizza gruppi di Azure Active Directory in Global Relay Identity Sync).

11. Esaminare gli attributi gruppo sincronizzati da Azure AD a Global Relay Identity Sync nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Global Relay Identity Sync per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      |Attributo|Type|
      |---|---|
      |displayName|string|
      |Membri di|Informazioni di riferimento|

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per Global Relay Identity Sync, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui effettuare il provisioning in Global Relay Identity Sync scegliendo i valori appropriati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo di sincronizzazione iniziale richiede più tempo dei cicli successivi, che verranno eseguiti ogni 40 minuti circa quando il servizio di provisioning di Azure AD è in esecuzione. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

1. Usare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
2. Controllare l'[indicatore di stato](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
3. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)