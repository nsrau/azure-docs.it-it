---
title: Gestione del provisioning di utenti per le app aziendali con Azure Active Directory | Microsoft Docs
description: Informazioni su come gestire il provisioning degli account utente per le app aziendali usando Azure Active Directory
services: active-directory
documentationcenter: 
author: asmalser
manager: mtillman
editor: 
ms.assetid: 34ac4028-a5aa-40d9-a93b-0db4e0abd793
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/26/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: 122dbdb838377a36020f9ec692b38544004e676c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Gestione del provisioning degli account utente per le app aziendali nel portale di Azure
Questo articolo illustra come usare il [portale di Azure](https://portal.azure.com) per gestire il provisioning e il deprovisioning automatici degli account per le applicazioni che li supportano, in particolare per le applicazioni aggiunte dalla categoria "In primo piano" della [raccolta di applicazioni di Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). Per altre informazioni sul provisioning automatico degli account utente e sul relativo funzionamento, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md).

## <a name="finding-your-apps-in-the-portal"></a>Individuazione delle app nel portale
Tutte le applicazioni configurate per l'accesso Single Sign-On in una directory da un amministratore di directory che usa la [raccolta di applicazioni di Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery) possono essere visualizzate e gestite nel [portale di Azure](https://portal.azure.com). Le applicazioni sono disponibili nella sezione **More Services** (Altri servizi) &gt; **Applicazioni aziendali** del portale. Le app aziendali sono app distribuite e usate all'interno dell'organizzazione.

![Pannello Applicazioni aziendali][0]

Se si seleziona il collegamento **Tutte le applicazioni** a sinistra, viene visualizzato un elenco di tutte le app configurate, incluse le app aggiunte dalla raccolta. Se si seleziona un'app, viene caricato il pannello delle risorse per tale app, in cui è possibile visualizzare i report per l'app e gestire diverse impostazioni.

Le impostazioni del provisioning degli account utente possono essere gestite selezionando **Provisioning** a sinistra.

![Pannello Risorsa applicazione][1]

## <a name="provisioning-modes"></a>Modalità di provisioning
Il pannello **Provisioning** inizia con un menu **Modalità**, che mostra le modalità di provisioning supportate per un'applicazione aziendale e ne consente la configurazione. Le opzioni disponibili includono:

* **Automatico** : questa opzione viene visualizzata se Azure AD supporta il provisioning e/o il deprovisioning automatico basato su API di account utente in questa applicazione. Se si seleziona questa modalità, viene visualizzata un'interfaccia che fornisce agli amministratori informazioni dettagliate sulla configurazione di Azure AD per la connessione all'API di gestione degli utenti dell'applicazione, sulla creazione di mapping di account e flussi di lavoro che definiscono il modo in cui i dati relativi agli account utente devono essere trasmessi tra Azure AD e l'app e sulla gestione del servizio di provisioning di Azure AD.
* **Manuale** : questa opzione viene visualizzata se Azure AD non supporta il provisioning automatico di account utente in questa applicazione. Questa opzione indica che i record relativi agli account utente archiviati nell'applicazione devono essere gestiti usando un processo esterno, in base alle funzionalità di gestione degli utenti e di provisioning fornite dall'applicazione, che possono includere il provisioning just-in-time SAML.

## <a name="configuring-automatic-user-account-provisioning"></a>Configurazione del provisioning automatico degli account utente
Se si seleziona l'opzione **Automatico** , viene visualizzata una schermata suddivisa in quattro sezioni:

### <a name="admin-credentials"></a>Credenziali di amministratore
In questa sezione vengono immesse le credenziali necessarie ad Azure AD per la connessione all'API di gestione degli utenti dell'applicazione. L'input necessario dipende dall'applicazione. Per informazioni sui tipi di credenziali e sui requisiti per applicazioni specifiche, vedere l' [esercitazione sulla configurazione per l'applicazione specifica](active-directory-saas-app-provisioning.md).

Se si seleziona il pulsante **Test connessione** , è possibile testare le credenziali mediante un tentativo di connessione di Azure AD all'app di provisioning dell'app con le credenziali fornite.

### <a name="mappings"></a>Mapping
In questa sezione gli amministratori possono visualizzare e modificare gli attributi utente trasmessi tra Azure AD e l'applicazione di destinazione durante il provisioning o l'aggiornamento degli account utente.

