---
title: 'Esercitazione: Integrazione di Azure Active Directory con Box | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Box.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 1c959595-6e57-4954-9c0d-67ba03ee212b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 9f061f3f5a0a4825854b893150ceccc8951487de
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-configuring-box-for-automatic-user-provisioning"></a>Esercitazione: Configurazione di Box per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire in Box e Azure AD per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Box.

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

*   Tenant di Azure Active Directory.
*   Sottoscrizione di Box abilitata per l'accesso Single Sign-On.
*   Account utente in Box con autorizzazioni di amministratore di team.

## <a name="assigning-users-to-box"></a>Assegnazione di utenti a Box 

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente, vengono sincronizzati solo gli utenti e i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti e/o gruppi in Azure AD rappresentano gli utenti che devono accedere all'app Box. Dopo aver stabilito questo, è possibile assegnare tali utenti all'app Box seguendo le istruzioni riportate nell'articolo seguente:

[Assegnare un utente o gruppo a un'app aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

## <a name="assign-users-and-groups"></a>Assegnare utenti e gruppi
La scheda **Box > Utenti e gruppi** nel portale di Azure consente di specificare gli utenti e i gruppi cui concedere l'accesso a Box. L'assegnazione di un utente o gruppo causa quanto segue:

* Azure AD consente all'utente, assegnato tramite assegnazione diretta o appartenenza a un gruppo, di eseguire l'autenticazione in Box. Se l'utente non è assegnato, Azure AD non consente all'utente di eseguire l'accesso a Box e restituisce un errore nella pagina di accesso di Azure AD.
* Un riquadro dell'app Box viene aggiunto alla [schermata di avvio delle applicazioni](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)dell'utente.
* Se il provisioning automatico è abilitato, gli utenti e/o i gruppi assegnati vengono aggiunti alla coda di provisioning per l'esecuzione automatica del provisioning.
  
  * Se è stata selezionata l'esecuzione del provisioning solo per gli oggetti utente, tutti gli utenti assegnati direttamente e tutti gli utenti che appartengono ai gruppi assegnati vengono aggiunti alla coda di provisioning. 
  * Se è stata selezionata l'esecuzione del provisioning per gli oggetti gruppo, viene eseguito il provisioning in Box di tutti gli oggetti gruppo assegnati e di tutti gli utenti che appartengono a tali gruppi. Le appartenenze utente e gruppo vengono mantenute dopo la scrittura in Box.

La scheda **Attributi > Single Sign-On** consente di configurare gli attributi utente o le attestazioni presentate a Box durante l'autenticazione SAML. La scheda **Attributi > Provisioning** permette di configurare il flusso degli attributi utente e gruppo da Azure AD a Box durante le operazioni di provisioning.

### <a name="important-tips-for-assigning-users-to-box"></a>Suggerimenti importanti per l'assegnazione di utenti a Box 

*   È consigliabile assegnare un singolo utente di Azure AD a Box per testare la configurazione del provisioning. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

*   Quando si assegna un utente a Box, è necessario selezionare un ruolo utente valido. Il ruolo "Default Access" (Accesso predefinito) non è applicabile per il provisioning.

## <a name="enable-automated-user-provisioning"></a>Abilitare il provisioning utenti automatizzato

Questa sezione illustra la connessione di Azure AD all'API per il provisioning degli account utente di Box e la configurazione del servizio di provisioning per la creazione, l'aggiornamento e la disabilitazione degli account utente assegnati in Box in base all'assegnazione di utenti e gruppi in Azure AD.

Se il provisioning automatico è abilitato, gli utenti e/o i gruppi assegnati vengono aggiunti alla coda di provisioning per l'esecuzione automatica del provisioning.
    
 * Se l'esecuzione del provisioning è selezionata solo per gli oggetti utente, gli utenti assegnati direttamente e tutti gli utenti che appartengono ai gruppi assegnati vengono aggiunti alla coda di provisioning. 
    
 * Se è stata selezionata l'esecuzione del provisioning per gli oggetti gruppo, viene eseguito il provisioning in Box di tutti gli oggetti gruppo assegnati e di tutti gli utenti che appartengono a tali gruppi. Le appartenenze utente e gruppo vengono mantenute dopo la scrittura in Box.

> [!TIP] 
> Si può anche scegliere di abilitare l'accesso Single Sign-On basato su SAML per Box, seguendo le istruzioni disponibili nel [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, nonostante queste due funzionalità siano complementari.

### <a name="to-configure-automatic-user-account-provisioning"></a>Per configurare il provisioning automatico degli account utente:

Questa sezione descrive come abilitare il provisioning degli account utente di Active Directory in Box.

1. Nel [portale di Azure](https://portal.azure.com) passare alla sezione **Azure Active Directory > App aziendali > Tutte le applicazioni**.

2. Se si è già configurato Box per l'accesso Single Sign-On, cercare l'istanza di Box usando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **Box** nella raccolta di applicazioni. Selezionare Box nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

3. Selezionare l'istanza di Box e quindi la scheda **Provisioning**.

4. Impostare **Modalità di provisioning** su **Automatico**. 

    ![provisioning](./media/active-directory-saas-box-userprovisioning-tutorial/provisioning.png)

5. Nella sezione **Credenziali amministratore** fare clic su **Autorizza** per aprire una finestra di dialogo di accesso a Box in una nuova finestra del browser.

6. Nella pagina **Log in to grant access to Box** (Accedere per concedere l'accesso a Box) specificare le credenziali richieste e fare clic su **Authorize** (Autorizza). 
   
    ![Enable automatic user provisioning](./media/active-directory-saas-box-userprovisioning-tutorial/IC769546.png "Enable automatic user provisioning")

7. Fare clic su **Grant access to Box** (Concedi l'accesso a Box) per autorizzare l'operazione e tornare al portale di Azure. 
   
    ![Enable automatic user provisioning](./media/active-directory-saas-box-userprovisioning-tutorial/IC769549.png "Enable automatic user provisioning")

8. Nel portale di Azure fare clic su **Test connessione** per verificare che Azure AD possa connettersi all'app Box. Se la connessione non riesce, verificare che l'account di Box abbia autorizzazioni di amministratore di team e ripetere il passaggio **"Autorizza"**.

9. Immettere l'indirizzo e-mail di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning nel campo **Messaggio di posta elettronica di notifica** e selezionare la casella di controllo.

10. Fare clic su **Salva**.

11. Nella sezione Mapping selezionare **Synchronize Azure Active Directory Users to Slack** (Sincronizza utenti di Azure Active Directory in Box).

12. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a Box. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Box per le operazioni di aggiornamento. Selezionare il pulsante Salva per eseguire il commit delle modifiche.

13. Per abilitare il servizio di provisioning di Azure AD per Box, impostare **Stato del provisioning** su **Sì** nella sezione Impostazioni

14. Fare clic su **Salva**.

Viene avviata la sincronizzazione iniziale di tutti gli utenti e/o i gruppi assegnati a Box nella sezione Utenti e gruppi. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 20 minuti per tutto il tempo che il servizio è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning per l'app Box.

È ora possibile creare un account di test. Attendere 20 minuti per verificare che l'account sia stato sincronizzato con Box.

Nel tenant Box, gli utenti sincronizzati sono visualizzati nella sezione **Managed Users** (Utenti gestiti) di **Admin Console** (Console di amministrazione).

![Stato integrazione](./media/active-directory-saas-box-userprovisioning-tutorial/IC769556.png "Stato integrazione")


## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Configurare l'accesso Single Sign-On](active-directory-saas-box-tutorial.md)