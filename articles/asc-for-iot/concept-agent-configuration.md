---
title: Configurare un Centro sicurezza di AZURE per l'agente di IoT Preview | Microsoft Docs
description: Informazioni su come configurare gli agenti per l'uso con Centro sicurezza di AZURE per IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: f95c445a-4f0d-4198-9c6c-d01446473bd0
ms.service: ascforiot
ms.devlang: na
ms.topic: concept
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 3a3806c73ef254abab91bd28cd8761917371235f
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541918"
---
# <a name="configure-security-agents"></a>Configurare gli agenti di protezione

> [!IMPORTANT]
> Centro sicurezza di AZURE per IoT è attualmente in anteprima pubblica.
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo fornisce una spiegazione di come configurare un agente per l'uso con Centro sicurezza di AZURE per IoT.

## <a name="agents"></a>Agents

Centro sicurezza di AZURE per gli agenti di sicurezza IoT raccogliere dati dai dispositivi IoT ed eseguire azioni di sicurezza per ridurre le vulnerabilità rilevate. Configurazione dell'agente protezione è controllabile tramite un set di proprietà del modulo gemello che è possibile personalizzare. In generale, gli aggiornamenti secondari a queste proprietà sono poco frequenti.  

Centro sicurezza di AZURE per l'oggetto di configurazione dispositivo gemello dell'agente protezione dell'IoT è un oggetto formato JSON. L'oggetto di configurazione è un set di proprietà controllabile che è possibile definire per controllare il comportamento dell'agente. 

Queste configurazioni consentono di personalizzare l'agente per ogni scenario è richiesto. Ad esempio, automaticamente esclusione di alcuni eventi o mantenerli consumo di energia elettrica per un livello minimo sono possibili mediante la configurazione di queste proprietà.  

Usare il Centro sicurezza di AZURE per la configurazione dell'agente di sicurezza IoT [schema](https://github.com/azure/asc-for-iot-schemas/security/module/twin) per apportare modifiche.  

## <a name="configuration-objects"></a>Oggetti di configurazione 

Ogni Centro sicurezza di AZURE per IoT proprietà si trova all'interno dell'oggetto di configurazione dell'agente, all'interno della sezione delle proprietà desiderate, del modulo azureiotsecurity correlati all'agente di protezione. 

Per modificare la configurazione, creare e modificare questo oggetto all'interno dell'identità del dispositivo gemello modulo azureiotsecurity. Se l'oggetto di configurazione dell'agente non esiste nel dispositivo gemello modulo azureiotsecurity, tutti i valori di proprietà dell'agente protezione sono impostati sul valore predefinito. 

```json
"desired": { //azureiotsecurity Module Identity Twin – desired properties section  
  "azureiot*com^securityAgentConfiguration^1*0*0": { //Agent configuration object 
  … 
} 
}
```

Assicurarsi di convalidare le modifiche alla configurazione dell'agente a fronte di questo [schema](https://aka.ms/iot-security-github-module-schema).
L'agente non verrà avviate se l'oggetto di configurazione corrisponde allo schema.


## <a name="editing-a-property"></a>Modifica di una proprietà 

All'interno dell'oggetto di configurazione dell'agente entro il modulo gemello azureiotsecurity, è necessario impostare tutte le proprietà personalizzate. 

Impostazione di una proprietà sostituisce il valore predefinito. Per impostare una proprietà, aggiungere la chiave di proprietà all'oggetto di configurazione con il valore desiderato. 

Per usare un valore di proprietà predefinito, rimuovere la proprietà dell'oggetto di configurazione. 

```json
"desired": { //azureiotsecurity Module Identity Twin – desired properties section  
  "azureiot*com^securityAgentConfiguration^1*0*0": { //ASC for IoT Agent 
      // configuration section  
    "lowPriorityMessageFrequency": "PT1H",     
    "highPriorityMessageFrequency": "PT7M",    
    "eventPriorityFirewallConfiguration": "High",     
    "eventPriorityConnectionCreate": "High" 
  } 
}, 
```

## <a name="default-properties"></a>Proprietà predefinite 
Set di proprietà controllabile che controllano il Centro sicurezza di AZURE per gli agenti di sicurezza IoT.

