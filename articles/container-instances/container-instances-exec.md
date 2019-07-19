---
title: Eseguire i comandi in contenitori in esecuzione in Istanze di Azure Container
description: Informazioni su come eseguire un comando in un contenitore attualmente in esecuzione in Istanze di Azure Container
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 03/30/2018
ms.author: danlep
ms.openlocfilehash: a8583cf605891631a2bce6914b24525aebd59ea0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325989"
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>Eseguire un comando in un'istanza di contenitore di Azure in esecuzione

Istanze di Azure Container supporta l'esecuzione di un comando in un contenitore in esecuzione. Eseguire un comando in un contenitore già avviato è particolarmente utile durante lo sviluppo e la risoluzione dei problemi di un'applicazione. L'uso più comune di questa funzionalità consiste nell'avviare una shell interattiva in modo che sia possibile eseguire il debug di problemi in un contenitore in esecuzione.

## <a name="run-a-command-with-azure-cli"></a>Eseguire un comando con l'interfaccia della riga di comando di Azure

Eseguire un comando in un contenitore in esecuzione con [AZ container Exec][az-container-exec] in the [Azure CLI][azure-cli]:

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

Ad esempio, per avviare una shell Bash in un contenitore Nginx:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

Nell'output di esempio seguente, la shell Bash viene avviata in un contenitore Linux in esecuzione, fornendo un terminale in cui viene eseguito `ls`:

```console
$ az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

In questo esempio, il prompt dei comandi viene avviato in un contenitore Nanoserver in esecuzione:

```console
$ az container exec --resource-group myResourceGroup --name myiis --exec-command "cmd.exe"
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\>dir
 Volume in drive C has no label.
 Volume Serial Number is 76E0-C852

 Directory of C:\

03/23/2018  09:13 PM    <DIR>          inetpub
11/20/2016  11:32 AM             1,894 License.txt
03/23/2018  09:13 PM    <DIR>          Program Files
07/16/2016  12:09 PM    <DIR>          Program Files (x86)
03/13/2018  08:50 PM           171,616 ServiceMonitor.exe
03/23/2018  09:13 PM    <DIR>          Users
03/23/2018  09:12 PM    <DIR>          var
03/23/2018  09:22 PM    <DIR>          Windows
               2 File(s)        173,510 bytes
               6 Dir(s)  21,171,609,600 bytes free

C:\>exit
Bye.
```

## <a name="multi-container-groups"></a>Gruppi di più contenitori

Se il [gruppo di contenitori](container-instances-container-groups.md) dispone di più contenitori, ad esempio un contenitore di applicazione e un sidecar di registrazione, specificare il nome del contenitore in cui eseguire il comando con `--container-name`.

Ad esempio, nel gruppo di contenitori *mynginx* sono presenti due contenitori *nginx-app* e *logger*. Per avviare una shell nel contenitore *nginx-app*:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>Restrizioni

Istanze di contenitore di Azure supporta attualmente l'avvio di un singolo processo con [AZ container Exec][az-container-exec]e non è possibile passare gli argomenti del comando. Ad esempio, non è possibile concatenare i comandi, come in `sh -c "echo FOO && echo BAR"`, o eseguire `echo FOO`.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su altri strumenti di risoluzione dei problemi e problemi di distribuzione comuni in [Risolvere i problemi di distribuzione in Istanze di Azure Container](container-instances-troubleshooting.md).

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[azure-cli]: /cli/azure