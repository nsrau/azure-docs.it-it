---
title: Configurare il Centro sicurezza di Azure per l'agente Microsoft Docs
description: Informazioni su come configurare gli agenti per l'uso con il Centro sicurezza di Azure.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: f95c445a-4f0d-4198-9c6c-d01446473bd0
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: mlottner
ms.openlocfilehash: 095c8fa080d96c9dc6d40261ee5afc559e9ca06b
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933875"
---
# <a name="tutorial-configure-security-agents"></a>Esercitazione: Configurare gli agenti di sicurezza

Questo articolo illustra il Centro sicurezza di Azure per gli agenti di sicurezza Internet e illustra come modificarli e configurarli. 

> [!div class="checklist"]
> * Configurare gli agenti di sicurezza
> * Modificare il comportamento dell'agente modificando le proprietà gemelle
> * Individua configurazione predefinita

## <a name="agents"></a>Agenti

Il Centro sicurezza di Azure per gli agenti di sicurezza Internet raccoglie i dati dai dispositivi Internet e esegue azioni di sicurezza per attenuare le vulnerabilità rilevate. La configurazione dell'agente di sicurezza è controllabile usando un set di proprietà dei moduli gemelli che è possibile personalizzare. In generale, gli aggiornamenti secondari a queste proprietà non sono frequenti.  

Il Centro sicurezza di Azure per l'oggetto di configurazione dell'agente di sicurezza di Internet è un oggetto formato JSON. L'oggetto di configurazione è un set di proprietà controllabili che è possibile definire per controllare il comportamento dell'agente. 

Queste configurazioni consentono di personalizzare l'agente per ogni scenario necessario. Se ad esempio si escludono automaticamente alcuni eventi o se il consumo di energia a un livello minimo è possibile, è possibile configurare queste proprietà.  

