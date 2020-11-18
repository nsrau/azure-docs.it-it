---
title: Portale di Azure - Limitare l'accesso di importazione/esportazione ai dischi gestiti con collegamenti privati
description: Abilitare collegamenti privati per i dischi gestiti con il portale di Azure. È così possibile esportare e importare in modo sicuro i dischi all'interno della rete virtuale.
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 68a308952b63d15aa6db90021400f826ef575c0c
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94537187"
---
# <a name="use-the-azure-portal-to-restrict-importexport-access-for-managed-disks-with-private-links"></a>Usare il portale di Azure per limitare l'accesso di importazione/esportazione per i dischi gestiti con collegamenti privati

Il supporto di collegamenti privati per i dischi gestiti consente di limitare l'esportazione e l'importazione di dischi gestiti esclusivamente nei confini della rete virtuale di Azure. È possibile generare un URI di firma di accesso condiviso con limiti di tempo per gli snapshot e i dischi gestiti non collegati per esportare i dati in un'altra area per l'espansione a livello di area, il ripristino di emergenza e per leggere i dati per scopi legali. È anche possibile usare l'URI di firma di accesso condiviso per caricare direttamente un disco rigido virtuale in un disco vuoto dall'ambiente locale. Il traffico di rete tra i client nella rete virtuale e i dischi gestiti passa soltanto attraverso la rete virtuale e un collegamento privato sulla rete backbone Microsoft, eliminando l'esposizione alla rete Internet pubblica.

È possibile creare una risorsa di accesso al disco e collegarla alla rete virtuale nella stessa sottoscrizione creando un endpoint privato. È necessario associare un disco o uno snapshot con un accesso al disco per l'esportazione e l'importazione dei dati tramite i collegamenti privati. Inoltre, è necessario impostare la proprietà NetworkAccessPolicy del disco o dello snapshot su `AllowPrivate`. 

È possibile impostare la proprietà NetworkAccessPolicy su `DenyAll` per impedire a chiunque di generare l'URI di firma di accesso condiviso per un disco o uno snapshot. Il valore predefinito per la proprietà NetworkAccessPolicy è `AllowAll`.

## <a name="limitations"></a>Limitazioni

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../includes/virtual-machines-disks-private-links-limitations.md)]


## <a name="create-a-disk-access-resource"></a>Crea una risorsa di accesso al disco

1. Accedere al portale di Azure e passare ad **Accesso al disco** con [questo collegamento](https://aka.ms/disksprivatelinks).

    > [!IMPORTANT]
    > È necessario usare il [collegamento fornito](https://aka.ms/disksprivatelinks) per passare al pannello Accesso al disco. Attualmente non è visibile nel portale pubblico senza usare il collegamento.

1. Selezionare **+ Aggiungi** per creare una nuova risorsa di accesso al disco.
1. Nel pannello Crea selezionare la sottoscrizione, un gruppo di risorse, immettere un nome e selezionare un'area.
1. Selezionare **Rivedi e crea**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-create-basics.png" alt-text="Screenshot del pannello di creazione di accesso al disco. Immettere il nome desiderato, selezionare un'area, selezionare un gruppo di risorse e continuare":::

Dopo la creazione passare direttamente alla risorsa.

:::image type="content" source="media/disks-enable-private-links-for-import-export-portal/screenshot-resource-button.png" alt-text="Screenshot del pulsante Vai alla risorsa nel portale":::

## <a name="create-a-private-endpoint"></a>Creare un endpoint privato

Dopo aver creato una risorsa di accesso al disco, è possibile usarla per gestire l'accesso alle operazioni di esportazione/importazione del disco. Questa operazione viene eseguita tramite gli endpoint privati. Di conseguenza, è necessario creare un endpoint privato e configurarlo per l'accesso al disco.

1. Dalla risorsa di accesso al disco selezionare **Connessioni a endpoint privato**.
1. Selezionare **+ Endpoint privato**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-main-private-blade.png" alt-text="Screenshot del pannello Panoramica per la risorsa di accesso al disco. Connessioni a endpoint privato è evidenziato.":::

1. Selezionare un gruppo di risorse
1. Immettere il nome e selezionare la stessa area in cui è stata creata la risorsa di accesso al disco.
1. Selezionare **Avanti: Risorsa >**

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-first-blade.png" alt-text="Screenshot del flusso di lavoro di creazione dell'endpoint privato, primo pannello. Se non si seleziona l'area appropriata, è possibile che si verifichino problemi in un secondo momento.":::

1. Nel pannello **Risorsa** selezionare **Connettersi a una risorsa di Azure nella directory**.
1. Per **Tipo di risorsa** selezionare **Microsoft.Compute/diskAccesses**
1. Per **Risorsa** selezionare la risorsa di accesso al disco creata in precedenza
1. Lasciare **Risorsa secondaria di destinazione** impostata su **dischi**
1. Selezionare **Avanti: Configurazione >** .

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-second-blade.png" alt-text="Screenshot del flusso di lavoro di creazione dell'endpoint privato, secondo pannello. Con tutti i valori evidenziati (Tipo di risorsa, Risorsa, Risorsa secondaria di destinazione)":::

1. Selezionare la rete virtuale a cui si vuole limitare l'esportazione del disco. Le altre reti virtuali non potranno esportare il disco.

    > [!NOTE]
    > Se per la subnet selezionata è abilitato un gruppo di sicurezza di rete (NSG), verrà disabilitato solo per gli endpoint privati in questa subnet. Per le altre risorse in questa subnet, l'imposizione del gruppo di sicurezza di rete rimarrà attiva.

1. Selezionare una subnet appropriata
1. Selezionare **Rivedi e crea**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-third-blade.png" alt-text="Screenshot del flusso di lavoro di creazione dell'endpoint privato, terzo pannello. Rete virtuale e Subnet evidenziate.":::

## <a name="enable-private-endpoint-on-your-disk"></a>Abilitare l'endpoint privato nel disco

1. Passare al disco che si vuole configurare
1. Selezionare **Rete**
1. Selezionare **Endpoint privato (tramite accesso al disco)** e selezionare l'accesso al disco creato in precedenza.
1. Selezionare **Salva**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-managed-disk-networking-blade.png" alt-text="Screenshot del pannello Rete del disco gestito. Evidenziazione della selezione dell'endpoint privato e dell'accesso al disco selezionato. Il salvataggio di queste impostazioni configura il disco per questo accesso.":::

A questo punto è stata completata la configurazione dei collegamenti privati che è possibile usare quando si importa/esporta il disco gestito.

## <a name="next-steps"></a>Passaggi successivi

- [Domande frequenti per i collegamenti privati](./faq-for-disks.md#private-links-for-securely-exporting-and-importing-managed-disks)
- [Esportare/copiare snapshot gestiti come disco rigido virtuale in un account di archiviazione di un'area diversa con PowerShell](./scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account.md)
