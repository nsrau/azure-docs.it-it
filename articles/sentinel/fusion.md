---
title: Rilevamento avanzato degli attacchi multifase in Sentinel di Azure
description: Usa la tecnologia Fusion in Sentinel di Azure per ridurre la fatica degli avvisi e creare eventi imprevisti che si basano sul rilevamento di attacchi multifase avanzati.
services: sentinel
documentationcenter: na
author: cabailey
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 9/24/2019
ms.author: cabailey
ms.openlocfilehash: e6ddb1b01b705d2a7857682bd84e9482e064a8db
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240076"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Rilevamento avanzato degli attacchi multifase in Sentinel di Azure

Grazie alla tecnologia Fusion basata sull'apprendimento automatico, Azure Sentinel può rilevare automaticamente gli attacchi multifase combinando comportamenti anomali e attività sospette osservate in varie fasi della kill chain. Azure Sentinel genera quindi eventi imprevisti che altrimenti sarebbero molto difficili da rilevare. Questi eventi imprevisti racchiuderanno due o più avvisi o attività. Per impostazione predefinita, questi eventi imprevisti sono un volume ridotto, una fedeltà elevata e una gravità elevata.

Personalizzato per l'ambiente in uso, questo rilevamento non solo riduce le percentuali di falsi positivi, ma può anche rilevare attacchi con informazioni limitate o mancanti.

