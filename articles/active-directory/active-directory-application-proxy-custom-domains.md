<properties
	pageTitle="Utilizzo di domini personalizzati nel Proxy di applicazione di Azure AD | Microsoft Azure"
	description="Viene illustrato il funzionamento di domini personalizzati nel Proxy di applicazione di Azure AD"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="StevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="kgremban"/>

# Utilizzo di domini personalizzati nel Proxy di applicazione AD Azure
> [AZURE.NOTE] Il proxy di applicazione di Azure AD è una funzionalità disponibile solo se è stato eseguito l'aggiornamento all'edizione Premium o Basic di Azure Active Directory. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).

L'utilizzo di un dominio predefinito consente di impostare lo stesso URL come URL interno ed esterno per l'accesso all'applicazione in modo che gli utenti dispongano solo di un URL per ricordare l'accesso all'app indipendentemente dal luogo da cui si accede. Ciò consente inoltre di creare un collegamento singolo nel Pannello di accesso per l'applicazione. Se si utilizza il dominio predefinito fornito dal Proxy di applicazione Azure AD, non esiste alcuna configurazione aggiuntiva necessaria per abilitare il dominio. Nel caso in cui si utilizza un dominio personalizzato, esistono alcune cose da fare per assicurarsi che il Proxy di applicazione riconosca il dominio e convalidi i certificati.

## Selezionare un dominio personalizzato

1. Pubblicare l'applicazione seguendo le istruzioni contenute in [Pubblicare le applicazioni con il proxy dell'applicazione](active-directory-application-proxy-publish.md).
2. Dopo che l'applicazione verrà visualizzata nell'elenco delle applicazioni, selezionarla e fare clic su **Configura**.
3. Su **URL esterno**, immettere il dominio personalizzato.
4. Se l'URL esterno è https, verrà richiesto di caricare un certificato in modo che in Azure sia possibile convalidare l'URL dell'applicazione. È inoltre possibile caricare un certificato con caratteri jolly che corrisponde all'URL esterno dell'applicazione. Questo dominio deve essere compreso nell'elenco [Domini verificati Azure](https://msdn.microsoft.com/library/azure/jj151788.aspx). Azure deve disporre di un certificato per l'URL del dominio dell'applicazione o un certificato con caratteri jolly che corrisponda all'URL esterno per l'applicazione.
5. Assicurarsi di aggiungere un record DNS che indirizzi l'URL interno all'applicazione che consente di ottenere lo stesso URL per l'accesso interno ed esterno e un singolo collegamento all'applicazione nell'elenco delle applicazioni dell'utente.

## Domande frequenti sull'utilizzo di domini personalizzati

D: è possibile selezionare un certificato già caricato senza caricarlo nuovamente? R: I certificati precedentemente caricati vengono associati automaticamente a un'applicazione ed esiste esattamente un certificato che ha un nome host dell'applicazione che corrisponde. Q: Come aggiungere un certificato e in quale formato deve essere caricato il documento esportato? R: Il certificato deve poi essere caricato dalla pagina di configurazione dell'applicazione. Il certificato deve essere un file PFX. ... Q: È possibile utilizzare i certificati ECC? R: Non esiste alcuna limitazione esplicita sui metodi di firma. ... Q: È possibile utilizzare i certificati SAN? R: Sì. ... D: È possibile utilizzare certificati con caratteri jolly? R: Sì. ... D: Si può utilizzare un certificato diverso in ogni applicazione? R: Sì, a meno che le due applicazioni condividano lo stesso host esterno. ... D: se registra un nuovo dominio, è possibile utilizzare tale dominio? R: Sì, l'elenco dei domini è preso dall'elenco di domini verificato del tenant. … D: Cosa accade quando scade un certificato? R: Verrà visualizzato un avviso nella sezione certificato nella pagina di configurazione dell'applicazione. Quando un utente tenta di accedere all'applicazione, viene visualizzato un avviso di sicurezza. ... D: Cosa fare se si desidera sostituire un certificato per un'applicazione specificata? R: Caricare un nuovo certificato dalla pagina di configurazione dell'applicazione. ... D: È possibile eliminare un certificato e sostituirlo? R: Quando si carica un nuovo certificato, se il certificato precedente non è usato da un'altra applicazione, verrà automaticamente eliminato. ... D: Cosa accade quando un certificato viene revocato? R: Non vengono eseguiti controlli per i certificati. Quando un utente tenta di accedere all'applicazione, a seconda del browser, potrebbe essere visualizzato un avviso di sicurezza. … D: È possibile usare un certificato autofirmato? R: Sì, sono consentiti i certificati autofirmati. Si noti che se si utilizza un'autorità di certificazione privata, il CDP (punto di distribuzione punto di revoche di certificati) per il certificato deve essere pubblico. ... D: esiste una posizione per visualizzare tutti i certificati per il tenant? R: Questo non è supportato nella versione corrente.


## Vedere anche
Si può fare molto di più con il proxy dell'applicazione:

- [Pubblicare le applicazioni con il proxy di applicazione](active-directory-application-proxy-publish.md)
- [Abilitare l'accesso Single Sign-On](active-directory-application-proxy-sso-using-kcd.md)
- [Abilitare l'accesso condizionale](active-directory-application-proxy-conditional-access.md)
- [Lavorare con applicazioni in grado di riconoscere attestazioni](active-directory-application-proxy-claims-aware-apps.md)- [Risoluzione dei problemi che si verificano con il Proxy dell'applicazione](active-directory-application-proxy-troubleshoot.md)

## Ulteriori informazioni sul proxy dell’applicazione
- [Dare un'occhiata alla nostra Guida in linea](active-directory-application-proxy-enable.md)
- [Blog del proxy dell'applicazione](http://blogs.technet.com/b/applicationproxyblog/)
- [Guarda i nostri video su Channel 9!](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Risorse aggiuntive
- [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
- [Iscriversi ad Azure come organizzazione](sign-up-organization.md)
- [Identità di Azure](fundamentals-identity.md)

<!---HONumber=AcomDC_0211_2016-->