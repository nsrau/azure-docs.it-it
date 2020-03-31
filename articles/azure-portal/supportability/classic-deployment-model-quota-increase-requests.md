---
title: Modello di distribuzione classica di Azure
description: Il modello di distribuzione classica, ora sostituito dal modello di Resource Manager, impone un limite di quota vCPU globale per le macchine virtuali e i set di scalabilità di macchine virtuali.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: a3d5106cafc1d3bfe77f3e42e85cedb668fc4fa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76835638"
---
# <a name="classic-deployment-model"></a>Modello di distribuzione classica

Il modello di distribuzione classica è il modello di distribuzione di Azure di generazione precedente. Applica un limite di quota vCPU globale per le macchine virtuali e i set di scalabilità delle macchine virtuali. Il modello di distribuzione classica non è più consigliato ed è ora sostituito dal modello di Resource Manager.The Classic deployment model is no longer recommended, and now is sostituitd by the Resource Manager model.

Per altre informazioni su questi due modelli di distribuzione e sui vantaggi dell'uso di Resource Manager, vedere [Resource Manager e distribuzione classica.](../../azure-resource-manager/management/deployment-models.md)

Quando viene creata una nuova sottoscrizione, viene assegnata una quota predefinita di vCPU. Ogni volta che una nuova macchina virtuale deve essere distribuita utilizzando il modello di distribuzione classica, la somma dell'utilizzo della vCPU nuovo ed esistente in tutte le aree non deve superare la quota vCPU approvata per il modello di distribuzione classica.

Per altre informazioni sulle quote, vedere [Limiti, quote e vincoli](../../azure-resource-manager/management/azure-subscription-service-limits.md)della sottoscrizione e del servizio di Azure.

È possibile richiedere un aumento del limite di quota vCPU per il modello di distribuzione classica. Usare **Guida , supporto** o Utilizzo e quote nel portale di Azure.Use either Help - support **or Usage - quotas** in the Azure portal.

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-help--support"></a>Aumento della quota vCPU della serie di richieste per VM a livello di sottoscrizione usando la Guida e il supporto

Seguire le istruzioni riportate di seguito per creare una richiesta di supporto usando Guida e supporto nel portale di Azure.Follow the instructions below to create a support request by using **Help - support** in the Azure portal.

1. Dal menu del portale di [Azure,](https://portal.azure.com) selezionare **Guida e supporto**.

   ![Selezionare Guida e supporto nel portale di AzureSelect Help and support in the Azure portal](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Selezionare **Nuova richiesta di supporto**.

   ![Creare una nuova richiesta di supporto nel portale di AzureCreate a new support request in the Azure portal](./media/resource-manager-core-quotas-request/new-support-request.png)

1. In **Tipo di problema**scegliere Limiti di servizio e sottoscrizione **(quote).**

   ![Selezionare le quote come tipo di problema](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Selezionare la sottoscrizione di cui si desidera aumentare la quota.

   ![Selezionare la sottoscrizione per la quale aumentare una quota](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Per **Tipo di quota**, selezionare Compute **-VM (cores-vCPUs) dei limiti**di sottoscrizione aumenta.

   ![Selezionare il tipo di quota da aumentare](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Selezionare **Avanti: Soluzioni** per aprire **DETTAGLI PROBLEMA**. Selezionare **Fornisci dettagli** per fornire informazioni aggiuntive.

   ![Fornisci i dettagli per aiutare la tua richiesta](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. In **Dettagli quota**selezionare **Classico** e selezionare una **posizione**.

   ![Aggiungere dettagli, tra cui Il modello di distribuzione e il percorsoAdd details including Deployment model and Location](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. Per **la famiglia SKU**, selezionare una o più famiglie SKU da aumentare.

   ![Specificare la famiglia di sKU da aumentare](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Immettere i nuovi limiti da applicare alla sottoscrizione. Per rimuovere una riga, deselezionare lo SKU dalla **famiglia di SKU** o selezionare l'icona di eliminazione "X". Dopo aver immesso una quota per ogni famiglia di SKU, selezionare **Salva e continua** nei dettagli della **quota** per continuare con la richiesta di supporto.

   ![Richiedi nuovi limiti](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usage--quotas"></a>Aumento della quota vCPU della serie di richieste per VM a livello di sottoscrizione usando Le quote di utilizzo e quoteRequest per VM series vCPU quota increase at subscription level using Usage - quotas

Seguire le istruzioni riportate di seguito per creare una richiesta di supporto usando Utilizzo e quote nel portale di Azure.Follow the instructions below to create a support request by using **Usage - quotas** in the Azure portal.

1. Nel [portale](https://portal.azure.com)di Azure cercare e selezionare **Sottoscrizioni**.

   ![Passare a Sottoscrizioni nel portale di AzureGo to Subscriptions in the Azure portal](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Selezionare la sottoscrizione di cui si desidera aumentare la quota.

   ![Selezionare la sottoscrizione da modificare](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Selezionare **Utilizzo e quote**.

   ![Selezionare l'utilizzo e le quote per una sottoscrizioneSelect usage and quotas for a subscription](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. Nell'angolo superiore destro selezionare **Richiedi aumento**.

   ![Selezionare per aumentare la quota](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Selezionare il limite di **sottoscrizione di vm di calcolo (cores-vCPU) aumenta** con il tipo di **quota**.

   ![Selezionare il tipo di quota](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Selezionare **Avanti: Soluzioni** per aprire **DETTAGLI PROBLEMA**. Selezionare **Fornisci dettagli** per fornire informazioni aggiuntive.

   ![Fornisci i dettagli per la tua richiesta](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. In **Dettagli quota**selezionare **Classico** e una **posizione**.

   ![Selezionare i dettagli della quota, inclusi il modello di distribuzione e il percorso](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. Selezionare una o più famiglie di SKU per un aumento.

   ![Selezionare la famiglia SKU per l'aumento](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Immettere i nuovi limiti da applicare alla sottoscrizione. Per rimuovere una riga, deselezionare lo SKU dalla **famiglia di SKU** o selezionare l'icona di eliminazione "X". Dopo aver immesso una quota per ogni famiglia di SKU, selezionare **Salva e continua** nei dettagli della **quota** per continuare con la richiesta di supporto.

   ![Immettere una nuova quota](./media/resource-manager-core-quotas-request/new-limits-classic.png)

