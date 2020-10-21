---
title: Report attività di accesso Azure Active Directory-anteprima | Microsoft Docs
description: Introduzione ai report delle attività di accesso nel portale di Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 09/23/2020
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36e6642e230fa809630751e224ff9384ea8524d1
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92319784"
---
# <a name="azure-active-directory-sign-in-activity-reports---preview"></a>Report attività di accesso Azure Active Directory-anteprima

L'architettura di report in Azure Active Directory (Azure AD) include i componenti seguenti:

- **Attività** 
    - **Accessi: informazioni** sul momento in cui gli utenti, le applicazioni e le risorse gestite accedono a Azure ad e accedono alle risorse.
    - **Log**  -  di controllo I [log di controllo](concept-audit-logs.md) forniscono informazioni sulle attività di sistema relative agli utenti e alla gestione dei gruppi, alle applicazioni gestite e alle attività di directory.
- **Sicurezza** 
    - **Accessi a rischio** : un [accesso rischioso](../identity-protection/overview-identity-protection.md) è un indicatore del tentativo di accesso da parte di un utente che non è il proprietario legittimo di un account utente.
    - **Utenti contrassegnati per il rischio** : un [utente rischioso](../identity-protection/overview-identity-protection.md) è un indicatore per un account utente che potrebbe essere stato compromesso.

Il report degli accessi classici in Azure Active Directory offre una panoramica degli accessi degli utenti interattivi. Inoltre, è ora possibile accedere a tre report di accesso aggiuntivi ora disponibili in anteprima:

- Accessi utente non interattivo

- Accessi all'entità servizio

- Identità gestite per gli accessi alle risorse di Azure

Questo articolo offre una panoramica del report sulle attività di accesso con l'anteprima delle identità non interattive, delle applicazioni e gestite per gli accessi alle risorse di Azure. Per informazioni sul report di accesso senza le funzionalità di anteprima, vedere  [report delle attività di accesso nel portale di Azure Active Directory](concept-sign-ins.md).



## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare a usare questa funzionalità, è necessario essere a conoscenza delle risposte seguenti:

- Chi può accedere ai dati?

- Quale licenza di Azure AD è necessaria per visualizzare le attività di accesso?

### <a name="who-can-access-the-data"></a>Chi può accedere ai dati?

- Utenti nei ruoli amministratore sicurezza, lettore sicurezza e lettore report

- Amministratori globali

- Qualsiasi utente (non amministratore) può visualizzare i propri accessi 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Quale licenza di Azure AD è necessaria per visualizzare le attività di accesso?

