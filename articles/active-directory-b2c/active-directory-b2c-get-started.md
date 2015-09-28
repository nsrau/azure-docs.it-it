<properties
	pageTitle="Anteprima di Azure Active Directory B2C: creazione di una directory di Azure Active Directory B2C | Microsoft Azure"
	description="Un argomento su come creare una directory di Azure Active Directory B2C"
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

# Anteprima di Azure Active Directory B2C: come creare una Directory AD B2C Azure

Per iniziare a utilizzare Azure Active Directory (AD) B2C, seguire i 3 passaggi descritti di seguito.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Passaggio 1: Iscriversi per ottenere una sottoscrizione ad Azure

Se si dispone già di una sottoscrizione ad Azure passare al passaggio successivo. In caso contrario, iscriversi [ad Azure](sign-up-organization.md) e accedere ad Azure AD B2C.

> [AZURE.NOTE]L’Anteprima AD B2C di Azure è attualmente disponibile gratuitamente per l'utilizzo ma limitato (fino a 50.000 utenti per directory). È necessario accedere a una sottoscrizione di Azure nel [portale Azure](http://manage.windowsazure.com/).

## Passaggio 2: Creare una Directory AD B2C di Azure

Utilizzare la procedura seguente per creare una nuova directory AD B2C di Azure. Attualmente le funzionalità B2C non possono essere attivate nelle directory esistenti, se sono presenti.

1. Accedere al [portale di Azure](https://manage.windowsazure.com/) come amministratore della sottoscrizione. Questo è lo stesso account di lavoro o scolastico o lo stesso Account Microsoft utilizzato per l'iscrizione ad Azure.
2. Fare clic su **Nuovo** > **Servizi app** > **Active Directory** > **Directory** > **Creazione personalizzata**.

    ![Creare la directory](./media/active-directory-b2c-get-started/new-directory.png)

3. Scegliere il **nome**, il **nome di dominio** e il **paese/area geografica** per la directory.
4. Selezionare l'opzione che indica che "**è una directory B2C**".
5. Fare clic sul segno di spunta per completare l’operazione.

    ![Creare la directory B2C](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. La directory è stata creata e verrà visualizzata nell'estensione Active Directory. Si ha anche il ruolo di amministratore generale della directory. È possibile aggiungere altri amministratori generali in base alle esigenze.

    > [AZURE.IMPORTANT]Ci può volere fino a due minuti per la creare la directory. Se si affrontano problemi durante la creazione della directory, consultare questo [articolo](active-directory-b2c-support-create-directory.md) per ricevere istruzioni.

## Passaggio 3: Passare al pannello delle funzionalità B2C nel portale di anteprima di Azure

1. Passare all'estensione Active Directory sulla barra di spostamento sul lato sinistro.
2. Trovare la directory sotto la scheda **Directory** e fare clic su di essa.
3. Fare clic sulla scheda **Configura**.
4. Fare clic sul link **Gestire le impostazioni di B2C** nella sezione **amministrazione B2C**.

    ![Creare la directory B2C](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

4. Il [portale di anteprima di Azure ](https://portal.azure.com/) con il pannello delle funzionalità B2C si aprirà in una nuova scheda o finestra del browser.
5. Aggiungere questo pannello (vedere l'angolo superiore destro) alla schermata iniziale per facilitare l'accesso.

    ![Funzionalità del pannello B2C](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE]È possibile gestire utenti e gruppi, configurazione della reimpostazione autonoma della password e funzionalità di personalizzazione dell’azienda per la directory nel [portale di Azure](https://manage.windowsazure.com/).

## Passaggi successivi

Passare alla [registrazione di un'applicazione con AD B2C di Azure e creazione di un'applicazione di avvio rapido](active-directory-b2c-app-registration.md).

<!---HONumber=Sept15_HO3-->