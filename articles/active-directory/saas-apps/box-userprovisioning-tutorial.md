---
title: 'Esercitazione: Configurare Box per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Box.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/20/2020
ms.author: jeedes
ms.openlocfilehash: 44f2195fb68b5a17eab3980f72cbc5374e1c033a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91312907"
---
# <a name="tutorial-configure-box-for-automatic-user-provisioning"></a>Esercitazione: Configurare Box per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire in Box e Azure AD per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Box.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Box, sono necessari gli elementi seguenti:

- Un tenant di Azure AD
- Un piano business o più vantaggioso di Box

> [!NOTE]
> Per testare i passaggi di questa esercitazione, è consigliabile *non* usare un ambiente di produzione.

> [!NOTE]
> Per prima cosa, le app devono essere abilitate nell'applicazione Box.

A questo scopo, seguire queste indicazioni:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-box"></a>Assegnazione di utenti a Box 

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente vengono sincronizzati solo gli utenti e i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti e/o gruppi in Azure AD rappresentano gli utenti che devono accedere all'app Box. Dopo aver stabilito questo, è possibile assegnare tali utenti all'app Box seguendo le istruzioni riportate nell'articolo seguente:

[Assegnare un utente o gruppo a un'app aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

## <a name="assign-users-and-groups"></a>Assegnare utenti e gruppi
La scheda **Box > Utenti e gruppi** nel portale di Azure consente di specificare gli utenti e i gruppi cui concedere l'accesso a Box. L'assegnazione di un utente o gruppo causa quanto segue:

* Azure AD consente all'utente, assegnato tramite assegnazione diretta o appartenenza a un gruppo, di eseguire l'autenticazione in Box. Se l'utente non è assegnato, Azure AD non consente all'utente di eseguire l'accesso a Box e restituisce un errore nella pagina di accesso di Azure AD.
* Un riquadro dell'app Box viene aggiunto alla [schermata di avvio delle applicazioni](../manage-apps/end-user-experiences.md)dell'utente.
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

    ![Screenshot della scheda provisioning per box in portale di Azure. La modalità di provisioning è impostata su automatico e l'autorizzazione è evidenziata nelle credenziali di amministratore.](./media/box-userprovisioning-tutorial/provisioning.png)

5. Nella sezione **Credenziali amministratore** fare clic su **Autorizza** per aprire una finestra di dialogo di accesso a Box in una nuova finestra del browser.

6. Nella pagina **Log in to grant access to Box** (Accedere per concedere l'accesso a Box) specificare le credenziali richieste e fare clic su **Authorize** (Autorizza). 
   
    ![Screenshot dell'accesso per concedere l'accesso alla schermata box, che mostra la voce per la posta elettronica e la password e il pulsante autorizza.](./media/box-userprovisioning-tutorial/IC769546.png "Abilita provisioning utenti automatico")

7. Fare clic su **Grant access to Box** (Concedi l'accesso a Box) per autorizzare l'operazione e tornare al portale di Azure. 
   
    ![Screenshot della schermata autorizza accesso in box, che mostra un messaggio esplicativo e il pulsante Concedi accesso a box.](./media/box-userprovisioning-tutorial/IC769549.png "Abilita provisioning utenti automatico")

8. Nel portale di Azure fare clic su **Test connessione** per verificare che Azure AD possa connettersi all'app Box. Se la connessione non riesce, verificare che l'account di Box abbia autorizzazioni di amministratore di team e ripetere il passaggio **"Autorizza"**.

9. Immettere l'indirizzo e-mail di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning nel campo **Messaggio di posta elettronica di notifica** e selezionare la casella di controllo.

10. Fare clic su **Salva**.

11. Nella sezione Mapping selezionare **Synchronize Azure Active Directory Users to Slack** (Sincronizza utenti di Azure Active Directory in Box).

12. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a Box. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Box per le operazioni di aggiornamento. Selezionare il pulsante Salva per eseguire il commit delle modifiche.

13. Per abilitare il servizio di provisioning di Azure AD per Box, impostare **Stato del provisioning** su **Sì** nella sezione Impostazioni

14. Fare clic su **Salva**.

Viene avviata la sincronizzazione iniziale di tutti gli utenti e/o i gruppi assegnati a Box nella sezione Utenti e gruppi. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti per tutto il tempo che il servizio è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai log delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning per l'app Box.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

Nel tenant di Box gli utenti sincronizzati sono elencati in **Utenti gestiti** della console di amministrazione ****.

![Integration status](./media/box-userprovisioning-tutorial/IC769556.png "Integration status")


## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
* [Configura accesso Single Sign-on](box-tutorial.md)
