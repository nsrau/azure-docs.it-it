---
title: Raccolta di dati JSON personalizzati in Log Analytics | Microsoft Docs
description: È possibile raccogliere origini dati JSON personalizzate in Log Analytics tramite l'agente di Log Analytics per Linux.  Queste origini dati personalizzate possono essere semplici script che restituiscono JSON, ad esempio curl, o uno degli oltre 300 plug-in di FluentD. Questo articolo descrive la configurazione necessaria per questa raccolta di dati.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: c8972df6f42920af6a9bd5f04a27f14dc647da44
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52336621"
---
# <a name="collecting-custom-json-data-sources-with-the-log-analytics-agent-for-linux-in-log-analytics"></a>Raccolta di origini dati JSON personalizzate con l'agente di Log Analytics per Linux in Log Analytics
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]
È possibile raccogliere origini dati JSON personalizzate in Log Analytics tramite l'agente di Log Analytics per Linux.  Queste origini dati personalizzate possono essere semplici script che restituiscono JSON, ad esempio [curl](https://curl.haxx.se/), o uno degli oltre [300 plug-in di FluentD](http://www.fluentd.org/plugins/all). Questo articolo descrive la configurazione necessaria per questa raccolta di dati.

> [!NOTE]
> Per i dati JSON personalizzati è necessario l'agente di Log Analytics per Linux v1.1.0-217+

## <a name="configuration"></a>Configurazione

### <a name="configure-input-plugin"></a>Configurare il plug-in di input

Per raccogliere dati JSON in Log Analytics, aggiungere `oms.api.` all'inizio di un tag FluentD in un plug-in di input.

Di seguito, ad esempio, è riportato un file di configurazione separato `exec-json.conf` in `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`.  Viene usato il plug-in FluentD `exec` per eseguire un comando curl ogni 30 secondi.  L'output di questo comando viene raccolto dal plug-in di output JSON.

```
<source>
  type exec
  command 'curl localhost/json.output'
  format json
  tag oms.api.httpresponse
  run_interval 30s
</source>

<match oms.api.httpresponse>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api_httpresponse*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```
Per il file di configurazione aggiunto in `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` sarà necessario modificare la proprietà con il comando seguente.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Configurare il plug-in di output 
Aggiungere la configurazione del plug-in di output seguente alla configurazione principale presente in `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` o come un file di configurazione separato inserito in `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

```
<match oms.api.**>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

### <a name="restart-log-analytics-agent-for-linux"></a>Riavviare l'agente di Log Analytics per Linux
Riavviare il servizio dell'agente di Log Analytics per Linux con il comando seguente.

    sudo /opt/microsoft/omsagent/bin/service_control restart 

## <a name="output"></a>Output
I dati verranno raccolti in Log Analytics con un record di tipo `<FLUENTD_TAG>_CL`.

Il tag personalizzato `tag oms.api.tomcat` in Log Analytics, ad esempio, viene raccolto con un record di tipo `tomcat_CL`.  È possibile recuperare tutti i record di questo tipo con la ricerca log seguente.

    Type=tomcat_CL

Sono supportate anche origini dati JSON annidate, che tuttavia vengono indicizzate in base al campo padre. I dati JSON seguenti, ad esempio, vengono restituiti da una ricerca di Log Analytics come `tag_s : "[{ "a":"1", "b":"2" }]`.

```
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulle [ricerche nei log](../../log-analytics/log-analytics-queries.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni. 
 
