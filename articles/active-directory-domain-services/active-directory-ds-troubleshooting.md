<properties
	pageTitle="Anteprima di Servizi di dominio di Azure Active Directory: Guida alla risoluzione dei problemi | Microsoft Azure"
	description="Guida alla risoluzione dei problemi di Servizi di dominio di Azure AD"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="udayh"
	editor="inhenk"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/16/2015"
	ms.author="maheshu"/>

# Servizi di dominio di Azure AD *(Anteprima)* - Guida alla risoluzione dei problemi
Questo articolo offre suggerimenti per la risoluzione dei problemi che possono verificarsi quando si configura o si amministra Servizi di dominio di Azure AD.


### Impossibile accedere al dominio gestito di Servizi di dominio di Azure AD
Se si verifica una situazione in cui uno o più utenti nel tenant di Azure AD non sono in grado di accedere al dominio gestito appena creato, seguire questa procedura di risoluzione dei problemi:

- Assicurarsi di avere [abilitato la sincronizzazione delle password](active-directory-ds-getting-started-password-sync.md) secondo i passaggi descritti nella Guida introduttiva.

- Assicurarsi che l'account utente interessato non sia un account esterno nel tenant di Azure AD. Esempi di account esterni sono gli account Microsoft (ad esempio, 'luca@live.com') o gli account utente di una directory esterna di Azure AD. Servizi di dominio di Azure AD non dispone di credenziali per questo tipo di account utente, pertanto questi utenti non sono in grado di accedere al dominio gestito.

- **Account sincronizzati:** se gli account utente interessati sono sincronizzati da una directory locale, assicurarsi di seguire i passaggi seguenti:
    - È stata eseguita la distribuzione o l'aggiornamento alla versione GA di Azure AD Connect. Le versioni precedenti non eseguiranno la sincronizzazione degli hash delle credenziali necessari per l'autenticazione NTLM o Kerberos.
    - È stata creata la chiave del registro di sistema necessaria per abilitare la sincronizzazione delle credenziali legacy ad Azure AD.
    - Dopo la creazione della chiave del registro di sistema sopra indicata nel server che esegue Azure AD Connect, Azure AD dovrà necessariamente eseguire una sincronizzazione completa, come descritto nel documento.
    - A seconda delle dimensioni della directory, potrebbero essere necessari alcuni minuti prima che gli account utente e gli hash delle credenziali siano disponibili in Servizi di dominio di Azure AD. Assicurarsi di attendere il tempo sufficiente prima di ritentare l'autenticazione (il tempo può variare a seconda delle dimensioni della directory - da alcune ore fino a un giorno o due per directory di grandi dimensioni).

- **Account solo cloud**: se l'account utente interessato è un account utente solo cloud, assicurarsi che l'utente abbia modificato la password dopo l'abilitazione di Servizi di dominio di Azure AD. Questa operazione comporta la generazione degli hash delle credenziali necessari per Servizi di dominio di Azure AD.


### Contattaci
In caso di problemi con il dominio gestito, verificare se è possibile risolvere il problema tramite i passaggi descritti in questa Guida alla risoluzione dei problemi. Se si verificano ancora problemi, è possibile contattare Microsoft tramite:

- Il [forum dedicato di Azure Active Directory](http://feedback.azure.com/forums/169401-azure-active-directory). Per contattare Microsoft, assicurarsi di anteporre alla domanda le parole **'AADDS'**.
- È inoltre possibile contattare Microsoft all'indirizzo di posta elettronica per il [feedback su Servizi di dominio di Azure AD](mailto:aaddsfb@microsoft.com)

<!---HONumber=Oct15_HO4-->