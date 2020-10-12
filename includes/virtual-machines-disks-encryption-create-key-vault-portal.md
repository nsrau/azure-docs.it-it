---
title: includere file
description: includere file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/23/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a967777b65c06cf23239a47e8e691fb3a29231b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88815471"
---
Per configurare le chiavi gestite dal cliente per i dischi, è necessario creare le risorse in un ordine particolare, se si esegue questa operazione per la prima volta. Prima di tutto, è necessario creare e configurare un Azure Key Vault.

## <a name="set-up-your-azure-key-vault"></a>Impostare l'insieme di credenziali delle chiavi di Azure

1. Accedere al [portale di Azure](https://aka.ms/diskencryptionupdates).
1. Cercare e selezionare **Key Vault**.

    [![Screenshot della portale di Azure con la finestra di dialogo Cerca espansa.](./media/virtual-machines-disk-encryption-portal/server-side-encryption-key-vault-portal-search.png)](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > L'insieme di credenziali delle chiavi di Azure, la crittografia del disco, la macchina virtuale, i dischi e gli snapshot devono trovarsi nella stessa area e nella stessa sottoscrizione affinché la distribuzione abbia esito positivo.

1. Selezionare **+ Aggiungi** per creare una nuova Key Vault.
1. Creare un nuovo gruppo di risorse.
1. Immettere un nome dell'insieme di credenziali delle chiavi, selezionare un'area e selezionare un piano tariffario.

    > [!NOTE]
    > Quando si crea l'istanza di Key Vault, è necessario abilitare l'eliminazione temporanea e la protezione dall'eliminazione. L'eliminazione temporanea assicura che l'insieme di credenziali delle chiavi mantenga una chiave eliminata per un determinato periodo di conservazione (valore predefinito di 90 giorni). La protezione dall'eliminazione garantisce che una chiave eliminata non possa essere eliminata definitivamente fino a quando non scade il periodo di conservazione. Queste impostazioni consentono di evitare la perdita di dati a causa dell'eliminazione accidentale. Queste impostazioni sono obbligatorie quando si usa un insieme di credenziali delle chiavi per la crittografia dei dischi gestiti.

1. Selezionare **Verifica + crea**, verificare le scelte, quindi selezionare **Crea**.

    ![Screenshot dell'esperienza di creazione del Azure Key Vault. Visualizzazione dei valori specifici creati](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-vault.png)

1. Al termine della distribuzione dell'insieme di credenziali delle chiavi, selezionarlo.
1. Selezionare **chiavi** in **Impostazioni**.
1. Selezionare **Genera/Importa**.

    ![Screenshot del riquadro Impostazioni risorsa Key Vault. Mostra il pulsante genera/importa all'interno delle impostazioni.](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-generate-settings.png)

1. Lasciare entrambi i **tipi di chiave** impostati su **RSA** e le **dimensioni della chiave RSA** impostate su **2048**.
1. Inserire le selezioni rimanenti desiderate e quindi selezionare **Crea**.

    ![Screenshot del pannello Crea chiave visualizzato dopo la selezione del pulsante genera/importa](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-generate.png)

## <a name="set-up-your-disk-encryption-set"></a>Configurare il set di crittografia del disco

1. Cercare i **set di crittografia del disco** e selezionarlo.
1. Nel pannello **set di crittografia del disco** selezionare **+ Aggiungi**.

    ![Screenshot della schermata principale del portale di crittografia del disco. Evidenziazione del pulsante Aggiungi](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-create-disk-encryption-set.png)

1. Selezionare il gruppo di risorse, denominare il set di crittografia e selezionare la stessa area dell'insieme di credenziali delle chiavi.
1. Per **tipo di crittografia** selezionare crittografia inattiva **con una chiave gestita dal cliente**.

    > [!NOTE]
    > Una volta creato un set di crittografia del disco con un tipo di crittografia particolare, non è possibile modificarlo. Se si desidera utilizzare un tipo di crittografia diverso, è necessario creare un nuovo set di crittografia del disco.

1. Selezionare **fare clic per selezionare una chiave**.
1. Selezionare l'insieme di credenziali delle chiavi e la chiave creati in precedenza, nonché la versione.
1. Fare clic su **Seleziona**.
1. Selezionare **Rivedi e crea** e quindi **Crea**.

    ![Screenshot del pannello di creazione della crittografia del disco. Visualizzazione di sottoscrizione, gruppo di risorse, nome del set di crittografia del disco, area e Key Vault + selettore di chiave.](./media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-set-blade.png)

1. Aprire il set di crittografia del disco al termine della creazione e selezionare l'avviso visualizzato.

    ![Screenshot della finestra popup avviso:' per associare un disco, un'immagine o uno snapshot con un set di crittografia del disco, è necessario concedere le autorizzazioni all'insieme di credenziali delle chiavi '. Selezionare questo avviso per continuare](./media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-encryption-set-alert-fix.png)

    Due notifiche dovrebbero apparire e avere esito positivo. Questo consente di usare la crittografia del disco impostata con l'insieme di credenziali delle chiavi.

    ![Screenshot dell'autorizzazione riuscita e dell'assegnazione di ruolo per l'insieme di credenziali delle chiavi.](./media/virtual-machines-disk-encryption-portal/disk-encryption-notification-success.png)