Per informazioni dettagliate sugli avvisi per gli scenari supportati, vedere la sezione [scenari supportati per il rilevamento di attacchi multifase](#scenarios-supported-for-advanced-multistage-attack-detection) in questa pagina.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Configurazione per il rilevamento di attacchi multifase avanzati

Questo rilevamento è abilitato per impostazione predefinita in Sentinel di Azure. Per controllare lo stato o per disabilitarlo probabilmente perché si sta usando una soluzione alternativa per creare eventi imprevisti basati su più avvisi, usare le istruzioni seguenti:

1. Accedere al [portale di Azure](https://portal.azure.com), se questa operazione non è già stata eseguita.

2. Passare ad **Azure Sentinel** > **Configuration** > **Analytics**

3. Selezionare **regole attive** e individuare **Rilevamento attacchi multifase avanzati** nella colonna **nome** . Controllare la colonna **stato** per verificare se il rilevamento è abilitato o disabilitato.

4. Per modificare lo stato, selezionare questa voce e nel pannello **Advanced MultiStage Attack Detection** selezionare Edit ( **modifica**).

5. Nel pannello della **creazione guidata regola** , la modifica dello stato viene selezionata automaticamente, quindi selezionare **avanti: Esaminare**e quindi **salvare**. 

I modelli di regole non sono applicabili per il rilevamento degli attacchi multifase avanzati.

## <a name="scenarios-supported-for-advanced-multistage-attack-detection"></a>Scenari supportati per il rilevamento di attacchi multifase avanzati

Grazie al rilevamento avanzato degli attacchi multifase, Azure Sentinel supporta gli scenari seguenti che combinano eventi di anomalia Azure Active Directory Identity Protection e Microsoft Cloud App Security:

- [Trasferimento Impossibile a una posizione atipica seguita da un'attività anomala di Office 365](#impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity)
- [Attività di accesso per una posizione non nota seguita da un'attività anomala di Office 365](#sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity)
- [Attività di accesso da dispositivo infetto seguita da un'attività anomala di Office 365](#sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity)
- [Attività di accesso da un indirizzo IP anonimo seguito da un'attività anomala di Office 365](#sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity)
- [Attività di accesso da un utente con credenziali perse seguite da un'attività anomala di Office 365](#sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity)

È necessario disporre del [connettore Azure ad Identity Protection data](connect-azure-ad-identity-protection.md) e dei connettori di [cloud app Security](connect-cloud-app-security.md) configurati.

Nelle descrizioni seguenti, Azure Sentinel visualizzerà il valore effettivo dei dati rappresentati in questa pagina come variabili tra parentesi quadre. Ad esempio, il nome visualizzato effettivo di un account \<anziché il *nome account*> e il numero \<effettivo anziché il *numero*>.

### <a name="impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity"></a>Trasferimento Impossibile a una posizione atipica seguita da un'attività anomala di Office 365

Ci sono sette possibili eventi imprevisti di Azure Sentinel che combinano la trasmissione Impossibile a avvisi di posizione atipica da Azure AD Identity Protection e gli avvisi anomali di Office 365 generati da Microsoft Cloud App Security:

- **Trasferimento Impossibile a posizioni atipiche che portano alla cassetta postale di Office 365 exfiltration**
    
    Questo avviso indica che un evento di \<accesso in base al nome dell' *account*> da un percorso non possibile \<a un *percorso*>, una posizione atipica, seguita da una regola di invio sospetta della posta in arrivo, è stata impostata nella posta in arrivo di un utente.
    
    Questo può indicare che l'account è compromesso e che la cassetta postale viene utilizzata per sottrarre le informazioni dell'organizzazione. Il nome \<dell' *account*utente > ha creato o aggiornato una regola di invio della posta in arrivo che invia tutti i messaggi di \<posta elettronica in arrivo all' *indirizzo di posta elettronica*indirizzo esterno >.

- **Trasferimento Impossibile a posizioni atipiche che comportano attività amministrative sospette per le app Cloud**
    
    Questo avviso indica che un evento di \<accesso in base al nome dell' *account*> da un percorso non possibile \<a un *percorso*>, una località atipica.
    
    Successivamente, il nome \<dell' *account*dell'account > \<eseguito sopra il *numero*> le attività amministrative in una singola sessione.

- **Trasferimento Impossibile a posizioni atipiche che comportano l'eliminazione di file di massa**
    
    Questo avviso indica che un evento di accesso in \<base al *nome account*> alla \< *località*>, una posizione atipica. 
    
    Successivamente, il nome \<dell' *account*dell'account \<> eliminato il *numero di*file > univoci in una singola sessione.

- **Trasferimento Impossibile a posizioni atipiche che portano al download di file di massa**
    
    Questo avviso indica che un evento di \<accesso in base al nome dell' *account*> da un percorso non possibile \<a un *percorso*>, una località atipica. 
    
    Successivamente, il nome \<dell' *account*dell'account > \<scaricato oltre il *numero di*file > univoci in una singola sessione.

- **Trasferimento Impossibile a posizioni atipiche che comportano la rappresentazione di Office 365**
    
    Questo avviso indica che un evento di \<accesso in base al nome dell' *account*> da un percorso non possibile \<a un *percorso*>, una località atipica. 
    
    Successivamente, il nome \<dell' *account*dell'account > eseguita una quantità\<insolita (*numero di attività*>) di attività di rappresentazione in una singola sessione.

- **Trasferimento Impossibile a posizioni atipiche che portano alla condivisione di file di massa**
    
    Questo avviso indica che un evento di \<accesso in base al nome dell' *account*> da un percorso non possibile \<a un *percorso*>, una località atipica. 
    
    Successivamente, il nome \<dell' *account*dell'account > \<condiviso per il *numero di*file > univoci in una singola sessione.

- **Trasferimento Impossibile a posizioni atipiche che portano a ransomware nell'app Cloud**
    
    Questo avviso indica che un evento di \<accesso in base al nome dell' *account*> da un percorso non possibile \<a un *percorso*>, una località atipica. 
    
    Successivamente, il nome \<dell' *account*dell'account \<> il *numero di*file > caricato ed eliminato un \<totale di un *numero*di file >. 
    
    Questo modello di attività è indicativo di un potenziale attacco ransomware.


### <a name="sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity"></a>Attività di accesso per una posizione non nota seguita da un'attività anomala di Office 365

Esistono sette possibili eventi imprevisti di Azure Sentinel che combinano l'attività di accesso per gli avvisi di posizione non nota da Azure AD Identity Protection e gli avvisi anomali di Office 365 generati da Microsoft Cloud App Security.

- **Evento di accesso da una posizione non nota che conduce alla cassetta postale di Exchange Online exfiltration**
    
    Questo avviso indica un evento di accesso in \<base al *nome account*> dalla \< *località*>, un percorso non noto, seguito da una regola di invio sospetta della posta in arrivo è stata impostata nella posta in arrivo di un utente.
    
    Questo può indicare che l'account è compromesso e che la cassetta postale viene utilizzata per sottrarre le informazioni dell'organizzazione. Il nome \<dell' *account*utente > ha creato o aggiornato una regola di invio della posta in arrivo che invia tutti i messaggi di \<posta elettronica in arrivo all' *indirizzo di posta elettronica*indirizzo esterno >. 

- **Evento di accesso da una posizione non nota che causa un'attività amministrativa sospetta per le app Cloud**
    
    Questo avviso indica un evento di accesso in \<base al *nome account*> dalla \< *località*>, una posizione non nota. 
    
    Successivamente, il nome \<dell' *account*dell'account > \<eseguito su un *numero di*> attività amministrative in una singola sessione.

- **Evento di accesso da una posizione non nota che causa l'eliminazione di file di massa**
    
    Questo avviso indica un evento di accesso in \<base al *nome account*> dalla \< *località*>, una posizione non nota. 
    
    Successivamente, il nome \<dell' *account*dell'account \<> eliminato il *numero di*file > univoci in una singola sessione.

- **Evento di accesso da una posizione non nota che conduce al download di file di massa**
    
    Questo avviso indica un evento di accesso in \<base al *nome account*> dalla \< *località*>, una posizione non nota. 
    
    Successivamente, il nome \<dell' *account*dell'account > \<scaricato oltre il *numero di*file > univoci in una singola sessione.

- **Evento di accesso da una posizione non nota che conduce alla rappresentazione di Office 365**
    
    Questo avviso indica un evento di accesso in \<base al *nome account*> dalla \< *località*>, una posizione non nota.
    
    Successivamente, il nome \<dell' *account*dell'account > rappresentato \<per il *numero di*> account diversi in una singola sessione.

- **Evento di accesso da una posizione non nota che conduce alla condivisione di file di massa**
    
    Questo avviso indica un evento di accesso in \<base al *nome account*> dalla \< *località*>, una posizione non nota. 
    
    Successivamente, il nome \<dell' *account*dell'account > \<condiviso per il *numero di*file > univoci in una singola sessione.

- **Evento di accesso da una posizione non nota che conduce a ransomware nell'app Cloud**
    
    Questo avviso indica un evento di accesso in \<base al *nome account*> dalla \< *località*>, una posizione non nota. 
    
    Successivamente, il nome \<dell' *account*dell'account \<> il *numero di*file > caricato ed eliminato un \<totale di un *numero*di file >. 
    
    Questo modello di attività è indicativo di un potenziale attacco ransomware.

### <a name="sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity"></a>Attività di accesso da dispositivo infetto seguita da un'attività anomala di Office 365

Esistono sette possibili eventi imprevisti di Azure Sentinel che combinano l'attività di accesso dagli avvisi di dispositivi infetti da Azure AD Identity Protection e da avvisi anomali di Office 365 generati da Microsoft Cloud App Security:

- **Evento di accesso da un dispositivo infetto che conduce alla cassetta postale di Office 365 exfiltration**
    
    Questo avviso indica un evento di accesso in base al nome di \< *account*> da un dispositivo potenzialmente infetto da malware, seguito da una regola di invio di posta in arrivo sospetta impostata nella posta in arrivo di un utente.
    
    Questo può indicare che l'account è compromesso e che la cassetta postale viene utilizzata per sottrarre le informazioni dell'organizzazione. Il nome \<dell' *account*utente > ha creato o aggiornato una regola di invio della posta in arrivo che invia tutti i messaggi di \<posta elettronica in arrivo all' *indirizzo di posta elettronica*indirizzo esterno >. 

- **Evento di accesso da un dispositivo infetto che causa attività amministrative sospette per le app Cloud**
    
    Questo avviso indica un evento di accesso in base \<al *nome account*> da un dispositivo potenzialmente infetto da malware.
    
    Successivamente, il nome \<dell' *account*dell'account > \<eseguito su un *numero di*> attività amministrative in una singola sessione.

- **Evento di accesso da un dispositivo infetto che causa l'eliminazione di file di massa**
    
    Questo avviso indica un evento di accesso in base \<al *nome account*> da un dispositivo potenzialmente infetto da malware. 
    
    Successivamente, il nome \<dell' *account*dell'account \<> eliminato il *numero di*file > univoci in una singola sessione.

- **Evento di accesso da un dispositivo infetto che conduce al download di file di massa**
    
    Questo avviso indica un evento di accesso in base \<al *nome account*> da un dispositivo potenzialmente infetto da malware. 
    
    Successivamente, il nome \<dell' *account*dell'account > \<scaricato oltre il *numero di*file > univoci in una singola sessione.

- **Evento di accesso da un dispositivo infetto che conduce alla rappresentazione di Office 365**
    
    Questo avviso indica un evento di accesso in base \<al *nome account*> da un dispositivo potenzialmente infetto da malware. 
    
    Successivamente, il nome \<dell' *account*dell'account > rappresentato \<per il *numero di*> account diversi in una singola sessione.

- **Evento di accesso da un dispositivo infetto che conduce alla condivisione di file di massa**
    
    Questo avviso indica un evento di accesso in base \<al *nome account*> da un dispositivo potenzialmente infetto da malware. 
    
    Successivamente, il nome \<dell' *account*dell'account > \<condiviso per il *numero di*file > univoci in una singola sessione.

- **Evento di accesso da un dispositivo infetto che conduce a ransomware nell'app Cloud**
    
    Questo avviso indica un evento di accesso in base \<al *nome account*> da un dispositivo potenzialmente infetto da malware. 
    
    Successivamente, il nome \<dell' *account*dell'account \<> il *numero di*file > caricato ed eliminato un \<totale di un *numero*di file >. 
    
    Questo modello di attività è indicativo di un potenziale attacco ransomware.

### <a name="sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity"></a>Attività di accesso da un indirizzo IP anonimo seguito da un'attività anomala di Office 365

Esistono sette possibili eventi imprevisti di Azure Sentinel che combinano l'attività di accesso da avvisi di indirizzi IP anonimi da Azure AD Identity Protection e da avvisi anomali di Office 365 generati da Microsoft Cloud App Security:

- **Evento di accesso da un indirizzo IP anonimo che conduce alla cassetta postale di Office 365 exfiltration**
    
    Questo avviso indica un evento di accesso in \<base al *nome account*> da un indirizzo IP del proxy \< *Anonimo >,* seguito da una regola di inoltro della posta in arrivo sospetta impostata nella posta in arrivo di un utente.
    
    Questo può indicare che l'account è compromesso e che la cassetta postale viene utilizzata per sottrarre le informazioni dell'organizzazione. Il nome \<dell' *account*utente > ha creato o aggiornato una regola di invio della posta in arrivo che invia tutti i messaggi di \<posta elettronica in arrivo all' *indirizzo di posta elettronica*indirizzo esterno >. 

- **Evento di accesso da un indirizzo IP anonimo che causa un'attività amministrativa sospetta dell'app Cloud**
    
    Questo avviso indica un evento di accesso in \<base al *nome account*> da un indirizzo IP del proxy \< *Anonimo >.* 
    
    Successivamente, il nome \<dell' *account*dell'account > \<eseguito su un *numero di*> attività amministrative in una singola sessione.

- **Evento di accesso da un indirizzo IP anonimo che causa l'eliminazione di file di massa**
    
    Questo avviso indica un evento di accesso in \<base al *nome account*> da un indirizzo IP del proxy \< *Anonimo >.* 
    
    Successivamente, il nome \<dell' *account*dell'account \<> eliminato il *numero di*file > univoci in una singola sessione.

- **Evento di accesso da un indirizzo IP anonimo che conduce al download di file di massa**
    
    Questo avviso indica un evento di accesso in \<base al *nome account*> da un indirizzo IP del proxy \< *Anonimo >.* 
    
    Successivamente, il nome \<dell' *account*dell'account > \<scaricato oltre il *numero di*file > univoci in una singola sessione.

- **Evento di accesso da un indirizzo IP anonimo che conduce alla rappresentazione di Office 365**
    
    Questo avviso indica un evento di accesso in \<base al *nome account*> da un indirizzo IP del proxy \< *Anonimo >.* 
    
    Successivamente, il nome \<dell' *account*dell'account > rappresentato \<per il *numero di*> account diversi in una singola sessione.

- **Evento di accesso da un indirizzo IP anonimo che conduce alla condivisione di file di massa**
    
    Questo avviso indica un evento di accesso in \<base al *nome account*> da un indirizzo IP del proxy \< *Anonimo >.* 
    
    Successivamente, il nome \<dell' *account*dell'account > \<condiviso per il *numero di*file > univoci in una singola sessione.

- **Evento di accesso da un indirizzo IP anonimo a ransomware nell'app Cloud**
    
    Questo avviso indica un evento di accesso in \<base al *nome account*> da un indirizzo IP del proxy \< *Anonimo >.* 
    
    Successivamente, il nome \<dell' *account*dell'account \<> il *numero di*file > caricato ed eliminato un \<totale di un *numero*di file >. 
    
    Questo modello di attività è indicativo di un potenziale attacco ransomware.

### <a name="sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity"></a>Attività di accesso da un utente con credenziali perse seguite da un'attività anomala di Office 365

Esistono sette possibili eventi imprevisti di Azure Sentinel che combinano l'attività di accesso dell'utente con avvisi di credenziali perse da Azure AD Identity Protection e avvisi anomali di Office 365 generati da Microsoft Cloud App Security:

- **Evento di accesso dall'utente con credenziali perse che portano alla cassetta postale di Office 365 exfiltration**
    
    Questo avviso indica che l'evento di accesso in base \<al nome dell' *account*> usato le credenziali perse, seguito da una regola di invio della posta in arrivo sospetta impostata nella posta in arrivo di un utente. 
    
    Questo può indicare che l'account è compromesso e che la cassetta postale viene utilizzata per sottrarre le informazioni dell'organizzazione. Il nome \<dell' *account*utente > ha creato o aggiornato una regola di invio della posta in arrivo che invia tutti i messaggi di \<posta elettronica in arrivo all' *indirizzo di posta elettronica*indirizzo esterno >. 

- **Evento di accesso dall'utente con credenziali perse che causano attività amministrative sospette per le app Cloud**
    
    Questo avviso indica che l'evento di accesso in base \<al nome dell' *account*> usato le credenziali perse.
    
    Successivamente, il nome \<dell' *account*dell'account > \<eseguito su un *numero di*> attività amministrative in una singola sessione.

- **Evento di accesso dall'utente con credenziali perse che portano all'eliminazione di file di massa**
    
    Questo avviso indica che l'evento di accesso in base \<al nome dell' *account*> usato le credenziali perse.
    
    Successivamente, il nome \<dell' *account*dell'account \<> eliminato il *numero di*file > univoci in una singola sessione.

- **Evento di accesso dall'utente con credenziali perse che portano al download di file di massa**
    
    Questo avviso indica che l'evento di accesso in base \<al nome dell' *account*> usato le credenziali perse.
    
    Successivamente, il nome \<dell' *account*dell'account > \<scaricato oltre il *numero di*file > univoci in una singola sessione.

- **Evento di accesso dall'utente con credenziali perse che portano alla rappresentazione di Office 365**
    
    Questo avviso indica che l'evento di accesso in base \<al nome dell' *account*> usato le credenziali perse. 
    
    Successivamente, il nome \<dell' *account*dell'account > rappresentato \<per il *numero di*> account diversi in una singola sessione.

- **Evento di accesso dall'utente con credenziali perse che portano alla condivisione di file di massa**
    
    Questo avviso indica che l'evento di accesso in base \<al nome dell' *account*> usato le credenziali perse.
    
    Successivamente, il nome \<dell' *account*dell'account > \<condiviso per il *numero di*file > univoci in una singola sessione.

- **Evento di accesso dall'utente con credenziali perse al ransomware nell'app Cloud**
    
    Questo avviso indica che l'evento di accesso in base \<al nome dell' *account*> usato le credenziali perse. 
    
    Successivamente, il nome \<dell' *account*dell'account \<> il *numero di*file > caricato ed eliminato un \<totale di un *numero*di file >. 
    
    Questo modello di attività è indicativo di un potenziale attacco ransomware.

## <a name="next-steps"></a>Passaggi successivi

A questo punto si è appreso come ottenere ulteriori informazioni sul rilevamento degli attacchi multifase avanzati. si potrebbe essere interessati alla Guida introduttiva seguente per informazioni su come ottenere la visibilità dei dati e le potenziali minacce: [Inizia a usare Sentinel di Azure](quickstart-get-visibility.md).

Se si è pronti per esaminare gli eventi imprevisti creati automaticamente, vedere l'esercitazione seguente: [Esaminare gli eventi imprevisti con Sentinel di Azure](tutorial-investigate-cases.md).

