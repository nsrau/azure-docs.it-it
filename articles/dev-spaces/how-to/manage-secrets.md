---
title: Come gestire i segreti quando si lavora in uno spazio Azure Dev Spaces
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: Informazioni su come usare i segreti Kubernetes in fase di esecuzione o di compilazione quando si sviluppano applicazioni con Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, servizio Azure Kubernetes, servizio Azure Container, contenitori
ms.openlocfilehash: d9dd0de348612bbb3baf5fb351c1c9af1c228c1f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75438470"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Come gestire i segreti quando si lavora in uno spazio Azure Dev Spaces

I servizi potrebbero richiedere alcune password, stringhe di connessione e altri segreti, ad esempio per i database o altri servizi sicuri di Azure. Impostando i valori di questi segreti nei file di configurazione, è possibile renderli disponibili nel codice come variabili di ambiente,  Questi file di configurazione devono essere gestiti con cautela per evitare di compromettere la sicurezza dei segreti.

## <a name="storing-and-using-runtime-secrets"></a>Archiviazione e uso dei segreti di runtime

Azure Dev Spaces offre due opzioni semplificate e consigliate per l'archiviazione dei segreti nei grafici Helm generati dagli strumenti client Azure Dev Spaces: nel `values.dev.yaml` file e inline direttamente in `azds.yaml`. Non è consigliabile archiviare i segreti in `values.yaml`.

> [!NOTE]
> Gli approcci seguenti illustrano come archiviare e usare i segreti per i grafici Helm generati dagli strumenti client. Se si crea un grafico Helm personalizzato, è possibile usare direttamente il grafico Helm per gestire e archiviare i segreti.

### <a name="using-valuesdevyaml"></a>Uso di values. dev. YAML

In un progetto già preparato con Azure Dev Spaces creare un `values.dev.yaml` file nella stessa cartella `azds.yaml` di per definire le chiavi e i valori segreti. Ad esempio:

```yaml
secrets:
  redis:
    port: "6380"
    host: "contosodevredis.redis.cache.windows.net"
    key: "secretkeyhere"
```

Verificare i `azds.yaml` riferimenti `values.dev.yaml` ai file come facoltativi `?`utilizzando un. Ad esempio:

```yaml
install:
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
```

Se sono presenti altri file Secret, è possibile aggiungerli anche qui.

Aggiornare o verificare che il servizio faccia riferimento ai segreti come variabili di ambiente. Ad esempio:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Eseguire i servizi aggiornati usando `azds up`.

```console
azds up
```
 
Usare `kubectl` per verificare che i segreti siano stati creati.

```console
kubectl get secret --namespace default -o yaml 
```

> [!IMPORTANT]
> Non è consigliabile archiviare i segreti nel controllo del codice sorgente. Se si usa Git, `values.dev.yaml` aggiungere al `.gitignore` file per evitare di eseguire il commit dei segreti nel controllo del codice sorgente.

### <a name="using-azdsyaml"></a>Uso di azds. YAML

In un progetto già preparato con Azure Dev Spaces aggiungere chiavi segrete e valore utilizzando *$PlaceHolder* sintassi in *Configurations. develop. Install. set* in `azds.yaml`. Ad esempio:

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
> È possibile immettere i valori del segreto direttamente *$PLACEHOLDER* senza usare $PlaceHolder `azds.yaml`sintassi in. Tuttavia, questo approccio non è consigliato perché `azds.yaml` è archiviato nel controllo del codice sorgente.
     
Creare un `.env` file nella stessa cartella `azds.yaml` di per definire i valori di *$PlaceHolder* . Ad esempio:

```
REDIS_PORT=3333
REDIS_HOST=myredishost
REDIS_KEY=myrediskey
```

> [!IMPORTANT]
> Non è consigliabile archiviare i segreti nel controllo del codice sorgente. Se si usa Git, `.env` aggiungere al `.gitignore` file per evitare di eseguire il commit dei segreti nel controllo del codice sorgente.

Aggiornare o verificare che il servizio faccia riferimento ai segreti come variabili di ambiente. Ad esempio:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Eseguire i servizi aggiornati usando `azds up`.

```console
azds up
```
 
Usare `kubectl` per verificare che i segreti siano stati creati.

```console
kubectl get secret --namespace default -o yaml 
```

## <a name="using-secrets-as-build-arguments"></a>Uso dei segreti come argomenti di compilazione

La sezione precedente ha illustrato come archiviare e usare i segreti da usare in fase di esecuzione del contenitore. È anche possibile usare qualsiasi segreto in fase di compilazione del contenitore, ad esempio una password per un NuGet privato `azds.yaml`, usando.

In `azds.yaml`impostare i segreti per la fase di compilazione in *Configurations. develop. Build. args* usando la `<variable name>: ${secret.<secret name>.<secret key>}` sintassi. Ad esempio:

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
> I nomi e le chiavi dei segreti `.` possono contenere il carattere. Usare `\` per eseguire `.` l'escape quando si passano i segreti come argomenti di compilazione. Ad esempio, per passare un segreto denominato *foo. bar* con la chiave del *token*: `MYTOKEN: ${secret.foo\.bar.token}`. Inoltre, i segreti possono essere valutati con il testo prefisso e suffisso. Ad esempio: `MYURL: eus-${secret.foo\.bar.token}-version1`. Inoltre, i segreti disponibili negli spazi padre e nonni possono essere passati come argomenti di compilazione.

In Dockerfile usare la direttiva *arg* per usare il segreto, quindi usare la stessa variabile in un secondo momento in Dockerfile. Ad esempio:

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
 
