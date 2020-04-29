---
title: Modello di distribuzione classica di Azure
description: Il modello di distribuzione classica, ora sostituito dal modello di Gestione risorse, impone un limite di quota vCPU globale per le macchine virtuali e i set di scalabilità di macchine virtuali.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: a3d5106cafc1d3bfe77f3e42e85cedb668fc4fa0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76835638"
---
# <a name="classic-deployment-model"></a>Modello di distribuzione classica

Il modello di distribuzione classica è il modello di distribuzione di Azure di generazione precedente. Impone un limite di quota vCPU globale per le macchine virtuali e i set di scalabilità di macchine virtuali. Il modello di distribuzione classica non è più consigliato ed è ora sostituito dal modello di Gestione risorse.

Per ulteriori informazioni su questi due modelli di distribuzione e sui vantaggi derivanti dall'utilizzo di Gestione risorse, vedere [Gestione risorse e distribuzione classica](../../azure-resource-manager/management/deployment-models.md).

Quando viene creata una nuova sottoscrizione, viene assegnata una quota predefinita di vCPU. Ogni volta che una nuova macchina virtuale deve essere distribuita con il modello di distribuzione classica, la somma dell'utilizzo di vCPU nuovo ed esistente in tutte le aree non deve superare la quota vCPU approvata per il modello di distribuzione classica.

Per altre informazioni sulle quote, vedere [sottoscrizione di Azure e limiti, quote e vincoli del servizio](../../azure-resource-manager/management/azure-subscription-service-limits.md).

È possibile richiedere un aumento del limite di quota vCPU per il modello di distribuzione classica. Usare la **Guida** e il supporto o l' **utilizzo + quote** nel portale di Azure.

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-help--support"></a>Richiesta per serie di macchine virtuali vCPU aumento della quota a livello di sottoscrizione usando guida e supporto

Seguire le istruzioni riportate di seguito per creare una richiesta di supporto usando **Guida e supporto tecnico** nella portale di Azure.

1. Scegliere **Guida e supporto**dal menu [portale di Azure](https://portal.azure.com) .

   ![Selezionare Guida e supporto nel portale di Azure](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Selezionare **Nuova richiesta di supporto**.

   ![Creare una nuova richiesta di supporto nel portale di Azure](./media/resource-manager-core-quotas-request/new-support-request.png)

1. In **tipo di problema**scegliere **limiti per servizio e sottoscrizione (quote)**.

   ![Selezionare le quote come tipo di problema](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Selezionare la sottoscrizione di cui si vuole aumentare la quota.

   ![Selezionare la sottoscrizione per cui aumentare una quota](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Per **tipo di quota**selezionare **aumenta il limite di sottoscrizione Compute-VM (Cores-vCPU)**.

   ![Selezionare il tipo di quota da aumentare](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Selezionare **Avanti: soluzioni** per aprire **i dettagli del problema**. Selezionare **specificare i dettagli** per fornire informazioni aggiuntive.

   ![Specificare i dettagli per aiutare la richiesta insieme](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. In **Dettagli quota**selezionare **classica** e selezionare una **località**.

   ![Aggiungere dettagli, tra cui il modello di distribuzione e il percorso](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. Per la **famiglia di SKU**selezionare una o più famiglie di SKU da aumentare.

   ![Specificare la famiglia di SKU da aumentare](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Immettere i nuovi limiti da applicare alla sottoscrizione. Per rimuovere una riga, deselezionare lo SKU dalla **famiglia di SKU** o selezionare l'icona di eliminazione "X". Dopo aver immesso una quota per ogni famiglia di SKU, selezionare **Salva e continua** in **Dettagli quota** per continuare con la richiesta di supporto.

   ![Richiedi nuovi limiti](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usage--quotas"></a>Richiesta per serie di macchine virtuali vCPU aumento della quota a livello di sottoscrizione usando utilizzo e quote

Seguire le istruzioni riportate di seguito per creare una richiesta di supporto usando l'utilizzo e le **quote** nella portale di Azure.

1. Nella [portale di Azure](https://portal.azure.com)cercare e selezionare **sottoscrizioni**.

   ![Passare a sottoscrizioni nella portale di Azure](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Selezionare la sottoscrizione di cui si vuole aumentare la quota.

   ![Selezionare la sottoscrizione da modificare](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Selezionare **utilizzo e quote**.

   ![Selezionare utilizzo e quote per una sottoscrizione](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. Nell'angolo superiore destro selezionare **Richiedi aumento**.

   ![Selezionare per aumentare la quota](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Selezionare **calcolo-VM (Core-vCPU). il limite di sottoscrizione aumenta** come **tipo di quota**.

   ![Seleziona tipo di quota](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Selezionare **Avanti: soluzioni** per aprire **i dettagli del problema**. Selezionare **specificare i dettagli** per fornire informazioni aggiuntive.

   ![Specificare i dettagli per la richiesta](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. In **Dettagli quota**selezionare **classica** e una **località**.

   ![Selezionare i dettagli della quota, inclusi il modello di distribuzione e il percorso](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. Selezionare una o più famiglie di SKU per un aumento.

   ![Selezionare la famiglia di SKU per l'aumento](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Immettere i nuovi limiti da applicare alla sottoscrizione. Per rimuovere una riga, deselezionare lo SKU dalla **famiglia di SKU** o selezionare l'icona di eliminazione "X". Dopo aver immesso una quota per ogni famiglia di SKU, selezionare **Salva e continua** in **Dettagli quota** per continuare con la richiesta di supporto.

   ![Immettere una nuova quota](./media/resource-manager-core-quotas-request/new-limits-classic.png)

