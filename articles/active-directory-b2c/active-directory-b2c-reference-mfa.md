<properties
	pageTitle="Anteprima di Azure Active Directory B2C: autenticazione a più fattori | Microsoft Azure"
	description="Come abilitare la Multi-Factor Authentication in applicazioni consumatori protette da Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="swkrish"/>

# Anteprima di Azure Active Directory B2C: abilitare l'autenticazione a più fattori nelle applicazioni dei consumatori

Azure Active Directory (AD) B2C si integra direttamente con [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) per semplificarlo, aggiungere un secondo livello di protezione alle esperienze di iscrizione e di accesso a nelle applicazioni utilizzate dagli utenti. Questo è possibile senza scrivere una singola riga di codice. Attualmente sono supportati la telefonata e l’SMS come opzioni di verifica. Se sono già stati creati i criteri per l’iscrizione e l’accesso (come descritto in [questo articolo]()), è possibile attivare l'autenticazione a più fattori come illustrato nelle sezioni successive.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]La Multi-Factor Authentication può anche essere attivata durante la creazione di criteri per l'iscrizione e l'accesso, non solo modificando i criteri esistenti.

Utilizzando questa funzionalità, le applicazioni possono gestire degli scenari come il seguente, dove:

- Non si richiede l'autenticazione a più fattori per accedere a un'applicazione, ma la si richiede per accedere a un’altra. Ad esempio, l’utente può accedere a una richiesta di assicurazione automatica con un account locale o di social, ma è necessario verificare il numero di telefono prima di accedere all'applicazione di assicurazione home registrata nella stessa directory.
- Non si richiede l'autenticazione a più fattori per accedere a un'applicazione in generale, ma si richiede l'accesso alle parti sensibili in esso contenute. Ad esempio, l’utente può accedere a un'applicazione bancaria con un account locale o di social e controllare il saldo del conto, ma è necessario verificare il numero di telefono prima di effettuare un bonifico.

## Modificare i criteri di iscrizione per abilitare la Multi-Factor Authentication

1. [Passare al pannello delle funzionalità B2C nel portale di anteprima di Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Fare clic su **Criteri di iscrizione**.
3. Aprire il criterio di iscrizione (ad esempio, "B2C\_1\_SiUp") facendo clic su di esso.
4. Fare clic su **autenticazione a più fattori** e impostare lo **stato** su **ON**. Fare clic su **OK**.
5. Fare clic su **Salva** nella parte superiore del pannello. L'operazione è completata.

È possibile utilizzare la funzionalità "Esegui ora" nei criteri per verificare l'esperienza utente. Questo è ciò che si prevede di vedere:

Un account utente viene creato nella directory prima che si verifichi il passaggio di autenticazione a più fattori. Durante il passaggio, all’utente viene richiesto di fornire il proprio numero di telefono e di verificarlo. Se la verifica ha esito positivo, il numero di telefono viene associato all'account utente per un utilizzo successivo. Anche se l'utente annulla o esce, gli può venire richiesto di verificare un numero di telefono nuovamente durante il successivo accesso (con autenticazione a più fattori abilitato; vedere la sezione successiva).

## Modificare i criteri di accesso per abilitare la Multi-Factor Authentication

1. Passare al pannello delle funzionalità B2C nel [portale di anteprima di Azure](htts://portal.azure.com/). Leggere [qui](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) per sapere come eseguire questa operazione.
2. Fare clic su **Criteri di accesso**.
3. Aprire il criterio di iscrizione (ad esempio, "B2C\_1\_SiIn") facendo clic su di esso. Fare clic su **Modifica** nella parte superiore del pannello.
4. Fare clic su **autenticazione a più fattori** e impostare lo **stato** su **ON**. Fare clic su **OK**.
5. Fare clic su **Salva** nella parte superiore del pannello. L'operazione è completata.

È possibile utilizzare la funzionalità "Esegui ora" nei criteri per verificare l'esperienza utente. Questo è ciò che si prevede di vedere:

Quando l’utente si iscrive (utilizzando un account locale o social), se un numero di telefono verificato è collegato all'account utente, viene richiesto di verificarlo. Se non è collegato alcun numero di telefono, all’utente viene richiesto di fornirne uno e di verificarlo. Una volta verificato, il numero di telefono è collegato all'account utente per un utilizzo successivo.

<!---HONumber=Sept15_HO3-->