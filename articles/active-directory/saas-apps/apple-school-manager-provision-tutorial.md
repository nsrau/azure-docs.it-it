---
title: 'Esercitazione: Configurare Apple School Manager per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD ad Apple School Manager.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: f006c177-7b35-4af1-84f2-db4a4e2bf96a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/08/2020
ms.author: Zhchia
ms.openlocfilehash: 00e3b40b0e3f4c799c54308b35ce3e810cde118c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181090"
---
# <a name="tutorial-configure-apple-school-manager-for-automatic-user-provisioning"></a>Esercitazione: Configurare Apple School Manager per il provisioning utenti automatico



Questa esercitazione descrive le procedure da eseguire sia in Apple School Manager che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Una volta configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi per [Apple School Manager](https://school.apple.com/) usando il servizio di provisioning di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in Apple School Manager
> * Rimuovere utenti da Apple School Manager quando non richiedono più l'accesso
> * Mantenere gli attributi utente sincronizzati tra Azure AD e Apple School Manager

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un account utente in Azure AD con l'[autorizzazione](../roles/permissions-reference.md) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale. 
* Un account Apple School Manager con il ruolo di amministratore, responsabile del sito o responsabile delle persone.

> [!NOTE]
> È necessario completare il trasferimento di token ad Azure AD e la creazione di una connessione in quattro giorni di calendario; in caso contrario, il processo deve essere avviato di nuovo.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Vedere le informazioni su [come funziona il servizio di provisioning](../app-provisioning/user-provisioning.md).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determinare quali dati [mappare tra Azure AD e Apple School Manager](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-apple-school-manager-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare Apple School Manager per supportare il provisioning con Azure AD

1. In Apple School Manager accedere con un account con il ruolo di amministratore, responsabile del sito o responsabile delle persone.
2. Fare clic su Settings (Impostazioni), quindi nella parte inferiore della barra laterale fare clic su Data Source (Origine dati) in Organization Settings (Impostazioni organizzazione) e infine fare clic su Connect to Data Source (Connetti a origine dati).
3. Fare clic su Connect (Connetti) accanto a SCIM, leggere attentamente l'avviso, fare clic su Copy (Copia) e quindi su Close (Chiudi).
[La finestra Connect to SCIM, con un token e un pulsante Copy al di sotto.] Lasciare aperta questa finestra per copiare l'URL del tenant da Apple Business Manager in Azure AD, ovvero 'https://federation.apple.com/feeds/school/scim '

    ![Apple School Manager](media/appleschoolmanager-provisioning-tutorial/scim-token.png)

> [!NOTE]
> Il token segreto non deve essere condiviso con chiunque altro che non sia l'amministratore di Azure AD.

## <a name="step-3-add-apple-school-manager-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere Apple School Manager dalla raccolta di applicazioni di Azure AD

Aggiungere Apple School Manager dalla raccolta di applicazioni di Azure AD per iniziare a gestire il provisioning in Apple School Manager. Se Apple School Manager è stato configurato in precedenza per l'accesso SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quando si assegnano utenti e gruppi ad Apple School Manager, è necessario selezionare un ruolo diverso da **Accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](../develop/howto-add-app-roles-in-azure-ad-apps.md) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configure-automatic-user-provisioning-to-apple-school-manager"></a>Passaggio 5. Configurare il provisioning utenti automatico in Apple School Manager

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Apple School Manager**.

    ![Apple School Manager nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Automatico nella scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere i valori di **URL di base di SCIM 2.0 e token di accesso** recuperati da Apple School Manager rispettivamente in **URL tenant** e **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi ad Apple School Manager. Se la connessione non riesce, verificare che l'account Apple School Manager abbia autorizzazioni di amministratore e riprovare.

    ![token](common/provisioning-testconnection-tenanturltoken.png)

> [!NOTE]
>Se la connessione riesce, Apple School Manager mostra la connessione SCIM come attiva. Questo processo può richiedere fino a 60 secondi prima che Apple School Manager visualizzi lo stato di connessione più recente.

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Apple School Manager** (Sincronizza utenti di Azure Active Directory con Apple School Manager).

9. Esaminare gli attributi utente sincronizzati tra Azure AD e Apple School Manager nella sezione **Mapping di attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Apple School Manager per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

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

11. Per abilitare il servizio di provisioning di Azure AD per Apple School Manager, impostare **Stato del provisioning** su **Sì** nella sezione Impostazioni.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui effettuare il provisioning in Apple Business Manager selezionando i valori appropriati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione.

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione

Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

1. Usare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
2. Controllare l'[indicatore di stato](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
3. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
* [Esaminare i requisiti di SCIM per Apple School Manager](https://support.apple.com/guide/apple-school-manager/apdd88331cd6)
* [Come viene usato un ID persona in Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd69e1e48e9)
* [Usare SCIM per importare gli utenti in Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd3ec7b95ad)
* [Risolvere i conflitti degli account utente SCIM in Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd313013d12)
* [Eliminare gli account Azure AD visualizzati in Apple School Manager](https://support.apple.com/guide/apple-school-manager/apdaa5798fbe)
* [Visualizzare l'attività SCIM in Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd1bfd8dfde)
* [Gestire le connessioni e il token SCIM esistenti in Apple School Manager](https://support.apple.com/guide/apple-school-manager/apdc9a823611)
* [Disconnettere la connessione SCIM in Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd609be3a61)
* [Risolvere i problemi della connessione SCIM in Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd403a0f3bd)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)