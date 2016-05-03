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
	ms.date="04/20/2016"
	ms.author="billmath"/>

# Domande frequenti su Azure AD Connect

## Installazione generale
**D: L'installazione verrà eseguita correttamente se per l'amministratore globale di Active Directory Azure è abilitata l'autenticazione a due fattori?** Questa opzione è supportata nelle build rilasciate a partire da febbraio 2016.

**D: Esiste un modo per eseguire l'installazione automatica di Azure AD Connect?** È supportata solo l'installazione di Azure AD Connect tramite l'installazione guidata. L'installazione automatica e invisibile all'utente non è supportata.

**D: Ho una foresta in cui un dominio non può essere contattato. Come installare Azure AD Connect?** Questa opzione è supportata nelle build rilasciate a partire da febbraio 2016.

## Rete
**D: Ho un firewall, un dispositivo di rete o qualcos’altro che limita il tempo massimo in cui le connessioni possono rimanere aperte sulla mia rete. Quanto deve durare la soglia di timeout lato client quando si utilizza Connetti AD Azure?** Tutti i software di rete, i dispositivi fisici o qualsiasi altra cosa che limiti il tempo massimo delle connessioni possono rimanere aperti qualora si utilizzi una soglia di almeno 5 minuti (300 secondi) per la connettività tra il server in cui è installato il client AD Azure Connect e la Azure Active Directory. Questo vale anche per tutti gli strumenti di sincronizzazione Microsoft Identity rilasciati in precedenza.

**D: I domini SDL sono supportati?** No, Azure AD Connect non supporta foreste/domini in locale mediante i domini SLD.

**D: Gli elementi denominati NetBios con "punti" sono supportati?** No, Azure AD Connect non supporta foreste/domini in locale il cui nome NetBios contenga un periodo "." nel nome.

## Federazione
**D: Cosa occorre fare se si riceve un messaggio di posta elettronica che richiede il rinnovo del certificato di Office 365?** Usare le indicazioni specifiche disponibili nell'argomento che spiega come [rinnovare i certificati](active-directory-aadconnect-o365-certs.md).

**D: L'opzione di aggiornamento automatico della relying party è impostata per la relying party Office 365. È necessario eseguire un'azione quando il certificato per la firma di token esegue automaticamente il rollover?** Usare le linee guida descritte nell'articolo relativo al [rinnovamento dei certificati](active-directory-aadconnect-o365-certs.md).

## Environment
**D: Il fatto di rinominare il server dopo l'installazione di Azure AD Connect è supportato?** No. La modifica del nome del server farà sì che il motore di sincronizzazione no sia in grado di connettersi al database SQL e il servizio non riuscirà ad avviarsi.

## Dati di identità
**D: Perché l'attributo UPN (userPrincipalName) in Azure AD non corrisponde con l’UPN locale?** Vedere i seguenti articoli:

- [I nomi utente in Office 365, Azure o Intune non corrispondono agli ID di accesso o alternativi dell’UPN locale](https://support.microsoft.com/it-IT/kb/2523192)
- [Le modifiche non sono sincronizzate dallo strumento di sincronizzazione di Azure Active Directory dopo aver modificato l'UPN di un account utente per usare un dominio federato diverso](https://support.microsoft.com/it-IT/kb/2669550)

È anche possibile configurare Azure AD per consentire al motore di sincronizzazione di aggiornare userPrincipalName come descritto in [Funzionalità del servizio di sincronizzazione di Azure AD Connect](active-directory-aadconnectsyncservice-features.md).

## Configurazione personalizzata
**D: Dove sono documentati i cmdlet PowerShell per Azure AD Connect?** Fatta eccezione per i cmdlet documentati in questo sito, gli altri cmdlet di PowerShell disponibili in Azure AD Connect di non sono supportati per l'utilizzo da parte degli utenti.

**D: Si può usare l'opzione "Server export/server import" disponibile in *Synchronization Service Manager* per spostare la configurazione tra i server?** No. Questa opzione non recupererà tutte le impostazioni di configurazione e non deve essere utilizzata. Si dovrebbe invece utilizzare la procedura guidata per creare la configurazione di base sul secondo server e usare l'editor delle regole di sincronizzazione per generare script di PowerShell per spostare qualsiasi regola personalizzata tra i server. Vedere [Spostare la configurazione personalizzata da server attivo a server di gestione temporanea](active-directory-aadconnect-upgrade-previous-version.md#move-custom-configuration-from-active-to-staging-server).

## Risoluzione dei problemi
**D: Come è possibile ottenere informazioni su Azure AD Connect?**

[Ricercare nella Microsoft Knowledge Base (KB)](https://www.microsoft.com/it-IT/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

- Cercare nella Microsoft Knowledge Base (KB) soluzioni tecniche per problemi comuni in garanzia riguardanti il supporto per Azure AD Connect.

[Forum di Microsoft Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/it-IT/home?forum=WindowsAzureAD)

- Per cercare e passare in rassegna domande e risposte tecniche della community o porre domande, fare clic [qui](https://social.msdn.microsoft.com/Forums/azure/it-IT/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Assistenza clienti per Azure AD Connect](https://manage.windowsazure.com/?getsupport=true)

- Usare questo collegamento per ottenere assistenza tramite il portale di Azure.

<!---HONumber=AcomDC_0420_2016-->