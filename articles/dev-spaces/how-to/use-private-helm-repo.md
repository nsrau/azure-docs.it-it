---
title: Come usare un repository Helm privato in Azure Dev SpacesHow to use a private Helm repository in Azure Dev Spaces
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/22/2019
ms.topic: conceptual
description: Usare un repository Helm privato in uno spazio di sviluppo di Azure.Use a private Helm repository in an Azure Dev Space.
keywords: Docker, Kubernetes, Azure, AKS, servizio contenitore di Azure, contenitori, Helm
manager: gwallace
ms.openlocfilehash: c8f0e463bc78d278d8162f8389664dbb46a83301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240460"
---
# <a name="use-a-private-helm-repository-in-azure-dev-spaces"></a>Usare un repository Helm privato in Azure Dev SpacesUse a private Helm repository in Azure Dev Spaces

[Helm][helm] è un package manager per Kubernetes. Helm utilizza un formato [grafico][helm-chart] per creare il pacchetto delle dipendenze. I grafici Helm sono memorizzati in un repository, che può essere pubblico o privato. Azure Dev Spaces recupera i grafici Helm dai repository pubblici solo quando si esegue l'applicazione. Nei casi in cui il repository Helm è privato o Azure Dev Spaces non può accedervi, è possibile aggiungere un grafico da tale repository direttamente all'applicazione. L'aggiunta del grafico consente direttamente ad Azure Dev Spaces di eseguire l'applicazione senza dover accedere al repository Helm privato.

## <a name="add-the-private-helm-repository-to-your-local-machine"></a>Aggiungere il repository Helm privato al computer locale

Utilizzare [helm repo add][helm-repo-add] e helm [repo update][helm-repo-update] per accedere al repository Helm privato dal computer locale.

```cmd
helm repo add privateRepoName http://example.com/helm/v1/repo --username user --password 5tr0ng_P@ssw0rd!
helm repo update
```

## <a name="add-the-chart-to-your-application"></a>Aggiungere il grafico all'applicazione

Passare alla directory del progetto `azds prep`ed eseguire .

```cmd
azds prep --enable-ingress
```

> [!TIP]
> Il comando `prep` prova a generare [un Dockerfile e un grafico Helm](../how-dev-spaces-works-prep.md#prepare-your-code) per il progetto. Azure Dev Spaces usa questi file per compilare ed eseguire il codice, ma è possibile modificarli se si vuole cambiare il modo in cui il progetto viene compilato ed eseguito.

Creare un file [requirements.yaml][helm-requirements] con il grafico nella directory dei grafici dell'applicazione. Ad esempio, se l'applicazione è denominata *app1*, è necessario creare *charts/app1/requirements.yaml*.

```yaml
dependencies:
    - name: mychart
      version: 0.1.0
      repository:  http://example.com/helm/v1/repo
```

Passare alla directory dei grafici dell'applicazione e usare l'aggiornamento della [dipendenza helm][helm-dependency-update] per aggiornare le dipendenze Helm per l'applicazione e scaricare il grafico dal repository privato.

```cmd
helm dependency update
```

Verificare che una sottodirectory *dei grafici* con un file *tgz* sia stata aggiunta alla directory dei grafici dell'applicazione. Ad esempio, *charts/app1/charts/mychart-0.1.0.tgz*.

Il grafico dal repository Elmo privato è stato scaricato e aggiunto al progetto. Rimuovere il file *requirements.yaml* in modo che Dev Spaces non tenti di aggiornare questa dipendenza.

## <a name="run-your-application"></a>Eseguire l'applicazione

Passare alla directory radice del `azds up` progetto ed eseguire per verificare che l'applicazione venga eseguita correttamente nello spazio di sviluppo.

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
...
Service 'app1' port 'http' is available at http://app1.1234567890abcdef1234.eus.azds.io/
Service 'app1' port 80 (http) is available at http://localhost:54256
...
```

## <a name="next-steps"></a>Passaggi successivi

Scopri di più su [Helm e come funziona][helm].

[helm]: https://docs.helm.sh
[helm-chart]: https://helm.sh/docs/topics/charts/
[helm-dependency-update]: https://helm.sh/docs/topics/charts/#managing-dependencies-with-the-dependencies-field
[helm-repo-add]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-requirements]: https://helm.sh/docs/topics/charts/#chart-dependencies
