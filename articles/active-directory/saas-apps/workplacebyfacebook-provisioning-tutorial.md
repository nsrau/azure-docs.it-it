---
title: 'Esercitazione: Configurare Workplace by Facebook per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Workplace by Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22576be8dec021f0f18a6e2dda16891ce70d4f13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603213"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Esercitazione: Configurare Workplace by Facebook per il provisioning utenti automatico

Questa esercitazione descrive i passaggi che è necessario eseguire sia in Workplace by Facebook che in Azure Active Directory (Azure AD) per configurare il provisioning automatico degli utenti. Quando si configura la configurazione, Azure AD esegue automaticamente il provisioning e il provisioning di utenti e gruppi in [Workplace by Facebook](https://work.workplace.com/) usando il servizio di provisioning di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="migrating-to-the-new-workplace-by-facebook-application"></a>Migrazione alla nuova applicazione Workplace by Facebook
Se hai un'integrazione esistente con Workplace by Facebook, consulta la sezione seguente sulle modifiche in arrivo. Se stai configurando Workplace by Facebook per la prima volta, puoi saltare questa sezione e passare alle funzionalità supportate. 

#### <a name="whats-changing"></a>Modifiche introdotte
* Modifiche sul lato Azure AD: il metodo di autorizzazione per eseguire il provisioning degli utenti in Workplace è stato storicamente un token segreto di lunga durata. Presto vedrai il metodo di autorizzazione modificato in una concessione di autorizzazione OAuth. 
* Modifiche sul lato Workplace: in precedenza l'app Azure AD era un'integrazione personalizzata in Workplace by Facebook.Changes on the Workplace side: Previously the Azure AD app was a custom integration in Workplace by Facebook. Ora si vedrà Azure AD nella directory delle integrazioni di Workplace come applicazione di terze parti. 

 

#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>Cosa è necessario fare per eseguire la migrazione dell'integrazione personalizzata esistente nella nuova applicazione?
Se si dispone di un'integrazione di Workplace esistente con un token valido, **non è richiesta alcuna azione.** Stiamo migrando automaticamente i clienti ogni settimana alla nuova applicazione. Questo viene fatto completamente dietro le quinte. Se non è possibile attendere e si desidera passare manualmente alla nuova applicazione, è possibile aggiungere una nuova istanza di Workplace dalla raccolta e configurare nuovamente il provisioning. Tutte le nuove istanze di Workplace utilizzeranno automaticamente la nuova versione dell'applicazione. 

 
Se l'integrazione di Workplace è in quarantena, dovrai fornire nuovamente un token valido per poterti migrare. La sezione delle credenziali di amministratore verrà visualizzata in grigio, ma è possibile aggiungere quanto segue (**? Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride , true**) all'URL per salvare nuovamente le credenziali. 

https://portal.azure.com/?Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride=true

#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Come è possibile stabilire se è stata eseguita la migrazione dell'applicazione? 
Quando viene eseguita la migrazione dell'applicazione, il banner nella sezione di autorizzazione sulle modifiche di upcomming verrà rimosso e il campo del token segreto verrà sostituito con un pulsante di autorizzazione blu. 

#### <a name="the-admin-credentials-section-is-greyed-out-on-my-application-and-i-cant-save-why"></a>La sezione delle credenziali di amministratore è disattivata nell'applicazione e non è possibile salvare. Perché?
Abbiamo bloccato la sezione delle credenziali di amministratore per i clienti di Workplace esistenti. Quando il tenant è stato migrato alla nuova applicazione Workplace, sarà possibile aggiornare nuovamente la sezione relativa alle credenziali di amministratore. Se non riesci a attendere, puoi utilizzare l'URL qui sopra per modificare l'applicazione. 

 
#### <a name="when-will-these-changes-happen"></a>Quando avranno apportato queste modifiche?
Tutte le nuove istanze di Workplace utilizzeranno già il nuovo metodo di integrazione/autorizzazione. Le integrazioni esistenti verranno migrate gradualmente entro maggio. Il team di lavoro ha fornito una proroga sulla scadenza dal 28 febbraio al 1 maggio. 

## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in Workplace by Facebook
> * Rimuovere gli utenti in Workplace da Facebook quando non richiedono più l'accesso
> * Mantenere sincronizzati gli attributi utente tra Azure AD e Workplace by FacebookKeep user attributes synchronized between Azure AD and Workplace by Facebook
> * [Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/workplacebyfacebook-tutorial) su Workplace by Facebook (consigliato)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un account utente in Azure AD con [l'autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per configurare il provisioning (ad esempio Amministratore applicazioni, Amministratore applicazione cloud, Proprietario applicazione o Amministratore globale)A user account in Azure AD with permission to configure provisioning (es. Application Administrator, Cloud Application administrator, Application Owner, or Global Administrator)
* Una sottoscrizione di Workplace by Facebook abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Informazioni sul funzionamento del servizio di [provisioning.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinare chi riverrà [nell'ambito del provisioning.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Determinare i dati [di cui eseguire il mapping tra Azure AD e Workplace di Facebook](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>Passaggio 2. Configurare Workplace by Facebook per supportare il provisioning con Azure ADConfigure Workplace by Facebook to support provisioning with Azure AD

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti e/o gruppi in Azure AD rappresentano gli utenti che devono accedere all'app Workplace by Facebook. Dopo aver stabilito questo, è possibile assegnare tali utenti all'app Workplace by Facebook seguendo le istruzioni riportate nell'articolo seguente:

*   È consigliabile assegnare un singolo utente di Azure AD a Workplace by Facebook per testare la configurazione del provisioning. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

*   Quando si assegna un utente a Workplace by Facebook, è necessario selezionare un ruolo utente valido. Il ruolo "Default Access" (Accesso predefinito) non è applicabile per il provisioning.

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere Workplace by Facebook dalla raccolta di applicazioni di Azure ADAdd Workplace by Facebook from the Azure AD application gallery

Aggiungere Workplace by Facebook dalla raccolta di applicazioni di Azure AD per iniziare a gestire il provisioning in Workplace by Facebook.Add Workplace by Facebook from the Azure AD application gallery to start managing provisioning to Workplace by Facebook. Se in precedenza hai configurato Workplace by Facebook per SSO, puoi utilizzare la stessa applicazione. Tuttavia, è consigliabile creare un'app separata durante il test iniziale dell'integrazione. Ulteriori informazioni sull'aggiunta di un'applicazione dalla raccolta [sono disponibili qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire chi riverrà nell'ambito del provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito degli utenti in base all'assegnazione all'applicazione e o in base agli attributi dell'utente/gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning nell'app in base all'assegnazione, è possibile usare la [procedura](../manage-apps/assign-user-or-group-access-portal.md) seguente per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning in base esclusivamente agli attributi dell'utente o del gruppo, è possibile utilizzare un filtro di ambito come descritto [di seguito.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 

* Quando assegni utenti e gruppi su Workplace tramite Facebook, devi selezionare un ruolo diverso da **Accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non effettivamente autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli. 

* Inizia in piccolo. Esegui test con un piccolo set di utenti e gruppi prima di distribuirlo a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Workplace by Facebook**.

    ![Collegamento di Workplace by Facebook nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore,** fare clic su **Autorizza**. Sarai reindirizzato a Workplace dalla pagina di autorizzazione di Facebook. Inserisci il tuo Workplace by Facebook nome utente e fare clic sul pulsante **Continua.** Fare clic su Test connessione per verificare che Azure AD possa connettersi a Workplace by Facebook.Click **Test Connection** to ensure Azure AD can connect to Workplace by Facebook. Se la connessione non riesce, assicurati che l'account Workplace by Facebook disponga delle autorizzazioni di amministratore e riprova.

    ![provisioning](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![Autorizza](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. Nel campo **E-mail** di notifica immettere l'indirizzo di posta elettronica di una persona o di un gruppo che deve ricevere le notifiche di errore di provisioning e selezionare la casella di controllo Invia una **notifica tramite posta elettronica quando si verifica un errore.**

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **Mapping** selezionare **Sincronizza utenti di Azure Active Directory con Workplace di Facebook**.

9. Esaminare gli attributi utente sincronizzati da Azure AD a Workplace by Facebook nella sezione **Mapping degli attributi.** Gli attributi selezionati come proprietà **Corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Workplace by Facebook per le operazioni di aggiornamento. Se scegli di modificare [l'attributo](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)target corrispondente, dovrai assicurarti che l'API Workplace by Facebook supporti il filtraggio degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|
   |---|---|
   |userName|string|
   |displayName|string|
   |active|Boolean|
   |title|Boolean|
   |emails[type eq "work"].value|string|
   |name.givenName|string|
   |name.familyName|string|
   |nome.formattato|string|
   |addresses[tipo eq "lavoro"].formato|string|
   |addresses[type eq "work"].streetAddress|string|
   |addresses[tipo eq "lavoro"].località|string|
   |addresses[digitare eq "lavoro"].regione|string|
   |addresses[tipo eq "lavoro"].country|string|
   |addresses[type eq "work"].postalCode|string|
   |addresses[tipo eq "altro"].formato|string|
   |phoneNumbers[type eq "work"].value|string|
   |phoneNumbers[type eq "mobile"].value|string|
   |phoneNumbers[type eq "fax"].value|string|
   |externalId|string|
   |preferredLanguage|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|string|

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning di Azure AD per Workplace by Facebook, modificare lo stato di provisioning su Attivato nella sezione Impostazioni.To enable the Azure AD provisioning service for Workplace by Facebook, change the **Provisioning Status** to **On** in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definisci gli utenti e/o i gruppi di cui desideri effettuare il provisioning su Workplace by Facebook scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

Questa operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni.** L'esecuzione del ciclo iniziale richiede più tempo rispetto ai cicli successivi, che si verificano approssimativamente ogni 40 minuti, purché sia in esecuzione il servizio di provisioning di Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:Once you've configured provisioning, use the following resources to monitor your deployment:

1. Utilizzare i registri di [provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare quali utenti sono stati sottoposti a provisioning con successo o meno
2. Controllare la barra di [avanzamento](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) per visualizzare lo stato del ciclo di provisioning e la sua vicinanza al completamento
3. Se la configurazione di provisioning sembra essere in uno stato non integro, l'applicazione andrà in quarantena. Ulteriori informazioni sugli stati di quarantena [sono disponibili qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi
*  Se viene visualizzato un utente creato senza successo ed è presente un evento del registro di controllo con il codice "1789003", significa che l'utente proviene da un dominio non verificato.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
