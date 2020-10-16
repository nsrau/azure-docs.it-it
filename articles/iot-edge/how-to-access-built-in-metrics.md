---
title: Accesso alle metriche predefinite-Azure IoT Edge
description: Accesso remoto alle metriche predefinite dai componenti di IoT Edge Runtime
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 10/05/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b6f4e50cac2f809172c2525ea9136a63e6bd9066
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92107012"
---
# <a name="access-built-in-metrics"></a>Accedere alle metriche predefinite

I componenti di IoT Edge Runtime, Hub IoT Edge e IoT Edge Agent, producono metriche predefinite nel [formato di esposizione Prometeo](https://prometheus.io/docs/instrumenting/exposition_formats/). Accedere a queste metriche in remoto per monitorare e comprendere l'integrità di un dispositivo IoT Edge.

A partire dalla versione 1.0.10, le metriche vengono automaticamente esposte per impostazione predefinita sulla **porta 9600** dei moduli **edgeHub** e **edgeAgent** ( `http://edgeHub:9600/metrics` e `http://edgeAgent:9600/metrics` ). Per impostazione predefinita, la porta non è mappata all'host.

Accedere alle metriche dall'host esponendo ed eseguendo il mapping della porta metrica dall'oggetto del modulo `createOptions` . Nell'esempio seguente viene eseguito il mapping della porta metrica predefinita alla porta 9601 nell'host:

```
{
  "ExposedPorts": {
    "9600/tcp": {},
  },
  "HostConfig": {
    "PortBindings": {
      "9600/tcp": [
        {
          "HostPort": "9601"
        }
      ]
    }
  }
}
```

Scegliere numeri di porta host diversi e univoci se si esegue il mapping degli endpoint della metrica edgeHub e edgeAgent.

> [!NOTE]
> Se si vuole disabilitare la metrica, impostare la `MetricsEnabled` variabile di ambiente su `false` per **edgeAgent**.

## <a name="available-metrics"></a>Metriche disponibili

Le metriche contengono tag che consentono di identificare la natura della metrica raccolta. Tutte le metriche contengono i tag seguenti:

| Tag | Description |
|-|-|
| iothub | Hub con cui comunica il dispositivo |
| edge_device | ID del dispositivo corrente |
| instance_number | GUID che rappresenta il runtime corrente. Al riavvio, verranno reimpostate tutte le metriche. Questo GUID semplifica la risoluzione di riavvii. |

Nel formato di esposizione Prometeo sono disponibili quattro tipi di metriche principali: contatore, misuratore, istogramma e riepilogo. Per ulteriori informazioni sui diversi tipi di metrica, vedere la [documentazione relativa ai tipi di metrica Prometeo](https://prometheus.io/docs/concepts/metric_types/).

I quantili specificati per l'istogramma e la metrica di riepilogo predefiniti sono 0,1, 0,5, 0,9 e 0,99.

Il modulo **edgeHub** produce le metriche seguenti:

| Nome | Dimensioni | Descrizione |
|-|-|-|
| `edgehub_gettwin_total` | `source` (origine operazione)<br> `id` (ID modulo) | Tipo: contatore<br> Numero totale di chiamate a gettwin |
| `edgehub_messages_received_total` | `route_output` (output del messaggio inviato)<br> `id` | Tipo: contatore<br> Numero totale di messaggi ricevuti dai client |
| `edgehub_messages_sent_total` | `from` (origine messaggio)<br> `to` (destinazione messaggio)<br>`from_route_output`<br> `to_route_input` (input destinazione messaggio)<br> `priority` (priorità messaggio alla destinazione) | Tipo: contatore<br> Numero totale di messaggi inviati ai client o upstream<br> `to_route_input` è vuoto quando `to` è $upstream |
| `edgehub_reported_properties_total` | `target`(destinazione aggiornamento)<br> `id` | Tipo: contatore<br> Totale chiamate aggiornamenti proprietà segnalate |
| `edgehub_message_size_bytes` | `id`<br> | Tipo: Riepilogo<br> Dimensioni dei messaggi dai client<br> I valori possono essere segnalati come `NaN` se non vengono segnalate nuove misure per un determinato periodo di tempo (attualmente 10 minuti); per il `summary` tipo, `_count` `_sum` verranno generati i contatori e corrispondenti. |
| `edgehub_gettwin_duration_seconds` | `source` <br> `id` | Tipo: Riepilogo<br> Tempo impiegato per le operazioni get Twin |
| `edgehub_message_send_duration_seconds` | `from`<br> `to`<br> `from_route_output`<br> `to_route_input` | Tipo: Riepilogo<br> Tempo impiegato per l'invio di un messaggio |
| `edgehub_message_process_duration_seconds` | `from` <br> `to` <br> `priority` | Tipo: Riepilogo<br> Tempo impiegato per l'elaborazione di un messaggio dalla coda |
| `edgehub_reported_properties_update_duration_seconds` | `target`<br> `id` | Tipo: Riepilogo<br> Tempo impiegato per aggiornare le proprietà segnalate |
| `edgehub_direct_method_duration_seconds` | `from` chiamante<br> `to` ricevitore | Tipo: Riepilogo<br> Tempo impiegato per la risoluzione di un messaggio diretto |
| `edgehub_direct_methods_total` | `from`<br> `to` | Tipo: contatore<br> Numero totale di messaggi diretti inviati |
| `edgehub_queue_length` | `endpoint` (origine messaggio)<br> `priority` (priorità coda) | Tipo: misuratore<br> Lunghezza corrente della coda di edgeHub per una determinata priorità |
| `edgehub_messages_dropped_total` | `reason` (no_route, ttl_expiry)<br> `from` <br> `from_route_output` | Tipo: contatore<br> Numero totale di messaggi rimossi a causa del motivo |
| `edgehub_messages_unack_total` | `reason` (storage_failure)<br> `from`<br> `from_route_output` | Tipo: contatore<br> Numero totale di messaggi non riconosciuti a causa di un errore di archiviazione |
| `edgehub_offline_count_total` | `id` | Tipo: contatore<br> Numero totale di volte in cui edgeHub è stato offline |
| `edgehub_offline_duration_seconds`| `id` | Tipo: Riepilogo<br> Hub di Time Edge offline |
| `edgehub_operation_retry_total` | `id`<br> `operation` (nome operazione) | Tipo: contatore<br> Numero totale di volte in cui sono state ritentate le operazioni edgeHub |
| `edgehub_client_connect_failed_total` | `id` <br> `reason` (non autenticato)<br> | Tipo: contatore<br> Numero totale di volte in cui i client non sono riusciti a connettersi a edgeHub |

Il modulo **edgeAgent** produce le metriche seguenti:

| Nome | Dimensioni | Descrizione |
|-|-|-|
| `edgeAgent_total_time_running_correctly_seconds` | `module_name` | Tipo: misuratore<br> La quantità di tempo durante la quale il modulo è stato specificato nella distribuzione e si trova nello stato in esecuzione |
| `edgeAgent_total_time_expected_running_seconds` | `module_name` | Tipo: misuratore<br> Periodo di tempo durante il quale il modulo è stato specificato nella distribuzione |
| `edgeAgent_module_start_total` | `module_name`, `module_version` | Tipo: contatore<br> Numero di volte in cui edgeAgent ha richiesto a Docker di avviare il modulo |
| `edgeAgent_module_stop_total` | `module_name`, `module_version` | Tipo: contatore<br> Numero di volte in cui edgeAgent ha chiesto a Docker di arrestare il modulo |
| `edgeAgent_command_latency_seconds` | `command` | Tipo: misuratore<br> Tempo impiegato da Docker per eseguire il comando specificato. I comandi possibili sono: crea, Aggiorna, Rimuovi, avvia, Interrompi, riavvia |
| `edgeAgent_iothub_syncs_total` | | Tipo: contatore<br> Numero di volte in cui edgeAgent ha tentato di sincronizzare il proprio dispositivo gemello con iotHub, con esito positivo e negativo. Questo numero include sia l'agente che richiede un gemello che l'hub che invia una notifica di un aggiornamento del dispositivo gemello |
| `edgeAgent_unsuccessful_iothub_syncs_total` | | Tipo: contatore<br> Numero di volte in cui edgeAgent non è riuscito a sincronizzare il proprio dispositivo gemello con iotHub. |
| `edgeAgent_deployment_time_seconds` | | Tipo: contatore<br> Quantità di tempo impiegato per completare una nuova distribuzione dopo la ricezione di una modifica. |
| `edgeagent_direct_method_invocations_count` | `method_name` | Tipo: contatore<br> Numero di volte in cui viene chiamato un metodo diretto edgeAgent incorporato, ad esempio ping o Restart. |
| `edgeAgent_host_uptime_seconds` | | Tipo: misuratore<br> Durata dell'host |
| `edgeAgent_iotedged_uptime_seconds` | | Tipo: misuratore<br> Tempo di esecuzione di iotedged |
| `edgeAgent_available_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype` | Tipo: misuratore<br> Quantità di spazio rimanente sul disco |
| `edgeAgent_total_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype`| Tipo: misuratore<br> Dimensioni del disco |
| `edgeAgent_used_memory_bytes` | `module_name` | Tipo: misuratore<br> Quantità di RAM utilizzata da tutti i processi |
| `edgeAgent_total_memory_bytes` | `module_name` | Tipo: misuratore<br> RAM disponibile |
| `edgeAgent_used_cpu_percent` | `module_name` | Tipo: istogramma<br> Percentuale di CPU utilizzata da tutti i processi |
| `edgeAgent_created_pids_total` | `module_name` | Tipo: misuratore<br> Numero di processi o thread creati dal contenitore |
| `edgeAgent_total_network_in_bytes` | `module_name` | Tipo: misuratore<br> Numero di byte ricevuti dalla rete |
| `edgeAgent_total_network_out_bytes` | `module_name` | Tipo: misuratore<br> Numero di byte inviati alla rete |
| `edgeAgent_total_disk_read_bytes` | `module_name` | Tipo: misuratore<br> Numero di byte letti dal disco |
| `edgeAgent_total_disk_write_bytes` | `module_name` | Tipo: misuratore<br> Numero di byte scritti su disco |
| `edgeAgent_metadata` | `edge_agent_version`, `experimental_features`, `host_information` | Tipo: misuratore<br> Metadati generali relativi al dispositivo. Il valore è sempre 0, le informazioni vengono codificate nei tag. Si noti `experimental_features` che `host_information` sono oggetti JSON. `host_information` ha un aspetto simile a ```{"OperatingSystemType": "linux", "Architecture": "x86_64", "Version": "1.0.10~dev20200803.4", "Provisioning": {"Type": "dps.tpm", "DynamicReprovisioning": false, "AlwaysReprovisionOnStartup": true}, "ServerVersion": "19.03.6", "KernelVersion": "5.0.0-25-generic", "OperatingSystem": "Ubuntu 18.04.4 LTS", "NumCpus": 6, "Virtualized": "yes"}``` . Nota `ServerVersion` è la versione di Docker e `Version` è la versione del daemon di sicurezza IoT Edge. |

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sul runtime di Azure IoT Edge e la relativa architettura](iot-edge-runtime.md)
* [Proprietà dei moduli gemelli "agente di IoT Edge" e "hub di IoT Edge"](module-edgeagent-edgehub.md)
