---
title: Eventi di rischio di Azure Active Directory | Microsoft Docs
description: Questo argomento presenta una panoramica dettagliata degli eventi di rischio.
services: active-directory
keywords: "Azure Active Directory Identity Protection, sicurezza, rischio, livello di rischio, vulnerabilità, criteri di sicurezza"
author: MarkusVi
manager: femila
ms.assetid: fa2c8b51-d43d-4349-8308-97e87665400b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: a454199137f8ccc99ddbef66758fd1cabd8fd486
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2017
---
# <a name="azure-active-directory-risk-events"></a>Eventi di rischio di Azure Active Directory

La maggior parte delle violazioni della sicurezza si verifica quando utenti malintenzionati ottengono l'accesso a un ambiente impadronendosi dell'identità di un utente. Trovare le identità compromesse non è un compito facile. Azure Active Directory usa l'euristica e gli algoritmi adattivi di Machine Learning per rilevare azioni sospette correlate agli account dell'utente. Ogni azione sospetta rilevata viene archiviata in un record denominato *evento di rischio*.

Azure Active Directory rileva attualmente sei tipi di eventi di rischio:

- [Utenti con credenziali perse](#leaked-credentials) 
- [Accessi da indirizzi IP anonimi](#sign-ins-from-anonymous-ip-addresses) 
- [Trasferimento impossibile a posizioni atipiche](#impossible-travel-to-atypical-locations) 
- [Accessi da dispositivi infetti](#sign-ins-from-infected-devices) 
- [Accessi da indirizzi IP con attività sospette](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [Accessi da posizioni non note](#sign-in-from-unfamiliar-locations) (esclusivamente per le edizioni di **Azure Active Directory Premium P2**)

![Evento di rischio](./media/active-directory-reporting-risk-events/91.png)

In questo argomento viene fatta una panoramica dettagliata della definizione di eventi di rischio e di come è possibile usarli per proteggere le identità di Azure AD.


## <a name="risk-event-types"></a>Tipi di evento di rischio

La proprietà dei tipi di evento di rischio è un identificatore dell'azione sospetta per cui è stato creato un record di evento.  
Gli investimenti continui di Microsoft nel processo di rilevamento garantiscono:

- Miglioramenti nell'accuratezza del rilevamento degli eventi di rischio esistenti 
- Nuovi tipi di eventi di rischio che verranno aggiunti in futuro

### <a name="leaked-credentials"></a>Credenziali perse

I criminali informatici, quando compromettono password valide di utenti legittimi, spesso condividono tali credenziali, in genere pubblicandole sul dark Web o su pastebin oppure scambiandole o vendendole al mercato nero. Il servizio Credenziali perse di Microsoft acquisisce coppie di nome utente/password monitorando i siti Web pubblici e il dark Web e collaborando con:

- Ricercatori
- Forze dell'ordine
- Team di sicurezza di Microsoft
- Altre fonti attendibili 

Quando le coppie di nome utente/password vengono acquisite dal servizio, vengono confrontate con le credenziali valide correnti degli utenti AAD. Quando viene trovata una corrispondenza, significa che la password di un utente è stata compromessa e si crea un *evento di rischio di credenziali perse*.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Accessi da indirizzi IP anonimi

Questo tipo di evento di rischio identifica gli utenti che hanno eseguito l'accesso da un indirizzo IP riconosciuto come un indirizzo IP proxy anonimo. Questi proxy vengono usati da persone che vogliono nascondere l'indirizzo IP del dispositivo e possono essere usati per attacchi dannosi.


### <a name="impossible-travel-to-atypical-locations"></a>Trasferimento impossibile a posizioni atipiche

Questo tipo di evento di rischio identifica due accessi provenienti da posizioni geograficamente distanti, almeno una delle quali può anche essere atipica in base al comportamento passato dell'utente. Viene preso in considerazione anche il tempo che intercorre tra i due accessi. Se è inferiore al tempo necessario all'utente per spostarsi dalla prima posizione alla seconda, ciò indica che un altro utente sta usando le stesse credenziali. 

Questo algoritmo di Machine Learning ignora i "*falsi positivi*" evidenti che contribuiscono alla condizione di trasferimento impossibile, ad esempio le VPN e le posizioni usate regolarmente da altri utenti nell'organizzazione.  Il sistema ha un periodo di apprendimento iniziale di 14 giorni, durante i quali apprende il comportamento di accesso di un nuovo utente.

### <a name="sign-in-from-unfamiliar-locations"></a>Accessi da posizioni non note

Questo tipo di evento di rischio prende in considerazione le posizioni di accesso precedenti, come l'indirizzo IP, la latitudine, la longitudine e l'ASN, per determinare posizioni nuove o non note. Il sistema archivia le informazioni sulle posizioni usate in precedenza da un utente e le considera posizioni "note". L'evento di rischio viene attivato quando viene eseguito l'accesso da una posizione non inclusa nell'elenco delle posizioni note. Il sistema ha un periodo di apprendimento iniziale di 30 giorni, durante i quali le nuove posizioni non vengono contrassegnate come posizioni non note. Il sistema ignora anche gli accessi da dispositivi noti e le posizioni geograficamente vicine a una posizione nota. 

### <a name="sign-ins-from-infected-devices"></a>Accessi da dispositivi infetti

Questo tipo di evento rischio identifica accessi da dispositivi infettati da malware, che comunicano attivamente con un server bot. Viene determinato mettendo in relazione gli indirizzi IP del dispositivo dell'utente con gli indirizzi IP che sono stati in contatto con un server bot. 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Accessi da indirizzi IP con attività sospette
Questo tipo di evento di rischio identifica gli indirizzi IP da cui si è verificato un numero elevato di tentativi di accesso non riusciti, per più account utente e in un periodo di tempo breve. Questo corrisponde ai modelli di traffico degli indirizzi IP usati da utenti malintenzionati e indica che gli account sono già compromessi o stanno per esserlo. Questo algoritmo di Machine Learning ignora i "*falsi positivi*" evidenti, ad esempio gli indirizzi IP che vengono regolarmente usati da altri utenti nell'organizzazione.  Il sistema ha un periodo di apprendimento iniziale di 14 giorni, durante i quali apprende il comportamento di accesso di un nuovo utente e di un nuovo tenant.


## <a name="detection-type"></a>Tipo di rilevamento

La proprietà del tipo di rilevamento è un indicatore (Tempo reale oppure Offline) dell'intervallo di tempo del rilevamento di un evento di rischio.  
La maggior parte degli eventi di rischio attualmente viene rilevata offline in un'operazione di post-elaborazione dopo che l'evento di rischio si è verificato.

La tabella seguente elenca il periodo di tempo necessario per visualizzare un tipo di rilevamento in un report correlato:

| Tipo di rilevamento | Latenza creazione di report |
| --- | --- |
| Tempo reale | Da 5 a 10 minuti |
| Offline | Da 2 a 4 ore |


Per i tipi di evento di rischio che Azure Active Directory rileva, i tipi di rilevamento sono:

| Tipo di evento di rischio | Tipo di rilevamento |
| :-- | --- | 
| [Utenti con credenziali perse](#leaked-credentials) | Offline |
| [Accessi da indirizzi IP anonimi](#sign-ins-from-anonymous-ip-addresses) | Tempo reale |
| [Trasferimento impossibile a posizioni atipiche](#impossible-travel-to-atypical-locations) | Offline |
| [Accessi da posizioni non note](#sign-in-from-unfamiliar-locations) | Tempo reale |
| [Accessi da dispositivi infetti](#sign-ins-from-infected-devices) | Offline |
| [Accessi da indirizzi IP con attività sospette](#sign-ins-from-ip-addresses-with-suspicious-activity) | Offline|


## <a name="risk-level"></a>Livello di rischio

La proprietà del livello di rischio di un evento di rischio è un indicatore (Alto, Medio o Basso) della gravità e dell'affidabilità di un evento di rischio. Questa proprietà consente di classificare in ordine di priorità le azioni da eseguire. 

La gravità dell'evento di rischio rappresenta il grado di probabilità che l'identità sia compromessa.  
Il livello di affidabilità è un indicatore della possibile presenza di falsi positivi. 

Ad esempio, 

* **Alta**: evento di rischio con livello di gravità elevato e attendibilità elevata. Questi eventi sono fortemente indicativi di una compromissione dell'identità dell'utente e tutti gli account utente interessati devono essere corretti.

* **Media**: evento di rischio con livello di gravità elevato e minore attendibilità o viceversa. Questi eventi sono potenzialmente rischiosi e tutti gli account utente interessati devono essere corretti.

* **Bassa**: evento di rischio con livello di gravità basso e attendibilità bassa. Questi eventi potrebbero non richiedere un'azione immediata, ma in combinazione con altri eventi di rischio possono essere fortemente indicativi di una compromissione dell'identità.

![Livello di rischio](./media/active-directory-reporting-risk-events/01.png)

### <a name="leaked-credentials"></a>Credenziali perse

Gli eventi di rischio correlati a credenziali perse è classificata come **Alta**, perché sono un chiaro indizio del fatto che il nome utente e la password sono a disposizione degli utenti malintenzionati.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Accessi da indirizzi IP anonimi

Il livello di rischio per questo tipo di evento di rischio è **Medio** perché un IP anonimo non è di per sé un'indicazione sicura di una compromissione dell'account.  
È consigliabile contattare immediatamente l'utente per verificare se sta usando indirizzi IP anonimi.


### <a name="impossible-travel-to-atypical-locations"></a>Trasferimento impossibile a posizioni atipiche

Il trasferimento impossibile indica in genere che un pirata informatico è riuscito a ottenere l'accesso. Possono, tuttavia, verificarsi falsi positivi quando un utente è in viaggio e usa un nuovo dispositivo o una VPN che non viene in genere usata da altri utenti nell'organizzazione. Un'altra origine di falsi positivi è costituita dalle applicazioni che passano in modo errato gli indirizzi IP di server come indirizzi IP di client. Questo può dare l'impressione che gli accessi si verifichino dal data center in cui è ospitato il back-end dell'applicazione. Spesso si tratta di data center Microsoft e gli accessi possono risultare eseguiti da indirizzi IP di proprietà di Microsoft. A causa di questi falsi positivi, il livello di rischio per questo evento di rischio è **Medio**.

> [!TIP]
> È possibile ridurre la quantità di falsi positivi segnalati per questo tipo di evento di rischio configurando le [località denominate](active-directory-named-locations.md). 

### <a name="sign-in-from-unfamiliar-locations"></a>Accessi da posizioni non note

Le posizioni non note possono indicare che un utente malintenzionato ha la possibilità di usare un'identità rubata. Possono verificarsi falsi positivi quando un utente è in viaggio, sta provando un nuovo dispositivo o usando una nuova VPN. A causa di questi falsi positivi, il livello di rischio per questo tipo di evento è **Medio**.

### <a name="sign-ins-from-infected-devices"></a>Accessi da dispositivi infetti

Questo evento di rischio identifica gli indirizzi IP, non i dispositivi degli utenti. Se più dispositivi usano un unico indirizzo IP e solo alcuni sono controllati da una rete bot, gli accessi provenienti da altri dispositivi possono attivare l'evento inutilmente. Ecco perché il livello di questo evento di rischio è classificato come **Basso**.  

È consigliabile contattare l'utente e di eseguire la scansione di tutti i relativi dispositivi. È anche possibile che un dispositivo personale dell'utente sia stato infettato o, come indicato in precedenza, che qualcun altro stia usando un dispositivo infetto dallo stesso indirizzo IP dell'utente. I dispositivi infetti sono spesso infettati da malware non ancora identificati dai software antivirus e possono anche essere indicativi di abitudini errate dell'utente.

Per altre informazioni su come risolvere problemi correlati alle infezioni malware, vedere [Malware Protection Center](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409).


### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Accessi da indirizzi IP con attività sospette

È consigliabile contattare l'utente per verificare se ha effettivamente eseguito l'accesso da un indirizzo IP contrassegnato come sospetto. Il livello di rischio per questo tipo di evento è "**medio**" perché lo stesso indirizzo IP può essere usato da più dispositivi, mentre solo alcuni di essi potrebbero essere responsabili dell'attività sospetta. 


 
## <a name="next-steps"></a>Passaggi successivi

Gli eventi di rischio costituiscono la base per la protezione delle identità di Azure AD. Azure AD attualmente è in grado di rilevare sei eventi di rischio: 


| Tipo di evento di rischio | Livello di rischio | Tipo di rilevamento |
| :-- | --- | --- |
| [Utenti con credenziali perse](#leaked-credentials) | Alto | Offline |
| [Accessi da indirizzi IP anonimi](#sign-ins-from-anonymous-ip-addresses) | Media | Tempo reale |
| [Trasferimento impossibile a posizioni atipiche](#impossible-travel-to-atypical-locations) | Media | Offline |
| [Accessi da posizioni non note](#sign-in-from-unfamiliar-locations) | Media | Tempo reale |
| [Accessi da dispositivi infetti](#sign-ins-from-infected-devices) | Basso | Offline |
| [Accessi da indirizzi IP con attività sospette](#sign-ins-from-ip-addresses-with-suspicious-activity) | Media | Offline|

Dove è possibile trovare gli eventi di rischio che sono stati rilevati nell'ambiente in uso?
Esistono due posizioni in cui è possibile esaminare gli eventi di rischio segnalati:

 - **Creazione di report di AD Azure**: gli eventi di rischio fanno parte dei report sulla sicurezza di Azure AD. Per altre informazioni, vedere il [report sugli utenti la cui sicurezza è a rischio](active-directory-reporting-security-user-at-risk.md) e [il report sulla sicurezza degli accessi a rischio](active-directory-reporting-security-risky-sign-ins.md).

 - **Azure AD Identity Protection:** gli eventi di rischio fanno parte anche delle capacità di segnalazione di [Azure AD Identity Protection](active-directory-identityprotection.md).
    

Sebbene il rilevamento di eventi di rischio rappresenti un aspetto importante per la protezione delle identità, è anche possibile risolverli manualmente o implementare risposte automatiche mediante la configurazione di criteri di accesso condizionale. Per altre informazioni, consultare l'articolo su [Azure Active Directory Identity Protection](active-directory-identityprotection.md).
 
