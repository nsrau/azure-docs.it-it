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
	ms.date="04/19/2016"
	ms.author="curtand;jeffsta"/>

# Aggiungere un nome di dominio personalizzato per semplificare l'accesso con Azure Active Directory

È possibile usare nomi di dominio personalizzati per migliorare e semplificare l'accesso e le altre esperienze utente in Azure Active Directory (Azure AD). Ad esempio, se l'azienda è proprietaria del nome di dominio "contoso.com", gli utenti possono accedere con nomi utente che conoscono, come "joe@contoso.com". È necessario avere accesso a un account utente amministratore globale per la directory di Azure AD.

Quando si ottiene la directory del tenant in Azure Active Directory per la prima volta, gli utenti accedono con nomi utente quali "alice@contoso.onmicrosoft.com". In questo esempio contoso.onmicrosoft.com è un nome di dominio predefinito iniziale che è possibile usare fino alla verifica del nome di dominio personalizzato. Successivamente, si aggiunge un nome di dominio personalizzato di proprietà dell'azienda, ad esempio "contoso.com". In questo modo è possibile assegnare nomi che siano familiari agli utenti, ad esempio "alice@contoso.com".

Per informazioni generali sull'uso di nomi di dominio in Azure AD, vedere la panoramica dei [concetti relativi alla gestione dei domini in Azure AD](active-directory-add-domain-concepts.md). La maggior parte degli amministratori esegue le attività di gestione dei nomi di dominio in Azure AD usando il portale di Azure classico. È anche possibile usare [PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) o l'[anteprima dell'API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations) per eseguire le attività di gestione, se si preferisce.

## Aggiungere un nome di dominio personalizzato alla directory

Per aggiungere un nome di dominio personalizzato alla directory:

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com/) con un account utente amministratore globale per la directory di Azure AD.

2. Selezionare **Active Directory** sulla barra di spostamento a sinistra.

3. Aprire la directory.

4. Selezionare la scheda **Domini**.

5. Sulla barra dei comandi selezionare **Aggiungi**.

6. Immettere il nome del dominio personalizzato, ad esempio "contoso.com". Assicurarsi di includere .com, .net o altre estensioni di primo livello.

7. Se si intende configurare il dominio per l'[accesso federato](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) con l'istanza di Active Directory locale, selezionare la relativa casella di controllo.

8. Selezionare **Aggiungi**.

Perché sia possibile assegnare nomi utente che includono il nome di dominio personalizzato, Azure AD deve verificare che l'azienda sia proprietaria del nome di dominio. A tale scopo, è necessario aggiornare le voci DNS nel registrar per il dominio.

## Ottenere le voci DNS che verranno usate da Azure AD per la verifica del nome di dominio

Se al momento dell'aggiunta del dominio è stata selezionata l'opzione per la configurazione del dominio per l'accesso federato, verranno visualizzate le istruzioni per scaricare lo strumento Azure AD Connect. Eseguire lo strumento Azure AD Connect per [ottenere le voci DNS da aggiungere al registrar](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Se non è stata selezionata l'opzione per l'accesso federato con AD di Windows Server, le voci DNS vengono visualizzate nella seconda pagina della procedura guidata **Aggiungi un dominio**.

## Aggiungere le voci DNS al file di zona DNS per il dominio

Per aggiungere le voci DNS richieste da Azure AD:

1.  Accedere al registrar per il dominio. Se non si hanno autorizzazioni sufficienti per aggiornare il file di zona DNS per il dominio, condividere le voci DNS con la persona o il team dell'organizzazione che ha questo tipo di accesso e chiedere di aggiornarle.

2.  Per aggiornare il file di zona DNS per il dominio, aggiungere le voci DNS fornite da Azure AD. Queste voci DNS consentiranno ad Azure AD di verificare la proprietà del dominio. Non verrà modificato alcun comportamento, ad esempio il routing della posta elettronica o l'hosting Web.

[Istruzioni sull'aggiunta di voci DNS ai registrar DNS comuni](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## Verificare il dominio con Azure AD

Dopo aver aggiunto le voci DNS, è possibile verificare il dominio con Azure AD.

Se è stata selezionata l'opzione per la federazione del nome di dominio personalizzato, la verifica viene eseguita automaticamente dallo strumento Azure AD Connect. Dopo aver completato i prerequisiti, eseguire lo strumento. In caso contrario, verificare il dominio nel portale di Azure classico. Se la procedura guidata **Aggiungi un dominio** è ancora aperta, è possibile fare clic sul pulsante di verifica nella terza pagina della procedura guidata. La propagazione dei record DNS potrebbe richiedere alcuni minuti.

Se la procedura guidata **Aggiungi un dominio** è stata chiusa, è possibile verificare il dominio nel [portale di Azure classico](https://manage.windowsazure.com/):

1.  Accedere con un account utente amministratore globale per la directory di Azure AD.

2.  Selezionare **Active Directory** sulla barra di spostamento a sinistra.

3.  Aprire la directory.

4.  Selezionare la scheda **Domini**.

5.  Selezionare nell'elenco il dominio da verificare.

6.  Sulla barra dei comandi selezionare **Verifica**.

7.  Nella finestra di dialogo selezionare **Verifica**.

A questo punto è possibile assegnare nomi utente che includono il nome di dominio personalizzato.

## Aggiungere altri nomi di dominio personalizzati

Se l'organizzazione usa più di un nome di dominio personalizzato, ad esempio "contoso.com" e "contosobank.com", è possibile aggiungerli alla directory di Azure AD, fino a un massimo di 900 domini. Per aggiungere ogni nome di dominio successivo, seguire gli stessi passaggi elencati in precedenza.

Passaggi successivi

-   [Assegnare utenti a un dominio personalizzato](active-directory-add-domain-add-users.md)

-   [Gestire i nomi di dominio personalizzati](active-directory-add-manage-domain-names.md)

-   [Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso e al pannello di accesso](active-directory-add-company-branding.md)

-   [Concetti relativi alla gestione dei domini in Azure AD](active-directory-add-domain-concepts.md)

<!---HONumber=AcomDC_0420_2016-->