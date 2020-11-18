---
title: 'Esercitazione: Configurare LinkedIn Learning per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a LinkedIn Learning.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 21e2f470-4eb1-472c-adb9-4203c00300be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/30/2020
ms.author: Zhchia
ms.openlocfilehash: 1a0ec8393e3c5ea40cb53fc101040441a5ca15bb
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359035"
---
# <a name="tutorial-configure-linkedin-learning-for-automatic-user-provisioning"></a>Esercitazione: Configurare LinkedIn Learning per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire sia in LinkedIn Learning che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Una volta configurato, Azure AD effettua automaticamente il provisioning e il deprovisioning di utenti e gruppi per [LinkedIn Learning](https://learning.linkedin.com/) usando il servizio di provisioning di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in LinkedIn Learning
> * Rimuovere utenti da LinkedIn Learning quando non richiedono più l'accesso
> * Mantenere gli attributi utente sincronizzati tra Azure AD e LinkedIn Learning
> * Effettuare il provisioning di gruppi e appartenenza a gruppi in LinkedIn Learning
> * [Accesso Single Sign-On](linkedinlearning-tutorial.md) a LinkedIn Learning (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un account utente in Azure AD con l'[autorizzazione](../users-groups-roles/directory-assign-admin-roles.md) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale. 
* Approvazione e SCIM abilitati per LinkedIn Learning (contatto tramite posta elettronica).

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Vedere le informazioni su [come funziona il servizio di provisioning](../app-provisioning/user-provisioning.md).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determinare i dati di cui [eseguire il mapping tra Azure AD e LinkedIn Learning](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-linkedin-learning-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare LinkedIn Learning per supportare il provisioning con Azure AD
1. Accedere a [LinkedIn Learning Settings](https://www.linkedin.com/learning-admin/settings/global) (Impostazioni di LinkedIn Learning). Selezionare **SCIM Setup** (Configurazione SCIM) e quindi selezionare **Add new SCIM configuration** (Aggiungi nuova configurazione SCIM).

   ![Configurazione di SCIM Setup](./media/linkedin-learning-provisioning-tutorial/learning-scim-settings.png)

2. Immettere un nome per la configurazione e impostare **Auto-assign licenses** (Assegna licenze automaticamente) su On (Attiva). Fare quindi clic su **Generate token** (Genera token).

   ![Nome della configurazione SCIM](./media/linkedin-learning-provisioning-tutorial/learning-scim-configuration.png)

3. Dopo la creazione della configurazione, dovrebbe essere generato un **token di accesso**. Copiarlo per usarlo più avanti.

   ![Token di accesso SCIM](./media/linkedin-learning-provisioning-tutorial/learning-scim-token.png)

4. È possibile eseguire nuovamente qualsiasi configurazione esistente (per generare un nuovo token) o rimuoverle.

## <a name="step-3-add-linkedin-learning-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere LinkedIn Learning dalla raccolta di applicazioni di Azure AD

Aggiungere LinkedIn Learning dalla raccolta di applicazioni di Azure AD per iniziare a gestire il provisioning in LinkedIn Learning. Se LinkedIn Learning è stato configurato in precedenza per l'accesso SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quando si assegnano utenti e gruppi a LinkedIn Learning, è necessario selezionare un ruolo diverso da **Accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](../develop/howto-add-app-roles-in-azure-ad-apps.md) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-linkedin-learning"></a>Passaggio 5. Configurare il provisioning utenti automatico per LinkedIn Learning 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-linkedin-learning-in-azure-ad"></a>Per configurare il provisioning utenti automatico per LinkedIn Learning in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **LinkedIn Learning**.

    ![Collegamento di LinkedIn Learning nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatica evidenziata.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere `https://api.linkedin.com/scim` in **URL tenant**. Immettere il valore del token di accesso recuperato in precedenza in **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a LinkedIn Learning. Se la connessione non riesce, verificare che l'account LinkedIn Learning abbia autorizzazioni di amministratore e riprovare.

    ![Screenshot che mostra la finestra di dialogo Credenziali amministratore, in cui è possibile immettere l'URL del tenant e il token segreto.](./media/linkedin-learning-provisioning-tutorial/provisioning.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **Mapping** selezionare **Provision Azure Active Directory Users** (Esegui provisioning utenti di Azure Active Directory).

9. Esaminare gli attributi utente sincronizzati da Azure AD a LinkedIn Learning nella sezione **Mapping degli attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in LinkedIn Learning per le operazioni di aggiornamento. Se si sceglie di modificare l'[attributo di destinazione corrispondente](../app-provisioning/customize-application-attributes.md), sarà necessario assicurarsi che l'API LinkedIn Learning supporti il filtro degli utenti basato su tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|Supportato per il filtro|
   |---|---|---|
   |externalId|string|&check;|
   |userName|string|
   |name.givenName|string|
   |name.familyName|string|
   |displayName|string|
   |addresses[type eq "work"].locality|string|
   |title|string|
   |emails[type eq "work"].value|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Informazioni di riferimento|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|string|

10. Nella sezione **Mapping** selezionare **Provision Azure Active Directory Users** (Effettua il provisioning dei gruppi di Azure Active Directory).

11. Esaminare gli attributi gruppo sincronizzati da Azure AD a LinkedIn Learning nella sezione **Mapping degli attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in LinkedIn Learning per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    |Attributo|Type|Supportato per il filtro|
    |---|---|---|
    |displayName|string|&check;|
    |Membri di|Informazioni di riferimento|
    |externalId|string|

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per LinkedIn Learning, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in LinkedIn Learning selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

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