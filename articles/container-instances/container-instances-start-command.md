---
title: Usare una riga di comando inizia in istanze di contenitore di Azure
description: Sostituire il punto di ingresso configurato in un'immagine del contenitore quando si distribuisce un'istanza di contenitore di Azure
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: da94a4c79694f511d41e5c8dda8c786fc7049726
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64569645"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Impostare la riga di comando in un'istanza di contenitore per sostituire l'operazione della riga di comando predefinita

Quando si crea un'istanza di contenitore, se lo si desidera specificare un comando per eseguire l'override dell'istruzione di riga di comando predefinita regolano l'immagine del contenitore. Questo comportamento è simile al `--entrypoint` argomento della riga di comando per `docker run`.

Quali l'impostazione [variabili di ambiente](container-instances-environment-variables.md) per istanze di contenitore, specificando un'avvio riga di comando è utile per i processi di batch in cui è necessario preparare ogni contenitore in modo dinamico con la configurazione specifica dell'attività.

## <a name="command-line-guidelines"></a>Linee guida per la riga di comando

* Per impostazione predefinita, la riga di comando specifica un *singolo processo di avvio senza una shell* nel contenitore. Ad esempio, la riga di comando potrebbe eseguire uno script di Python o un file eseguibile. 

* Per eseguire più comandi, avviare la riga di comando tramite l'impostazione di un ambiente della shell che è supportato nel sistema operativo contenitore. Esempi:

  |Sistema operativo  |Shell predefinita  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpine     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  Seguire le convenzioni della shell per combinare più comandi da eseguire in sequenza.

* A seconda della configurazione del contenitore, si potrebbe essere necessario impostare un percorso completo al file eseguibile da riga di comando o argomenti.

* Impostare un'apposita [criterio di riavvio](container-instances-restart-policy.md) per l'istanza di contenitore, a seconda del fatto che la riga di comando specifica un'attività a esecuzione prolungata o un'attività di eseguire una sola volta. Ad esempio, un criterio di riavvio del `Never` o `OnFailure` è consigliato per un'attività di eseguire una sola volta. 

* Se sono necessarie informazioni sul punto di ingresso predefinito impostato in un'immagine del contenitore, usare il [immagine docker ispezionare](https://docs.docker.com/engine/reference/commandline/image_inspect/) comando.

## <a name="command-line-syntax"></a>Sintassi della riga di comando

La sintassi della riga di comando varia a seconda dell'API di Azure o lo strumento utilizzato per creare le istanze. Se si specifica un ambiente della shell, osservare inoltre le convenzioni della sintassi di comando della shell.

* [creare il contenitore di AZ] [ az-container-create] comando: Passare una stringa con il `--command-line` parametro. Esempio: `--command-line "python myscript.py arg1 arg2"`).

* [Nuovo-AzureRmContainerGroup] [ new-azurermcontainergroup] cmdlet Azure PowerShell: Passare una stringa con il `-Command` parametro. Esempio: `-Command "echo hello"`.

* Portale di Azure: Nel **sostituzione comando** la proprietà della configurazione del contenitore, specificare un elenco delimitato da virgole di stringhe, senza virgolette. Esempio: `python, myscript.py, arg1, arg2`). 

* Modello di Resource Manager o file YAML o uno degli SDK di Azure: Specificare la proprietà della riga di comando come una matrice di stringhe. Esempio: matrice JSON `["python", "myscript.py", "arg1", "arg2"]` in un modello di Resource Manager. 

  Se si ha familiarità con [Dockerfile](https://docs.docker.com/engine/reference/builder/) sintassi, questo formato è simile al *exec* forma dell'istruzione di CMD.

### <a name="examples"></a>Esempi

|    |  Interfaccia della riga di comando di Azure   | Portale | Modello | 
| ---- | ---- | --- | --- |
| Singolo comando | `--command-line "python myscript.py arg1 arg2"` | **Comando override**: `python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| Più comandi | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Comando override**: `/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Esempio di interfaccia della riga di comando di Azure

Ad esempio, modificare il comportamento dei [microsoft/aci-wordcount] [ aci-wordcount] immagine del contenitore, che analizza il testo in Shakespeare *Amleto* per trovare più di frequente parole in corso. Anziché analizzare *Amleto*, è possibile impostare una riga di comando che punta a un'origine di un testo diverso.

Per visualizzare l'output del [microsoft/aci-wordcount] [ aci-wordcount] contenitore quando analizza il testo predefinito, eseguirlo con il codice seguente [crea contenitore di az] [ az-container-create] comando. Non viene specificata alcuna riga di comando di avvio, pertanto viene eseguito il comando contenitore predefinito. A scopo illustrativo, questo esempio viene impostato [variabili di ambiente](container-instances-environment-variables.md) per trovare le prime 3 parole che sono almeno cinque lettere lungo:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Quando lo stato del contenitore viene mostrato come *Terminated* (usare [show di contenitore di az] [ az-container-show] per controllare lo stato), visualizzare il log con [i log dei contenitori di az] [ az-container-logs] per visualizzare l'output.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Output:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Impostare ora un secondo contenitore di esempio per analizzare un testo diverso, specificando una riga di comando diversa. Lo script Python eseguito dal contenitore, *wordcount.py*, accetta un URL come argomento ed elabora contenuto di questa pagina anziché il valore predefinito.

Ad esempio, per determinare la parte superiore 3 parole che sono almeno cinque lettere in lungo *Romeo e Giulietta*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Di nuovo, quando il contenitore mostra lo stato *Terminato*, visualizzare l'output tramite la visualizzazione dei log del contenitore:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Output:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Passaggi successivi

Scenari basati su attività, ad esempio un set di dati di grandi dimensioni con più contenitori, elaborazione batch possono trarre vantaggio dalle righe di comando personalizzate in fase di esecuzione. Per altre informazioni sull'esecuzione di contenitori basati su attività, vedere [eseguire le attività incluse in contenitori con criteri di riavvio](container-instances-restart-policy.md).

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
