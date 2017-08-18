---
title: Rendere persistenti i file in Azure Cloud Shell (Anteprima) | Microsoft Docs
description: Procedura dettagliata su come Azure Cloud Shell rende persistenti i file.
services: 
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
ms.date: 07/17/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 26428ad0d3acda959235ffa780294154ba61bca5
ms.contentlocale: it-it
ms.lasthandoff: 08/08/2017

---

# <a name="persist-files-in-azure-cloud-shell"></a>Rendere persistenti i file in Azure Cloud Shell
Cloud Shell si avvale dell'archiviazione file di Azure per rendere persistenti i file in più sessioni.

## <a name="set-up-a-clouddrive-file-share"></a>Configurare una condivisione file `clouddrive`
Al primo avvio Cloud Shell richiede di associare una condivisione file nuova o esistente per mantenere i file in più sessioni.

### <a name="create-new-storage"></a>Creare una nuova risorsa di archiviazione

Quando si usano le impostazioni di base e si seleziona una sola sottoscrizione, Cloud Shell crea tre risorse per conto dell'utente nell'area di Azure supportata più vicina all'utente:
* Gruppo di risorse: `cloud-shell-storage-<region>`
* Account di archiviazione: `cs-uniqueGuid`
* Condivisione file: `cs-<user>-<domain>-com-uniqueGuid`

![Impostazione della sottoscrizione](media/basic-storage.png)

La condivisione viene montata come `clouddrive` nella directory `$Home`. La condivisione file viene usata anche per archiviare un'immagine da 5 GB creata automaticamente, che si aggiorna automaticamente e rende persistente la directory `$Home`. Questa azione viene eseguita una sola volta e la condivisione file viene ripetuta automaticamente nelle sessioni successive.

### <a name="use-existing-resources"></a>Usare le risorse esistenti

Con l'opzione Avanzate è possibile associare risorse esistenti. Al prompt di impostazione dell'archiviazione, selezionare **Mostra impostazioni avanzate** per visualizzare le opzioni aggiuntive. Le condivisioni file esistenti ricevono un'immagine utente di 5 GB per mantenere persistente la directory `$Home`. I menu a discesa vengono filtrati in base all'area Cloud Shell assegnata e agli account di archiviazione ridondanti a livello locale o globale.

![Impostazione del gruppo di risorse](media/advanced-storage.png)

### <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Limitare la creazione di risorse con i criteri delle risorse di Azure
Gli account di archiviazione che si creano in Cloud Shell sono contrassegnati con `ms-resource-usage:azure-cloud-shell`. Se si desidera impedire agli utenti di creare account di archiviazione con Cloud Shell, creare [criteri di risorse di Azure per tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy-tags) che vengono attivati dal tag specificato.

## <a name="how-cloud-shell-works"></a>Funzionamento di Cloud Shell
Cloud Shell rende persistenti i file tramite entrambe le modalità seguenti:
* Creazione di un'immagine del disco della directory `$Home` per rendere persistenti tutti i contenuti della directory. L'immagine del disco viene salvata nella condivisione file specificata come `acc_<User>.img` in `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img` e le modifiche vengono automaticamente sincronizzate.

* Montaggio della condivisione file specificata come `clouddrive` nella directory `$Home` per l'interazione diretta con la condivisione file. Viene eseguito il mapping di `/Home/<User>/clouddrive` a `fileshare.storage.windows.net/fileshare`.
 
> [!NOTE]
> Tutti i file della directory `$Home`, come le chiavi SSH, vengono mantenuti nell'immagine del disco utente archiviata nella condivisione file montata. Applicare le procedure consigliate quando si rendono persistenti le informazioni nella directory `$Home` e nella condivisione file montata.

## <a name="use-the-clouddrive-command"></a>Usare il comando `clouddrive`
Con Cloud Shell è possibile eseguire un comando denominato `clouddrive`, che consente di aggiornare manualmente la condivisione file montata in Cloud Shell.
![Esecuzione del comando "clouddrive"](media/clouddrive-h.png)

## <a name="mount-a-new-clouddrive"></a>Montare un nuovo elemento `clouddrive`

