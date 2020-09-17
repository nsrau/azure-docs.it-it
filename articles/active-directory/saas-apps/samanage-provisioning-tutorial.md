---
title: 'Esercitazione: configurare SolarWinds Service Desk (in precedenza samanage) per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD al servizio SolarWinds Service Desk (in precedenza samanage).
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/13/2020
ms.author: Zhchia
ms.openlocfilehash: 0941c95ee6215a710ebb1bbc2fba9fae09ccf16d
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707267"
---
# <a name="tutorial-configure-solarwinds-service-desk-previously-samanage-for-automatic-user-provisioning"></a>Esercitazione: configurare SolarWinds Service Desk (in precedenza samanage) per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire sia in SolarWinds Service Desk (precedentemente samanage) che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Quando è configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi in [SolarWinds Service Desk](https://www.samanage.com/pricing/) usando il servizio di provisioning Azure ad. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="migrate-to-the-new-solarwinds-service-desk-application"></a>Eseguire la migrazione alla nuova applicazione SolarWinds Service Desk

Se si dispone di un'integrazione esistente con SolarWinds Service Desk, vedere la sezione seguente sulle modifiche imminenti. Se si sta configurando il servizio SolarWinds Service Desk per la prima volta, è possibile ignorare questa sezione e passare a **funzionalità supportate**.

#### <a name="whats-changing"></a>Modifiche introdotte

* Modifiche al lato Azure AD: il metodo di autorizzazione per il provisioning degli utenti in Samange è stato in passato l' **autenticazione di base**. Si noterà presto che il metodo di autorizzazione è stato modificato in **token segreto di lunga durata**.


#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>Cosa è necessario fare per eseguire la migrazione dell'integrazione personalizzata esistente alla nuova applicazione?

Se si dispone di un'integrazione di SolarWinds Service Desk con credenziali di amministratore valide, **non è necessaria alcuna azione**. I clienti vengono migrati automaticamente alla nuova applicazione. Questo processo viene eseguito completamente dietro le quinte. Se le credenziali esistenti scadono o se è necessario autorizzare di nuovo l'accesso all'applicazione, è necessario generare un token segreto di lunga durata. Per generare un nuovo token, fare riferimento al passaggio 2 di questo articolo.


#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Come è possibile stabilire se è stata eseguita la migrazione dell'applicazione? 

Quando si esegue la migrazione dell'applicazione, nella sezione **credenziali amministratore** i campi **nome utente amministratore** e **password amministratore** verranno sostituiti con un singolo campo **token segreto** .

## <a name="capabilities-supported"></a>Funzionalità supportate

> [!div class="checklist"]
> * Creare utenti in SolarWinds Service Desk
> * Rimuovere gli utenti in SolarWinds Service desk quando non richiedono più l'accesso
> * Mantieni gli attributi utente sincronizzati tra Azure AD e il servizio SolarWinds Service Desk
> * Effettuare il provisioning di gruppi e appartenenze a gruppi in SolarWinds Service Desk
> * [Single Sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/samanage-tutorial) per SolarWinds Service Desk (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un account utente in Azure AD con l'[autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale. 
* [Tenant del Service Desk per SolarWinds](https://www.samanage.com/pricing/) con il pacchetto Professional.
* Un account utente in SolarWinds Service Desk con autorizzazioni di amministratore.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Vedere le informazioni su [come funziona il servizio di provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determinare quali dati eseguire il [mapping tra Azure ad e il servizio SolarWinds Service Desk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-solarwinds-service-desk-to-support-provisioning-with-azure-ad"></a>Passaggio 2. Configurare il Service Desk per SolarWinds per supportare il provisioning con Azure AD

Per generare un token segreto per l'autenticazione, vedere [esercitazione token Authentication per l'integrazione dell'API](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657).

## <a name="step-3-add-solarwinds-service-desk-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere il Service Desk per SolarWinds dalla raccolta di applicazioni Azure AD

Aggiungere il servizio SolarWinds Service Desk dalla raccolta di applicazioni Azure AD per iniziare a gestire il provisioning in SolarWinds Service Desk. Se in precedenza è stato configurato il servizio SolarWinds Service Desk per SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Quando si assegnano utenti e gruppi al servizio SolarWinds Service Desk, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-solarwinds-service-desk"></a>Passaggio 5. Configurare il provisioning utenti automatico in SolarWinds Service Desk 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-solarwinds-service-desk-in-azure-ad"></a>Per configurare il provisioning utenti automatico per SolarWinds Service Desk in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **SolarWinds Service Desk**.

3. Selezionare la scheda **Provisioning**.

    ![Screenshot che mostra la scheda Provisioning selezionata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot che mostra la modalità di provisioning impostata su automatico.](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere `https://api.samanage.com` in **URL tenant**.  Immettere il valore del token segreto recuperato in precedenza in **Token segreto**. Selezionare **Test connessione** per assicurarsi che Azure ad possa connettersi al servizio SolarWinds Service Desk. Se la connessione ha esito negativo, verificare che l'account del Service Desk di SolarWinds disponga di autorizzazioni di amministratore e riprovare.

    ![Screenshot che mostra il pulsante Test connessione selezionato.](./media/samanage-provisioning-tutorial/provisioning.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory Users to SolarWinds Service Desk**.

9. Esaminare gli attributi utente che vengono sincronizzati da Azure AD al Service Desk SolarWinds nella sezione **mapping** degli attributi. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in SolarWinds Service Desk per le operazioni di aggiornamento. Se si sceglie di modificare l' [attributo di destinazione corrispondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), è necessario assicurarsi che l'API del Service Desk di SolarWinds supporti il filtraggio degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      ![Mapping utente Samange](./media/samanage-provisioning-tutorial/user-attributes.png)

10. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to SolarWinds Service Desk**.

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD al Service Desk di SolarWinds nella sezione **attribute-mapping** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in SolarWinds Service Desk per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      ![Mapping del gruppo Samange](./media/samanage-provisioning-tutorial/group-attributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per SolarWinds Service Desk, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in SolarWinds Service Desk scegliendo i valori desiderati in **ambito** nella sezione **Settings (impostazioni** ).

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per il provisioning, selezionare **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo di sincronizzazione iniziale richiede più tempo dei cicli successivi, che verranno eseguiti ogni 40 minuti circa quando il servizio di provisioning di Azure AD è in esecuzione. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

1. Usare i [log di provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
2. Controllare l'[indicatore di stato](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
3. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="connector-limitations"></a>Limitazioni dei connettori

Se si seleziona l'opzione **Sincronizza tutti gli utenti e i gruppi** e si configura un valore per l'attributo dei **ruoli** di Service Desk di SolarWinds, il valore nella casella **valore predefinito se null (facoltativo)** deve essere espresso nel formato seguente:

- {"displayName": "Role"}, dove Role è il valore predefinito desiderato.

## <a name="change-log"></a>Registro delle modifiche

* 09/14/2020: il nome della società è stato modificato in due esercitazioni SaaS da samanage al Service Desk SolarWinds (in precedenza samanage) per https://github.com/ravitmorales .
* 04/22/2020: metodo di autorizzazione aggiornato da auth di base a token segreto di lunga durata.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
