---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d4075f644ed1ce42479e35544f0e1442488f78eb
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
1. Accedere al [portale di Azure](https://portal.azure.com).

2. In altro a sinistra fare clic su **Crea una risorsa** > **Calcolo** > **Windows Server 2016 Datacenter**.

    ![Passare alle immagini della macchina virtuale di Azure nel portale](./media/virtual-machines-common-portal-create-fqdn/marketplace-new.png)

3. In Windows Server 2016 Datacenter selezionare il modello di distribuzione classica. Fare clic su Crea.

    ![Screenshot che illustra le immagini di VM di Azure disponibili nel portale](./media/virtual-machines-common-portal-create-fqdn/deployment-classic-model.png)

## <a name="1-basics-blade"></a>1. Pannello Nozioni di base

Il pannello delle nozioni di base richiede le informazioni amministrative per la macchina virtuale.

1. Inserire un **nome** per la macchina virtuale. In questo esempio _HeroVM_ è il nome della macchina virtuale. Il nome deve avere una lunghezza compresa tra 1 e 15 caratteri e non può contenere caratteri speciali.

2. Immettere un **Nome utente** e una **Password** complessa, da usare per creare un account locale nella VM. L'account locale viene usato per accedere alla macchina virtuale e gestirla. In questo esempio _azureuser_ è il nome utente.

 La password deve avere una lunghezza compresa tra 8 e 123 caratteri e soddisfare almeno tre dei quattro requisiti di complessità seguenti: una lettera minuscola, una lettera maiuscola, un numero e un carattere speciale. Sono disponibili altre informazioni sui [requisiti relativi a nome utente e password](../articles/virtual-machines/windows/faq.md).

3. La **sottoscrizione** è facoltativa. Un'impostazione comune è "Uso prepagato".

4. Selezionare un **gruppo di risorse** esistente o specificare il nome di un nuovo gruppo. In questo esempio _HeroVMRG_ è il nome del gruppo di risorse.

5. Selezionare una **località** nel datacenter di Azure in cui si desidera eseguire la macchina virtuale. In questo esempio **Stati Uniti orientali** è la località.

6. Al termine, fare clic su **Avanti** per passare al pannello successivo.

    ![Screenshot che illustra le impostazioni nel pannello Nozioni di base per la configurazione di una macchina virtuale di Azure](./media/virtual-machines-common-portal-create-fqdn/basics-blade-classic.png)

## <a name="2-size-blade"></a>2. Pannello Dimensioni

Con il pannello Dimensioni si identificano i dettagli di configurazione della macchina virtuale e si elencano varie opzioni che includono sistemi operativi, numero di processori, tipo di archiviazione su disco e costi di uso mensili stimati.  

Scegliere le dimensioni per la macchina virtuale e quindi fare clic su **Seleziona** per continuare. In questo esempio _DS1_\__V2 Standard_ è la dimensione della VM.

  ![Screenshot del pannello Dimensioni che mostra le dimensioni di VM di Azure disponibili per la selezione](./media/virtual-machines-common-portal-create-fqdn/vm-size-classic.png)


## <a name="3-settings-blade"></a>3. Pannello Impostazioni

Nel pannello Impostazioni è necessario definire le opzioni di archiviazione e di rete. È possibile accettare le impostazioni predefinite. Se necessario, Azure crea le voci appropriate.

Se sono state selezionate dimensioni di macchina virtuale che lo supportano, è possibile provare il servizio Archiviazione Premium di Azure selezionando Premium (SSD) nel Tipo di disco.

Al termine delle modifiche, fare clic su **OK**.

## <a name="4-summary-blade"></a>4. Pannello Riepilogo

Il pannello Riepilogo elenca le impostazioni specificate nei pannelli precedenti. Quando si è pronti per creare l'immagine, fare clic su **OK** .

 ![Il report del pannello di riepilogo offre impostazioni specifiche della macchina virtuale](./media/virtual-machines-common-portal-create-fqdn/summary-blade-classic.png)

Dopo la creazione della macchina virtuale, nel portale classico la nuova macchina virtuale viene elencata in **Tutte le risorse** e viene mostrato un riquadro della macchina virtuale nel dashboard. Vengono creati ed elencati anche il servizio cloud e l'account di archiviazione corrispondenti. La macchina virtuale e il servizio cloud vengono avviati automaticamente e viene indicato lo stato **In esecuzione**.

 ![Configurare l'agente di macchine virtuali e gli endpoint della macchina virtuale](./media/virtual-machines-common-portal-create-fqdn/portal-with-new-vm.png)
