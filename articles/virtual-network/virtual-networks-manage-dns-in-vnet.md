---
title: Gestire i server DNS utilizzati da una rete virtuale (classica) - Portale di Azure (classico) | Documentazione Microsoft
description: Informazioni su come aggiungere e rimuovere server DNS in una rete virtuale (classica) mediante il portale di Azure (classico).
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: b582be7d-dc78-4cfe-a766-185bd7e5dc68
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: b765fb94f881453ae6a90ec0ae6b6f06843b3aa2
ms.lasthandoff: 02/28/2017


---
# <a name="manage-dns-servers-used-by-a-virtual-network-classic-using-the-azure-portal-classic"></a>Gestire i server DNS utilizzati da una rete virtuale (classica) mediante il portale di Azure (classico)

È possibile gestire l'elenco dei server DNS utilizzati in una rete virtuale nel portale di Azure (classico) o nel file di configurazione di rete. È possibile aggiungere fino a 12 server DNS per ogni rete virtuale. Quando si specificano i server DNS, è importante verificare che siano elencati nell'ordine corretto per l'ambiente. Gli elenchi dei server DNS non supportano il round robin. Vengono usati nell'ordine in cui sono specificati. Se è possibile raggiungere il primo server DNS nell'elenco, il client userà tale server DNS indipendentemente dal fatto che funzioni correttamente o meno. Per modificare l'ordine dei server DNS per una rete virtuale, rimuovere i server DNS dall'elenco e aggiungerli nuovamente nell'ordine desiderato.

> [!WARNING]
> Dopo aver aggiornato l'elenco di server DNS, è necessario riavviare le macchine virtuali presenti nella rete virtuale affinché possano acquisire le nuove impostazioni dei server DNS. Le macchine virtuali continueranno a usare la configurazione corrente fino a quando non vengono riavviate.
> 
> 

## <a name="edit-a-dns-server-list-for-a-virtual-network-using-the-azure-portal-classic"></a>Modificare un elenco di server DNS per una rete virtuale tramite il portale di Azure (classico)
1. Accedere al [portale di Azure (classico)](https://manage.windowsazure.com).
2. Nel pannello di navigazione fare clic su **Reti** e quindi sul nome della rete virtuale nella colonna **Nome**.
3. Fare clic su **Configura**.
4. In **Server DNS**è possibile configurare quanto segue:
   
   * **Per registrare (aggiungere) un nuovo server DNS** : digitare semplicemente il nome e l'indirizzo IP nelle caselle. In questo modo viene aggiunto un server DNS all'elenco dei server DNS della rete virtuale e tale server viene registrato in Azure.
   * **Per aggiungere un server DNS precedentemente registrato** : se è stato già registrato un server DNS in Azure, è possibile selezionarlo dall'elenco prepopolato.
   * **Per rimuovere un server DNS dalla rete virtuale** : fare clic sulla X accanto al server da rimuovere. Si noti che in questo modo il server viene rimosso solo dall'elenco di questa rete virtuale. Il server DNS resta registrato in Azure in modo che venga usato dalle altre reti virtuali. Per eliminare un server DNS dalla sottoscrizione, passare alla pagina **Reti -> Server DNS**.
   * **Per riordinare i server DNS** : rimuovere tutti i server DNS elencati e quindi aggiungerli di nuovo nell'ordine desiderato. Tenere presente che non si tratta di un elenco DNS round robin.
   * **Per rinominare un server DNS** : evidenziare il server DNS nell'elenco e quindi digitare il nuovo nome. In questo modo verrà registrato un nuovo server DNS in Azure e tale server verrà aggiunto all'elenco di server DNS della rete virtuale. Il server DNS precedente e il relativo indirizzo IP continueranno a essere registrati in Azure. È possibile eliminarlo nella pagina **Server DNS** se non viene usato per altre reti virtuali.
5. Fare clic su **Salva** nella parte inferiore della pagina per salvare la nuova configurazione di server DNS.
6. Riavviare le macchine virtuali contenute nella rete virtuale in modo che possano acquisire le nuove impostazioni DNS.

## <a name="edit-a-dns-server-list-using-a-network-configuration-file"></a>Modificare un elenco di server DNS tramite un file di configurazione di rete
Per modificare un elenco di server DNS usando un file di configurazione di rete, è necessario prima esportare le impostazioni di configurazione dal portale di gestione. Modificare quindi il file di configurazione di rete e reimportarlo tramite il portale di Azure (classico). Segue un elenco di carattere generale dei passaggi per il completamento del processo.

1. Esportare le impostazioni della rete virtuale in un file di configurazione di rete. Per altre informazioni e per i passaggi per l'esportazione delle impostazioni della configurazione di rete, vedere [Esportare le impostazioni della rete virtuale in un file di configurazione di rete](virtual-networks-using-network-configuration-file.md).
2. Specificare le informazioni del server DNS per la rete virtuale. Per altre informazioni su come specificare un server DNS, vedere [Specifica delle impostazioni DNS in un file di configurazione della rete virtuale](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md). Per altre informazioni sui file di configurazione di rete, vedere [Azure Virtual Network Configuration Schema](https://msdn.microsoft.com/library/azure/jj157100.aspx) (Schema di configurazione della rete virtuale di Azure) e [Configurare una rete virtuale usando un file di configurazione di rete](virtual-networks-using-network-configuration-file.md).
3. Importare il file di configurazione di rete. Per altre informazioni e per i passaggi relativi all'importazione del file di configurazione di rete, vedere [Importare un file di configurazione di rete](virtual-networks-using-network-configuration-file.md).
4. Riavviare le macchine virtuali contenute nella rete virtuale in modo che possano acquisire le nuove impostazioni DNS.