### <a name="prerequisites-for-manual-mounting"></a>Prerequisiti per il montaggio manuale
È possibile aggiornare la condivisione file associata a Cloud Shell usando il comando `clouddrive mount`.

Se si monta una condivisione file esistente, gli account di archiviazione devono avere le caratteristiche seguenti:
* Avere un account di archiviazione ridondante a livello locale o globale per supportare le condivisioni file.
* Trovarsi nell'area assegnata. Durante l'onboarding, l'area a cui si è assegnati viene elencata nel nome del gruppo di risorse `cloud-shell-storage-<region>`.

### <a name="supported-storage-regions"></a>Aree di archiviazione supportate
I file di Azure devono risiedere nella stessa area del computer Cloud Shell in cui vengono montati. I computer Cloud Shell esistono nelle aree seguenti:
|Area|Region|
|---|---|
|Americhe|Stati Uniti orientali, Stati Uniti centro-meridionali, Stati Uniti occidentali|
|Europa|Europa settentrionale, Europa occidentale|
|Asia/Pacifico|India centrale, Asia sud-orientale|

### <a name="the-clouddrive-mount-command"></a>Comando `clouddrive mount`

> [!NOTE]
> Se si monta una nuova condivisione file, viene creata una nuova immagine utente per la directory `$Home` perché l'immagine di `$Home` precedente viene mantenuta nella condivisione file precedente.

Eseguire il comando `clouddrive mount` con i parametri seguenti:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Per visualizzare altri dettagli, eseguire `clouddrive mount -h`, come illustrato di seguito:

![Esecuzione del comando `clouddrive mount`](media/mount-h.png)

## <a name="unmount-clouddrive"></a>Smontare `clouddrive`
È possibile smontare in qualsiasi momento una condivisione file montata in Cloud Shell. Dal momento però che Cloud Shell richiede una condivisione file montata, se la condivisione file viene smontata, alla sessione successiva verrà chiesto di crearne e montarne una nuova.

Per rimuovere una condivisione file da Cloud Shell:
1. Eseguire `clouddrive unmount`.
2. Accettare e confermare i prompt.

La condivisione file continuerà a esistere a meno che non venga eliminata manualmente. Cloud Shell non eseguirà più la ricerca di questa condivisione file nelle sessioni successive.

Per visualizzare altri dettagli, eseguire `clouddrive unmount -h`, come illustrato di seguito:

![Esecuzione del comando "clouddrive unmount"](media/unmount-h.png)

> [!WARNING]
> Sebbene l'esecuzione di questo comando non elimini le risorse, l'eliminazione manuale di un gruppo di risorse, un account di archiviazione o una condivisione file di cui è stato eseguito il mapping a Cloud Shell cancellerà l'immagine del disco della directory `$Home` e tutti i file presenti nella condivisione file. Questa azione non può essere annullata.

## <a name="list-clouddrive-file-shares"></a>Elencare le condivisioni file `clouddrive`
Per sapere quale condivisione file è montata come `clouddrive`, eseguire il comando `df` seguente. 

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

## <a name="transfer-local-files-to-cloud-shell"></a>Trasferire file locali in Cloud Shell
La directory `clouddrive` viene sincronizzata con il pannello di archiviazione del portale di Azure. Usare questo pannello per trasferire file locali da e verso la condivisione file. L'aggiornamento dei file dall'interno di Cloud Shell si riflette nell'interfaccia utente grafica di archiviazione file quando il pannello viene aggiornato.

### <a name="download-files"></a>Download dei file

![Elenco di file locali](media/download.png)
1. Nel portale di Azure passare alla condivisione file montata.
2. Selezionare il file di destinazione.
3. Fare clic sul pulsante **Download**.

### <a name="upload-files"></a>Caricare file

![File locali da caricare](media/upload.png)
1. Passare alla condivisione file montata.
2. Selezionare il pulsante **Carica**.
3. Selezionare uno o più file che si desidera caricare.
4. Confermare il caricamento.

Si dovrebbe a questo punto vedere che è possibile accedere ai file nella directory `clouddrive` in Cloud Shell.

## <a name="next-steps"></a>Passaggi successivi
[Avvio rapido di Cloud Shell](quickstart.md) <br>
[Informazioni sull'archiviazione file di Azure](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[Informazioni sui tag di archiviazione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>

