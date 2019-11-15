---
title: Mantieni file in Azure Cloud Shell | Microsoft Docs
description: Procedura dettagliata su come Azure Cloud Shell rende persistenti i file.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: damaerte
ms.openlocfilehash: ee68400d000ca823816c8efc6bcbc224d1388832
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082990"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Rendere persistenti i file in Azure Cloud Shell
Cloud Shell utilizza l'archiviazione dei file di Azure per mantenere i file in più sessioni. Al primo avvio Cloud Shell richiede di associare una condivisione file nuova o esistente per mantenere i file in più sessioni.

> [!NOTE]
> La condivisione Bash e PowerShell sono la stessa condivisione file. Solo una condivisione file può essere associata con il montaggio automatico in Cloud Shell.

## <a name="create-new-storage"></a>Creare una nuova risorsa di archiviazione

Quando si usano le impostazioni di base e si seleziona una sola sottoscrizione, Cloud Shell crea tre risorse per conto dell'utente nell'area di Azure supportata più vicina all'utente:
* Gruppo di risorse: `cloud-shell-storage-<region>`
* Account di archiviazione: `cs<uniqueGuid>`
* Condivisione file: `cs-<user>-<domain>-com-<uniqueGuid>`

![Impostazione della sottoscrizione](media/persisting-shell-storage/basic-storage.png)

La condivisione viene montata come `clouddrive` nella directory `$Home`. Questa azione viene eseguita una sola volta e la condivisione file viene ripetuta automaticamente nelle sessioni successive. 

> [!NOTE]
> Per la sicurezza, ogni utente deve effettuare il provisioning del proprio account di archiviazione.  Per il controllo degli accessi in base al ruolo, gli utenti devono disporre dell'accesso come collaboratore o superiore a livello dell'account di archiviazione.

La condivisione file contiene anche un'immagine da 5 GB creata per l'utente che rende automaticamente persistenti i dati nella directory `$Home`. Questo vale sia per Bash che per PowerShell.

## <a name="use-existing-resources"></a>Usare le risorse esistenti

Con l'opzione Avanzate è possibile associare risorse esistenti. Quando si seleziona un'area Cloud Shell è necessario selezionare un account di archiviazione di backup collocato nella stessa area. Se, ad esempio, l'area assegnata è Stati Uniti occidentali, è necessario associare una condivisione di file anch'essa posizionata nell'area Stati Uniti occidentali.

Al prompt di impostazione dell'archiviazione, selezionare **Mostra impostazioni avanzate** per visualizzare le opzioni aggiuntive. Il filtro di opzioni di archiviazione popolato per gli account di archiviazione con ridondanza locale, archiviazione con ridondanza della zona e archiviazione con ridondanza geografica. 

> [!NOTE]
> L'uso degli account di archiviazione con ridondanza geografica o della zona è consigliato per aumentare la resilienza per la condivisione di file di backup. Il tipo di ridondanza dipende dagli obiettivi e dalla preferenza di prezzo. [Altre informazioni sulle opzioni di replica per gli account di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy).

![Impostazione del gruppo di risorse](media/persisting-shell-storage/advanced-storage.png)

### <a name="supported-storage-regions"></a>Aree di archiviazione supportate
Gli account di archiviazione di Azure associati devono risiedere nella stessa area del computer Cloud Shell in cui vengono montati. Per trovare l'area corrente, è possibile eseguire `env` in Bash e individuare la variabile `ACC_LOCATION`. Le condivisioni file ricevono un'immagine da 5 GB creata automaticamente per mantenere persistente la directory `$Home`.

I computer Cloud Shell esistono nelle aree seguenti:

|Area|Area|
|---|---|
|Americhe|Stati Uniti orientali, Stati Uniti centro-meridionali, Stati Uniti occidentali|
|Europa|Europa settentrionale, Europa occidentale|
|Asia/Pacifico|India centrale, Asia sud-orientale|

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Limitare la creazione di risorse con i criteri delle risorse di Azure
Gli account di archiviazione che si creano in Cloud Shell sono contrassegnati con `ms-resource-usage:azure-cloud-shell`. Se si desidera impedire agli utenti di creare account di archiviazione con Cloud Shell, creare [criteri di risorse di Azure per tag](../azure-policy/json-samples.md) che vengono attivati dal tag specificato.



## <a name="how-cloud-shell-storage-works"></a>Come funziona l’archiviazione Cloud Shell 
Cloud Shell rende persistenti i file tramite entrambe le modalità seguenti: 
* Creazione di un'immagine del disco della directory `$Home` per rendere persistenti tutti i contenuti della directory. L'immagine del disco viene salvata nella condivisione file specificata come `acc_<User>.img` in `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img` e le modifiche vengono automaticamente sincronizzate. 
* Montaggio della condivisione file specificata come `clouddrive` nella directory `$Home` per l'interazione diretta con la condivisione file. Viene eseguito il mapping di `/Home/<User>/clouddrive` a `fileshare.storage.windows.net/fileshare`.
 
