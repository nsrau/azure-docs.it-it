---
title: Aumento del limite di rete | Microsoft Docs
description: Aumento del limite rete
author: anavinahar
ms.author: anavin
ms.date: 06/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 48d7e9cc4a3034e149901931f2addbc7df78e2bc
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297129"
---
# <a name="networking-limit-increase"></a>Aumento del limite rete

Per visualizzare l'utilizzo di rete e quota corrente, è possibile visitare il **utilizzi e quote** pannello nel portale di Azure. È anche possibile usare utilizzo [CLI](https://docs.microsoft.com//cli/azure/network?view=azure-cli-latest#az-network-list-usages), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkusage?view=azurermps-6.13.0) o nella [API di utilizzo di rete](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/listusage) per visualizzare l'utilizzo della rete e i limiti.

È possibile richiedere un aumento tramite **Guida e supporto** pannello o il **gli utilizzi e quote** pannello nel portale.

## <a name="request-networking-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Richiedi aumento della quota di rete a livello di sottoscrizione, utilizzando il **Guida e supporto** pannello

Seguire le istruzioni seguenti per creare una richiesta di supporto tramite Azure 'Guida e supporto' blade disponibile nel portale di Azure. 

1. Dal https://portal.azure.com, selezionare **Guida e supporto**.

    ![Guida e supporto](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Selezionare **Nuova richiesta di supporto**. 

    ![Nuova richiesta di supporto](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Nell'elenco a discesa tipo di problema, scegli **limiti di servizio e sottoscrizione (quote)** .

    ![Elenco a discesa tipo di problema](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Selezionare la sottoscrizione per cui è necessario ottenere un aumento della quota.

    ![Selezionare la sottoscrizione newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Selezionare **Networking** nelle **tipo di quota** elenco a discesa. 

    ![Selezionare il tipo di quota](./media/networking-quota-request/select-quota-type-network.png)

6. Nelle **dettagli del problema**, fornire informazioni aggiuntive per eseguire processi la richiesta facendo clic **riportano informazioni dettagliate**.

    ![Fornire i dettagli](./media/resource-manager-core-quotas-request/provide-details.png)

7. Nel **dettagli Quota** pannello, selezionare la distribuzione modello, una posizione e le risorse che si desidera richiedere un aumento per.

    ![Dettagli quota DM](./media/networking-quota-request/quota-details-network.png)

8.  Immettere i nuovi limiti da applicare alla sottoscrizione. Per rimuovere una riga, deselezionare la risorsa dall'elenco a discesa risorsa oppure fare clic sull'icona di eliminazione "x". Dopo aver immesso la quota desiderata per ogni risorsa, fare clic su **Salva e continua** nel pannello dei dettagli di Quota per continuare con la creazione di richiesta di supporto.

    ![Nuovi limiti](./media/networking-quota-request/network-new-limits.png)


## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Richiedi aumento della quota di rete a livello di sottoscrizione, utilizzando **utilizzi e quote** pannello

Seguire le istruzioni sotto usando per creare una richiesta di supporto tramite di Azure "Utilizzo + quote' blade disponibile nel portale di Azure. 

1. In https://portal.azure.com, selezionare **Sottoscrizioni**.

    ![Sottoscrizioni](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Selezionare la sottoscrizione per cui è necessario ottenere un aumento della quota.

    ![Selezionare la sottoscrizione](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Selezionare **Utilizzo e quote**.

    ![Selezionare Utilizzo e quote](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. Nell'angolo superiore destro selezionare **Richiedi aumento**.

    ![Richiedi aumento](./media/resource-manager-core-quotas-request/request-increase.png)

5. Seguire i passaggi iniziando dal passaggio # 3 dal *richieste di rete aumento della quota a livello di sottoscrizione* sezione usando la **Guida e supporto** sezione pannello

## <a name="about-networking-limits"></a>Sui limiti di rete

Per altre informazioni sui limiti di rete, vedere la [sezione rete](../azure-subscription-service-limits.md#networking-limits) della pagina limiti o le domande frequenti i limiti di rete