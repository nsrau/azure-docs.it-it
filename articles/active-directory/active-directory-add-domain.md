<properties
	pageTitle="Aggiungere un nome di dominio personalizzato per semplificare l'accesso con Azure Active Directory | Microsoft Azure"
	description="Informazioni su come aggiungere i nomi di dominio dell'azienda ad Azure Active Directory e come verificare il nome di dominio."
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/20/2016"
	ms.author="curtand;jeffsta"/>

# Aggiungere un nome di dominio personalizzato per semplificare l'accesso con Azure Active Directory

Quando inizialmente si ottiene la directory in Azure Active Directory (Azure AD), una delle prime attività importanti consiste nel verificare un nome di dominio personalizzato usato dall'organizzazione, ad esempio "contoso.com". Il completamento di questa attività consente di assegnare nomi familiari per gli utenti, ad esempio "alice@contoso.com". Finché non si verifica il nome di dominio personalizzato, gli utenti devono accedere con nomi quale "alice@contoso.onmicrosoft.com" che usa il nome di dominio iniziale della directory.

## Aggiungere un nome di dominio personalizzato alla directory

Per aggiungere un nome di dominio personalizzato alla directory:

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com/) con un account utente di amministratore globale per la directory di Azure AD.

2. Selezionare **Active Directory** sulla barra di spostamento a sinistra e aprire la directory.

4. Selezionare la scheda **Domini**.

5. Sulla barra dei comandi selezionare **Aggiungi**.

6. Immettere il nome del dominio personalizzato, ad esempio "contoso.com". Assicurarsi di includere .com, .net o altre estensioni di primo livello.

7. Se si intende configurare il dominio per l'[accesso federato](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) con l'istanza di Active Directory locale, selezionare la relativa casella di controllo.

8. Selezionare **Aggiungi**.

Perché sia possibile assegnare nomi utente che includono il nome di dominio personalizzato, Azure AD deve verificare che l'azienda sia proprietaria del nome di dominio. Per eseguire questa verifica, è necessario aggiungere una voce DNS nel file di zona DNS per il nome di dominio. Questa attività può essere eseguita nel sito Web del registrar per il nome di dominio.

## Ottenere le voci DNS per il nome di dominio

Se si è scelto di configurare il dominio per la federazione, si verrà indirizzati al download dello strumento Azure AD Connect. Eseguire lo strumento Azure AD Connect per [ottenere le voci DNS da aggiungere al registrar](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Se si non è scelto di configurare il dominio per la federazione, le voci DNS si trovano nella seconda pagina della procedura guidata **Aggiungi un dominio**.

## Aggiungere la voce DNS al file di zona DNS

Per aggiungere la voce DNS richiesta da Azure AD:

1.  Accedere al registrar per il dominio. Se non si hanno autorizzazioni sufficienti per aggiornare la voce DNS, chiedere alla persona o al team che ha l'accesso di aggiungerla.

2.  Per aggiornare il file di zona DNS per il dominio, aggiungere la voce DNS fornita da Azure AD. Questa voce DNS consente ad Azure AD di verificare la proprietà del dominio. La voce DNS non modifica alcun comportamento, ad esempio il routing della posta elettronica o l'hosting Web.

[Istruzioni sull'aggiunta di voci DNS ai registrar DNS comuni](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## Verificare il nome di dominio con Azure AD

Dopo aver aggiunto la voce DNS, è possibile verificare il nome di dominio con Azure AD.

Se si è scelto configurare la federazione del nome di dominio personalizzato, lo strumento Azure AD Connect esegue automaticamente la verifica. Dopo aver completato i prerequisiti, eseguire lo strumento. In caso contrario, verificare il dominio nel portale di Azure classico. Se la procedura guidata **Aggiungi un dominio** è ancora aperta, è possibile fare clic su **Verifica** nella terza pagina della procedura guidata. La propagazione dei record DNS può richiedere fino a un'ora.

Se la procedura guidata **Aggiungi un dominio** è stata chiusa, è possibile verificare il dominio nel [portale di Azure classico](https://manage.windowsazure.com/):

1.  Accedere con un account utente amministratore globale per la directory di Azure AD.

2.  Selezionare **Active Directory** sulla barra di spostamento a sinistra.

3.  Aprire la directory.

4.  Selezionare la scheda **Domini**.

5.  Selezionare nell'elenco il dominio da verificare.

6.  Sulla barra dei comandi selezionare **Verifica**.

7.  Nella finestra di dialogo selezionare **Verifica**.

A questo punto è possibile [assegnare nomi utente che includono il nome di dominio personalizzato](active-directory-add-domain-add-users.md).

## Aggiungere altri nomi di dominio personalizzati

Se l'organizzazione usa più nome di dominio personalizzati, ad esempio "contoso.com" e "contosobank.com", è possibile aggiungerli alla directory di Azure AD, fino a un massimo di 900 nomi di dominio. Per aggiungere ogni nome di dominio successivo, usare la stessa procedura.

## Risoluzione dei problemi
Se non è possibile verificare un nome di dominio personalizzato, ecco alcune cause potenziali. Si inizierà dalla più comune fino a quella meno comune.

- Si è tentato di verificare il nome di dominio prima della propagazione della voce DNS. Attendere qualche minuto e riprovare.

- Il record DNS non è stato immesso. Verificare la voce DNS e attenderne la propagazione, quindi riprovare.

- Il nome di dominio è già stato verificato in un'altra directory. Individuare il nome di dominio ed eliminarlo dell'altra directory, quindi riprovare.

- Il record DNS contiene un errore. Correggere l'errore e riprovare.

- Non si hanno autorizzazioni sufficienti per aggiornare i record DNS. Condividere le voci DNS con la persona o team dell'organizzazione che ha l'accesso e chiedere di aggiungere la voce DNS.


## Passaggi successivi

-   [Assegnare nomi utente che includono il nome di dominio personalizzato](active-directory-add-domain-add-users.md)

-   [Gestire i nomi di dominio personalizzati](active-directory-add-manage-domain-names.md)

-   [Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso e al pannello di accesso](active-directory-add-company-branding.md)

-   [Usare PowerShell per gestire i nomi di dominio in Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [Informazioni sui concetti relativi alla gestione dei domini in Azure AD](active-directory-add-domain-concepts.md)

<!---HONumber=AcomDC_0427_2016-->