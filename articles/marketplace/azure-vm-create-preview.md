---
title: Aggiungere un pubblico di anteprima per un'offerta di macchina virtuale in Azure Marketplace
description: Informazioni su come aggiungere un pubblico di anteprima per un'offerta di macchina virtuale in Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 854d0fa9b34d495f03a0c3c6203ceb227e4712d2
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629513"
---
# <a name="how-to-add-a-preview-audience-for-a-virtual-machine-offer"></a>Come aggiungere un gruppo di destinatari di anteprima per un'offerta di macchina virtuale

Nella pagina di **Anteprima** (selezionare dal menu di spostamento a sinistra nel centro per i partner) selezionare un gruppo di **destinatari di anteprima** limitato per la convalida dell'offerta prima della pubblicazione in tempo reale per i destinatari più ampi del Marketplace commerciale.

> [!IMPORTANT]
> Dopo aver controllato l'offerta nel riquadro **Anteprima** , selezionare **Go Live** per pubblicare l'offerta per il pubblico del marketplace commerciale.

I destinatari dell'anteprima sono identificati dai GUID di **ID sottoscrizione di Azure** , insieme a una **Descrizione** facoltativa per ogni utente. Nessuno di questi campi viene visualizzato dai clienti. È possibile trovare l'ID sottoscrizione di Azure nella pagina **Sottoscrizioni** del portale di Azure.

Aggiungere almeno un ID sottoscrizione di Azure, singolarmente (fino a 10 ID) o caricando un file CSV (fino a 100 ID). Aggiungendo questi ID sottoscrizione, è possibile definire chi può visualizzare l'anteprima dell'offerta prima che venga pubblicata. Se l'offerta è già stata pubblicata, è possibile comunque definire un gruppo di destinatari dell'anteprima per testare le modifiche o gli aggiornamenti dell'offerta.

> [!NOTE]
> Il gruppo di destinatari dell'anteprima è diverso dal gruppo di destinatari privato. Il gruppo di destinatari dell'anteprima può accedere all'offerta *prima* che venga pubblicata in Azure Marketplace. Il gruppo di destinatari dell'anteprima può visualizzare e convalidare tutti i piani, inclusi quelli che saranno disponibili solo a un gruppo di destinatari privato dopo la pubblicazione dell'offerta in Azure Marketplace. Il gruppo di destinatari privato (definito nel riquadro **Prezzi e disponibilità** del piano) dispone di accesso esclusivo a un piano specifico.

Se sono state apportate modifiche, selezionare **Salva bozza** prima di continuare con la scheda successiva nel menu di navigazione a sinistra, **piano Panoramica**.

## <a name="next-steps"></a>Passaggi successivi

- [Creare piani](azure-vm-create-plans.md)
