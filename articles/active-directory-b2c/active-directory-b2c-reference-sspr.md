<properties
	pageTitle="Anteprima di Azure Active Directory B2C: configurazione della Reimpostazione password self-service | Microsoft Azure"
	description="Un argomento che dimostra come configurare la Reimpostazione password self-service per gli utenti in Azure Active Directory B2C"
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

# Anteprima di Azure Active Directory B2C: configurazione della Reimpostazione password self-service per gli utenti

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Questa funzionalità consente agli utenti (che hanno effettuato la registrazione agli account locali) di reimpostare le password in modo indipendente. Questo riduce notevolmente il carico per lo staff di supporto, soprattutto se l'applicazione dispone di milioni di clienti che la utilizzano regolarmente. Attualmente, è supportato solo l’utilizzo di un indirizzo di posta elettronica verificato come metodo di ripristino. Verranno aggiunti metodi di ripristino aggiuntivi (numero di telefono verificato, domande di sicurezza e così via) in futuro. Per impostazione predefinita, la directory non avrà la reimpostazione password self-service attivata. Usare i passaggi seguenti per attivarla:

1. Accedere al [portale di Azure](https://manage.windowsazure.com/) come amministratore della sottoscrizione. Questo è lo stesso account di lavoro o scolastico o lo stesso Account Microsoft utilizzato per creare la directory.
2. Passare all'estensione Active Directory sulla barra di spostamento sul lato sinistro.
3. Trovare la directory sotto la scheda **Directory** e fare clic su di essa.
4. Fare clic sulla scheda **Configura**.
5. Scorrere in basso alla sezione relativa ai **criteri di reimpostazione della password utente** e impostare l'opzione **Utenti abilitati per la reimpostazione della password** su **Sì**. Si noti che l’opzione **Indirizzo di posta elettronica alternativo** è selezionata; lasciarla com’è.

    ![Reimpostazione della password self-service](./media/active-directory-b2c-reference-sspr/sspr.png)
 
6. Fare clic su **Save** nella parte inferiore della pagina. L'operazione è completata.

Per eseguire il test, utilizzare la funzionalità "Run now" in ogni criterio di accesso (che include gli account locali come provider di identità). Nella pagina di accesso dell'account locale (dove si inserisce l’indirizzo di posta elettronica e la password o il nome utente e la password), fare clic su **Impossibile accedere all'account?** per verificare l'esperienza dell’utente.

> [AZURE.NOTE]Le pagine di reimpostazione della password self-service sono personalizzabili tramite la [funzionalità marchio della società](active-directory-add-company-branding.md).

<!---HONumber=Sept15_HO3-->