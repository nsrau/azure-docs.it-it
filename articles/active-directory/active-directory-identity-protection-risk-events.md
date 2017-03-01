---
title: Eventi di rischio di Azure Active Directory | Documentazione Microsoft
description: Questo argomento presenta una panoramica dettagliata degli eventi di rischio.
services: active-directory
keywords: "Azure Active Directory Identity Protection, sicurezza, rischio, livello di rischio, vulnerabilità, criteri di sicurezza"
author: MarkusVi
manager: femila
ms.assetid: ce3b054c-7772-401f-9b06-3d24f6e7ca86
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: e37f1716b459ea8eb0f2d0a5a12e7a3a8ab2c083
ms.openlocfilehash: e2ec0cefb6e009f22c3406ccd1570748dc467ed0
ms.lasthandoff: 02/22/2017


---
# <a name="azure-active-directory-risk-events"></a>Eventi di rischio di Azure Active Directory

Azure Active Directory consente di proteggere le identità. Una parte del processo di protezione è il rilevamento di azioni sospette correlate agli account utente. Il rilevamento si basa sull'euristica e sugli algoritmi di apprendimento automatico adattivi. Ogni azione sospetta rilevata viene archiviata in un record denominato *evento di rischio*. 


![Evento di rischio](./media/active-directory-identity-protection-risk-events/91.png)


Azure Active Directory rileva attualmente sei tipi di eventi di rischio.