> [!NOTE]
> Tutti i file della directory `$Home`, come le chiavi SSH, vengono mantenuti nell'immagine del disco utente archiviata nella condivisione file montata. Applicare le procedure consigliate quando si rendono persistenti le informazioni nella directory `$Home` e nella condivisione file montata.

## <a name="clouddrive-commands"></a>comandi CloudDrive

### <a name="use-the-clouddrive-command"></a>Usare il comando `clouddrive`
In Cloud Shell, è possibile eseguire un comando denominato `clouddrive`, che consente di aggiornare manualmente la condivisione file montata in Cloud Shell.
![Esecuzione del comando "clouddrive"](media/persisting-shell-storage/clouddrive-h.png)

### <a name="list-clouddrive"></a>Elenco `clouddrive`
Per sapere quale condivisione file è montata come `clouddrive`, eseguire il comando `df`. 

Il percorso file a clouddrive indica il nome dell'account di archiviazione e la condivisione file nell'URL. Ad esempio, `//storageaccountname.file.core.windows.net/filesharename`

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

### <a name="mount-a-new-clouddrive"></a>Montare una nuova clouddrive

#### <a name="prerequisites-for-manual-mounting"></a>Prerequisiti per il montaggio manuale
È possibile aggiornare la condivisione file associata a Cloud Shell usando il comando `clouddrive mount`.

Se si monta una condivisione file esistente, gli account di archiviazione devono essere posizionati nell'area Cloud Shell selezionata. Recuperare il percorso eseguendo `env` e controllando il `ACC_LOCATION`.

#### <a name="the-clouddrive-mount-command"></a>Comando `clouddrive mount`

> [!NOTE]
> Se si sta montando una nuova condivisione file, una nuova immagine utente viene creata per la directory `$Home`. L’immagine `$Home` precedente viene mantenuta nella condivisione file precedente.

Eseguire il comando `clouddrive mount` con i parametri seguenti:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Per visualizzare altri dettagli, eseguire `clouddrive mount -h`, come illustrato di seguito:

![Esecuzione del comando `clouddrive mount`](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>Smontare l'unità clouddrive
È possibile smontare in qualsiasi momento una condivisione file montata in Cloud Shell. Dal momento che Cloud Shell richiede l’uso di una condivisione file montata, verrà richiesto di creare e montare una nuova condivisione file alla sessione successiva.

1. Eseguire `clouddrive unmount`.
2. Accettare e confermare i prompt.

La condivisione file continuerà a esistere a meno che non venga eliminata manualmente. Cloud Shell non eseguirà più la ricerca di questa condivisione file nelle sessioni successive. Per visualizzare altri dettagli, eseguire `clouddrive unmount -h`, come illustrato di seguito:

![Esecuzione del comando "clouddrive unmount"](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Sebbene l'esecuzione di questo comando non elimini le risorse, l'eliminazione manuale di un gruppo di risorse, un account di archiviazione o una condivisione file di cui è stato eseguito il mapping a Cloud Shell cancellerà l'immagine del disco della directory `$Home` e tutti i file presenti nella condivisione file. Questa azione non può essere annullata.
## <a name="powershell-specific-commands"></a>PowerShell - Comandi specifici

### <a name="list-clouddrive-azure-file-shares"></a>Elencare le condivisioni file di Azure `clouddrive`
Il cmdlet `Get-CloudDrive` recupera le informazioni sulle condivisioni file di Azure attualmente montate da `clouddrive` in Cloud Shell. <br>
![Esecuzione di Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>Smontare `clouddrive`
È possibile smontare in qualsiasi momento una condivisione file di Azure montata in Cloud Shell. Se la condivisione file di Azure è stata rimossa, alla sessione successiva verrà chiesto di crearne e montarne una nuova.

Il cmdlet `Dismount-CloudDrive` smonta una condivisione file di Azure dall'account di archiviazione corrente. Se si smonta `clouddrive`, viene terminata la sessione corrente. All'utente viene chiesto di creare e montare una nuova condivisione file di Azure durante la sessione successiva.
![Esecuzione di Dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

Nota: se è necessario definire una funzione in un file e chiamarla dai cmdlet di PowerShell, è necessario includere l'operatore punto. Ad esempio: . .\MyFunctions.ps1

## <a name="next-steps"></a>Passaggi successivi
[Avvio rapido di Cloud Shell](quickstart.md) <br>
[Informazioni sull'archiviazione di Microsoft Azure Files](https://docs.microsoft.com/azure/storage/storage-introduction) <br>
[Informazioni sui tag di archiviazione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
