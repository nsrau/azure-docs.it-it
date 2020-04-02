---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/11/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6077db0a09b09f7e4bfb859902da53b173845e55
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520778"
---
### <a name="portal"></a>Portale

L'impostazione di chiavi gestite dal cliente per i dischi richiederà di creare risorse in un ordine particolare, se lo si esegue per la prima volta. In primo luogo, è necessario creare e configurare un insieme di credenziali delle chiavi di Azure.First, you will need to create and set up an Azure Key Vault.

#### <a name="setting-up-your-azure-key-vault"></a>Configurazione dell'insieme di credenziali delle chiavi di AzureSetting up your Azure Key Vault

1. Accedere al portale di [Azure](https://portal.azure.com/) e cercare Key Vault
1. Cercare e selezionare **Key Vaults**.

    [![sse-key-vault-portal-search.png](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search.png)](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > Affinché la distribuzione abbia esito positivo, l'insieme di credenziali delle chiavi di Azure, il set di crittografia del disco, la macchina virtuale, i dischi e gli snapshot devono trovarsi tutti nella stessa area e nella stessa sottoscrizione.

1. Per creare un nuovo insieme di credenziali delle chiavi, **selezionare Aggiungi.**
1. Creare un nuovo gruppo di risorse
1. Immettere un nome dell'insieme di credenziali delle chiavi, selezionare un'area e selezionare un piano tariffario.
1. Seleziona **Rivedi e crea**, verifica le tue scelte, quindi seleziona **Crea**.

    ![Screenshot dell'esperienza di creazione dell'insieme di chiavi di Azure.Screenshot of the Azure Key Vault creation experience. Visualizzazione dei valori specifici creati](media/virtual-machines-disk-encryption-portal/sse-create-a-key-vault.png)

1. Al termine della distribuzione dell'insieme di credenziali delle chiavi, selezionarlo.
1. Selezionare **Tasti** in **Impostazioni**.
1. Selezionare **Genera/Importa**

    ![Screenshot del riquadro Impostazioni delle risorse dell'insieme di credenziali delle chiavi. Mostra il pulsante Genera/Importa all'interno delle impostazioni.](media/virtual-machines-disk-encryption-portal/sse-key-vault-generate-settings.png)

1. Lasciare entrambi tipo **di chiave** impostato su **RSA** e **Dimensione chiave RSA** impostata su **2048**.
1. Compilare le selezioni rimanenti desiderate e quindi selezionare **Crea**.

    ![Screenshot del pulsante crea un pannello chiave che viene visualizzato dopo la generazione/importazione del pulsante](media/virtual-machines-disk-encryption-portal/sse-create-a-key-generate.png)

#### <a name="setting-up-your-disk-encryption-set"></a>Configurazione del set di crittografia del disco

Per creare e configurare set di crittografia https://aka.ms/diskencryptionsetsdel disco, è necessario utilizzare il seguente collegamento: . Se ci si trova nelle aree di governo [https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)di Microsoft Azure, è necessario utilizzare questo collegamento: . La creazione del set di crittografia del disco non è ancora disponibile nel portale globale di Azure.Disk encryption set creation is not yet available in the global Azure portal.

1. Aprire il collegamento ai set di crittografia del disco appropriato per l'area geografica:

    Regioni pubbliche:[https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Aree di Azure per enti pubblici:Azure Government regions:[https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)
    
1. Selezionare **: Aggiungi**.

    ![Screenshot della schermata principale del portale di crittografia del disco. Evidenziazione del pulsante Aggiungi](media/virtual-machines-disk-encryption-portal/sse-create-disk-encryption-set.png)

1. Selezionare il gruppo di risorse, assegnare un nome al set di crittografia e selezionare la stessa area dell'insieme di credenziali delle chiavi.
1. Selezionare **Key vault e key**.
1. Selezionare l'insieme di credenziali delle chiavi e la chiave create in precedenza, nonché la versione.
1. Fare clic su **Seleziona**.
1. Selezionare **Revisione e Crea,** quindi **Crea**.

    ![Screenshot del pannello di creazione della crittografia del disco. Visualizzazione della sottoscrizione, del gruppo di risorse, del nome del set di crittografia del disco, dell'area e dell'insieme di credenziali delle chiavi e del selettore di chiave.](media/virtual-machines-disk-encryption-portal/sse-disk-enc-set-blade-key.png)

1. Aprire il set di crittografia del disco al termine della creazione e selezionare l'avviso visualizzato.

    ![Screenshot del popup di avviso: 'Per associare un disco, un'immagine o uno snapshot a un set di crittografia del disco, è necessario concedere autorizzazioni per l'insieme di credenziali delle chiavi'. Selezionare questo avviso per continuare](media/virtual-machines-disk-encryption-portal/sse-disk-enc-alert-fix.png)

Due notifiche dovrebbero apparire e avere esito positivo. In questo modo sarà possibile utilizzare il set di crittografia del disco con l'insieme di credenziali delle chiavi.

![Screenshot dell'autorizzazione e dell'assegnazione di ruolo per l'insieme di credenziali delle chiavi.](media/virtual-machines-disk-encryption-portal/disk-enc-notification-success.png)

#### <a name="deploy-a-vm"></a>Distribuire una macchina virtuale

Dopo aver creato e configurato l'insieme di credenziali delle chiavi e il set di crittografia del disco, è possibile distribuire una macchina virtuale usando la crittografia.
Il processo di distribuzione della macchina virtuale è simile al processo di distribuzione standard, le uniche differenze sono che è necessario distribuire la macchina virtuale nella stessa area delle altre risorse e si sceglie di usare una chiave gestita dal cliente.

1. Aprire il collegamento ai set di crittografia del disco appropriato per l'area geografica:

    Regioni pubbliche:[https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Aree di Azure per enti pubblici:Azure Government regions:[https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)

1. Cercare **Macchine virtuali** e selezionare **Aggiungi** per creare una macchina virtuale.
1. Nella scheda Basic selezionare la stessa area del set di crittografia del disco e dell'insieme di chiavi di Azure.On the **Basic** tab, select the same region as your disk encryption set and Azure Key Vault.
1. Compilare gli altri valori nella scheda **Base** come si desidera.

    ![Screenshot dell'esperienza di creazione della macchina virtuale, con il valore dell'area evidenziato.](media/virtual-machines-disk-encryption-portal/sse-create-a-vm-region.png)

1. Nella scheda **Dischi** selezionare **Crittografia inattivi con una chiave gestita dal cliente.**
1. Selezionare il set di crittografia del disco nell'elenco a discesa Set di **crittografia del** disco.
1. Effettuare le selezioni rimanenti come si desidera.

    ![Screenshot dell'esperienza di creazione della macchina virtuale, il blade dei dischi. Con il menu a discesa del set di crittografia del disco evidenziato.](media/virtual-machines-disk-encryption-portal/sse-create-vm-select-cmk-encryption-set.png)

#### <a name="enable-on-an-existing-disk"></a>Abilitare su un disco esistente

Per gestire e configurare la crittografia del disco nei https://aka.ms/diskencryptionsetsdischi esistenti, è necessario utilizzare il seguente collegamento: . L'abilitazione delle chiavi gestite dal cliente nei dischi esistenti non è ancora disponibile nel portale globale di Azure.Enabling customer-managed keys on existing disks is not yet available in the global Azure portal.

> [!CAUTION]
> L'abilitazione della crittografia del disco in tutti i dischi collegati a una macchina virtuale richiederà l'arresto della macchina virtuale.

1. Aprire il collegamento ai set di crittografia del disco appropriato per l'area geografica:

    Regioni pubbliche:[https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Aree di Azure per enti pubblici:Azure Government regions:[https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)
    
1. Passare a una macchina virtuale che si trova nella stessa area di uno dei set di crittografia del disco.
1. Aprire la macchina virtuale e selezionare **Arresta**.

    ![Screenshot dell'overlay principale per la macchina virtuale di esempio. Con il pulsante Interrompi evidenziato](media/virtual-machines-disk-encryption-portal/sse-stop-VM-to-encrypt-disk.png)

1. Al termine dell'arresto della macchina virtuale, selezionare **Dischi** e quindi selezionare il disco da crittografare.

    ![Screenshot della macchina virtuale di esempio con il pannello Dischi aperto. Il disco del sistema operativo è evidenziato, come disco di esempio da selezionare.](media/virtual-machines-disk-encryption-portal/sse-existing-disk-select.png)

1. Selezionare **Crittografia** e **Crittografia inattivi con una chiave gestita dal cliente,** quindi selezionare la crittografia del disco impostata nell'elenco a discesa.
1. Selezionare **Save** (Salva).

    ![Screenshot del disco del sistema operativo di esempio. Il pannello di crittografia è aperto, viene selezionata la crittografia inattivi con una chiave gestita dal cliente, nonché l'esempio di insieme di credenziali delle chiavi di Azure.The encryption blade is open, encryption at rest with a customer-managed key is selected, as well as your example Azure Key Vault. Dopo aver effettuato tali selezioni, viene selezionato il pulsante Salva.](media/virtual-machines-disk-encryption-portal/sse-encrypt-existing-disk-customer-managed-key.png)

1. Ripetere questo processo per tutti gli altri dischi collegati alla macchina virtuale che si vuole crittografare.
1. Quando i dischi completano il passaggio alle chiavi gestite dal cliente, se non sono presenti altri dischi collegati che si desidera crittografare, è possibile avviare la macchina virtuale.
