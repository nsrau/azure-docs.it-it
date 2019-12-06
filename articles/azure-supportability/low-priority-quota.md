---
title: Quota spot | Microsoft Docs
description: Richieste di quota spot
author: sowmyavenkat86
ms.author: svenkat
ms.date: 11/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 09c9d7940314b691e6351353e6a0076510fdcb13
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850568"
---
# <a name="spot-quota-limit-increase-for-all-vm-series"></a>Quota spot: aumento del limite per tutte le serie di macchine virtuali

Le VM spot forniscono un modello diverso di utilizzo di Azure, con un costo inferiore per consentire ad Azure di rimuovere una macchina virtuale in base alle esigenze per le distribuzioni con pagamento in base al consumo o per le istanze di VM riservate. Per altre informazioni sulle macchine virtuali, vedere [qui](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot).

Gestione risorse supporta due tipi di quote vCPU per le macchine virtuali. **Le macchine virtuali con pagamento in base al consumo e le istanze di VM riservate** usano la quota standard. Le **VM spot** usano la quota spot. 

Per il tipo di **quota spot** , gestione risorse le quote vCPU vengono applicate a tutte le serie di macchine virtuali disponibili come singolo limite a livello di area.

Ogni volta che viene distribuita una nuova macchina virtuale spot, la somma dell'utilizzo di vCPU nuovo ed esistente per tutte le istanze di VM spot non deve superare il limite di quota vCPU di punti approvati. Se la quota spot viene superata, la distribuzione della macchina virtuale spot non sarà consentita. È possibile richiedere un aumento del limite di quota vCPU di spot da portale di Azure. 

Per altre informazioni sulle quote vCPU standard, vedere la pagina delle quote vCPU della macchina virtuale e la pagina relativa ai limiti di sottoscrizione e servizio di Azure. Altre informazioni su come aumentare il limite di vCPU a livello di area per la quota standard in questa [pagina](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests).

È ora possibile richiedere un aumento **dei limiti di quota spot per tutte le serie di macchine virtuali** tramite il pannello **Guida e supporto** oppure il pannello **utilizzi e quota** nel portale.

## <a name="request-spot-quota-limit-increase-for-all-vm-series-per-subscription-using-the-help--support-blade"></a>Richiedere un aumento del limite di quota per tutte le serie di VM per ogni sottoscrizione usando il pannello Guida e supporto

Seguire le istruzioni riportate di seguito per creare una richiesta di supporto tramite il pannello "Guida e supporto" di Azure disponibile nel portale di Azure.

È anche possibile **richiedere una quota per più aree** tramite un unico caso di supporto. Per informazioni dettagliate, vedere il passaggio 10 riportato di seguito. 


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
   
7.  Nel pannello **Dettagli quota*** selezionare modello di **distribuzione** e selezionare un **percorso**.

![Specificare i dettagli](./media/resource-manager-core-quotas-request/3-7.png)

8. Per il percorso selezionato selezionare **tipo** di valore come **"spot"** . È possibile richiedere i tipi di quota standard e spot da un singolo caso di supporto tramite supporto per più selezioni nel campo **tipo** . Altre informazioni sull' **aumento della quota standard per ogni serie di VM** in questa [pagina](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

![Specificare i dettagli](./media/resource-manager-core-quotas-request/3-8.png)

9.  Immettere il nuovo limite che si desidera per la sottoscrizione. 
 
 ![Specificare i dettagli](./media/resource-manager-core-quotas-request/3-9.png)

10. Per richiedere una quota per più percorsi, è possibile verificare in un'altra posizione dall'elenco a discesa e selezionare il tipo di macchina virtuale appropriato. È quindi possibile immettere i nuovi limiti desiderati.

![Specificare i dettagli](./media/resource-manager-core-quotas-request/3-10.png)

11. Dopo aver immesso la quota desiderata, fare clic su **Salva e continua** nel pannello Dettagli quota per continuare con la creazione della richiesta di supporto.

## <a name="request-spot-quota-limit-increase-for-all-vm-series-per-subscription-using-usages--quota-blade"></a>Richiedere l'aumento del limite di quota per le singole serie di VM per ogni sottoscrizione usando gli utilizzi e il pannello quota

Seguire le istruzioni riportate di seguito per creare una richiesta di supporto tramite il pannello "utilizzo e quota" di Azure disponibile nel portale di Azure.

È anche possibile **richiedere una quota per più aree** tramite un unico caso di supporto. Per informazioni dettagliate, vedere il passaggio 9 riportato di seguito. 

1.  In https://portal.azure.com , selezionare **Sottoscrizioni**.

 ![Sottoscrizioni](./media/resource-manager-core-quotas-request/subscriptions.png)

2.  Selezionare la sottoscrizione per cui è necessario ottenere un aumento della quota.

 ![Selezionare la sottoscrizione](./media/resource-manager-core-quotas-request/select-subscription.png)

3.  Selezionare **Utilizzo e quote**.

 ![Selezionare Utilizzo e quote](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4.  Nell'angolo superiore destro selezionare **Richiedi aumento.**

   ![Richiedi aumento](./media/resource-manager-core-quotas-request/request-increase.png)

5.  Il **limite di sottoscrizione Compute-VM (Cores-vCPU) aumenta** come tipo di virgolette.

  ![Compilare i moduli](./media/resource-manager-core-quotas-request/select-quota-type.png)

6.  Nel pannello **Dettagli quota** selezionare modello di distribuzione e selezionare un percorso.

  ![Compilare i moduli](./media/resource-manager-core-quotas-request/3-2-6.png)
 
7.  Per il percorso selezionato selezionare **tipo** di valore come **"spot".** È possibile richiedere i tipi di quota standard e spot da un singolo caso di supporto tramite supporto per più selezioni nel campo **tipo** . Altre informazioni sull' **aumento della quota standard per ogni serie di VM** in questa [pagina](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

  ![Compilare i moduli](./media/resource-manager-core-quotas-request/3-2-7.png)
 
8.  Immettere il nuovo limite che si desidera per la sottoscrizione.

  ![Compilare i moduli](./media/resource-manager-core-quotas-request/3-2-8.png)
 
9.  Per richiedere una quota per più percorsi, è possibile verificare in un'altra **posizione** dall'elenco a discesa e selezionare il tipo di macchina virtuale appropriato. È quindi possibile immettere i nuovi limiti desiderati.

  ![Compilare i moduli](./media/resource-manager-core-quotas-request/3-2-9.png)
 
10. Dopo aver immesso la quota desiderata, fare clic su **Salva e continua** nel pannello Dettagli quota per continuare con la creazione della richiesta di supporto.


