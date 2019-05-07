---
title: Configurare il Centro sicurezza di Azure per IoT agente Preview | Microsoft Docs
description: Informazioni su come configurare gli agenti per l'uso con il Centro sicurezza di Azure per IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: f95c445a-4f0d-4198-9c6c-d01446473bd0
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 311a867c863cbd72292d8e34b4f7abdb949b12c6
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65198452"
---
# <a name="tutorial-configure-security-agents"></a>Esercitazione: Configurare gli agenti di sicurezza

> [!IMPORTANT]
> Centro sicurezza di Azure per IoT è attualmente in versione di anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo illustra Azure Security Center (Centro sicurezza di AZURE) per l'agente di sicurezza IoT, come modificare tali configurare Centro sicurezza di AZURE per gli agenti di sicurezza IoT.

> [!div class="checklist"]
> * Configurare gli agenti di sicurezza
> * Modificare il comportamento dell'agente, modificando le proprietà dei dispositivi gemelli
> * Individuare la configurazione predefinita

## <a name="agents"></a>Agents

Centro sicurezza di AZURE per gli agenti di sicurezza IoT raccogliere dati dai dispositivi IoT ed eseguire azioni di sicurezza per ridurre le vulnerabilità rilevate. Configurazione dell'agente protezione è controllabile tramite un set di proprietà del modulo gemello che è possibile personalizzare. In generale, gli aggiornamenti secondari a queste proprietà sono poco frequenti.  

Centro sicurezza di AZURE per l'oggetto di configurazione dispositivo gemello dell'agente protezione dell'IoT è un oggetto formato JSON. L'oggetto di configurazione è un set di proprietà controllabile che è possibile definire per controllare il comportamento dell'agente. 

Queste configurazioni consentono di personalizzare l'agente per ogni scenario è richiesto. Ad esempio, automaticamente esclusione di alcuni eventi o mantenerli consumo di energia elettrica per un livello minimo sono possibili mediante la configurazione di queste proprietà.  

Usare il Centro sicurezza di AZURE per la configurazione dell'agente di sicurezza IoT [schema](https://aka.ms/iot-security-github-module-schema) per apportare modifiche.  

## <a name="configuration-objects"></a>Oggetti di configurazione 

Ogni Centro sicurezza di AZURE per l'agente di sicurezza IoT correlati proprietà si trova nell'oggetto di configurazione dell'agente, all'interno della sezione, le proprietà desiderate del **azureiotsecurity** modulo. 

Per modificare la configurazione, creare e modificare questo oggetto all'interno di **azureiotsecurity** identità dispositivo gemello del modulo. 

Se l'oggetto di configurazione dell'agente non esiste nel **azureiotsecurity** dispositivo gemello del modulo, tutti i valori di proprietà dell'agente di sicurezza sono impostati sul valore predefinito. 

```json
"desired": {
  "azureiot*com^securityAgentConfiguration^1*0*0": {
  } 
}
```

Assicurarsi di convalidare le modifiche alla configurazione dell'agente a fronte di questo [schema](https://aka.ms/iot-security-github-module-schema).
L'agente non verrà avviate se l'oggetto di configurazione corrisponde allo schema.

## <a name="configuration-schema-and-validation"></a>Schema di configurazione e convalida 

Assicurarsi di convalidare la configurazione dell'agente a fronte di questo [schema](https://aka.ms/iot-security-github-module-schema). Un agente non verrà avviate se l'oggetto di configurazione corrisponde allo schema.

 
Se, durante l'esecuzione dell'agente, l'oggetto di configurazione viene modificato in una configurazione non valido (la configurazione corrisponde allo schema), l'agente ignorerà la configurazione non è valida e continuerà a usare la configurazione corrente. 

## <a name="editing-a-property"></a>Modifica di una proprietà 

Tutte le proprietà personalizzate devono essere impostate all'interno dell'oggetto di configurazione dell'agente all'interno di **azureiotsecurity** modulo gemello.
Per usare un valore di proprietà predefinito, rimuovere la proprietà dell'oggetto di configurazione.

### <a name="setting-a-property"></a>Impostazione di una proprietà

1. Nell'IoT Hub, individuare e selezionare il dispositivo che si desidera modificare.

1. Fare clic sul dispositivo, quindi su **azureiotsecurity** modulo.

1. Fare clic su **identità modulo gemello**.

1. Modificare le proprietà desiderate del modulo di protezione.
   
   Ad esempio, per configurare eventi di connessione con priorità elevata e raccogliere gli eventi con priorità alta ogni 7 minuti, usare la configurazione seguente.
   
   ```json
    "desired": {
      "azureiot*com^securityAgentConfiguration^1*0*0": {
        "highPriorityMessageFrequency": "PT7M",    
        "eventPriorityConnectionCreate": "High" 
      } 
    }, 
    ```

1. Fare clic su **Save**.

### <a name="using-a-default-value"></a>Utilizza un valore predefinito

Per usare un valore di proprietà predefinito, rimuovere la proprietà dell'oggetto di configurazione.

## <a name="default-properties"></a>Proprietà predefinite 

Nella tabella seguente contiene le proprietà controllabile del Centro sicurezza di AZURE per gli agenti di sicurezza IoT.

I valori predefiniti sono disponibili nello schema appropriata [Github](https://aka.ms/iot-security-module-default).

| NOME| Stato | Valori validi| Valori predefiniti| DESCRIZIONE |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Richiesto: false |Valori validi:  Durata in formato ISO 8601 |Valore predefinito: PT7M |Tempo massimo prima dei messaggi con priorità alta vengono inviati.|
|lowPriorityMessageFrequency |Richiesto: false|Valori validi:  Durata in formato ISO 8601 |Valore predefinito: PT5H |Tempo massimo prima dei messaggi con priorità bassa vengono inviati.| 
|snapshotFrequency |Richiedi: false|I valori valida: durata in formato ISO 8601 |Valore predefinito PT13H |Intervallo di tempo per la creazione di snapshot dello stato di dispositivi.| 
|maxLocalCacheSizeInBytes |Richiesto: false |Valori validi:  |Valore predefinito: 2560000, dimensioni superiori a 8192 | Archiviazione massima (in byte) consentito per la cache messaggi di un agente. Quantità massima di spazio consentito per archiviare i messaggi sul dispositivo, prima che i messaggi vengono inviati.| 
|maxMessageSizeInBytes |Richiesto: false |Valori validi:  Un numero positivo maggiore di 8192 262144 minore di |Valore predefinito: 204800 |Numero massimo di dimensioni del messaggio da cloud di un agente. Questa impostazione controlla la quantità di dati massimi in ogni messaggio inviati. |
|eventPriority${EventName} |Richiesto: false |Valori validi:  Massimo, minimo, disattivato |Valori predefiniti: |Priorità di ogni agente ha generato l'evento | 

### <a name="supported-security-events"></a>Eventi di sicurezza supportate

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
 

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sul Centro sicurezza di AZURE di consigli di IoT](concept-recommendations.md)
- [Esplorazione di Centro sicurezza di AZURE per IoT avvisi](concept-security-alerts.md)
- [Accedere ai dati di sicurezza non elaborati](how-to-security-data-access.md)
