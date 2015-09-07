<properties
	pageTitle="Uso di app in grado di riconoscere attestazioni nel proxy di applicazione"
	description="Viene illustrato come iniziare a utilizzare il proxy dell'applicazione di AD Azure."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="rkarlin"/>



# Uso di app in grado di riconoscere attestazioni nel proxy di applicazione


Le app in grado di riconoscere attestazioni eseguono un reindirizzamento al servizio token di sicurezza, che a sua volta richiede all'utente le credenziali in cambio di un token prima di reindirizzare l'utente all'applicazione. Per permettere al proxy di applicazione di funzionare con questi reindirizzamenti, è necessario eseguire la procedura seguente per consentire al proxy di applicazione di interagire con le applicazioni in grado di riconoscere attestazioni.

> [AZURE.IMPORTANT]Il proxy di applicazione di Azure AD è una funzionalità disponibile solo se è stato eseguito l'aggiornamento all'edizione Premium o Basic di Azure Active Directory. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).


## Prerequisito

Prima di eseguire la procedura, assicurarsi che il servizio token di sicurezza a cui reindirizza l'app in grado di riconoscere attestazioni sia disponibile all'esterno della rete locale.

### Configurazione del portale di Azure

1. Pubblicare l'applicazione seguendo le istruzioni contenute in [Pubblicare le applicazioni con il proxy di applicazione](active-directory-application-proxy-publish.md).
2. Nell'elenco delle applicazioni selezionare l'app in grado di riconoscere attestazioni e fare clic su **Configura**.
3. Se si sceglie **Passthrough** come **Metodo di autenticazione preliminare**, assicurarsi di selezionare **HTTPS** come schema di **URL esterno**.
4. Se si sceglie Azure Active Directory come **Metodo di autenticazione preliminare**, assicurarsi di selezionare **Nessuno** come **Metodo di autenticazione interna**.

### Configurazione di AD FS

1. Aprire la console di **gestione di AD FS**.
2. Passare a **Attendibilità componente**, fare clic con il pulsante destro del mouse sull'app da pubblicare con il proxy di applicazione e scegliere **Proprietà**.

![Schermata: clic con il pulsante destro del mouse sul nome dell'app in Attendibilità componente][1]

3. Nella scheda **Endpoint** in **Tipo di endpoint** selezionare **WS-Federation**.
4. In **URL attendibile** specificare l'URL immesso in **URL esterno** nel proxy di applicazione e quindi fare clic su **OK**.

![Schermata: aggiunta di un endpoint e impostazione del valore per URL attendibile][2]

<!--image references-->
[1]: ./media/active-directory-application-proxy-claims-aware-apps/AppDropdown.jpg
[2]: ./media/active-directory-application-proxy-claims-aware-apps/AddEndpoint.jpg

<!---HONumber=August15_HO9-->