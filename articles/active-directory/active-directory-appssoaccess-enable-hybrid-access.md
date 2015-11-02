<properties
	pageTitle="Abilitazione dell'accesso ibrido con il proxy di app | Microsoft Azure"
	description="Abilitare l'accesso alle applicazioni in esecuzione all'interno della rete privata dall'esterno della rete tramite Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/16/2015"
	ms.author="femila"/>

# Abilitazione dell'accesso ibrido con il proxy di app
Con il proxy di applicazione di Microsoft Azure Active Directory (AD) è possibile garantire l'accesso ad applicazioni che si trovano all'interno della rete privata in modo sicuro da qualsiasi posizione e su qualsiasi dispositivo. Dopo aver installato un connettore del proxy di applicazione all'interno dell'ambiente, è possibile configurarlo facilmente con Azure AD.

L'abilitazione dell'accesso a un'applicazione Web presenta un solo requisito: l'applicazione Web deve essere accessibile nel server in cui è installato il connettore. In altre parole è possibile installare il connettore nel server applicazioni o in qualsiasi altro server all'interno dell'ambiente, purché il connettore possa accedere all'applicazione Web.

##Funzionamento
### Breve panoramica
1. I connettori vengono distribuiti nella rete locale. Si possono distribuire più connettori ai fini della ridondanza e della scalabilità.
2. Il connettore si connette al servizio cloud.
3. Il connettore e il servizio cloud indirizzano il traffico utente alle applicazioni.

 ![Diagramma del proxy di app di Azure AD](./media/active-directory-appssoaccess-whatis/azureappproxxy.png)

### Approfondimento
1. L'utente accede all'applicazione tramite il proxy di applicazione e viene reindirizzato alla pagina di accesso di Azure AD per l'autenticazione.
2. Una volta che l'accesso è riuscito, un token viene generato e inviato all'utente.
3. L'utente invia il token al proxy di applicazione che recupera dal token il nome dell'entità utente (UPN) e il nome dell'entità sicurezza (SPN) e indirizza la richiesta al connettore.
4. Il connettore richiedere per conto dell'utente un ticket Kerberos che può essere usato per l'autenticazione interna (Windows). Questo passaggio è noto come delega vincolata Kerberos.
5. Un ticket Kerberos viene recuperato da Active Directory.
6. Il ticket viene inviato al server applicazioni e verificato.
7. La risposta viene inviata all'utente tramite il proxy di applicazione.

## Articoli correlati
- [Abilitazione del proxy di applicazione di Azure AD](active-directory-application-proxy-enable.md#step-1-enable-application-proxy-in-azure-ad)
- [Pubblicazione di applicazioni mediante il proxy di app di Azure AD](active-directory-application-proxy-publish.md)

<!---HONumber=Oct15_HO4-->