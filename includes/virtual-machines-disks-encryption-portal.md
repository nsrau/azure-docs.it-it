---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 165a115f35810c1bfe463a571affb2e44ed74205
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75893794"
---
### <a name="portal"></a>Portale

Per configurare le chiavi gestite dal cliente per i dischi, è necessario creare le risorse in un ordine particolare, se si esegue questa operazione per la prima volta. Prima di tutto, è necessario creare e configurare un Azure Key Vault.

#### <a name="setting-up-your-azure-key-vault"></a>Impostazione della Azure Key Vault

1. Accedere al [portale di Azure](https://portal.azure.com/) e cercare Key Vault
1. Cercare e selezionare **Key Vault**.

![SSE-Key-Vault-Portal-search. png](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search.png)

> [!IMPORTANT]
> L'insieme di credenziali delle chiavi di Azure, la crittografia del disco, la macchina virtuale, i dischi e gli snapshot devono trovarsi nella stessa area e nella stessa sottoscrizione affinché la distribuzione abbia esito positivo.

1. Selezionare **+ Aggiungi** per creare una nuova Key Vault.
1. Creare un nuovo gruppo di risorse
1. Immettere un nome dell'insieme di credenziali delle chiavi, selezionare un'area e selezionare un piano tariffario.
1. Selezionare **Verifica + crea**, verificare le scelte, quindi selezionare **Crea**.

![SSE-create-a-Key-Vault. png](media/virtual-machines-disk-encryption-portal/sse-create-a-key-vault.png)

1. Al termine della distribuzione dell'insieme di credenziali delle chiavi, selezionarlo.
1. Selezionare **chiavi** in **Impostazioni**.
1. Selezionare **genera/importa**

![SSE-Key-Vault-generate-Settings. png](media/virtual-machines-disk-encryption-portal/sse-key-vault-generate-settings.png)

1. Mantieni entrambi i **tipi di chiave** impostati su **RSA** e le **dimensioni della chiave RSA** impostate su **2080**.
1. Inserire le selezioni rimanenti desiderate e quindi selezionare **Crea**.

![SSE-create-a-Key-generate. png](media/virtual-machines-disk-encryption-portal/sse-create-a-key-generate.png)

#### <a name="setting-up-your-disk-encryption-set"></a>Impostazione del set di crittografia del disco

Per creare e configurare i set di crittografia del disco, è necessario usare il collegamento seguente: https://aka.ms/diskencryptionsets. La creazione del set di crittografia del disco non è ancora disponibile nel portale di Azure pubblico.

1. Aprire il [collegamento dei set di crittografia del disco](https://aka.ms/diskencryptionsets).
1. Selezionare **+Aggiungi**.

![SSE-create-Disk-Encryption-set. png](media/virtual-machines-disk-encryption-portal/sse-create-disk-encryption-set.png)

1. Selezionare il gruppo di risorse, denominare il set di crittografia e selezionare la stessa area dell'insieme di credenziali delle chiavi.
1. Selezionare Key **Vault e Key**.
1. Selezionare l'insieme di credenziali delle chiavi e la chiave creati in precedenza, nonché la versione.
1. Fare clic su **Seleziona**.
1. Selezionare **Verifica + crea** e quindi **Crea**.

![SSE-disk-ENC-set-Blade-Key. png](media/virtual-machines-disk-encryption-portal/sse-disk-enc-set-blade-key.png)

1. Aprire il set di crittografia del disco al termine della creazione e selezionare l'avviso visualizzato.

![SSE-disk-ENC-Alert-FIX. png](media/virtual-machines-disk-encryption-portal/sse-disk-enc-alert-fix.png)

Due notifiche dovrebbero apparire e avere esito positivo. Questa operazione consentirà di usare il set con l'insieme di credenziali delle chiavi.

![Disk-ENC-Notification-success. png](media/virtual-machines-disk-encryption-portal/disk-enc-notification-success.png)

#### <a name="deploy-a-vm"></a>Distribuire una macchina virtuale

Ora che è stato creato e configurato l'insieme di credenziali delle chiavi e il set di crittografia del disco, è possibile distribuire una VM usando la crittografia.
Il processo di distribuzione delle VM è simile al processo di distribuzione standard. le uniche differenze sono la necessità di distribuire la macchina virtuale nella stessa area delle altre risorse e di scegliere di usare una chiave gestita dal cliente.

1. Cercare le **macchine virtuali** e selezionare **+ Aggiungi** per creare una macchina virtuale.
1. Nella scheda di **base** selezionare la stessa area del set di crittografia del disco e Azure Key Vault.
1. Inserire gli altri valori nella scheda di **base** , come si desidera.

![SSE-create-a-VM-Region. png](media/virtual-machines-disk-encryption-portal/sse-create-a-vm-region.png)

1. Nella scheda **dischi** selezionare crittografia inattiva **con chiave gestita dal cliente**.
1. Selezionare la crittografia del disco impostata nell'elenco a discesa del **set di crittografia del disco** .
1. Eseguire le selezioni rimanenti desiderate.

![SSE-create-VM-Select-CMK-Encryption-set. png](media/virtual-machines-disk-encryption-portal/sse-create-vm-select-cmk-encryption-set.png)