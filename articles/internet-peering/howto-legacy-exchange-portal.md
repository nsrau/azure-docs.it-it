---
title: Convertire un peering di Exchange legacy in una risorsa di Azure con il portale di Azure
titleSuffix: Azure
description: Convertire un peering di Exchange legacy in una risorsa di Azure con il portale di Azure
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 5/21/2020
ms.author: derekol
ms.openlocfilehash: e4f9f5b59b61065c300b58fb1cdb88e12b7ddbe0
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84247224"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Convertire un peering di Exchange legacy in una risorsa di Azure con il portale di Azure

Questo articolo illustra come convertire un peering di Exchange legacy esistente in una risorsa di Azure usando il portale di Azure.

Se si preferisce, è possibile completare questa guida usando [PowerShell](howto-legacy-exchange-powershell.md).

## <a name="before-you-begin"></a>Prima di iniziare
* Prima di iniziare la configurazione, esaminare i [prerequisiti](prerequisites.md) e la [procedura dettagliata per il peering di Exchange](walkthrough-exchange-all.md).

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>Convertire un peering di Exchange legacy in una risorsa di Azure

I provider di Internet Exchange possono creare una richiesta di peering di Exchange tramite la [creazione di un peering]( https://go.microsoft.com/fwlink/?linkid=2129593).

1. Nella scheda **Generale** della pagina **Create a Peering** (Crea peering) compilare le caselle come illustrato nell'immagine seguente:

   ![Registrare il Servizio di peering](./media/setup-basics-tab.png)

* Selezionare la sottoscrizione di Azure.

* Per Gruppo di risorse, è possibile sceglierne uno esistente nell'elenco a discesa o crearne uno nuovo selezionando Crea nuovo. Per questo esempio, creare un nuovo gruppo di risorse.

* Il nome corrisponde a quello della risorsa e può essere qualsiasi nome desiderato.

* Se si sceglie un gruppo di risorse esistente, l'area viene selezionata automaticamente. Se si sceglie di creare un nuovo gruppo di risorse, è necessario scegliere anche l'area di Azure in cui deve trovarsi la risorsa.

  >[!NOTE]
  >L'area in cui si trova un gruppo di risorse è indipendente dalla località in cui si vuole creare il peering con Microsoft. Tuttavia, è consigliabile organizzare le risorse di peering all'interno di gruppi di risorse che si trovano nelle aree di Azure più vicine. Ad esempio, per peering in Ashburn è possibile creare un gruppo di risorse nell'area Stati Uniti orientali o Stati Uniti orientali 2.

* Selezionare l'ASN nella casella **ASN peer**.

  >[!IMPORTANT]  
  >È possibile scegliere un ASN solo con stato di convalida approvato prima di inviare una richiesta di peering. Se la richiesta dell'ASN del peer è appena stata inviata, attendere 12 ore che venga approvata l'associazione dell'ASN. Se la convalida dell'ASN selezionato è in sospeso, verrà visualizzato un messaggio di errore. Se l'ASN che è necessario scegliere non è visualizzato, verificare di aver selezionato la sottoscrizione corretta. Se sì, controllare se è già stata creata una richiesta di ASN del peer facendo riferimento a **[Associare l'ASN del peer alla sottoscrizione di Azure](https://go.microsoft.com/fwlink/?linkid=2129592)** .

* Selezionare **Avanti: Configurazione** per continuare.


#### <a name="configure-connections-and-submit"></a>Configurare le connessioni e inviare
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name="verify-exchange-peering"></a><a name=get></a>Verificare un peering di Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>Risorse aggiuntive

Per altre informazioni, vedere [Domande frequenti sul peering Internet](faqs.md).

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare un peering di Exchange con il portale](howto-exchange-portal.md)
