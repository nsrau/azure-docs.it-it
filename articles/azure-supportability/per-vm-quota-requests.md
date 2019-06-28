---
title: Azure per ogni macchina virtuale le richieste di aumento della quota di vCPU | Microsoft Docs
description: per ogni quota di vCPU della macchina virtuale richieste di aumento
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 79879cd7f5ea5af1b794735f32e6e1367458e124
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310717"
---
# <a name="vm-series-vcpu-limit-increase"></a>Aumento del limite di vCPU per una serie di VM

Le quote di vCPU per Resource Manager per macchine virtuali e set di scalabilità di macchine virtuali sono imposte a due livelli per ogni sottoscrizione, in ogni area. 

Il primo livello è il **limite di Vcpu regionale totale** (in tutte le serie di VM), e il secondo livello contiene i **per ogni serie di macchine Virtuali Vcpu limitare** (ad esempio i Vcpu serie D). Qualsiasi momento che una nuova macchina virtuale deve essere distribuita, la somma dell'utilizzo di CPU virtuali nuove ed esistenti per tale serie di macchine Virtuali non deve superare la quota di vCPU approvata per quel particolare serie di macchine Virtuali. Inoltre, il conteggio totale vCPU nuovi ed esistenti distribuito in tutte le macchine virtuali della serie non deve superare la quota Vcpu regionale totale approvata per la sottoscrizione. Se una di queste quote viene superata, la distribuzione della macchina virtuale non sarà possibile.
È possibile richiedere un aumento del limite della quota di Vcpu per la serie di macchine Virtuali dal portale di Azure. Un aumento della quota di macchine virtuali della serie di aumenta automaticamente il limite di Vcpu regionale totale dello stesso importo. 

Quando viene creata una nuova sottoscrizione, i Vcpu regionale totale predefinito potrebbero non essere uguale alla somma delle quote di vCPU predefinito per tutte le serie di macchine Virtuali singole. Ciò può comportare una sottoscrizione con una quota sufficiente per ogni singola serie di VM che si desidera distribuire, ma non sono sufficienti quote di Vcpu regionali totali per tutte le distribuzioni. In questo caso, è necessario inviare una richiesta per aumentare il limite di Vcpu regionali totali in modo esplicito. Limite di Vcpu regionale totale non può superare la somma della quota approvato in tutte le serie VM per l'area.

Altre informazioni sulle quote nel [pagina delle quote vCPU della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) e [sottoscrizione di Azure e limiti dei servizi](https://aka.ms/quotalimits) pagina. 

È ora possibile richiedere un aumento tramite **Guida e supporto** pannello o il **gli utilizzi e quote** pannello nel portale. 

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Richiesta per ogni aumento della quota di vCPU serie di macchine Virtuali a livello di sottoscrizione, utilizzando il **Guida e supporto** pannello

Seguire le istruzioni seguenti per creare una richiesta di supporto tramite Azure 'Guida e supporto' blade disponibile nel portale di Azure. 

1. Dal https://portal.azure.com, selezionare **Guida e supporto**.

![Guida e supporto](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Selezionare **Nuova richiesta di supporto**. 

![Nuova richiesta di supporto](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Nell'elenco a discesa tipo di problema, scegli **limiti di servizio e sottoscrizione (quote)** .

![Elenco a discesa tipo di problema](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Selezionare la sottoscrizione per cui è necessario ottenere un aumento della quota.

![Selezionare la sottoscrizione newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Selezionare **calcolo - VM (Core-Vcpu) sottoscrizione limite aumenta** nelle **tipo di quota** elenco a discesa. 

![Selezionare il tipo di quota](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. Nelle **dettagli del problema**, fornire informazioni aggiuntive per eseguire processi la richiesta facendo clic **riportano informazioni dettagliate**.

![Fornire i dettagli](./media/resource-manager-core-quotas-request/provide-details.png)

7. Nel **dettagli Quota** pannello selezionare il modello di distribuzione, selezionare un percorso.

![Dettagli quota DM](./media/resource-manager-core-quotas-request/quota-details.png)

8. Selezionare il **famiglie di SKU** che richiedono un aumento. 

![Famiglia SKU](./media/resource-manager-core-quotas-request/sku-family.png)

9. Immettere i nuovi limiti da applicare alla sottoscrizione. Per rimuovere una riga, deselezionare lo SKU dall'elenco a discesa delle famiglie di SKU oppure fare clic sull'icona di eliminazione "x". Dopo aver immesso la quota desiderata per ogni famiglia di SKU, fare clic su **Salva e continua** nel pannello dei dettagli di Quota per continuare con la creazione di richiesta di supporto.

![Nuovi limiti](./media/resource-manager-core-quotas-request/new-limits.png)


## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Richiesta per ogni aumento della quota di vCPU serie di macchine Virtuali a livello di sottoscrizione, utilizzando **utilizzi e quote** pannello

Seguire le istruzioni sotto usando per creare una richiesta di supporto tramite di Azure "Utilizzo + quote' blade disponibile nel portale di Azure. 

1. In https://portal.azure.com, selezionare **Sottoscrizioni**.

![Sottoscrizioni](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Selezionare la sottoscrizione per cui è necessario ottenere un aumento della quota.

![Selezionare la sottoscrizione](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Selezionare **Utilizzo e quote**.

![Selezionare Utilizzo e quote](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. Nell'angolo superiore destro selezionare **Richiedi aumento**.

![Richiedi aumento](./media/resource-manager-core-quotas-request/request-increase.png)

5. Selezionare **aumenta il limite di sottoscrizione di calcolo-VM (Core-Vcpu)** come tipo di quota. 

![Compilare i moduli](./media/resource-manager-core-quotas-request/forms.png)
   
6. Nel **dettagli Quota** pannello selezionare il modello di distribuzione, selezionare un percorso.

![Pannello Problema della quota](./media/resource-manager-core-quotas-request/problemstep.png)

7. Selezionare il **famiglie di SKU** che richiedono un aumento.

![Serie SKU selezionata](./media/resource-manager-core-quotas-request/sku-family.png)

8. Immettere i nuovi limiti da applicare alla sottoscrizione. Per rimuovere una riga, deselezionare lo SKU dall'elenco a discesa delle famiglie di SKU oppure fare clic sull'icona di eliminazione "x". Dopo aver immesso la quota desiderata per ogni famiglia di SKU, fare clic su **Salva e continua** nella pagina di passaggio del problema per continuare con la creazione di richiesta di supporto.

![Richiesta di nuova quota per lo SKU](./media/resource-manager-core-quotas-request/new-limits.png)

