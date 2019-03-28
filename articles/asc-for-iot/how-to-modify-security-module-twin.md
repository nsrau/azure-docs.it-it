---
title: Come per le istruzioni per modificare un Centro sicurezza di AZURE per IoT modulo gemello in Centro sicurezza di AZURE per l'anteprima di IoT | Microsoft Docs
description: Informazioni su come modificare un Centro sicurezza di AZURE per IoT sicurezza modulo gemello per Centro sicurezza di AZURE per IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 1bc5dc86-0f33-4625-b3d3-f9b6c1a54e14
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 4bb25db7feddea66e75d9dd069aaec7785faf738
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541948"
---
# <a name="modify-an-asc-for-iot-module-twin"></a>Modificare un Centro sicurezza di AZURE per IoT modulo gemello

> [!IMPORTANT]
> Centro sicurezza di AZURE per IoT è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo illustra come modificare la configurazione di un oggetto esistente **AzureIoTSecurity modulo gemello** per un dispositivo esistente. 

Visualizzare [creare un Centro sicurezza di AZURE per il modulo di IoT](quickstart-create-security-twin.md) per imparare a creare un nuovo modulo di protezione per un nuovo dispositivo.  

## <a name="modification-considerations"></a>Considerazioni sulla modifica

> [!IMPORTANT]
> Ogni configurazione nella configurazione del dispositivo gemello sostituisce la configurazione predefinita. Se una configurazione specifica non è più presente nella configurazione del dispositivo gemello, viene utilizzata la configurazione predefinita. 

## <a name="configuration-schema-and-validation"></a>Schema di configurazione e convalida 

Assicurarsi di convalidare la configurazione dell'agente a fronte di questo [schema](https://github.com/Azure/asc-for-iot/schema/security_module_twin). Un agente non verrà avviate se l'oggetto di configurazione corrisponde allo schema.

 
Se, durante l'esecuzione dell'agente, l'oggetto di configurazione viene modificato in una configurazione non valido (la configurazione corrisponde allo schema), l'agente ignorerà la configurazione non è valida e continuerà a usare la configurazione corrente. 

## <a name="edit-a-property"></a>Modificare una proprietà  

Impostare tutte le proprietà personalizzate all'interno dell'oggetto di configurazione dell'agente entro il modulo gemello AzureIoTSecurity. 

Per impostare una proprietà, aggiungere la chiave di proprietà all'oggetto di configurazione con il valore desiderato. Per usare un valore di proprietà predefinito, rimuovere la proprietà dell'oggetto di configurazione:

```json
"desired": { //AzureIoTSecurity Module Identity Twin – desired properties section  
  "azureiot*com^securityAgentConfiguration^1*0*0": { //ASC for IoT Agent 
      // configuration section  
    "hubResourceId": "/subscriptions/82392767-31d3-4bd2-883d-9b60596f5f42/resourceGroups/myResourceGroup/providers/Microsoft.Devices/IotHubs/myIotHub",     
    "lowPriorityMessageFrequency": "PT1H",     
    "highPriorityMessageFrequency": "PT7M",    
    "eventPriorityFirewallConfiguration": "High",     
    "eventPriorityConnectionCreate": "Off" 
  } 
}, 
```

## <a name="properties"></a>Properties 
Nella tabella seguente contiene tutte le proprietà configurabili che controllano il Centro sicurezza di AZURE per gli agenti di IoT. 
          

