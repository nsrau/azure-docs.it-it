---
title: Rilevamento Azure Active Directory rischi | Microsoft Docs
description: Questo Artice offre una panoramica dettagliata dei rilevamenti dei rischi.
services: active-directory
keywords: Azure Active Directory Identity Protection, sicurezza, rischio, livello di rischio, vulnerabilità, criteri di sicurezza
author: cawrites
manager: daveba
ms.assetid: fa2c8b51-d43d-4349-8308-97e87665400b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 523ae8e1ba31a4fe2c9683007f717149dfdc3bc6
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127329"
---
# <a name="azure-active-directory-risk-detections"></a>Rilevamento del rischio Azure Active Directory

La maggior parte delle violazioni della sicurezza si verifica quando utenti malintenzionati ottengono l'accesso a un ambiente impadronendosi dell'identità di un utente. Trovare le identità compromesse non è un compito facile. Azure Active Directory usa l'euristica e gli algoritmi adattivi di Machine Learning per rilevare azioni sospette correlate agli account dell'utente. Ogni azione sospetta rilevata viene archiviata in un record denominato **rilevamento del rischio**.

Esistono due posizioni in cui è possibile esaminare i rilevamenti dei rischi segnalati:

 - **Azure ad report** : i rilevamenti dei rischi fanno parte dei report di sicurezza di Azure ad. Per altre informazioni, vedere il [report sugli utenti la cui sicurezza è a rischio](concept-user-at-risk.md) e [il report sulla sicurezza degli accessi a rischio](concept-risky-sign-ins.md).

 - I rilevamenti a rischio **Azure ad Identity Protection** fanno anche parte delle funzionalità di creazione di report di [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

Inoltre, è possibile usare l' [API rilevamento dei rischi di Identity Protection](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) per ottenere l'accesso a livello di codice ai rilevamenti di sicurezza usando Microsoft Graph. Per altre informazioni, vedere [Introduzione a Azure Active Directory Identity Protection e Microsoft Graph](../identity-protection/graph-get-started.md). 

Attualmente, Azure Active Directory rileva sei tipi di rilevamento dei rischi:

- [Utenti con credenziali perse](#leaked-credentials) 
- [Accessi da indirizzi IP anonimi](#sign-ins-from-anonymous-ip-addresses) 
- [Trasferimento impossibile a posizioni atipiche](#impossible-travel-to-atypical-locations) 
- [Accessi da dispositivi infetti](#sign-ins-from-infected-devices) 
- [Accessi da indirizzi IP con attività sospette](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [Accessi da posizioni non note](#sign-in-from-unfamiliar-locations) 

![Rilevamento del rischio](./media/concept-risk-events/91.png)

> [!IMPORTANT]
> In alcuni casi, è possibile che si verifichi un rilevamento dei rischi senza una voce di accesso corrispondente nel [report](concept-sign-ins.md)degli accessi. Questo avviene perché Identity Protection valuta il rischio per gli accessi sia **interattivi** che **non interattivi** mentre il report sugli accessi mostra solo gli accessi interattivi.

Le informazioni che si ottengono per un rilevamento dei rischi rilevato sono legate alla sottoscrizione del Azure AD. 

* Con l'**edizione Azure AD Premium P2**, si ottengono le informazioni più dettagliate su tutti i rilevamenti sottostanti. 
* Con l' **edizione Azure ad Premium P1**, i rilevamenti avanzati (ad esempio le proprietà di accesso non note) non sono coperti dalla licenza e verranno visualizzati sotto il nome **accesso con rischi aggiuntivi**rilevati. Inoltre, i campi livello di rischio e dettagli rischio sono nascosti.

Sebbene il rilevamento dei rilevamenti dei rischi rappresenti già un aspetto importante della protezione delle identità, è anche possibile indirizzarli manualmente o implementare risposte automatiche configurando i criteri di accesso condizionale. Per altre informazioni, vedere [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="risk-detection-types"></a>Tipi di rilevamento del rischio

La proprietà del **tipo di rilevamento del rischio** è un identificatore per l'azione sospetta per cui è stato creato un record di rilevamento dei rischi.

Gli investimenti continui di Microsoft nel processo di rilevamento garantiscono:

- Miglioramenti dell'accuratezza del rilevamento dei rilevamenti dei rischi esistenti 
- Nuovi tipi di rilevamento del rischio che verranno aggiunti in futuro

### <a name="leaked-credentials"></a>Credenziali perse

Quando i criminali informatici compromettono password valide di utenti legittimi, spesso condividono tali credenziali, in genere pubblicandole sul dark Web o su pastebin oppure scambiandole o vendendole al mercato nero. Il servizio Credenziali perse di Microsoft acquisisce coppie di nome utente/password monitorando i siti Web pubblici e il dark Web e collaborando con:

- Ricercatori
- Forze dell'ordine
- Team di sicurezza di Microsoft
- Altre fonti attendibili 

Quando le coppie di nome utente/password vengono acquisite dal servizio, vengono confrontate con le credenziali valide correnti degli utenti AAD. Quando viene trovata una corrispondenza, significa che la password di un utente è stata compromessa e che è stato creato un rilevamento dei rischi per le **credenziali perse** .

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Accessi da indirizzi IP anonimi

Questo tipo di rilevamento dei rischi identifica gli utenti che hanno eseguito l'accesso da un indirizzo IP che è stato identificato come indirizzo IP proxy anonimo. Questi proxy vengono usati da persone che vogliono nascondere l'indirizzo IP del dispositivo e possono essere usati per attacchi dannosi.

### <a name="impossible-travel-to-atypical-locations"></a>Trasferimento impossibile a posizioni atipiche

Questo tipo di rilevamento dei rischi identifica due accessi provenienti da località geograficamente distanti, in cui almeno una delle posizioni può essere atipica per l'utente, dato il comportamento precedente. Oltre ad altri fattori, l'algoritmo di apprendimento automatico prende in considerazione il tempo che intercorre tra i due accessi e il tempo necessario all'utente per spostarsi dalla prima posizione alla seconda, che indica che un altro utente sta usando le stesse credenziali.

L'algoritmo ignora i "falsi positivi" evidenti che contribuiscono alle condizioni di impossibilità del trasferimento, ad esempio le VPN e le posizioni usate regolarmente da altri utenti nell'organizzazione. Il sistema ha un periodo di apprendimento iniziale di 14 giorni, durante i quali apprende il comportamento di accesso di un nuovo utente. 

### <a name="sign-in-from-unfamiliar-locations"></a>Accessi da posizioni non note

Questo tipo di rilevamento del rischio prende in considerazione le posizioni di accesso precedenti (IP, Latitudine/Longitudine e ASN) per determinare posizioni nuove o non note. Il sistema archivia le informazioni sulle posizioni usate in precedenza da un utente e le considera posizioni "note". Il rilevamento dei rischi viene attivato quando viene eseguito l'accesso da una posizione non ancora presente nell'elenco di posizioni note. Il sistema ha un periodo di apprendimento iniziale di 30 giorni, durante i quali le nuove posizioni non vengono contrassegnate come posizioni non note. Il sistema ignora anche gli accessi da dispositivi noti e le posizioni geograficamente vicine a una posizione nota. 

Identity Protection individua gli accessi da posizioni non note anche per i protocolli di autenticazione/legacy di base. Poiché questi protocolli non hanno le moderne funzionalità comuni, ad esempio l'ID client, non sono presenti dati di telemetria sufficienti a ridurre i falsi positivi. Per ridurre il numero di rilevamenti dei rischi rilevati, è necessario passare all'autenticazione moderna.   

### <a name="sign-ins-from-infected-devices"></a>Accessi da dispositivi infetti

Questo tipo di rilevamento dei rischi identifica gli accessi dai dispositivi infetti da malware, noti per comunicare attivamente con un server bot. Viene determinato mettendo in relazione gli indirizzi IP del dispositivo dell'utente con gli indirizzi IP che sono stati in contatto con un server bot. 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Accessi da indirizzi IP con attività sospette
Questo tipo di rilevamento del rischio identifica gli indirizzi IP da cui è stato rilevato un numero elevato di tentativi di accesso non riusciti tra più account utente in un breve periodo di tempo. Questo corrisponde ai modelli di traffico degli indirizzi IP usati da utenti malintenzionati e indica che gli account sono già compromessi o stanno per esserlo. Questo algoritmo di apprendimento automatico ignora i falsi positivi evidenti, ad esempio gli indirizzi IP che vengono regolarmente usati da altri utenti nell'organizzazione.  Il sistema ha un periodo di apprendimento iniziale di 14 giorni, durante i quali apprende il comportamento di accesso di un nuovo utente e di un nuovo tenant.

## <a name="detection-type"></a>Tipo di rilevamento

La proprietà del tipo di rilevamento è un indicatore (in**tempo reale** o **offline**) per l'intervallo di tempo di rilevamento di un rilevamento dei rischi. Attualmente, la maggior parte dei rilevamenti dei rischi viene rilevata offline in un'operazione di post-elaborazione dopo che si è verificato il rischio.

La tabella seguente elenca il periodo di tempo necessario per visualizzare un tipo di rilevamento in un report correlato:

| Tipo di rilevamento | Latenza creazione di report |
| --- | --- |
| Tempo reale | Da 5 a 10 minuti |
| Offline | Da 2 a 4 ore |


Per i tipi di rilevamento dei rischi Azure Active Directory rileva, i tipi di rilevamento sono:

| Tipo di rilevamento del rischio | Tipo di rilevamento |
| :-- | --- | 
| [Utenti con credenziali perse](#leaked-credentials) | Offline |
| [Accessi da indirizzi IP anonimi](#sign-ins-from-anonymous-ip-addresses) | Tempo reale |
| [Trasferimento impossibile a posizioni atipiche](#impossible-travel-to-atypical-locations) | Offline |
| [Accessi da posizioni non note](#sign-in-from-unfamiliar-locations) | Tempo reale |
| [Accessi da dispositivi infetti](#sign-ins-from-infected-devices) | Offline |
| [Accessi da indirizzi IP con attività sospette](#sign-ins-from-ip-addresses-with-suspicious-activity) | Offline|


## <a name="risk-level"></a>Livello di rischio

La proprietà livello di rischio di un rilevamento dei rischi è un indicatore (**alto**, **medio**o **basso**) per la gravità e la confidenza di un rilevamento dei rischi. Questa proprietà consente di classificare in ordine di priorità le azioni da eseguire. 

La gravità del rilevamento dei rischi rappresenta il livello di attendibilità del segnale come un predittore della compromissione dell'identità. Il livello di affidabilità è un indicatore della possibile presenza di falsi positivi. 

Ad esempio, 

* **Alta**: Sicurezza elevata e rilevamento del rischio di gravità elevata. Questi eventi sono fortemente indicativi di una compromissione dell'identità dell'utente e tutti gli account utente interessati devono essere corretti.

* **Medium**: Gravità elevata, ma rilevamento del rischio di confidenza inferiore o viceversa. Questi eventi sono potenzialmente rischiosi e tutti gli account utente interessati devono essere corretti.

* **Bassa**: Rilevamento del rischio basso e a bassa sicurezza. Questo evento potrebbe non richiedere un'azione immediata, ma se combinato con altri rilevamenti dei rischi, può fornire un'indicazione sicura che l'identità sia compromessa.

![Livello di rischio](./media/concept-risk-events/01.png)

### <a name="leaked-credentials"></a>Credenziali perse

Il rilevamento dei rischi per le credenziali perse è classificato come **elevato**, perché fornisce un'indicazione chiara che il nome utente e la password sono disponibili per un utente malintenzionato.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Accessi da indirizzi IP anonimi

Il livello di rischio per questo tipo di rilevamento dei rischi è **medio** , perché un indirizzo IP anonimo non è un'indicazione sicura di una compromissione dell'account. È consigliabile contattare immediatamente l'utente per verificare se sta usando indirizzi IP anonimi.


### <a name="impossible-travel-to-atypical-locations"></a>Trasferimento impossibile a posizioni atipiche

Il trasferimento impossibile indica in genere che un pirata informatico è riuscito a ottenere l'accesso. Possono, tuttavia, verificarsi falsi positivi quando un utente è in viaggio e usa un nuovo dispositivo o una VPN che non viene in genere usata da altri utenti nell'organizzazione. Un'altra origine di falsi positivi è costituita dalle applicazioni che passano in modo errato gli indirizzi IP di server come indirizzi IP di client. Questo può dare l'impressione che gli accessi si verifichino dal data center in cui è ospitato il back-end dell'applicazione. Spesso si tratta di data center Microsoft e gli accessi possono risultare eseguiti da indirizzi IP di proprietà di Microsoft. In seguito a questi falsi positivi, il livello di rischio per questo rilevamento dei rischi è **medio**.

> [!TIP]
> È possibile ridurre la quantità di falsi positivi segnalati per questo tipo di rilevamento del rischio configurando le [località](../active-directory-named-locations.md)denominate. 

### <a name="sign-in-from-unfamiliar-locations"></a>Accessi da posizioni non note

Le posizioni non note possono indicare che un utente malintenzionato ha la possibilità di usare un'identità rubata. Possono verificarsi falsi positivi quando un utente è in viaggio, sta provando un nuovo dispositivo o usando una nuova VPN. A causa di questi falsi positivi, il livello di rischio per questo tipo di evento è **Medio**.

### <a name="sign-ins-from-infected-devices"></a>Accessi da dispositivi infetti

Questo rilevamento dei rischi identifica gli indirizzi IP, non i dispositivi utente. Se più dispositivi sono protetti da un unico indirizzo IP e solo alcuni sono controllati da una rete bot, gli accessi provenienti da altri dispositivi attivano questo evento inutilmente, motivo per cui questo rilevamento dei rischi viene classificato come **basso**.  

È consigliabile contattare l'utente e di eseguire la scansione di tutti i relativi dispositivi. È anche possibile che un dispositivo personale di un utente sia stato infettato o che qualcun altro stia usando un dispositivo infetto dallo stesso indirizzo IP dell'utente. I dispositivi infetti sono spesso colpiti da malware non ancora identificati dai software antivirus e possono anche essere indicativi di abitudini errate dell'utente.

Per altre informazioni su come risolvere problemi correlati alle infezioni malware, vedere [Malware Protection Center](https://www.microsoft.com/en-us/security/portal/definitions/adl.aspx/).

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Accessi da indirizzi IP con attività sospette

È consigliabile contattare l'utente per verificare se ha effettivamente eseguito l'accesso da un indirizzo IP contrassegnato come sospetto. Il livello di rischio per questo tipo di evento è "**medio**" perché lo stesso indirizzo IP può essere usato da più dispositivi, mentre solo alcuni di essi potrebbero essere responsabili dell'attività sospetta. 


## <a name="next-steps"></a>Fasi successive

* [Report di sicurezza Utenti contrassegnati per il rischio](concept-user-at-risk.md)
* [Report degli accessi a rischio](concept-risky-sign-ins.md)
* [Azure AD Identity Protection](../active-directory-identityprotection.md).
