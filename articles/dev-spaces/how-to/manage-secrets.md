---
title: Come gestire i segreti quando si lavora in uno spazio Azure Dev Spaces
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: Docker, Kubernetes, Azure, servizio Azure Kubernetes, servizio Azure Container, contenitori
ms.openlocfilehash: b184f72dfbbfe093443ab8a9b79bafbece3a3d51
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790181"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Come gestire i segreti quando si lavora in uno spazio Azure Dev Spaces

I servizi potrebbero richiedere alcune password, stringhe di connessione e altri segreti, ad esempio per i database o altri servizi sicuri di Azure. Impostando i valori di questi segreti nei file di configurazione, è possibile renderli disponibili nel codice come variabili di ambiente,  che devono essere gestite con attenzione per evitare di compromettere la sicurezza dei segreti.

Azure Dev Spaces offre due opzioni semplificate e consigliate per l'archiviazione dei segreti nei grafici Helm generati dagli strumenti client Azure Dev Spaces: nel file di `values.dev.yaml` e direttamente in `azds.yaml`. Non è consigliabile archiviare i segreti in `values.yaml`. Al di fuori dei due approcci per i grafici Helm generati dagli strumenti client definiti in questo articolo, se si crea un grafico Helm personalizzato, è possibile usare direttamente il grafico Helm per gestire e archiviare i segreti.

## <a name="method-1-valuesdevyaml"></a>Metodo 1: values.dev.yaml
1. Aprire Visual Studio Code con il progetto abilitato per Azure Dev Spaces.
2. Aggiungere un file denominato _values. dev. YAML_ nella stessa cartella del file _azds. YAML_ esistente e definire i valori e la chiave privata, come nell'esempio seguente:

    ```yaml
    secrets:
      redis:
        port: "6380"
        host: "contosodevredis.redis.cache.windows.net"
        key: "secretkeyhere"
    ```
     
3. _azds. YAML_ fa già riferimento al file _values. dev. YAML_ , se esistente. Se si preferisce un nome file diverso, aggiornare la sezione install. Values:

    ```yaml
    install:
      values:
      - values.dev.yaml?
      - secrets.dev.yaml?
    ```
 
4. Modificare il codice di servizio in modo che faccia riferimento a questi segreti come variabili di ambiente, come nell'esempio seguente:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
    
5. Aggiornare i servizi in esecuzione nel cluster con queste modifiche. Nella riga di comando eseguire il comando:

    ```
    azds up
    ```
 
6. (Facoltativo) Dalla riga di comando verificare che siano stati creati questi segreti:

      ```
      kubectl get secret --namespace default -o yaml 
      ```

7. Assicurarsi di aggiungere _values.dev.yaml_ al file con estensione _gitignore_ per evitare di eseguire il commit dei segreti nel controllo del codice sorgente.
 
 
## <a name="method-2-azdsyaml"></a>Metodo 2: azds. YAML
1.  In _azds.yaml_ impostare i segreti nella sezione yaml configurations/develop/install. Anche se è possibile immettere i valori dei segreti direttamente in questo punto, non è consigliabile perché _azds.yaml_ è archiviato nel controllo del codice sorgente. Al contrario, aggiungere i segnaposto usando la sintassi "$PLACEHOLDER".

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
     
2.  Creare un file con estensione _env_ nella stessa cartella di _azds.yaml_. Immettere i segreti usando una chiave standard = notazione del valore. Non eseguire il commit del file con estensione _env_ al controllo del codice sorgente Per omettere il controllo del codice sorgente nei sistemi di controllo della versione basati su git, aggiungerlo al file con _estensione gitignore_ . Nell'esempio seguente viene illustrato un file con _estensione ENV_ :

    ```
    REDIS_PORT=3333
    REDIS_HOST=myredishost
    REDIS_KEY=myrediskey
    ```
2.  Modificare il codice sorgente del servizio in modo che faccia riferimento a questi segreti nel codice, come nell'esempio seguente:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
 
3.  Aggiornare i servizi in esecuzione nel cluster con queste modifiche. Nella riga di comando eseguire il comando:

    ```
    azds up
    ```

4.  (Facoltativo) Visualizzare i segreti da kubectl:

    ```
    kubectl get secret --namespace default -o yaml
    ```

## <a name="passing-secrets-as-build-arguments"></a>Passaggio di segreti come argomenti di compilazione

Nelle sezioni precedenti è stato illustrato come passare i segreti da usare in fase di esecuzione del contenitore. È anche possibile passare un segreto in fase di compilazione del contenitore, ad esempio una password per un NuGet privato, usando `azds.yaml`.

In `azds.yaml`impostare i segreti per la fase di compilazione in *Configurations. develop. Build. args* usando la sintassi del `<variable name>: ${secret.<secret name>.<secret key>}`. ad esempio:

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
> I nomi e le chiavi dei segreti possono contenere il carattere `.`. Usare `\` per eseguire l'escape `.` quando si passano i segreti come argomenti di compilazione. Ad esempio, per passare un segreto denominato *foo. bar* con la chiave del *token*: `MYTOKEN: ${secret.foo\.bar.token}`. Inoltre, i segreti possono essere valutati con il testo prefisso e suffisso. Ad esempio `MYURL: eus-${secret.foo\.bar.token}-version1`. Inoltre, i segreti disponibili negli spazi padre e nonni possono essere passati come argomenti di compilazione.

In Dockerfile usare la direttiva *arg* per usare il segreto, quindi usare la stessa variabile in un secondo momento in Dockerfile. ad esempio:

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
 