Usare il Centro sicurezza di Azure per lo [schema](https://aka.ms/iot-security-github-module-schema) di configurazione dell'agente di sicurezza Internet per apportare modifiche.  

## <a name="configuration-objects"></a>Oggetti di configurazione 

Le proprietà correlate a ogni Centro sicurezza di Azure per l'agente di sicurezza di Internet si trovano nell'oggetto di configurazione dell'agente, all'interno della sezione delle proprietà desiderata, del modulo **azureiotsecurity** . 

Per modificare la configurazione, creare e modificare questo oggetto all'interno dell'identità **azureiotsecurity** del modulo gemello. 

Se l'oggetto di configurazione dell'agente non è presente nel modulo gemello di **azureiotsecurity** , tutti i valori delle proprietà dell'agente di sicurezza sono impostati su predefinito. 

```json
"desired": {
  "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
  } 
}
```

## <a name="configuration-schema-and-validation"></a>Schema di configurazione e convalida 

Assicurarsi di convalidare la configurazione dell'agente rispetto a questo [schema](https://aka.ms/iot-security-github-module-schema). Un agente non viene avviato se l'oggetto di configurazione non corrisponde allo schema.

 
Se, mentre l'agente è in esecuzione, l'oggetto di configurazione viene modificato in una configurazione non valida (la configurazione non corrisponde allo schema), l'agente ignorerà la configurazione non valida e continuerà a utilizzare la configurazione corrente. 

### <a name="configuration-validation"></a>Convalida configurazione

Il Centro sicurezza di Azure per l'agente sicurezza di Azure segnala la configurazione corrente all'interno della sezione delle proprietà segnalate dell'identità del dispositivo gemello del modulo **azureiotsecurity** .
L'agente segnala tutte le proprietà disponibili, se una proprietà non è stata impostata dall'utente, l'agente segnala la configurazione predefinita.

Per convalidare la configurazione, confrontare i valori impostati nella sezione desiderata con i valori riportati nella sezione segnalata.

Se si verifica una mancata corrispondenza tra le proprietà desiderate e segnalate, l'agente non è stato in grado di analizzare la configurazione.

Convalidare le proprietà desiderate rispetto allo [schema](https://aka.ms/iot-security-github-module-schema), correggere gli errori e impostare di nuovo le proprietà desiderate.

> [!NOTE]
> Verrà generato un avviso di errore di configurazione dall'agente nel caso in cui l'agente non sia stato in grado di analizzare la configurazione desiderata.
> Confrontare la sezione segnalata e desiderata per determinare se l'avviso è ancora applicabile

## <a name="editing-a-property"></a>Modifica di una proprietà 

Tutte le proprietà personalizzate devono essere impostate all'interno dell'oggetto di configurazione dell'agente all'interno del modulo gemello **azureiotsecurity** .
Per usare un valore di proprietà predefinito, rimuovere la proprietà dall'oggetto di configurazione.

### <a name="setting-a-property"></a>Impostazione di una proprietà

1. Nell'hub Internet delle cose individuare e selezionare il dispositivo che si vuole modificare.

1. Fare clic sul dispositivo e quindi sul modulo **azureiotsecurity** .

1. Fare clic su **Module Identity gemelle**.

1. Modificare le proprietà che si desidera modificare nel modulo di sicurezza.
   
   Ad esempio, per configurare gli eventi di connessione come priorità alta e raccogliere gli eventi con priorità alta ogni 7 minuti, utilizzare la configurazione seguente.
   
    ```json
    "desired": {
        "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
            "highPriorityMessageFrequency": {
                "value": "PT7M"
            },
            "eventPriorityConnectionCreate": {
                "value": "High"
            }
        }
    }
    ```

1. Fare clic su **Save**.

### <a name="using-a-default-value"></a>Uso di un valore predefinito

Per usare un valore di proprietà predefinito, rimuovere la proprietà dall'oggetto di configurazione.

## <a name="default-properties"></a>Proprietà predefinite 

La tabella seguente contiene le proprietà controllabili del Centro sicurezza di Azure per gli agenti di sicurezza.

I valori predefiniti sono disponibili nello schema appropriato in [GitHub](https\://aka.ms/iot-security-module-default).

| Name| Stato | Valori validi| Valori predefiniti| Descrizione |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Obbligatorio: false |Valori validi: Durata nel formato ISO 8601 |Valore predefinito: PT7M |Intervallo di tempo massimo prima dell'invio dei messaggi con priorità alta.|
|lowPriorityMessageFrequency |Obbligatorio: false|Valori validi: Durata nel formato ISO 8601 |Valore predefinito: PT5H |Tempo massimo prima dell'invio dei messaggi con priorità bassa.| 
|snapshotFrequency |Richiedi: false|Valori validi: Durata nel formato ISO 8601 |Valore predefinito PT13H |Intervallo di tempo per la creazione di snapshot dello stato del dispositivo.| 
|maxLocalCacheSizeInBytes |Obbligatorio: false |Valori validi: |Valore predefinito: 2560000, maggiore di 8192 | Archiviazione massima (in byte) consentita per la cache dei messaggi di un agente. Quantità massima di spazio consentito per l'archiviazione dei messaggi nel dispositivo prima dell'invio dei messaggi.| 
|maxMessageSizeInBytes |Obbligatorio: false |Valori validi: Un numero positivo maggiore di 8192, minore di 262144 |Valore predefinito: 204800 |Dimensione massima consentita di un agente per il messaggio cloud. Questa impostazione consente di controllare la quantità massima di dati inviati in ogni messaggio. |
|eventPriority${EventName} |Obbligatorio: false |Valori validi: Alto, basso, disattivato |Valori predefiniti: |Priorità di ogni evento generato dall'agente | 

### <a name="supported-security-events"></a>Eventi di sicurezza supportati

|Nome evento| PropertyName | Default Value| Evento snapshot| Stato dettagli  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Evento di diagnostica|eventPriorityDiagnostic| Off| False| Eventi di diagnostica correlati agli agenti. Utilizzare questo evento per la registrazione dettagliata.| 
|Errore di configurazione |eventPriorityConfigurationError |Basso |False |L'agente non è riuscito ad analizzare la configurazione. Verificare la configurazione in base allo schema.| 
|Statistiche eventi eliminati |eventPriorityDroppedEventsStatistics |Basso |True|Statistiche evento correlate agli agenti. |
|Statistiche messaggio|eventPriorityMessageStatistics |Basso |True |Statistiche messaggi correlati all'agente. |
|Hardware connesso|eventPriorityConnectedHardware |Basso |True |Snapshot di tutti i componenti hardware connessi al dispositivo.|
|Porte in ascolto|eventPriorityListeningPorts |Alto |True |Snapshot di tutte le porte di ascolto aperte sul dispositivo.|
|Creazione processo |eventPriorityProcessCreate |Basso |False |Controlla la creazione del processo nel dispositivo.|
|Terminazione processo|eventPriorityProcessTerminate |Basso |False |Controlla la terminazione del processo nel dispositivo.| 
|Informazioni di sistema |eventPrioritySystemInformation |Basso |True |Uno snapshot delle informazioni di sistema, ad esempio: Sistema operativo o CPU).| 
|Utenti locali| eventPriorityLocalUsers |Alto |True|Snapshot degli utenti locali registrati nel sistema. |
|Accedi|  eventPriorityLogin |Alto|False|Controllare gli eventi di accesso al dispositivo (account di accesso locali e remoti).|
|Creazione della connessione |eventPriorityConnectionCreate|Basso|False|Controlla le connessioni TCP create da e verso il dispositivo. |
|Configurazione del firewall| eventPriorityFirewallConfiguration|Basso|True|Snapshot della configurazione del firewall del dispositivo (regole del firewall). |
|Baseline del sistema operativo| eventPriorityOSBaseline| Basso|True|Snapshot del controllo della linea di base del sistema operativo del dispositivo.|
|
 

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sul centro sicurezza di Azure per le raccomandazioni](concept-recommendations.md)
- [Esplora il Centro sicurezza di Azure per gli avvisi](concept-security-alerts.md)
- [Accesso ai dati di sicurezza non elaborati](how-to-security-data-access.md)
