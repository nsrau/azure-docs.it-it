---
title: Raccolta di Azure dei dati multi-Factor Authentication utente - Azure Active Directory
description: Quali informazioni vengono usate per autenticare gli utenti da Azure Multi-Factor Authentication?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0c22d4421aa984a9862f83b9be1095d548e5841
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314479"
---
# <a name="azure-multi-factor-authentication-user-data-collection"></a>Raccolta dati utente in Microsoft Azure Multi-Factor Authentication

Questo documento illustra come trovare informazioni utente raccolte dal server Microsoft Azure Multi-Factor Authentication (MFA Server) e Azure MFA (basato sul cloud) nel caso in cui si desidera rimuoverle.

[!INCLUDE [gdpr-hybrid-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="information-collected"></a>Informazioni raccolte

MFA Server, l'estensione del Server dei criteri di rete e l’adattatore Azure MFA AD FS di Windows Server 2016 raccolgono e archiviano le seguenti informazioni per 90 giorni.

Tentativi di autenticazione (utilizzati per la segnalazione e la risoluzione dei problemi):

- Timestamp
- Username
- Nome
- Cognome
- Indirizzo di posta elettronica
- Gruppo utenti
- Metodo di autenticazione (telefonata, SMS, app per dispositivi mobili, un token OATH)
- Modalità telefonata (Standard, PIN)
- Direzione SMS (unidirezionale e bidirezionale)
- Modalità SMS (OTP, OTP + PIN)
- Modalità app per dispositivi mobili (Standard, PIN)
- Modalità token mobili (Standard, PIN)
- Tipo di autenticazione
- Nome dell'applicazione
- Prefisso internazionale per chiamata primario
- Numero di telefono per chiamata primario
- Interno chiamata primario
- Autenticazione eseguita chiamata primaria
- Risultato chiamata primario
- Prefisso internazionale per chiamata di backup
- Numero di telefono per chiamata di backup
- Estensione chiamata di ba
- Autenticazione eseguita chiamata di backup
- Risultato della chiamata di backup
- Autenticazione complessiva eseguita
- Risultati complessivi
- Risultati
- Autenticata
- Risultato
- Indirizzo IP di avvio
- Dispositivi
- Token dispositivo
- Tipo di dispositivo
- Versione dell'app per dispositivi mobili
- Versione sistema operativo
- Risultato
- Cerca notifiche usato

Attivazioni (tentativi di attivare un account nell'app per dispositivi mobili Microsoft Authenticator):
- Username
- Nome account
- Timestamp
- Ottenere il risultato del codice di attivazione
- Attivare l'esito positivo
- Attivare l'errore
- Risultato dello stato di attivazione
- Nome dispositivo
- Tipo di dispositivo
- Versione dell'app
- Token OATH abilitato

Blocchi (usato per determinare lo stato bloccato e creare report):

- Timestamp blocco
- Blocco dal nome utente
- Username
- Prefisso internazionale
- Numero di telefono
- Numero di telefono formattato
- Estensione
- Pulisci estensione
- Bloccato
- Motivo per blocco
- Timestamp di completamento
- Risultati di completamento
- Blocco account
- Avviso di illecito
- Avviso di illecito non bloccato
- Linguaggio

Bypass (usato per la creazione di report):

- Timestamp di bypass
- Secondi bypass
- Bypass dal nome utente
- Username
- Prefisso internazionale
- Numero di telefono
- Numero di telefono formattato
- Estensione
- Pulisci estensione
- Motivo per bypass
- Timestamp di completamento
- Risultati di completamento
- Bypass usato

Modifiche (usate per sincronizzare le modifiche utente al server Multi-Factor Authentication o AAD):

- Timestamp di modifica
- Username
- Nuovo prefisso internazionale
- Nuovo numero di telefono
- Nuovo interno
- Nuovo prefisso internazionale di backup
- Nuovo numero di telefono di backup
- Nuovo interno di backup
- Nuovo PIN
- Modifica PIN obbligatoria
- Token dispositivo precedente
- Nuovo token dispositivo

## <a name="gather-data-from-mfa-server"></a>Raccogliere dati dal server Multi-Factor Authentication

Nella versione 8.0 o successiva di MFA Server, la seguente procedura consente agli amministratori di esportare tutti i dati per gli utenti:

- Accedere a MFA Server, passare alla scheda **Utenti**, selezionare l'utente in questione e fare clic sul pulsante **Modifica**. Acquisire schermate (Alt-Stamp) di ogni scheda per fornire all'utente le relative impostazioni correnti di Multi-Factor Authentication.
- Dalla riga di comando del server MFA, eseguire il seguente comando di modifica del percorso in base all'installazione `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe export <username>` per produrre un file JSON formattato.
- Gli amministratori hanno anche la possibilità di usare l'operazione di SDK GetUserGdpr del servizio Web per esportare tutte le informazioni del servizio cloud MFA raccolte per un determinato utente o incorporarle in una soluzione di report maggiore.
- Cercare `C:\Program Files\Multi-Factor Authentication Server\Logs\MultiFactorAuthSvc.log` ed eventuali backup per "<username>" (includere le virgolette nella ricerca) per trovare tutte le istanze del record utente che sono state aggiunte o modificate.
   - Questi record possono essere limitati (ma non vengono eliminati) deselezionando **"Modifiche dell'utente di log"** nell'esperienza utente del server di MFA, sezione di registrazione, scheda File di log.
   - Se syslog è configurato e **"Modifiche dell'utente di log"** viene archiviato nell'esperienza utente di MFA, sezione di registrazione, scheda Syslog, le voci di log possono essere raccolte da syslog.
- Altre occorrenze di nome utente nei file MultiFactorAuthSvc.log e altri file di log del server MFA relativi ai tentativi di autenticazione sono considerate operative e duplicati delle informazioni fornite usando l'esportazioneMultiFactorAuthGdpr.exe o il kit SDK GetUserGdpr del servizio Web.

## <a name="delete-data-from-mfa-server"></a>Eliminare dati dal server MFA

Dalla riga di comando del server MFA, eseguire il seguente comando di modifica del percorso in base all'installazione `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe delete <username>` per eliminare tutte le informazioni del servizio cloud MFA raccolte per un determinato utente.

- I dati inclusi nell'esportazione verranno eliminati in tempo reale, ma la rimozione completa dei dati operativi o duplicati potrebbe richiedere fino a 30 giorni.
- Gli amministratori hanno anche la possibilità di usare l'operazione di SDK GetUserGdpr del servizio Web per eliminare tutte le informazioni del servizio cloud MFA raccolte per un determinato utente o incorporarle in una soluzione di report maggiore.

## <a name="gather-data-from-nps-extension"></a>Raccogliere i dati dall'estensione del Server di criteri di rete

Usare il [portale sulla Privacy di Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) per effettuare una richiesta di esportazione.

- Le informazioni di autenticazione a più fattori sono incluse nell'esportazione, che potrebbe richiedere ore o giorni per il completamento.
- Le occorrenze del nome utente nei log eventi AzureMfa/AuthN/AuthNOptCh, AzureMfa/AuthZ/AuthZAdminCh e AzureMfa/AuthZ/AuthZOptCh sono considerate operative e duplicate delle informazioni fornite nell'esportazione.

## <a name="delete-data-from-nps-extension"></a>Eliminare i dati dall'estensione del Server di criteri di rete

Usare il [portale sulla Privacy di Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) per effettuare una richiesta a Chiudi Account per eliminare tutte le informazioni del servizio cloud MFA raccolte per un determinato utente.

- La rimozione completa dei dati potrebbe richiedere fino a 30 giorni.

## <a name="gather-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Raccogliere i dati dall’adattatore MFA AD FS di Windows Server 2016

Usare il [portale sulla Privacy di Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) per effettuare una richiesta di esportazione. 

- Le informazioni di autenticazione a più fattori sono incluse nell'esportazione, che potrebbe richiedere ore o giorni per il completamento.
- Le occorrenze del nome utente nei log eventi di traccia/debug di AD FS (se abilitati) vengono considerate operativo e duplicate delle informazioni fornite nell'esportazione.

## <a name="delete-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Eliminare i dati dall’adattatore MFA AD FS di Windows Server 2016

Usare il [portale sulla Privacy di Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) per effettuare una richiesta a Chiudi Account per eliminare tutte le informazioni del servizio cloud MFA raccolte per un determinato utente.

- La rimozione completa dei dati potrebbe richiedere fino a 30 giorni.

## <a name="gather-data-for-azure-mfa"></a>Raccogliere i dati per Azure MFA

Usare il [portale sulla Privacy di Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) per effettuare una richiesta di esportazione.

- Le informazioni di autenticazione a più fattori sono incluse nell'esportazione, che potrebbe richiedere ore o giorni per il completamento.

## <a name="delete-data-for-azure-mfa"></a>Eliminare i dati per Azure MFA

Usare il [portale sulla Privacy di Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) per effettuare una richiesta a Chiudi Account per eliminare tutte le informazioni del servizio cloud MFA raccolte per un determinato utente.

- La rimozione completa dei dati potrebbe richiedere fino a 30 giorni.

## <a name="next-steps"></a>Passaggi successivi

[Creazione di report nel server MFA](howto-mfa-reporting.md)
