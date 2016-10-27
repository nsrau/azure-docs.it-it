<properties
    pageTitle="Servizi di dominio Azure Active Directory: Guida alla risoluzione dei problemi | Microsoft Azure"
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
    ms.date="10/12/2016"
    ms.author="maheshu"/>


# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Guida alla risoluzione dei problemi di Servizi di dominio Azure Active Directory
Questo articolo offre suggerimenti per la risoluzione dei problemi che possono verificarsi quando si configura o si amministra Servizi di dominio di Azure Active Directory (AD).


### <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Non è possibile abilitare i Servizi di dominio Azure AD per la directory Azure AD
Se il tentativo di abilitazione di Servizi di dominio Azure AD per la directory ha esito negativo o lo stato viene impostato su "Disabilitato", seguire questa procedura per la risoluzione dei problemi:

- Assicurarsi che non sia presente un dominio esistente con lo stesso nome di dominio disponibile nella rete virtuale. Ad esempio, si supponga che un dominio denominato 'contoso.com' sia già disponibile nella rete virtuale selezionata. Provare successivamente ad abilitare un dominio gestito di Servizi di dominio Azure AD con lo stesso nome di dominio, ovvero "contoso.com", alla rete virtuale. Si verifica un errore quando si prova ad abilitare Azure AD Domain Services. L'errore è dovuto a conflitti di nomi per il nome di dominio nella rete virtuale. In questa situazione è necessario usare un nome diverso per configurare il dominio gestito di Servizi di dominio Azure AD. In alternativa, è possibile eseguire il deprovisioning del dominio esistente e quindi abilitare Servizi di dominio Azure AD.

- Verificare se nella directory Azure AD è presente un'applicazione con nome 'Azure AD Domain Services Sync'. Se l'applicazione esiste, sarà necessario eliminarla e quindi abilitare di nuovo Servizi di dominio Azure AD. Per verificare se l'applicazione è presente e per eliminarla, se esiste, seguire questa procedura:

  1. Passare al **portale di Azure classico** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
  2. Selezionare il nodo **Active Directory** nel riquadro sinistro.
  3. Selezionare il tenant di Azure AD (directory) per il quale si desidera abilitare Servizi di dominio Azure AD.
  4. Passare alla scheda **Applicazioni** .
  5. Selezionare l'opzione **Applicazioni di proprietà dell'azienda** nell'elenco a discesa.
  6. Cercare un'applicazione denominata **Azure AD Domain Services Sync**. Se l'applicazione esiste, eliminarla.
  7. Dopo avere eliminato l'applicazione, provare ad abilitare di nuovo Servizi di dominio Azure AD.


### <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Impossibile accedere al dominio gestito di Servizi di dominio di Azure AD
Se uno o più utenti nel tenant di Azure AD non sono in grado di accedere al dominio gestito appena creato, seguire questa procedura di risoluzione dei problemi:

- Provare a effettuare l'accesso usando il formato UPN (ad esempio 'joeuser@contoso.com')) anziché il formato SAMAccountName ("CONTOSO\joeuser"). In alcuni casi l'attributo SAMAccountName può essere generato automaticamente per gli utenti il cui prefisso UPN è troppo lungo o è identico a un altro utente nel dominio gestito. Il formato UPN è garantito come univoco all'interno di un tenant di Azure AD.

> [AZURE.NOTE] Si consiglia di usare il formato UPN per accedere al dominio gestito di Servizi di dominio Azure AD.

- Assicurarsi di avere [abilitato la sincronizzazione delle password](active-directory-ds-getting-started-password-sync.md) secondo i passaggi descritti nella Guida introduttiva.

- **Account esterni** : assicurarsi che l'account utente interessato non sia un account esterno nel tenant di Azure AD. Esempi di account esterni sono gli account Microsoft (ad esempio 'joe@live.com')) o gli account utente di una directory esterna di Azure AD. Servizi di dominio di Azure AD non dispone di credenziali per questo tipo di account utente, pertanto questi utenti non sono in grado di accedere al dominio gestito.

- **Account sincronizzati:** : se gli account utente interessati sono sincronizzati da una directory locale, verificare quanto segue:
    - È stata eseguita la distribuzione o l'aggiornamento all' [ultima versione consigliata di Azure AD Connect](active-directory-ds-getting-started-password-sync.md#install-or-update-azure-ad-connect).

    - Azure AD Connect è stato configurato per [eseguire una sincronizzazione completa](active-directory-ds-getting-started-password-sync.md).

    - A seconda delle dimensioni della directory, potrebbero essere necessari alcuni minuti prima che gli account utente e gli hash delle credenziali siano disponibili in Servizi di dominio di Azure AD. Assicurarsi di attendere il tempo sufficiente prima di ritentare l'autenticazione (il tempo può variare a seconda delle dimensioni della directory - da alcune ore fino a un giorno o due per directory di grandi dimensioni).

    - Se il problema persiste dopo la verifica dei passaggi precedenti, provare a riavviare il servizio Microsoft Azure AD Sync. Dal computer di sincronizzazione avviare un prompt dei comandi ed eseguire i comandi seguenti:
      1. net stop 'Microsoft Azure AD Sync'
      2. net start 'Microsoft Azure AD Sync'

- **Account solo cloud**: se l'account utente interessato è un account utente solo cloud, assicurarsi che l'utente abbia modificato la password dopo l'abilitazione di Servizi di dominio di Azure AD. Questa operazione comporta la generazione degli hash delle credenziali necessari per Servizi di dominio di Azure AD.


### <a name="contact-us"></a>Contattaci
Contattare il team di prodotto di Servizi di dominio Azure AD per [condividere commenti e suggerimenti o per chiedere supporto tecnico] (active-directory-ds-contact-us.md).



<!--HONumber=Oct16_HO2-->


