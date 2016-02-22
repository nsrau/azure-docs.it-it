<properties
	pageTitle="Aggiungere e verificare un nome di dominio personalizzato in Azure Active Directory | Microsoft Azure"
	description="Come aggiungere i domini esistenti in Azure Active Directory quando si inizia a usare Azure AD. Configurare il dominio personalizzato per la sincronizzazione delle informazioni degli account utente con l'infrastruttura delle identità locale."
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
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="curtand;jeffsta"/>

# Aggiungere e verificare un nome di dominio personalizzato in Azure Active Directory

Per aggiungere un nome di dominio personalizzato e verificarlo per l'uso con Azure Active Directory, è necessario completare i passaggi seguenti.

1.  Eseguire una di queste operazioni:

    -   [Aggiungere un nome di dominio personalizzato che verrà federato con Active Directory locale](#add-a-custom-domain-name-that-will-be-federated)

    -   [Aggiungere un nome di dominio personalizzato che non verrà federato con Active Directory locale](#add-a-custom-domain-name-that-will-not-be-federated)

2.  Verificare il nome di dominio personalizzato.

    -   Aggiungere le voci DNS che Azure AD userà per verificare che l'azienda è proprietaria del nome di dominio personalizzato nel registrar del dominio.

    -   Verificare il dominio in Azure AD.

## Aggiungere un nome di dominio personalizzato che verrà federato

Per altre informazioni sull'integrazione di directory locali con Azure AD Connect, vedere

**Per aggiungere un nome di dominio personalizzato alla directory di Azure AD**

1.  Accedere al [portale di Azure classico](https://manage.windowsazure.com/) con un account che disponga dei privilegi di amministratore globale in Azure AD.

2.  Selezionare Active Directory.

3.  Aprire la directory.

4.  Selezionare la scheda **Domini**.

5.  Sulla barra dei comandi selezionare **Aggiungi**.

6.  Immettere il nome del dominio personalizzato. Assicurarsi di includere l'estensione .com.

7.  Selezionare la casella di controllo **Desidero configurare questo dominio per l'accesso Single Sign-On con il mio servizio Active Directory locale**.

8.  Fare clic su **Aggiungi**.

## Aggiungere un nome di dominio personalizzato che non verrà federato

La maggior parte dei nomi di dominio personalizzato sono domini di secondo livello "contoso.com"

**Per aggiungere il nome di dominio personalizzato alla directory di Azure AD**

1.  Accedere al [portale di Azure classico](https://manage.windowsazure.com/) con un account che disponga dei privilegi di amministratore globale in Azure AD.

2.  Selezionare Active Directory.

3.  Aprire la directory.

4.  Selezionare la scheda **Domini**.

5.  Sulla barra dei comandi selezionare **Aggiungi**.

6.  Immettere il nome del dominio personalizzato. Assicurarsi di includere l'estensione .com.

7.  Verificare che la casella di controllo **Desidero configurare questo dominio per l'accesso Single Sign-On con il mio servizio Active Directory locale** non sia selezionata.

8.  Selezionare **Aggiungi**.

9.  Osservare che il dominio è stato aggiunto alla directory.

## Verificare un dominio presso qualsiasi registrar

Per verificare il dominio, è necessario creare un record DNS presso il registrar, o nella posizione in cui è ospitato il sistema DNS, e quindi Azure AD usa tale record per confermare la proprietà del dominio. [Istruzioni sull'aggiunta di voci DNS ai registrar DNS comuni](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

Se il dominio è già registrato in un registrar, i record DNS necessari esistono già.

Se il dominio personalizzato è stato aggiunto ma non è stato ancora verificato, lo stato sarà **Non verificato**.

## Verificare un nome di dominio personalizzato che non verrà federato con una directory locale
Dopo aver aggiunto i record creati per il dominio attraverso il sistema DNS presso il registrar, procedere come segue:

1.  Nel [portale di Azure classico](https://manage.windowsazure.com/) in Azure Active Directory fare clic su **Domini**.

2.  Nell'elenco **Domini** trovare innanzitutto il dominio che si sta verificando e quindi, in base al portale in uso, fare clic su **Fare clic per verificare il dominio** o su **Verifica**.

3.  Seguire le istruzioni fornite per completare il processo di verifica.

    -   Se la verifica del dominio ha esito positivo, si riceverà una notifica che indica che il dominio è stato aggiunto all'account.

    -   Se la verifica del dominio ha esito negativo, le modifiche apportate al registrar potrebbero richiedere più tempo per la propagazione. Annullare la verifica e riprovare più tardi.

Se sono passate più di 72 ore da quando sono state apportate le modifiche al dominio, accedere al sito Web del registrar e verificare che le informazioni sull'alias siano state immesse correttamente. Se le informazioni immesse non sono corrette, è necessario rimuovere il record DNS errato e crearne uno nuovo con le informazioni corrette.

## Verificare il dominio personalizzato per la federazione con la directory locale

1.  Scaricare ed eseguire Azure AD Connect. Lo strumento Azure AD Connect [richiederà di aggiungere le voci DNS fornite](active-directory-aadconnect-get-started-custom.md#select-the-azure-ad-domain-that-you-wish-to-federate).

## Nomi di dominio di terzo livello

È possibile usare domini di terzo livello, ad esempio "europe.contoso.com", con Azure AD. Per aggiungere e usare un dominio di terzo livello:

1.  Aggiungere e verificare il dominio di secondo livello "contoso.com"

2.  Aggiungere eventuali sottodomini, ad esempio "europe.contoso.com", ad Azure AD. Quando si aggiunge un sottodominio di un dominio di secondo livello verificato, il dominio di terzo livello viene verificato automaticamente da Azure AD. Non è necessario aggiungere altre voci DNS.

Questi passaggi possono essere eseguiti anche con PowerShell e Graph.

Dopo aver verificato il dominio, è possibile configurarlo per l'uso con i propri account.

## Passaggi successivi

- [Uso di nomi di dominio personalizzati per semplificare l'esperienza di accesso degli utenti](active-directory-add-domain.md)
- [Aggiungere informazioni personalizzate distintive dell'azienda alle pagine Accedi e Pannello di accesso](active-directory-add-company-branding.md)
- [Assegnare utenti a un dominio personalizzato](active-directory-add-domain-add-users.md)
- [Cosa fare se si modifica il registrar DNS per il nome di dominio personalizzato](active-directory-add-domain-change-registrar.md)
- [Eliminare un dominio personalizzato in Azure Active Directory](active-directory-add-domain-delete-domain.md)

<!---HONumber=AcomDC_0211_2016-->