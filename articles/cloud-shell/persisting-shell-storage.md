---
title: Impostazione della persistenza dei file in Azure Cloud Shell (anteprima) | Microsoft Docs
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
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 4603bcbbb752bba341b3e372266b98a1f0863195
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="persisting-files-in-azure-cloud-shell"></a>Impostazione della persistenza dei file in Azure Cloud Shell
Cloud Shell si avvale dell'archiviazione dei file di Azure per mantenere i file in più sessioni.

## <a name="setup-clouddrive"></a>Configurare clouddrive
All'avvio iniziale, Cloud Shell richiede di associare una condivisione di file nuovi o esistenti per mantenere i file in più sessioni.

### <a name="creating-new-storage"></a>Creazione di una nuova risorsa di archiviazione
![](media/basic-storage.png)

Quando si usano le impostazioni di base e si selezione solo una sottoscrizione, vengono create tre risorse per conto dell'utente nell'area supportata più vicina:
1. Gruppo di risorse denominato: `cloud-shell-storage-<region>`
2. Account di archiviazione denominato: `cs-uniqueGuid`
3. Condivisione file denominata: `cs-<user>-<domain>-com-uniqueGuid`

Questa condivisione file verrà montata come `clouddrive` nella directory $Home. La condivisione file viene anche usata per archiviare un'immagine da 5 GB creata per l'utente, che automaticamente aggiorna e rende persistente la directory $Home. Questa azione è occasionale e viene ripetuta automaticamente nelle sessioni successive.

### <a name="use-existing-resources"></a>Usare le risorse esistenti
![](media/advanced-storage.png) Viene data anche un'opzione avanzata che consente di associare le risorse esistenti. Quando verrà richiesta l'installazione dell'archiviazione, fare clic su "Mostra impostazioni avanzate" per vedere le opzioni aggiuntive. Le condivisioni di file esistenti riceveranno un'immagine utente di 5 GB per mantenere la directory $Home. I menu a discesa vengono filtrati per le aree Cloud Shell assegnate e gli account di archiviazione ridondanti a livello locale o globale.

### <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Limitare la creazione di risorse con i criteri delle risorse di Azure
Gli account di archiviazione creati da Cloud Shell sono contrassegnati con `ms-resource-usage:azure-cloud-shell`. Se si desidera impedire agli utenti di creare gli account di archiviazione con Cloud Shell, creare i [criteri delle risorse di Azure per i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy-tags) attivati dal tag specificato.

## <a name="how-it-works"></a>Funzionamento
### <a name="cloud-shell-persists-files-with-both-methods-below"></a>Cloud Shell rende persistenti i file con entrambi i metodi seguenti:
1. Creare un'immagine del disco della directory $Home per rendere permanenti i file presenti in $Home. L'immagine del disco viene salvata nella condivisione file specificata come `acc_<User>.img` in `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img` e le modifiche vengono automaticamente sincronizzate.

2. Montare la condivisione file specificata come `clouddrive` nella directory $Home per l'interazione diretta con la condivisione file. 
Viene eseguito il mapping di `/Home/<User>/clouddrive` a `fileshare.storage.windows.net/fileshare`.
 
> [!Note]
> Tutti i file della directory $Home, come le chiavi SSH, vengono mantenuti nell'immagine del disco utente archiviato nella condivisione file montata. Applicare le procedure consigliate quando si rendono persistenti le informazioni nella directory $Home e nella condivisione file montata.

## <a name="using-the-clouddrive-command"></a>Uso del comando clouddrive
Cloud Shell consente agli utenti di eseguire un comando denominato `clouddrive` che consente di aggiornare manualmente la condivisione file montata in Cloud Shell.
![](media/clouddrive-h.png)

## <a name="mount-a-new-clouddrive"></a>Montare una nuova clouddrive

