---
title: Cos'è Azure Active Directory Identity Protection (aggiornato)? | Microsoft Docs
description: Cos'è Azure Active Directory Identity Protection (aggiornato)?
services: active-directory
keywords: azure active directory identity protection, cloud app discovery, gestione applicazioni, sicurezza, rischio, livello di rischio, vulnerabilità, criteri di sicurezza
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f6c2f36e1061243851b37da47659aaf7a18e8d6
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673013"
---
# <a name="what-is-azure-active-directory-identity-protection-refreshed"></a>Cos'è Azure Active Directory Identity Protection (aggiornato)?

L'esperienza di Identity Protection è stata aggiornata per migliorare la protezione delle identità dell'organizzazione. L’esperienza aggiornata offre:

- Nuovo approccio all’amministrazione, incentrato sui rischi che interessano maggiormente l’utente: il rischio utente e il rischio di accesso

- Potenti funzionalità di indagine con supporto per opzioni di filtro, ordinamento e download intelligenti

- Miglioramento del calcolo del rischio utente per assegnare la priorità agli interventi verso gli utenti con probabilità di compromissione più elevata

- Supporto di una nuova API per abilitare l'accesso a livello di codice ai dati sui rischi

- Processo di feedback di amministrazione semplificato, che consente di proteggere immediatamente gli utenti

- Nuova funzionalità di apprendimento automatico con supervisione per migliorare l'accuratezza delle valutazioni dei rischi



La sicurezza è una priorità assoluta per le organizzazioni di oggi. La maggior parte delle violazioni della sicurezza si verifica quando utenti malintenzionati ottengono l'accesso a un ambiente impadronendosi dell'identità di un utente. Nel corso degli anni gli utenti malintenzionati hanno messo a punto tecniche sempre più efficaci per sfruttare le violazioni di terze parti e sferrare sofisticati attacchi di phishing. L'accesso a un account utente, anche quelli con privilegi limitati, permette agli utenti malintenzionati di accedere immediatamente a risorse aziendali importanti in modo piuttosto semplice tramite il movimento laterale. 

Per rispondere a queste minacce, Azure AD Identity Protection consente di: 

- Impedire in modo proattivo l'uso improprio delle identità compromesse 

- Contenere automaticamente i rischi quando viene rilevata un'attività sospetta 

- Analizzare gli utenti e gli accessi a rischio per risolvere potenziali vulnerabilità  

- Essere avvisati qualora il rischio di un utente raggiunge una soglia specificata 

 

Azure AD Identity Protection è una funzionalità di Azure Active Directory Premium P2 che consente di configurare i criteri per rispondere automaticamente quando viene compromessa l'identità di un utente o quando un utente diverso dal proprietario dell'account sta tentando di accedere usando l’identità di quest’ultimo. Questi criteri, insieme ad altri controlli di accesso condizionale forniti da Azure AD, se possono bloccare automaticamente l'accesso o azioni di mitigazione di avvio, ad esempio la reimpostazione della password o l'applicazione dell'autenticazione a più fattori. Inoltre, Identity Protection offre funzionalità di monitoraggio e creazione di report per ottenere informazioni più approfondite sui rischi e sulle potenziali compromissioni all'interno dell'organizzazione. 

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWsS6Q]


## <a name="risk-events"></a>Eventi di rischio

Azure AD Identity Protection rileva gli eventi di rischio seguenti: 

 

| Tipo di evento di rischio | DESCRIZIONE | Tipo di rilevamento |
| ---             | ---         | ---            |
| Trasferimento atipico | Accesso da una posizione insolita in base agli accessi recenti dell'utente. | Offline |
| Indirizzo IP anonimo | Accesso da indirizzo IP anonimo (ad esempio Tor Browser, VPN per navigazione in anonimato). | Tempo reale |
| Proprietà di accesso insolite | Accesso con proprietà non osservate di recente per l'utente specificato. | Tempo reale |
| Indirizzo IP collegato a malware | Accesso da indirizzo IP collegato a malware | Offline |
| Credenziali perse | Questo evento di rischio indica che le credenziali valide dell'utente sono andate perse | Offline |





## <a name="types-of-risk"></a>Tipi di rischio 

Identity Protection si basa su due tipi di rischio:

- Rischio di accesso

- Rischio utente

### <a name="sign-in-risk"></a>Rischio di accesso

Un rischio di accesso rappresenta la probabilità che una richiesta di autenticazione specificata non sia stata autorizzata dal proprietario dell'identità.

Esistono due valutazioni del rischio di accesso: 

