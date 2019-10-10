---
title: Incremento limite rete | Microsoft Docs
description: Aumento del limite delle connessioni di rete
author: anavinahar
ms.author: anavin
ms.date: 06/19/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 278e9ff68fa20a0a99a6447bb4cf7ac7fddbfb7b
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249105"
---
# <a name="networking-limit-increase"></a>Aumento del limite delle connessioni di rete

Per visualizzare l'utilizzo e la quota correnti della rete, è possibile visitare il pannello **utilizzi e quote** nella portale di Azure. È anche possibile usare l' [interfaccia](https://docs.microsoft.com//cli/azure/network?view=azure-cli-latest#az-network-list-usages)della riga di comando di utilizzo, [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkusage?view=azurermps-6.13.0) o l' [API di utilizzo della rete](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/listusage) per visualizzare i limiti e l'utilizzo della rete.

È possibile richiedere un aumento tramite il pannello **Guida e supporto** oppure il pannello **utilizzi e quota** nel portale.

## <a name="request-networking-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Richiedere l'aumento della quota di rete a livello di sottoscrizione usando il pannello **Guida e supporto**

Seguire le istruzioni riportate di seguito per creare una richiesta di supporto tramite il pannello "Guida e supporto" di Azure disponibile nel portale di Azure. 

1. Da https://portal.azure.com, scegliere **Guida e supporto**.

    ![Guida e supporto](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Selezionare **Nuova richiesta di supporto**. 

    ![Nuova richiesta di supporto](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Nell'elenco a discesa tipo di problema scegliere **limiti per servizio e sottoscrizione (quote)** .

    ![Elenco a discesa tipo di problema](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Selezionare la sottoscrizione per cui è necessario ottenere un aumento della quota.

    ![Seleziona newS Subscription](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Selezionare **rete** nell'elenco a discesa **tipo di quota** . 

    ![Seleziona tipo di quota](./media/networking-quota-request/select-quota-type-network.png)

6. In **Dettagli problema**specificare informazioni aggiuntive per l'elaborazione della richiesta facendo clic su **Fornisci dettagli**.

    ![Specificare i dettagli](./media/resource-manager-core-quotas-request/provide-details.png)

7. Nel pannello **Dettagli quota** selezionare modello di distribuzione, un percorso e le risorse per cui si desidera richiedere un aumento.

    ![Dettagli quota DM](./media/networking-quota-request/quota-details-network.png)

8.  Immettere i nuovi limiti da applicare alla sottoscrizione. Per rimuovere una riga, deselezionare la risorsa dall'elenco a discesa risorsa oppure fare clic sull'icona Ignora "x". Dopo aver immesso la quota desiderata per ogni risorsa, fare clic su **Salva e continua** nel pannello Dettagli quota per continuare con la creazione della richiesta di supporto.

    ![Nuovi limiti](./media/networking-quota-request/network-new-limits.png)


## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Richiedi aumento della quota di rete a livello di sottoscrizione usando il pannello **utilizzo + quote**

Seguire le istruzioni riportate di seguito per creare una richiesta di supporto tramite il pannello "utilizzo e quota" di Azure disponibile nel portale di Azure. 

1. In https://portal.azure.com, selezionare **Sottoscrizioni**.

    ![Sottoscrizioni](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Selezionare la sottoscrizione per cui è necessario ottenere un aumento della quota.

    ![Selezionare la sottoscrizione](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Selezionare **Utilizzo e quote**.

    ![Selezionare Utilizzo e quote](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. Nell'angolo superiore destro selezionare **Richiedi aumento**.

    ![Richiedi aumento](./media/resource-manager-core-quotas-request/request-increase.png)

5. Seguire i passaggi a partire dal passaggio 3 dalla sezione *aumento della quota di richieste di rete a livello di sottoscrizione* tramite il pannello **Guida e supporto**

## <a name="about-networking-limits"></a>Informazioni sui limiti di rete

Per altre informazioni sui limiti di rete, vedere la [sezione rete](../azure-subscription-service-limits.md#networking-limits) della pagina limiti o domande frequenti sui limiti di rete