---
title: Rilevamento avanzato degli attacchi multifase in Sentinel di Azure
description: Usa la tecnologia Fusion in Sentinel di Azure per ridurre la fatica degli avvisi e creare eventi imprevisti che si basano sul rilevamento di attacchi multifase avanzati.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: ba872f221f3bde29f0bb48b04dc2259d3ab4938a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906278"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Rilevamento avanzato degli attacchi multifase in Sentinel di Azure


> [!IMPORTANT]
> Alcune funzionalità Fusion in Sentinel di Azure sono attualmente disponibili in **anteprima pubblica**.
> Queste funzionalità vengono fornite senza un contratto di servizio e non sono consigliate per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Grazie alla tecnologia Fusion basata sull'apprendimento automatico, Azure Sentinel può rilevare automaticamente gli attacchi multifase identificando combinazioni di comportamenti anomali e attività sospette osservate in varie fasi della kill chain. Alla base di queste individuazioni, Azure Sentinel genera eventi imprevisti che altrimenti sarebbero difficili da rilevare. Questi eventi imprevisti includono due o più avvisi o attività. Per impostazione predefinita, questi eventi imprevisti sono a basso volume, ad alta fedeltà e a gravità elevata.

Personalizzata per l'ambiente in uso, questa tecnologia di rilevamento non solo riduce i tassi falsi positivi, ma può anche rilevare attacchi con informazioni limitate o mancanti.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Configurazione per il rilevamento di attacchi multifase avanzati

Questo rilevamento è abilitato per impostazione predefinita in Sentinel di Azure. Per controllare lo stato o per disabilitarlo nel caso in cui si usi una soluzione alternativa per creare eventi imprevisti basati su più avvisi, usare le istruzioni seguenti:

