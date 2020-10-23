---
title: Aggiungere un pubblico di anteprima per un'offerta di macchina virtuale in Azure Marketplace
description: Informazioni su come aggiungere un pubblico di anteprima per un'offerta di macchina virtuale in Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 48d111decf2145a843733b1f61b1daa87b84b85c
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284332"
---
# <a name="how-to-add-a-preview-audience-for-a-virtual-machine-offer"></a>Come aggiungere un gruppo di destinatari di anteprima per un'offerta di macchina virtuale

Selezionare la scheda **Anteprima** , quindi selezionare un **pubblico di anteprima** limitato per convalidare l'offerta prima di pubblicarla in un pubblico più ampio del Marketplace commerciale.

> [!IMPORTANT]
> Dopo aver controllato l'offerta nel riquadro **Anteprima**, selezionare **Go Live** per pubblicare l'offerta per il pubblico del marketplace commerciale.

Il gruppo di destinatari dell'anteprima viene identificato tramite i GUID dell'ID sottoscrizione di Azure, insieme a una descrizione facoltativa per ognuno di essi. Nessuno di questi campi viene visualizzato dai clienti. È possibile trovare l'ID sottoscrizione di Azure nella pagina **Sottoscrizioni** del portale di Azure.

Aggiungere almeno un ID sottoscrizione di Azure, singolarmente (fino a 10 ID) o caricando un file CSV (fino a 100 ID). Aggiungendo questi ID sottoscrizione, è possibile definire chi può visualizzare l'anteprima dell'offerta prima che venga pubblicata. Se l'offerta è già stata pubblicata, è possibile comunque definire un gruppo di destinatari dell'anteprima per testare le modifiche o gli aggiornamenti dell'offerta.

> [!NOTE]
> Il gruppo di destinatari dell'anteprima è diverso dal gruppo di destinatari privato. Il gruppo di destinatari dell'anteprima può accedere all'offerta *prima* che venga pubblicata in Azure Marketplace. Il gruppo di destinatari dell'anteprima può visualizzare e convalidare tutti i piani, inclusi quelli che saranno disponibili solo a un gruppo di destinatari privato dopo la pubblicazione dell'offerta in Azure Marketplace. Il gruppo di destinatari privato (definito nel riquadro **Prezzi e disponibilità** del piano) dispone di accesso esclusivo a un piano specifico.

Prima di passare alla sezione successiva, selezionare **Salva bozza**.

## <a name="next-steps"></a>Passaggi successivi

- [Rivedere e pubblicare le offerte](review-publish-offer.md)