### <a name="pre-requisites-for-manual-mounting"></a>Prerequisiti per il montaggio manuale
È possibile aggiornare la condivisione di file associata a Cloud Shell con il comando `clouddrive mount`.

Se si monta una condivisione file esistente, gli account di archiviazione devono:
1. Essere account di archiviazione con ridondanza locale o di archiviazione con ridondanza geografica per supportare le condivisioni file.
2. Trovarsi nell'area assegnata. Durante l'onboarding, l'area a cui si è assegnati è elencata nel nome del gruppo di risorse `cloud-shell-storage-<region>`.

### <a name="supported-storage-regions"></a>Aree di archiviazione supportate
I file di Azure devono trovarsi nella stessa area del computer Cloud Shell in cui vengono montati. I computer Cloud Shell esistono nelle aree seguenti:
|Area|Region|
|---|---|
|Americhe|Stati Uniti orientali, Stati Uniti centro-meridionali, Stati Uniti occidentali|
|Europa|Europa settentrionale, Europa occidentale|
|Asia/Pacifico|India centrale, Asia sud-orientale|

### <a name="mount-command"></a>Comando mount

> [!NOTE]
> Se si monta una nuova condivisione file, verrà creata una nuova immagine utente per la directory $Home perché l'immagine di $Home precedente viene mantenuta nella condivisione file precedente.

Eseguire `clouddrive mount` con i parametri seguenti: <br>

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Per informazioni dettagliate, eseguire `clouddrive mount -h`: <br>
![](media/mount-h.png)

## <a name="unmount-clouddrive"></a>Smontare l'unità clouddrive
È possibile smontare una condivisione file montata in Cloud Shell in qualsiasi momento. Cloud Shell richiede tuttavia una condivisione file montata, quindi, se viene rimossa, verrà chiesto di creare e montare una nuova condivisione file alla sessione successiva.

Per rimuovere una condivisione file da Cloud Shell:
1. Eseguire `clouddrive unmount`
2. Accettare e confermare i prompt

La condivisione file continuerà a esistere a meno che non venga eliminata manualmente. Cloud Shell non eseguirà più la ricerca di questa condivisione file nelle sessioni successive.

Per informazioni dettagliate, eseguire `clouddrive mount -h`: <br>
![](media/unmount-h.png)

> [!WARNING]
> Questo comando non elimina le risorse, ma l'eliminazione manuale del gruppo di risorse, dell'account di archiviazione o della condivisione file di cui è stato eseguito il mapping a Cloud Shell cancellerà l'immagine del disco della directory $Home e i file presenti nella condivisione file. Questa operazione non può essere annullata.

## <a name="list-clouddrive"></a>Elencare clouddrive
Per sapere quale condivisione file è montata come `clouddrive`:

Eseguire `df` 

Il percorso file di clouddrive indicherà il nome dell'account di archiviazione e la condivisione file nell'URL.

`//storageaccountname.file.core.windows.net/filesharename`

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
La directory `clouddrive` viene sincronizzata con il pannello di archiviazione del portale di Azure. Usarlo per trasferire file locali da e verso la condivisione file. L'aggiornamento dei file da Cloud Shell viene applicato nell'interfaccia utente grafica di archiviazione file quando il pannello viene aggiornato.

### <a name="download-files"></a>Download dei file
![](media/download.png)
1. Passare alla condivisione file montata
2. Selezionare il file di destinazione nel portale
3. Fare clic su "Scarica"

### <a name="upload-files"></a>Caricare file
![](media/upload.png)
1. Passare alla condivisione file montata
2. Selezionare "Carica"
3. Selezionare il file da caricare
4. Confermare il caricamento

Ora si dovrebbe vedere il file accessibile nella directory clouddrive in Cloud Shell.

## <a name="next-steps"></a>Passaggi successivi
[Avvio rapido di Cloud Shell](quickstart.md) <br>
[Informazioni sull'archiviazione file di Azure](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[Informazioni sui tag di archiviazione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