I valori predefiniti sono disponibili nello schema appropriata [Github](https://aka.ms/iot-security-module-default).

| NOME| Stato | Valori validi| Valori predefiniti| DESCRIZIONE |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Richiesto: false |Valori validi:  Durata in formato ISO 8601 |Valore predefinito: PT7M |Tempo massimo prima dei messaggi con priorità alta vengono inviati.|
|lowPriorityMessageFrequency |Richiesto: false|Valori validi:  Durata in formato ISO 8601 |Valore predefinito: PT5H |Tempo massimo prima dei messaggi con priorità bassa vengono inviati.| 
|snapshotFrequency |Richiedi: false|I valori valida: durata in formato ISO 8601 |Valore predefinito PT13H |Intervallo di tempo per la creazione di snapshot dello stato di dispositivi.| 
|maxLocalCacheSizeInBytes |Richiesto: false |Valori validi:  |Valore predefinito: 2560000, dimensioni superiori a 8192 | Archiviazione massima (in byte) consentito per la cache messaggi di un agente. Quantità massima di spazio consentito per archiviare i messaggi sul dispositivo, prima che i messaggi vengono inviati.| 
|maxMessageSizeInBytes |Richiesto: false |Valori validi:  Un numero positivo maggiore di 8192 262144 minore di |Valore predefinito: 204800 |Numero massimo di dimensioni del messaggio da cloud di un agente. Questa impostazione controlla la quantità di dati massimi in ogni messaggio inviati. |
|eventPriority${EventName} |Richiesto: false |Valori validi:  Massimo, minimo, disattivato |Valori predefiniti: |Priorità di ogni agente ha generato l'evento | 

### <a name="events"></a>Eventi

|Nome evento| PropertyName | Default Value| Evento di snapshot| Stato dei dettagli  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Evento di diagnostica|eventPriorityDiagnostic| Off| False| Gli eventi di diagnostica correlati all'agente. Usare questo evento per la registrazione dettagliata.| 
|Errore di configurazione |eventPriorityConfigurationError |Basso |False |Agente non è riuscito ad analizzare la configurazione. Verificare la configurazione a fronte dello schema.| 
|Statistiche di eventi eliminati |eventPriorityDroppedEventsStatistics |Basso |True |Le statistiche di eventi correlati all'agente. |
|Statistiche relative ai messaggi|eventPriorityMessageStatistics |Basso |True  |Statistiche relative ai messaggi correlati all'agente. |
|Componenti hardware connessi|eventPriorityConnectedHardware |Basso |True  |Snapshot di tutti i componenti hardware connessi al dispositivo.|
|Porte in ascolto|eventPriorityListeningPorts |Alto |True  |Snapshot di tutte le porte in ascolto sul dispositivo.|
|Crea processo |eventPriorityProcessCreate |Basso |False |I controlli di elaborano la creazione del dispositivo.|
|Processo interrotto|eventPriorityProcessTerminate |Basso |False |I controlli di chiusura sul dispositivo del processo.| 
|Informazioni di sistema |eventPrioritySystemInformation |Basso |True  |Un'istantanea delle informazioni di sistema (ad esempio: Sistema operativo o CPU).| 
|Utenti locali| eventPriorityLocalUsers |Alto |True |Uno snapshot degli utenti locali registrati all'interno del sistema. |
|Login|  eventPriorityLogin |Alto|False|Controllare gli eventi di accesso al dispositivo (accessi locali e remoti).|
|Crea connessione |eventPriorityConnectionCreate|Basso|False|Consente di controllare le connessioni TCP create da e verso il dispositivo. |
|Configurazione del firewall| eventPriorityFirewallConfiguration|Basso|True |Snapshot della configurazione del firewall dispositivo (regole del firewall). |
|Linea di base del sistema operativo| eventPriorityOSBaseline| Basso|True |Snapshot della linea di base del sistema operativo dispositivo controllare.|
 

## <a name="see-also"></a>Vedere anche

- [Informazioni sul Centro sicurezza di AZURE di consigli di IoT](concept-recommendations.md)
- [Esplorazione di Centro sicurezza di AZURE per IoT avvisi](concept-security-alerts.md)
- [Accedere ai dati di sicurezza non elaborati](how-to-security-data-access.md)