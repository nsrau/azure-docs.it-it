---
title: Integrazione git per Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Informazioni su come Azure Machine Learning si integra con un repository git locale. Quando si invia un'esecuzione di training da una directory locale che è un repository git, le informazioni sul repository, il ramo e il commit corrente vengono rilevate come parte dell'esecuzione.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.openlocfilehash: f13773a8e3e78451dfb587e55c40a20d1b4b385c
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324759"
---
# <a name="git-integration-for-azure-machine-learning"></a>Integrazione git per Azure Machine Learning

[Git](https://git-scm.com/) è un sistema di controllo della versione noto che consente di condividere e collaborare con i progetti. 

Azure Machine Learning supporta completamente i repository Git per tenere traccia del lavoro: è possibile clonare i repository direttamente nell'area di lavoro condivisa file system, usare git nella workstation locale o usare git da una pipeline CI/CD.

Quando si invia un processo a Azure Machine Learning, se i file di origine sono archiviati in un repository git locale, le informazioni sul repository vengono registrate come parte del processo di training.

Poiché Azure Machine Learning tiene traccia delle informazioni da un repository git locale, non è associato ad alcun repository centrale specifico. Il repository può essere clonato da GitHub, GitLab, Bitbucket, Azure DevOps o da qualsiasi altro servizio compatibile con git.

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Clonare i repository GIT nel file system dell'area di lavoro
Azure Machine Learning fornisce un file system condiviso per tutti gli utenti nell'area di lavoro.
Per clonare un repository git in questa condivisione file, è consigliabile creare un'istanza di calcolo & aprire un terminale.
Una volta aperto il terminale, è possibile accedere a un client Git completo ed è possibile clonare e usare git usando l'interfaccia della riga di comando di git.

Si consiglia di clonare il repository nella directory degli utenti in modo che altri non effettuino conflitti direttamente nel ramo di lavoro.

È possibile clonare qualsiasi repository git a cui è possibile eseguire l'autenticazione (GitHub, Azure Repos, BitBucket e così via)

Per altre informazioni sulla clonazione, vedere la guida su [come usare l'interfaccia](https://guides.github.com/introduction/git-handbook/)della riga di comando di git.

## <a name="authenticate-your-git-account-with-ssh"></a>Autenticare l'account git con SSH
### <a name="generate-a-new-ssh-key"></a>Genera una nuova chiave SSH
1) [Aprire la finestra del terminale](./how-to-run-jupyter-notebooks.md#terminal) nella scheda Azure Machine Learning notebook.

2) Incollare il testo seguente, sostituendo l'indirizzo di posta elettronica.

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

Viene creata una nuova chiave SSH, usando il messaggio di posta elettronica fornito come etichetta.

```
> Generating public/private rsa key pair.
```

3) Quando viene richiesto di "immettere un file in cui salvare la chiave" premere INVIO. Accetta il percorso del file predefinito.

4) Verificare che il percorso predefinito sia "/Home/azureuser/.ssh" e premere INVIO. In caso contrario, specificare il percorso '/Home/azureuser/.ssh '.

> [!TIP]
> Verificare che la chiave SSH sia salvata in '/Home/azureuser/.ssh '. Questo file viene salvato nell'istanza di calcolo è accessibile solo al proprietario dell'istanza di calcolo

```
> Enter a file in which to save the key (/home/azureuser/.ssh/id_rsa): [Press enter]
```

5) Al prompt dei comandi digitare una passphrase sicura. È consigliabile aggiungere una passphrase alla chiave SSH per una maggiore sicurezza

```
> Enter passphrase (empty for no passphrase): [Type a passphrase]
> Enter same passphrase again: [Type passphrase again]
```

### <a name="add-the-public-key-to-git-account"></a>Aggiungere la chiave pubblica all'account git
1) Nella finestra del terminale copiare il contenuto del file di chiave pubblica. Se la chiave è stata rinominata, sostituire id_rsa. pub con il nome del file di chiave pubblica.

```bash
cat ~/.ssh/id_rsa.pub
```
> [!TIP]
> **Copiare e incollare nel terminale**
> * Windows: `Ctrl-Insert` per copiare e usare `Ctrl-Shift-v` o `Shift-Insert` per incollare.
> * Mac OS: `Cmd-c` per copiare e `Cmd-v` per incollare.
> * FireFox o Internet Explorer potrebbero non supportare correttamente le autorizzazioni per gli appunti.

2) Selezionare e copiare l'output della chiave negli Appunti.

