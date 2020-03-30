---
title: "Esercitazione: Configurare l'accesso privato di scalare per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft"
description: Informazioni su come configurare Azure Active Directory per il provisioning e l'annullamento del provisioning automatico degli account utente in accesso privato con scalare .
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
ms.openlocfilehash: 609d2726eaaaeb49210e19f000bcc2faef1de5d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064156"
---
# <a name="tutorial-configure-zscaler-private-access-zpa-for-automatic-user-provisioning"></a>Esercitazione: Configurare l'accesso privato per il provisioning automatico degli utenti

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Accesso privato di Scalar e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e/o gruppi per l'accesso privato di scalar.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant di accesso privato di scalar (PA)](https://www.zscaler.com/pricing-and-plans#contact-us)
* Un account utente in accesso privato con autorizzazioni di amministratore.

## <a name="assigning-users-to-zscaler-private-access-zpa"></a>Assegnazione di utenti all'accesso privato di scalante

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere all'accesso privato di .scaler. Una volta deciso, è possibile assegnare questi utenti e/o gruppi all'accesso privato di scalante (PA) seguendo le istruzioni riportate di seguito:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-zscaler-private-access-zpa"></a>Suggerimenti importanti per l'assegnazione di utenti all'accesso privato di .scaler

* Per testare la configurazione automatica del provisioning degli utenti, è consigliabile assegnare un singolo utente di Azure AD all'accesso privato di scalante. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente all'accesso privato di scalare, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="set-up-zscaler-private-access-zpa-for-provisioning"></a>Impostare l'accesso privato di scaler per il provisioning

