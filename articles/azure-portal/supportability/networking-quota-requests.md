---
title: Aumento del limite di rete Documenti Microsoft
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

Usare il portale di [Azure](https://portal.azure.com) per aumentare la quota di rete.

Per visualizzare l'utilizzo e la quota **correnti**della rete nel portale di Azure, aprire la sottoscrizione, quindi selezionare Utilizzo , quote . È inoltre possibile utilizzare le opzioni seguenti per visualizzare l'utilizzo e i limiti della rete.

* [Interfaccia di comando per l'utilizzo](/cli/azure/network#az-network-list-usages)
* [Powershell](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [L'API di utilizzo della rete](/rest/api/virtualnetwork/virtualnetworks/listusage)

È possibile richiedere un aumento utilizzando **guida , supporto** o in utilizzo e **quote** nel portale.

> [!Note]
> Per modificare la dimensione predefinita dei **prefissi IP pubblici**, selezionare **Lunghezza massima prefisso INTERrete IP pubblico** dall'elenco a discesa.

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>Richiedi aumento della quota di rete a livello di abbonamento utilizzando la Guida e il supporto

Seguire le istruzioni riportate di seguito per creare una richiesta di supporto usando Guida e supporto nel portale di Azure.Follow the instructions below to create a support request by using **Help - support** in the Azure portal.

1. Accedere al portale di [Azure](https://portal.azure.com), quindi selezionare Guida **e supporto** dal menu del portale di Azure oppure cercare e selezionare Guida e **supporto**.

    ![Guida e supporto](./media/networking-quota-request/help-plus-support.png)

1. Selezionare **Nuova richiesta di supporto**.

    ![Nuova richiesta di supporto](./media/networking-quota-request/new-support-request.png)

1. Per **Tipo di problema**scegliere Limiti di servizio e sottoscrizione **(quote)**.

    ![Selezionare i limiti di sottoscrizione dall'elenco a discesa Tipo di problema](./media/networking-quota-request/select-quota-issue-type.png)

1. Selezionare la sottoscrizione per cui è necessario ottenere un aumento della quota.

    ![Selezionare la sottoscrizione newSRSelect subscription newSR](./media/networking-quota-request/select-subscription-support-request.png)

1. In **Tipo di quota**selezionare **Rete**. Selezionare **Avanti: Soluzioni**.

    ![Selezionare il tipo di quota](./media/networking-quota-request/select-quota-type-network.png)

1. In **DETTAGLI PROBLEMA**selezionare Fornire **dettagli** e immettere informazioni aggiuntive per l'elaborazione della richiesta.

    ![Fornire i dettagli](./media/networking-quota-request/provide-details-link.png)

1. Nel riquadro **Dettagli quota** selezionare un modello di distribuzione, un percorso e le risorse da includere nella richiesta.

    ![Dettagli quota DM](./media/networking-quota-request/quota-details-network.png)

1. Immettere i nuovi limiti da applicare alla sottoscrizione. Per rimuovere una riga, deseleziona la risorsa dal menu **Risorse** o seleziona l'icona di eliminazione della "x". Dopo aver immesso la quota per ogni risorsa, selezionare **Salva e continuare** con la creazione della richiesta di supporto.

    ![Nuovi limiti](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>Richiedi aumento della quota di rete a livello di sottoscrizione utilizzando Usages - quote

Seguire queste istruzioni per creare una richiesta di supporto usando **L'utilizzo e** la quota nel portale di Azure.Follow these instructions to create a support request by using Usage - quota in the Azure portal.

1. Da https://portal.azure.com, cercare e selezionare **Abbonamenti**.

    ![Sottoscrizioni](./media/networking-quota-request/search-for-suscriptions.png)

1. Selezionare la sottoscrizione per cui è necessario ottenere un aumento della quota.

    ![Seleziona sottoscrizione](./media/networking-quota-request/select-subscription-change-quota.png)

1. Selezionare **Utilizzo e quote**.

    ![Selezionare Utilizzo e quote](./media/networking-quota-request/select-usage-plus-quotas.png)

1. Nell'angolo superiore destro selezionare **Richiedi aumento**.

    ![Richiedi aumento](./media/networking-quota-request/request-increase-from-subscription.png)

1. Seguire i passaggi a partire dal passaggio 3 in Richiesta di [aumento della quota di rete a livello](#request-networking-quota-increase-at-subscription-level-using-help--support)di sottoscrizione.

## <a name="about-networking-limits"></a>Informazioni sui limiti di rete

Per ulteriori informazioni sui limiti di rete, consulta la [sezione Rete](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) della pagina dei limiti o le nostre domande frequenti sui limiti di rete.
