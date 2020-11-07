---
title: 'Esercitazione: configurare Tic-Tac mobile per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Tic-Tac mobile.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d0f24e81-fecf-4e71-bd8a-ab911366fdf5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: Zhchia
ms.openlocfilehash: 99fe2f8356b53b5d8e459dbf62534ddb4f0019b2
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357267"
---
# <a name="tutorial-configure-tic-tac-mobile-for-automatic-user-provisioning"></a>Esercitazione: configurare Tic-Tac mobile per il provisioning utenti automatico

Questa esercitazione descrive i passaggi che è necessario eseguire sia in Tic-Tac mobile che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Se configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi in [TIC-TAC mobile](https://www.tictacmobile.com/) usando il servizio di provisioning Azure ad. Per informazioni sul funzionamento di questo servizio, sul suo funzionamento e sulle domande frequenti, vedere [automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS (software as a Service) con Azure ad](../manage-apps/user-provisioning.md).


## <a name="capabilities-supported"></a>Funzionalità supportate

> [!div class="checklist"]
> * Creare utenti in Tic-Tac mobile.
> * Rimuovere gli utenti in Tic-Tac mobile quando non richiedono più l'accesso.
> * Mantieni gli attributi utente sincronizzati tra Azure AD e Tic-Tac mobile.

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Un account utente in Azure AD con l' [autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per la configurazione del provisioning. Gli esempi sono amministratore dell'applicazione, amministratore di applicazioni cloud, proprietario dell'applicazione o amministratore globale.
* Un account [TIC-TAC per dispositivi mobili](https://www.tictacmobile.com/) con un ruolo di amministratore con privilegi avanzati.


## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning

1. Vedere le informazioni su [come funziona il servizio di provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
1. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
1. Determinare quali dati eseguire il [mapping tra Azure ad e Tic-Tac mobile](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-tic-tac-mobile-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare Tic-Tac mobile per supportare il provisioning con Azure AD

Contattare support@tictacmobile.com per ottenere l' **URL del tenant** e il **token segreto**. È necessario avere un ruolo di amministratore con privilegi avanzati in Tic-Tac mobile per ricevere un token. Il token verrà immesso nella casella **token segreto** della scheda **provisioning** dell'applicazione Tic-Tac mobile nell'portale di Azure.

## <a name="step-3-add-tic-tac-mobile-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere Tic-Tac mobile dalla raccolta di applicazioni Azure AD

Aggiungere Tic-Tac mobile dalla raccolta di applicazioni Azure AD per iniziare a gestire il provisioning per Tic-Tac dispositivi mobili. Se in precedenza è stato configurato Tic-Tac mobile per Single Sign-On, è possibile usare la stessa applicazione. Quando si testa l'integrazione inizialmente, creare un'app separata. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, vedere [provisioning di applicazioni basate su attributi con filtri di ambito](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning

Con il servizio di provisioning di Azure AD, è possibile definire l'ambito di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito di cui verrà eseguito il provisioning per l'app in base all'assegnazione, seguire i passaggi descritti in [gestire l'assegnazione utente per un'app in Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito di cui verrà eseguito il provisioning basato esclusivamente sugli attributi dell'utente o del gruppo, usare un filtro di ambito come descritto in [provisioning di applicazioni basate su attributi con i filtri di ambito](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

* Quando si assegnano utenti e gruppi a Tic-Tac mobile, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti con il ruolo di accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli.
* Iniziare con pochi elementi. Eseguire un test con un piccolo set di utenti e gruppi prima di distribuire a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile mantenere il controllo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

## <a name="step-5-configure-automatic-user-provisioning-to-tic-tac-mobile"></a>Passaggio 5. Configurare il provisioning utenti automatico per Tic-Tac mobile

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti o gruppi in TestApp in base alle assegnazioni di utenti o gruppi in Azure AD.

### <a name="configure-automatic-user-provisioning-for-tic-tac-mobile-in-azure-ad"></a>Configurare il provisioning utenti automatico per Tic-Tac mobile in Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Screenshot che mostra il riquadro applicazioni aziendali.](common/enterprise-applications.png)

1. Nell'elenco delle applicazioni selezionare **TIC-TAC mobile**.

    ![Screenshot che mostra il collegamento Tic-Tac mobile nell'elenco delle applicazioni.](common/all-applications.png)

1. Selezionare la scheda **Provisioning**.

    ![Screenshot che mostra la scheda provisioning.](common/provisioning.png)

1. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot che mostra l'opzione automatica della scheda provisioning.](common/provisioning-automatic.png)

1. Nella sezione **credenziali amministratore** immettere l' **URL del tenant** di Tic-Tac mobile e il **token segreto**. Selezionare **Test connessione** per assicurarsi che Azure ad possa connettersi a Tic-Tac mobile. Se la connessione non riesce, verificare che l'account Tic-Tac mobile disponga delle autorizzazioni di amministratore e riprovare.

    ![Screenshot che mostra la casella del token segreto.](common/provisioning-testconnection-tenanturltoken.png)

1. Nella casella **posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche degli errori di provisioning. Selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Screenshot che mostra la casella di messaggio di posta elettronica di notifica.](common/provisioning-notification-email.png)

1. Selezionare **Salva**.

1. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory users to Tic-Tac mobile**.

1. Esaminare gli attributi utente sincronizzati da Azure AD a Tic-Tac mobile nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Tic-Tac mobile per le operazioni di aggiornamento. Se si modifica l' [attributo di destinazione corrispondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), è necessario assicurarsi che l'API Tic-Tac Mobile supporti il filtraggio degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|
   |---|---|
   |userName|string|
   |name.givenName|string|
   |name.familyName|string|
   |externalId|string|
   |title|string|
   |messaggi di posta elettronica [digitare EQ "Work"]. Value|string|
   |preferredLanguage|string|
   |externalId|string|
   |userType|string|
   |locale|string|
   |timezone|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|string|

1. Per configurare i filtri di ambito, vedere le istruzioni nell'esercitazione relativa al [filtro di ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

1. Per abilitare il servizio di provisioning Azure AD per Tic-Tac mobile, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Screenshot che mostra lo stato di provisioning attivato.](common/provisioning-toggle-on.png)

1. Definire gli utenti o i gruppi di cui si vuole eseguire il provisioning in Tic-Tac mobile selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Screenshot che mostra l'ambito del provisioning.](common/provisioning-scope.png)

1. Quando si è pronti per eseguire il provisioning, selezionare **Salva**.

    ![Screenshot che mostra il salvataggio della configurazione del provisioning.](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo di sincronizzazione iniziale richiede più tempo dei cicli successivi, che verranno eseguiti ogni 40 minuti circa quando il servizio di provisioning di Azure AD è in esecuzione.

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione

Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione.

1. Usare i [log di provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
1. Controllare l'[indicatore di stato](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
1. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per ulteriori informazioni sugli stati di quarantena, vedere [provisioning di applicazioni in stato di quarantena](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
