---
title: 'Esercitazione: Configurare Atlassian Cloud per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Atlassian Cloud.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2019
ms.author: jeedes
ms.openlocfilehash: 691d35267c255c933a8098b99301fbb795a3cd0c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181068"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Esercitazione: Configurare Atlassian Cloud per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Atlassian Cloud e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in [Atlassian Cloud](https://www.atlassian.com/licensing/cloud). Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in Atlassian Cloud
> * Rimuovere utenti da Atlassian Cloud quando non richiedono più l'accesso
> * Mantenere gli attributi utente sincronizzati tra Azure AD e Atlassian Cloud
> * Effettuare il provisioning di gruppi e appartenenza a gruppi in Atlassian Cloud
> * [Single Sign-On](./atlassian-cloud-tutorial.md) in Atlassian Cloud (consigliato)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](../develop/quickstart-create-new-tenant.md).
* Un account utente in Azure AD con l'[autorizzazione](../roles/permissions-reference.md) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale.
* [Un tenant di Atlassian Cloud](https://www.atlassian.com/licensing/cloud)
* Un account utente in Atlassian Cloud con autorizzazioni di amministratore.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Vedere le informazioni su [come funziona il servizio di provisioning](../app-provisioning/user-provisioning.md).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determinare quali dati [mappare tra Azure AD e Atlassian Cloud](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-atlassian-cloud-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare Atlassian Cloud per supportare il provisioning con Azure AD

1. Passare a [Atlassian Organization Manager](https://admin.atlassian.com) **> selezionare l'organizzazione > Directory**.

    ![Screenshot della pagina Administration con l'opzione Directory evidenziata.](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

2. Fare clic su **User Provisioning** (Provisioning utente) e quindi su **Create a directory** (Crea una directory). Copiare i valori di **Directory base URL** (URL di base directory) e **Bearer Token** (Token di connessione) che verranno inseriti rispettivamente nei campi **Tenant URL** (URL tenant) e **Secret Token** (Token segreto) della scheda Provisioning dell'applicazione Atlassian Cloud nel portale di Azure AD.

    ![Screenshot della pagina Administration con l'opzione User provisioning evidenziata.](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Screenshot della pagina Create a token.](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png)
    ![Screenshot della pagina token demo-time-directory.](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)


## <a name="step-3-add-atlassian-cloud-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere Atlassian Cloud dalla raccolta di applicazioni di Azure AD

Aggiungere Atlassian Cloud dalla raccolta di applicazioni di Azure AD per iniziare a gestire il provisioning in Atlassian Cloud. Se Atlassian Cloud è stato configurato in precedenza per l'accesso SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quando si assegnano utenti e gruppi ad Atlassian Cloud, è necessario selezionare un ruolo diverso da **Accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](../develop/howto-add-app-roles-in-azure-ad-apps.md) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Passaggio 5. Configurazione del provisioning utenti automatico in Atlassian Cloud 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Atlassian Cloud in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Atlassian Cloud in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com), selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Atlassian Cloud**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Atlassian Cloud**.

    ![Collegamento di Atlassian Cloud nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot che mostra l'opzione puntini di sospensione per un indicatore KPI selezionata e l'opzione GESTISCI evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatico evidenziata.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere i valori di **URL tenant** e **Token segreto** recuperati in precedenza dall'account di Atlassian Cloud. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi ad Atlassian Cloud. Se la connessione non riesce, verificare che l'account Atlassian Cloud abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Atlassian Cloud** (Sincronizza utenti di Azure Active Directory in Atlassian Cloud).

9. Esaminare gli attributi utente sincronizzati da Azure AD ad Atlassian Cloud nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Atlassian Cloud per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|
   |---|---|
   |userName|string|
   |active|Boolean|
   |name.familyName|string|
   |name.givenName|string|
   |emails[type eq "work"].value|string|   

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to Atlassian Cloud** (Sincronizza gruppi di Azure Active Directory in Atlassian Cloud).

11. Esaminare gli attributi gruppo sincronizzati da Azure AD ad Atlassian Cloud nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Atlassian Cloud per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      |Attributo|Type|
      |---|---|
      |displayName|string|
      |externalId|string|
      |Membri di|Informazioni di riferimento|

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per Atlassian Cloud, impostare **Stato del provisioning** su **Attivato** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui effettuare il provisioning in Atlassian Cloud scegliendo i valori appropriati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

16. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione.

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

1. Usare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
2. Controllare l'[indicatore di stato](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
3. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Atlassian cloud consente il provisioning degli utenti solo da [domini verificati](https://confluence.atlassian.com/cloud/organization-administration-938859734.html).
* Atlassian Cloud non supporta attualmente la ridenominazione dei gruppi. Questo significa che eventuali modifiche all'attributo displayName di un gruppo in Azure AD non verranno aggiornate e applicate in Atlassian Cloud.
* Il valore dell'attributo utente **mail** in Azure AD viene popolato solo se l'utente ha una cassetta postale di Microsoft Exchange. Se l'utente non ne ha una, è consigliabile eseguire il mapping di un attributo diverso all'attributo **emails** in Atlassian Cloud.

## <a name="change-log"></a>Registro delle modifiche

* 15/06/2020: -Aggiunta del supporto per la PATCH batch per i gruppi.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png