1. Se non è già stato fatto, accedere al [portale di Azure](https://portal.azure.com).

1. Passare ad **Azure Sentinel**  >  **Configuration**  >  **Analytics**

1. Selezionare **regole attive**, quindi individuare **Rilevamento attacchi multifase avanzati** nella colonna **nome** filtrando l'elenco per il tipo di regola **Fusion** . Controllare la colonna **stato** per verificare se il rilevamento è abilitato o disabilitato.

    :::image type="content" source="./media/fusion/selecting-fusion-rule-type.png" alt-text="{alt-text}":::

1. Per modificare lo stato, selezionare questa voce e nel pannello **Advanced MultiStage Attack Detection** selezionare Edit ( **modifica**).

1. Nel pannello della **creazione guidata regola** , la modifica dello stato viene selezionata automaticamente, quindi selezionare **Avanti: verifica**, quindi **Salva**. 

 Poiché il tipo di regola **Fusion** contiene solo una regola che non può essere modificata, i modelli di regole non sono applicabili per questo tipo di regola.

> [!NOTE]
> Azure Sentinel usa attualmente i dati cronologici per 30 giorni per eseguire il training dei sistemi di machine learning. Questi dati vengono sempre crittografati usando le chiavi di Microsoft mentre passano attraverso la pipeline di machine learning. Tuttavia, i dati di training non vengono crittografati con le [chiavi gestite dal cliente (CMK)](customer-managed-keys.md) se è stato abilitato CMK nell'area di lavoro di Azure Sentinel. Per rifiutare esplicitamente la fusione, passare ad **Azure Sentinel**   \>  **Configuration**   \>  **Analytics \> Active rules \> Advanced MultiStage Attack Detection** e nella colonna **stato** selezionare **Disable (Disabilita).**

## <a name="attack-detection-scenarios"></a>Scenari di rilevamento degli attacchi

La sezione seguente elenca i tipi di scenari di correlazione, raggruppati in base alla classificazione delle minacce, che Azure Sentinel cerca di usare la tecnologia Fusion.

Come indicato in precedenza, poiché Fusion mette in correlazione più avvisi di sicurezza di diversi prodotti per rilevare gli attacchi multifase avanzati, i rilevamenti di fusione riusciti vengono presentati come **eventi imprevisti di Fusion** nella pagina **eventi imprevisti** di Sentinel di Azure e non come **avvisi** nella tabella degli **avvisi di sicurezza** nei **log**.

Per abilitare questi scenari di rilevamento degli attacchi basati su Fusion, è necessario inserire tutte le origini dati elencate usando i connettori dati sentinella di Azure associati.

> [!NOTE]
> Alcuni di questi scenari sono disponibili in **anteprima pubblica**. Saranno così indicate.

## <a name="compute-resource-abuse"></a>Abuso risorse di calcolo

### <a name="multiple-vm-creation-activities-following-suspicious-azure-active-directory-sign-in"></a>Più attività di creazione di macchine virtuali dopo l'accesso Azure Active Directory sospetta
Questo scenario è attualmente disponibile in **anteprima pubblica**.

**Mitre ATT&le tattiche CK:** Accesso iniziale, effetti 

**Mitre ATT&le tecniche CK:** Account valido (T1078), Hijack delle risorse (T1496)

**Origini connettore dati:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Descrizione:** Gli eventi imprevisti di Fusion di questo tipo indicano che un numero anomalo di VM è stato creato in una singola sessione dopo un accesso sospetto a un account Azure AD. Questo tipo di avviso indica, con un livello di confidenza elevato, che l'account indicato nella descrizione dell'evento imprevisto Fusion è stato compromesso e usato per creare nuove macchine virtuali per scopi non autorizzati, ad esempio per l'esecuzione di operazioni di data mining di crittografia. Le permutazioni di avvisi di accesso Azure AD sospette con l'avviso per le attività di creazione di più macchine virtuali sono:

- **Trasferimento Impossibile a una posizione atipica che conduce a più attività di creazione di macchine virtuali**

- **Evento di accesso da una posizione non nota che conduce a più attività di creazione di macchine virtuali**

- **Evento di accesso da un dispositivo infetto che conduce a più attività di creazione di macchine virtuali**

- **Evento di accesso da un indirizzo IP anonimo che conduce a più attività di creazione di macchine virtuali**

- **Evento di accesso dall'utente con credenziali perse che portano a più attività di creazione di macchine virtuali**

## <a name="data-exfiltration"></a>Esfiltrazione di dati

### <a name="office-365-mailbox-exfiltration-following-a-suspicious-azure-ad-sign-in"></a>Exfiltration della cassetta postale di Office 365 dopo un accesso Azure AD sospetto

**Mitre ATT&le tattiche CK:** Accesso iniziale, exfiltration, raccolta

**Mitre ATT&le tecniche CK:** Account valido (T1078), raccolta di messaggi di posta elettronica (T1114), exfiltration automatizzato (T1020)

**Origini connettore dati:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Descrizione:** Gli eventi imprevisti di Fusion di questo tipo indicano che una regola di invio di posta in arrivo sospetta è stata impostata nella posta in arrivo di un utente dopo un accesso sospetto a un account Azure AD. Questa indicazione garantisce un livello di attendibilità elevato rispetto alla compromissione dell'account dell'utente (annotato nella descrizione dell'evento imprevisto Fusion) e che è stato usato per sottrarre i dati dalla rete dell'organizzazione, abilitando una regola di inoltro delle cassette postali senza la conoscenza dell'utente reale. Le permutazioni degli avvisi di accesso Azure AD sospette con l'avviso exfiltration della cassetta postale di Office 365 sono:

- **Trasferimento Impossibile a una località atipica che conduce alla cassetta postale di Office 365 exfiltration**

- **Evento di accesso da una posizione non nota che conduce alla cassetta postale di Office 365 exfiltration**

- **Evento di accesso da un dispositivo infetto che conduce alla cassetta postale di Office 365 exfiltration**

- **Evento di accesso da un indirizzo IP anonimo che conduce alla cassetta postale di Office 365 exfiltration**

- **Evento di accesso dall'utente con credenziali perse che portano alla cassetta postale di Office 365 exfiltration**

### <a name="mass-file-download-following-suspicious-azure-ad-sign-in"></a>Download del file di massa dopo l'accesso Azure AD sospetto

**Mitre ATT&le tattiche CK:** Accesso iniziale, exfiltration

**Mitre ATT&le tecniche CK:** Account valido (T1078)

**Origini connettore dati:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Descrizione:** Gli eventi imprevisti di Fusion di questo tipo indicano che un numero anomalo di file è stato scaricato da un utente dopo un accesso sospetto a un account Azure AD. Questa indicazione garantisce un livello di attendibilità elevato che l'account indicato nella descrizione dell'evento imprevisto Fusion è stato compromesso ed è stato usato per sottrarre i dati dalla rete dell'organizzazione. Le permutazioni di avvisi di accesso Azure AD sospette con l'avviso di download di file di massa sono:  

- **Trasferimento Impossibile a una località atipica che conduce al download di file di massa**

- **Evento di accesso da una posizione non nota che conduce al download di file di massa**

- **Evento di accesso da un dispositivo infetto che conduce al download di file di massa**

- **Evento di accesso da un indirizzo IP anonimo che conduce al download di file di massa**

- **Evento di accesso dall'utente con credenziali perse che portano al download di file di massa**

### <a name="mass-file-sharing-following-suspicious-azure-ad-sign-in"></a>Condivisione di file di massa dopo l'accesso Azure AD sospetto

**Mitre ATT&le tattiche CK:** Accesso iniziale, exfiltration

**Mitre ATT&le tecniche CK:** Account valido (T1078), exfiltration over Web Service (T1567)

**Origini connettore dati:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Descrizione:** Gli eventi imprevisti di Fusion di questo tipo indicano che un numero di file superiore a una determinata soglia è stato condiviso con altri utenti dopo un accesso sospetto a un account Azure AD. Questa indicazione garantisce un livello di attendibilità elevato che l'account indicato nella descrizione dell'evento imprevisto Fusion è stato compromesso e usato per sottrarre i dati dalla rete dell'organizzazione condividendo file quali documenti, fogli di calcolo e così via, con utenti non autorizzati per scopi dannosi. Le permutazioni di avvisi di accesso Azure AD sospette con l'avviso di condivisione file di massa sono:  

- **Trasferimento Impossibile a una posizione atipica che conduce alla condivisione di file di massa**

- **Evento di accesso da una posizione non nota che conduce alla condivisione di file di massa**

- **Evento di accesso da un dispositivo infetto che conduce alla condivisione di file di massa**

- **Evento di accesso da un indirizzo IP anonimo che conduce alla condivisione di file di massa**

- **Evento di accesso dall'utente con credenziali perse che portano alla condivisione di file di massa**

### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>Regole di manipolazione della posta in arrivo sospette impostate dopo l'accesso Azure AD sospetto
Questo scenario appartiene a due classificazioni delle minacce in questo elenco: **exfiltration dati** e **spostamento laterale**. Per maggiore chiarezza, viene visualizzato in entrambe le sezioni.

Questo scenario è attualmente disponibile in **anteprima pubblica**.

**Mitre ATT&le tattiche CK:** Accesso iniziale, spostamento laterale, exfiltration

**Mitre ATT&le tecniche CK:** Account valido (T1078), Internal Spear phishing (T1534)

**Origini connettore dati:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Descrizione:** Gli eventi imprevisti di Fusion di questo tipo indicano che le regole della posta in arrivo anomale sono state impostate nella posta in arrivo di un utente dopo un accesso sospetto a un account Azure AD. In questo modo si indica che l'account indicato nella descrizione dell'evento imprevisto Fusion è stato compromesso ed è stato usato per manipolare le regole della posta in arrivo della posta elettronica dell'utente per scopi dannosi. Questo potrebbe essere un tentativo da parte di un utente malintenzionato di sottrarre i dati dalla rete dell'organizzazione. In alternativa, l'utente malintenzionato potrebbe provare a generare messaggi di posta elettronica di phishing dall'interno dell'organizzazione (ignorando i meccanismi di rilevamento del phishing destinati alla posta elettronica da origini esterne) allo scopo di spostarsi in un secondo momento, ottenendo l'accesso ad account utente e/o con privilegi aggiuntivi. Le permutazioni di avvisi di accesso Azure AD sospette con l'avviso relativo alle regole di manipolazione della posta in arrivo sospette sono:  

- **Trasferimento Impossibile a una posizione atipica che causa una regola di manipolazione della posta in arrivo sospetta**

- **Evento di accesso da una posizione non nota che conduce a una regola di manipolazione della posta in arrivo sospetta**

- **Evento di accesso da un dispositivo infetto che conduce a una regola di manipolazione della posta in arrivo sospetta**

- **Evento di accesso da un indirizzo IP anonimo che conduce a una regola di manipolazione della posta in arrivo sospetta**

- **Evento di accesso dall'utente con credenziali perse che portano a una regola di manipolazione della posta in arrivo sospetta**

### <a name="multiple-power-bi-report-sharing-activities-following-suspicious-azure-ad-sign-in"></a>Più Power BI le attività di condivisione di report dopo l'accesso Azure AD sospetto 
Questo scenario è attualmente disponibile in **anteprima pubblica**.

**Mitre ATT&le tattiche CK:** Accesso iniziale, exfiltration 

**Mitre ATT&le tecniche CK:** Account valido (T1078), exfiltration over Web Service (T1567)

**Origini connettore dati:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Descrizione:** Gli eventi imprevisti Fusion di questo tipo indicano che un numero anomalo di Power BI report è stato condiviso in una singola sessione dopo un accesso sospetto a un account di Azure AD. Questa indicazione garantisce un livello di attendibilità elevato che l'account indicato nella descrizione dell'evento imprevisto Fusion è stato compromesso ed è stato usato per sottrarre i dati dalla rete dell'organizzazione condividendo Power BI report con utenti non autorizzati per scopi dannosi. Le permutazioni di avvisi di accesso Azure AD sospette con le attività di condivisione dei report multiple Power BI sono:  

- **Trasferimento Impossibile a una posizione atipica che conduce a più attività di condivisione del report Power BI**

- **Evento di accesso da una posizione non nota che conduce a più attività di condivisione dei report Power BI**

- **Evento di accesso da un dispositivo infetto che conduce a più attività di condivisione dei report Power BI**

- **Evento di accesso da un indirizzo IP anonimo che conduce a più attività di condivisione dei report Power BI**

- **Evento di accesso dall'utente con credenziali perse che portano a più attività di condivisione dei report Power BI**

### <a name="suspicious-power-bi-report-sharing-following-suspicious-azure-ad-sign-in"></a>Segnalazione di Power BI sospette condivisione degli Azure AD sospetti seguenti
Questo scenario è attualmente disponibile in **anteprima pubblica**.

**Mitre ATT&le tattiche CK:** Accesso iniziale, exfiltration 

**Mitre ATT&le tecniche CK:** Account valido (T1078), exfiltration over Web Service (T1567)

**Origini connettore dati:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Descrizione:** Gli eventi imprevisti Fusion di questo tipo indicano che si è verificata un'attività sospetta di condivisione di report Power BI dopo un accesso sospetto a un account di Azure AD. L'attività di condivisione è stata identificata come sospetta perché il report Power BI conteneva informazioni riservate identificate mediante l'elaborazione del linguaggio naturale e perché era condiviso con un indirizzo di posta elettronica esterno, pubblicato sul Web o consegnato come snapshot a un indirizzo di posta elettronica sottoscritto dall'esterno. Questo avviso indica con la massima sicurezza che l'account indicato nella descrizione dell'evento imprevisto Fusion è stato compromesso ed è stato usato per sottrarre i dati sensibili dell'organizzazione condividendo Power BI report con utenti non autorizzati per scopi dannosi. Le permutazioni di avvisi di accesso Azure AD sospette con la condivisione sospetta di Power BI report sono:  

- **Trasferimento Impossibile a una posizione atipica che causa la condivisione sospetta di Power BI report**

- **Evento di accesso da una posizione non nota che causa la condivisione sospetta dei report Power BI**

- **Evento di accesso da un dispositivo infetto che genera sospetti Power BI condivisione di report**

- **Evento di accesso da un indirizzo IP anonimo che conduce a sospetti Power BI la condivisione di report**

- **Evento di accesso dall'utente con credenziali perse che causano sospetti Power BI condivisione dei report**

## <a name="data-destruction"></a>Distruzione dei dati

### <a name="mass-file-deletion-following-suspicious-azure-ad-sign-in"></a>Eliminazione di file di massa dopo l'accesso Azure AD sospetto

**Mitre ATT&le tattiche CK:** Accesso iniziale, effetti

**Mitre ATT&le tecniche CK:** Account valido (T1078), distruzione dei dati (T1485)

**Origini connettore dati:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Descrizione:** Gli eventi imprevisti Fusion di questo tipo indicano che un numero anomalo di file univoci è stato eliminato dopo un accesso sospetto a un account Azure AD. Ciò indica che è possibile che l'account indicato nella descrizione di Fusion Incident sia stato compromesso e che sia stato usato per eliminare i dati per scopi dannosi. Le permutazioni di avvisi di accesso Azure AD sospette con l'avviso di eliminazione di file di massa sono:  

- **Trasferimento Impossibile a una posizione atipica che causa l'eliminazione di file di massa**

- **Evento di accesso da una posizione non nota che causa l'eliminazione di file di massa**

- **Evento di accesso da un dispositivo infetto che causa l'eliminazione di file di massa**

- **Evento di accesso da un indirizzo IP anonimo che causa l'eliminazione di file di massa**

- **Evento di accesso dall'utente con credenziali perse che portano all'eliminazione di file di massa**

### <a name="suspicious-email-deletion-activity-following-suspicious-azure-ad-sign-in"></a>Attività di eliminazione di posta elettronica sospetta dopo l'accesso Azure AD sospetta
Questo scenario è attualmente disponibile in **anteprima pubblica**.

**Mitre ATT&le tattiche CK:** Accesso iniziale, effetti 

**Mitre ATT&le tecniche CK:** Account valido (T1078), distruzione dei dati (T1485)

**Origini connettore dati:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Descrizione:** Gli eventi imprevisti Fusion di questo tipo indicano che un numero anomalo di messaggi di posta elettronica è stato eliminato in una singola sessione dopo un accesso sospetto a un account Azure AD. Ciò indica che è possibile che l'account indicato nella descrizione dell'evento di fusione sia stato compromesso ed è stato usato per eliminare i dati per scopi dannosi, ad esempio per danneggiare l'organizzazione o nascondere l'attività di posta elettronica relativa alla posta indesiderata. Le permutazioni di avvisi di accesso Azure AD sospette con l'avviso di attività di eliminazione di posta elettronica sospetta sono:   

- **Trasferimento Impossibile a una località atipica che causa un'attività di eliminazione di posta elettronica sospetta**

- **Evento di accesso da una posizione non nota che causa un'attività di eliminazione di posta elettronica sospetta**

- **Evento di accesso da un dispositivo infetto che causa un'attività di eliminazione di posta elettronica sospetta**

- **Evento di accesso da un indirizzo IP anonimo che conduce a un'attività di eliminazione di posta elettronica sospetta**

- **Evento di accesso dall'utente con credenziali perse che portano a un'attività di eliminazione della posta elettronica sospetta**

## <a name="denial-of-service"></a>Denial of Service

### <a name="multiple-vm-delete-activities-following-suspicious-azure-ad-sign-in"></a>Più attività di eliminazione di macchine virtuali dopo l'accesso Azure AD sospetta
Questo scenario è attualmente disponibile in **anteprima pubblica**.

**Mitre ATT&le tattiche CK:** Accesso iniziale, effetti

**Mitre ATT&le tecniche CK:** Account valido (T1078), endpoint Denial of Service (T1499)

**Origini connettore dati:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Descrizione:** Gli eventi imprevisti di Fusion di questo tipo indicano che un numero anomalo di macchine virtuali è stato eliminato in una singola sessione dopo un accesso sospetto a un account Azure AD. Questa indicazione garantisce un livello di attendibilità elevato rispetto all'account indicato nella descrizione dell'evento imprevisto Fusion ed è stato usato per tentare di danneggiare o distruggere l'ambiente cloud dell'organizzazione. Le permutazioni di avvisi di accesso Azure AD sospette con l'avviso più attività di eliminazione delle macchine virtuali sono:  

- **Trasferimento Impossibile a una posizione atipica che conduce a più attività di eliminazione di macchine virtuali**

- **Evento di accesso da una posizione non nota che conduce a più attività di eliminazione di macchine virtuali**

- **Evento di accesso da un dispositivo infetto che conduce a più attività di eliminazione di macchine virtuali**

- **Evento di accesso da un indirizzo IP anonimo che conduce a più attività di eliminazione di macchine virtuali**

- **Evento di accesso dall'utente con credenziali perse che portano a più attività di eliminazione della macchina virtuale**

## <a name="lateral-movement"></a>Spostamento laterale

### <a name="office-365-impersonation-following-suspicious-azure-ad-sign-in"></a>Rappresentazione di Office 365 dopo l'accesso Azure AD sospetto

**Mitre ATT&le tattiche CK:** Accesso iniziale, spostamento laterale

**Mitre ATT&le tecniche CK:** Account valido (T1078), Internal Spear phishing (T1534)

**Origini connettore dati:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Descrizione:** Gli eventi imprevisti Fusion di questo tipo indicano che si è verificato un numero anomalo di azioni di rappresentazione in seguito a un accesso sospetto da un account Azure AD. In alcuni software sono disponibili opzioni che consentono agli utenti di rappresentare altri utenti. Ad esempio, i servizi di posta elettronica consentono agli utenti di autorizzare altri utenti a inviare messaggi di posta elettronica per loro conto. Questo avviso indica con maggiore fiducia che l'account indicato nella descrizione dell'evento imprevisto Fusion è stato compromesso ed è stato usato per condurre attività di rappresentazione per scopi dannosi, ad esempio l'invio di messaggi di posta elettronica di phishing per la distribuzione di malware o lo spostamento laterale. Le permutazioni degli avvisi di accesso Azure AD sospette con l'avviso di rappresentazione di Office 365 sono:  

- **Trasferimento Impossibile a una posizione atipica che conduce alla rappresentazione di Office 365**

- **Evento di accesso da una posizione non nota che conduce alla rappresentazione di Office 365**

- **Evento di accesso da un dispositivo infetto che conduce alla rappresentazione di Office 365**

- **Evento di accesso da un indirizzo IP anonimo che conduce alla rappresentazione di Office 365**

- **Evento di accesso dall'utente con credenziali perse che portano alla rappresentazione di Office 365**
 
### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>Regole di manipolazione della posta in arrivo sospette impostate dopo l'accesso Azure AD sospetto
Questo scenario appartiene a due classificazioni delle minacce in questo elenco: **spostamento laterale** e **dati exfiltration**. Per maggiore chiarezza, viene visualizzato in entrambe le sezioni.

Questo scenario è attualmente disponibile in **anteprima pubblica**.

**Mitre ATT&le tattiche CK:** Accesso iniziale, spostamento laterale, exfiltration

**Mitre ATT&le tecniche CK:** Account valido (T1078), Spear phishing interno (T1534), exfiltration automatizzato (T1020)

**Origini connettore dati:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Descrizione:** Gli eventi imprevisti di Fusion di questo tipo indicano che le regole della posta in arrivo anomale sono state impostate nella posta in arrivo di un utente dopo un accesso sospetto a un account Azure AD. Questa indicazione garantisce un livello di attendibilità elevato che l'account indicato nella descrizione dell'evento imprevisto Fusion è stato compromesso ed è stato usato per modificare le regole della posta in arrivo della posta elettronica dell'utente per scopi dannosi. Questo potrebbe essere un tentativo da parte di un utente malintenzionato di sottrarre i dati dalla rete dell'organizzazione. In alternativa, l'utente malintenzionato potrebbe provare a generare messaggi di posta elettronica di phishing dall'interno dell'organizzazione (ignorando i meccanismi di rilevamento del phishing destinati alla posta elettronica da origini esterne) allo scopo di spostarsi in un secondo momento, ottenendo l'accesso ad account utente e/o con privilegi aggiuntivi. Le permutazioni di avvisi di accesso Azure AD sospette con l'avviso relativo alle regole di manipolazione della posta in arrivo sospette sono:

- **Trasferimento Impossibile a una posizione atipica che causa una regola di manipolazione della posta in arrivo sospetta**

- **Evento di accesso da una posizione non nota che conduce a una regola di manipolazione della posta in arrivo sospetta**

- **Evento di accesso da un dispositivo infetto che conduce a una regola di manipolazione della posta in arrivo sospetta**

- **Evento di accesso da un indirizzo IP anonimo che conduce a una regola di manipolazione della posta in arrivo sospetta**

- **Evento di accesso dall'utente con credenziali perse che portano a una regola di manipolazione della posta in arrivo sospetta**

## <a name="malicious-administrative-activity"></a>Attività amministrativa dannosa

### <a name="suspicious-cloud-app-administrative-activity-following-suspicious-azure-ad-sign-in"></a>Attività amministrative sospette per le app cloud dopo l'accesso Azure AD sospetta

**Mitre ATT&le tattiche CK:** Accesso iniziale, persistenza, evasione della difesa, spostamento laterale, raccolta, exfiltration e conseguenze

**Mitre ATT&le tecniche CK:** N/A

**Origini connettore dati:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Descrizione:** Gli eventi imprevisti Fusion di questo tipo indicano che un numero anomalo di attività amministrative è stato eseguito in una singola sessione dopo un Azure AD di accesso sospetto dallo stesso account. Ciò indica che è possibile che l'account indicato nella descrizione di Fusion Incident sia stato compromesso e che sia stato utilizzato per eseguire un numero qualsiasi di azioni amministrative non autorizzate con finalità dannose. Ciò indica inoltre che è possibile che un account con privilegi amministrativi sia stato compromesso. Le permutazioni di avvisi di accesso Azure AD sospette con l'avviso di attività amministrative di cloud app sospette sono:  

- **Trasferimento Impossibile a una posizione atipica che causa un'attività amministrativa sospetta dell'app Cloud**

- **Evento di accesso da una posizione non nota che causa un'attività amministrativa sospetta per le app Cloud**

- **Evento di accesso da un dispositivo infetto che causa attività amministrative sospette per le app Cloud**

- **Evento di accesso da un indirizzo IP anonimo che causa un'attività amministrativa sospetta dell'app Cloud**

- **Evento di accesso dall'utente con credenziali perse che causano attività amministrative sospette per le app Cloud**

## <a name="malicious-execution-with-legitimate-process"></a>Esecuzione dannosa con processo legittimo

### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>PowerShell ha effettuato una connessione di rete sospetta, seguita da traffico anomalo contrassegnato da Palo Alto Networks firewall.
Questo scenario è attualmente disponibile in **anteprima pubblica**.

**Mitre ATT&le tattiche CK:** Esecuzione

**Mitre ATT&le tecniche CK:** Interprete di comandi e script (T1059)

**Origini connettore dati:** Microsoft Defender for endpoint (in precedenza Microsoft Defender Advanced Threat Protection o MDATP), Palo Alto Networks 

**Descrizione:** Gli eventi imprevisti di Fusion di questo tipo indicano che è stata effettuata una richiesta di connessione in uscita tramite un comando di PowerShell e che, in seguito, è stata rilevata un'attività in ingresso anomala da Palo Alto Networks firewall. Questo consente di indicare che un utente malintenzionato ha probabilmente ottenuto l'accesso alla rete e sta provando a eseguire azioni dannose. I tentativi di connessione eseguiti da PowerShell che seguono questo modello possono essere un'indicazione di attività di controllo e comando malware, richieste di download di malware aggiuntivo o un utente malintenzionato che stabilisce accesso interattivo remoto. Come per tutti gli attacchi di "vita fuori terra", questa attività potrebbe essere un uso legittimo di PowerShell. Tuttavia, l'esecuzione del comando di PowerShell seguita da un'attività del firewall in ingresso sospetta aumenta la fiducia che PowerShell viene usato in modo dannoso e deve essere analizzato ulteriormente. Nei registri di palo alto, Azure Sentinel si concentra sui [log delle minacce](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)e il traffico viene considerato sospetto quando sono consentite le minacce (dati sospetti, file, inondazioni, pacchetti, analisi, spyware, URL, virus, vulnerabilità, incendi-virus, incendi). Fare riferimento anche al log di palo alto Threat corrispondente al [tipo di contenuto o minaccia](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) elencato nella descrizione dell'evento imprevisto Fusion per ulteriori dettagli sull'avviso.

### <a name="suspicious-remote-wmi-execution-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Esecuzione remota di WMI sospetta seguita da traffico anomalo contrassegnato da Palo Alto Networks firewall
Questo scenario è attualmente disponibile in **anteprima pubblica**.

**Mitre ATT&le tattiche CK:** Esecuzione, individuazione

**Mitre ATT&le tecniche CK:** Strumentazione gestione Windows (T1047)

**Origini connettore dati:** Microsoft Defender per endpoint (in precedenza MDATP), Palo Alto Networks 

**Descrizione:** Gli eventi imprevisti di Fusion di questo tipo indicano che i comandi di Windows Management Interface (WMI) sono stati eseguiti in modalità remota in un sistema e, successivamente, l'attività in ingresso sospetta è stata rilevata dal firewall Palo Alto Networks. Ciò indica che un utente malintenzionato potrebbe avere ottenuto l'accesso alla rete e che sta tentando di spostarsi in modo successivo, inoltrare i privilegi e/o eseguire payload dannosi. Come per tutti gli attacchi di "vita fuori terra", questa attività potrebbe essere un utilizzo legittimo di WMI. Tuttavia, l'esecuzione del comando WMI remoto seguita da un'attività del firewall in ingresso sospetta aumenta la fiducia che WMI viene usato in modo dannoso e deve essere analizzato ulteriormente. Nei registri di palo alto, Azure Sentinel si concentra sui [log delle minacce](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)e il traffico viene considerato sospetto quando sono consentite le minacce (dati sospetti, file, inondazioni, pacchetti, analisi, spyware, URL, virus, vulnerabilità, incendi-virus, incendi). Fare riferimento anche al log di palo alto Threat corrispondente al [tipo di contenuto o minaccia](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) elencato nella descrizione dell'evento imprevisto Fusion per ulteriori dettagli sull'avviso.

## <a name="malware-c2-or-download"></a>Malware C2 o download

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Richiesta di rete al servizio TOR anonimato dei seguita da traffico anomalo contrassegnato da Palo Alto Networks firewall.
Questo scenario è attualmente disponibile in **anteprima pubblica**.

**Mitre ATT&le tattiche CK:** Comando e controllo

**Mitre ATT&le tecniche CK:** Canale crittografato (T1573), proxy (T1090)

**Origini connettore dati:** Microsoft Defender per endpoint (in precedenza MDATP), Palo Alto Networks 

**Descrizione:** Gli eventi imprevisti di Fusion di questo tipo indicano che è stata effettuata una richiesta di connessione in uscita al servizio TOR anonimato dei e, in seguito, è stata rilevata un'attività in ingresso anomala da Palo Alto Networks firewall. Questo indica che un utente malintenzionato ha probabilmente ottenuto l'accesso alla rete e sta provando a nascondere le loro azioni e finalità. Le connessioni alla rete TOR che seguono questo modello possono essere un'indicazione di attività di controllo e comando malware, richieste di download di malware aggiuntivo o un utente malintenzionato che stabilisce accesso interattivo remoto. Nei registri di palo alto, Azure Sentinel si concentra sui [log delle minacce](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)e il traffico viene considerato sospetto quando sono consentite le minacce (dati sospetti, file, inondazioni, pacchetti, analisi, spyware, URL, virus, vulnerabilità, incendi-virus, incendi). Fare riferimento anche al log di palo alto Threat corrispondente al [tipo di contenuto o minaccia](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) elencato nella descrizione dell'evento imprevisto Fusion per ulteriori dettagli sull'avviso.

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Connessione in uscita a IP con una cronologia di tentativi di accesso non autorizzati seguito da traffico anomalo contrassegnato da Palo Alto Networks firewall
Questo scenario è attualmente disponibile in **anteprima pubblica**.

**Mitre ATT&le tattiche CK:** Comando e controllo

**Mitre ATT&le tecniche CK:** Non applicabile

**Origini connettore dati:** Microsoft Defender per endpoint (in precedenza MDATP), Palo Alto Networks 

**Descrizione:** Gli eventi imprevisti di Fusion di questo tipo indicano che è stata stabilita una connessione in uscita a un indirizzo IP con una cronologia dei tentativi di accesso non autorizzati e che, in seguito, è stata rilevata un'attività anomala da Palo Alto Networks firewall. Questo indica che un utente malintenzionato ha probabilmente ottenuto l'accesso alla rete. I tentativi di connessione che seguono questo modello possono essere un'indicazione dell'attività del comando e del controllo malware, le richieste di download di malware aggiuntivo o un utente malintenzionato che stabilisce l'accesso interattivo remoto. Nei registri di palo alto, Azure Sentinel si concentra sui [log delle minacce](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)e il traffico viene considerato sospetto quando sono consentite le minacce (dati sospetti, file, inondazioni, pacchetti, analisi, spyware, URL, virus, vulnerabilità, incendi-virus, incendi). Fare riferimento anche al log di palo alto Threat corrispondente al [tipo di contenuto o minaccia](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) elencato nella descrizione dell'evento imprevisto Fusion per ulteriori dettagli sull'avviso.

## <a name="ransomware"></a>Ransomware

### <a name="ransomware-execution-following-suspicious-azure-ad-sign-in"></a>Esecuzione di ransomware dopo l'accesso Azure AD sospetta

**Mitre ATT&le tattiche CK:** Accesso iniziale, effetti

**Mitre ATT&le tecniche CK:** Account valido (T1078), dati crittografati per l'effetto (T1486)

**Origini connettore dati:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Descrizione:** Gli eventi imprevisti Fusion di questo tipo indicano che è stato rilevato un comportamento anomalo dell'utente che indica un attacco ransomware dopo un accesso sospetto a un account Azure AD. Questa indicazione garantisce un livello di attendibilità elevato che l'account indicato nella descrizione dell'evento imprevisto Fusion è stato compromesso ed è stato utilizzato per crittografare i dati allo scopo di estorcere il proprietario dei dati o negare l'accesso ai dati da parte del proprietario dei dati. Le permutazioni di avvisi di accesso Azure AD sospette con l'avviso di esecuzione ransomware sono:  

- **Trasferimento Impossibile a una posizione atipica che conduce a ransomware nell'app Cloud**

- **Evento di accesso da una posizione non nota che conduce a ransomware nell'app Cloud**

- **Evento di accesso da un dispositivo infetto che conduce a ransomware nell'app Cloud**

- **Evento di accesso da un indirizzo IP anonimo che conduce a ransomware nell'app Cloud**

- **Evento di accesso dall'utente con credenziali perse che portano a ransomware nell'app Cloud**

## <a name="remote-exploitation"></a>Exploit remoto

### <a name="suspected-use-of-attack-framework-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Uso sospetto del Framework di attacco seguito da traffico anomalo contrassegnato da Palo Alto Networks firewall
Questo scenario è attualmente disponibile in **anteprima pubblica**.

**Mitre ATT&le tattiche CK:** Accesso iniziale, esecuzione, spostamento laterale, escalation dei privilegi

**Mitre ATT&le tecniche CK:** Exploit di applicazioni pubbliche (T1190), Exploiting for client Execution (T1203), Exploiting of Remote Services (T1210), Exploiting for Privilege escalation (T1068)

**Origini connettore dati:** Microsoft Defender per endpoint (in precedenza MDATP), Palo Alto Networks 

**Descrizione:** Gli eventi imprevisti di Fusion di questo tipo indicano che sono stati rilevati usi non standard dei protocolli, ad esempio l'uso di Framework di attacco come metasploit, ed è stato rilevato che l'attività in ingresso sospetta è stata rilevata dal firewall Palo Alto Networks. Questo può indicare che un utente malintenzionato ha sfruttato un servizio per accedere alle risorse di rete o che un utente malintenzionato ha già ottenuto l'accesso e sta provando a sfruttare ulteriormente i sistemi/servizi disponibili per spostare i privilegi in modo successivo e/o escalation. Nei registri di palo alto, Azure Sentinel si concentra sui [log delle minacce](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)e il traffico viene considerato sospetto quando sono consentite le minacce (dati sospetti, file, inondazioni, pacchetti, analisi, spyware, URL, virus, vulnerabilità, incendi-virus, incendi). Fare riferimento anche al log di palo alto Threat corrispondente al [tipo di contenuto o minaccia](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) elencato nella descrizione dell'evento imprevisto Fusion per ulteriori dettagli sull'avviso.

## <a name="next-steps"></a>Passaggi successivi

A questo punto si è appreso come ottenere altre informazioni sul rilevamento di attacchi multifase avanzati. si potrebbe essere interessati alla Guida introduttiva seguente per informazioni su come ottenere la visibilità dei dati e le potenziali minacce: [Introduzione a Sentinel di Azure](quickstart-get-visibility.md).

Se si è pronti per esaminare gli eventi imprevisti creati automaticamente, vedere l'esercitazione seguente: esaminare gli [eventi imprevisti con Sentinel di Azure](tutorial-investigate-cases.md).

