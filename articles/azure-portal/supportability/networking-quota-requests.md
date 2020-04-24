---
title: Incremento limite rete | Microsoft Docs
description: Aumento del limite delle connessioni di rete
author: anavinahar
ms.author: anavin
ms.date: 01/23/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9b5c7043b06172c2d4931ca1c3fd3ac5d0e80883
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76547806"
---
# <a name="networking-limit-increase"></a>Aumento del limite delle connessioni di rete

Usare il [portale di Azure](https://portal.azure.com) per aumentare la quota di rete.

Per visualizzare l'utilizzo e la quota di rete correnti in portale di Azure, aprire la sottoscrizione e quindi selezionare **utilizzi e quote**. È anche possibile usare le opzioni seguenti per visualizzare i limiti e l'utilizzo della rete.

* [INTERFACCIA della riga di comando di utilizzo](/cli/azure/network#az-network-list-usages)
* [PowerShell](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [API utilizzo rete](/rest/api/virtualnetwork/virtualnetworks/listusage)

È possibile richiedere un aumento usando **Guida e supporto tecnico** oppure in **utilizzi + quote** nel portale.

> [!Note]
> Per modificare le dimensioni predefinite dei **prefissi IP pubblici**, selezionare **lunghezza prefisso Internet IP pubblico minimo** dall'elenco a discesa.

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>Richiedere l'aumento della quota di rete a livello di sottoscrizione usando guida e supporto

Seguire le istruzioni riportate di seguito per creare una richiesta di supporto usando **Guida e supporto tecnico** nella portale di Azure.

1. Accedere a [portale di Azure](https://portal.azure.com), quindi scegliere Guida e **supporto** dal menu portale di Azure oppure cercare e selezionare **Guida e supporto**.

    ![Guida e supporto](./media/networking-quota-request/help-plus-support.png)

1. Selezionare **Nuova richiesta di supporto**.

    ![Nuova richiesta di supporto](./media/networking-quota-request/new-support-request.png)

1. Per **tipo di problema**, scegliere **limiti per servizio e sottoscrizione (quote)**.

    ![Selezionare i limiti della sottoscrizione dall'elenco a discesa tipo di problema](./media/networking-quota-request/select-quota-issue-type.png)

1. Selezionare la sottoscrizione per cui è necessario ottenere un aumento della quota.

    ![Seleziona newS Subscription](./media/networking-quota-request/select-subscription-support-request.png)

1. In **tipo di quota**selezionare **rete**. Selezionare **Avanti: soluzioni**.

    ![Seleziona tipo di quota](./media/networking-quota-request/select-quota-type-network.png)

1. In **Dettagli problema**selezionare **specificare i dettagli** e immettere informazioni aggiuntive per facilitare l'elaborazione della richiesta.

    ![Specificare i dettagli](./media/networking-quota-request/provide-details-link.png)

1. Nel pannello **Dettagli quota** selezionare un modello di distribuzione, un percorso e le risorse da includere nella richiesta.

    ![Dettagli quota DM](./media/networking-quota-request/quota-details-network.png)

1. Immettere i nuovi limiti da applicare alla sottoscrizione. Per rimuovere una riga, deselezionare la risorsa dal menu **risorse** oppure selezionare l'icona Ignora "x". Dopo aver immesso la quota per ogni risorsa, selezionare **Salva e continua** per continuare con la creazione della richiesta di supporto.

    ![Nuovi limiti](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>Richiedi aumento della quota di rete a livello di sottoscrizione usando gli utilizzi e le quote

Seguire queste istruzioni per creare una richiesta di supporto usando l'utilizzo e la **quota** nella portale di Azure.

1. Da https://portal.azure.comcercare e selezionare le **sottoscrizioni**.

    ![Sottoscrizioni](./media/networking-quota-request/search-for-suscriptions.png)

1. Selezionare la sottoscrizione per cui è necessario ottenere un aumento della quota.

    ![Seleziona sottoscrizione](./media/networking-quota-request/select-subscription-change-quota.png)

1. Selezionare **Utilizzo e quote**.

    ![Selezionare Utilizzo e quote](./media/networking-quota-request/select-usage-plus-quotas.png)

1. Nell'angolo superiore destro selezionare **Richiedi aumento**.

    ![Richiedi aumento](./media/networking-quota-request/request-increase-from-subscription.png)

1. Seguire i passaggi a partire dal passaggio 3 in [richiedere l'aumento della quota di rete a livello di sottoscrizione](#request-networking-quota-increase-at-subscription-level-using-help--support).

## <a name="about-networking-limits"></a>Informazioni sui limiti di rete

Per altre informazioni sui limiti di rete, vedere la [sezione rete](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) della pagina limiti o domande frequenti sui limiti di rete.
