---
title: Sostituisci EntryPoint nell'istanza del contenitore
description: Impostare una riga di comando per eseguire l'override del EntryPoint in un'immagine del contenitore quando si distribuisce un'istanza di contenitore di Azure
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: d9554603f78a07fa44af51d8f39a91e1b3c39f70
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533413"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Impostare la riga di comando in un'istanza di contenitore per sostituire l'operazione della riga di comando predefinita

Quando si crea un'istanza del contenitore, è possibile specificare facoltativamente un comando per eseguire l'override dell'istruzione della riga di comando predefinita preparata nell'immagine del contenitore. Questo comportamento è simile all'argomento della riga di comando `--entrypoint` per `docker run`.

Come l'impostazione delle [variabili di ambiente](container-instances-environment-variables.md) per le istanze di contenitore, specificare una riga di comando iniziale è utile per i processi batch in cui è necessario preparare ogni contenitore in modo dinamico con la configurazione specifica dell'attività.

## <a name="command-line-guidelines"></a>Linee guida della riga di comando

* Per impostazione predefinita, la riga di comando specifica un *singolo processo che inizia senza una shell* nel contenitore. Ad esempio, la riga di comando può eseguire uno script Python o un file eseguibile. Il processo può specificare parametri o argomenti aggiuntivi.

* Per eseguire più comandi, iniziare la riga di comando impostando un ambiente shell supportato nel sistema operativo del contenitore. Esempi:

  |Sistema operativo  |Shell predefinita  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpine     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  Seguire le convenzioni della Shell per combinare più comandi da eseguire in sequenza.

* A seconda della configurazione del contenitore, potrebbe essere necessario impostare un percorso completo per l'eseguibile della riga di comando o per gli argomenti.

* Impostare un [criterio di riavvio](container-instances-restart-policy.md) appropriato per l'istanza del contenitore, a seconda che la riga di comando specifichi un'attività a esecuzione prolungata o un'attività Run-Once. Ad esempio, è consigliabile un criterio di riavvio di `Never` o `OnFailure` per un'attività Esegui una sola volta. 

* Se sono necessarie informazioni sul set di EntryPoint predefinito in un'immagine del contenitore, usare il comando di [controllo dell'immagine Docker](https://docs.docker.com/engine/reference/commandline/image_inspect/) .

## <a name="command-line-syntax"></a>Sintassi della riga di comando

La sintassi della riga di comando varia a seconda dell'API o dello strumento di Azure usato per creare le istanze. Se si specifica un ambiente della shell, osservare anche le convenzioni della sintassi del comando della shell.

* comando [AZ container create][az-container-create] : passa una stringa con il parametro `--command-line`. Esempio: `--command-line "python myscript.py arg1 arg2"`).

* [New-AzureRmContainerGroup][new-azurermcontainergroup] Cmdlet Azure PowerShell: passare una stringa con il parametro `-Command`. Esempio: `-Command "echo hello"`.

* Portale di Azure: nella proprietà di **override del comando** della configurazione del contenitore specificare un elenco di stringhe delimitato da virgole, senza virgolette. Esempio: `python, myscript.py, arg1, arg2`). 

* Gestione risorse modello o file YAML o uno degli SDK di Azure: specificare la proprietà della riga di comando come una matrice di stringhe. Esempio: la matrice JSON `["python", "myscript.py", "arg1", "arg2"]` in un modello di Gestione risorse. 

  Se si ha familiarità con la sintassi di [Dockerfile](https://docs.docker.com/engine/reference/builder/) , questo formato è simile al formato *Exec* dell'istruzione cmd.

### <a name="examples"></a>esempi

|    |  Interfaccia della riga di comando di Azure   | di Microsoft Azure | Modello | 
| ---- | ---- | --- | --- |
| Singolo comando | `--command-line "python myscript.py arg1 arg2"` | **Override del comando**: `python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| Più comandi | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Override del comando**: `/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Esempio di interfaccia della riga di comando di Azure

Ad esempio, modificare il comportamento dell'immagine del contenitore [Microsoft/ACI-WordCount][aci-wordcount] , che analizza il testo nel *villaggio* di Shakespeare per trovare le parole che si verificano più di frequente. Anziché analizzare *Hamlet*, è possibile impostare una riga di comando che punta a un'origine di testo diversa.

Per visualizzare l'output del contenitore [Microsoft/ACI-WordCount][aci-wordcount] quando analizza il testo predefinito, eseguirlo con il comando [AZ container create][az-container-create] seguente. Non viene specificata alcuna riga di comando di avvio, pertanto viene eseguito il comando contenitore predefinito. A scopo illustrativo, questo esempio imposta le [variabili di ambiente](container-instances-environment-variables.md) per trovare le prime 3 parole che hanno una lunghezza di almeno cinque lettere:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Quando lo stato del contenitore viene visualizzato come *terminato* (usare [AZ container Show][az-container-show] per verificare lo stato), visualizzare il log con [AZ container logs][az-container-logs] per visualizzare l'output.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Output:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Configurare ora un secondo contenitore di esempio per analizzare il testo diverso specificando una riga di comando diversa. Lo script Python eseguito dal contenitore, *WordCount.py*, accetta un URL come argomento e elabora il contenuto della pagina anziché il valore predefinito.

Ad esempio, per determinare le prime 3 parole che hanno una lunghezza di almeno cinque lettere in *Romeo e Giulietta*:

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

Gli scenari basati su attività, ad esempio l'elaborazione batch di un set di dati di grandi dimensioni con diversi contenitori, possono trarre vantaggio dalle righe di comando personalizzate in fase di esecuzione. Per altre informazioni sull'esecuzione di contenitori basati su attività, vedere [eseguire attività in contenitori con i criteri di riavvio](container-instances-restart-policy.md).

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
