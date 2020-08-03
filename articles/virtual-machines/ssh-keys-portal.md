---
title: Creare chiavi SSH nel portale di Azure
description: Informazioni su come generare e archiviare le chiavi SSH nel portale di Azure per la connessione delle macchine virtuali Linux.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/31/2020
ms.author: cynthn
ms.openlocfilehash: 39dbf7d9ad933dd47f0a566f02b5e276e4b615a3
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2020
ms.locfileid: "87514450"
---
# <a name="generate-and-store-ssh-keys-in-the-azure-portal"></a>Generare e archiviare le chiavi SSH nel portale di Azure

Se si usa spesso il portale per distribuire macchine virtuali Linux, è possibile fare in modo che l'uso di chiavi SSH risulti più semplice creandoli direttamente nel portale o caricando tali VM dal computer.

È possibile creare chiavi SSH quando si crea una macchina virtuale per la prima volta e riutilizzarle per altre macchine virtuali. In alternativa, è possibile creare chiavi SSH separatamente, in modo da avere un set di chiavi archiviate in Azure per soddisfare le esigenze delle organizzazioni. 

Se si dispone di chiavi esistenti e si desidera semplificarne l'utilizzo nel portale, è possibile caricarle e archiviarle in Azure per riutilizzarle.

Per informazioni più dettagliate sulla creazione e l'uso di chiavi SSH con macchine virtuali Linux, vedere (usare le chiavi SSH per connettersi alle macchine virtuali Linux) [./Linux/SSH-from-Windows.MD].

## <a name="generate-new-keys"></a>Genera nuove chiavi

1. Aprire [portale di Azure] ( https://portal.azure.com .

1. Nella parte superiore della pagina digitare *SSH* per eseguire la ricerca. In **Marketplace*selezionare **chiavi SSH**.

1. Nella pagina **chiave SSH** selezionare **Crea**.

:::image type="content" source="./media/ssh-keys/portal-sshkey.png" alt-text="Creare un nuovo gruppo di risorse e generare una coppia di chiavi SSH":::

1. In **gruppo di risorse** selezionare **Crea nuovo** per creare un nuovo gruppo di risorse in cui archiviare le chiavi. Digitare un nome per il gruppo di risorse e fare clic su **OK**.

1. In **area** selezionare un'area in cui archiviare le chiavi. È possibile usare le chiavi in qualsiasi area, ma è solo l'area in cui verranno archiviate.

1. Digitare un nome per la chiave nel **nome della coppia di chiavi**.

1. In **origine chiave pubblica SSH**selezionare **genera origine chiave pubblica**. 

1. Al termine, selezionare **Rivedi e crea**.

1. Dopo la convalida selezionare **Crea**.

1. Si otterrà quindi una finestra popup, selezionare **Scarica chiave privata e Crea risorsa**. La chiave SSH verrà scaricata come file con estensione PEM.

:::image type="content" source="./media/ssh-keys/download-key.png" alt-text="Scaricare la chiave privata come file con estensione PEM":::

1. Una volta scaricato il file con estensione PEM, potrebbe essere necessario spostarlo in un punto qualsiasi del computer in cui è facile puntare dal client SSH.


## <a name="connect-to-the-vm"></a>Connettersi alla VM

Nel computer locale aprire un prompt di PowerShell e digitare:

```powershell
ssh -i <path to the .pem file> username@<ipaddress of the VM>
```

Digitare ad esempio: `ssh -i /Downloads/mySSHKey.pem azureuser@123.45.67.890`


## <a name="upload-an-ssh-key"></a>Caricare una chiave SSH

È anche possibile caricare una chiave SSH pubblica da archiviare in Azure. Per informazioni su come creare una coppia di chiavi SSH, vedere [usare le chiavi SSH per connettersi alle macchine virtuali Linux ](./linux/ssh-from-windows.md).

1. Aprire [portale di Azure] ( https://portal.azure.com .

1. Nella parte superiore della pagina digitare *SSH* per eseguire la ricerca. In **Marketplace*selezionare **chiavi SSH**.

1. Nella pagina **chiave SSH** selezionare **Crea**.

:::image type="content" source="./media/ssh-keys/upload.png" alt-text="Caricare una chiave pubblica SSH da archiviare in Azure":::

1. In **gruppo di risorse** selezionare **Crea nuovo** per creare un nuovo gruppo di risorse in cui archiviare le chiavi. Digitare un nome per il gruppo di risorse e fare clic su **OK**.

1. In **area** selezionare un'area in cui archiviare le chiavi. È possibile usare le chiavi in qualsiasi area, ma è solo l'area in cui verranno archiviate.

1. Digitare un nome per la chiave nel **nome della coppia di chiavi**.

1. In **origine chiave pubblica SSH**selezionare **carica chiave pubblica esistente**. 

1. Incollare il contenuto completo della chiave pubblica nella **chiave di caricamento** , quindi selezionare **Verifica + crea**.

1. Al termine della convalida selezionare **Crea**. 

Una volta caricata la chiave, è possibile scegliere di usarla quando si crea una macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso delle chiavi SSH con le macchine virtuali di Azure, vedere (usare le chiavi SSH per connettersi alle macchine virtuali Linux) [./Linux/SSH-from-Windows.MD].