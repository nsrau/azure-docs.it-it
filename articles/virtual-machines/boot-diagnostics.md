---
title: Diagnostica di avvio di Azure
description: Panoramica della diagnostica di avvio di Azure e della diagnostica di avvio gestito
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: aca41edeb159a65b27ecbbc27ae568f8bc94cebe
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181868"
---
# <a name="azure-boot-diagnostics"></a>Diagnostica di avvio di Azure

La diagnostica di avvio è una funzionalità di debug per macchine virtuali di Azure (VM) che consente la diagnosi degli errori di avvio della VM. La diagnostica di avvio consente a un utente di osservare lo stato della macchina virtuale durante l'avvio raccogliendo informazioni sul log seriale e schermate.

## <a name="boot-diagnostics-storage-account"></a>Account di archiviazione di diagnostica di avvio
Quando si crea una macchina virtuale in portale di Azure, la diagnostica di avvio è abilitata per impostazione predefinita. L'esperienza di diagnostica di avvio consigliata prevede l'uso di un account di archiviazione gestito, poiché produce miglioramenti significativi delle prestazioni nel tempo per la creazione di una macchina virtuale di Azure. Questo perché verrà usato un account di archiviazione gestito di Azure, rimuovendo il tempo necessario per creare un nuovo account di archiviazione utente per archiviare i dati di diagnostica di avvio.

Un'esperienza di diagnostica di avvio alternativa consiste nell'usare un account di archiviazione gestito dall'utente. Un utente può creare un nuovo account di archiviazione o utilizzarne uno esistente.

> [!IMPORTANT]
> Ai clienti di Azure non verranno addebitati i costi di archiviazione associted con la diagnostica di avvio usando un account di archiviazione gestito fino al 2020 ottobre.

## <a name="boot-diagnostics-view"></a>Visualizzazione diagnostica di avvio
Nel pannello della macchina virtuale l'opzione diagnostica di avvio si trova nella sezione *supporto e risoluzione dei problemi* del portale di Azure. Selezionando diagnostica di avvio vengono visualizzate una schermata e informazioni sul log seriale. Il log seriale contiene la messaggistica del kernel e lo screenshot è uno snapshot dello stato corrente delle macchine virtuali. A seconda che la macchina virtuale esegua Windows o Linux, determina come dovrebbe apparire lo screenshot previsto. Per Windows, gli utenti visualizzeranno uno sfondo del desktop e per Linux, gli utenti visualizzeranno una richiesta di accesso.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Screenshot della diagnostica di avvio Linux":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Screenshot della diagnostica di avvio di Windows":::


## <a name="limitations"></a>Limitazioni
- La diagnostica di avvio è disponibile solo per le macchine virtuali Azure Resource Manager. 
- La diagnostica di avvio non supporta gli account di archiviazione Premium, se viene usato un account di archiviazione Premium per la diagnostica di avvio, gli utenti riceveranno un `StorageAccountTypeNotSupported` errore all'avvio della macchina virtuale. 

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [console seriale di Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview) e su come usare la diagnostica di avvio per [risolvere i problemi relativi alle macchine virtuali in Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics).