Per visualizzare le attività di accesso, è necessario che al tenant sia associata una licenza di Azure AD Premium. vedere [Procedura: Effettuare l'iscrizione alle edizioni Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) per aggiornare l'edizione di Azure Active Directory in uso. Verranno visualizzati alcuni giorni prima che i dati vengano visualizzati nei report dopo l'aggiornamento a una licenza Premium senza attività di dati prima dell'aggiornamento.



## <a name="sign-ins-report"></a>Report sugli accessi

Il report accessi fornisce le risposte alle domande seguenti:

- Qual è il modello di accesso di un utente, un'applicazione o un servizio?
- Il numero di utenti, app o servizi che hanno eseguito l'accesso più di una settimana?
- Qual è lo stato di questi accessi?


Nel pannello report accessi è possibile spostarsi tra:

- **Accessi utente interattivo** : accessi in cui un utente fornisce un fattore di autenticazione, ad esempio una password, una risposta tramite un'app multi-factor, un fattore biometrico o un codice a matrice.

- **Accessi utente non interattivo** : accessi eseguiti da un client per conto di un utente. Questi accessi non richiedono alcuna interazione o fattore di autenticazione da parte dell'utente. Ad esempio, l'autenticazione e l'autorizzazione con i token di accesso e di aggiornamento che non richiedono l'immissione di credenziali da un utente.

- **Accessi dell'entità servizio** : accessi per app ed entità servizio che non coinvolgono alcun utente. In questi accessi l'app o il servizio fornisce una credenziale per il proprio conto per l'autenticazione o l'accesso alle risorse.

- **Identità gestite per le risorse di Azure accessi** : accessi dalle risorse di Azure con segreti gestiti da Azure. Per altre informazioni, vedere [che cosa sono le identità gestite per le risorse di Azure?](../managed-identities-azure-resources/overview.md) 


![Tipi di report di accessi](./media/concept-all-sign-ins/sign-ins-report-types.png)












## <a name="user-sign-ins"></a>Accessi utente

Ogni scheda nel pannello degli accessi Mostra le colonne predefinite seguenti. Alcune schede contengono colonne aggiuntive:

- Data di accesso

- ID richiesta

- Nome utente o ID utente

- Nome dell'applicazione o ID applicazione

- Stato dell'accesso

- Indirizzo IP del dispositivo usato per l'accesso



### <a name="interactive-user-sign-ins"></a>Accessi utente interattivo


Gli accessi utente interattivi sono accessi in cui un utente fornisce un fattore di autenticazione per Azure AD o interagisce direttamente con Azure AD o un'app helper, ad esempio l'app Microsoft Authenticator. I fattori forniti dagli utenti includono password, risposte a problemi di autenticazione a più fattori, fattori biometrici o codici a matrice forniti da un utente per Azure AD o a un'app helper.

Questo report include anche gli accessi federati da provider di identità federati per Azure AD.  


**Dimensioni report:** piccole <br> 
**Esempi**

- Un utente fornisce nome utente e password nella schermata di accesso Azure AD.

- Un utente supera una richiesta di autenticazione A più fattori di SMS.

- Un utente fornisce un movimento biometrico per sbloccare il PC Windows con Windows Hello for business.

- Un utente è federato per Azure AD con un'asserzione SAML AD FS.


Oltre ai campi predefiniti, il report accessi interattivo mostra anche: 

- Percorso di accesso

- Indica se l'accesso condizionale è stato applicato



È possibile personalizzare la visualizzazione elenco facendo clic su **colonne** nella barra degli strumenti.

![Colonne di accesso utente interattivo](./media/concept-all-sign-ins/columns-interactive.png "Colonne di accesso utente interattivo")





La personalizzazione della visualizzazione consente di visualizzare campi aggiuntivi o di rimuovere i campi già visualizzati.

![Tutte le colonne interattive](./media/concept-all-sign-ins/all-interactive-columns.png)


Selezionare un elemento nella visualizzazione elenco per ottenere informazioni più dettagliate sull'accesso correlato.

![Attività di accesso](./media/concept-all-sign-ins/interactive-user-sign-in-details.png "Accessi utente interattivo")



### <a name="non-interactive-user-sign-ins"></a>Accessi utente non interattivo

Gli accessi utente non interattivi sono accessi eseguiti da un'app client o componenti del sistema operativo per conto di un utente. Analogamente agli accessi degli utenti interattivi, questi accessi vengono eseguiti per conto di un utente. Diversamente dagli accessi degli utenti interattivi, questi accessi non richiedono che l'utente fornisca un fattore di autenticazione. Il dispositivo o l'app client usa invece un token o un codice per l'autenticazione o l'accesso a una risorsa per conto di un utente. In generale, l'utente percepirà questi accessi come avviene in background dell'attività dell'utente.


**Dimensioni report:** Grandi dimensioni <br>
**Esempi:** 

- Un'app client usa un token di aggiornamento OAuth 2,0 per ottenere un token di accesso.

- Un client usa un codice di autorizzazione OAuth 2,0 per ottenere un token di accesso e un token di aggiornamento.

- Un utente esegue Single Sign-On (SSO) in un'app Web o Windows in un PC Azure AD aggiunto.

- Un utente accede a una seconda app Microsoft Office mentre ha una sessione in un dispositivo mobile usando i fuochi (famiglia di ID client).




Oltre ai campi predefiniti, il report accessi non interattivo mostra anche: 

- ID risorsa

- Numero di accessi raggruppati




Non è possibile personalizzare i campi visualizzati nel report.


![Colonne disabilitate](./media/concept-all-sign-ins/disabled-columns.png "Colonne disabilitate")

Per semplificare il digest dei dati, vengono raggruppati gli eventi di accesso non interattivo. I client spesso creano molti accessi non interattivi per conto dello stesso utente in un breve periodo di tempo, che condividono tutte le stesse caratteristiche, ad eccezione del momento in cui è stato effettuato il tentativo di accesso. Un client, ad esempio, può ottenere un token di accesso una volta all'ora per conto di un utente. Se l'utente o il client non modifica lo stato, l'indirizzo IP, la risorsa e tutte le altre informazioni sono le stesse per ogni richiesta di token di accesso. Quando Azure AD registra più accessi identici a quelli di ora e di data, tali accessi proverranno dalla stessa entità vengono aggregati in una singola riga. Una riga con più accessi identici (ad eccezione di data e ora rilasciata) avrà un valore maggiore di 1 nella colonna # sign-ins. È possibile espandere la riga per visualizzare tutti i diversi accessi e i rispettivi timestamp diversi. Gli accessi vengono aggregati negli utenti non interattivi quando i dati seguenti corrispondono:


- Applicazione

- Utente

- Indirizzo IP

- Stato

- ID risorsa


è possibile:

- Espandere un nodo per visualizzare i singoli elementi di un gruppo.  

- Fare clic su un singolo elemento per visualizzare tutti i dettagli 


![Dettagli di accesso utente non interattivo](./media/concept-all-sign-ins/non-interactive-sign-ins-details.png)




## <a name="service-principal-sign-ins"></a>Accessi all'entità servizio

Diversamente dagli accessi utente interattivi e non interattivi, gli accessi dell'entità servizio non coinvolgono un utente. Sono invece accessi da qualsiasi account non utente, ad esempio app o entità servizio, ad eccezione dell'accesso all'identità gestita, incluso solo nel report degli accessi a identità gestite. In questi accessi l'app o il servizio fornisce le proprie credenziali, ad esempio un certificato o un segreto dell'app per l'autenticazione o l'accesso alle risorse.


**Dimensioni report:** Grandi dimensioni <br>
**Esempi:**

- Un'entità servizio utilizza un certificato per l'autenticazione e l'accesso al Microsoft Graph. 

- Un'applicazione usa un segreto client per l'autenticazione nel flusso di credenziali client OAuth. 


Questo report include una visualizzazione elenco predefinita che mostra:

- Data di accesso

- ID richiesta

- Nome o ID dell'entità servizio

- Stato

- Indirizzo IP

- Nome risorsa

- ID risorsa

- Numero di accessi

Non è possibile personalizzare i campi visualizzati nel report.

![Colonne disabilitate](./media/concept-all-sign-ins/disabled-columns.png "Colonne disabilitate")

Per semplificare il digest dei dati nei log di accesso dell'entità servizio, vengono raggruppati gli eventi di accesso dell'entità servizio. Gli accessi dalla stessa entità nelle stesse condizioni vengono aggregati in una singola riga. È possibile espandere la riga per visualizzare tutti i diversi accessi e i rispettivi timestamp diversi. Gli accessi vengono aggregati nel report dell'entità servizio quando i dati seguenti corrispondono:

- Nome o ID dell'entità servizio

- Stato

- Indirizzo IP

- Nome o ID risorsa

è possibile:

- Espandere un nodo per visualizzare i singoli elementi di un gruppo.  

- Fare clic su un singolo elemento per visualizzare tutti i dettagli 


![Dettagli colonna](./media/concept-all-sign-ins/service-principals-sign-ins-view.png "Dettagli colonna")




## <a name="managed-identity-for-azure-resources-sign-ins"></a>Identità gestita per gli accessi alle risorse di Azure 

Le identità gestite per gli accessi alle risorse di Azure sono accessi eseguiti da risorse con i segreti gestiti da Azure per semplificare la gestione delle credenziali.

**Dimensioni report:** Piccolo <br> 
**Esempi**

Una macchina virtuale con credenziali gestite USA Azure AD per ottenere un token di accesso.   


Questo report include una visualizzazione elenco predefinita che mostra:


- ID identità gestita

- Nome identità gestita

- Risorsa

- ID risorsa

- Numero di accessi raggruppati

Non è possibile personalizzare i campi visualizzati nel report.

Per semplificare il digest dei dati, le identità gestite per i log di accesso alle risorse di Azure e gli eventi di accesso non interattivo sono raggruppati. Gli accessi dalla stessa entità vengono aggregati in una singola riga. È possibile espandere la riga per visualizzare tutti i diversi accessi e i rispettivi timestamp diversi. Gli accessi vengono aggregati nel report identità gestite quando tutti i dati seguenti corrispondono:

- ID o nome identità gestita

- Stato

- Indirizzo IP

- Nome o ID risorsa

Selezionare un elemento nella visualizzazione elenco per visualizzare tutti gli accessi raggruppati in un nodo.

Selezionare un elemento raggruppato per visualizzare tutti i dettagli dell'accesso. 


## <a name="filter-sign-in-activities"></a>Filtrare le attività di accesso

Impostando un filtro, è possibile restringere l'ambito dei dati di accesso restituiti. Azure AD offre un'ampia gamma di filtri aggiuntivi che è possibile impostare. Quando si imposta il filtro, è sempre necessario prestare particolare attenzione al filtro di intervallo di **date** configurato. Un filtro di intervallo di date appropriato garantisce che Azure AD restituisca solo i dati di cui si ha realmente interesse.     

Il filtro intervallo di **date** consente di definire un intervallo di tempo per i dati restituiti.
I valori possibili sono:

- Un mese

- Sette giorni

- Ventiquattro ore

- Personalizzato

![Filtro intervallo di date](./media/concept-all-sign-ins/date-range-filter.png)





### <a name="filter-user-sign-ins"></a>Filtrare gli accessi utente

Il filtro per gli accessi interattivi e non interattivi è lo stesso. Per questo motivo, il filtro configurato per gli accessi interattivi viene reso permanente per gli accessi non interattivi e viceversa. 






## <a name="access-the-new-sign-in-activity-reports"></a>Accedere ai nuovi report sulle attività di accesso 

Il report attività di accesso nel portale di Azure fornisce un metodo semplice per attivare e disattivare il report di anteprima. Se i report di anteprima sono abilitati, viene visualizzato un nuovo menu che consente di accedere a tutti i tipi di report delle attività di accesso.     


Per accedere ai nuovi report di accesso con accessi non interattivi e dell'applicazione: 

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory**.

    ![Selezionare Azure AD](./media/concept-all-sign-ins/azure-services.png)

2. Nella sezione **monitoraggio** fare clic su **accessi**.

    ![Seleziona accessi](./media/concept-all-sign-ins/sign-ins.png)

3. Fare clic sulla barra di **Anteprima** .

    ![Abilita nuova visualizzazione](./media/concept-all-sign-ins/enable-new-preview.png)

4. Per tornare alla visualizzazione predefinita, fare di nuovo clic sulla barra di **Anteprima** . 

    ![Ripristina visualizzazione classica](./media/concept-all-sign-ins/switch-back.png)







## <a name="download-sign-in-activity-reports"></a>Scaricare i report delle attività di accesso

Quando si scarica un report dell'attività di accesso, si verifica quanto segue:

- È possibile scaricare il report di accesso come file CSV o JSON.

- È possibile scaricare fino a 100-K record. Se si vogliono scaricare più dati, usare l'API di creazione report.

- Il download è basato sulla selezione del filtro eseguita.

- Il numero di record che è possibile scaricare è limitato dai [criteri di conservazione dei report di Azure Active Directory](reference-reports-data-retention.md). 


![Scarica report](./media/concept-all-sign-ins/download-reports.png "Scarica report")


Ogni download CSV è costituito da sei file diversi:

- Accessi interattivi

- Dettagli dell'autenticazione degli accessi interattivi

- Accessi non interattivi

- Dettagli dell'autenticazione degli accessi non interattivi

- Accessi all'entità servizio

- Identità gestita per gli accessi alle risorse di Azure

Ogni download JSON è costituito da quattro file diversi:

- Accessi interattivi (include i dettagli di autenticazione)

- Accessi non interattivi (include i dettagli di autenticazione)

- Accessi all'entità servizio

- Identità gestita per gli accessi alle risorse di Azure

![Scaricare i file](./media/concept-all-sign-ins/download-files.png "Scaricare i file")




## <a name="next-steps"></a>Passaggi successivi

* [Codici di errore del report delle attività di accesso](reference-sign-ins-error-codes.md)
* [Criteri di conservazione dei report di Azure AD](reference-reports-data-retention.md)
* [Latenze dei report di Azure AD](reference-reports-latencies.md)