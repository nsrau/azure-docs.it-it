---
title: Come gestire i segreti quando si lavora in uno spazio Azure Dev Spaces
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: Informazioni su come usare i segreti di Kubernetes in fase di esecuzione o di compilazione quando si sviluppano applicazioni con Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, servizio Azure Kubernetes, servizio Azure Container, contenitori
ms.openlocfilehash: d9dd0de348612bbb3baf5fb351c1c9af1c228c1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438470"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Come gestire i segreti quando si lavora in uno spazio Azure Dev Spaces

I servizi potrebbero richiedere alcune password, stringhe di connessione e altri segreti, ad esempio per i database o altri servizi sicuri di Azure. Impostando i valori di questi segreti nei file di configurazione, è possibile renderli disponibili nel codice come variabili di ambiente,  Questi file di configurazione devono essere gestiti con attenzione per evitare di compromettere la sicurezza dei segreti.

## <a name="storing-and-using-runtime-secrets"></a>Archiviazione e utilizzo di segreti di runtime

Azure Dev Spaces offre due opzioni consigliate e semplificate per l'archiviazione di segreti `values.dev.yaml` nei grafici Helm `azds.yaml`generati dagli strumenti client Azure Dev Spaces: nel file e inline direttamente in . Non è consigliabile memorizzare `values.yaml`i segreti in .

> [!NOTE]
> Negli approcci seguenti viene illustrato come archiviare e utilizzare i segreti per i grafici Helm generati dagli strumenti client. Se si crea il proprio grafico Helm, è possibile utilizzare il grafico Helm direttamente per gestire e memorizzare i segreti.

### <a name="using-valuesdevyaml"></a>Utilizzo di values.dev.yamlUsing values.dev.yaml

In un progetto già preparato con Azure Dev `values.dev.yaml` Spaces creare un `azds.yaml` file nella stessa cartella per definire le chiavi e i valori dei segreti. Ad esempio:

```yaml
secrets:
  redis:
    port: "6380"
    host: "contosodevredis.redis.cache.windows.net"
    key: "secretkeyhere"
```

Verificare `azds.yaml` i `values.dev.yaml` riferimenti al `?`file come facoltativi utilizzando un file . Ad esempio:

```yaml
install:
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
```

Se si dispone di ulteriori file segreti, è possibile aggiungerli anche qui.

Aggiornare o verificare che il servizio faccia riferimento ai segreti come variabili di ambiente. Ad esempio:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Eseguire i servizi `azds up`aggiornati utilizzando .

```console
azds up
```
 
Utilizzare `kubectl` per verificare che i segreti siano stati creati.

```console
kubectl get secret --namespace default -o yaml 
```

> [!IMPORTANT]
> Non è consigliabile archiviare segreti nel controllo del codice sorgente. Se si usa `values.dev.yaml` Git, aggiungere al file per evitare il `.gitignore` commit di segreti nel controllo del codice sorgente.

### <a name="using-azdsyaml"></a>Utilizzo di azds.yaml

In un progetto già preparato con Azure Dev Spaces aggiungere chiavi segrete e valore usando `azds.yaml` *$PLACEHOLDER* sintassi in *configurations.develop.install.set* in . Ad esempio:

```yaml
configurations:
  develop:
    ...
    install:
      set:
        secrets:
          redis:
            port: "$REDIS_PORT"
            host: "$REDIS_HOST"
            key: "$REDIS_KEY"
```

> [!NOTE]
> È possibile immettere direttamente *$PLACEHOLDER* valori `azds.yaml`segreti senza utilizzare $PLACEHOLDER sintassi in . Tuttavia, questo approccio `azds.yaml` non è consigliato poiché viene archiviato nel controllo del codice sorgente.
     
Creare `.env` un file nella `azds.yaml` stessa cartella per definire i valori *$PLACEHOLDER.* Ad esempio:

```
REDIS_PORT=3333
REDIS_HOST=myredishost
REDIS_KEY=myrediskey
```

> [!IMPORTANT]
> Non è consigliabile archiviare segreti nel controllo del codice sorgente. Se si usa `.env` Git, aggiungere al file per evitare il `.gitignore` commit di segreti nel controllo del codice sorgente.

Aggiornare o verificare che il servizio faccia riferimento ai segreti come variabili di ambiente. Ad esempio:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Eseguire i servizi `azds up`aggiornati utilizzando .

```console
azds up
```
 
Utilizzare `kubectl` per verificare che i segreti siano stati creati.

```console
kubectl get secret --namespace default -o yaml 
```

## <a name="using-secrets-as-build-arguments"></a>Utilizzo di segreti come argomenti di compilazioneUsing secrets as build arguments

Nella sezione precedente è stato illustrato come archiviare e usare i segreti da usare in fase di esecuzione del contenitore. È inoltre possibile utilizzare qualsiasi segreto in fase di compilazione `azds.yaml`del contenitore, ad esempio una password per un NuGet privato, utilizzando .

In `azds.yaml`, impostare i segreti della fase di `<variable name>: ${secret.<secret name>.<secret key>}` compilazione in *configurations.develop.build.args* utilizzando la sintassi . Ad esempio:

```yaml
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
        MYTOKEN: ${secret.mynugetsecret.pattoken}
```

Nell'esempio precedente, *mynugetsecret* è un segreto esistente e *pattoken* è una chiave esistente.

>[!NOTE]
> I nomi e le `.` chiavi dei segreti possono contenere il carattere. Utilizzare `\` per `.` eseguire l'escape quando si passano segreti come argomenti di compilazione. Ad esempio, per passare un segreto denominato *foo.bar* con la chiave di *token*: `MYTOKEN: ${secret.foo\.bar.token}`. Inoltre, i segreti possono essere valutati con prefisso e testo suffisso. Ad esempio: `MYURL: eus-${secret.foo\.bar.token}-version1`. Inoltre, i segreti disponibili negli spazi padre e nonno possono essere passati come argomenti di compilazione.

Nel Dockerfile usare la direttiva *ARG* per usare il segreto, quindi usare la stessa variabile in un secondo momento nel file Docker. Ad esempio:

```dockerfile
...
ARG MYTOKEN
...
ARG NUGET_EXTERNAL_FEED_ENDPOINTS="{'endpointCredentials': [{'endpoint':'PRIVATE_NUGET_ENDPOINT', 'password':'${MYTOKEN}'}]}"
...
```

Aggiornare i servizi in esecuzione nel cluster con queste modifiche. Nella riga di comando eseguire il comando:

```
azds up
```

## <a name="next-steps"></a>Passaggi successivi

Con questi metodi, è ora possibile connettersi in modo sicuro a un database, una cache di Azure per Redis o accedere ai servizi sicuri di Azure.
 
