---
title: Creare o modificare un peering di Exchange con il portale di Azure
titleSuffix: Azure
description: Creare o modificare un peering di Exchange con il portale di Azure
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/2/2020
ms.author: derekol
ms.openlocfilehash: 69201c97882846fb929b3b6f9a90be6647603bcc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84700481"
---
# <a name="create-or-modify-an-exchange-peering-by-using-the-azure-portal"></a>Creare o modificare un peering di Exchange con il portale di Azure

Questo articolo descrive come creare un peering di Microsoft Exchange usando il portale di Azure. Questo articolo descrive come controllare lo stato della risorsa, aggiornarla o eliminarla ed effettuarne il deprovisioning.

Se si preferisce, è possibile completare questa esercitazione usando [Azure PowerShell](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Prima di iniziare
* Prima di iniziare la configurazione, esaminare i [prerequisiti](prerequisites.md) e la [procedura dettagliata per il peering di Exchange](walkthrough-exchange-all.md).
* Se esistono già peering di Exchange con Microsoft che non sono convertiti in risorse di Azure, vedere [Convertire un peering di Exchange legacy in risorsa di Azure tramite il portale](howto-legacy-exchange-portal.md).

## <a name="create-and-provision-an-exchange-peering"></a>Creare un peering di Exchange ed effettuarne il provisioning

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Accedere al portale e selezionare la sottoscrizione
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Creare un peering di Exchange


I provider di Internet Exchange possono creare una richiesta di peering di Exchange tramite la [creazione di un peering]( https://go.microsoft.com/fwlink/?linkid=2129593).

1. Nella scheda **Generale**, nella pagina **Create a Peering** (Crea peering) completare le caselle nel modo seguente:

    > [!div class="mx-imgBorder"] 
    > ![Registrare un servizio di peering](./media/setup-basics-tab.png)

*    Selezionare la sottoscrizione di Azure.

* Per il gruppo di risorse, è possibile sceglierne uno esistente nell'elenco a discesa o crearne uno nuovo selezionando Crea nuova. Per questo esempio, creare un nuovo gruppo di risorse.

* Il nome corrisponde a quello della risorsa e può essere qualsiasi nome desiderato.

* Se si sceglie un gruppo di risorse esistente, l'area viene selezionata automaticamente. Se si sceglie di creare un nuovo gruppo di risorse, è necessario scegliere anche l'area di Azure in cui deve trovarsi la risorsa.

>[!NOTE]
>L'area in cui si trova un gruppo di risorse è indipendente dalla località in cui si vuole creare il peering con Microsoft. Tuttavia, è consigliabile organizzare le risorse di peering all'interno di gruppi di risorse che si trovano nelle aree di Azure più vicine. Ad esempio, per peering in Ashburn, è consigliabile creare un gruppo di risorse nell'area Stati Uniti orientali o Stati Uniti orientali 2.

* Selezionare l'ASN nella casella **ASN peer**.

>[!IMPORTANT] 
>È possibile scegliere un ASN solo con stato di convalida approvato prima di inviare una richiesta di peering. Se la richiesta dell'ASN del peer è appena stata inviata, attendere 12 ore che venga approvata l'associazione dell'ASN. Se la convalida dell'ASN selezionato è in sospeso, verrà visualizzato un messaggio di errore. Se l'ASN che è necessario scegliere non è visualizzato, verificare di aver selezionato la sottoscrizione corretta. Se sì, controllare se è già stata creata una richiesta di ASN del peer facendo riferimento a **[Associare l'ASN del peer alla sottoscrizione di Azure](https://go.microsoft.com/fwlink/?linkid=2129592)** .

* Selezionare **Avanti: Configurazione** per continuare.

#### <a name="configure-connections-and-submit"></a>Configurare le connessioni e inviare
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name="verify-an-exchange-peering"></a><a name=get></a>Verificare un peering di Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Modificare un peering di Exchange
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Effettuare il deprovisioning di un peering di Exchange
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare un peering diretto con il portale](howto-direct-portal.md)
* [Convertire un peering diretto legacy in risorsa di Azure con il portale](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Risorse aggiuntive

Per altre informazioni, vedere [Domande frequenti sul peering Internet](faqs.md).