+ [GitHub](https://docs.github.com/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account)

+ [GitLab](https://docs.gitlab.com/ee/ssh/#adding-an-ssh-key-to-your-gitlab-account)

+ [Azure DevOps](/azure/devops/repos/git/use-ssh-keys-to-authenticate?view=azure-devops#step-2--add-the-public-key-to-azure-devops-servicestfs)  Iniziare dal **passaggio 2**.

+ [Bitbucket](https://support.atlassian.com/bitbucket-cloud/docs/set-up-an-ssh-key/#SetupanSSHkey-ssh2). Iniziare dal **passaggio 4**.

### <a name="clone-the-git-repository-with-ssh"></a>Clonare il repository git con SSH

1) Copiare l'URL del clone git SSH dal repository git.

2) Incollare l'URL nel `git clone` comando riportato di seguito per usare l'URL del repository git SSH. Il risultato sarà simile al seguente:

```bash
git clone git@example.com:GitUser/azureml-example.git
Cloning into 'azureml-example'...
```

Viene visualizzata una risposta simile alla seguente:

```bash
The authenticity of host 'example.com (192.30.255.112)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,192.30.255.112' (RSA) to the list of known hosts.
```

SSH può visualizzare l'impronta digitale SSH del server e richiedere di verificarla. È necessario verificare che l'impronta digitale visualizzata corrisponda a una delle impronte digitali nella pagina chiavi pubbliche SSH.

SSH Visualizza questa impronta digitale quando si connette a un host sconosciuto per proteggersi dagli [attacchi man-in-the-Middle](/previous-versions/windows/it-pro/windows-2000-server/cc959354(v=technet.10)). Una volta accettata l'impronta digitale dell'host, SSH non verrà più richiesto se l'impronta digitale non cambia.

3) Quando viene chiesto se si desidera continuare la connessione, digitare `yes` . Git clona il repository e configura l'origine remota per connettersi con SSH per i comandi Git futuri.

## <a name="track-code-that-comes-from-git-repositories"></a>Tenere traccia del codice che deriva da repository git

Quando si invia un'esecuzione di training da Python SDK o Machine Learning CLI, i file necessari per il training del modello vengono caricati nell'area di lavoro. Se il `git` comando è disponibile nell'ambiente di sviluppo, il processo di caricamento lo usa per verificare se i file sono archiviati in un repository git. In tal caso, vengono caricate anche le informazioni del repository Git come parte dell'esecuzione del training. Queste informazioni vengono archiviate nelle proprietà seguenti per l'esecuzione del training:

| Proprietà | Comando git usato per ottenere il valore | Descrizione |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | URI da cui è stato clonato il repository. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | URI da cui è stato clonato il repository. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | Ramo attivo al momento dell'invio dell'esecuzione. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | Ramo attivo al momento dell'invio dell'esecuzione. |
| `azureml.git.commit` | `git rev-parse HEAD` | Hash di commit del codice che è stato inviato per l'esecuzione. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | Hash di commit del codice che è stato inviato per l'esecuzione. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`, se il ramo/commit è modificato; in caso contrario, `false` . |

Queste informazioni vengono inviate per le esecuzioni che usano un estimatore, una pipeline di Machine Learning o un'esecuzione di script.

Se i file di training non si trovano in un repository git nell'ambiente di sviluppo oppure il `git` comando non è disponibile, non viene tenuta traccia delle informazioni relative a git.

> [!TIP]
> Per verificare se il comando git è disponibile nell'ambiente di sviluppo, aprire una sessione della shell, un prompt dei comandi, PowerShell o un'altra interfaccia della riga di comando e digitare il comando seguente:
>
> ```
> git --version
> ```
>
> Se installato e nel percorso, si riceve una risposta simile a `git version 2.4.1` . Per ulteriori informazioni sull'installazione di git nell'ambiente di sviluppo, vedere il [sito Web git](https://git-scm.com/).

## <a name="view-the-logged-information"></a>Visualizzare le informazioni registrate

Le informazioni git vengono archiviate nelle proprietà di un'esecuzione di training. È possibile visualizzare queste informazioni usando il portale di Azure, Python SDK e l'interfaccia della riga di comando. 

### <a name="azure-portal"></a>Portale di Azure

1. Dal [portale di studio](https://ml.azure.com)selezionare l'area di lavoro.
1. Selezionare __esperimenti__ , quindi selezionare uno degli esperimenti.
1. Selezionare una delle esecuzioni dalla colonna __numero di esecuzione__ .
1. Selezionare __output + log__ , quindi espandere i __registri__ e le voci __azureml__ . Selezionare il collegamento che inizia con __### \_ Azure__.

Le informazioni registrate contengono testo simile al seguente JSON:

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>Python SDK

Dopo aver inviato un'esecuzione di training, viene restituito un oggetto [Run](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) . L' `properties` attributo di questo oggetto contiene le informazioni git registrate. Il codice seguente, ad esempio, recupera l'hash di commit:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

Il `az ml run` comando CLI può essere usato per recuperare le proprietà da un'esecuzione. Ad esempio, il comando seguente restituisce le proprietà dell'ultima esecuzione nell'esperimento denominata `train-on-amlcompute` :

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Per ulteriori informazioni, vedere la documentazione di riferimento [AZ ml Run](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest) .

## <a name="next-steps"></a>Passaggi successivi

* [Usare destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md)