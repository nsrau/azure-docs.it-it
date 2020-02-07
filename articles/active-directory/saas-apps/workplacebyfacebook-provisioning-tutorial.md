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
ms.openlocfilehash: 11a5e92ccf1104f36b3f2b045f9922158b1f7330
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064143"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Esercitazione: Configurare Workplace by Facebook per il provisioning utenti automatico

Questa esercitazione descrive i passaggi che è necessario eseguire in entrambe le area di lavoro di Facebook e Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Se configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi nell' [area di lavoro di Facebook](https://work.workplace.com/) usando il servizio di provisioning Azure ad. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="migrating-to-the-new-workplace-by-facebook-application"></a>Migrazione alla nuova area di lavoro per applicazione Facebook
Se si dispone di un'integrazione esistente con l'area di lavoro di Facebook, vedere la sezione seguente relativa alle modifiche in arrivo. Se si sta configurando l'area di lavoro di Facebook per la prima volta, è possibile ignorare questa sezione e passare alle funzionalità supportate. 

#### <a name="whats-changing"></a>Modifiche introdotte
* Modifiche al lato Azure AD: il metodo di autorizzazione per il provisioning degli utenti nell'area di lavoro è stato in passato un token segreto di lunga durata. A breve si noterà che il metodo di autorizzazione è stato modificato in concessione autorizzazione OAuth. 
* Modifiche sul lato dell'area di lavoro: in precedenza l'app Azure AD era un'integrazione personalizzata nell'area di lavoro di Facebook. A questo punto verrà visualizzato Azure AD nella directory Integrations dell'area di lavoro come applicazione di terze parti. 

 

#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>Cosa è necessario fare per eseguire la migrazione dell'integrazione personalizzata esistente alla nuova applicazione?
Se si dispone di un'integrazione aziendale esistente con un token valido, **non è necessaria alcuna azione**. La migrazione automatica dei clienti viene eseguita ogni settimana alla nuova applicazione. Questa operazione viene eseguita completamente dietro le quinte. Se non è possibile attendere e si desidera passare manualmente alla nuova applicazione, è possibile aggiungere una nuova istanza dell'area di lavoro dalla raccolta e configurare di nuovo il provisioning. Tutte le nuove istanze dell'area di lavoro utilizzeranno automaticamente la nuova versione dell'applicazione. 

 
Se l'integrazione dell'area di lavoro è in quarantena, sarà necessario specificare nuovamente un token valido per eseguire la migrazione dell'utente. La sezione credenziali amministratore sarà disattivata, ma è possibile aggiungere quanto segue ( **? Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride = true**) all'URL per salvare di nuovo le credenziali. 

https://portal.azure.com/?Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride=true

 
#### <a name="the-admin-credentials-section-is-greyed-out-on-my-application-and-i-cant-save-why"></a>La sezione credenziali amministratore è disattivata nell'applicazione e non è possibile salvare. Perché?
È stata bloccata la sezione credenziali amministratore per i clienti esistenti dell'area di lavoro. Quando è stata eseguita la migrazione del tenant alla nuova applicazione aziendale, sarà possibile aggiornare di nuovo la sezione credenziali amministratore. Se non è possibile attendere, è possibile usare l'URL sopra riportato per modificare l'applicazione. 

 
#### <a name="when-will-these-changes-happen"></a>Quando si verificano queste modifiche?
Tutte le nuove istanze dell'area di lavoro utilizzeranno già il nuovo metodo di integrazione/autorizzazione. Le integrazioni esistenti verranno migrate gradualmente a febbraio. La migrazione verrà completata per tutti i tenant entro la fine del mese. 

## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti nell'area di lavoro di Facebook
> * Rimuovere gli utenti nell'area di lavoro da Facebook quando non richiedono più l'accesso
> * Mantieni gli attributi utente sincronizzati tra Azure AD e l'area di lavoro di Facebook
> * [Single Sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/workplacebyfacebook-tutorial) per l'area di lavoro di Facebook (scelta consigliata)

