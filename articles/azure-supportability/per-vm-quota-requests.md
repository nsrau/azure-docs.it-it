---
title: Richieste di aumento della quota di vCPU per macchina virtuale Azure | Microsoft Docs
description: richieste di aumento della quota di vCPU per macchina virtuale
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: ccd0c88c95ae9a752ef8ea2387bbde4f8559bc68
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74531785"
---
# <a name="standard-quota-per-vm-series-vcpu-limit-increase"></a>Quota standard: aumento del limite di vCPU per serie di VM

Gestione risorse supporta due tipi di quote vCPU per le macchine virtuali. **Le macchine virtuali con pagamento in base al consumo e le istanze di VM riservate** usano la quota standard. Le **macchine virtuali con priorità bassa** usano una quota con priorità bassa. La quota vCPU standard per le istanze con pagamento in base al consumo e le VM riservate viene applicata a due livelli per ogni sottoscrizione in ogni area

Il primo livello è il **limite di vCPU regionale totale** (in tutte le serie di VM) e il secondo livello è il **limite di vCPU per serie di VM** , ad esempio la serie dv3 vCPU. Ogni volta che viene distribuita una nuova VM, la somma dell'utilizzo di vCPU nuovo ed esistente per tale serie di macchine virtuali non deve superare la quota di vCPU approvata per la serie di VM specifica. Inoltre, il numero totale di vCPU nuovo ed esistente distribuito in tutte le serie di macchine virtuali non deve superare la quota vCPU regionale totale approvata per la sottoscrizione. Se una di queste quote viene superata, la distribuzione della macchina virtuale non sarà possibile.
È possibile richiedere un aumento del limite di quota vCPU per la serie di macchine virtuali da portale di Azure. Un aumento della quota della serie di VM aumenta automaticamente il limite di vCPU regionale totale per la stessa quantità. 

Per altre informazioni sulle quote vCPU standard, vedere la pagina delle [quote vCPU della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) e la pagina relativa ai [limiti di sottoscrizione e servizio di Azure](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests). 

Altre informazioni su come aumentare il limite di vCPU a livello di area per la quota standard [qui](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests). 

Altre informazioni su come **aumentare i limiti di vCPU di VM con priorità bassa** [qui](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota).

È possibile richiedere un aumento del **limite di quota di vCPU standard per ogni serie di VM** tramite il pannello **Guida e supporto** oppure il pannello **utilizzi e quota** nel portale.

## <a name="request-standard-vcpu-quota-increase-per-vm-series-at-subscription-level-using-the-help--support-blade"></a>Richiedi aumento della quota di vCPU standard per serie di VM a livello di sottoscrizione usando il pannello Guida e supporto

Seguire le istruzioni riportate di seguito per creare una richiesta di supporto tramite il pannello "Guida e supporto" di Azure disponibile nel portale di Azure. 

È anche possibile richiedere una quota per più aree tramite un unico caso di supporto. Per informazioni dettagliate, vedere il passaggio 11 riportato di seguito.

