---
title: "Informazioni di riferimento sui report dell'autenticazione a più fattori nel portale di Azure | Microsoft Docs"
description: "Informazioni di riferimento sui report dell'autenticazione a più fattori nel portale di Azure"
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
ms.date: 10/24/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 8e4fc5c9eb031beef42b52c771ef88ea80dba068
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="reference-for-multi-factor-authentication-reporting-in-the-azure-portal"></a>Informazioni di riferimento sui report dell'autenticazione a più fattori nel portale di Azure

Con i [report di Azure Active Directory (Azure AD)](active-directory-reporting-azure-portal.md) nel [portale di Azure](https://portal.azure.com) è possibile ottenere le informazioni necessarie per determinare lo stato e le prestazioni dell'ambiente.

I [report delle attività di accesso](active-directory-reporting-activity-sign-ins.md) forniscono informazioni sull'utilizzo delle applicazioni gestite e sulle attività di accesso, tra cui informazioni sull'utilizzo dell'autenticazione a più fattori. 

I dati sull'autenticazione a più fattori forniscono informazioni approfondite sul funzionamento di questo tipo di autenticazione nell'organizzazione. Questi dati consentono di rispondere a domande simili alle seguenti: 

- L'accesso è stato verificato con l'autenticazione a più fattori? 

- In che modo l'utente ha completato l'autenticazione a più fattori? 

- Perché l'utente non è riuscito a completare l'autenticazione a più fattori?  

Aggregando tutti i dati sull'accesso, è possibile esaminare al meglio l'esperienza di autenticazione a più fattori all'interno dell'organizzazione. Questi dati consentono di rispondere a domande simili alle seguenti: 

- Quanti utenti vengono verificati tramite l'autenticazione a più fattori?  

- Quanti utenti non riescono a completare la verifica tramite l'autenticazione a più fattori? 

- Quali sono i problemi comuni relativi all'autenticazione a più fattori riscontrati dagli utenti finali? 


Questi dati sono disponibili tramite il portale di Azure e l'[API di creazione report](active-directory-reporting-api-getting-started-azure-portal.md). 


## <a name="data-structure"></a>Struttura dei dati


I report delle attività di accesso per l'autenticazione a più fattori permettono di accedere alle informazioni seguenti:

**Autenticazione MFA obbligatoria:** specifica se l'autenticazione a più fattori è o meno obbligatoria per l'accesso. L'autenticazione a più fattori può essere obbligatoria per motivi di autenticazione a più fattori per singoli utenti, accesso condizionale o altri motivi. I possibili valori sono `Yes` o `No`.

**Metodo autenticazione a più fattori:** metodo di autenticazione usato dall'utente per completare l'autenticazione a più fattori. I valori possibili sono: 

- SMS 

- Notifica dell'app per dispositivi mobili 

- Chiamata telefonica (telefono per l'autenticazione) 

- Codice di verifica dell'app per dispositivi mobili 

- Chiamata telefonica (telefono ufficio) 

- Chiamata telefonica (telefono per l'autenticazione alternativo) 

**Dettagli autenticazione a più fattori:** versione parzialmente nascosta del numero di telefono, ad esempio +XX XXXXXXXX64. 

**Risultato autenticazione MFA:** altre informazioni che specificano se l'autenticazione a più fattori è riuscita o è stata negata:

- Se l'autenticazione a più fattori è riuscita, questa colonna fornisce più informazioni riguardo al modo in cui è stata completata. 

- Se l'autenticazione a più fattori è stata negata, questa colonna fornisce informazioni sul motivo del rifiuto. I possibili valori sono `Satisfied` o `Denied`. 

La sezione seguente elenca i possibili significati dei valori delle stringhe per il campo del risultato dell'autenticazione a più fattori.

## <a name="status-strings"></a>Stringhe di stato

Questa sezione elenca i possibili significati della stringa di stato del risultato dell'autenticazione a più fattori.

### <a name="satisfied-status-strings"></a>Stringhe di stato per l'autenticazione riuscita


- Azure Multi-Factor Authentication

    - Autenticazione completata nel cloud 

    - Autenticazione scaduta a causa dei criteri configurati nel tenant 

    - Registrazione richiesta 

    - Autenticazione riuscita tramite attestazione nel token 

    - Autenticazione riuscita tramite attestazione nel token 

    - Autenticazione riuscita tramite attestazione nel token 

    - Autenticazione riuscita tramite attestazione nel token 

    - Autenticazione riuscita tramite attestazione fornita dal provider esterno 

    - Autenticazione riuscita tramite autenticazione avanzata 

    - Autenticazione ignorata perché il flusso eseguito è un flusso di accesso del broker Windows 

    - Autenticazione ignorata perché il flusso eseguito è un flusso di accesso del broker Windows 

    - Autenticazione ignorata a causa della password di un'app 

    - Autenticazione ignorata a causa della posizione 

    - Autenticazione ignorata a causa di un dispositivo registrato 
    
    - Autenticazione ignorata a causa di un dispositivo memorizzato 

    - Autenticazione completata correttamente 

- Reindirizzamento al provider esterno per l'autenticazione a più fattori 

 
### <a name="denied-status-strings"></a>Stringhe di stato per l'autenticazione negata

Autenticazione di Azure Multi-Factor Authentication negata 

- Autenticazione in corso 

- Tentativo di autenticazione duplicato 

- Immissione di un codice non corretto troppe volte 

- Autenticazione non valida 

- Codice di verifica dell'app per dispositivi mobili non valido 

- Errore di configurazione 

- Chiamata telefonica reindirizzata alla segreteria 

- Numero di telefono in formato non valido 

- Errore del servizio 

- Non è stato possibile raggiungere il telefono dell'utente 

- Non è stato possibile inviare la notifica dell'app per dispositivi mobili al dispositivo 

- Non è stato possibile inviare la notifica dell'app per dispositivi mobili 

- L'utente ha rifiutato l'autenticazione 

- L'utente non ha risposto alla notifica dell'app per dispositivi mobili 

- L'utente non ha registrato alcun metodo di verifica 

- L'utente ha immesso un codice non corretto 

- L'utente ha immesso un PIN non corretto 

- L'utente ha chiuso il telefono senza completare l'autenticazione 

- L'utente è bloccato 

- L'utente non ha mai immesso il codice di verifica 

- Utente non trovato 
 
- Il codice di verifica è già stato usato una volta 



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Creazione di report in Azure Active Directory](active-directory-reporting-azure-portal.md).




























