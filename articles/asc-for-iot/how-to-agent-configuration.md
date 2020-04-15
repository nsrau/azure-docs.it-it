---
title: Configurare gli agenti di sicurezza
description: Informazioni su come configurare il Centro sicurezza di Azure per gli agenti di sicurezza IoT per l'uso con il Centro sicurezza di Azure per il servizio di sicurezza IoT.Learn how to configure Azure Security Center for IoT security agents for use with the Azure Security Center for IoT security service.
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
ms.date: 02/18/2020
ms.author: mlottner
ms.openlocfilehash: e41b7222ca89976674973e1eb700d62765b306f0
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311336"
---
# <a name="tutorial-configure-security-agents"></a>Esercitazione: Configurare gli agenti di sicurezzaTutorial: Configure security agents

Questo articolo illustra il Centro sicurezza di Azure per gli agenti di sicurezza IoT e descrive in dettaglio come modificarli e configurarli.

> [!div class="checklist"]
> * Configurare gli agenti di sicurezza
> * Modificare il comportamento dell'agente modificando le proprietà gemelle
> * Individua la configurazione predefinita

## <a name="agents"></a>Agenti

Il Centro sicurezza di Azure per gli agenti di sicurezza IoT raccoglie dati dai dispositivi IoT ed esegue azioni di sicurezza per ridurre le vulnerabilità rilevate. La configurazione dell'agente di sicurezza è controllabile utilizzando un set di proprietà del modulo gemello che è possibile personalizzare. In generale, gli aggiornamenti secondari a queste proprietà non sono frequenti.

L'oggetto di configurazione gemello del Centro sicurezza di Azure per l'agente di sicurezza IoT è un oggetto formato JSON. L'oggetto di configurazione è un set di proprietà controllabili che è possibile definire per controllare il comportamento dell'agente.

Queste configurazioni consentono di personalizzare l'agente per ogni scenario richiesto. Ad esempio, è possibile escludere automaticamente alcuni eventi o mantenere il consumo energetico a un livello minimo configurando queste proprietà.

