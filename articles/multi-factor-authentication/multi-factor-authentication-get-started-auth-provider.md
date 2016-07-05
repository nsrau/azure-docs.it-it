<properties 
	pageTitle="Introduzione all'uso di un provider di Microsoft Azure Multi-Factor Authentication" 
	description="Informazioni su come creare un provider di Azure Multi-Factor Authentication." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="06/29/2016" 
	ms.author="billmath"/>



# Introduzione all'uso di un provider di Azure Multi-Factor Authentication
Multi-Factor Authentication è disponibile per impostazione predefinita per gli amministratori globali che si occupano di utenti di Azure Active Directory e Office 365. Se tuttavia si vogliono sfruttare le [funzionalità avanzate](multi-factor-authentication-whats-next.md), è necessario acquistare la versione completa di Azure MFA.

> [AZURE.NOTE]  Un provider di Azure Multi-Factor Authentication viene usato per sfruttare le funzionalità fornite dalla versione completa di Azure MFA. È destinato agli utenti che **non hanno ottenuto licenze tramite Azure MFA, Azure AD Premium o EMS**. Azure MFA, Azure AD Premium e EMS includono le versione completa di Azure MFA per impostazione predefinita. Se si hanno licenze disponibili, non è necessario un provider Azure Multi-Factor Authentication.

È necessario un provider Azure Multi-Factor Authentication se si vuole scaricare l'SDK.

> [AZURE.IMPORTANT]  Per scaricare l'SDK occorre creare un provider Azure Multi-Factor Authentication anche se sono disponibili licenze di Azure MFA, AAD Premium o EMS. Se si crea un provider Azure Multi-Factor Authentication a questo scopo e sono disponibili le licenze, il provider deve essere creato con il modello **Per utente abilitato** e collegato alla directory che contiene le licenze di Azure MFA, Azure AD Premium o EMS. In questo modo è possibile evitare addebiti, a meno che il numero di utenti singoli che usano l'SDK sia maggiore del numero di licenze possedute.
 
Usare la procedura seguente per creare un provider di Azure Multi-Factor Authentication.

## Per creare un provider di Multi-Factor Authentication
--------------------------------------------------------------------------------

1. Accedere al **portale di Azure classico** come amministratore.
2. A sinistra, selezionare **Active Directory**.
3. Nella parte superiore della pagina Active Directory selezionare **Provider di Autenticazione a più fattori**. ![Creazione di un provider di MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)
4. Nella parte inferiore fare clic su **Nuovo**. ![Creazione di un provider di MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)
5. In **Servizi app** selezionare **Provider di Autenticazione a più fattori** ![Creazione di un provider di MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)
6. Selezionare **Creazione rapida**. ![Creazione di un provider di MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)
5. Compilare i campi seguenti, quindi selezionare **Crea**.
	1. **Nome**: nome del provider Multi-Factor Authentication.
	2. **Modello di utilizzo**: modello di utilizzo del provider Multi-Factor Authentication.
		- Per autenticazione: modello di acquisto in cui è previsto l'addebito in base al numero di autenticazioni. Usato, in genere, per scenari in cui viene usato Azure Multi-Factor Authentication in un'applicazione per il consumer.
		- Per utente abilitato: modello di acquisto in cui è previsto l'addebito in base al numero di utenti abilitati. Usato, in genere, per l'accesso dei dipendenti alle applicazioni come Office 365.
	2. **Directory**: tenant di Azure Active Directory a cui è associato il provider Multi-Factor Authentication. Tenere presente quanto segue:
		- Non è necessaria una directory di Azure AD per creare un provider di Multi-Factor Authentication. Lasciare vuota la casella se si prevede di usare solo il server Azure Multi-Factor Authentication o l'SDK.
		- Il provider di Azure Multi-Factor Authentication deve essere associato a una directory di Azure AD per sfruttare le funzionalità avanzate.
		- Azure AD Connect, AAD Sync e DirSync sono necessari unicamente se si esegue la sincronizzazione dell'ambiente Active Directory locale con una directory di Azure AD. Se si usa solo una directory di Azure AD non sincronizzata, non è necessaria. ![Creazione di un provider di MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)
5. Facendo clic su Crea, viene creato il provider Multi-Factor Authentication e viene visualizzato un messaggio simile al seguente: **Creazione del provider di Multi-Factor Authentication completata**. Fare clic su **OK**. ![Creazione di un provider di MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)

<!---HONumber=AcomDC_0629_2016-->