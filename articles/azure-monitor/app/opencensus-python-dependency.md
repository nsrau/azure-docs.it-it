---
title: Rilevamento delle dipendenze in applicazione Azure Insights con OpenCensus Python | Microsoft Docs
description: Monitorare le chiamate alle dipendenze per le app Python tramite OpenCensus Python.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: 00ebf86bf6d0d57b29d660f78aae3dd828d44ded
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819132"
---
# <a name="track-dependencies-with-opencensus-python"></a>Tenere traccia delle dipendenze con OpenCensus Python

Una dipendenza è un componente esterno chiamato dall'applicazione. I dati sulle dipendenze vengono raccolti usando OpenCensus Python e le varie integrazioni. I dati vengono quindi inviati a Application Insights in monitoraggio di Azure.

Per prima cosa, instrumentare l'applicazione Python con la versione più recente di [OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md).

## <a name="in-process-dependencies"></a>Dipendenze in-process

OpenCensus Python SDK per monitoraggio di Azure consente di inviare i dati di telemetria delle dipendenze "in-process" (informazioni e logica che si verificano all'interno dell'applicazione). Le dipendenze in-process avranno il campo `type` come `INPROC` in Analytics.

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>Dipendenze con l'integrazione "richieste"

Tenere traccia delle richieste in uscita con l'integrazione di `requests` OpenCensus.

Scaricare e installare `opencensus-ext-requests` da [PyPI](https://pypi.org/project/opencensus-ext-requests/) e aggiungerlo alle integrazioni della traccia. Verranno rilevate le richieste inviate tramite la libreria delle [richieste](https://pypi.org/project/requests/) di Python.

```python
import requests
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])  # <-- this line enables the requests integration

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='parent'):
    response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit') # <-- this request will be tracked
```

## <a name="dependencies-with-httplib-integration"></a>Dipendenze con integrazione "HttpLib"

Tenere traccia delle richieste in uscita con OpenCensus `httplib` Integration.

Scaricare e installare `opencensus-ext-httplib` da [PyPI](https://pypi.org/project/opencensus-ext-httplib/) e aggiungerlo alle integrazioni della traccia. Verranno rilevate le richieste inviate tramite [http. client](https://docs.python.org/3.7/library/http.client.html) per Python3 o [HttpLib](https://docs.python.org/2/library/httplib.html) per python2.

```python
import http.client as httplib
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['httplib'])
conn = httplib.HTTPConnection("www.python.org")

tracer = Tracer(
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(1.0)
)

conn.request("GET", "http://www.python.org", "", {})
response = conn.getresponse()
conn.close()
```

## <a name="dependencies-with-django-integration"></a>Dipendenze con l'integrazione con "Django"

Tenere traccia delle richieste Django in uscita con l'integrazione di `django` OpenCensus.

Scaricare e installare `opencensus-ext-django` da [PyPI](https://pypi.org/project/opencensus-ext-django/) e aggiungere la riga seguente alla sezione `MIDDLEWARE` del file di `settings.py` Django.

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

È possibile fornire una configurazione aggiuntiva e leggere le [personalizzazioni](https://github.com/census-instrumentation/opencensus-python#customization) per un riferimento completo.

```python
OPENCENSUS = {
    'TRACE': {
        'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
        'EXPORTER': '''opencensus.ext.ocagent.trace_exporter.TraceExporter(
            service_name='foobar',
        )''',
    }
}
```

## <a name="dependencies-with-mysql-integration"></a>Dipendenze con l'integrazione con "MySQL"

Tenere traccia delle dipendenze di MYSQL con l'integrazione `mysql` di OpenCensus. Questa integrazione supporta la libreria del [connettore MySQL](https://pypi.org/project/mysql-connector-python/) .

Scaricare e installare `opencensus-ext-mysql` da [PyPI](https://pypi.org/project/opencensus-ext-mysql/) e aggiungere le righe seguenti al codice.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>Dipendenze con integrazione "pymysql"

Tenere traccia delle dipendenze di PyMySQL con l'integrazione `pymysql` OpenCensus.

Scaricare e installare `opencensus-ext-pymysql` da [PyPI](https://pypi.org/project/opencensus-ext-pymysql/) e aggiungere le righe seguenti al codice.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>Dipendenze con l'integrazione con "PostgreSQL"

Tenere traccia delle dipendenze di PostgreSQL con l'integrazione `postgresql` di OpenCensus. Questa integrazione supporta la libreria [psycopg2](https://pypi.org/project/psycopg2/) .

Scaricare e installare `opencensus-ext-postgresql` da [PyPI](https://pypi.org/project/opencensus-ext-postgresql/) e aggiungere le righe seguenti al codice.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>Dipendenze con integrazione "pymongo"

Tenere traccia delle dipendenze MongoDB con l'integrazione `pymongo` di OpenCensus. Questa integrazione supporta la libreria [pymongo](https://pypi.org/project/pymongo/) .

Scaricare e installare `opencensus-ext-pymongo` da [PyPI](https://pypi.org/project/opencensus-ext-pymongo/) e aggiungere le righe seguenti al codice.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>Dipendenze con l'integrazione "SQLAlchemy"

Tenere traccia delle dipendenze usando SQLAlchemy usando OpenCensus `sqlalchemy` Integration. Questa integrazione tiene traccia dell'utilizzo del pacchetto [SQLAlchemy](https://pypi.org/project/SQLAlchemy/) , indipendentemente dal database sottostante.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>Passaggi successivi

* [Mappa delle applicazioni](../../azure-monitor/app/app-map.md)
* [Disponibilità](../../azure-monitor/app/monitor-web-app-availability.md)
* [Search](../../azure-monitor/app/diagnostic-search.md)
* [Query log (analisi)](../../azure-monitor/log-query/log-query-overview.md)
* [Diagnostica delle transazioni](../../azure-monitor/app/transaction-diagnostics.md)