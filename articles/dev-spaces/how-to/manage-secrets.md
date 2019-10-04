---
title: Come gestire i segreti quando si lavora in uno spazio Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: conceptual
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: Docker, Kubernetes, Azure, servizio Azure Kubernetes, servizio Azure Container, contenitori
ms.openlocfilehash: 900529d54a26729d9d0fb949d9217d5e2d618254
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66515301"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Come gestire i segreti quando si lavora in uno spazio Azure Dev Spaces

I servizi potrebbero richiedere alcune password, stringhe di connessione e altri segreti, ad esempio per i database o altri servizi sicuri di Azure. Impostando i valori di questi segreti nei file di configurazione, è possibile renderli disponibili nel codice come variabili di ambiente,  che devono essere gestite con attenzione per evitare di compromettere la sicurezza dei segreti.

Spazi di sviluppo Azure offre due opzioni consigliate, ottimizzate per l'archiviazione dei segreti in grafici Helm generati dal client Azure Dev spazi tooling: nel file values.dev.yaml e inline direttamente in azds.yaml. Non è consigliabile archiviare i segreti in values.yaml. Di fuori i due approcci per Helm i grafici generati dal client di strumenti definito in questo articolo, se si crea un grafico Helm, è possibile usare il grafico Helm direttamente per gestire e archiviare i segreti.

## <a name="method-1-valuesdevyaml"></a>Metodo 1: values.dev.yaml
1. Aprire Visual Studio Code con il progetto abilitato per Azure Dev Spaces.
2. Aggiungere un file denominato _values.dev.yaml_ nella stessa cartella esistente _azds.yaml_ e definire la chiave privata e i valori, come nell'esempio seguente:

    ```yaml
    secrets:
      redis:
        port: "6380"
        host: "contosodevredis.redis.cache.windows.net"
        key: "secretkeyhere"
    ```
     
3. _azds.yaml_ fa già riferimento la _values.dev.yaml_ file se esiste. Se si preferisce un nome file diverso, aggiornare la sezione install.values:

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
 
 
## <a name="method-2-inline-directly-in-azdsyaml"></a>Metodo 2: Inline direttamente in azds.yaml
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
     
2.  Creare un file con estensione _env_ nella stessa cartella di _azds.yaml_. Immettere i segreti usando una chiave standard = notazione del valore. Non eseguire il commit del file con estensione _env_ al controllo del codice sorgente (per ometterlo dal controllo del codice sorgente in sistemi di controllo della versione basati su git, aggiungerlo al file con estensione _gitignore_). L'esempio seguente mostra un file con estensione _env_:

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

## <a name="next-steps"></a>Passaggi successivi

Con questi metodi, è ora possibile connettersi in modo sicuro a un database, una cache di Azure per Redis o accedere ai servizi sicuri di Azure.
 
