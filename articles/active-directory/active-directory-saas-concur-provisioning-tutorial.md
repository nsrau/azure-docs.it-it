---
title: 'Esercitazione: Integrazione di Azure Active Directory con Concur | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Concur.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: df47f55f-a894-4e01-a82e-0dbf55fc8af1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: f39013785f000c7055aaa28f4c6f41998644cb6a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-configuring-concur-for-user-provisioning"></a>Esercitazione: Configurazione di Concur per il provisioning utenti

Questa esercitazione descrive le procedure da eseguire in Concur e Azure AD per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Concur.

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

*   Tenant di Azure Active Directory.
*   Sottoscrizione di Concur abilitata per l'accesso Single Sign-On.
*   Account utente in Concur con autorizzazioni di amministratore di team.

## <a name="assigning-users-to-concur"></a>Assegnazione di utenti a Concur

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente vengono sincronizzati solo gli utenti e i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti e/o gruppi in Azure AD rappresentano gli utenti che devono accedere all'app Concur. Dopo aver stabilito questo, è possibile assegnare tali utenti all'app Concur seguendo le istruzioni riportate qui:

[Assegnare un utente o gruppo a un'app aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-concur"></a>Suggerimenti importanti per l'assegnazione di utenti a Concur

*   È consigliabile assegnare un singolo utente di Azure AD a Concur per testare la configurazione del provisioning. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

*   Quando si assegna un utente a Concur, è necessario selezionare un ruolo utente valido. Il ruolo "Default Access" (Accesso predefinito) non è applicabile per il provisioning.

## <a name="enable-user-provisioning"></a>Abilitare il provisioning utenti

Questa sezione illustra la connessione di Azure AD all'API per il provisioning degli account utente di Concur e la configurazione del servizio di provisioning per la creazione, l'aggiornamento e la disabilitazione degli account utente assegnati in Concur in base all'assegnazione di utenti e gruppi in Azure AD.

> [!Tip] 
> Si può anche scegliere di abilitare l'accesso Single Sign-On basato su SAML per Concur, seguendo le istruzioni disponibili nel [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, nonostante queste due funzionalità siano complementari.

### <a name="to-configure-user-account-provisioning"></a>Per configurare il provisioning degli account utente:

Questa sezione descrive come abilitare il provisioning utente degli account utente di Active Directory in Concur.

Per abilitare le app nel servizio Expense, è necessario configurare e usare in modo corretto un profilo di amministratore di servizi Web. Non aggiungere il ruolo di amministratore dei servizi Web al profilo amministratore esistente usato per le funzioni amministrative di T&E.

I consulenti Concur o l'amministratore client devono creare un diverso profilo di amministratore dei servizi Web e l'amministratore client deve usare tale profilo per le funzioni di amministratore dei servizi Web, ad esempio per abilitare le app. Questi profili devono essere mantenuti separati dal profilo di amministratore T&E giornaliero dell'amministratore del client. Questo significa che al profilo di amministratore T&E non deve essere assegnato il ruolo di amministratore dei servizi Web.

Quando si crea il profilo da usare per abilitare l'app, immettere il nome dell'amministratore client nei campi del profilo utente per assegnare la proprietà al profilo. Ciò consente di assegnare la proprietà al profilo. Dopo la creazione di uno o più profili, il client dovrà accedere con questo profilo per poter fare clic sul pulsante "*Abilita*" relativo a un'app dei partner nel menu Servizi web.

Per i seguenti motivi è consigliabile non eseguire questa operazione con il profilo usato per la normale amministrazione T&E.

* Il client deve essere quello che fa clic su "*Sì*" nella finestra di dialogo visualizzata dopo l'abilitazione di un'app. Tale operazione conferma che il client consente all'applicazione del partner di accedere ai dati, di conseguenza non può essere l'utente o il partner a fare clic sul pulsante Yes.

* Se un amministratore client che ha abilitato un'app con il profilo di amministratore T&E lascia la società e il relativo profilo viene disattivato, tutte le app abilitate tramite tale profilo non funzioneranno finché l'app non viene abilitata con un altro profilo di amministratore di servizi Web attivo. È per questo motivo che è richiesta la creazione di profili distinti per l'amministratore di servizi Web.

* Se un amministratore lascia la società, se necessario è possibile modificare il nome associato al profilo dell'amministratore di servizi Web impostandolo su quello di sostituzione senza alcun impatto sull'app abilitata perché non è necessario disattivare tale profilo.

**Per configurare il provisioning utenti, seguire questa procedura:**

1. Accedere al tenant di **Concur**.

2. Nel menu **Administration** (Amministrazione) selezionare **Web Services** (Servizi Web).
   
    ![Tenant Concur](./media/active-directory-saas-concur-provisioning-tutorial/IC721729.png "Tenant Concur")

3. Sul lato sinistro nel riquadro **Web Services** (Servizi Web) selezionare **Enable Partner Application** (Abilita applicazione partner).
   
    ![Enable Partner Application](./media/active-directory-saas-concur-provisioning-tutorial/ic721730.png "Enable Partner Application")

4. Dall'elenco **Enable Application** (Abilita applicazione) selezionare **Azure Active Directory**, quindi fare clic su **Enable** (Abilita).
   
    ![Microsoft Azure Active Directory](./media/active-directory-saas-concur-provisioning-tutorial/ic721731.png "Microsoft Azure Active Directory")

5. Fare clic su **Yes** (Sì) per chiudere la finestra di dialogo **Confirm Action** (Conferma operazione).
   
    ![Confirm Action](./media/active-directory-saas-concur-provisioning-tutorial/ic721732.png "Confirm Action")

6. Nel [portale di Azure](https://portal.azure.com) passare alla sezione **Azure Active Directory > App aziendali > Tutte le applicazioni**.

7. Se si è già configurato Concur per l'accesso Single Sign-On, cercare l'istanza di Concur usando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **Concur** nella raccolta di applicazioni. Selezionare Concur nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

8. Selezionare l'istanza di Concur e quindi la scheda **Provisioning**.

9. Impostare **Modalità di provisioning** su **Automatico**. 
 
    ![provisioning](./media/active-directory-saas-concur-provisioning-tutorial/provisioning.png)

10. Nella sezione **Credenziali amministratore** immettere il **nome utente** e la **password** dell'amministratore di Concur.

11. Nel portale di Azure fare clic su **Connessione di test** per verificare che Azure AD possa connettersi all'app Concur. Se la connessione non riesce, verificare che l'account Concur disponga di autorizzazioni di amministratore di team.

12. Immettere l'indirizzo e-mail di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning nel campo **Messaggio di posta elettronica di notifica** e selezionare la casella di controllo.

13. Fare clic su **Salva**.

14. Nella sezione Mapping selezionare **Synchronize Azure Active Directory Users to Concur** (Sincronizza utenti di Azure Active Directory in Concur).

15. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a Concur. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Concur per le operazioni di aggiornamento. Selezionare il pulsante Salva per eseguire il commit delle modifiche.

16. Per abilitare il servizio di provisioning di Azure AD per Concur, impostare lo **Stato del provisioning** su **Sì** nella sezione **Impostazioni**

17. Fare clic su **Salva**.

È ora possibile creare un account di test. Attendere 20 minuti per verificare che l'account sia stato sincronizzato con Concur.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Configurare l'accesso Single Sign-On](active-directory-saas-concur-tutorial.md)

