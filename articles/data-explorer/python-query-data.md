---
title: 'Guida introduttiva: Eseguire query sui dati con la libreria di Esplora dati di Azure per Python'
description: Questa guida introduttiva descrive come eseguire query sui dati da Esplora dati di Azure tramite Python.
services: data-explorer
author: mgblythe
ms.author: mblythe
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: fee982812456548ed6d1e15d86151df88532389f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956099"
---
# <a name="quickstart-query-data-using-the-azure-data-explorer-python-library"></a>Guida introduttiva: Eseguire query sui dati con la libreria di Esplora dati di Azure per Python

Esplora dati di Azure è un servizio di esplorazione dei dati rapido e a scalabilità elevata per dati di log e di telemetria. Esplora i dati di Azure mette a disposizione una [libreria client per i dati per Python](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data). Questa libreria consente di eseguire query sui dati dal codice. In questa guida introduttiva, per facilitare l'apprendimento ci si connetterà a una tabella nel *cluster della guida* configurato in precedenza. Si eseguirà quindi una query in tale cluster e si riceveranno i risultati.

Questa guida introduttiva è disponibile anche come [Notebook di Azure](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueryKusto.ipynb).

## <a name="prerequisites"></a>Prerequisiti

* Un account di posta elettronica dell'organizzazione che sia membro di Azure Active Directory

* [Python](https://www.python.org/downloads/) installato nel computer di sviluppo

## <a name="install-the-data-library"></a>Installare la libreria per i dati

Installare *azure-kusto-data*.

```python
pip install azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>Aggiungere le costanti e le istruzioni import

Importare le classi dalla libreria, nonché *pandas*, una libreria di analisi dei dati.

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
import pandas as pd
```

Per autenticare un'applicazione, Esplora dati di Azure usa l'ID tenant AAD. Per trovare l'ID del tenant, usare l'URL seguente, sostituendo il dominio a *YourDomain*.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Ad esempio, se il dominio è *contoso.com*, l'URL è: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Fare clic su questo URL per visualizzare i risultati; la prima riga è come indicato di seguito. 

```
`"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"`
```

Il tenant ID in questo caso è `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. Prima di eseguire questo codice, impostare il valore di AAD_TENANT_ID.

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_CLUSTER = "https://help.kusto.windows.net/"
KUSTO_DATABASE  = "Samples"
```

Costruire ora la stringa di connessione. Questo esempio usa l'autenticazione del dispositivo per accedere al cluster. È anche possibile usare il certificato dell'applicazione AAD, la chiave dell'applicazione Azure Active Directory e l'utente e password di AAD.

```python
KCSB = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_CLUSTER)
KCSB.authority_id = AAD_TENANT_ID
```

## <a name="connect-to-azure-data-explorer-and-execute-a-query"></a>Connettersi a Esplora dati di Azure ed eseguire una query

Eseguire una query sul cluster e archiviare l'output in un frame di dati. Quando viene eseguito questo codice, restituisce un messaggio simile al seguente: *Per accedere, usare un web browser per aprire la pagina https://microsoft.com/devicelogin e immettere il codice F3W4VWZDM per l'autenticazione*. Seguire i passaggi per l'accesso, quindi tornare per eseguire il blocco di codice successivo.

```python
KUSTO_CLIENT  = KustoClient(KCSB)
KUSTO_QUERY  = "StormEvents | sort by StartTime desc | take 10"

df = KUSTO_CLIENT.execute_query(KUSTO_DATABASE, KUSTO_QUERY).primary_results[0].to_dataframe()
```

## <a name="explore-data-in-dataframe"></a>Esplorare i dati in DataFrame

Dopo che è stato eseguito l'accesso, la query restituisce risultati, che vengono archiviati in un frame di dati. È possibile usare i risultati in modo analogo a qualsiasi altro frame di dati.

```python
df
```

I primi dieci risultati visualizzati provengono dalla tabella StormEvents.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Guida introduttiva: Inserire dati usando la libreria di Esplora dati di Azure per Python](python-ingest-data.md)