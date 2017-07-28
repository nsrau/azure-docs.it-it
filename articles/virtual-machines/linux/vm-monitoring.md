---
title: Abilitare o disabilitare il monitoraggio delle VM di Azure
description: Descrive come abilitare o disabilitare il monitoraggio delle VM di Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: kmouss
manager: timlt
editor: 
ms.assetid: 6ce366d2-bd4c-4fef-a8f5-a3ae2374abcc
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/08/2016
ms.author: kmouss
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 9b2fe579113d6ca6bfd27d82eb9d4657657d44ff
ms.contentlocale: it-it
ms.lasthandoff: 05/25/2017

---
# <a name="enable-or-disable-azure-vm-monitoring"></a>Abilitare o disabilitare il monitoraggio delle VM di Azure

Questa sezione descrive come abilitare o disabilitare il monitoraggio nelle macchine virtuali in esecuzione in Azure. È possibile abilitare o disabilitare il monitoraggio usando il portale o l'interfaccia della riga di comando di Azure per Mac, Linux e Windows (l'interfaccia della riga di comando di Azure).

> [!IMPORTANT]
> Questo documento descrive la versione 2.3 dell'estensione Diagnostica per Linux, che è stata deprecata. La versione 2.3 sarà supportata fino al 30 giugno 2018.
>
> È invece possibile abilitare la versione 3.0 dell'estensione Diagnostica per Linux. Per altre informazioni, vedere [la documentazione](./diagnostic-extension.md).

## <a name="enable--disable-monitoring-through-the-azure-portal"></a>Abilitare/Disabilitare il monitoraggio dal portale di Azure

È possibile abilitare il monitoraggio della VM di Azure, che fornisce dati sull'istanza ogni minuto (sono incluse le modifiche dell'archivio). Dati di diagnostica dettagliati sono quindi disponibili per la VM nei grafici del portale o tramite l'API. Per impostazione predefinita, il portale di Azure consente di abilitare il monitoraggio basato su host di un set limitato di metriche. È possibile abilitare il monitoraggio delle metriche dalla macchina virtuale mentre questa si trova nello stato in corso di esecuzione o arrestato.

* Aprire il portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
* Nel riquadro di spostamento a sinistra fare clic su Macchine virtuali.
* Nell'elenco Macchine virtuali selezionare un'istanza in esecuzione o arrestata. Verrà visualizzato il pannello "Macchina virtuale".
* Fare clic su Tutte le impostazioni.
* Fare clic su Diagnostica.
* Impostare lo stato su Sì o No. In questo pannello è anche possibile selezionare il livello di dettagli di monitoraggio che si vuole abilitare per la macchina virtuale.

![Abilitare/Disabilitare il monitoraggio dal portale di Azure.][1]

## <a name="enable--disable-monitoring-with-azure-cli"></a>Abilitare/Disabilitare il monitoraggio con l'interfaccia della riga di comando di Azure

Per abilitare il monitoraggio per una VM di Azure.

* Creare un file denominato, ad esempio, PrivateConfig.json:

```json
{
        "storageAccountName":"the storage account to receive data",
        "storageAccountKey":"the key of the account"
}
```

* Abilitare l'estensione tramite l'interfaccia della riga di comando di Azure.

```azurecli
azure vm extension set myvm LinuxDiagnostic Microsoft.OSTCExtensions 2.3 --private-config-path PrivateConfig.json
```

Per altre informazioni vedere [Uso dell'estensione Diagnostica per Linux per monitorare le prestazioni e i dati di diagnostica di una macchina virtuale Linux](classic/diagnostic-extension-v2.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

<!--Image references-->
[1]: ./media/vm-monitoring/portal-enable-disable.png

