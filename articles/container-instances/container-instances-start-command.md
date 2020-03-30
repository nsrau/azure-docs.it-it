---
title: Eseguire l'override del punto di ingresso nell'istanza del contenitoreOverride entrypoint in container instance
description: Impostare una riga di comando per eseguire l'override del punto di ingresso in un'immagine del contenitore quando si distribuisce un'istanza del contenitore di AzureSet a command line to override the entrypoint in a container image when you deploy an Azure container instance
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: d9554603f78a07fa44af51d8f39a91e1b3c39f70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247124"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Impostare la riga di comando in un'istanza del contenitore per eseguire l'override dell'operazione della riga di comando predefinitaSet the command line in a container instance to override the default command line operation

Quando si crea un'istanza del contenitore, specificare facoltativamente un comando per eseguire l'override dell'istruzione della riga di comando predefinita inserita nell'immagine del contenitore. Questo comportamento è `--entrypoint` simile all'argomento `docker run`della riga di comando di .

Come l'impostazione delle variabili di [ambiente](container-instances-environment-variables.md) per le istanze del contenitore, la specifica di una riga di comando iniziale è utile per i processi batch in cui è necessario preparare ogni contenitore in modo dinamico con la configurazione specifica dell'attività.

## <a name="command-line-guidelines"></a>Linee guida della riga di comando

* Per impostazione predefinita, la riga di comando specifica un *singolo processo che viene avviato senza una shell* nel contenitore. Ad esempio, la riga di comando potrebbe eseguire uno script Python o un file eseguibile. Il processo può specificare parametri o argomenti aggiuntivi.

* Per eseguire più comandi, iniziare la riga di comando impostando un ambiente shell supportato nel sistema operativo contenitore. Esempi:

  |Sistema operativo  |Shell predefinita  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpino     |   `/bin/sh`      |
  |WINDOWS     |    `cmd`     |

  Seguire le convenzioni della shell per combinare più comandi da eseguire in sequenza.

* A seconda della configurazione del contenitore, potrebbe essere necessario impostare un percorso completo per l'eseguibile della riga di comando o gli argomenti.

* Impostare un criterio di [riavvio](container-instances-restart-policy.md) appropriato per l'istanza del contenitore, a seconda che la riga di comando specifichi un'attività a esecuzione prolungata o una sola volta. Ad esempio, un `Never` criterio `OnFailure` di riavvio o è consigliato per un'attività run-once. 

* Per informazioni sul punto di ingresso predefinito impostato in un'immagine contenitore, utilizzare il comando [docker image inspect.](https://docs.docker.com/engine/reference/commandline/image_inspect/)

## <a name="command-line-syntax"></a>Sintassi della riga di comando

La sintassi della riga di comando varia a seconda dell'API di Azure o dello strumento usato per creare le istanze. Se si specifica un ambiente shell, osservare anche le convenzioni di sintassi dei comandi della shell.

* [comando az container create:][az-container-create] passa `--command-line` una stringa con il parametro. Esempio: `--command-line "python myscript.py arg1 arg2"`).

* [Nuovo-AzureRmContainerGroup][new-azurermcontainergroup] Cmdlet di Azure PowerShell: `-Command` passare una stringa con il parametro. Esempio: `-Command "echo hello"`.

* Portale di Azure: nella proprietà **Command override** della configurazione del contenitore, fornire un elenco separato da virgole di stringhe, senza virgolette. Esempio: `python, myscript.py, arg1, arg2`). 

* Modello di Resource Manager o file YAML o uno degli SDK di Azure: specificare la proprietà della riga di comando come matrice di stringhe. Esempio: la `["python", "myscript.py", "arg1", "arg2"]` matrice JSON in un modello di Resource Manager.Example: the JSON array in a Resource Manager template. 

  Se si ha familiarità con la sintassi [Dockerfile,](https://docs.docker.com/engine/reference/builder/) questo formato è simile alla forma *exec* dell'istruzione CMD.

### <a name="examples"></a>Esempi

|    |  Interfaccia della riga di comando di Azure   | Portale | Modello | 
| ---- | ---- | --- | --- |
| Comando singolo | `--command-line "python myscript.py arg1 arg2"` | **Comando override :**`python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| Comandi multipli | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Comando override :**`/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Esempio di interfaccia della riga di comando di Azure

Ad esempio, modificare il comportamento dell'immagine contenitore [microsoft/aci-wordcount,][aci-wordcount] che analizza il testo *nell'Amleto* di Shakespeare per trovare le parole più frequenti. Anziché analizzare *Amleto*, è possibile impostare una riga di comando che punti a un'origine di testo diversa.

Per visualizzare l'output del contenitore [microsoft/aci-wordcount][aci-wordcount] quando analizza il testo predefinito, eseguirlo con il seguente comando [az container create.][az-container-create] Non è stata specificata alcuna riga di comando di avvio, pertanto viene eseguito il comando contenitore predefinito. A scopo illustrativo, in questo esempio vengono impostate [le variabili](container-instances-environment-variables.md) di ambiente per trovare le prime 3 parole lunghe di almeno cinque lettere:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Una volta che lo stato del contenitore viene visualizzato come *Terminated* (usare [az container show][az-container-show] to check state), visualizzare il log con i log del [contenitore az][az-container-logs] per visualizzare l'output.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Output:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Impostare ora un secondo contenitore di esempio per analizzare testo diverso specificando una riga di comando diversa. Lo script Python eseguito dal contenitore, *wordcount.py*, accetta un URL come argomento ed elabora il contenuto della pagina anziché quello predefinito.

Ad esempio, per determinare le prime 3 parole che sono lunghe almeno cinque lettere in *Romeo e Giulietta*:

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

Gli scenari basati su attività, ad esempio l'elaborazione batch di un set di dati di grandi dimensioni con diversi contenitori, possono trarre vantaggio dalle righe di comando personalizzate in fase di esecuzione. Per ulteriori informazioni sull'esecuzione di contenitori basati su attività, vedere [Eseguire attività in contenitori con criteri](container-instances-restart-policy.md)di riavvio .

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