## <a name="prerequisites"></a>Prerequisites

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un account utente in Azure AD con l' [autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per la configurazione del provisioning, ad esempio amministratore dell'applicazione, amministratore di applicazioni cloud, proprietario dell'applicazione o amministratore globale.
* Una sottoscrizione di Workplace by Facebook abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Informazioni sul [funzionamento del servizio di provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determinare chi sarà nell' [ambito per il provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determinare quali dati eseguire il [mapping tra Azure ad e l'area di lavoro di Facebook](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>Passaggio 2. Configurare l'area di lavoro di Facebook per supportare il provisioning con Azure AD

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti e/o gruppi in Azure AD rappresentano gli utenti che devono accedere all'app Workplace by Facebook. Dopo aver stabilito questo, è possibile assegnare tali utenti all'app Workplace by Facebook seguendo le istruzioni riportate nell'articolo seguente:

*   È consigliabile assegnare un singolo utente di Azure AD a Workplace by Facebook per testare la configurazione del provisioning. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

*   Quando si assegna un utente a Workplace by Facebook, è necessario selezionare un ruolo utente valido. Il ruolo "Default Access" (Accesso predefinito) non è applicabile per il provisioning.

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere l'area di lavoro di Facebook dalla raccolta di applicazioni Azure AD

Aggiungere l'area di lavoro di Facebook dalla raccolta di applicazioni Azure AD per iniziare a gestire il provisioning nell'area di lavoro di Facebook. Se in precedenza è stata impostata l'area di lavoro di Facebook per SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'applicazione separata quando si testa inizialmente l'integrazione. Altre informazioni sull'aggiunta di un'applicazione dalla raccolta sono disponibili [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire chi sarà nell'ambito per il provisioning 

Il servizio Azure AD provisioning consente di definire l'ambito di chi verrà eseguito il provisioning in base all'assegnazione all'applicazione e o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile usare i [passaggi](../manage-apps/assign-user-or-group-access-portal.md) seguenti per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito di cui verrà eseguito il provisioning basato esclusivamente sugli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Quando si assegnano utenti e gruppi all'area di lavoro di Facebook, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti con il ruolo di accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli. 

* Avviare Small. Eseguire il test con un piccolo set di utenti e gruppi prima di distribuirlo a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Workplace by Facebook**.

    ![Collegamento di Workplace by Facebook nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** fare clic su **autorizza**. Si verrà reindirizzati all'area di lavoro dalla pagina di autorizzazione di Facebook. Immettere l'area di lavoro per nome utente di Facebook e fare clic sul pulsante **continue (continua** ). Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi all'area di lavoro da Facebook. Se la connessione ha esito negativo, verificare che l'account di lavoro per Facebook disponga delle autorizzazioni di amministratore e riprovare.

    ![provisioning](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![Autorizza](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. Nel campo **messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che deve ricevere le notifiche degli errori di provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica quando si verifica un errore** .

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory Users to area di lavoro by Facebook**.

9. Esaminare gli attributi utente che vengono sincronizzati da Azure AD a area di lavoro da Facebook nella sezione **attributo-mapping** . Gli attributi selezionati come proprietà **Corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Workplace by Facebook per le operazioni di aggiornamento. Se si sceglie di modificare l' [attributo di destinazione corrispondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), è necessario assicurarsi che l'API per l'area di lavoro di Facebook supporti il filtraggio degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|
   |---|---|
   |userName|string|
   |displayName|string|
   |active|Boolean|
   |title|Boolean|
   |emails[type eq "work"].value|string|
   |name.givenName|string|
   |name.familyName|string|
   |nome. formattato|string|
   |indirizzi [tipo EQ "lavoro"]. formattato|string|
   |addresses[type eq "work"].streetAddress|string|
   |indirizzi [digitare EQ "Work"]. località|string|
   |indirizzi [digitare EQ "Work"]. Region|string|
   |indirizzi [digitare EQ "Work"]. Country|string|
   |addresses[type eq "work"].postalCode|string|
   |indirizzi [tipo EQ "other"]. formattato|string|
   |phoneNumbers[type eq "work"].value|string|
   |phoneNumbers[type eq "mobile"].value|string|
   |phoneNumbers[type eq "fax"].value|string|
   |externalId|string|
   |preferredLanguage|string|
   |urn: IETF: params: SCIM: schemas: Extension: Enterprise: 2.0: User: Manager|string|
   |urn: IETF: params: SCIM: schemas: Extension: Enterprise: 2.0: User: Department|string|

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning Azure AD per l'area di lavoro di Facebook, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning nell'area di lavoro di Facebook scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

Questa operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti nell' **ambito** nella sezione **Impostazioni** . Il ciclo iniziale richiede più tempo rispetto ai cicli successivi, che si verificano approssimativamente ogni 40 minuti, a condizione che sia in esecuzione il servizio di provisioning Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

1. Usare i [log di provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare gli utenti di cui è stato effettuato il provisioning con esito positivo o negativo
2. Controllare l' [indicatore di stato per visualizzare](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) lo stato del ciclo di provisioning e il modo in cui terminarlo
3. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entra in quarantena. Altre informazioni sugli stati di quarantena sono disponibili [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi
*  Se viene visualizzato un utente che non è stato creato correttamente ed è presente un evento del registro di controllo con il codice "1789003", significa che l'utente è da un dominio non verificato.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
