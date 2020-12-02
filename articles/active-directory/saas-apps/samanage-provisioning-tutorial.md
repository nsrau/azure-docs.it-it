---
title: 'Esercitazione: Configurare SolarWinds Service Desk (in precedenza Samanage) per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a SolarWinds Service Desk (in precedenza Samanage).
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2020
ms.author: Zhchia
ms.openlocfilehash: cca46d47003a1611c861986f8df839de57500db6
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181483"
---
# <a name="tutorial-configure-solarwinds-service-desk-previously-samanage-for-automatic-user-provisioning"></a>Esercitazione: Configurare SolarWinds Service Desk (in precedenza Samanage) per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire sia in SolarWinds Service Desk (in precedenza Samanage) che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Una volta configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi per [SolarWinds Service Desk](https://www.samanage.com/pricing/) tramite il servizio di provisioning di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="migrate-to-the-new-solarwinds-service-desk-application"></a>Eseguire la migrazione alla nuova applicazione SolarWinds Service Desk

Se è già disponibile un'integrazione con SolarWinds Service Desk, vedere la sezione seguente per informazioni sulle prossime modifiche. Se si configura SolarWinds Service Desk per la prima volta, è possibile ignorare questa sezione e passare a **Funzionalità supportate**.

#### <a name="whats-changing"></a>Modifiche introdotte

* Modifiche sul lato di Azure AD: Il metodo di autorizzazione per effettuare il provisioning utenti in Samange è sempre stato l'**autenticazione di base**. Presto il metodo di autorizzazione cambierà in **token segreto di lunga durata**.


#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>Che cosa bisogna fare per eseguire la migrazione dell'attuale integrazione personalizzata alla nuova applicazione?

Se attualmente è disponibile un'integrazione di SolarWinds Service Desk con credenziali di amministratore valide, **non è necessario alcun intervento**. I clienti verranno automaticamente trasferiti nella nuova applicazione. Questo processo viene completato interamente dietro le quinte. Se le credenziali esistenti scadono, oppure è necessario autorizzare di nuovo l'accesso all'applicazione, è necessario generare un token segreto di lunga durata. Per generare un nuovo token, vedere il passaggio 2 di questo articolo.


#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Come si può determinare se la migrazione dell'applicazione è stata eseguita? 

Dopo la migrazione dell'applicazione, i campi **Nome utente amministratore** e **Password amministratore** della sezione **Credenziali amministratore** verranno sostituiti dal singolo campo **Token segreto**.

## <a name="capabilities-supported"></a>Funzionalità supportate

> [!div class="checklist"]
> * Creare utenti in SolarWinds Service Desk
> * Rimuovere utenti da SolarWinds Service Desk quando non richiedono più l'accesso
> * Mantenere gli attributi utente sincronizzati tra Azure AD e SolarWinds Service Desk
> * Effettuare il provisioning di gruppi e appartenenze a gruppi in SolarWinds Service Desk
> * [Accesso Single Sign-On](./samanage-tutorial.md) a SolarWinds Service Desk (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un account utente in Azure AD con l'[autorizzazione](../roles/permissions-reference.md) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale. 
* Un [tenant di SolarWinds Service Desk](https://www.samanage.com/pricing/) con il pacchetto Professional.
* Un account utente in SolarWinds Service Desk con autorizzazioni di amministratore.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Vedere le informazioni su [come funziona il servizio di provisioning](../app-provisioning/user-provisioning.md).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determinare i dati da [mappare tra Azure AD e SolarWinds Service Desk](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-solarwinds-service-desk-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare SolarWinds Service Desk per supportare il provisioning con Azure AD

Per generare il token segreto per l'autenticazione, vedere l'[esercitazione sui token di autenticazione per l'integrazione di API](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657).

## <a name="step-3-add-solarwinds-service-desk-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere SolarWinds Service Desk dalla raccolta di applicazioni di Azure AD

Aggiungere SolarWinds Service Desk dalla raccolta di applicazioni di Azure AD per iniziare a gestire il provisioning in SolarWinds Service Desk. Se SolarWinds Service Desk è stato configurato in precedenza per l'accesso SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quando si assegnano utenti e gruppi a SolarWinds Service Desk, è necessario selezionare un ruolo diverso da **Accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](../develop/howto-add-app-roles-in-azure-ad-apps.md) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-solarwinds-service-desk"></a>Passaggio 5. Configurare il provisioning utenti automatico per SolarWinds Service Desk 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-solarwinds-service-desk-in-azure-ad"></a>Per configurare il provisioning utenti automatico per SolarWinds Service Desk in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **SolarWinds Service Desk**.

3. Selezionare la scheda **Provisioning**.

    ![Screenshot che mostra la scheda Provisioning selezionata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot che mostra la modalità di provisioning impostata su Automatico.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere `https://api.samanage.com` in **URL tenant**.  Immettere il valore del token segreto recuperato in precedenza in **Token segreto**. Selezionare **Test connessione** per verificare che Azure AD possa connettersi a SolarWinds Service Desk. Se la connessione non riesce, verificare che l'account SolarWinds Service Desk abbia le autorizzazioni di amministratore e riprovare.

    ![Screenshot che mostra il pulsante Test connessione selezionato.](./media/samanage-provisioning-tutorial/provisioning.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to SolarWinds Service Desk** (Sincronizza utenti di Azure Active Directory con SolarWinds Service Desk).

9. Esaminare gli attributi utente sincronizzati tra Azure AD e SolarWinds Service Desk nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in SolarWinds Service Desk per le operazioni di aggiornamento. Se si sceglie di cambiare l'[attributo di destinazione corrispondente](../app-provisioning/customize-application-attributes.md), sarà necessario assicurarsi che l'API SolarWinds Service Desk supporti il filtro degli utenti basato su tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      ![Mapping degli utenti di Samange](./media/samanage-provisioning-tutorial/user-attributes.png)

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to SolarWinds Service Desk** (Sincronizza gruppi di Azure Active Directory con SolarWinds Service Desk).

11. Esaminare gli attributi gruppo sincronizzati tra Azure AD e SolarWinds Service Desk nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in SolarWinds Service Desk per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      ![Mapping dei gruppi di Samange](./media/samanage-provisioning-tutorial/group-attributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per SolarWinds Service Desk, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui effettuare il provisioning in SolarWinds Service Desk scegliendo i valori appropriati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per effettuare il provisioning, selezionare **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo di sincronizzazione iniziale richiede più tempo dei cicli successivi, che verranno eseguiti ogni 40 minuti circa quando il servizio di provisioning di Azure AD è in esecuzione. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

1. Usare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
2. Controllare l'[indicatore di stato](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
3. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

Se si seleziona l'opzione **Sincronizza tutti gli utenti e i gruppi** e si configura un valore per l'attributo **roles** di SolarWinds Service Desk, il valore nella casella **Valore predefinito se Null (facoltativo)** deve essere espresso nel formato seguente:

- {"NomeVisualizzato":"ruolo"}, dove ruolo è il valore predefinito da usare.

## <a name="change-log"></a>Registro delle modifiche

* 14/09/2020 - Cambiato il nome dell'azienda in due esercitazioni da Samanage a SolarWinds Service Desk (in precedenza Samanage) in base a https://github.com/ravitmorales.
* 22/04/2020 - Aggiornato il metodo di autorizzazione da autenticazione di base a token segreto di lunga durata.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)