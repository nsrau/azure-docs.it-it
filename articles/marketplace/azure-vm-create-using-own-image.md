---
title: Crea un'offerta di macchina virtuale di Azure in Azure Marketplace usando la tua immagine
description: Informazioni su come pubblicare un'offerta di macchina virtuale in Azure Marketplace usando un'immagine personalizzata.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: 42022d1204c3b524ee2e9ef2770f616fba89dc8c
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284303"
---
# <a name="how-to-create-a-virtual-machine-using-your-own-image"></a>Come creare una macchina virtuale usando un'immagine personalizzata

Questo articolo descrive come creare e distribuire un'immagine di macchina virtuale (VM) fornita dall'utente.

> [!NOTE]
> Prima di iniziare questa procedura, esaminare i [requisiti tecnici](marketplace-virtual-machines.md#technical-requirements) per le offerte di VM di Azure, inclusi i requisiti del disco rigido virtuale (VHD).

Per usare invece un'immagine di base approvata, seguire le istruzioni riportate in [creare un'immagine di macchina virtuale da una base approvata](azure-vm-create-using-approved-base.md).

## <a name="configure-the-vm"></a>Configurare la macchina virtuale

Questa sezione descrive come ridimensionare, aggiornare e generalizzare una macchina virtuale di Azure. Questa procedura è necessaria per preparare la macchina virtuale alla distribuzione in Azure Marketplace.

### <a name="size-the-vhds"></a>Ridimensionare i dischi rigidi virtuali

[!INCLUDE [Discussion of VHD sizing](includes/vhd-size.md)]

### <a name="install-the-most-current-updates"></a>Installare gli aggiornamenti più recenti

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>Eseguire controlli di sicurezza aggiuntivi

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Eseguire una configurazione personalizzata e attività pianificate

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

## <a name="upload-the-vhd-to-azure"></a>Caricare il disco rigido virtuale in Azure

Configurare e preparare la macchina virtuale da caricare come descritto in [preparare un disco rigido virtuale Windows o VHDX per il caricamento in Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) o [creare e caricare un disco rigido virtuale Linux](../virtual-machines/linux/create-upload-generic.md).

## <a name="extract-the-vhd-from-image-if-using-image-building-services"></a>Estrai il disco rigido virtuale dall'immagine (se si usano i servizi di creazione immagini)

Se si usa un servizio di creazione di immagini, ad esempio [Packer](https://www.packer.io/), potrebbe essere necessario estrarre il disco rigido virtuale dall'immagine. Non esiste un modo diretto per eseguire questa operazione. Sarà necessario creare una macchina virtuale ed estrarre il disco rigido virtuale dal disco della macchina virtuale.

### <a name="create-the-vm-on-the-azure-portal"></a>Creare la macchina virtuale nella portale di Azure

Seguire questa procedura per creare l'immagine di macchina virtuale di base nel [portale di Azure](https://ms.portal.azure.com/).

1. Accedere al [portale di Azure](https://ms.portal.azure.com/).
2. Selezionare **Macchine virtuali**.
3. Selezionare **+ Aggiungi** per aprire la schermata **Crea una macchina virtuale** .
4. Selezionare l'immagine nell'elenco a discesa oppure selezionare **Sfoglia tutte le immagini pubbliche e private** per cercare o esplorare tutte le immagini di macchine virtuali disponibili.
5. Per creare una macchina virtuale di **generazione 2** , passare alla scheda **Avanzate** e selezionare l'opzione **gen 2** .

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Selezionare generazione 1 o generazione 2.":::

6. Selezionare le dimensioni della macchina virtuale da distribuire.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Selezionare generazione 1 o generazione 2.":::

7. Indicare gli altri dettagli necessari per creare la macchina virtuale.
8. Selezionare **Review + create (Revisione e creazione)** per rivedere le scelte effettuate. Quando viene visualizzato il messaggio **convalida superata** , selezionare **Crea**.

Azure avvia il provisioning della macchina virtuale specificata. Tenere traccia dello stato di avanzamento selezionando la scheda **macchine virtuali** nel menu a sinistra. Dopo aver creato lo stato delle modifiche della macchina virtuale in **esecuzione**.

### <a name="connect-to-your-vm"></a>Connettersi alla macchina virtuale

Per connettersi alla VM [Windows](../virtual-machines/windows/connect-logon.md) o [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) , vedere la documentazione seguente.

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Passaggi successivi

- Passaggio successivo consigliato: [testare l'immagine della macchina virtuale](azure-vm-image-test.md) per verificare che soddisfi i requisiti di pubblicazione in Azure Marketplace. Questo valore è facoltativo.
- Se non si testa l'immagine della macchina virtuale, continuare con [generare l'URI](azure-vm-get-sas-uri.md)di firma di accesso condiviso.
- Se si riscontrano difficoltà nella creazione del nuovo VHD basato su Azure, vedere [domande frequenti sulle VM per Azure Marketplace](azure-vm-create-faq.md).
