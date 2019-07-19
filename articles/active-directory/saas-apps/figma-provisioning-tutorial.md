---
title: 'Esercitazione: Configurare il provisioning utenti automatico di Figma con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Figma.
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
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 38ebba8803e584e9b5d1179281fcff3a3f98d5a4
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67848148"
---
# <a name="tutorial-configure-figma-for-automatic-user-provisioning"></a>Esercitazione: Configurare Figma per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire in Figma e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Figma.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per ulteriori informazioni sulle condizioni per l'utilizzo di Microsoft Azure generali per le funzionalità di anteprima, vedere le [condizioni per l'utilizzo supplementari per](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)le anteprime di Microsoft Azure.

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Tenant di Figma](https://www.figma.com/pricing/).
* Un account utente in Figma con autorizzazioni di amministratore.

## <a name="assign-users-to-figma"></a>Assegnare gli utenti a Figma.
Azure Active Directory usa un concetto denominato assegnazioni per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a Figma. Dopo aver stabilito questo, è possibile assegnare gli utenti e/o i gruppi a Figma seguendo le istruzioni riportate qui:
 
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)
## <a name="important-tips-for-assigning-users-to-figma"></a>Suggerimenti importanti per l'assegnazione di utenti a Figma

 * È consigliabile assegnare un singolo Azure AD utente a Figma per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Figma, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo di accesso predefinito vengono esclusi dal provisioning.

## <a name="set-up-figma-for-provisioning"></a>Configurare Figma per il provisioning

Prima di configurare Figma per il provisioning utenti automatico con Azure AD, sarà necessario recuperare alcune informazioni di provisioning da Figma.

1. Accedere a [Figma Admin Console](https://www.Figma.com/). Fare clic sull'icona a forma di ingranaggio accanto al tenant.

    ![FigmaFigma-Employee-provisioning](media/Figma-provisioning-tutorial/image0.png)

2. Passare a **generale > aggiornare le impostazioni di accesso**.

    ![FigmaFigma-Employee-provisioning](media/Figma-provisioning-tutorial/figma03.png)

3. Copiare l' **ID tenant**. Questo valore verrà usato per costruire l'URL dell'endpoint SCIM da immettere nel campo **URL tenant** nella scheda provisioning dell'applicazione Figma nel portale di Azure.

    ![Figma creare token](media/Figma-provisioning-tutorial/figma-tenantid.png)

4. Scorrere verso il basso e fare clic su **genera token API**.

    ![Figma creare token](media/Figma-provisioning-tutorial/token.png)

5. Copiare il valore del **token dell'API** . Questo valore verrà immesso nel campo **token segreto** nella scheda provisioning dell'applicazione Figma nel portale di Azure. 

    ![Figma creare token](media/Figma-provisioning-tutorial/figma04.png)

## <a name="add-figma-from-the-gallery"></a>Aggiungere Figma dalla raccolta

Per configurare Figma per il provisioning utenti automatico con Azure AD, è necessario aggiungere Figma dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Figma**, selezionare **Figma** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Figma nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-figma"></a>Configurazione del provisioning utenti automatico in Figma 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Figma in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-on basato su SAML per Figma, seguendo le istruzioni fornite nell' [esercitazione sull'accesso Single Sign-on di Figma](figma-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-figma--in-azure-ad"></a>Per configurare il provisioning utenti automatico per Figma in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni, selezionare **Figma**.

    ![Collegamento di Figma nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere `https://www.figma.com/scim/v2/<TenantID>` l'URL del **tenant** dove **TenantId** è il valore recuperato da Figma in precedenza. Immettere il valore del **token API** in **token segreto**. Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a Figma. Se la connessione non riesce, verificare che l'account Figma disponga delle autorizzazioni di amministratore e riprovare.

    ![URL tenant + token](common/provisioning-testconnection-tenanturltoken.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Posta elettronica di notifica](common/provisioning-notification-email.png)

9. Fare clic su **Save**.

10. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a Figma**.

    ![Mapping utente Figma](media/Figma-provisioning-tutorial/figma05.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD a Figma nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Figma per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di Figma](media/Figma-provisioning-tutorial/figma06.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per Figma, impostare **stato** del provisioning **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Figma selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito del provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio Azure ad provisioning su Figma.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)