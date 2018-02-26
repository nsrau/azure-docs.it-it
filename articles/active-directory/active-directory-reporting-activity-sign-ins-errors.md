---
title: "Codici di errore del report delle attività di accesso nel portale di Azure Active Directory | Microsoft Docs"
description: "Informazioni di riferimento sui codici di errore del report delle attività di accesso."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/09/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 1e5d477aa74d3d0588e33363888d84e860d4b2f8
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2018
---
# <a name="sign-in-activity-report-error-codes-in-the-azure-active-directory-portal"></a>Codici di errore del report delle attività di accesso nel portale di Azure Active Directory

Le informazioni contenute nel report relativo agli accessi utente consentono di rispondere a domande come le seguenti:

- Chi ha eseguito l'accesso tramite Azure Active Directory?
- A quali app è stato eseguito l'accesso?
- Quali accessi hanno avuto esito negativo e perché?

Questo argomento contiene un elenco dei codici di errore e le relative descrizioni. 

## <a name="how-can-i-display-failed-sign-ins"></a>Come è possibile visualizzare gli accessi non riusciti? 

Il primo punto di ingresso a tutte le attività di accesso è **[Accessi](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)** nella sezione **Attività** di **Azure Active**.


![Attività di accesso](./media/active-directory-reporting-activity-sign-ins-errors/61.png "Attività di accesso")

Il report sugli accessi permette di visualizzare gli accessi non riusciti selezionando **Errore** come **Stato accesso**.

![Attività di accesso](./media/active-directory-reporting-activity-sign-ins-errors/06.png "Attività di accesso")

Facendo clic su un elemento nell'elenco visualizzato, verrà aperto il pannello **Dettagli attività: accessi**. Questa visualizzazione contiene tutti i dettagli di cui Azure Active Directory tiene traccia riguardo agli accessi, incluso il **Codice di errore di accesso** e il **Motivo dell'errore**.

![Attività di accesso](./media/active-directory-reporting-activity-sign-ins-errors/05.png "Attività di accesso")


In alternativa al portale di Azure per accedere ai dati di accesso, è possibile usare l'[API di creazione report](active-directory-reporting-api-getting-started-azure-portal.md).


La sezione seguente offre una panoramica completa di tutti gli errori possibili e delle relative descrizioni. 

## <a name="error-codes"></a>Codici di errore

|Tipi di errore|DESCRIZIONE|
|---|---|
|50001|L'entità servizio denominata X non è stata trovata nel tenant denominato Y. Questa situazione può verificarsi se l'applicazione non è stata installata dall'amministratore del tenant. In alternativa, l'entità di sicurezza della risorsa non è stata trovata nella directory oppure non è valida.|
|50008|L'asserzione SAML manca o non è configurata correttamente nel token.|
|50011|L'indirizzo di risposta manca, non è configurato correttamente o non corrisponde agli indirizzi di risposta configurati per l'applicazione.|
|50012|L'utente ha segnalato un illecito durante l'autenticazione a più fattori.|
|50053|L'account è bloccato perché l'utente ha cercato di accedere troppe volte con una combinazione non corretta di ID utente e password.|
|50054|Per l'autenticazione è stata usata la password precedente.|
|50055|È stata immessa una password non valida o scaduta.|
|50057|L'account utente è disabilitato.|
|50058|Non sono state trovate informazioni sull'identità dell'utente tra le credenziali specificate. Oppure, l'utente non è stato trovato nel tenant. Oppure, è stata inviata una richiesta di accesso automatica ma nessun utente ha eseguito l'accesso. Oppure, il servizio non è riuscito ad autenticare l'utente.|
|50074|L'utente non ha superato la richiesta di verifica MFA.|
|50079|L'utente deve registrarsi per l'autenticazione del secondo fattore.|
|50089|La convalida del token di flusso non è riuscita perché il token di flusso è scaduto.|
|50097|Il dispositivo non è autenticato.|
|50105|L'utente connesso non è assegnato a un ruolo per questa applicazione.|
|50126|Sono stati usati un nome utente o una password non validi oppure un nome utente o una password locali non validi.|
|50131|Usato in diversi errori di accesso condizionale. Ad esempio, in caso di stato del dispositivo Windows non valido, in caso di richiesta bloccata a causa di un'attività sospetta oppure per decisioni relative a criteri di accesso e criteri di sicurezza.|
|50133|La sessione non è valida perché è scaduta o la password è stata modificata di recente.|
|50144|La password di Active Directory dell'utente è scaduta.|
|65001|L'applicazione X non è autorizzata ad accedere all'applicazione Y o l'autorizzazione è stata revocata. Oppure, l'utente o l'amministratore non ha acconsentito all'uso dell'applicazione con ID X. Inviare una richiesta di autorizzazione interattiva per questo utente e questa risorsa. Oppure, l'utente o l'amministratore non ha acconsentito all'uso dell'applicazione con ID X. Inviare all'amministratore del tenant una richiesta di autorizzazione ad agire per conto dell'applicazione Y per la risorsa Z.|
|65005|L'elenco di accesso alle risorse necessario per l'applicazione non contiene applicazioni individuabili dalla risorsa. Oppure, l'applicazione client ha richiesto l'accesso a una risorsa non specificata nell'elenco di accesso alle risorse necessario. Oppure, il servizio Graph ha restituito una richiesta non valida o la risorsa non è stata trovata.|
|70001|L'applicazione denominata X non è stata trovata nel tenant denominato Y. Questa situazione può verificarsi se l'applicazione non è stata installata dall'amministratore del tenant o non è consentita da uno degli utenti nel tenant. La richiesta di autenticazione potrebbe essere stata inviata al tenant sbagliato.|
|80001|L'agente di autenticazione non è in grado di connettersi ad Active Directory.|
|80002|Timeout della richiesta di convalida della password dell'agente di autenticazione.|
|80003|Risposta non valida ricevuta dall'agente di autenticazione.|
|80004|È stato usato un nome dell'entità utente (UPN) non corretto nella richiesta di accesso.|
|80005|Agente di autenticazione: si è verificato un errore.|
|80007|L'agente di autenticazione non riesce a convalidare la password dell'utente.|
|80010|L'agente di autenticazione non è in grado di decrittografare la password.|
|80011|L'agente di autenticazione non è in grado di recuperare la chiave di decrittografia.|
|81001|Il ticket Kerberos dell'utente è troppo grande.|
|81002|Impossibile convalidare il ticket Kerberos dell'utente.|
|81003|Impossibile convalidare il ticket Kerberos dell'utente.|
|81004|Tentativo di autenticazione Kerberos non riuscito.|
|81008|Impossibile convalidare il ticket Kerberos dell'utente.|
|81009|Impossibile convalidare il ticket Kerberos dell'utente.|
|81010|Non è stato possibile usare l'accesso SSO facile perché il ticket Kerberos dell'utente è scaduto o non è valido.|
|81011|Impossibile trovare l'oggetto utente in base alle informazioni nel ticket Kerberos dell'utente.|
|81012|L'utente che sta tentando di accedere ad Azure AD è diverso dall'utente che ha eseguito l'accesso al dispositivo.|
|81013|Impossibile trovare l'oggetto utente in base alle informazioni nel ticket Kerberos dell'utente.|
|90014|Usato in diversi casi quando un campo previsto non è presente nelle credenziali.|
|90093|Graph ha restituito un codice di errore non consentito per la richiesta.|



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Report delle attività di accesso nel portale di Azure Active Directory](active-directory-reporting-activity-sign-ins.md).
