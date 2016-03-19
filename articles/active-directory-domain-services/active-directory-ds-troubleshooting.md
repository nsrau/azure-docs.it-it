<properties
	pageTitle="Anteprima di Servizi di dominio di Azure Active Directory: Guida alla risoluzione dei problemi | Microsoft Azure"
	description="Guida alla risoluzione dei problemi di Servizi di dominio di Azure AD"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/26/2016"
	ms.author="maheshu"/>

# Servizi di dominio di Azure AD *(Anteprima)* - Guida alla risoluzione dei problemi
Questo articolo offre suggerimenti per la risoluzione dei problemi che possono verificarsi quando si configura o si amministra Servizi di dominio di Azure Active Directory (AD).


### Non è possibile abilitare i Servizi di dominio Azure AD per la directory Azure AD
Se si verifica una situazione in cui il tentativo di abilitazione di Servizi di dominio Azure AD per la directory ha esito negativo o lo stato viene impostato su 'Disabilitato', seguire questa procedura per la risoluzione dei problemi:

- Assicurarsi che non sia presente un dominio esistente con lo stesso nome di dominio disponibile nella rete virtuale. Ad esempio, si supponga che un dominio denominato 'contoso.com' sia già disponibile nella rete virtuale selezionata. Successivamente provare ad abilitare un dominio gestito di Servizi di dominio Azure AD con lo stesso nome di dominio, ovvero 'contoso.com', alla rete virtuale. Si verificherà un errore quando si prova ad abilitare Servizi di dominio Azure AD. L'errore è dovuto a conflitti di nomi per il nome di dominio nella rete virtuale. In questa situazione è necessario usare un nome diverso per configurare il dominio gestito di Servizi di dominio Azure AD. In alternativa, è possibile eseguire il deprovisioning del dominio esistente e quindi abilitare Servizi di dominio Azure AD.

- Verificare se nella directory Azure AD è presente un'applicazione con nome 'Azure AD Domain Services Sync'. Se l'applicazione esiste, sarà necessario eliminarla e quindi abilitare di nuovo Servizi di dominio Azure AD. Seguire questa procedura per verificare se l'applicazione è presente e per eliminarla, se esiste:

  1. Accedere al **portale di gestione di Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
  2. Selezionare il nodo **Active Directory** nel riquadro sinistro.
  3. Selezionare il tenant di Azure AD (directory) per il quale si desidera abilitare Servizi di dominio Azure AD.
  4. Passare alla scheda **Applicazioni**.
  5. Selezionare l'opzione **Applicazioni di proprietà dell'azienda** nell'elenco a discesa.
  6. Cercare un'applicazione denominata **Azure AD Domain Services Sync**. Se l'applicazione esiste, eliminarla.
  7. Dopo avere eliminato l'applicazione, provare ad abilitare di nuovo Servizi di dominio Azure AD.


### Impossibile accedere al dominio gestito di Servizi di dominio di Azure AD
Se si verifica una situazione in cui uno o più utenti nel tenant di Azure AD non sono in grado di accedere al dominio gestito appena creato, seguire questa procedura di risoluzione dei problemi:

- Assicurarsi di avere [abilitato la sincronizzazione delle password](active-directory-ds-getting-started-password-sync.md) secondo i passaggi descritti nella Guida introduttiva.

- Assicurarsi che l'account utente interessato non sia un account esterno nel tenant di Azure AD. Esempi di account esterni sono gli account Microsoft (ad esempio, 'luca@live.com') o gli account utente di una directory esterna di Azure AD. Servizi di dominio di Azure AD non dispone di credenziali per questo tipo di account utente, pertanto questi utenti non sono in grado di accedere al dominio gestito.

- Assicurarsi che il prefisso UPN dell'account utente interessato (ad esempio la prima parte dell’UPN) nel tenant di Azure AD non superi i 20 caratteri. Ad esempio, per l'UPN 'joereallylongnameuser@contoso.com', il prefisso ('joereallylongnameuser') supera i 20 caratteri e questo account non sarà disponibile nel dominio gestito di Servizi di dominio di Azure AD.

- **Account sincronizzati:** se gli account utente interessati sono sincronizzati da una directory locale, verificare quanto segue:
    - È stata eseguita la distribuzione o l'aggiornamento all'[ultima versione consigliata di Azure AD Connect](active-directory-ds-getting-started-password-sync.md#install-or-update-azure-ad-connect).
    - Azure AD Connect è stato configurato per [eseguire una sincronizzazione completa](active-directory-ds-getting-started-password-sync.md).
    - A seconda delle dimensioni della directory, potrebbero essere necessari alcuni minuti prima che gli account utente e gli hash delle credenziali siano disponibili in Servizi di dominio di Azure AD. Assicurarsi di attendere il tempo sufficiente prima di ritentare l'autenticazione (il tempo può variare a seconda delle dimensioni della directory - da alcune ore fino a un giorno o due per directory di grandi dimensioni).

- **Account solo cloud**: se l'account utente interessato è un account utente solo cloud, assicurarsi che l'utente abbia modificato la password dopo l'abilitazione di Servizi di dominio di Azure AD. Questa operazione comporta la generazione degli hash delle credenziali necessari per Servizi di dominio di Azure AD.


### Contattaci
In caso di problemi con il dominio gestito, verificare se è possibile risolvere il problema tramite i passaggi descritti in questa Guida alla risoluzione dei problemi. Se si verificano ancora problemi, è possibile contattare Microsoft tramite:

- **Posta elettronica:** è possibile contattare Microsoft all'indirizzo di posta elettronica per [commenti su Servizi di dominio di Azure AD](mailto:aaddsfb@microsoft.com). Assicurarsi di includere l'ID tenant per la directory Azure AD e il nome di dominio configurato per Servizi di dominio AAD, per consentire a Microsoft di esaminare il problema.
- **[Suggerimenti degli utenti per Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/):** per contattare Microsoft, assicurarsi di anteporre alla domanda le parole **'AADDS'**.

<!---HONumber=AcomDC_0128_2016-->