---
title: 'Linee guida per la denominazione dell&quot;infrastruttura di Azure: Windows | Microsoft Docs'
description: Informazioni sulle principali linee guida di progettazione e implementazione per la denominazione nei servizi di infrastruttura di Azure.
documentationcenter: 
services: virtual-machines-windows
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 660765fa-4d42-49cb-a9c6-8c596d26d221
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: e335ecafdd6347bf8b839f50bf36e3d5e2cd42fa
ms.lasthandoff: 03/18/2017


---
# <a name="azure-infrastructure-naming-guidelines-for-windows-vms"></a>Linee guida sulle convenzioni di denominazione dell'infrastruttura di Azure per macchine virtuali Windows

[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

Questo articolo è incentrato sulla comprensione dell'approccio alle convenzioni di denominazione delle varie risorse di Azure per la creazione di un set di risorse logico e facilmente identificabile all'interno dell'ambiente.

## <a name="implementation-guidelines-for-naming-conventions"></a>Linee guida di implementazione per le convenzioni di denominazione
Decisioni:

* Quali sono le convenzioni di denominazione per le risorse di Azure?

Attività:

* Definire gli affissi da usare per mantenere la coerenza delle risorse.
* Definire i nomi degli account di archiviazione, rispettando il requisito dell'univocità globale.
* Documentare la convenzione di denominazione che sarà usata e distribuire il documento a tutte le parti interessate per garantire la coerenza tra le distribuzioni.

## <a name="naming-conventions"></a>Convenzioni di denominazione
Prima di creare qualsiasi elemento in Azure, è necessaria una buona convenzione di denominazione Una convenzione di denominazione garantisce la possibilità che tutte le risorse dispongano di un nome stimabile, in modo da ridurre il carico amministrativo associato alla gestione di tali risorse.

È possibile scegliere di seguire un set specifico di convenzioni di denominazione definite per l'intera organizzazione oppure per un account o una sottoscrizione di Azure specifica. Sebbene sia facile per i singoli utenti all'interno delle organizzazioni stabilire le regole implicite quando si usano risorse di Azure, quando un team deve lavorare a un progetto in Azure, la scalabilità di tale modello non è ottimale.

Concordare in anticipo il set di convenzioni di denominazione. Alcune considerazioni relative alle convenzioni di denominazione trascendono questi set di regole.

## <a name="affixes"></a>Affissi
Quando si definisce una convenzione di denominazione, una decisione riguarda la posizione dell'affisso:

* Inizio del nome (prefisso)
* Fine del nome (suffisso)

Ad esempio, di seguito sono indicati due possibili nomi di un gruppo di risorse nei quali viene usato l'affisso `rg` :

* Rg-WebApp (prefisso)
* WebApp-Rg (suffisso)

Gli affissi possono fare riferimento ai diversi aspetti che descrivono le specifiche risorse. La tabella seguente illustra alcuni esempi usati comunemente.

| Aspetto | esempi | Note |
|:--- |:--- |:--- |
| Environment |dev, stg, prod |A seconda dello scopo e del nome di ogni ambiente. |
| Percorso |usw (Stati Uniti occidentali), use (Stati Uniti orientali 2) |A seconda dell'area del data center o dell’area dell'organizzazione. |
| Componente di Azure, servizio o prodotto |Rg per gruppo di risorse, VNet per rete virtuale |A seconda del prodotto per il quale la risorsa fornisce il supporto. |
| Ruolo |sql, ora, sp, iis |A seconda del ruolo della macchina virtuale. |
| Istanza |01, 02 e 03 e così via. |Per le risorse con più di un'istanza. Ad esempio, i server Web con carico bilanciato in un servizio cloud. |

Quando si stabiliscono le convenzioni di denominazione, assicurarsi che siano indicati chiaramente quali affissi usare per ogni tipo di risorsa, e in quale posizione (prefisso o suffisso).

## <a name="dates"></a>Date
Molte volte è importante determinare la data di creazione dal nome di una risorsa. È consigliabile usare il formato di data AAAAMMGG. Tale formato garantisce non solo che venga registrata la data completa, ma anche che due risorse i cui nomi si differenziano solo per la data verranno ordinate alfabeticamente e allo stesso tempo in ordine cronologico.

## <a name="naming-resources"></a>Nomi delle risorse
Definire ogni tipo di risorsa nella convenzione di denominazione, quindi è necessario avere regole che definiscono come assegnare nomi a ogni risorsa creata. Tali regole devono essere applicate a tutti i tipi di risorse, ad esempio:

* Sottoscrizioni
* Account
* Account di archiviazione
* Reti virtuali
* Subnet
* Set di disponibilità
* Gruppi di risorse
* Macchine virtuali
* Endpoint
* Gruppi di sicurezza di rete
* Ruoli

Per assicurarsi che il nome fornisca informazioni sufficienti per determinare a quali risorse fa riferimento, è necessario inserire nomi descrittivi.

## <a name="computer-names"></a>Nomi dei computer
Quando si crea una macchina virtuale, Microsoft Azure richiede un nome di macchina virtuali con un massimo 15 caratteri che viene usato per il nome della risorsa. Azure usa lo stesso nome per il sistema operativo installato nella macchina virtuale. È possibile tuttavia che questi nomi non siano sempre gli stessi.

Nel caso in cui viene creata una macchina virtuale da un file immagine con estensione vhd che già contiene un sistema operativo, il nome della macchina virtuale in Azure può essere diverso dal nome computer del sistema operativo della macchina virtuale. Questa situazione può aggiungere un livello di difficoltà alla gestione delle macchine virtuali, quindi è sconsigliata. Assicurarsi sempre che il nome della risorsa macchina virtuale di Azure sia lo stesso nome del computer assegnato al sistema operativo di tale macchina virtuale.

È consigliabile che il nome della VM di Azure e del computer del sistema operativo sottostante coincidano.

## <a name="storage-account-names"></a>Nomi account di archiviazione
Questa sezione non si applica a [Managed Disks di Azure](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), in quanto non si crea un account di archiviazione separato. Per i dischi non gestiti, gli account di archiviazione hanno regole speciali che ne controllano i nomi. È possibile usare solo lettere minuscole e numeri. Per altre informazioni, vedere [Creare un account di archiviazione](../storage/storage-create-storage-account.md#create-a-storage-account). Inoltre, il nome dell'account di archiviazione, insieme a core.windows.net, deve essere un nome DNS a livello globale valido e univoco. Ad esempio, se il nome dell'account di archiviazione è mystorageaccount, i seguenti nomi DNS risultanti devono essere univoci:

* mystorageaccount.blob.core.windows.net
* mystorageaccount.table.core.windows.net
* mystorageaccount.queue.core.windows.net

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]


