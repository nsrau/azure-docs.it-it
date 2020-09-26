---
title: 'Esercitazione: configurare Atlassian cloud per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Atlassian cloud.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 12/27/2019
ms.author: jeedes
ms.openlocfilehash: ca2284e3e2f581d1935a8978d26fd674154d581b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91333513"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Esercitazione: configurare Atlassian cloud per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire in Atlassian cloud e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in [Atlassian cloud](https://www.atlassian.com/licensing/cloud). Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in Atlassian cloud
> * Rimuovere gli utenti in Atlassian cloud quando non richiedono più l'accesso
> * Mantieni gli attributi utente sincronizzati tra Azure AD e cloud Atlassian
> * Effettuare il provisioning di gruppi e appartenenze a gruppi in Atlassian cloud
> * [Single Sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/atlassian-cloud-tutorial) per Atlassian cloud (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Un account utente in Azure AD con l'[autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale.
* [Tenant cloud Atlassian](https://www.atlassian.com/licensing/cloud)
* Un account utente in Atlassian cloud con autorizzazioni di amministratore.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Vedere le informazioni su [come funziona il servizio di provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determinare quali dati eseguire il [mapping tra Azure ad e Atlassian cloud](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-atlassian-cloud-to-support-provisioning-with-azure-ad"></a>Passaggio 2. Configurare Atlassian cloud per supportare il provisioning con Azure AD

1. Passare a [Atlassian Organization Manager](https://admin.atlassian.com) **> selezionare la directory > org**.

    ![Screenshot della pagina di amministrazione con l'opzione directory denominata.](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

2. Fare clic su **provisioning utenti** e fare clic su **Crea una directory**. Copiare l' **URL di base della directory** e il **token di porta** , che verranno immessi nei campi **URL tenant** e **token segreto** nella scheda provisioning dell'applicazione cloud Atlassian nel portale di Azure ad.

    ![Screenshot della pagina di amministrazione con l'opzione Provisioning utenti denominata. ](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Screenshot della pagina creare un token.](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png)
    ![Screenshot della pagina demo Time directory token.](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)


## <a name="step-3-add-atlassian-cloud-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere Atlassian cloud dalla raccolta di applicazioni Azure AD

Aggiungere Atlassian cloud dalla raccolta di applicazioni Azure AD per iniziare a gestire il provisioning in Atlassian cloud. Se in precedenza è stato configurato Atlassian Cloud for SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Quando si assegnano utenti e gruppi a Atlassian cloud, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Passaggio 5. Configurazione del provisioning utenti automatico in Atlassian cloud 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Atlassian cloud in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Atlassian cloud in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare **applicazioni aziendali**, selezionare **tutte le applicazioni**, quindi selezionare **Atlassian cloud**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Atlassian Cloud**.

    ![Collegamento di Atlassian Cloud nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni Gestisci con l'opzione di provisioning denominata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa modalità di provisioning con l'opzione automatica chiamata.](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere l'URL del **tenant** e il **token del segreto** recuperati in precedenza dall'account del cloud Atlassian. Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a Atlassian cloud. Se la connessione non riesce, verificare che l'account cloud Atlassian disponga delle autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory Users to Atlassian cloud**.

9. Esaminare gli attributi utente sincronizzati da Azure AD a cloud Atlassian nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Atlassian cloud per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|
   |---|---|
   |userName|string|
   |active|Boolean|
   |name.familyName|string|
   |name.givenName|string|
   |emails[type eq "work"].value|string|   

10. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to Atlassian cloud**.

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a cloud Atlassian nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Atlassian cloud per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      |Attributo|Type|
      |---|---|
      |displayName|string|
      |externalId|string|
      |Membri di|Informazioni di riferimento|

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per Atlassian cloud, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Atlassian cloud scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

16. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione.

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

1. Usare i [log di provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
2. Controllare l'[indicatore di stato](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
3. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Atlassian cloud consente il provisioning degli utenti solo da [domini verificati](https://confluence.atlassian.com/cloud/organization-administration-938859734.html).
* Atlassian cloud non supporta oggi i rinomi di gruppo. Ciò significa che tutte le modifiche apportate al displayName di un gruppo in Azure AD non verranno aggiornate e riflesse in Atlassian cloud.
* Il valore dell'attributo utente della **posta** in Azure ad viene popolato solo se l'utente dispone di una cassetta postale di Microsoft Exchange. Se l'utente non dispone di un attributo, è consigliabile eseguire il mapping di un attributo desiderato diverso all'attributo **emails** in Atlassian cloud.

## <a name="change-log"></a>Registro delle modifiche

* 06/15/2020-è stato aggiunto il supporto per la PATCH batch per i gruppi.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png