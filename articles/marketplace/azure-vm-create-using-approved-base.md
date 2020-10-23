---
title: Creare un'offerta di macchina virtuale (VM) di Azure da una base approvata, Azure Marketplace
description: Informazioni su come creare un'offerta di macchina virtuale (VM) da una base approvata.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: 34d64d70c657712bb44aa6331b53f014349a0dda
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284319"
---
# <a name="how-to-create-a-virtual-machine-using-an-approved-base"></a>Come creare una macchina virtuale usando una base approvata

Questo articolo descrive come usare Azure per creare una macchina virtuale (VM) contenente un sistema operativo già configurato e approvato. Se questo non è compatibile con la soluzione, è possibile [creare e configurare una macchina virtuale locale](azure-vm-create-using-own-image.md) usando un sistema operativo approvato, quindi configurarla e prepararla per il caricamento come descritto in [preparare un disco rigido virtuale Windows o VHDX per il caricamento in Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md).

> [!NOTE]
> Prima di iniziare questa procedura, esaminare i [requisiti tecnici](marketplace-virtual-machines.md#technical-requirements) per le offerte di VM di Azure, inclusi i requisiti del disco rigido virtuale (VHD).

## <a name="select-an-approved-base-image"></a>Selezionare un'immagine di base approvata

Selezionare una delle seguenti immagini Windows o Linux come base.

### <a name="windows"></a>Windows

- [Windows Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)
- SQL Server [2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [2014](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2014sp3-ws2012r2?tab=Overview), [2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2012sp4-ws2012r2?tab=Overview)

### <a name="linux"></a>Linux

Azure offre una gamma di distribuzioni Linux approvate. Per un elenco aggiornato, vedere [Linux in distribuzioni approvate da Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

## <a name="create-vm-on-the-azure-portal"></a>Creare una macchina virtuale nella portale di Azure

1. Accedere al [portale di Azure](https://ms.portal.azure.com/).
2. Selezionare **Macchine virtuali**.
3. Selezionare **+ Aggiungi** per aprire la schermata **Crea una macchina virtuale** .
4. Selezionare l'immagine nell'elenco a discesa oppure selezionare **Sfoglia tutte le immagini pubbliche e private** per cercare o esplorare tutte le immagini di macchine virtuali disponibili.
5. Per creare una macchina virtuale di **generazione 2** , passare alla scheda **Avanzate** e selezionare l'opzione **gen 2** .

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Selezionare generazione 1 o generazione 2.":::

6. Selezionare le dimensioni della macchina virtuale da distribuire.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Selezionare generazione 1 o generazione 2.":::

7. Indicare gli altri dettagli necessari per creare la macchina virtuale.
8. Selezionare **Review + create (Revisione e creazione)** per rivedere le scelte effettuate. Quando viene visualizzato il messaggio **convalida superata** , selezionare  **Crea**.

Azure avvia il provisioning della macchina virtuale specificata. Tenere traccia dello stato di avanzamento selezionando la scheda **macchine virtuali** nel menu a sinistra. Dopo la creazione, lo stato della macchina virtuale viene modificato in **in esecuzione**.


## <a name="configure-the-vm"></a>Configurare la macchina virtuale

Questa sezione descrive come ridimensionare, aggiornare e generalizzare una macchina virtuale di Azure. Questa procedura è necessaria per preparare la macchina virtuale alla distribuzione in Azure Marketplace.

### <a name="connect-to-your-vm"></a>Connettersi alla macchina virtuale

Per connettersi alla VM [Windows](../virtual-machines/windows/connect-logon.md) o [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) , vedere la documentazione seguente.

### <a name="install-the-most-current-updates"></a>Installare gli aggiornamenti più recenti

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>Eseguire controlli di sicurezza aggiuntivi

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Eseguire una configurazione personalizzata e attività pianificate

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Passaggi successivi

- Passaggio successivo consigliato: [testare l'immagine della macchina virtuale](azure-vm-image-test.md) per verificare che soddisfi i requisiti di pubblicazione in Azure Marketplace. Questo valore è facoltativo.
- Se non si testa l'immagine della macchina virtuale, continuare con [generare l'URI](azure-vm-get-sas-uri.md)di firma di accesso condiviso.
- Se si riscontrano difficoltà nella creazione del nuovo VHD basato su Azure, vedere [domande frequenti sulle VM per Azure Marketplace](azure-vm-create-faq.md).