Esiste un set preconfigurato di mapping tra gli oggetti utente di Azure AD e gli oggetti utente di ogni app SaaS. Alcune app gestiscono altri tipi di oggetti, quali Gruppi o Contatti. Se si seleziona uno di questi mapping nella tabella, viene visualizzato l'editor di mapping, che consente di visualizzare e personalizzare i mapping.

![Pannello Risorsa applicazione][2]

Le personalizzazioni supportate includono:

* Abilitazione e disabilitazione dei mapping per oggetti specifici, ad esempio l'oggetto utente di Azure AD all'oggetto utente dell'app SaaS.
* Modifica degli attributi che devono essere trasmessi dall'oggetto utente di Azure AD all'oggetto utente dell'app. Per altre informazioni sul mapping degli attributi, vedere [Informazioni sui tipi di mapping degli attributi](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-types).
* Filtrare le azioni di provisioning che Azure AD esegue nell'applicazione di destinazione. Invece di fare in modo che Azure AD esegua la sincronizzazione completa degli oggetti, è possibile limitare le azioni eseguite. Se si seleziona solo **Aggiorna**, ad esempio, Azure AD aggiorna solo gli account utente esistenti in un'applicazione e non crea nuovi account utente. Se si seleziona solo **Crea**, Azure crea solo nuovi account utente, ma non aggiorna gli account utente esistenti. Questa funzionalità consente agli amministratori di creare diversi mapping per i flussi di lavoro di creazione e aggiornamento degli account.

### <a name="settings"></a>Impostazioni
Questa sezione consente agli amministratori di avviare e arrestare il servizio di provisioning di Azure AD per l'applicazione selezionata, oltre a cancellare facoltativamente la cache di provisioning e riavviare il servizio.

Se il provisioning viene abilitato per la prima volta per un'applicazione, attivare il servizio impostando **Stato del provisioning** su **Sì**. Il servizio di provisioning di Azure AD esegue quindi una sincronizzazione iniziale, durante la quale legge gli utenti assegnati nella sezione **Utenti e gruppi**, esegue query nell'applicazione di destinazione alla ricerca di tali utenti e quindi esegue le azioni di provisioning definite nella sezione **Mapping** di Azure AD. Durante questo processo il servizio di provisioning archivia i dati memorizzati nella cache relativi agli account utente gestiti, in modo che gli account non gestiti all'interno dell'applicazione di destinazione non inclusi nell'ambito dell'assegnazione non siano interessati dalle operazioni di deprovisioning. Dopo la sincronizzazione iniziale, il servizio di provisioning sincronizza automaticamente gli oggetti utente e gruppo a intervalli di dieci minuti.

Se si imposta **Stato del provisioning** su **No**, il servizio di provisioning viene semplicemente sospeso. In questo stato Azure non crea, aggiorna o rimuove oggetti utente o gruppo nell'app. Se si reimposta lo stato su Sì, il servizio ripartirà dal punto in cui si era interrotto.

Se si seleziona la casella di controllo **Cancella lo stato corrente e riavvia la sincronizzazione** e si salva, il servizio di provisioning viene arrestato, i dati memorizzati nella cache sugli account gestiti da Azure AD vengono rimossi, i servizi vengono riavviati e viene eseguita di nuovo la sincronizzazione iniziale. Questa opzione consente agli amministratori di avviare di nuovo il processo di distribuzione del provisioning.

### <a name="synchronization-details"></a>Dettagli sincronizzazione
Questa sezione fornisce dettagli aggiuntivi sul funzionamento del servizio di provisioning, incluse la prima e l'ultima volta in cui il servizio di provisioning è stato eseguito nell'applicazione e il modo in cui vengono gestiti gli oggetti utente e gruppo.

Sono disponibili collegamenti al **report dell'attività di provisioning**, che fornisce un log di tutti gli utenti e di tutti i gruppi creati, aggiornati e rimossi tra Azure AD e l'applicazione di destinazione, e al **report degli errori di provisioning**, che fornisce messaggi di errore più dettagliati per oggetti utente e gruppo che non è stato possibile leggere, creare, aggiornare o rimuovere. 

##<a name="feedback"></a>Commenti e suggerimenti

Speriamo che gli utenti apprezzino Azure AD e inviino commenti e suggerimenti. È possibile inviare commenti e idee per apportare miglioramenti nella sezione **Portale di amministrazione** del [forum dei commenti](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Microsoft accoglie i suggerimenti degli utenti per definire nuove funzionalità ogni giorno.


[0]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-blade.PNG
[1]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning.PNG
[2]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning-mapping.PNG
