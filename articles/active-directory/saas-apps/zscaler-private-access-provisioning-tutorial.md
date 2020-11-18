---
title: 'Esercitazione: Configurare Zscaler Private Access (ZPA) per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Zscaler Private Access (ZPA).
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: a93e2a88201f32ed99698f2bfbab631c81ed8b35
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357709"
---
# <a name="tutorial-configure-zscaler-private-access-zpa-for-automatic-user-provisioning"></a>Esercitazione: Configurare Zscaler Private Access (ZPA) per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Zscaler Private Access (ZPA) e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Zscaler Private Access (ZPA).

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant di Zscaler Private Access (ZPA)](https://www.zscaler.com/pricing-and-plans#contact-us)
* Un account utente in Zscaler Private Access (ZPA) con autorizzazioni di amministratore.

## <a name="assigning-users-to-zscaler-private-access-zpa"></a>Assegnazione di utenti a Zscaler Private Access (ZPA)

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning utenti automatico vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a Zscaler Private Access (ZPA). Dopo aver definito questo aspetto, è possibile assegnare gli utenti e/o i gruppi a Zscaler Private Access (ZPA) seguendo le istruzioni fornite qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-zscaler-private-access-zpa"></a>Suggerimenti importanti per l'assegnazione di utenti a Zscaler Private Access (ZPA)

* È consigliabile assegnare un singolo utente di Azure AD a Zscaler Private Access per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Zscaler Private Access (ZPA), è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="set-up-zscaler-private-access-zpa-for-provisioning"></a>Configurare Zscaler Private Access (ZPA) per il provisioning

1. Accedere alla [console di amministrazione di Zscaler Private Access (ZPA)](https://admin.private.zscaler.com/). Passare a **Administration > IdP Configuration** (Amministrazione > Configurazione IdP).

    ![Console di amministrazione di Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/idpconfig.png)

2.  Verificare che sia configurato un provider di identità per **Single Sign-On**. Se non è configurato alcun provider di identità, aggiungerne uno facendo clic sull'icona con il segno più nell'angolo in alto a destra dello schermo.

    ![Zscaler Private Access (ZPA) - Aggiungere SCIM](media/zscaler-private-access-provisioning-tutorial/plusicon.png)

3. Per aggiungere un provider di identità, seguire la procedura guidata **Add IdP Configuration** (Aggiunta della configurazione IdP). Lasciare il campo **Single Sign-On** impostato su **User** (Utente). Specificare un valore per **Name** (Nome) ed effettuare una selezione nell'elenco a discesa **Domains** (Domini). Fare clic su **Avanti** per passare alla finestra successiva.

    ![Zscaler Private Access (ZPA) - Aggiungere il provider di identità](media/zscaler-private-access-provisioning-tutorial/addidp.png)

4. Scaricare il **certificato del provider di servizi**. Fare clic su **Avanti** per passare alla finestra successiva.

    ![Zscaler Private Access (ZPA) - Certificato del provider di servizi](media/zscaler-private-access-provisioning-tutorial/spcertificate.png)

5. Nella finestra successiva caricare il **certificato del provider di servizi** scaricato in precedenza.

    ![Zscaler Private Access (ZPA) - Caricare il certificato](media/zscaler-private-access-provisioning-tutorial/uploadfile.png)

6.  Scorrere verso il basso per specificare i valori per **Single sign-On URL** (URL Single Sign-On) e **IdP Entity ID** (ID entità IdP).

    ![Zscaler Private Access (ZPA) - ID IdP](media/zscaler-private-access-provisioning-tutorial/idpid.png)

7.  Scorrere verso il basso fino a **Enable SCIM Sync** (Abilita sincronizzazione SCIM). Fare clic sul pulsante **Generate New Token** (Genera nuovo token). Copiare il **token di connessione**. Questo valore verrà immesso nel campo Token segreto nella scheda Provisioning dell'applicazione Zscaler Private Access (ZPA) nel portale di Azure.

    ![Zscaler Private Access (ZPA) - Creare il token](media/zscaler-private-access-provisioning-tutorial/token.png)

8.  Per individuare l'**URL tenant** passare a **Administration > IdP Configuration** (Amministrazione > Configurazione IdP). Fare clic sul nome della configurazione IdP appena aggiunta visualizzata nella pagina.

    ![Zscaler Private Access (ZPA) - Nome IdP](media/zscaler-private-access-provisioning-tutorial/idpname.png)

9.  Scorrere verso il basso per visualizzare **SCIM Service Provider Endpoint** (Endpoint del provider di servizi SCIM) in fondo alla pagina. Copiare il valore di **SCIP Service Provider Endpoint**. Questo valore verrà immesso nel campo URL tenant nella scheda Provisioning dell'applicazione Zscaler Private Access (ZPA) nel portale di Azure.

    ![Zscaler Private Access (ZPA) - URL SCIM](media/zscaler-private-access-provisioning-tutorial/tenanturl.png)


## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>Aggiungere Zscaler Private Access (ZPA) dalla raccolta

Prima di configurare Zscaler Private Access (ZPA) per il provisioning utenti automatico con Azure AD, è necessario aggiungere Zscaler Private Access (ZPA) dalla raccolta di applicazioni di Azure AD all'elenco di applicazioni SaaS gestite.

**Per aggiungere Zscaler Private Access (ZPA) dalla raccolta di applicazioni di Azure AD, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Zscaler Private Access (ZPA)** , selezionare **Zscaler Private Access (ZPA)** nel pannello dei risultati, quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Zscaler Private Access (ZPA) nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-zscaler-private-access-zpa"></a>Configurare il provisioning utenti automatico in Zscaler Private Access (ZPA) 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Zscaler Private Access (ZPA) in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Zscaler Private Access (ZPA), seguendo le istruzioni fornite nell'[esercitazione sull'accesso Single Sign-On per Zscaler Private Access (ZPA)](./zscalerprivateaccess-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

> [!NOTE]
> Per altre informazioni sull'endpoint SCIM di Zscaler Private Access, vedere [questo articolo](https://www.zscaler.com/partners/microsoft).

### <a name="to-configure-automatic-user-provisioning-for-zscaler-private-access-zpa-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Zscaler Private Access (ZPA) in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni, selezionare **Zscaler Private Access (ZPA)** .

    ![Collegamento a Zscaler Private Access (ZPA) nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni di gestione con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatico evidenziata.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere il valore di **SCIM Service Provider Endpoint** recuperato in precedenza in **URL tenant**. Immettere il valore di **Bearer Token** recuperato in precedenza in **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Zscaler Private Access (ZPA). Se la connessione non riesce, verificare che l'account Zscaler Private Access (ZPA) abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Zscaler Private Access (ZPA)** (Sincronizza utenti di Azure Active Directory con Zscaler Private Access (ZPA)).

    ![Zscaler Private Access (ZPA) - Mapping degli utenti](media/zscaler-private-access-provisioning-tutorial/usermappings.png)

9. Esaminare gli attributi utente sincronizzati tra Azure AD e Zscaler Private Access (ZPA) nella sezione **Mapping di attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Zscaler Private Access (ZPA) per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Zscaler Private Access (ZPA) - Attributi utente](media/zscaler-private-access-provisioning-tutorial/userattributes.png)

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to Zscaler Private Access (ZPA)** (Sincronizza gruppi di Azure Active Directory con Zscaler Private Access (ZPA)).

    ![Zscaler Private Access (ZPA) - Mapping dei gruppi](media/zscaler-private-access-provisioning-tutorial/groupmappings.png)

11. Esaminare gli attributi gruppo sincronizzati tra Azure AD e Zscaler Private Access (ZPA) nella sezione **Mapping di attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Zscaler Private Access (ZPA) per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Zscaler Private Access (ZPA) - Attributi gruppo](media/zscaler-private-access-provisioning-tutorial/groupattributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per Zscaler Private Access (ZPA), impostare **Stato del provisioning** su **Attivato** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui effettuare il provisioning in Zscaler Private Access (ZPA) scegliendo i valori appropriati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning di Azure AD in Zscaler Private Access (ZPA).

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)