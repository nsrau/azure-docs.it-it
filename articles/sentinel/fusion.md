---
title: Rilevamento avanzato degli attacchi multistadio in Azure SentinelAdvanced multistage attack detection in Azure Sentinel
description: Usare la tecnologia Fusion in Azure Sentinel per ridurre l'affaticamento degli avvisi e creare eventi imprevisti utilizzabili basati sul rilevamento avanzato degli attacchi multifase.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: yelevin
ms.openlocfilehash: 87ca322cbdfdd8a53a3ecefcb120a961ea1bb936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587924"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Rilevamento avanzato degli attacchi multistadio in Azure SentinelAdvanced multistage attack detection in Azure Sentinel


> [!IMPORTANT]
> Alcune funzionalità di Fusion in Azure Sentinel sono attualmente in anteprima pubblica.
> Queste funzionalità vengono fornite senza un contratto di servizio e non sono consigliate per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Utilizzando la tecnologia Fusion basata sull'apprendimento automatico, Azure Sentinel è in grado di rilevare automaticamente gli attacchi multifase combinando comportamenti anomali e attività sospette che vengono osservati in varie fasi della catena di uccisioni. Azure Sentinel genera quindi eventi imprevisti che altrimenti sarebbero molto difficili da rilevare. Questi eventi imprevisti incapsulano due o più avvisi o attività. Per impostazione generale, questi incidenti sono a basso volume, alta fedeltà e gravità elevata.

Personalizzato per il tuo ambiente, questo rilevamento non solo riduce i tassi di falsi positivi, ma può anche rilevare attacchi con informazioni limitate o mancanti.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Configurazione per il rilevamento di attacchi multifase avanzati

Questo rilevamento è abilitato per impostazione predefinita in Azure Sentinel.This detection is enabled by default in Azure Sentinel. Per controllare lo stato o per disabilitarlo forse perché si utilizza una soluzione alternativa per creare eventi imprevisti basati su più avvisi, utilizzare le istruzioni seguenti:To check the status, or to disable it perhaps because you are using an alternative solution to create incidents based on multiple alerts, use the following instructions:

1. Se non è già stato fatto, accedere al portale di [Azure.](https://portal.azure.com)

2. Passare ad**Analisi** della**configurazione** >  **di Sentinel di** > Azure

3. Selezionare **Regole attive** e individuare **Rilevamento avanzato attacchi multifase** nella colonna **NOME.** Controllare la colonna **STATUS** per verificare se il rilevamento è abilitato o disabilitato.

4. Per modificare lo stato, selezionare questa voce e nel pannello **Rilevamento avanzato attacchi multifase** selezionare **Modifica**.

5. Nel pannello **Creazione guidata regola** la modifica dello stato viene selezionata automaticamente, quindi selezionare **Avanti: Revisione**e quindi **Salva**. 

I modelli di regola non sono applicabili per il rilevamento avanzato degli attacchi in più fasi.

> [!NOTE]
> Azure Sentinel usa attualmente 30 giorni di dati cronologici per eseguire il training dei sistemi di apprendimento automatico. Questi dati vengono sempre crittografati usando le chiavi di Microsoft mentre passano attraverso la pipeline di Machine Learning.This data is always encrypted using Microsoft's keys as it passes through the machine learning pipeline. Tuttavia, i dati di training non vengono crittografati usando [le chiavi gestite dal cliente (CMK)](customer-managed-keys.md) se CMK è stato abilitato nell'area di lavoro di Azure Sentinel.However, the training data is not encrypted using Customer Managed Keys (CMK) if you enabled CMK in your Azure Sentinel workspace. Per rifiutare esplicitamente Fusion, passare a **Azure Sentinel** \>Configuration **Analytics Regole \> \> avanzate Rilevamento degli attacchi multifase avanzate** e nella colonna Stato selezionare Disabilita.To opt out of Fusion, navigate to Azure Sentinel **Configuration** \>Analytics Active rules Advanced Multistage Attack Detection and in the **Status** column, select **Disable.**

## <a name="fusion-using-palo-alto-networks-and-microsoft-defender-atp"></a>Fusion utilizzando Palo Alto Networks e Microsoft Defender ATP

Questi scenari combinano due dei registri fondamentali utilizzati dagli analisti della protezione: i registri di Firewall da Palo Alto Networks e i log di rilevamento dei punti finali di Microsoft Defender ATP. In tutti gli scenari elencati di seguito, viene rilevata un'attività sospetta nel punto finale che coinvolge un indirizzo IP esterno, quindi, questo è seguito da traffico anomalo dall'indirizzo IP esterno nel firewall. Nei log di Palo Alto, Azure Sentinel si concentra sui log delle [minacce](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)e il traffico è considerato sospetto quando sono consentite minacce (dati sospetti, file, inondazioni, pacchetti, scansioni, spyware, URL, virus, vulnerabilità, virus incendi, incendi.

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Richiesta di rete al servizio di anonimato TOR seguita da traffico anomalo contrassegnato dal firewall Palo Alto Networks.

In questo scenario, Azure Sentinel rileva innanzitutto un avviso che Microsoft Defender Advanced Threat Protection ha rilevato una richiesta di rete a un servizio di anonimato TOR che porta all'attività di tipo anomalo. Questo è stato avviato con l'account "nome account" con ID SID (Sid) in corrispondenza dell'ora. L'indirizzo IP in uscita per la connessione era "IndividualIp".
Quindi, è stata rilevata un'attività insolita da Palo Alto Networks Firewall in TimeGenerated . Ciò indica che il traffico dannoso è entrato nella rete L'indirizzo IP di destinazione per il traffico di rete è "DestinationIP".

Questo scenario è attualmente in anteprima pubblica.


### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>PowerShell ha effettuato una connessione di rete sospetta seguita da traffico anomalo contrassegnato dal firewall di Palo Alto Networks.

In questo scenario, Azure Sentinel rileva innanzitutto un avviso che Microsoft Defender Advanced Threat Protection ha rilevato che PowerShell ha effettuato una connessione di rete sospetta che causa un'attività anomala rilevata da un Palo Alto Network Firewall. Questo è stato avviato dall'account "nome account" con ID SID (Sid) in corrispondenza dell'ora. L'indirizzo IP in uscita per la connessione era "IndividualIp". Quindi, è stata rilevata un'attività insolita da Palo Alto Networks Firewall in TimeGenerated . Ciò indica che il traffico dannoso è entrato nella rete. L'indirizzo IP di destinazione per il traffico di rete è "DestinationIP".

Questo scenario è attualmente in anteprima pubblica.

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Connessione in uscita a IP con una cronologia dei tentativi di accesso non autorizzati seguita da traffico anomalo contrassegnato dal firewall Palo Alto Networks

In questo scenario, Azure Sentinel rileva un avviso che Microsoft Defender Advanced Threat Protection ha rilevato una connessione in uscita a un indirizzo IP con una cronologia dei tentativi di accesso non autorizzati che portano al rilevamento di attività anomale da Palo Alto Firewall reti. Questo è stato avviato dall'account "nome account" con ID SID (Sid) in corrispondenza dell'ora. L'indirizzo IP in uscita per la connessione era "IndividualIp". Dopo questo, l'attività insolita è stata rilevata dal Palo Alto Networks Firewall in . Ciò indica che il traffico dannoso è entrato nella rete. L'indirizzo IP di destinazione per il traffico di rete è "DestinationIP".

Questo scenario è attualmente in anteprima pubblica.



## <a name="fusion-using-identity-protection-and-microsoft-cloud-app-security"></a>Fusion e Protezione identità e Microsoft Cloud App Security

Utilizzando il rilevamento avanzato degli attacchi multifase, Azure Sentinel supporta gli scenari seguenti che combinano gli eventi anomali di Azure Active Directory Identity Protection e Microsoft Cloud App Security:

- [Impossibile viaggiare in posizione atipica seguita da attività anomala di Office 365](#impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity)
- [Attività di accesso per una posizione sconosciuta seguita da attività di Office 365 anomala](#sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity)
- [Attività di accesso da dispositivo infetto seguita da attività di Office 365 anomala](#sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity)
- [Attività di accesso dall'indirizzo IP anonimo seguito da un'attività di Office 365 anomala](#sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity)
- [Attività di accesso da parte dell'utente con credenziali trapelate seguite da attività di Office 365 anomala](#sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity)

È necessario disporre del [connettore dati](connect-azure-ad-identity-protection.md) di Azure AD Identity Protection e dei connettori [di Cloud App Security](connect-cloud-app-security.md) configurati.

Nelle descrizioni seguenti, Azure Sentinel visualizzerà il valore effettivo dai dati rappresentati in questa pagina come variabili tra parentesi. Ad esempio, il nome visualizzato effettivo \<di un account anziché \<il nome *dell'account*> e il numero effettivo anziché il *numero*>.

### <a name="impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity"></a>Impossibile viaggiare in posizione atipica seguita da attività anomala di Office 365

Esistono sette possibili incidenti di Azure Sentinel che combinano traslazioni impossibili agli avvisi di posizione atipici da Azure AD Identity Protection e avvisi anomali di Office 365 generati da Microsoft Cloud App Security:

- **Impossibile viaggiare in luoghi atipici che portano all'esfiltrazione delle cassette postali di Office 365**
    
    Questo avviso è un'indicazione di \<un evento di accesso \< *location* in base al nome *dell'account*> da un> di un percorso impossibile, una posizione atipica, seguita da una regola di inoltro della posta in arrivo sospetta è stata impostata nella posta in arrivo di un utente.
    
    Ciò potrebbe indicare che l'account è compromesso e che la cassetta postale viene utilizzata per esfiltrare informazioni dall'organizzazione. Il \< *nome dell'account* utente> creato o aggiornato una regola di \<inoltro della posta in arrivo che inoltra tutti i messaggi di posta elettronica in arrivo all'indirizzo di *posta elettronica* esterno>.

- **Impossibile viaggiare in luoghi atipici che portano ad attività amministrative sospette di app cloud**
    
    Questo avviso è un'indicazione di \<un evento di accesso \< *location* in base al *nome dell'account*> da un> di viaggio impossibile, una località atipica.
    
    Successivamente, il \<nome dell'account account> eseguito su *account name* \<un *numero*> attività amministrative in una singola sessione.

- **Impossibile viaggiare in luoghi atipici che portano alla cancellazione di massa di file**
    
    Questo avviso è un'indicazione di \<un evento \<di accesso in base al nome *dell'account*> alla *posizione*>, una posizione atipica. 
    
    Successivamente, il \<nome dell'account> eliminato *account name* \<il numero *di* file> univoci in una singola sessione.

- **Impossibile viaggiare in luoghi atipici che portano al download di massa di file**
    
    Questo avviso è un'indicazione di \<un evento di accesso \< *location* in base al *nome dell'account*> da un> di viaggio impossibile, una località atipica. 
    
    Successivamente, il \<nome dell'account> scaricato oltre *account name* \<il numero *di*> file univoci in una singola sessione.

- **Impossibile viaggiare in luoghi atipici che portano alla rappresentazione di Office 365**
    
    Questo avviso è un'indicazione di \<un evento di accesso \< *location* in base al *nome dell'account*> da un> di viaggio impossibile, una località atipica. 
    
    Successivamente, il \<nome dell'account> eseguito un importo insolito *account name* \<*(numero di attività*>) di attività di rappresentazione in una singola sessione.

- **Impossibile viaggiare in luoghi atipici che portano alla condivisione di massa di file**
    
    Questo avviso è un'indicazione di \<un evento di accesso \< *location* in base al *nome dell'account*> da un> di viaggio impossibile, una località atipica. 
    
    Successivamente, il \<nome dell'account> condiviso sul *account name* \<numero *di*> file univoci in una singola sessione.

- **Impossibile viaggiare in luoghi atipici che portano al ransomware nell'app cloud**
    
    Questo avviso è un'indicazione di \<un evento di accesso \< *location* in base al *nome dell'account*> da un> di viaggio impossibile, una località atipica. 
    
    Successivamente, il \<nome dell'account> il *numero di* \<file di> caricato *account name* \<ed eliminato un numero totale di file *di*>. 
    
    Questo modello di attività è indicativo di un potenziale attacco ransomware.


### <a name="sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity"></a>Attività di accesso per una posizione sconosciuta seguita da attività di Office 365 anomala

Esistono sette possibili eventi imprevisti di Azure Sentinel che combinano l'attività di accesso per gli avvisi di posizione sconosciuti da Azure AD Identity Protection e gli avvisi anomali di Office 365 generati da Microsoft Cloud App Security.There are seven possible Azure Sentinel incidents that combine sign-in activity for un familiar location alerts from Azure AD Identity Protection and anomalo Office 365 alerts generated by Microsoft Cloud App Security.

- **Evento di accesso da una posizione sconosciuta che porta all'esfiltrazione delle cassette postali di Exchange Online**
    
    Questo avviso è un'indicazione di \<un evento \<di accesso in base al nome *dell'account*> dalla *posizione*>, una posizione sconosciuta, seguita da una regola di inoltro della posta in arrivo sospetta è stata impostata nella posta in arrivo di un utente.
    
    Ciò potrebbe indicare che l'account è compromesso e che la cassetta postale viene utilizzata per esfiltrare informazioni dall'organizzazione. Il \< *nome dell'account* utente> creato o aggiornato una regola di \<inoltro della posta in arrivo che inoltra tutti i messaggi di posta elettronica in arrivo all'indirizzo di *posta elettronica* esterno>. 

- **Evento di accesso da una posizione sconosciuta che causa attività amministrative sospette di app cloud**
    
    Questo avviso è un'indicazione di \<un evento \<di accesso in base al *nome dell'account*> dalla *posizione*>, una posizione sconosciuta. 
    
    Successivamente, il \<nome dell'account> eseguito sul *account name* \<numero *di* attività amministrative> in una singola sessione.

- **Evento di accesso da una posizione sconosciuta che porta all'eliminazione di massa dei file**
    
    Questo avviso è un'indicazione di \<un evento \<di accesso in base al *nome dell'account*> dalla *posizione*>, una posizione sconosciuta. 
    
    Successivamente, il \<nome dell'account> eliminato *account name* \<il numero *di* file> univoci in una singola sessione.

- **Evento di accesso da una posizione sconosciuta che porta al download di massa dei file**
    
    Questo avviso è un'indicazione di \<un evento \<di accesso in base al *nome dell'account*> dalla *posizione*>, una posizione sconosciuta. 
    
    Successivamente, il \<nome dell'account> scaricato oltre *account name* \<il numero *di*> file univoci in una singola sessione.

- **Evento di accesso da una posizione sconosciuta che conduce alla rappresentazione di Office 365**
    
    Questo avviso è un'indicazione di \<un evento \<di accesso in base al *nome dell'account*> dalla *posizione*>, una posizione sconosciuta.
    
    Successivamente, il \<nome dell'account> \<rappresentato su *un numero di* account> diversi in una singola sessione. *account name*

- **Evento di accesso da una posizione sconosciuta che porta alla condivisione di massa di file**
    
    Questo avviso è un'indicazione di \<un evento \<di accesso in base al *nome dell'account*> dalla *posizione*>, una posizione sconosciuta. 
    
    Successivamente, il \<nome dell'account> condiviso sul *account name* \<numero *di*> file univoci in una singola sessione.

- **Evento di accesso da una posizione sconosciuta che porta al ransomware nell'app cloud**
    
    Questo avviso è un'indicazione di \<un evento \<di accesso in base al *nome dell'account*> dalla *posizione*>, una posizione sconosciuta. 
    
    Successivamente, il \<nome dell'account> il *numero di* \<file di> caricato *account name* \<ed eliminato un numero totale di file *di*>. 
    
    Questo modello di attività è indicativo di un potenziale attacco ransomware.

### <a name="sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity"></a>Attività di accesso da dispositivo infetto seguita da attività di Office 365 anomala

Esistono sette possibili eventi imprevisti di Azure Sentinel che combinano l'attività di accesso da avvisi di dispositivi infetti da Azure AD Identity Protection e avvisi di Office 365 anomali generati da Microsoft Cloud App Security:There are seven possible Azure Sentinel incidents that combine sign-in activity from infected device alerts from Azure AD Identity Protection and anomalous Office 365 alerts generated by Microsoft Cloud App Security:

- **Evento di accesso da un dispositivo infetto che causa l'esfiltrazione delle cassette postali di Office 365**
    
    Questo avviso è un'indicazione di \<un evento di accesso in base al nome dell'account> da un dispositivo potenzialmente infetto da malware, seguito da una regola di inoltro della posta in arrivo sospetta è stata impostata nella posta in arrivo di un utente. *account name*
    
    Ciò potrebbe indicare che l'account è compromesso e che la cassetta postale viene utilizzata per esfiltrare informazioni dall'organizzazione. Il \< *nome dell'account* utente> creato o aggiornato una regola di \<inoltro della posta in arrivo che inoltra tutti i messaggi di posta elettronica in arrivo all'indirizzo di *posta elettronica* esterno>. 

- **Evento di accesso da un dispositivo infetto che causa attività amministrative sospette di app cloud**
    
    Questo avviso è un'indicazione di \<un evento di accesso in base al *nome dell'account*> da un dispositivo potenzialmente infettato da malware.
    
    Successivamente, il \<nome dell'account> eseguito sul *account name* \<numero *di* attività amministrative> in una singola sessione.

- **Evento di accesso da un dispositivo infetto che causa l'eliminazione di massa dei file**
    
    Questo avviso è un'indicazione di \<un evento di accesso in base al *nome dell'account*> da un dispositivo potenzialmente infettato da malware. 
    
    Successivamente, il \<nome dell'account> eliminato *account name* \<il numero *di* file> univoci in una singola sessione.

- **Evento di accesso da un dispositivo infetto che porta al download di massa di file**
    
    Questo avviso è un'indicazione di \<un evento di accesso in base al *nome dell'account*> da un dispositivo potenzialmente infettato da malware. 
    
    Successivamente, il \<nome dell'account> scaricato oltre *account name* \<il numero *di*> file univoci in una singola sessione.

- **Evento di accesso da un dispositivo infetto che causa la rappresentazione di Office 365**
    
    Questo avviso è un'indicazione di \<un evento di accesso in base al *nome dell'account*> da un dispositivo potenzialmente infettato da malware. 
    
    Successivamente, il \<nome dell'account> \<rappresentato su *un numero di* account> diversi in una singola sessione. *account name*

- **Evento di accesso da un dispositivo infetto che causa la condivisione di file di massa**
    
    Questo avviso è un'indicazione di \<un evento di accesso in base al *nome dell'account*> da un dispositivo potenzialmente infettato da malware. 
    
    Successivamente, il \<nome dell'account> condiviso sul *account name* \<numero *di*> file univoci in una singola sessione.

- **Evento di accesso da un dispositivo infetto che porta al ransomware nell'app cloud**
    
    Questo avviso è un'indicazione di \<un evento di accesso in base al *nome dell'account*> da un dispositivo potenzialmente infettato da malware. 
    
    Successivamente, il \<nome dell'account> il *numero di* \<file di> caricato *account name* \<ed eliminato un numero totale di file *di*>. 
    
    Questo modello di attività è indicativo di un potenziale attacco ransomware.

### <a name="sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity"></a>Attività di accesso dall'indirizzo IP anonimo seguito da un'attività di Office 365 anomala

Esistono sette possibili eventi imprevisti di Azure Sentinel che combinano l'attività di accesso da avvisi di indirizzi IP anonimi da Azure AD Identity Protection e avvisi anomali di Office 365 generati da Microsoft Cloud App Security:There are seven possible Azure Sentinel incidents that combine sign-in activity from anonymous IP address alerts from Azure AD Identity Protection and anomalous Office 365 alerts generated by Microsoft Cloud App Security:

- **Evento di accesso da un indirizzo IP anonimo che porta all'esfiltrazione delle cassette postali di Office 365**
    
    Questo avviso è un'indicazione di \<un evento di accesso \<in base al nome *dell'account*> da un *indirizzo* IP dell'indirizzo IP proxy anonimo>, seguito da una regola di inoltro della posta in arrivo sospetta impostata nella posta in arrivo di un utente.
    
    Ciò potrebbe indicare che l'account è compromesso e che la cassetta postale viene utilizzata per esfiltrare informazioni dall'organizzazione. Il \< *nome dell'account* utente> creato o aggiornato una regola di \<inoltro della posta in arrivo che inoltra tutti i messaggi di posta elettronica in arrivo all'indirizzo di *posta elettronica* esterno>. 

- **Evento di accesso da un indirizzo IP anonimo che causa attività amministrative di app cloud sospette**
    
    Questo avviso è un'indicazione di \<un evento di accesso \<in base al *nome dell'account*> da un indirizzo IP proxy anonimo *>.* 
    
    Successivamente, il \<nome dell'account> eseguito sul *account name* \<numero *di* attività amministrative> in una singola sessione.

- **Evento di accesso da un indirizzo IP anonimo che causa l'eliminazione di massa dei file**
    
    Questo avviso è un'indicazione di \<un evento di accesso \<in base al *nome dell'account*> da un indirizzo IP proxy anonimo *>.* 
    
    Successivamente, il \<nome dell'account> eliminato *account name* \<il numero *di* file> univoci in una singola sessione.

- **Evento di accesso da un indirizzo IP anonimo che porta al download di massa dei file**
    
    Questo avviso è un'indicazione di \<un evento di accesso \<in base al *nome dell'account*> da un indirizzo IP proxy anonimo *>.* 
    
    Successivamente, il \<nome dell'account> scaricato oltre *account name* \<il numero *di*> file univoci in una singola sessione.

- **Evento di accesso da un indirizzo IP anonimo che porta alla rappresentazione di Office 365**
    
    Questo avviso è un'indicazione di \<un evento di accesso \<in base al *nome dell'account*> da un indirizzo IP proxy anonimo *>.* 
    
    Successivamente, il \<nome dell'account> \<rappresentato su *un numero di* account> diversi in una singola sessione. *account name*

- **Evento di accesso da un indirizzo IP anonimo che causa la condivisione di file di massa**
    
    Questo avviso è un'indicazione di \<un evento di accesso \<in base al *nome dell'account*> da un indirizzo IP proxy anonimo *>.* 
    
    Successivamente, il \<nome dell'account> condiviso sul *account name* \<numero *di*> file univoci in una singola sessione.

- **Evento di accesso da un indirizzo IP anonimo a ransomware nell'app cloud**
    
    Questo avviso è un'indicazione di \<un evento di accesso \<in base al *nome dell'account*> da un indirizzo IP proxy anonimo *>.* 
    
    Successivamente, il \<nome dell'account> il *numero di* \<file di> caricato *account name* \<ed eliminato un numero totale di file *di*>. 
    
    Questo modello di attività è indicativo di un potenziale attacco ransomware.

### <a name="sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity"></a>Attività di accesso da parte dell'utente con credenziali trapelate seguite da attività di Office 365 anomala

Esistono sette possibili eventi imprevisti di Azure Sentinel che combinano l'attività di accesso da parte dell'utente con avvisi relativi alle credenziali perse da Azure AD Identity Protection e avvisi anomali di Office 365 generati da Microsoft Cloud App Security:There are seven possible Azure Sentinel incidents that combine sign-in activity from user with leaked credentials alerts from Azure AD Identity Protection and anomalous Office 365 alerts generated by Microsoft Cloud App Security:

- **Evento di accesso da parte dell'utente con credenziali trapelate che portano all'esfiltrazione delle cassette postali di Office 365**
    
    Questo avviso indica che l'evento \<di accesso in base al *nome dell'account*> utilizzate credenziali trapelate, seguite da una regola di inoltro della posta in arrivo sospetta, è stata impostata nella posta in arrivo di un utente. 
    
    Ciò potrebbe indicare che l'account è compromesso e che la cassetta postale viene utilizzata per esfiltrare informazioni dall'organizzazione. Il \< *nome dell'account* utente> creato o aggiornato una regola di \<inoltro della posta in arrivo che inoltra tutti i messaggi di posta elettronica in arrivo all'indirizzo di *posta elettronica* esterno>. 

- **Evento di accesso dall'utente con credenziali trapelate che causano attività amministrative sospette dell'app cloud**
    
    Questo avviso indica che l'evento \<di accesso in base al *nome dell'account*> utilizzate credenziali trapelate.
    
    Successivamente, il \<nome dell'account> eseguito sul *account name* \<numero *di* attività amministrative> in una singola sessione.

- **Evento di accesso dall'utente con credenziali trapelate che portano all'eliminazione di massa dei file**
    
    Questo avviso indica che l'evento \<di accesso in base al *nome dell'account*> utilizzate credenziali trapelate.
    
    Successivamente, il \<nome dell'account> eliminato *account name* \<il numero *di* file> univoci in una singola sessione.

- **Evento di accesso dall'utente con credenziali trapelate che portano al download di massa di file**
    
    Questo avviso indica che l'evento \<di accesso in base al *nome dell'account*> utilizzate credenziali trapelate.
    
    Successivamente, il \<nome dell'account> scaricato oltre *account name* \<il numero *di*> file univoci in una singola sessione.

- **Evento di accesso da parte dell'utente con credenziali trapelate che portano alla rappresentazione di Office 365**
    
    Questo avviso indica che l'evento \<di accesso in base al *nome dell'account*> utilizzate credenziali trapelate. 
    
    Successivamente, il \<nome dell'account> \<rappresentato su *un numero di* account> diversi in una singola sessione. *account name*

- **Evento di accesso dall'utente con credenziali trapelate che portano alla condivisione di massa di file**
    
    Questo avviso indica che l'evento \<di accesso in base al *nome dell'account*> utilizzate credenziali trapelate.
    
    Successivamente, il \<nome dell'account> condiviso sul *account name* \<numero *di*> file univoci in una singola sessione.

- **Evento di accesso dall'utente con credenziali trapelate al ransomware nell'app cloud**
    
    Questo avviso indica che l'evento \<di accesso in base al *nome dell'account*> utilizzate credenziali trapelate. 
    
    Successivamente, il \<nome dell'account> il *numero di* \<file di> caricato *account name* \<ed eliminato un numero totale di file *di*>. 
    
    Questo modello di attività è indicativo di un potenziale attacco ransomware.

## <a name="next-steps"></a>Passaggi successivi

Ora hai imparato di più sul rilevamento avanzato degli attacchi multifase, potresti essere interessato alla guida introduttiva seguente per scoprire come ottenere visibilità sui dati e sulle potenziali minacce: [Introduzione ad Azure Sentinel.](quickstart-get-visibility.md)

Se si è pronti per analizzare gli eventi imprevisti creati automaticamente, vedere l'esercitazione seguente: [Esaminare gli eventi imprevisti con Azure Sentinel](tutorial-investigate-cases.md).

