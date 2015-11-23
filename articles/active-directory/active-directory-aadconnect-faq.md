<properties
	pageTitle="Domande frequenti su Azure AD Connect | Microsoft Azure"
	description="Questa pagina contiene le domande frequenti su Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/10/2015"
	ms.author="billmath"/>

# Domande frequenti su Azure Active Directory Connect

## Installazione generale
**D: L'installazione verrà eseguita correttamente se per l'amministratore globale di Active Directory Azure è abilitata l'autenticazione a due fattori?**

In questo caso, l'installazione non verrà eseguita. Per l'amministratore globale che esegue l'installazione di Azure AD Connect non deve essere abilitata 'autenticazione MFA. Siamo consapevoli di questa limitazione e questa funzionalità verrà supportata in futuro.

**D: Esiste un modo per eseguire l'installazione automatica di Azure AD Connect?**

È supportata solo l'installazione di Azure AD Connect tramite l'installazione guidata. L'installazione automatica e invisibile all'utente non è supportata.

## Installazione rapida

## Installazione personalizzata

## Rete
**D: Ho un firewall, un dispositivo di rete o qualcos'altro che limita il tempo massimo in cui le connessioni possono rimanere aperte sulla mia rete. Quanto deve durare la soglia di timeout lato client quando si utilizza Connetti AD Azure?**

Tutti i software di rete, i dispositivi fisici o qualsiasi altra cosa che limiti il tempo massimo delle connessioni possono rimanere aperti qualora si utilizzi una soglia di almeno 5 minuti (300 secondi) per la connettività tra il server in cui è installato il client AD Azure Connect e la Azure Active Directory. Questo vale anche per tutti gli strumenti di sincronizzazione Microsoft Identity rilasciati in precedenza.

**D: Cosa fare se ricevo un messaggio di posta elettronica che è necessario rinnovare il certificato di Office 365**

Usare le linee guida descritte nell'articolo disponibile [qui](active-directory-aadconnect-o365-certs.md) per rinnovare il certificato.

## Risoluzione dei problemi

**D: Come è possibile ottenere informazioni su Azure AD Connect?**

[Ricercare nella Microsoft Knowledge Base (KB)](https://www.microsoft.com/it-IT/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

- Cercare nella Microsoft Knowledge Base (KB) soluzioni tecniche per problemi comuni in garanzia riguardanti il supporto per Azure AD Connect.

[Forum di Microsoft Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/it-IT/home?forum=WindowsAzureAD)

- È possibile cercare e sfogliare le domande e risposte tecniche dalla community o porre le proprie domande facendo clic [qui](https://social.msdn.microsoft.com/Forums/azure/it-IT/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).


[Assistenza clienti per Azure AD Connect](https://manage.windowsazure.com/?getsupport=true)

- Usare questo collegamento per ottenere assistenza tramite il portale di Azure.

<!---HONumber=Nov15_HO3-->