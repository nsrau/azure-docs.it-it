
<properties
    pageTitle="Modificare il tenant di Azure Active Directory in Azure RemoteApp | Microsoft Azure"
    description="Informazioni su come modificare il tenant di Azure Active Directory associato a RemoteApp di Azure"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/04/2015"
    ms.author="elizapo" />



# Modificare il tenant di Azure Active Directory in RemoteApp di Azure

RemoteApp usa Azure Active Directory (Azure AD) per consentire l'accesso utente. L'unico tenant di Azure AD che è possibile usare nella RemoteApp di Azure è quello associato alla sottoscrizione Azure. È possibile visualizzare la sottoscrizione associata nella pagina **Impostazioni** nel portale. Controllare la colonna **Directory** nella scheda **Sottoscrizioni**.

> [AZURE.NOTE]Affinché la modifica abbia esito positivo, rimuovere tutti gli utenti dal tenant esistente di Azure Active Directory da tutte le raccolte di Azure RemoteApp. A tale scopo, accedere al portale di Azure, passare alla scheda **RemoteApp di Azure** e aprire ogni raccolta di RemoteApp di Azure. Andare alla scheda **Utenti** e rimuovere gli utenti che appartengono al tenant di Azure Active Directory corrente. Ripetere per tutte le raccolte di RemoteApp di Azure esistenti. Senza questa operazione, non sarà possibile creare raccolte o applicarvi patch.

Se si desidera usare un tenant differente, completare i seguenti passaggi per modificare l'associazione alla propria sottoscrizione:

1. Nel portale rimuovere eventuali utenti di Azure AD a cui è stato concesso l'accesso ai servizi RemoteApp. (Vedere la nota precedente per i passaggi utili ad eseguire questa operazione).


2. Impostare un account Microsoft (in precedenza denominato Live ID) come amministratore del servizio. (Non si sa se si è già l'amministratore del servizio? È possibile scoprirlo facendo clic su **Impostazioni -> Amministratori**.) A questo punto, ecco come lo si modifica:
	1. Fare clic sull'utente nell'angolo superiore destro, quindi su **Visualizza fattura**.
	2. Fare clic sulla sottoscrizione. Quindi, nella nuova pagina, scorrere verso il basso e fare clic su **Modifica i dettagli della sottoscrizione** sulla destra. (Puntare sulla parte centrale inferiore destra, se è utile per trovarlo.)
	3. Digitare l'account Microsoft per l'utente che deve essere l’amministratore del servizio.

3. A questo punto, disconnettersi dal portale, quindi riaccedere con l'account Microsoft specificato nel passaggio precedente.


4. Fare clic su **Nuovo > Servizi app > Active Directory > Directory > Creazione personalizzata**.
5. In **Directory**, scegliere **Usa directory esistente**. Sarà necessario disconnettersi dal portale ora, quindi scegliere **Sono pronto per la disconnessione**.
6. Accedere di nuovo al portale come un amministratore globale della directory che si desidera aggiungere. (Se non si era già un amministratore globale, lo si sarà aver avuto accesso e poi essersi disconnessi.)
7. Al momento dell’acceso, verrà chiesto se si desidera usare il tenant di Active Directory esistente con la sottoscrizione. Fare clic su **Continua**, quindi fare clic su **Esci ora**.
5. Accedere di nuovo e passare a **Impostazioni -> sottoscrizioni**. Selezionare la propria sottoscrizione, quindi fare clic su **Modifica la directory**. Selezionare il tenant di Azure AD che si desidera usare.



È ora possibile usare il nuovo tenant di Azure AD per controllare l'accesso alla sottoscrizione di Azure e configurare l'accesso utente in RemoteApp.

<!---HONumber=AcomDC_1210_2015-->