<properties
	pageTitle="Tipi di eventi di rischio rilevati da Azure Active Directory Identity Protection | Microsoft Azure"
	description="Questo argomento presenta una panoramica dettagliata dei tipi di eventi di rischio disponibili in Azure Active Directory Identity Protection"
	services="active-directory"
	keywords="azure active directory identity protection, cloud app discovery, gestione applicazioni, sicurezza, rischio, livello di rischio, vulnerabilità, criteri di sicurezza"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/04/2016"
	ms.author="markvi"/>

#Tipi di eventi di rischio rilevati da Azure Active Directory Identity Protection 

In Azure Active Directory Identity Protection gli eventi di rischio sono eventi che:

- sono stati contrassegnati come sospetti

- indicano che un'identità potrebbe essere stata compromessa.

Questo argomento presenta una panoramica dettagliata dei tipi di eventi di rischio disponibili.


## Credenziali perse

I ricercatori Microsoft sulla sicurezza trovano le credenziali perse pubblicate nel Dark Web, in genere in formato testo normale. Queste vengono confrontate con le credenziali di Azure AD e, in caso di corrispondenza, vengono segnalate come "credenziali perse" in Identity Protection.

La gravità degli eventi di rischio correlati a credenziali perse è classificata come "alta", perché sono un chiaro indizio del fatto che il nome utente e la password sono a disposizione degli utenti malintenzionati.

## Trasferimento impossibile a posizioni atipiche

Questo tipo di evento di rischio identifica due accessi provenienti da posizioni geograficamente distanti, almeno una delle quali può anche essere atipica in base al comportamento passato dell'utente. Viene preso in considerazione anche il tempo che intercorre tra i due accessi. Se è inferiore al tempo necessario all'utente per spostarsi dalla prima posizione alla seconda, ciò indica che un altro utente sta usando le stesse credenziali.

Questo algoritmo di Machine Learning ignora i "*falsi positivi*" evidenti che contribuiscono alla condizione di trasferimento impossibile, ad esempio le VPN e le posizioni usate regolarmente da altri utenti nell'organizzazione. Il sistema ha un periodo di apprendimento iniziale di 14 giorni, durante i quali apprende il comportamento di accesso di un nuovo utente.

Il trasferimento impossibile indica in genere che un pirata informatico è riuscito a ottenere l'accesso. Possono, tuttavia, verificarsi falsi positivi quando un utente è in viaggio e usa un nuovo dispositivo o una VPN che non viene in genere usata da altri utenti nell'organizzazione. Un'altra origine di falsi positivi è costituita dalle applicazioni che passano in modo errato gli indirizzi IP di server come indirizzi IP di client. Questo può dare l'impressione che gli accessi si verifichino dal data center in cui è ospitato il back-end dell'applicazione. Spesso si tratta di data center Microsoft e gli accessi possono risultare eseguiti da indirizzi IP di proprietà di Microsoft. A causa di questi falsi positivi, il livello di rischio per questo evento di rischio è "**medio**".

##Accessi da dispositivi infetti

Questo tipo di evento rischio identifica accessi da dispositivi infettati da malware, che comunicano attivamente con un server bot. Viene determinato mettendo in relazione gli indirizzi IP del dispositivo dell'utente con gli indirizzi IP che sono stati in contatto con un server bot.

Questo evento di rischio identifica gli indirizzi IP, non i dispositivi degli utenti. Se più dispositivi usano un unico indirizzo IP e solo alcuni sono controllati da una rete bot, gli accessi provenienti da altri dispositivi possono attivare l'evento inutilmente. Ecco perché il livello di questo evento di rischio è classificato come "**basso**".

È consigliabile contattare l'utente e di eseguire la scansione di tutti i relativi dispositivi. È anche possibile che un dispositivo personale dell'utente sia stato infettato o, come indicato in precedenza, che qualcun altro stia usando un dispositivo infetto dallo stesso indirizzo IP dell'utente. I dispositivi infetti sono spesso infettati da malware non ancora identificati dai software antivirus e possono anche essere indicativi di abitudini errate dell'utente.

Per altre informazioni su come risolvere problemi correlati alle infezioni malware, vedere [Malware Protection Center](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409).


## Accessi da indirizzi IP anonimi

Questo tipo di evento di rischio identifica gli utenti che hanno eseguito l'accesso da un indirizzo IP riconosciuto come un indirizzo IP proxy anonimo. Questi proxy vengono usati da persone che vogliono nascondere l'indirizzo IP del dispositivo e possono essere usati per attacchi dannosi.

È consigliabile contattare immediatamente l'utente per verificare se sta usando indirizzi IP anonimi. Il livello di rischio per questo tipo di evento di rischio è "**medio**" perché un IP anonimo non è di per sé un'indicazione sicura di una compromissione dell'account.

## Accessi da indirizzi IP con attività sospette

Questo tipo di evento di rischio identifica gli indirizzi IP da cui si è verificato un numero elevato di tentativi di accesso non riusciti, per più account utente e in un periodo di tempo breve. Questo corrisponde ai modelli di traffico degli indirizzi IP usati da utenti malintenzionati e indica che gli account sono già compromessi o stanno per esserlo. Questo algoritmo di Machine Learning ignora i "*falsi positivi*" evidenti, ad esempio gli indirizzi IP che vengono regolarmente usati da altri utenti nell'organizzazione. Il sistema ha un periodo di apprendimento iniziale di 14 giorni, durante i quali apprende il comportamento di accesso di un nuovo utente e di un nuovo tenant.

È consigliabile contattare l'utente per verificare se ha effettivamente eseguito l'accesso da un indirizzo IP contrassegnato come sospetto. Il livello di rischio per questo tipo di evento è "**medio**" perché lo stesso indirizzo IP può essere usato da più dispositivi, mentre solo alcuni di essi potrebbero essere responsabili dell'attività sospetta.


## Accessi da posizioni non note

Questo tipo di evento di rischio è un meccanismo di valutazione dell'accesso in tempo reale che prende in considerazione le posizioni di accesso precedenti, come l'indirizzo IP, la latitudine, la longitudine e l'ASN, per determinare posizioni nuove o non note. Il sistema archivia le informazioni sulle posizioni usate in precedenza da un utente e le considera posizioni "note". L'evento di rischio viene attivato quando viene eseguito l'accesso da una posizione non inclusa nell'elenco delle posizioni note. Il sistema ha un periodo di apprendimento iniziale di 14 giorni, durante i quali le nuove posizioni non vengono contrassegnate come posizioni non note. Il sistema ignora anche gli accessi da dispositivi noti e le posizioni geograficamente vicine a una posizione nota. <br> Le posizioni non note possono indicare che un utente malintenzionato ha la possibilità di provare a usare un'identità rubata. Possono verificarsi falsi positivi quando un utente è in viaggio, sta provando un nuovo dispositivo o usa una nuova VPN. A causa di questi falsi positivi, il livello di rischio per questo tipo di evento è "**Medio**".





## Vedere anche

- [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

<!---HONumber=AcomDC_0309_2016-->