---
title: Rendere persistenti i file per Bash in Azure Cloud Shell | Microsoft Docs
description: Procedura dettagliata su come Bash in Azure Cloud Shell rende persistenti i file.
services: azure
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: juluk
ms.openlocfilehash: a04342f77126afe1e0a2707f1d213e0a8eb5825d
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
[!include [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-bash-in-cloud-shell-storage-works"></a>Come funziona l'archiviazione di Bash in Cloud Shell 
Bash in Cloud Shell rende persistenti i file tramite entrambi i metodi seguenti: 
* Creazione di un'immagine del disco della directory `$Home` per rendere persistenti tutti i contenuti della directory. L'immagine del disco viene salvata nella condivisione file specificata come `acc_<User>.img` in `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img` e le modifiche vengono automaticamente sincronizzate. 
* Montaggio della condivisione file specificata come `clouddrive` nella directory `$Home` per l'interazione diretta con la condivisione file. Viene eseguito il mapping di `/Home/<User>/clouddrive` a `fileshare.storage.windows.net/fileshare`.
 
> [!NOTE]
> Tutti i file della directory `$Home`, come le chiavi SSH, vengono mantenuti nell'immagine del disco utente archiviata nella condivisione file montata. Applicare le procedure consigliate quando si rendono persistenti le informazioni nella directory `$Home` e nella condivisione file montata.

## <a name="use-the-clouddrive-command"></a>Usare il comando `clouddrive`
Con Bash in Cloud Shell è possibile eseguire un comando denominato `clouddrive`, che consente di aggiornare manualmente la condivisione file montata in Cloud Shell.
![Esecuzione del comando "clouddrive"](media/persisting-shell-storage/clouddrive-h.png)

## <a name="mount-a-new-clouddrive"></a>Montare una nuova clouddrive

### <a name="prerequisites-for-manual-mounting"></a>Prerequisiti per il montaggio manuale
È possibile aggiornare la condivisione file associata a Cloud Shell usando il comando `clouddrive mount`.

Se si monta una condivisione file esistente, gli account di archiviazione devono avere le caratteristiche seguenti:
* Avere un account di archiviazione ridondante a livello locale o globale per supportare le condivisioni file.
* Trovarsi nell'area assegnata. Durante l'onboarding, l'area a cui si è assegnati viene elencata nel nome del gruppo di risorse `cloud-shell-storage-<region>`.

### <a name="the-clouddrive-mount-command"></a>Il comando di montaggio clouddrive

> [!NOTE]
> Se si sta montando una nuova condivisione file, una nuova immagine utente viene creata per la directory `$Home`. L’immagine `$Home` precedente viene mantenuta nella condivisione file precedente.

Eseguire il comando `clouddrive mount` con i parametri seguenti:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Per visualizzare altri dettagli, eseguire `clouddrive mount -h`, come illustrato di seguito:

![Esecuzione del comando `clouddrive mount`](media/persisting-shell-storage/mount-h.png)

## <a name="unmount-clouddrive"></a>Smontare l'unità clouddrive
È possibile smontare in qualsiasi momento una condivisione file montata in Cloud Shell. Dal momento che Cloud Shell richiede l’uso di una condivisione file montata, verrà richiesto di creare e montare una nuova condivisione file alla sessione successiva.

1. Eseguire `clouddrive unmount`.
2. Accettare e confermare i prompt.

La condivisione file continuerà a esistere a meno che non venga eliminata manualmente. Cloud Shell non eseguirà più la ricerca di questa condivisione file nelle sessioni successive. Per visualizzare altri dettagli, eseguire `clouddrive unmount -h`, come illustrato di seguito:

![Esecuzione del comando "clouddrive unmount"](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Sebbene l'esecuzione di questo comando non elimini le risorse, l'eliminazione manuale di un gruppo di risorse, un account di archiviazione o una condivisione file di cui è stato eseguito il mapping a Cloud Shell cancellerà l'immagine del disco della directory `$Home` e tutti i file presenti nella condivisione file. Questa azione non può essere annullata.

## <a name="list-clouddrive"></a>Elenco `clouddrive`
Per sapere quale condivisione file è montata come `clouddrive`, eseguire il comando `df`. 

Il percorso file a clouddrive indicherà il nome dell'account di archiviazione e la condivisione file nell'URL. Ad esempio, `//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```

[!include [features-introblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>Passaggi successivi
[Avvio rapido di Bash in Cloud Shell](quickstart.md) <br>
[Informazioni sull'archiviazione di Microsoft Azure Files](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[Informazioni sui tag di archiviazione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