- **Rischio di accesso (in tempo reale)** - Il rischio di accesso (in tempo reale) si basa su tutti i rilevamenti in tempo reale che si attivano durante l'elaborazione dell’accesso.  

- **Rischio di accesso (aggregazione)** - Il rischio di accesso (aggregazione) è il rischio complessivo di un accesso. Viene calcolato tramite un modello di machine learning che prende in considerazione:

    - Rilevamenti in tempo reale (descritti in precedenza)
    
    - Rilevamenti offline (che vengono generati dopo l’esecuzione dell'accesso) 
    
    - Tutte le altre funzionalità dell’accesso


### <a name="user-risk"></a>Rischio utente

Un rischio utente rappresenta la probabilità che un'identità specificata sia compromesso. 

Il rischio utente viene calcolato considerando tutti i rischi associati all'utente:

- Tutti gli accessi a rischio
- Tutti gli eventi di rischio non collegati a un accesso
- Il rischio utente corrente
- Eventuali azioni di correzione o rimozione dei rischi eseguite per l'utente fino alla data corrente



## <a name="how-identity-protection-detects-risk"></a>Rilevamento del rischio in Identity Protection  

Azure AD usa l’apprendimento automatico per rilevare anomalie ed eventuali attività sospette, sfruttando sia i segnali rilevati in tempo reale durante gli accessi, sia quelli non in tempo reale correlati agli utenti e alle loro attività di accesso. Usando questi dati, Identity Protection consente di calcolare un rischio di accesso in tempo reale ogni volta che un utente esegue l'autenticazione, nonché determinare un livello di rischio utente complessivo per ogni utente. Identity Protection consente di eseguire azioni automatiche sui rilevamenti di rischio tramite la configurazione dei criteri per rischi utente e rischi di accesso.  

 

Per capire come Identity Protection rileva i rischi, è necessario spiegare due concetti importanti: rischio utente e rischio di accesso. Un rischio di accesso rappresenta la probabilità che una richiesta di autenticazione specificata non sia stata autorizzata dal proprietario dell'identità. Esistono due tipi di rischi di accesso: in tempo reale e totale. Il rischio di accesso in tempo reale viene rilevato durante il tentativo di accesso specificato (ad esempio, accessi da indirizzi IP anonimi). Il rischio di accesso totale è l'aggregazione dei rischi di accesso in tempo reale rilevati oltre a eventuali eventi di rischio non in tempo reale successivi associati agli accessi dell'utente (ad esempio comunicazione impossibile). Il rischio utente rappresenta la probabilità complessiva che una determinata identità sia stata compromessa da un utente malintenzionato. Il rischio utente contiene tutte le attività di rischio per un determinato utente, tra cui:

- Rischio di accesso in tempo reale
- Rischi di accesso successivi
- Rilevamenti di utenti a rischio.   

 

 
 ![Flusso](./media/overview-v2/01.png)
 

 

Il flusso di base per il rilevamento dei rischi e la risposta di Identity Protection in relazione a qualsiasi accesso specificato è riepilogato nella figura precedente.  

 

 

 

## <a name="common-scenarios"></a>Scenari comuni 

Esaminiamo l'esempio di un dipendente di Contoso. 

1. Un dipendente tenta di accedere a Exchange Online dal browser Tor. Al momento dell'accesso, Azure AD rileva gli eventi di rischio in tempo reale. 

2. Azure AD rileva che il dipendente esegue l'accesso da un indirizzo IP anonimo, attivazione di un livello di rischio di accesso medio. 

3. Viene visualizzata una richiesta dipendente da un prompt di autenticazione a più fattori, poiché configurate dall'amministratore di Contoso di Identity Protection rischio di accesso criteri di accesso condizionale. I criteri richiedono l'autenticazione a più fattori per un rischio di accesso di livello medio o superiore. 

4. Il dipendente passa la richiesta di autenticazione a più fattori e accede a Exchange Online e non viene modificato il livello di rischio utente. 

Cosa è successo dietro le quinte? Il tentativo di accesso da Tor Browser ha attivato un rischio di accesso in tempo reale in Azure AD per l'indirizzo IP anonimo. Man mano che elaborate la richiesta di Azure AD, applicato il criterio di rischio di accesso configurato in Identity Protection perché a livello di rischio di accesso del dipendente raggiunto la soglia (Medium). Poiché il dipendente fosse registrato in precedenza per MFA, erano in grado di rispondere e passare la richiesta di verifica MFA. La possibilità di passare correttamente la verifica MFA segnalato ad Azure AD che sono stati probabilmente il proprietario legittimo di identità e non si incrementa il livello di rischio utente. 


Ma cosa accade se il dipendente non è stato di un tentativo di accedere? 

1. Un attore malintenzionato con credenziali del dipendente tenta di accedere al proprio account Exchange Online dal browser Tor, poiché si sta tentando di nascondere l'indirizzo IP. 

2. Azure AD rileva che il tentativo di accesso avviene da un indirizzo IP anonimo, attivando un rischio di accesso in tempo reale. 

3. L’utente malintenzionato visualizza una richiesta di verifica tramite prompt di autenticazione a più fattori (MFA), perché l’amministratore IT di Contoso ha configurato i criteri di accesso condizionale per il rischio di accesso di Identity Protection in modo richiedere l’autenticazione a più fattori quando il rischio di accesso è medio o superiore. 

4. L'attore malintenzionato si verifica un errore di verifica MFA e non può accedere all'account Exchange Online del dipendente. 

5. L'autenticazione a più fattori non riuscite prompt attivato un evento di rischio per la registrazione, che genera il rischio utente per gli accessi futuri. 

Ora che un attore malintenzionato ha provato ad accedere l'account di Sarah, vediamo cosa accade la volta successiva che tenta di accedere al dipendente. 

1. Il dipendente tenta di accedere a Exchange Online da Outlook. Al momento dell'accesso, Azure AD rileva gli eventi di rischio in tempo reale, nonché qualsiasi rischio utente precedente. 

2. Azure AD non rileva alcun rischio di accesso in tempo reale, ma un rischio utente più elevato dovuto all’attività rischiosa degli scenari precedenti.  

3. Il dipendente viene visualizzata una richiesta da una richiesta di reimpostazione della password, poiché Contoso dell'amministratore IT configurato i criteri di rischio utente di Identity Protection per richiedere la modifica della password all'accesso di un utente con ad alto rischio. 

4. Poiché il dipendente è registrato per MFA e SSPR, sono correttamente Reimposta la password. 

5. Per reimpostare la password, non è più compromissione delle credenziali del dipendente e la propria identità restituisce a uno stato sicuro. 

6. Gli eventi di rischio precedenti del dipendente vengono risolti e il livello di rischio utente viene reimpostato automaticamente in risposta a riduzione del rischio di compromissione delle credenziali. 

## <a name="how-do-i-configure-identity-protection"></a>Come si configura Identity Protection? 

Per iniziare a usare Identity Protection, per prima cosa è necessario configurare i criteri di rischio utente e di rischio di accesso. Una volta configurati questi criteri e applicati a un gruppo di test, è possibile simulare eventi di rischio per comprendere la risposta di Identity Protection nell'ambiente in uso. Le guide di avvio rapido seguenti offrono una procedura dettagliata su come configurare i criteri indicati in precedenza ed effettuarne il test nel proprio ambiente. 

 

Identity Protection supporta 3 ruoli in Azure AD per bilanciare le attività di gestione nella distribuzione: 

| Role | Operazione consentita | Operazione non consentita |
| --- | --- | --- |
| Amministratore globale | Accesso completo a Identity Protection, implementazione di Identity Protection | |
| Amministratore della sicurezza | Accesso completo a Identity Protection | Implementazione di Identity Protection, reimpostazione delle password per un utente |
| Ruolo con autorizzazioni di lettura per la sicurezza | Accesso in sola lettura a Identity Protection | Implementazione di Identity Protection, correzione degli utenti, configurazione dei criteri, reimpostazione delle password| 

Per altri dettagli, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)

 
## <a name="licensing"></a>Licenze

>[!NOTE]
> Durante l'anteprima pubblica di Identity Protection (aggiornato), solo i clienti di Azure AD Premium P2 avranno accesso ai report degli utenti a rischio e degli accessi a rischio.



| Funzionalità | Azure AD P2 Premium | Azure AD Premium P1 | Azure AD Basic/Gratuito |
| --- | --- | --- | --- |
| Criteri di rischio utente | Sì | No | No |
| Criteri di rischio di accesso | Sì | No | No |
| Report utenti a rischio | Accesso completo | Informazioni limitate | Informazioni limitate |
| Report sugli accessi a rischio | Accesso completo | Informazioni limitate | Informazioni limitate |
| Criteri di registrazione MFA | Yes | No | No |







## <a name="next-steps"></a>Passaggi successivi 

Per iniziare a usare Identity Protection, vedere [Configurare i criteri di rischio di accesso](quickstart-sign-in-risk-policy.md). 






