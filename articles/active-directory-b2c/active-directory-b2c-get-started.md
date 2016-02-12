<properties
	pageTitle="Anteprima di Azure Active Directory B2C: creazione di un tenant di Azure Active Directory B2C | Microsoft Azure"
	description="Un argomento su come creare un tenant di Azure Active Directory B2C"
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
	ms.date="01/28/2016"
	ms.author="swkrish"/>

# Anteprima di Azure Active Directory B2C: come creare un Tenant AD B2C Azure

Per iniziare a utilizzare Azure Active Directory (AD) B2C, seguire i 3 passaggi descritti di seguito.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Passaggio 1: Iscriversi per ottenere una sottoscrizione ad Azure

Se si dispone già di una sottoscrizione ad Azure passare al passaggio successivo. In caso contrario, iscriversi [ad Azure](../active-directory/sign-up-organization.md) e accedere ad Azure AD B2C.

> [AZURE.NOTE]
L’Anteprima AD B2C di Azure è attualmente disponibile gratuitamente per l'utilizzo ma limitato (fino a 50.000 utenti per tenant). Per accedere al [portale di Azure classico](http://manage.windowsazure.com/), è necessario avere una sottoscrizione di Azure.

## Passaggio 2: Creare un Tenant AD B2C di Azure

Utilizzare la procedura seguente per creare un nuovo tenant AD B2C di Azure. Attualmente le funzionalità B2C non possono essere attivate nelle directory esistenti, se sono presenti.

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com/) come amministratore della sottoscrizione. Questo è lo stesso account di lavoro o scolastico o lo stesso Account Microsoft utilizzato per l'iscrizione ad Azure.
2. Fare clic su **Nuovo** > **Servizi app** > **Active Directory** > **Directory** > **Creazione personalizzata**.

    ![Creazione di tenant](./media/active-directory-b2c-get-started/new-directory.png)

3. Scegliere il **nome**, **nome di dominio** e **paese o regione** per il tenant.
4. Selezionare l'opzione che indica che "**è una directory B2C**".
5. Fare clic sul segno di spunta per completare l’operazione.

    ![Creazione di tenant B2C](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. Il tenant è stato creato e verrà visualizzato nell'estensione Active Directory. Si ha anche il ruolo di amministratore generale del tenant. È possibile aggiungere altri amministratori generali in base alle esigenze.

    > [AZURE.IMPORTANT]
    Ci può volere fino a due minuti per la creare il tenant. Se si affrontano problemi durante la creazione del tenant, consultare questo [articolo](active-directory-b2c-support-create-directory.md) per ricevere istruzioni.

## Passaggio 3: Passare al pannello delle funzionalità B2C nel portale di Azure

1. Passare all'estensione Active Directory sulla barra di spostamento sul lato sinistro.
2. Trovare il tenant sotto la scheda **Directory** e fare clic su di essa.
3. Fare clic sulla scheda **Configura**.
4. Fare clic sul link **Gestire le impostazioni di B2C** nella sezione **amministrazione B2C**.

    ![Creazione di tenant B2C](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

4. Il portale di Azure con il pannello delle funzionalità B2C si aprirà in una nuova scheda o finestra del browser.

    > [AZURE.IMPORTANT]
    Esiste un problema noto in base al quale questa pagina non carica correttamente (per un numero ridotto di tenant). Aggiornando il browser si dovrebbe risolvere il problema. Altrimenti, contattare il team di supporto.

5. Aggiungere questo pannello (vedere l'angolo superiore destro) alla schermata iniziale per facilitare l'accesso.

    ![Funzionalità del pannello B2C](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE]
    È possibile gestire utenti e gruppi, la configurazione della reimpostazione autonoma della password e le funzionalità di personalizzazione della società per il tenant nel [portale di Azure classico](https://manage.windowsazure.com/).

## Passaggi successivi

Passare alla [registrazione di un'applicazione con AD B2C di Azure e creazione di un'applicazione di avvio rapido](active-directory-b2c-app-registration.md).

<!---HONumber=AcomDC_0204_2016-->