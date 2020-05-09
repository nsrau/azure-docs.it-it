---
title: 'Esercitazione: Configurare Samanage per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a samanage.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2020
ms.author: Zhchia
ms.openlocfilehash: 182d314b24ce082d996cb692e2a7bb35265abcfe
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628053"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Esercitazione: Configurare Samanage per il provisioning utenti automatico
Questa esercitazione descrive i passaggi da eseguire in samanage e Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Se configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning [di utenti](https://www.samanage.com/pricing/) e gruppi in samanage usando il servizio di provisioning Azure ad. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="migrate-to-the-new-samange-application"></a>Eseguire la migrazione alla nuova applicazione Samange

Se si dispone di un'integrazione esistente con samanage, vedere la sezione seguente sulle modifiche imminenti. Se si sta configurando samanage per la prima volta, è possibile ignorare questa sezione e passare alle **funzionalità supportate**.

#### <a name="whats-changing"></a>Modifiche introdotte
* Modifiche al lato Azure AD: il metodo di autorizzazione per il provisioning degli utenti in Samange è stato in passato l' **autenticazione di base**. Si noterà presto che il metodo di autorizzazione è stato modificato in **token segreto di lunga durata**.


#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>Cosa è necessario fare per eseguire la migrazione dell'integrazione personalizzata esistente alla nuova applicazione?
Se si dispone di un'integrazione di samanage esistente con credenziali di amministratore valide, **non è richiesta alcuna azione**. I clienti vengono migrati automaticamente alla nuova applicazione. Questo processo viene eseguito completamente dietro le quinte. Se le credenziali esistenti scadono o se è necessario autorizzare di nuovo l'accesso all'applicazione, è necessario generare un token segreto di lunga durata. Per generare un nuovo token, fare riferimento al passaggio 2 di questo articolo.


#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Come è possibile stabilire se è stata eseguita la migrazione dell'applicazione? 
Quando si esegue la migrazione dell'applicazione, nella sezione **credenziali amministratore** i campi **nome utente amministratore** e **password amministratore** verranno sostituiti con un singolo campo **token segreto** .

## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in samanage
> * Rimuovere gli utenti in samanage quando non richiedono più l'accesso
> * Mantieni gli attributi utente sincronizzati tra Azure AD e samanage
> * Effettuare il provisioning di gruppi e appartenenze a gruppi in samanage
> * [Single Sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/samanage-tutorial) per samanage (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un account utente in Azure AD con l' [autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per la configurazione del provisioning, ad esempio amministratore dell'applicazione, amministratore di applicazioni cloud, proprietario dell'applicazione o amministratore globale. 
* Un [tenant](https://www.samanage.com/pricing/) di samanage con il pacchetto professionale.
* Un account utente in samanage con autorizzazioni di amministratore.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Informazioni sul [funzionamento del servizio di provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determinare chi sarà nell' [ambito per il provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determinare quali dati eseguire il [mapping tra Azure ad e](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)samanage. 

## <a name="step-2-configure-samanage-to-support-provisioning-with-azure-ad"></a>Passaggio 2. Configurare samanage per supportare il provisioning con Azure AD

Per generare un token segreto per l'autenticazione, fare riferimento a [questo](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657).

## <a name="step-3-add-samanage-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere samanage dalla raccolta di applicazioni di Azure AD

Aggiungere samanage dalla raccolta di applicazioni Azure AD per iniziare a gestire il provisioning in samanage. Se per SSO è stato configurato in precedenza, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'applicazione separata quando si testa inizialmente l'integrazione. Altre informazioni sull'aggiunta di un'applicazione dalla raccolta sono disponibili [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire chi sarà nell'ambito per il provisioning 

Il servizio Azure AD provisioning consente di definire l'ambito di chi verrà eseguito il provisioning in base all'assegnazione all'applicazione e o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile usare i [passaggi](../manage-apps/assign-user-or-group-access-portal.md) seguenti per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito di cui verrà eseguito il provisioning basato esclusivamente sugli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Quando si assegnano utenti e gruppi a samanage, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti con il ruolo di accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli. 

* Avviare Small. Eseguire il test con un piccolo set di utenti e gruppi prima di distribuirlo a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-samanage"></a>Passaggio 5. Configurare il provisioning utenti automatico per samanage 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Samanage in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Samanage**.

    ![Collegamento di Samanage nell'elenco Applicazioni](common/all-applications.png)

3. Selezionare la scheda **provisioning** .

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere `https://api.samanage.com` in **URL tenant**.  Immettere il valore del token Secret recuperato in precedenza nel **token Secret**. Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a samanage. Se la connessione non riesce, verificare che l'account samanage abbia autorizzazioni di amministratore e riprovare.

    ![provisioning](./media/samanage-provisioning-tutorial/provisioning.png)

6. Nel campo **messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che deve ricevere le notifiche degli errori di provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica quando si verifica un errore** .

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a**samanage.

9. Esaminare gli attributi utente sincronizzati da Azure AD a samanage nella sezione **mapping** degli attributi. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Samanage per le operazioni di aggiornamento. Se si sceglie di modificare l' [attributo di destinazione corrispondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), sarà necessario assicurarsi che l'API samanage supporti il filtraggio degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      ![Mapping utente Samange](./media/samanage-provisioning-tutorial/user-attributes.png)

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to Samanage** (Sincronizza gruppi di Azure Active Directory con Samanage).

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a samanage nella sezione **mapping** degli attributi. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Samanage per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      ![Mapping del gruppo Samange](./media/samanage-provisioning-tutorial/group-attributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per samanage, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole effettuare il provisioning in Samanage selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

Questa operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti nell' **ambito** nella sezione **Impostazioni** . Il ciclo iniziale richiede più tempo rispetto ai cicli successivi, che si verificano approssimativamente ogni 40 minuti, a condizione che sia in esecuzione il servizio di provisioning Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

1. Usare i [log di provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare gli utenti di cui è stato effettuato il provisioning con esito positivo o negativo
2. Controllare l' [indicatore di stato per visualizzare](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) lo stato del ciclo di provisioning e il modo in cui terminarlo
3. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entra in quarantena. Altre informazioni sugli stati di quarantena sono disponibili [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="connector-limitations"></a>Limitazioni dei connettori

Se si seleziona l'opzione **Sincronizza tutti gli utenti e i gruppi** e si configura un valore per l'attributo **ruoli** samanage, il valore nella casella **valore predefinito se null (facoltativo)** deve essere espresso nel formato seguente:

- {"displayName": "Role"}, dove Role è il valore predefinito desiderato.

## <a name="change-log"></a>Log delle modifiche

* 04/22/2020: metodo di autorizzazione aggiornato da auth di base a token segreto di lunga durata.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
