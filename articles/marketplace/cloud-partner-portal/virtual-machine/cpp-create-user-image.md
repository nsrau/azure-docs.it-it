---
title: Creare un'immagine di macchina virtuale per Azure Marketplace
description: Sono elencati i passaggi e i riferimenti richiesti per creare un'immagine di macchina virtuale degli utenti.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: 0005ab517d38903b87889b67449569495e396265
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938321"
---
# <a name="create-a-user-vm-image"></a>Creare un'immagine di macchina virtuale degli utenti

Questo articolo illustra i due passaggi generali necessari per creare un'immagine non gestita da un disco rigido virtuale generalizzato.  Vengono inoltre forniti i riferimenti per ogni passaggio: acquisire l'immagine e generalizzare l'immagine.


## <a name="capture-the-vm-image"></a>Acquisire l'immagine della macchina virtuale

Seguire le istruzioni indicate nell'articolo seguente sull'acquisizione della macchina virtuale che corrisponde all'acceso usato:

-  PowerShell: [Come creare un'immagine di macchina virtuale non gestita da una macchina virtuale di Azure](../../../virtual-machines/windows/capture-image-resource.md)
-  Interfaccia della riga di comando di Azure: [Come creare un'immagine di una macchina virtuale o un disco rigido virtuale](../../../virtual-machines/linux/capture-image.md)
-  API: [Virtual Machines - Capture](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture) (Macchine virtuali - Acquisizione)


## <a name="generalize-the-vm-image"></a>Generalizzare l'immagine di macchina virtuale

Poiché l'immagine dell'utente è stata generata da un disco rigido virtuale generalizzato in precedenza, è necessario che sia generalizzata.  Anche in questo caso selezionare l'articolo seguente che corrisponde al meccanismo di accesso.  Il disco potrebbe già essere stato generalizzato al momento dell'acquisizione.

-  PowerShell: [Generalizzare la macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Interfaccia della riga di comando di Azure: [Passaggio 2: Creare l'immagine della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API: [Virtual Machines - Generalize](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize) (Macchine virtuali - Generalizzazione)


## <a name="next-steps"></a>Passaggi successivi

Nel passaggio successivo si apprenderà come [creare un certificato](cpp-create-key-vault-cert.md) e archiviarlo in un nuovo insieme di credenziali delle chiavi di Azure.  Questo certificato è necessario per stabilire una connessione WinRM sicura con la macchina virtuale.