1. In https://portal.azure.com selezionare **Guida e supporto**.

   ![Guida e supporto](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Selezionare **Nuova richiesta di supporto**. 

     ![Nuova richiesta di supporto](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Nell'elenco a discesa tipo di problema scegliere **limiti per servizio e sottoscrizione (quote)** .

   ![Elenco a discesa tipo di problema](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Selezionare la sottoscrizione per cui è necessario ottenere un aumento della quota.

   ![Seleziona newS Subscription](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Selezionare **calcolo-VM (Core-vCPU) limite sottoscrizione aumenta** nell'elenco a discesa **tipo di quota** . 

   ![Seleziona tipo di quota](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. In **Dettagli problema**specificare informazioni aggiuntive per l'elaborazione della richiesta facendo clic su **Fornisci dettagli**.

   ![Specificare i dettagli](./media/resource-manager-core-quotas-request/provide-details.png)

7. Nel pannello **Dettagli quota** selezionare modello di **distribuzione** e selezionare un **percorso.**

   ![Dettagli quota DM](./media/resource-manager-core-quotas-request/1-7.png)

8. Per il percorso selezionato selezionare **tipo** di valore come **"standard"** . È possibile richiedere sia i tipi di quota standard che quelli con priorità bassa da un singolo caso di supporto tramite supporto per più selezioni nel campo **tipo** . Altre informazioni su come **aumentare i limiti di quota con priorità bassa** nella **pagina > di <** .

   ![Famiglia SKU](./media/resource-manager-core-quotas-request/1-8.png)

9. Selezionare le **famiglie di SKU** che richiedono un aumento

   ![Famiglia SKU](./media/resource-manager-core-quotas-request/1-9.png)

10. Immettere i nuovi limiti da applicare alla sottoscrizione. Per rimuovere una riga, deselezionare lo SKU dall'elenco a discesa famiglia di SKU oppure fare clic sull'icona Ignora "x". 

   ![Nuovi limiti](./media/resource-manager-core-quotas-request/1-10.png)

11. Per richiedere una quota per più percorsi, è possibile verificare in un'altra **posizione** dall'elenco a discesa e selezionare il tipo di macchina virtuale appropriato. Questo passaggio consente di precaricare le famiglie di SKU selezionate per la **posizione** precedente nel nuovo percorso ed è sufficiente immettere i nuovi limiti desiderati.

   ![Percorsi multipli](./media/resource-manager-core-quotas-request/1-11.png)
   
12. Dopo aver immesso la quota desiderata per ogni famiglia di SKU, fare clic su **Salva e continua** nel pannello Dettagli quota per continuare con la creazione della richiesta di supporto.

## <a name="request-standard-vcpu-quota-increase-per-vm-series-at-subscription-level-using-usages--quota-blade"></a>Richiedi aumento della quota di vCPU standard per serie di macchine virtuali a livello di sottoscrizione usando il pannello utilizzi e quote

Seguire le istruzioni riportate di seguito per creare una richiesta di supporto tramite il pannello "utilizzo e quota" di Azure disponibile nel portale di Azure.

È anche possibile **richiedere una quota per più aree** tramite un unico caso di supporto. Per informazioni dettagliate, vedere il passaggio 10 riportato di seguito

1. In https://portal.azure.com , selezionare **Sottoscrizioni**.

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

   ![Pannello Problema della quota](./media/resource-manager-core-quotas-request/1-1-6.png)

7. Per il percorso selezionato selezionare **tipo** di valore come **"standard"** . È possibile richiedere sia i tipi di quota standard che quelli con priorità bassa da un singolo caso di supporto tramite supporto per più selezioni nel campo **tipo** . Altre informazioni su come **aumentare i limiti di vCPU con priorità bassa** in questa [pagina](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota).

   ![Serie SKU selezionata](./media/resource-manager-core-quotas-request/1-1-7.png)
   
   
8. Selezionare le **famiglie di SKU** che richiedono un aumento

   ![Serie SKU selezionata](./media/resource-manager-core-quotas-request/1-1-8.png)

9. Immettere i nuovi limiti da applicare alla sottoscrizione. Per rimuovere una riga, deselezionare lo SKU dall'elenco a discesa famiglia di SKU oppure fare clic sull'icona Ignora "x". 

   ![Richiesta di nuova quota per lo SKU](./media/resource-manager-core-quotas-request/1-1-9.png)
   

10. Per richiedere una quota per più percorsi, è possibile verificare in un'altra **posizione** dall'elenco a discesa e selezionare il tipo di macchina virtuale appropriato. Questo passaggio consente di precaricare le famiglie di SKU selezionate per la **posizione** precedente nel nuovo percorso ed è sufficiente immettere i nuovi limiti desiderati.
   
    ![Richiesta di nuova quota per lo SKU](./media/resource-manager-core-quotas-request/1-1-10.png)
 