1. Accedere all'Admin Console di accesso privato con accesso privato con [estensione a zscaler](https://admin.private.zscaler.com/). Passare ad **Amministrazione > Configurazione IdP**.

    ![Console di amministrazione per l'accesso privato a zscaler](media/zscaler-private-access-provisioning-tutorial/idpconfig.png)

2.  Verificare che sia configurato un IdP per **Single Sign-On.** Se non è configurato alcun IdP, aggiungerne uno facendo clic sull'icona più nell'angolo in alto a destra dello schermo.

    ![Accesso privato di scaler (PA) Aggiungi SCIM](media/zscaler-private-access-provisioning-tutorial/plusicon.png)

3. Seguire la procedura guidata Aggiungi configurazione IdP per aggiungere un IdP.Follow through the **Add IdP Configuration** wizard to add an IdP. Lasciare il campo **Single Sign-On** impostato su **Utente**. Specificare un **Nome** e selezionare **Domini** dall'elenco a discesa. Fare clic su **Avanti** per passare alla finestra successiva.

    ![Aggiunta di un id-token per l'accesso privato a livello di entità](media/zscaler-private-access-provisioning-tutorial/addidp.png)

4. Scaricare il certificato del **provider di servizi**. Fare clic su **Avanti** per passare alla finestra successiva.

    ![Certificato SP per l'accesso privato di scalare (PA)](media/zscaler-private-access-provisioning-tutorial/spcertificate.png)

5. Nella finestra successiva, caricare il certificato del **provider** di servizi scaricato in precedenza.

    ![Certificato di caricamento per l'accesso privato di scalare (PA)](media/zscaler-private-access-provisioning-tutorial/uploadfile.png)

6.  Scorrere verso il basso per specificare **l'URL Single Sign-On** e **l'ID entità IdP.**

    ![ID IdP di accesso privato di scalare (PA)](media/zscaler-private-access-provisioning-tutorial/idpid.png)

7.  Scorrere verso il basso fino a **Abilita sincronizzazione SCIM**. Fare clic sul **pulsante Genera nuovo token.** Copiare il **gettone del portatore**. Questo valore verrà immesso nel campo Token segreto nella scheda Provisioning dell'applicazione di accesso privato con scalare nel portale di Azure.

    ![Accesso privato di scalare (PA) Crea token](media/zscaler-private-access-provisioning-tutorial/token.png)

8.  Per individuare **l'URL tenant** , passare ad Amministrazione > Configurazione **IdP**. Fare clic sul nome della configurazione IdP appena aggiunta elencata nella pagina.

    ![Nome Idp di accesso privato di scalare (PA)](media/zscaler-private-access-provisioning-tutorial/idpname.png)

9.  Scorrere verso il basso per visualizzare l'endpoint del provider di servizi **SCIM** alla fine della pagina. Copiare l'endpoint del provider di **servizi SCIM**. Questo valore verrà immesso nel campo URL tenant nella scheda Provisioning dell'applicazione di accesso privato con scalare (PA) nel portale di Azure.

    ![URL SCIM di accesso privato con accesso privato a scalare](media/zscaler-private-access-provisioning-tutorial/tenanturl.png)


## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>Aggiunta dell'accesso privato alla galleria

Prima di configurare l'accesso privato di scalar per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere l'accesso privato di scalar dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere l'accesso privato di .scaler dalla raccolta di applicazioni di Azure AD, eseguire la procedura seguente:To add sscaler Private Access ('pa') from the Azure AD application gallery, perform the following steps:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, immettere l'accesso privato a **scalare (PA),** selezionare accesso **privato a scalare (PA),** quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Zscaler Private Access (ZPA) nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-zscaler-private-access-zpa"></a>Configurazione del provisioning automatico degli utenti per l'accesso privato a .A 

In questa sezione vengono illustrati i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi nell'accesso privato di scalar in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per l'accesso privato di scalar (PA) seguendo le istruzioni fornite [nell'esercitazione Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial)di accesso privato di .PA. Single Sign-On può essere configurato indipendentemente dal provisioning automatico degli utenti, anche se queste due funzionalità si completano a vicenda.

> [!NOTE]
> Per ulteriori informazioni sull'endpoint SCIM di Private Access , fare riferimento a [questa opzione.](https://www.zscaler.com/partners/microsoft)

### <a name="to-configure-automatic-user-provisioning-for-zscaler-private-access-zpa-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per l'accesso privato di scalar in Azure AD:To configure automatic user provisioning for 'scaler Private Access' s'PA) in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni, selezionare **Zscaler Private Access (ZPA)**.

    ![Collegamento a Zscaler Private Access (ZPA) nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere il valore **SCIM Service Provider Endpoint** recuperato in precedenza in URL **tenant**. Immettere il valore **del gettone bearer** recuperato in precedenza in **Secret Token**. Fare clic su **Test connessione** per verificare che Azure AD sia in grado di connettersi all'accesso privato di scalante. Se la connessione non riesce, verificare che l'account di accesso privato con estensione a discalar disponga delle autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare Sincronizza gli utenti di **Azure Active Directory con l'accesso privato con scalare (PA)**.

    ![Mapping di utenti con accesso privato a scalaro (PA)](media/zscaler-private-access-provisioning-tutorial/usermappings.png)

9. Esaminare gli attributi dell'utente che vengono sincronizzati da Azure AD all'accesso privato di scalare nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per la corrispondenza con gli account utente in .NET per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di accesso privato di .scaler (PA)](media/zscaler-private-access-provisioning-tutorial/userattributes.png)

10. Nella sezione **Mapping** selezionare Sincronizza gruppi di **Azure Active Directory con l'accesso privato con scalare (PA)**.

    ![Mappature di gruppi di accesso privato di scalar (PA)](media/zscaler-private-access-provisioning-tutorial/groupmappings.png)

11. Esaminare gli attributi di gruppo che vengono sincronizzati da Azure AD a .scaler Private Access (PA) nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per trovare la corrispondenza con i gruppi in Accesso privato di scalar per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi del gruppo di accesso privato di .scaler (PA)](media/zscaler-private-access-provisioning-tutorial/groupattributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per l'accesso privato di Scalar, modificare **lo stato** di provisioning **su Attivato** nella sezione **Impostazioni.**

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in .NET, scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Access privato di .scaler.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

