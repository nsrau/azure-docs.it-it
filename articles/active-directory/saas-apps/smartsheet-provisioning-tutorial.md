---
title: 'Esercitazione: configurare Smartsheet per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Smartsheet.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: 699eb37176d6737744fb0ba01f9f3f4a2d4e55b1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85318748"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Esercitazione: configurare Smartsheet per il provisioning utenti automatico

Questa esercitazione illustra i passaggi da eseguire in Smartsheet e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in [Smartsheet](https://www.smartsheet.com/pricing). Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in Smartsheet
> * Rimuovere gli utenti in Smartsheet quando non richiedono più l'accesso
> * Mantieni gli attributi utente sincronizzati tra Azure AD e Smartsheet
> * Single Sign-on per Smartsheet (scelta consigliata)

> [!NOTE]
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Tenant Azure ad](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Un account utente in Azure AD con l'[autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale.
* [Tenant Smartsheet](https://www.smartsheet.com/pricing).
* Un account utente in un piano Smartsheet Enterprise o Enterprise Premier con autorizzazioni di amministratore di sistema.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Vedere le informazioni su [come funziona il servizio di provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determinare quali dati eseguire il [mapping tra Azure ad e Smartsheet](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-smartsheet-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare Smartsheet per supportare il provisioning con Azure AD

Prima di configurare Smartsheet per il provisioning utenti automatico con Azure AD, sarà necessario abilitare il provisioning di SCIM su Smartsheet.

1. Accedere come **sysadmin** nel **[portale Smartsheet](https://app.smartsheet.com/b/home)** e passare a **account admin**.

    ![Amministratore account Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Passare a **controlli di sicurezza > provisioning automatico dell'utente > modifica**.

    ![Controlli di sicurezza Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Aggiungere e convalidare i domini di posta elettronica per gli utenti di cui si intende eseguire il provisioning da Azure AD a Smartsheet. Scegliere **non abilitato** per assicurarsi che tutte le azioni di provisioning abbiano origine solo da Azure ad e per assicurarsi che l'elenco di utenti Smartsheet sia sincronizzato con Azure ad assegnazioni.

    ![Provisioning utenti Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Al termine della convalida, sarà necessario attivare il dominio. 

    ![Attiva dominio Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Generare il **token segreto** necessario per configurare il provisioning utenti automatico con Azure ad passando ad **app e integrazioni**.

    ![Installazione di Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Scegliere **accesso all'API**. Fare clic su **genera nuovo token di accesso**.

    ![Installazione di Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Definire il nome del token di accesso all'API. Fare clic su **OK**.

    ![Installazione di Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Copiare il token di accesso all'API e salvarlo perché questo sarà l'unico momento in cui è possibile visualizzarlo. Questa operazione è necessaria nel campo **token segreto** in Azure ad.

    ![Token Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="step-3-add-smartsheet-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere Smartsheet dalla raccolta di applicazioni Azure AD

Aggiungere Smartsheet dalla raccolta di applicazioni Azure AD per iniziare a gestire il provisioning in Smartsheet. Se in precedenza è stato configurato Smartsheet per SSO, è possibile utilizzare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Quando si assegnano utenti e gruppi a Smartsheet, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli. 

* Per garantire la parità nelle assegnazioni di ruolo utente tra Smartsheet e Azure AD, è consigliabile usare le stesse assegnazioni di ruolo compilate nell'elenco completo degli utenti Smartsheet. Per recuperare l'elenco di utenti da Smartsheet, passare a **amministratore Account > gestione utenti > altre azioni > Scarica elenco utenti (CSV)**.

* Per accedere a determinate funzionalità nell'app, Smartsheet richiede che un utente disponga di più ruoli. Per altre informazioni sui tipi di utente e le autorizzazioni in Smartsheet, vedere [tipi di utente e autorizzazioni](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Se un utente dispone di più ruoli assegnati in Smartsheet, è **necessario** assicurarsi che queste assegnazioni di ruolo vengano replicate in Azure ad per evitare uno scenario in cui gli utenti potrebbero perdere l'accesso agli oggetti Smartsheet in modo permanente. Ogni ruolo univoco in Smartsheet **deve** essere assegnato a un altro gruppo in Azure ad. L'utente **deve** quindi essere aggiunto a ognuno dei gruppi corrispondenti ai ruoli desiderati. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

## <a name="step-5-configure-automatic-user-provisioning-to-smartsheet"></a>Passaggio 5. Configurare il provisioning utenti automatico in Smartsheet 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Smartsheet in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Smartsheet in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Smartsheet**.

    ![Collegamento di Smartsheet nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere l'URL di **base scim 2,0 e** i valori dei token di accesso recuperati in precedenza da Smartsheet rispettivamente in **URL tenant** e **token segreto** . Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a Smartsheet. Se la connessione non riesce, verificare che l'account Smartsheet disponga delle autorizzazioni SysAdmin e riprovare.

    ![token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a Smartsheet**.

9. Esaminare gli attributi utente sincronizzati da Azure AD a Smartsheet nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Smartsheet per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|
   |---|---|
   |active|Boolean|
   |title|string|
   |userName|string|
   |name.givenName|string|
   |name.familyName|string|
   |phoneNumbers[type eq "work"].value|string|
   |phoneNumbers[type eq "mobile"].value|string|
   |phoneNumbers[type eq "fax"].value|string|
   |externalId|string|
   |ruoli [Primary EQ "true"]. display|string|
   |ruoli [Primary EQ "true"]. Type|string|
   |ruoli [Primary EQ "true"]. Value|string|
   |roles|string|
   urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|string|


10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning Azure AD per Smartsheet, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Smartsheet scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

1. Usare i [log di provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
2. Controllare l'[indicatore di stato](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
3. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Smartsheet non supporta le eliminazioni automatiche. Quando l'attributo **attivo** di un utente è impostato su false, Smartsheet Elimina l'utente in modo permanente.

## <a name="change-log"></a>Registro delle modifiche

* 06/16/2020-è stato aggiunto il supporto per gli attributi di estensione Enterprise "centro di costo", "divisione", "responsabile" e "reparto" per gli utenti.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
