---
title: 'Esercitazione: configurare un contenuto per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a con contenuto.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 3b761984-a9a0-4519-b23e-563438978de5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2020
ms.author: Zhchia
ms.openlocfilehash: a473ecc21a5f8a0eb427f01ffba785732f0151e9
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181051"
---
# <a name="tutorial-configure-contentful-for-automatic-user-provisioning"></a>Esercitazione: configurare un contenuto per il provisioning utenti automatico

In questa esercitazione vengono descritti i passaggi da eseguire sia in contentful che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Quando è configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi con il [contenuto](https://www.contentful.com/) con il servizio di provisioning Azure ad. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti con contenuto
> * Rimuovere gli utenti con contenuto quando non richiedono più l'accesso
> * Mantieni gli attributi utente sincronizzati tra Azure AD e con contenuto
> * Effettuare il provisioning di gruppi e appartenenze a gruppi in contentful
> * [Single Sign-on](./contentful-tutorial.md) a contentful (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un account utente in Azure AD con l'[autorizzazione](../roles/permissions-reference.md) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale. 
* Un account dell'organizzazione con contenuto con una sottoscrizione che supporta il provisioning di SCIM. Per [support@contentful.com](mailto:support@contentful.com) domande sulla sottoscrizione dell'organizzazione, contattare l'utente con contenuto.
 
## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Vedere le informazioni su [come funziona il servizio di provisioning](../app-provisioning/user-provisioning.md).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determinare quali dati eseguire il [mapping tra Azure ad e con contenuto](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-contentful-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare il contenuto per il supporto del provisioning con Azure AD

1. Creare un account **utente del servizio** in con contenuto. Tutte le autorizzazioni di provisioning per Azure verranno fornite tramite questo account. È consigliabile scegliere **proprietario** come ruolo di organizzazione per questo account.

2. Accedere a contentful come utente del **servizio** creato nel passaggio precedente.

3. Passare al **dispositivo di scorrimento sinistro**  ->  **Impostazioni organizzazione**  ->  **strumenti di accesso**  ->  **provisioning utenti**.

    ![Menu](media/contentful-provisioning-tutorial/access.png)

4. Copiare e salvare l' **URL scim**. Questo valore verrà immesso nella scheda provisioning dell'applicazione con contenuto nell'portale di Azure.

5. Fare clic su **genera il token di accesso personale**.

    ![url](media/contentful-provisioning-tutorial/generate.png)

6. Nella finestra modale assegnare un nome significativo al token di accesso personale e fare clic su "genera".
    
7. Verranno generati l' **URL scim** e il **token Secret** . Copiare e salvare questi valori. Questi valori verranno immessi nella scheda provisioning dell'applicazione con contenuto nell'portale di Azure.

    ![access](media/contentful-provisioning-tutorial/token.png)


Rivolgersi a [support@contentful.com](mailto:support@contentful.com) se si hanno domande quando si configura il provisioning nella console di amministrazione con contenuto.


## <a name="step-3-add-contentful-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere il contenuto dalla raccolta di applicazioni di Azure AD

Aggiungere contentful dalla raccolta di applicazioni di Azure AD per iniziare a gestire il provisioning in contenuto. Se in precedenza è stato configurato il contenuto per SSO, è possibile utilizzare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quando si assegnano utenti e gruppi a contentful, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](../develop/howto-add-app-roles-in-azure-ad-apps.md) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-contentful"></a>Passaggio 5. Configurare il provisioning utenti automatico in contenuto 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-contentful-in-azure-ad"></a>Per configurare il provisioning utenti automatico per il contenuto nella Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Contentful**.

    ![Collegamento con contenuto nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Automatico nella scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere l'URL del tenant con contenuto e il token segreto. Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi al contenuto. Se la connessione non riesce, verificare che l'account con contenuto abbia le autorizzazioni di amministratore e riprovare.

    ![token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti con il contenuto**.

9. Esaminare gli attributi utente sincronizzati da Azure AD a contentful nella sezione **attribute-mapping** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in contenuto per le operazioni di aggiornamento. Se si sceglie di modificare l' [attributo di destinazione corrispondente](../app-provisioning/customize-application-attributes.md), sarà necessario assicurarsi che l'API contentful supporti il filtraggio degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|Supportato per il filtro|
   |---|---|---|
   |userName|string|&check;|
   |name.givenName|string|
   |name.familyName|string|

10. Nella sezione **mapping** selezionare **sincronizza gruppi di Azure Active Directory con contenuto**.

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a contentful nella sezione **attribute-mapping** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in contenuto per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      |Attributo|Type|Supportato per il filtro|
      |---|---|---|
      |displayName|string|&check;|
      |Membri di|Informazioni di riferimento|

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per il contenuto, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning nel contenuto scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

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