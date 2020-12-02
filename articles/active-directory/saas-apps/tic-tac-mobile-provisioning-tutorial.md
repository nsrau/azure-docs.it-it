---
title: 'Esercitazione: Configurare Tic-Tac Mobile per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Tic-Tac Mobile.
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
ms.openlocfilehash: a09594d1bc6037f252ba71855ae302208b4980d4
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182221"
---
# <a name="tutorial-configure-tic-tac-mobile-for-automatic-user-provisioning"></a>Esercitazione: Configurare Tic-Tac Mobile per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire sia in Tic-Tac Mobile che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Una volta configurato, Azure AD effettua automaticamente il provisioning e il deprovisioning di utenti e gruppi per [Tic-Tac Mobile](https://www.tictacmobile.com/) tramite il servizio di provisioning di Azure AD. Per informazioni su questo servizio e su come funziona, insieme alle domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning degli utenti in applicazioni SaaS (software-as-a-service) con Azure AD](../app-provisioning/user-provisioning.md).


## <a name="capabilities-supported"></a>Funzionalità supportate

> [!div class="checklist"]
> * Creazione di utenti in Tic-Tac Mobile.
> * Rimozione di utenti in Tic-Tac Mobile quando l'accesso non è più necessario.
> * Mantenimento della sincronizzazione degli attributi utente tra Azure AD e Tic-Tac Mobile.

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](../develop/quickstart-create-new-tenant.md).
* Un account utente in Azure AD con l'[autorizzazione](../roles/permissions-reference.md) per configurare il provisioning, ad esempio amministratore di applicazioni, amministratore di applicazioni cloud, proprietario di applicazioni o amministratore globale.
* Un account [Tic-Tac Mobile](https://www.tictacmobile.com/) con il ruolo di amministratore con privilegi elevati.


## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning

1. Vedere le informazioni su [come funziona il servizio di provisioning](../app-provisioning/user-provisioning.md).
1. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Determinare i dati di cui [eseguire il mapping tra Azure AD e Tic-Tac Mobile](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-tic-tac-mobile-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare Tic-Tac Mobile per supportare il provisioning con Azure AD

Contattare support@tictacmobile.com per ottenere l'**URL tenant** e il **token segreto**. È necessario avere un ruolo di amministratore con privilegi elevati in Tic-Tac Mobile per ricevere un token. Il token dovrà essere immesso nel campo **Token segreto** nella scheda **Provisioning** dell'applicazione Tic-Tac Mobile nel portale di Azure.

## <a name="step-3-add-tic-tac-mobile-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere Tic-Tac Mobile dalla raccolta di applicazioni di Azure AD

Aggiungere Tic-Tac Mobile dalla raccolta di applicazioni di Azure AD per iniziare a gestire il provisioning in Tic-Tac Mobile. Se Tic-Tac Mobile è stato configurato in precedenza per l'accesso Single Sign-On, è possibile usare la stessa applicazione. Quando si testa inizialmente l'integrazione, creare un'app separata. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, vedere [Provisioning dell'applicazione basato su attributi con filtri per la definizione dell'ambito](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning

Con il servizio di provisioning di Azure AD, è possibile definire l'ambito per gli utenti di cui verrà effettuato il provisioning in base all'assegnazione all'applicazione oppure in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà effettuato il provisioning per l'app in base all'assegnazione, seguire la procedura descritta in [Gestire l'assegnazione di utenti per un'app in Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà effettuato il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, usare un filtro per la definizione dell'ambito come descritto in [Provisioning dell'applicazione basato su attributi con filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Quando si assegnano utenti e gruppi a Tic-Tac Mobile, è necessario selezionare un ruolo diverso da **Accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](../develop/howto-add-app-roles-in-azure-ad-apps.md) per aggiungere altri ruoli.
* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di procedere alla distribuzione generale. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile mantenere il controllo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di definizione dell'ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-5-configure-automatic-user-provisioning-to-tic-tac-mobile"></a>Passaggio 5. Configurare il provisioning utenti automatico per Tic-Tac Mobile

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti o gruppi in Clarizen One in base alle assegnazioni di utenti o gruppi in Azure AD.

### <a name="configure-automatic-user-provisioning-for-tic-tac-mobile-in-azure-ad"></a>Configurare il provisioning utenti automatico per Tic-Tac Mobile in Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Screenshot che mostra il riquadro Applicazioni aziendali.](common/enterprise-applications.png)

1. Nell'elenco delle applicazioni selezionare **Tic-Tac Mobile**.

    ![Screenshot che mostra il collegamento a Tic-Tac Mobile nell'elenco delle applicazioni.](common/all-applications.png)

1. Selezionare la scheda **Provisioning**.

    ![Screenshot che mostra la scheda Provisioning.](common/provisioning.png)

1. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot che mostra l'opzione Automatico nella scheda Provisioning.](common/provisioning-automatic.png)

1. Nella sezione **Credenziali amministratore** immettere l'**URL tenant** e il **token segreto** di Tic-Tac Mobile. Selezionare **Test connessione** per verificare che Azure AD possa connettersi a Tic-Tac Mobile. Se la connessione non riesce, verificare che l'account Tic-Tac Mobile abbia autorizzazioni di amministratore e riprovare.

    ![Screenshot che mostra la casella Token segreto.](common/provisioning-testconnection-tenanturltoken.png)

1. Nella casella **Indirizzo di posta elettronica per le notifiche** immettere l'indirizzo di posta elettronica di una persona o di un gruppo che riceverà le notifiche degli errori di provisioning. Selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Screenshot che mostra la casella Indirizzo di posta elettronica per le notifiche.](common/provisioning-notification-email.png)

1. Selezionare **Salva**.

1. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Tic-Tac Mobile** (Sincronizza utenti di Azure Active Directory in Tic-Tac Mobile).

1. Esaminare gli attributi utente sincronizzati da Azure AD a Tic-Tac Mobile nella sezione **Mapping di attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per l'abbinamento con gli account utente in Tic-Tac Mobile per le operazioni di aggiornamento. Se si cambia l'[attributo di destinazione corrispondente](../app-provisioning/customize-application-attributes.md), sarà necessario assicurarsi che l'API Tic-Tac Mobile supporti il filtro degli utenti basato su tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|
   |---|---|
   |userName|string|
   |name.givenName|string|
   |name.familyName|string|
   |externalId|string|
   |title|string|
   |emails[type eq"work"].value|string|
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

1. Per configurare i filtri di definizione dell'ambito, vedere le istruzioni riportate in questa [esercitazione](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Per abilitare il servizio di provisioning di Azure AD per Tic-Tac Mobile, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Screenshot che mostra l'opzione Stato del provisioning impostata su Sì.](common/provisioning-toggle-on.png)

1. Definire gli utenti o i gruppi di cui si vuole effettuare il provisioning in Tic-Tac Mobile selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Screenshot che mostra l'ambito del provisioning.](common/provisioning-scope.png)

1. Quando si è pronti per eseguire il provisioning, selezionare **Salva**.

    ![Screenshot che mostra il salvataggio della configurazione del provisioning.](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo di sincronizzazione iniziale richiede più tempo dei cicli successivi, che verranno eseguiti ogni 40 minuti circa quando il servizio di provisioning di Azure AD è in esecuzione.

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione

Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione.

1. Usare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
1. Controllare l'[indicatore di stato](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
1. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, vedere [Provisioning delle applicazioni in stato di quarantena](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)