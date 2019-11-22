---
title: Come usare un repository Helm privato in Azure Dev Spaces
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/22/2019
ms.topic: conceptual
description: Usare un repository Helm privato in uno spazio di sviluppo di Azure.
keywords: Docker, Kubernetes, Azure, AKS, servizio contenitore di Azure, contenitori, Helm
manager: gwallace
ms.openlocfilehash: f212df74ab8102391e4170ccef6b0c3b1129b046
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279927"
---
# <a name="use-a-private-helm-repository-in-azure-dev-spaces"></a>Usare un repository Helm privato in Azure Dev Spaces

[Helm][helm] è una gestione pacchetti per Kuberentes. Helm usa un formato [grafico][helm-chart] per creare il pacchetto delle dipendenze. I grafici Helm vengono archiviati in un repository, che può essere pubblico o privato. Azure Dev Spaces recupera solo i grafici Helm da repository pubblici durante l'esecuzione dell'applicazione. Nei casi in cui il repository Helm è privato o Azure Dev Spaces non può accedervi, è possibile aggiungere un grafico da tale repository direttamente all'applicazione. L'aggiunta del grafico consente Azure Dev Spaces di eseguire l'applicazione senza dover accedere al repository Helm privato.

## <a name="add-the-private-helm-repository-to-your-local-machine"></a>Aggiungere il repository Helm privato al computer locale

Usare [il repository Helm e][helm-repo-add] l'aggiornamento del repository [Helm][helm-repo-update] per accedere al repository Helm privato dal computer locale.

```cmd
helm repo add privateRepoName http://example.com/helm/v1/repo --username user --password 5tr0ng_P@ssw0rd!
helm repo update
```

## <a name="add-the-chart-to-your-application"></a>Aggiungere il grafico all'applicazione

Passare alla directory del progetto ed eseguire `azds prep`.

```cmd
azds prep --public
```

Creare un file [requirements. YAML][helm-requirements] con il grafico nella directory del grafico dell'applicazione. Ad esempio, se l'applicazione è denominata *App1*, si creeranno *Charts/App1/requirements. YAML*.

```yaml
dependencies:
    - name: mychart
      version: 0.1.0
      repository:  http://example.com/helm/v1/repo
```

Passare alla directory del grafico dell'applicazione e usare l' [aggiornamento delle dipendenze Helm][helm-dependency-update] per aggiornare le dipendenze Helm per l'applicazione e scaricare il grafico dal repository privato.

```cmd
helm dependency update
```

Verificare che la sottodirectory *grafici* con un file *tgz* sia stata aggiunta alla directory del grafico dell'applicazione. Ad esempio, *Charts/App1/Charts/myChart-0.1.0. tgz*.

Il grafico del repository Helm privato è stato scaricato e aggiunto al progetto. Rimuovere il file *requirements. YAML* in modo che gli spazi di sviluppo non tenti di aggiornare questa dipendenza.

## <a name="run-your-application"></a>Eseguire l'applicazione

Passare alla directory radice del progetto ed eseguire `azds up` per verificare che l'applicazione venga eseguita correttamente nello spazio di sviluppo.

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

Scopri di più su [Helm e sul suo funzionamento][helm].

[helm]: https://docs.helm.sh
[helm-chart]: https://helm.sh/docs/topics/charts/
[helm-dependency-update]: https://v2.helm.sh/docs/helm/#helm-dependency-update
[helm-repo-add]: https://v2.helm.sh/docs/helm/#helm-repo-add
[helm-repo-update]: https://v2.helm.sh/docs/helm/#helm-repo-update
[helm-requirements]: https://helm.sh/docs/topics/charts/#chart-dependencies