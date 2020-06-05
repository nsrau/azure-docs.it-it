---
title: Convertire un peering diretto legacy in risorsa di Azure con il portale di Azure
titleSuffix: Azure
description: Convertire un peering diretto legacy in risorsa di Azure con il portale di Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 159e15a6be59991fba574e72dcaf2ed740fb10c2
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83846160"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Convertire un peering diretto legacy in risorsa di Azure con il portale di Azure

Questo articolo illustra come convertire un peering diretto legacy esistente in una risorsa di Azure usando il portale di Azure.

Se si preferisce, è possibile completare questa guida usando [PowerShell](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Prima di iniziare
* Prima di iniziare la configurazione, esaminare i [prerequisiti](prerequisites.md) e la [procedura dettagliata per il peering diretto](walkthrough-direct-all.md).


## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>Convertire un peering diretto legacy in una risorsa di Azure

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Accedere al portale e selezionare la sottoscrizione
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-a-legacy-direct-peering"></a><a name=create></a>Convertire un peering diretto legacy

I provider di servizi Internet possono convertire le connessioni peering dirette legacy usando la pagina [Create a Peering]( https://go.microsoft.com/fwlink/?linkid=2129593) (Crea un peering).

1. Nella scheda **Generale** della pagina **Create a Peering** (Crea peering) compilare le caselle come illustrato nell'immagine seguente:

    > [!div class="mx-imgBorder"] 
    > ![Registrare un servizio di peering](./media/setup-basics-tab.png)

*    Selezionare la sottoscrizione di Azure.

* Per Gruppo di risorse, è possibile sceglierne uno esistente nell'elenco a discesa o crearne uno nuovo selezionando Crea nuovo. Per questo esempio, creare un nuovo gruppo di risorse.

* Il nome corrisponde a quello della risorsa e può essere qualsiasi nome desiderato.

* Se si sceglie un gruppo di risorse esistente, l'area viene selezionata automaticamente. Se si sceglie di creare un nuovo gruppo di risorse, è necessario scegliere anche l'area di Azure in cui deve trovarsi la risorsa.

>[!NOTE]
>L'area in cui si trova un gruppo di risorse è indipendente dalla località in cui si vuole creare il peering con Microsoft. Tuttavia, è consigliabile organizzare le risorse di peering all'interno di gruppi di risorse che si trovano nelle aree di Azure più vicine. Ad esempio, per peering in Ashburn è possibile creare un gruppo di risorse nell'area Stati Uniti orientali o Stati Uniti orientali 2.

* Selezionare l'ASN nella casella **ASN peer**.

>[!IMPORTANT] 
>È possibile scegliere un ASN solo con stato di convalida approvato prima di inviare una richiesta di peering. Se la richiesta dell'ASN del peer è appena stata inviata, attendere 12 ore che venga approvata l'associazione dell'ASN. Se la convalida dell'ASN selezionato è in sospeso, verrà visualizzato un messaggio di errore. Se l'ASN che è necessario scegliere non è visualizzato, verificare di aver selezionato la sottoscrizione corretta. Se sì, controllare se è già stato creato un ASN del peer facendo riferimento a **[Associare l'ASN del peer alla sottoscrizione di Azure](https://go.microsoft.com/fwlink/?linkid=2129592)** .

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Avviare la risorsa e configurare le impostazioni di base
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurare le connessioni e inviare
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verificare il peering diretto
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Risorse aggiuntive

Per altre informazioni, vedere [Domande frequenti sul peering Internet](faqs.md).

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare un peering diretto con il portale](howto-direct-portal.md)