Usare lo [schema](https://aka.ms/iot-security-github-module-schema) di configurazione dell'agente di sicurezza di Azure per ioT per apportare modifiche.

## <a name="configuration-objects"></a>Oggetti di configurazione

Le proprietà correlate a ogni agente di sicurezza IoT del Centro sicurezza di Azure si trovano nell'oggetto di configurazione dell'agente, all'interno della sezione delle proprietà desiderata, del modulo **azureiotsecurity.Properties** related to every Azure Security Center for IoT security agent are located in the agent configuration object, within the desired properties section, of the azureiotsecurity module.

Per modificare la configurazione, creare e modificare questo oggetto all'interno dell'identità gemella del modulo **azureiotsecurity.To** modify the configuration, create and modify this object inside the azureiotsecurity module twin identity.

Se l'oggetto di configurazione dell'agente non esiste nel modulo **gemello di azureiotsecurity,** tutti i valori delle proprietà dell'agente di sicurezza vengono impostati sul valore predefinito.

```json
"desired": {
  "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
  }
}
```

## <a name="configuration-schema-and-validation"></a>Schema di configurazione e convalida

Assicurarsi di convalidare la configurazione dell'agente rispetto a questo [schema.](https://aka.ms/iot-security-github-module-schema) Un agente non verrà avviato se l'oggetto di configurazione non corrisponde allo schema.

Se, mentre l'agente è in esecuzione, l'oggetto di configurazione viene modificato in una configurazione non valida (la configurazione non corrisponde allo schema), l'agente ignorerà la configurazione non valida e continuerà a utilizzare la configurazione corrente.

### <a name="configuration-validation"></a>Convalida della configurazione

Il Security Agent di Azure Security Center for IoT segnala la configurazione corrente all'interno della sezione delle proprietà segnalate dell'identità gemella del modulo **azureiotsecurity.Azure** Security Center for IoT security agent reports its current configuration inside the reported properties section of the azureiotsecurity module twin identity.
L'agente segnala tutte le proprietà disponibili, se una proprietà non è stata impostata dall'utente, l'agente segnala la configurazione predefinita.

Per convalidare la configurazione, confrontare i valori impostati nella sezione desiderata con i valori riportati nella sezione segnalata.

Se esiste una mancata corrispondenza tra le proprietà desiderate e quelle segnalate, l'agente non è stato in grado di analizzare la configurazione.

Convalidare le proprietà desiderate rispetto allo [schema](https://aka.ms/iot-security-github-module-schema), correggere gli errori e impostare nuovamente le proprietà desiderate.

> [!NOTE]
> Un avviso di errore di configurazione verrà generato dall'agente nel caso in cui l'agente non sia stato in grado di analizzare la configurazione desiderata.
> Confrontare la sezione segnalata e desiderata per capire se l'avviso è ancora valido

## <a name="editing-a-property"></a>Modifica di una proprietà

Tutte le proprietà personalizzate devono essere impostate all'interno dell'oggetto di configurazione dell'agente all'interno del modulo **gemello azureiotsecurity.**
Per utilizzare un valore di proprietà predefinito, rimuovere la proprietà dall'oggetto di configurazione.

### <a name="setting-a-property"></a>Impostazione di una proprietà

1. Nel tuo hub IoT, individua e seleziona il dispositivo che desideri modificare.

1. Fare clic sul dispositivo e quindi sul modulo **azureiotsecurity.Click** on your device, and then on azureiotsecurity module.

1. Fare clic su **Module Identity Twin**.

1. Modificare le proprietà che si desidera modificare nel modulo di sicurezza.

   Ad esempio, per configurare gli eventi di connessione come ad alta priorità e raccogliere gli eventi ad alta priorità ogni 7 minuti, utilizzare la configurazione seguente.

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

1. Fare clic su **Salva**.

### <a name="using-a-default-value"></a>Utilizzo di un valore predefinito

Per utilizzare un valore di proprietà predefinito, rimuovere la proprietà dall'oggetto di configurazione.

## <a name="default-properties"></a>Proprietà predefinite

La tabella seguente contiene le proprietà controllabili del Centro sicurezza di Azure per gli agenti di sicurezza IoT.The following table contains the controllable properties of Azure Security Center for IoT security agents.

I valori predefiniti sono disponibili nello schema corretto in [GitHub](https\://aka.ms/iot-security-module-default).

| Nome| Stato | Valori validi| Valori predefiniti| Descrizione |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Obbligatorio: false |Valori validi: Durata in formato ISO 8601 |Valore predefinito: PT7M |Intervallo di tempo massimo prima dell'invio di messaggi con priorità alta.|
|lowPriorityMessageFrequency |Obbligatorio: false|Valori validi: Durata in formato ISO 8601 |Valore predefinito: PT5H |Tempo massimo prima dell'invio di messaggi con priorità bassa.|
|snapshotFrequenza |Richiesta: falseRequire: false|Valori validi: Durata in formato ISO 8601 |Valore predefinito PT13H |Intervallo di tempo per la creazione di istantanee dello stato del dispositivo.|
|maxLocalCacheSizeInByte |Obbligatorio: false |Valori validi: |Valore predefinito: 2560000, maggiore di 8192Default value: 2560000, larger than 8192 | Spazio di archiviazione massimo (in byte) consentito per la cache dei messaggi di un agente. Quantità massima di spazio consentito per archiviare i messaggi nel dispositivo, prima dell'invio dei messaggi.|
|maxMessageSizeInByte |Obbligatorio: false |Valori validi: un numero positivo, maggiore di 8192, minore di 262144 |Valore predefinito: 204800 |Dimensione massima consentita di un agente per il messaggio cloud. Questa impostazione controlla la quantità massima di dati inviati in ogni messaggio. |
|EventPriority, NomeEvento, NomeEvento |Obbligatorio: false |Valori validi: High, Low, Off |Valori predefiniti: |Priorità di ogni evento generato dall'agente |

### <a name="supported-security-events"></a>Eventi di sicurezza supportati

|Nome evento| PropertyName | Default Value| Evento snapshot| Stato dettagli  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Evento di diagnostica|eventPriorityDiagnostic| Disattivato| False| Eventi di diagnostica correlati all'agente. Utilizzare questo evento per la registrazione dettagliata.|
|Errore di configurazione |eventoPriorityConfigurationError |Basso |False |L'agente non è riuscito ad analizzare la configurazione. Verificare la configurazione rispetto allo schema.|
|Statistiche degli eventi eliminati |eventPriorityDroppedEventsStatistics (eventoPriorityDroppedEventsStatistics) |Basso |True|Statistiche degli eventi correlati all'agente. |
|Hardware connesso|eventoPriorityConnectedHardware |Basso |True |Istantanea di tutto l'hardware collegato al dispositivo.|
|Porte in ascolto|eventPriorityListeningPorts |Alto |True |Istantanea di tutte le porte di attesa aperte sul dispositivo.|
|Creazione processo |eventoPriorityProcessCreate |Basso |False |Controlla la creazione del processo nel dispositivo.|
|Terminazione processo|eventoPriorityProcessTerminate |Basso |False |Controlla la terminazione del processo sul dispositivo.|
|Informazioni di sistema |eventPrioritySystemInformation |Basso |True |Un'istantanea delle informazioni di sistema (ad esempio: Sistema operativo o CPU).|
|Utenti locali| eventPriorityLocalUsers |Alto |True|Un'istantanea degli utenti locali registrati all'interno del sistema. |
|Login|  eventPriorityLogin (Accesso a Windows Priority) |Alto|False|Controllare gli eventi di accesso al dispositivo (account di accesso locali e remoti).|
|Connessione crea |eventPriorityConnectionCreate|Basso|False|Controlla le connessioni TCP create da e verso il dispositivo. |
|Configurazione del firewall| configurazione di eventPriorityFirewallConfiguration|Basso|True|Istantanea della configurazione del firewall del dispositivo (regole del firewall). |
|Linea di base del sistema operativo| eventPriorityOSBaseline| Basso|True|Istantanea del controllo di base del sistema operativo del dispositivo.|
|

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sui consigli del Centro sicurezza di Azure per l'IoTUnderstand Azure Security Center for IoT recommendations](concept-recommendations.md)
- [Esplorare il Centro sicurezza di Azure per gli avvisi IoTExplore Azure Security Center for IoT alerts](concept-security-alerts.md)
- [Accedere a dati non elaborati sulla sicurezza](how-to-security-data-access.md)
