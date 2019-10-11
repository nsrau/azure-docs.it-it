---
title: Richieste di aumento della quota di vCPU per macchina virtuale Azure | Microsoft Docs
description: richieste di aumento della quota di vCPU per macchina virtuale
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e99bdd92d4a1aab833a95943b22aaabe7f0daa1d
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248670"
---
# <a name="vm-series-vcpu-limit-increase"></a>Aumento del limite di vCPU per una serie di VM

Gestione risorse le quote vCPU per le macchine virtuali e i set di scalabilità di macchine virtuali vengono applicati a due livelli per ogni sottoscrizione, in ogni area. 

Il primo livello è il **limite di vCPU regionale totale** (in tutte le serie di VM) e il secondo livello è il **limite di vCPU per serie di VM** , ad esempio la serie D vCPU. Ogni volta che viene distribuita una nuova VM, la somma dell'utilizzo di vCPU nuovo ed esistente per tale serie di macchine virtuali non deve superare la quota di vCPU approvata per la serie di VM specifica. Inoltre, il numero totale di vCPU nuovo ed esistente distribuito in tutte le serie di macchine virtuali non deve superare la quota vCPU regionale totale approvata per la sottoscrizione. Se una di queste quote viene superata, la distribuzione della macchina virtuale non sarà possibile.
È possibile richiedere un aumento del limite di quota vCPU per la serie di macchine virtuali da portale di Azure. Un aumento della quota della serie di VM aumenta automaticamente il limite di vCPU regionale totale per la stessa quantità. 

Altre informazioni sulle quote sono disponibili nella pagina delle [quote vCPU della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) e nella pagina relativa ai [limiti di sottoscrizione e servizio di Azure](https://aka.ms/quotalimits) . 

È ora possibile richiedere un aumento tramite il pannello **Guida e supporto** oppure il pannello **utilizzi e quote** nel portale. 

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Richiesta per serie di macchine virtuali vCPU aumento della quota a livello di sottoscrizione usando il pannello **Guida e supporto**

Seguire le istruzioni riportate di seguito per creare una richiesta di supporto tramite il pannello "Guida e supporto" di Azure disponibile nel portale di Azure. 

1. Da https://portal.azure.com, scegliere **Guida e supporto**.

   ![Guida e supporto](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Selezionare **Nuova richiesta di supporto**. 

     ![Nuova richiesta di supporto](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Nell'elenco a discesa tipo di problema scegliere **limiti per servizio e sottoscrizione (quote)** .

   ![Elenco a discesa tipo di problema](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Selezionare la sottoscrizione per cui è necessario ottenere un aumento della quota.

   ![Seleziona newS Subscription](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Selezionare **calcolo-VM (Core-vCPU) il limite di sottoscrizione aumenta** nell'elenco a discesa **tipo di quota** . 

   ![Seleziona tipo di quota](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. In **Dettagli problema**specificare informazioni aggiuntive per l'elaborazione della richiesta facendo clic su **Fornisci dettagli**.

   ![Specificare i dettagli](./media/resource-manager-core-quotas-request/provide-details.png)

7. Nel pannello **Dettagli quota** selezionare modello di distribuzione e selezionare un percorso.

   ![Dettagli quota DM](./media/resource-manager-core-quotas-request/quota-details.png)

8. Selezionare le **famiglie di SKU** che richiedono un aumento. 

   ![Famiglia SKU](./media/resource-manager-core-quotas-request/sku-family.png)

9. Immettere i nuovi limiti da applicare alla sottoscrizione. Per rimuovere una riga, deselezionare lo SKU dall'elenco a discesa delle famiglie di SKU oppure fare clic sull'icona di eliminazione "x". Dopo aver immesso la quota desiderata per ogni famiglia di SKU, fare clic su **Salva e continua** nel pannello Dettagli quota per continuare con la creazione della richiesta di supporto.

   ![Nuovi limiti](./media/resource-manager-core-quotas-request/new-limits.png)


## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Richiesta per serie di macchine virtuali vCPU aumento della quota a livello di sottoscrizione usando il pannello **utilizzo + quote**

Seguire le istruzioni riportate di seguito per creare una richiesta di supporto tramite il pannello "utilizzo e quota" di Azure disponibile nel portale di Azure. 

1. In https://portal.azure.com, selezionare **Sottoscrizioni**.

   ![Sottoscrizioni](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Selezionare la sottoscrizione per cui è necessario ottenere un aumento della quota.

   ![Selezionare la sottoscrizione](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Selezionare **Utilizzo e quote**.

   ![Selezionare Utilizzo e quote](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. Nell'angolo superiore destro selezionare **Richiedi aumento**.

   ![Richiedi aumento](./media/resource-manager-core-quotas-request/request-increase.png)

5. Il **limite di sottoscrizione Compute-VM (Cores-vCPU) aumenta** come tipo di virgolette. 

   ![Compilare i moduli](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
6. Nel pannello **Dettagli quota** selezionare modello di distribuzione e selezionare un percorso.

   ![Pannello Problema della quota](./media/resource-manager-core-quotas-request/quota-details.png)

7. Selezionare le **famiglie di SKU** che richiedono un aumento.

   ![Serie SKU selezionata](./media/resource-manager-core-quotas-request/sku-family.png)

8. Immettere i nuovi limiti da applicare alla sottoscrizione. Per rimuovere una riga, deselezionare lo SKU dall'elenco a discesa delle famiglie di SKU oppure fare clic sull'icona di eliminazione "x". Dopo aver immesso la quota desiderata per ogni famiglia di SKU, fare clic su **Salva e continua** nella pagina passaggio problema per continuare con la creazione della richiesta di supporto.

   ![Richiesta di nuova quota per lo SKU](./media/resource-manager-core-quotas-request/new-limits.png)
 