| Tipo di evento di rischio | Livello di rischio | Tipo di rilevamento |
| :-- | --- | --- |
| [Utenti con credenziali perse](#leaked-credentials) | Alto | Offline |
| [Accessi da indirizzi IP anonimi](#sign-ins-from-anonymous-ip-addresses) | Media | Tempo reale |
| [Trasferimento impossibile a posizioni atipiche](#impossible-travel-to-atypical-locations) | Media | Offline |
| [Accessi da posizioni non note](#sign-in-from-unfamiliar-locations) | Media | Tempo reale |
| [Accessi da dispositivi infetti](#sign-ins-from-infected-devices) | Basso | Offline |
| [Accessi da indirizzi IP con attività sospette](#sign-ins-from-ip-addresses-with-suspicious-activity) | Media | Offline|

## <a name="risk-level"></a>Livello di rischio

La proprietà del livello di rischio è un indicatore (Alto, Medio o Basso) della gravità di un evento di rischio. Questa proprietà consente di classificare in ordine di priorità le azioni da eseguire. 

La gravità dell'evento di rischio rappresenta il grado di probabilità che l'identità sia compromessa, unita alla quantità di disturbo che in genere produce.

* **Alta**: evento di rischio con livello di gravità elevato e attendibilità elevata. Questi eventi sono fortemente indicativi di una compromissione dell'identità dell'utente e tutti gli account utente interessati devono essere corretti.

* **Media**: evento di rischio con livello di gravità elevato e minore attendibilità o viceversa. Questi eventi sono potenzialmente rischiosi e tutti gli account utente interessati devono essere corretti.

* **Bassa**: evento di rischio con livello di gravità basso e attendibilità bassa. Questi eventi potrebbero non richiedere un'azione immediata, ma in combinazione con altri eventi di rischio possono essere fortemente indicativi di una compromissione dell'identità.

![Livello di rischio](./media/active-directory-identity-protection-risk-events/01.png)


## <a name="detection-type"></a>Tipo di rilevamento

La proprietà del tipo di rilevamento è un indicatore (Tempo reale, Offline) dell'intervallo di tempo del rilevamento di un evento di rischio.  
La maggior parte degli eventi di rischio attualmente viene rilevata offline in un'operazione di post-elaborazione dopo che l'evento di rischio si è verificato.

La tabella seguente elenca il periodo di tempo necessario per visualizzare un tipo di rilevamento in un report correlato.

| Tipo di rilevamento | Latenza creazione di report |
| --- | --- |
| Tempo reale | Da 5 a 10 minuti |
| Offline | Da 2 a 4 ore |



## <a name="risk-event-types"></a>Tipi di evento di rischio

La proprietà dei tipi di evento di rischio è un identificatore dell'azione sospetta per cui è stato creato un record di evento.  
Azure Active Directory rileva attualmente sei tipi di eventi.

Gli investimenti continui di Microsoft nel processo di rilevamento garantiscono:

- Miglioramenti nell'accuratezza del rilevamento degli eventi di rischio esistenti 
- Nuovi tipi di eventi di rischio che verranno aggiunti in futuro

### <a name="leaked-credentials"></a>Credenziali perse
I ricercatori Microsoft sulla sicurezza trovano le credenziali perse pubblicate nel Dark Web, in genere in formato testo normale. Queste vengono confrontate con le credenziali di Azure AD e, in caso di corrispondenza, vengono segnalate come "credenziali perse" in Identity Protection.

La gravità degli eventi di rischio correlati a credenziali perse è classificata come "alta", perché sono un chiaro indizio del fatto che il nome utente e la password sono a disposizione degli utenti malintenzionati.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Accessi da indirizzi IP anonimi
Questo tipo di evento di rischio identifica gli utenti che hanno eseguito l'accesso da un indirizzo IP riconosciuto come un indirizzo IP proxy anonimo. Questi proxy vengono usati da persone che vogliono nascondere l'indirizzo IP del dispositivo e possono essere usati per attacchi dannosi.

È consigliabile contattare immediatamente l'utente per verificare se sta usando indirizzi IP anonimi. Il livello di rischio per questo tipo di evento di rischio è "**medio**" perché un IP anonimo non è di per sé un'indicazione sicura di una compromissione dell'account.


### <a name="impossible-travel-to-atypical-locations"></a>Trasferimento impossibile a posizioni atipiche
Questo tipo di evento di rischio identifica due accessi provenienti da posizioni geograficamente distanti, almeno una delle quali può anche essere atipica in base al comportamento passato dell'utente. Viene preso in considerazione anche il tempo che intercorre tra i due accessi. Se è inferiore al tempo necessario all'utente per spostarsi dalla prima posizione alla seconda, ciò indica che un altro utente sta usando le stesse credenziali. 

Questo algoritmo di Machine Learning ignora i "*falsi positivi*" evidenti che contribuiscono alla condizione di trasferimento impossibile, ad esempio le VPN e le posizioni usate regolarmente da altri utenti nell'organizzazione.  Il sistema ha un periodo di apprendimento iniziale di 14 giorni, durante i quali apprende il comportamento di accesso di un nuovo utente.

Il trasferimento impossibile indica in genere che un pirata informatico è riuscito a ottenere l'accesso. Possono, tuttavia, verificarsi falsi positivi quando un utente è in viaggio e usa un nuovo dispositivo o una VPN che non viene in genere usata da altri utenti nell'organizzazione. Un'altra origine di falsi positivi è costituita dalle applicazioni che passano in modo errato gli indirizzi IP di server come indirizzi IP di client. Questo può dare l'impressione che gli accessi si verifichino dal data center in cui è ospitato il back-end dell'applicazione. Spesso si tratta di data center Microsoft e gli accessi possono risultare eseguiti da indirizzi IP di proprietà di Microsoft. A causa di questi falsi positivi, il livello di rischio per questo evento di rischio è "**medio**".

### <a name="sign-in-from-unfamiliar-locations"></a>Accessi da posizioni non note
Questo tipo di evento di rischio è un meccanismo di valutazione dell'accesso in tempo reale che prende in considerazione le posizioni di accesso precedenti, come l'indirizzo IP, la latitudine, la longitudine e l'ASN, per determinare posizioni nuove o non note. Il sistema archivia le informazioni sulle posizioni usate in precedenza da un utente e le considera posizioni "note". L'evento di rischio viene attivato quando viene eseguito l'accesso da una posizione non inclusa nell'elenco delle posizioni note. Il sistema ha un periodo di apprendimento iniziale di 14 giorni, durante i quali le nuove posizioni non vengono contrassegnate come posizioni non note. Il sistema ignora anche gli accessi da dispositivi noti e le posizioni geograficamente vicine a una posizione nota. 

Le posizioni non note possono indicare che un utente malintenzionato ha la possibilità di provare a usare un'identità rubata. Possono verificarsi falsi positivi quando un utente è in viaggio, sta provando un nuovo dispositivo o usa una nuova VPN. A causa di questi falsi positivi, il livello di rischio per questo tipo di evento è "**medio**".



### <a name="sign-ins-from-infected-devices"></a>Accessi da dispositivi infetti
Questo tipo di evento rischio identifica accessi da dispositivi infettati da malware, che comunicano attivamente con un server bot. Viene determinato mettendo in relazione gli indirizzi IP del dispositivo dell'utente con gli indirizzi IP che sono stati in contatto con un server bot. 

Questo evento di rischio identifica gli indirizzi IP, non i dispositivi degli utenti. Se più dispositivi usano un unico indirizzo IP e solo alcuni sono controllati da una rete bot, gli accessi provenienti da altri dispositivi possono attivare l'evento inutilmente. Ecco perché il livello di questo evento di rischio è classificato come "**basso**".  

È consigliabile contattare l'utente e di eseguire la scansione di tutti i relativi dispositivi. È anche possibile che un dispositivo personale dell'utente sia stato infettato o, come indicato in precedenza, che qualcun altro stia usando un dispositivo infetto dallo stesso indirizzo IP dell'utente. I dispositivi infetti sono spesso infettati da malware non ancora identificati dai software antivirus e possono anche essere indicativi di abitudini errate dell'utente.

Per altre informazioni su come risolvere problemi correlati alle infezioni malware, vedere [Malware Protection Center](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409).


### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Accessi da indirizzi IP con attività sospette
Questo tipo di evento di rischio identifica gli indirizzi IP da cui si è verificato un numero elevato di tentativi di accesso non riusciti, per più account utente e in un periodo di tempo breve. Questo corrisponde ai modelli di traffico degli indirizzi IP usati da utenti malintenzionati e indica che gli account sono già compromessi o stanno per esserlo. Questo algoritmo di Machine Learning ignora i "*falsi positivi*" evidenti, ad esempio gli indirizzi IP che vengono regolarmente usati da altri utenti nell'organizzazione.  Il sistema ha un periodo di apprendimento iniziale di 14 giorni, durante i quali apprende il comportamento di accesso di un nuovo utente e di un nuovo tenant.

È consigliabile contattare l'utente per verificare se ha effettivamente eseguito l'accesso da un indirizzo IP contrassegnato come sospetto. Il livello di rischio per questo tipo di evento è "**medio**" perché lo stesso indirizzo IP può essere usato da più dispositivi, mentre solo alcuni di essi potrebbero essere responsabili dell'attività sospetta. 





## <a name="azure-ad-anomalous-activity-reports"></a>Report di Anomalie dell'attività di Azure AD

Nel portale di Azure classico alcuni degli eventi di rischio sono già disponibili nei report di Anomalie dell'attività di Azure AD. 

La tabella seguente riporta un elenco dei vari tipi di eventi di rischio e i report di **Anomalie dell'attività di Azure AD** corrispondenti. 

| Tipo di evento di rischio di Identity Protection | Report di Anomalie dell'attività di Azure AD corrispondente |
|:--- |:--- |
| Credenziali perse |Utenti con credenziali perse |
| Trasferimento impossibile a posizioni atipiche |Attività di accesso irregolare |
| Accessi da dispositivi infetti |Accessi da dispositivi potenzialmente infetti |
| Accessi da indirizzi IP anonimi |Accessi da origini sconosciute |
| Accessi da indirizzi IP con attività sospette |Accessi da indirizzi IP con attività sospette |
| Accessi da posizioni non note |- |


I report di Anomalie dell'attività di Azure AD seguenti non sono inclusi come eventi di rischio in Azure AD Identity Protection e non sono quindi disponibili in Identity Protection. Questi report sono ancora disponibili nel portale di Azure classico, ma in futuro saranno deprecati perché progressivamente sostituiti dagli eventi di rischio di Identity Protection.

* Accessi dopo più errori
* Accessi da più aree geografiche






## <a name="next-steps"></a>Passaggi successivi
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)


