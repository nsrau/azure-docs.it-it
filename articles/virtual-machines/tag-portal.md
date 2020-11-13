---
title: Come contrassegnare una macchina virtuale usando il portale di Azure
description: Informazioni sull'assegnazione di tag a una macchina virtuale tramite il portale di Azure.
ms.topic: how-to
ms.workload: infrastructure-services
author: cynthn
ms.service: virtual-machines
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 7c3e779c152a967452f86b55f06d38076102085c
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594977"
---
# <a name="tagging-a-vm-using-the-portal"></a>Assegnazione di tag a una macchina virtuale tramite il portale

Questo articolo descrive come aggiungere tag a una macchina virtuale usando il portale. I tag sono coppie chiave/valore definite dall'utente che possono essere inserite direttamente in una risorsa o un gruppo di risorse. Azure supporta attualmente fino a 50 tag per risorsa e gruppo di risorse. I tag possono essere posizionati su una risorsa al momento della creazione o aggiunti a una risorsa esistente.


1. Passare alla macchina virtuale nel portale.
1. In **Essentials** selezionare **fare clic qui per aggiungere i tag**.

    :::image type="content" source="media/tag/azure-portal-tag.png" alt-text="Screenshot della sezione Essentials della pagina VM.":::

1. Aggiungere un valore per **nome** e **valore** , quindi selezionare **Salva**.

    :::image type="content" source="media/tag/key-value.png" alt-text="Screenshot della pagina per l'aggiunta di una coppia chiave-valore come tag.":::



**Passaggi successivi**

- Per altre informazioni sull'uso dei tag nelle risorse di Azure, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/management/overview.md) e [Uso dei tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md).
- Per informazioni sull'utilità dei tag nella gestione dell'uso delle risorse di Azure, vedere [Comprendere la fattura per Microsoft Azure](../cost-management-billing/understand/review-individual-bill.md) e [Ottenere informazioni dettagliate sul consumo di risorse di Microsoft Azure](../cost-management-billing/manage/usage-rate-card-overview.md).