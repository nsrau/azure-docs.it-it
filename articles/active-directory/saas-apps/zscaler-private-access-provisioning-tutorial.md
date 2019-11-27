---
title: 'Esercitazione: configurare zScaler private Access (ZPA) per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in zScaler private Access (ZPA).
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: ee9128c3-ff02-4739-8c51-0693d8451742
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: c5274f3c38782394b01f8f29c56823753783307c
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74287723"
---
# <a name="tutorial-configure-zscaler-private-access-zpa-for-automatic-user-provisioning"></a>Esercitazione: configurare zScaler private Access (ZPA) per il provisioning utenti automatico

Questa esercitazione illustra i passaggi da eseguire in zScaler private Access (ZPA) e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in zScaler private Access (ZPA).

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Tenant di zScaler private Access (ZPA)](https://www.zscaler.com/pricing-and-plans#contact-us)
* Un account utente in zScaler private Access (ZPA) con autorizzazioni di amministratore.

## <a name="assigning-users-to-zscaler-private-access-zpa"></a>Assegnazione di utenti a zScaler private Access (ZPA)

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a zScaler private Access (ZPA). Dopo aver stabilito questo, è possibile assegnare questi utenti e/o gruppi a zScaler private Access (ZPA) seguendo le istruzioni riportate qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-zscaler-private-access-zpa"></a>Suggerimenti importanti per l'assegnazione di utenti a zScaler private Access (ZPA)

* È consigliabile assegnare un singolo utente Azure AD a zScaler private Access (ZPA) per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a zScaler private Access (ZPA), è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="set-up-zscaler-private-access-zpa-for-provisioning"></a>Configurare zScaler private Access (ZPA) per il provisioning

1. Accedere alla console di [amministrazione di zScaler private Access (ZPA)](https://admin.private.zscaler.com/). Passare ad **amministrazione > configurazione IDP**.

    ![Console di amministrazione di zScaler private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/idpconfig.png)

2.  Verificare che sia configurato un IdP per l' **accesso Single Sign-on** . Se non è configurato alcun provider di identità, aggiungerne uno facendo clic sull'icona con il segno più nell'angolo superiore destro della schermata.

    ![ZScaler private Access (ZPA) aggiungere SCIM](media/zscaler-private-access-provisioning-tutorial/plusicon.png)

3. Per aggiungere un IdP, seguire la procedura guidata **Aggiungi configurazione IDP** . Lasciare il campo **Single Sign-on** impostato su **User**. Specificare un **nome** e selezionare i **domini** dall'elenco a discesa. Fare clic su **Avanti per passare alla finestra successiva.**

    ![ZScaler private Access (ZPA) Add IdP](media/zscaler-private-access-provisioning-tutorial/addidp.png)

4. Scaricare il **certificato del provider di servizi**. Fare clic su **Avanti per passare alla finestra successiva.**

    ![Certificato SP zScaler private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/spcertificate.png)

5. Nella finestra successiva caricare il certificato del **provider di servizi** scaricato in precedenza.

    ![ZScaler private Access (ZPA) caricare il certificato](media/zscaler-private-access-provisioning-tutorial/uploadfile.png)

6.  Scorrere verso il basso per specificare l' **URL Single Sign-on** e l' **ID entità IDP**.

    ![ID IdP di accesso privato zScaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/idpid.png)

7.  Scorrere verso il basso per **abilitare la sincronizzazione di SCIM**. Fare clic sul pulsante **genera nuovo token** . Copiare il **token di porta**. Questo valore verrà immesso nel campo token segreto nella scheda provisioning dell'applicazione zScaler private Access (ZPA) nel portale di Azure.

    ![Creazione del token di zScaler private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/token.png)

8.  Per individuare l' **URL del tenant** , passare ad **Amministrazione > configurazione IDP**. Fare clic sul nome della configurazione IdP appena aggiunta elencata nella pagina.

    ![Nome IDP di accesso privato zScaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/idpname.png)

9.  Scorrere verso il basso per visualizzare l' **endpoint del provider di servizi scim** alla fine della pagina. Copiare l' **endpoint del provider di servizi scim**. Questo valore verrà immesso nel campo URL tenant nella scheda provisioning dell'applicazione zScaler private Access (ZPA) nel portale di Azure.

    ![URL SCIM di accesso privato zScaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/tenanturl.png)


## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>Aggiungere zScaler private Access (ZPA) dalla raccolta

Prima di configurare zScaler private Access (ZPA) per il provisioning utenti automatico con Azure AD, è necessario aggiungere zScaler private Access (ZPA) dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere zScaler private Access (ZPA) dalla raccolta di applicazioni Azure AD, seguire questa procedura:**

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **zScaler private Access (ZPA)** , selezionare **zScaler private Access (ZPA)** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Zscaler Private Access (ZPA) nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-zscaler-private-access-zpa"></a>Configurazione del provisioning utenti automatico in zScaler private Access (ZPA) 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in zScaler private Access (ZPA) in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare la Single Sign-On basata su SAML per zScaler private Access (ZPA) seguendo le istruzioni fornite nell' [esercitazione sull'accesso Single Sign-on a zScaler private Access (ZPA)](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial). Il Single Sign-on può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

> [!NOTE]
> Per altre informazioni sull'endpoint SCIM di zScaler private Access, vedere [questo](https://www.zscaler.com/partners/microsoft)articolo.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-private-access-zpa-in-azure-ad"></a>Per configurare il provisioning utenti automatico per zScaler private Access (ZPA) in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni, selezionare **Zscaler Private Access (ZPA)** .

    ![Collegamento a Zscaler Private Access (ZPA) nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere il valore dell' **endpoint del provider di servizi scim** recuperato in precedenza in **URL tenant**. Immettere il valore del **token di porta** recuperato in precedenza nel **token segreto**. Fare clic su **Test connessione** per verificare che Azure ad possibile connettersi a zScaler private Access (ZPA). Se la connessione non riesce, verificare che l'account zScaler private Access (ZPA) disponga delle autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Save**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a zScaler private Access (ZPA)** .

    ![Mapping utente di zScaler private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/usermappings.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a zScaler private Access (ZPA) nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in zScaler private Access (ZPA) per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente zScaler private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/userattributes.png)

10. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to zScaler private Access (ZPA)** .

    ![Mapping del gruppo zScaler private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/groupmappings.png)

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a zScaler private Access (ZPA) nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in zScaler private Access (ZPA) per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi del gruppo zScaler private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/groupattributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per zScaler private Access (ZPA), impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in zScaler private Access (ZPA) scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio Azure ad provisioning in zScaler private Access (ZPA).

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere [Esercitazione: creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)

