<properties
	pageTitle="Aggiungere un nome di dominio personalizzato ad Azure Active Directory | Microsoft Azure"
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

# Aggiungere un nome di dominio personalizzato ad Azure Active Directory

L'organizzazione usa uno o più nomi di dominio per svolgere attività commerciali e gli utenti accedono alla rete aziendale con il nome di dominio aziendale. Poiché ora si usa Azure Active Directory (Azure AD), è possibile aggiungere il nome di dominio aziendale anche ad Azure AD. Nella directory si potranno così assegnare nomi utente familiari, ad esempio "alice@contoso.com". Il processo è semplice:

- Aggiungere il nome di dominio tramite la procedura guidata **Aggiungi dominio** nel portale di Azure classico.

- Ottenere la voce DNS nel portale di Azure AD classico o con lo strumento Azure AD Connect.

- Aggiungere la voce DNS per il nome di dominio nel file di zona DNS sul sito Web del registrar DNS.

- Verificare il nome di dominio nel portale di Azure AD classico o con lo strumento Azure AD Connect.


Finché non si verifica il nome di dominio personalizzato, gli utenti devono accedere con nomi che includono il nome di dominio iniziale della directory, ad esempio "alice@contoso.onmicrosoft.com". Se sono necessari più nomi di dominio personalizzati, ad esempio "contoso.com" e "contosobank.com", è possibile aggiungerne fino a un massimo di 900. Usare la stessa procedura riportata in questo articolo per aggiungere ogni nome di dominio.

## Aggiungere un nome di dominio personalizzato alla directory

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com/) con un account utente di amministratore globale per la directory di Azure AD.

2. In **Active Directory**, aprire la directory e selezionare la scheda **Domini**.

3. Sulla barra dei comandi selezionare **Aggiungi** e quindi immettere il nome del dominio personalizzato, ad esempio "contoso.com". Assicurarsi di includere .com, .net o altre estensioni di primo livello.

4. Se si vuole configurare questo dominio per l'[accesso federato](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) con l'istanza di Active Directory locale, selezionare la relativa casella di controllo.

5. Selezionare **Aggiungi**.

Dopo aver aggiunto il nome di dominio, Azure AD deve verificare che sia di proprietà dell'organizzazione. Per consentire ad Azure AD di eseguire questa verifica, è necessario aggiungere una voce DNS nel file di zona DNS per il nome di dominio. Questa attività viene eseguita nel sito Web del registrar per il nome di dominio.

## Ottenere le voci DNS per il nome di dominio

Le voci DNS si trovano nella seconda pagina della procedura guidata **Aggiungi dominio**, se non si configura la federazione con un'istanza locale di Active Directory di Windows Server.

Se si configura il dominio per la federazione, si verrà indirizzati al download dello strumento Azure AD Connect. Eseguire lo strumento Azure AD Connect per [ottenere le voci DNS che è necessario aggiungere al registrar](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). Lo strumento Azure AD Connect verificherà anche il nome di dominio per Azure AD.

## Aggiungere la voce DNS al file di zona DNS

1.  Accedere al registrar per il dominio. Se non si hanno autorizzazioni sufficienti per aggiornare la voce DNS, chiedere alla persona o al team che ha l'accesso di aggiungerla.

2.  Per aggiornare il file di zona DNS per il dominio, aggiungere la voce DNS fornita da Azure AD. Questa voce DNS consente ad Azure AD di verificare la proprietà del dominio. La voce DNS non modifica alcun comportamento, ad esempio il routing della posta elettronica o l'hosting Web. La propagazione dei record DNS può richiedere fino a un'ora.

[Istruzioni sull'aggiunta di voci DNS ai registrar DNS comuni](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## Verificare il nome di dominio con Azure AD

Dopo avere aggiunto la voce DNS, è necessario assicurarsi che il nome di dominio venga verificato da Azure AD. Questo è il passaggio finale per completare l'operazione.

Se la procedura guidata **Aggiungi dominio** è ancora aperta, selezionare **Verifica** nella terza pagina. È necessario attendere fino a un'ora la propagazione della voce DNS prima di procedere alla verifica.

Se la procedura guidata **Aggiungi dominio** è stata chiusa, è possibile verificare il dominio nel [portale di Azure classico](https://manage.windowsazure.com/):

1.  Accedere con un account utente amministratore globale per la directory di Azure AD.

2.  Aprire la directory e selezionare la scheda **Domini**.

3.  Selezionare il dominio da verificare.

4.  Selezionare **Verifica** sulla barra dei comandi e quindi selezionare **Verifica** nella finestra di dialogo.

La procedura è così completata. A questo punto è possibile [assegnare nomi utente che includono il nome di dominio personalizzato](active-directory-add-domain-add-users.md). In caso di problemi con la verifica del nome di dominio, vedere la sezione [Risoluzione dei problemi](#troubleshooting).

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
-   [Informazioni sui concetti relativi alla gestione dei domini in Azure AD](active-directory-add-domain-concepts.md)
-   [Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso e al pannello di accesso](active-directory-add-company-branding.md)
-   [Usare PowerShell per gestire i nomi di dominio in Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

<!---HONumber=AcomDC_0504_2016-->