| NOME| Stato | Valori validi| Valori predefiniti| DESCRIZIONE |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Richiesto: false |Valori validi:  Durata in formato ISO 8601 |Valore predefinito: PT7M |Tempo massimo prima dei messaggi con priorità alta vengono inviati.|
|lowPriorityMessageFrequency |Richiesto: false|Valori validi:  Durata in formato ISO 8601 |Valore predefinito: PT5H |Tempo massimo prima dei messaggi con priorità bassa vengono inviati.| 
|snapshotFrequency |Richiedi: false|I valori valida: durata in formato ISO 8601 |Valore predefinito PT13H |Intervallo di tempo per la creazione di snapshot dello stato di dispositivi.| 
|maxLocalCacheSizeInBytes |Richiesto: false |Valori validi:  |Valore predefinito: 2560000, dimensioni superiori a 8192 | Archiviazione massima (in byte) consentito per la cache messaggi di un agente. Quantità massima di spazio consentito per archiviare i messaggi sul dispositivo, prima che i messaggi vengono inviati.| 
|maxMessageSizeInBytes |Richiesto: false |Valori validi:  Un numero positivo maggiore di 8192 262144 minore di |Valore predefinito: 204800 |Numero massimo di dimensioni del messaggio da cloud di un agente. Questa impostazione controlla la quantità di dati massimi in ogni messaggio inviati. |
|eventPriority${EventName} |Richiesto: false |Valori validi:  Massimo, minimo, disattivato |Valori predefiniti: |Priorità di ogni agente ha generato l'evento. | 
|

### <a name="events"></a>Eventi

Nell'elenco seguente di eventi sono tutti gli eventi il Centro sicurezza di AZURE per IoT agent può raccogliere dai dispositivi. Usare il modulo gemello AzureIotSecurity per configurare quali di questi eventi vengono raccolti e decidere le priorità nella soluzione. 
 
|Nome evento| PropertyName | Valore predefinito| Evento di snapshot| Stato dei dettagli  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Evento di diagnostica|eventPriorityDiagnostic| Off| False| Gli eventi di diagnostica correlati all'agente. Usare questo evento per la registrazione dettagliata.| 
Errore di configurazione |eventPriorityConfigurationError |Basso |False |Agente non è riuscito ad analizzare la configurazione. Verificare la configurazione a fronte dello schema.| 
|Statistiche di eventi eliminati |eventPriorityDroppedEventsStatistics |Basso |True |Le statistiche di eventi correlati all'agente. |
|Statistiche relative ai messaggi|eventPriorityMessageStatistics |Basso |True  |Statistiche relative ai messaggi correlati all'agente. |
|Componenti hardware connessi|eventPriorityConnectedHardware |Basso |True  |Snapshot di tutti i componenti hardware connessi al dispositivo.|
|Porte in ascolto|eventPriorityListeningPorts |Alto |True  |Snapshot di tutte le porte in ascolto sul dispositivo.|
| Crea processo |eventPriorityProcessCreate |Basso |False |I controlli di elaborano la creazione del dispositivo.|
| Processo interrotto|eventPriorityProcessTerminate |Basso |False |I controlli di chiusura sul dispositivo del processo.| 
 Informazioni di sistema |eventPrioritySystemInformation |Basso |True  |Un'istantanea delle informazioni di sistema, ad esempio CPU o del sistema operativo.|
|Utenti locali| eventPriorityLocalUsers |Alto |True |Uno snapshot degli utenti locali registrati all'interno del sistema. |
|Login|  eventPriorityLogin |Alto|False|Consente di controllare gli eventi di accesso al dispositivo (accessi locali e remoti).|
|Crea connessione |eventPriorityConnectionCreate|Basso|False|Consente di controllare le connessioni TCP create da e verso il dispositivo. |
|Configurazione del firewall| eventPriorityFirewallConfiguration|Basso|True |Snapshot della configurazione del firewall dispositivo (regole del firewall). |
|Linea di base del sistema operativo| eventPriorityOSBaseline| Basso|True |Snapshot della linea di base del sistema operativo dispositivo controllare.| 
|


## <a name="next-steps"></a>Passaggi successivi

- Leggere il Centro sicurezza di AZURE per IoT [Panoramica](overview.md)
- Informazioni sul Centro sicurezza di AZURE per IoT [architettura](architecture.md)
- Comprendere ed esplorare [Centro sicurezza di AZURE per gli avvisi di IoT](concept-security-alerts.md)
- Informazioni su come accedere al [dati sulla sicurezza](how-to-security-data-access.md)
