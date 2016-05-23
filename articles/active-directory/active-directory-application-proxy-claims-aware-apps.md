<properties
	pageTitle="Uso di app in grado di riconoscere attestazioni nel proxy dell'applicazione"
	description="Viene illustrato come iniziare a utilizzare il proxy dell'applicazione di AD Azure."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/09/2016"
	ms.author="kgremban"/>



# Uso di app in grado di riconoscere attestazioni nel proxy dell'applicazione

> [AZURE.IMPORTANT] Il proxy di applicazione di Azure AD è una funzionalità disponibile solo se è stato eseguito l'aggiornamento all'edizione Premium o Basic di Azure Active Directory. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).

Le app in grado di riconoscere attestazioni eseguono un reindirizzamento al servizio token di sicurezza, che a sua volta richiede le credenziali dell'utente in cambio di un token prima di reindirizzare l'utente all'applicazione. Per abilitare il proxy dell'applicazione e poter gestire le operazioni di reindirizzamento, è necessario prima seguire questa procedura.

## Prerequisiti
Prima di eseguire la procedura, assicurarsi che il servizio token di sicurezza a cui reindirizza l'app basata sul riconoscimento delle attestazioni sia disponibile all'esterno della rete locale.

## Configurazione del portale di Azure classico

1. Pubblicare l'applicazione seguendo le istruzioni contenute in [Pubblicare le applicazioni con il proxy di applicazione](active-directory-application-proxy-publish.md).
2. Nell'elenco delle applicazioni selezionare l'app in grado di riconoscere attestazioni e fare clic su **Configura**.
3. Se si sceglie **Passthrough** come **Metodo di autenticazione preliminare**, assicurarsi di selezionare **HTTPS** come schema di **URL esterno**.
4. Se si sceglie **Azure Active Directory** come **Metodo di autenticazione preliminare**, assicurarsi di selezionare **Nessuno** come **Metodo di autenticazione interna**.


## Configurazione di AD FS

1. Aprire la console di gestione di AD FS.
2. Passare a **Attendibilità relying party**, fare clic con il pulsante destro del mouse sull'app da pubblicare con il proxy dell'applicazione e scegliere **Proprietà**.![Schermata: clic con il pulsante destro del mouse sul nome dell'app in Trust relying party](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)  
3. Nella scheda **Endpoint** in **Tipo di endpoint** selezionare **WS-Federation**.
4. In **URL attendibile** specificare l'URL immesso in **URL esterno** nel proxy dell'applicazione e quindi fare clic su **OK**. ![Schermata: aggiunta di un endpoint e impostazione del valore per URL attendibile](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)  

## Vedere anche

- [Pubblicare le applicazioni con il proxy di applicazione](active-directory-application-proxy-publish.md)
- [Abilitare l'accesso Single Sign-On](active-directory-application-proxy-sso-using-kcd.md)
- [Risolvere i problemi che si verificano con il proxy di applicazione](active-directory-application-proxy-troubleshoot.md)
- [Abilitare le app client native per l'interazione con applicazioni proxy](active-directory-application-proxy-native-client.md)

Per le notizie e gli aggiornamenti più recenti, leggere il [blog del proxy di applicazione](http://blogs.technet.com/b/applicationproxyblog/)

<!---HONumber=AcomDC_0511_2016-->