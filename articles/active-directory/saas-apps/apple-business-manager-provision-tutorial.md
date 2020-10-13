---
title: 'Esercitazione: configurare Apple Business Manager per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD ad Apple Business Manager.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 4ad30031-9904-4ac3-a4d2-e8c28d44f319
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2020
ms.author: Zhchia
ms.openlocfilehash: ff017671496816f0142bd3b71c3df1a3769b274b
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2020
ms.locfileid: "91941093"
---
# <a name="tutorial-configure-apple-business-manager-for-automatic-user-provisioning"></a>Esercitazione: configurare Apple Business Manager per il provisioning utenti automatico



Questa esercitazione descrive i passaggi da eseguire sia in Apple Business Manager che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Se configurato, Azure AD effettua automaticamente il provisioning e il deprovisioning degli utenti ad [Apple Business Manager](https://business.apple.com/) usando il servizio di provisioning Azure ad. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md). 

## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in Apple Business Manager
> * Rimuovere gli utenti in Apple Business Manager quando non sono più necessari per l'accesso
> * Mantieni gli attributi utente sincronizzati tra Azure AD e Apple Business Manager

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Un account utente in Azure AD con l'[autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale.
* Un account Apple Business Manager con ruolo di amministratore o responsabile delle persone.

> [!NOTE]
> Il trasferimento di token a Azure AD e stabilire una connessione riuscita deve essere completato entro quattro giorni di calendario oppure il processo deve essere riavviato.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Vedere le informazioni su [come funziona il servizio di provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determinare quali dati eseguire il [mapping tra Azure ad e Apple Business Manager](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-apple-business-manager-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare Apple Business Manager per supportare il provisioning con Azure AD

1. In Apple Business Manager, accedere con un account con il ruolo di amministratore o People Manager.
2. Fare clic su impostazioni nella parte inferiore della barra laterale fare clic su origine dati sotto Impostazioni organizzazione, quindi fare clic su Connetti a origine dati.
3. Fare clic su Connetti accanto a SCIM, leggere attentamente l'avviso, fare clic su copia e quindi su Chiudi.
[La finestra Connetti a SCIM, che fornisce un token e un pulsante copia sotto di essa.] Lasciare aperta questa finestra per copiare l'URL del tenant da Apple Business Manager in Azure AD, ovvero:' https://federation.apple.com/feeds/business/scim '

    ![Apple Business Manager](media/applebusinessmanager-provisioning-tutorial/scim-token.png)

> [!NOTE]
> Il token Secret non deve essere condiviso con altri utenti Azure AD amministratore.

## <a name="step-3-add-apple-business-manager-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere Apple Business Manager dalla raccolta di applicazioni Azure AD

Aggiungere Apple Business Manager dalla raccolta di applicazioni Azure AD per iniziare a gestire il provisioning in Apple Business Manager. Se in precedenza è stato configurato Apple Business Manager per SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Quando si assegnano utenti ad Apple Business Manager, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

## <a name="step-5-configure-automatic-user-provisioning-to-apple-business-manager"></a>Passaggio 5. Configurare il provisioning utenti automatico in Apple Business Manager

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Apple Business Manager**.

    ![Apple Business Manager nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning automatica](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere l'URL di **base scim 2,0 e** i valori dei token di accesso recuperati rispettivamente da Apple Business Manager nell' **URL tenant** e nel **token segreto** . Fare clic su **Test connessione** per verificare che Azure ad possibile connettersi ad Apple Business Manager. Se la connessione non riesce, verificare che l'account Apple Business Manager disponga delle autorizzazioni di amministratore e riprovare.

    ![token](common/provisioning-testconnection-tenanturltoken.png)

> [!NOTE]
>Se la connessione ha esito positivo, Apple Business Manager Mostra la connessione SCIM come attiva. Questo processo può richiedere fino a 60 secondi affinché Apple Business Manager rifletta lo stato di connessione più recente.

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Save**.

8. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory Users to Apple Business Manager**.

9. Esaminare gli attributi utente sincronizzati da Azure AD ad Apple Business Manager nella sezione **mapping attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Apple Business Manager per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|
   |---|---|
   |active|Boolean|
   |userName|string|
   |name.givenName|string|
   |name.familyName|string|
   |name.givenName|string|
   |externalId|string|
   |locale|string|
   |timezone|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|string|

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning Azure AD per Apple Business Manager, impostare **stato del provisioning** **su** attivato nella sezione Impostazioni.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Apple Business Manager scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione.

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

1. Usare i [log di provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
2. Controllare l'[indicatore di stato](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
3. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
* [Esaminare i requisiti di SCIM per Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apdd88331cd6)
* [Modalità di utilizzo di un ID persona in Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apd69e1e48e9)
* [Usare SCIM per importare utenti in Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apd3ec7b95ad)
* [Risolvere i conflitti di account utente di SCIM in Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apd313013d12)
* [Eliminare gli account di Azure AD visualizzati in Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apdaa5798fbe)
* [Visualizzare l'attività SCIM in Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apd1bfd8dfde)
* [Gestire le connessioni e il token SCIM esistenti in Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apdc9a823611)
* [Disconnettere la connessione SCIM in Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apd609be3a61)
* [Gestire le connessioni e il token SCIM esistenti in Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apdc9a8236e9)
* [Risoluzione dei problemi relativi alla connessione SCIM in Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apd403a0f3bd/web)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)

