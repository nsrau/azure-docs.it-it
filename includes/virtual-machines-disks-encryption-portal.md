---
title: includere il file
description: includere file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/23/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8956d4f5b2243cab433fcb3abaf2e71da8f8c772
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86229415"
---
Per configurare le chiavi gestite dal cliente per i dischi, è necessario creare le risorse in un ordine particolare, se si esegue questa operazione per la prima volta. Prima di tutto, è necessario creare e configurare un Azure Key Vault.

## <a name="set-up-your-azure-key-vault"></a>Impostare l'insieme di credenziali delle chiavi di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Cercare e selezionare **Key Vault**.

    [![server-side-encryption-key-vault-portal-search.png](media/virtual-machines-disk-encryption-portal/server-side-encryption-key-vault-portal-search.png)](media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > L'insieme di credenziali delle chiavi di Azure, la crittografia del disco, la macchina virtuale, i dischi e gli snapshot devono trovarsi nella stessa area e nella stessa sottoscrizione affinché la distribuzione abbia esito positivo.

1. Selezionare **+ Aggiungi** per creare una nuova Key Vault.
1. Creare un nuovo gruppo di risorse.
1. Immettere un nome dell'insieme di credenziali delle chiavi, selezionare un'area e selezionare un piano tariffario.
1. Selezionare **Verifica + crea**, verificare le scelte, quindi selezionare **Crea**.

    ![Screenshot dell'esperienza di creazione del Azure Key Vault. Visualizzazione dei valori specifici creati](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-vault.png)

1. Al termine della distribuzione dell'insieme di credenziali delle chiavi, selezionarlo.
1. Selezionare **chiavi** in **Impostazioni**.
1. Selezionare **Genera/Importa**.

    ![Screenshot del riquadro Impostazioni risorsa Key Vault. Mostra il pulsante genera/importa all'interno delle impostazioni.](media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-generate-settings.png)

1. Lasciare entrambi i **tipi di chiave** impostati su **RSA** e le **dimensioni della chiave RSA** impostate su **2048**.
1. Inserire le selezioni rimanenti desiderate e quindi selezionare **Crea**.

    ![Screenshot del pannello Crea chiave visualizzato dopo la selezione del pulsante genera/importa](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-generate.png)

## <a name="set-up-your-disk-encryption-set"></a>Configurare il set di crittografia del disco

1. Cercare i **set di crittografia del disco** e selezionarlo.
1. Nel pannello **set di crittografia del disco** selezionare **+ Aggiungi**.

    ![Screenshot della schermata principale del portale di crittografia del disco. Evidenziazione del pulsante Aggiungi](media/virtual-machines-disk-encryption-portal/sever-side-encryption-create-disk-encryption-set.png)

1. Selezionare il gruppo di risorse, denominare il set di crittografia e selezionare la stessa area dell'insieme di credenziali delle chiavi.
1. Selezionare Key **Vault e Key**.
1. Selezionare l'insieme di credenziali delle chiavi e la chiave creati in precedenza, nonché la versione.
1. Fare clic su **Seleziona**.
1. Selezionare **Verifica + crea** e quindi **Crea**.

    ![Screenshot del pannello di creazione della crittografia del disco. Visualizzazione di sottoscrizione, gruppo di risorse, nome del set di crittografia del disco, area e Key Vault + selettore di chiave.](media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-encryption-set-blade-key.png)

1. Aprire il set di crittografia del disco al termine della creazione e selezionare l'avviso visualizzato.

    ![Screenshot della finestra popup avviso:' per associare un disco, un'immagine o uno snapshot con un set di crittografia del disco, è necessario concedere le autorizzazioni all'insieme di credenziali delle chiavi '. Selezionare questo avviso per continuare](media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-encryption-set-alert-fix.png)

Due notifiche dovrebbero apparire e avere esito positivo. Questa operazione consentirà di usare la crittografia del disco impostata con l'insieme di credenziali delle chiavi.

![Screenshot dell'autorizzazione riuscita e dell'assegnazione di ruolo per l'insieme di credenziali delle chiavi.](media/virtual-machines-disk-encryption-portal/disk-encryption-notification-success.png)

## <a name="deploy-a-vm"></a>Distribuire una macchina virtuale

Ora che è stato creato e configurato l'insieme di credenziali delle chiavi e il set di crittografia del disco, è possibile distribuire una VM usando la crittografia.
Il processo di distribuzione delle VM è simile al processo di distribuzione standard. le uniche differenze sono la necessità di distribuire la macchina virtuale nella stessa area delle altre risorse e di scegliere di usare una chiave gestita dal cliente.

1. Cercare le **macchine virtuali** e selezionare **+ Aggiungi** per creare una macchina virtuale.
1. Nella scheda di **base** selezionare la stessa area del set di crittografia del disco e Azure Key Vault.
1. Inserire gli altri valori nella scheda di **base** , come si desidera.

    ![Screenshot dell'esperienza di creazione della macchina virtuale con il valore Region evidenziato.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-vm-region.png)

1. Nella scheda **dischi** selezionare crittografia inattiva **con una chiave gestita dal cliente**.
1. Selezionare la crittografia del disco impostata nell'elenco a discesa del **set di crittografia del disco** .
1. Eseguire le selezioni rimanenti desiderate.

    ![Screenshot dell'esperienza di creazione delle VM, il pannello dischi. Con l'elenco a discesa impostazione crittografia disco evidenziato.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-vm-select-customer-managed-key-disk-encryption-set.png)

## <a name="enable-on-an-existing-disk"></a>Abilita su un disco esistente

> [!CAUTION]
> Per abilitare la crittografia dei dischi in qualsiasi disco collegato a una macchina virtuale, sarà necessario arrestare la macchina virtuale.
    
1. Passare a una macchina virtuale che si trova nella stessa area di uno dei set di crittografia del disco.
1. Aprire la macchina virtuale e selezionare **Arresta**.

    ![Screenshot della sovrimpressione principale per la VM di esempio. Con il pulsante Interrompi evidenziato](media/virtual-machines-disk-encryption-portal/server-side-encryption-stop-vm-to-encrypt-disk-fix.png)

1. Al termine dell'arresto della macchina virtuale, selezionare **dischi** e quindi selezionare il disco che si desidera crittografare.

    ![Screenshot della VM di esempio con il pannello dischi aperto. Il disco del sistema operativo è evidenziato, come un disco di esempio da selezionare.](media/virtual-machines-disk-encryption-portal/server-side-encryption-existing-disk-select.png)

1. Selezionare **crittografia** e selezionare crittografia inattiva **con una chiave gestita dal cliente** , quindi selezionare la crittografia del disco impostata nell'elenco a discesa.
1. Selezionare **Salva**.

    ![Screenshot del disco del sistema operativo di esempio. Il pannello crittografia è aperto, la crittografia dei computer inattivi con una chiave gestita dal cliente è selezionata e l'esempio Azure Key Vault. Dopo aver effettuato queste selezioni, viene selezionato il pulsante Salva.](media/virtual-machines-disk-encryption-portal/server-side-encryption-encrypt-existing-disk-customer-managed-key.png)

1. Ripetere questo processo per tutti gli altri dischi collegati alla macchina virtuale che si vuole crittografare.
1. Al termine del trasferimento dei dischi alle chiavi gestite dal cliente, se non sono presenti altri dischi collegati che si vuole crittografare, è possibile avviare la macchina virtuale.
