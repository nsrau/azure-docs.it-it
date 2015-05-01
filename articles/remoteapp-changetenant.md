
<properties 
    pageTitle="Modificare il tenant di Azure Active Directory in RemoteApp"
    description="Informazioni su come modificare il tenant di Azure Active Directory associato RemoteApp" 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/19/2015" 
    ms.author="elizapo" />



# Modificare il tenant di Azure Active Directory in RemoteApp

RemoteApp usa Azure Active Directory (Azure AD) per consentire l'accesso utente. L'unico tenant di Azure AD che è possibile usare è quello associato alla sottoscrizione Azure. È possibile visualizzare la sottoscrizione associata nella pagina Impostazioni nel portale. Controllare la colonna Directory nella scheda Sottoscrizioni. 

Se si desidera usare un tenant differente, completare i seguenti passaggi per modificare l'associazione alla propria sottoscrizione:

1. Nel portale rimuovere eventuali utenti di Azure AD a cui è stato concesso l'accesso ai servizi RemoteApp.


2. Impostare un account Microsoft (in precedenza denominato Live ID) come amministratore del servizio. Esaminare **Impostazioni -> Amministratori**.
	1. Fare clic sull'utente attualmente connesso nell'angolo superiore destro, quindi su **Visualizza fattura**.
	2. Selezionare la propria sottoscrizione, quindi fare clic su **Modifica i dettagli della sottoscrizione**.
	3. Apportare le modifiche necessarie.



3. Disconnettersi dal portale, quindi riaccedere con l'account Microsoft specificato nel passaggio precedente.


4. Fare clic su **Nuovo -> Servizi app -> Active Directory -> Creazione personalizzata -> Utilizza directory esistente**. Aggiungere il tenant di Azure AD che si desidera associare alla sottoscrizione.


5. In **Impostazioni -> Sottoscrizioni** selezionare la sottoscrizione e quindi fare clic su **Modifica directory**. Selezionare il tenant di Azure AD che si desidera usare.



È ora possibile usare il nuovo tenant di Azure AD per controllare l'accesso alla sottoscrizione di Azure e configurare l'accesso utente in RemoteApp.


<!--HONumber=52-->