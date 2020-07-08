---
title: Creare o modificare un peering di Direct con il portale di Azure
titleSuffix: Azure
description: Creare o modificare un peering di Direct con il portale di Azure
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/19/2020
ms.author: derekol
ms.openlocfilehash: e49462612b58163c2ac51b78584761d0d8b8bd06
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84700566"
---
# <a name="create-or-modify-a-direct-peering-by-using-the-azure-portal"></a>Creare o modificare un peering di Direct con il portale di Azure

Questo articolo descrive come creare un peering di Direct per un provider di servizi Internet o un provider di Internet Exchange usando il portale di Azure. Questo articolo descrive come controllare lo stato della risorsa, aggiornarla o eliminarla ed effettuarne il deprovisioning.

Se si preferisce, è possibile completare questa guida usando Azure [PowerShell](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Prima di iniziare
* Prima di iniziare la configurazione, esaminare i [prerequisiti](prerequisites.md) e la [procedura dettagliata per il peering di Direct](walkthrough-direct-all.md).
* Se esistono già connessioni di peering di Direct con Microsoft che non sono convertite in risorse di Azure, vedere [Convertire un peering di Direct legacy in risorsa di Azure tramite il portale](howto-legacy-direct-portal.md).

## <a name="create-and-provision-a-direct-peering"></a>Creare un peering di Direct ed effettuarne il provisioning

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Accedere al portale e selezionare la sottoscrizione
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Creare un peering di Direct

I provider di servizi internet o Internet Exchange possono creare una nuova richiesta di peering di Direct tramite la [creazione di un peering]( https://go.microsoft.com/fwlink/?linkid=2129593).

1. Nella scheda **Generale** della pagina **Create a Peering** (Crea peering) compilare le caselle come illustrato nell'immagine seguente:


    ![Registrare il Servizio di peering](./media/setup-basics-tab.png)

2. Selezionare la sottoscrizione di Azure.

3. Per Gruppo di risorse, è possibile sceglierne uno esistente nell'elenco a discesa o crearne uno nuovo selezionando Crea nuovo. Per questo esempio, creare un nuovo gruppo di risorse.

4. Il nome corrisponde a quello della risorsa e può essere qualsiasi nome desiderato.

5. Se si sceglie un gruppo di risorse esistente, l'area viene selezionata automaticamente. Se si sceglie di creare un nuovo gruppo di risorse, è necessario scegliere anche l'area di Azure in cui deve trovarsi la risorsa.

    >[!NOTE]
    > L'area in cui si trova un gruppo di risorse è indipendente dalla località in cui si vuole creare il peering con Microsoft. Tuttavia, è consigliabile organizzare le risorse di peering all'interno di gruppi di risorse che si trovano nelle aree di Azure più vicine. Ad esempio, per peering in Ashburn è possibile creare un gruppo di risorse nell'area Stati Uniti orientali o Stati Uniti orientali 2.

6. Selezionare l'ASN nella casella **ASN peer**.

    >[!IMPORTANT]
    >È possibile scegliere un ASN solo con stato di convalida approvato prima di inviare una richiesta di peering. Se la richiesta dell'ASN del peer è appena stata inviata, attendere 12 ore che venga approvata l'associazione dell'ASN. Se la convalida dell'ASN selezionato è in sospeso, verrà visualizzato un messaggio di errore. Se l'ASN che è necessario scegliere non è visualizzato, verificare di aver selezionato la sottoscrizione corretta. Se sì, controllare se è già stata creata una richiesta di ASN del peer facendo riferimento a **[Associare l'ASN del peer alla sottoscrizione di Azure](https://go.microsoft.com/fwlink/?linkid=2129592)** .

7. Selezionare **Avanti: Configurazione** per continuare.



    ![Registrare il Servizio di peering](./media/setup-direct-basics-filled-tab.png)


#### <a name="configure-connections-and-submit"></a>Configurare le connessioni e inviare
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verificare il peering di Direct
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Modificare un peering di Direct
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Effettuare il deprovisioning di un peering di Direct
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare un peering di Exchange con il portale](howto-exchange-portal.md)
* [Convertire un peering di Exchange legacy in una risorsa di Azure con il portale](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Risorse aggiuntive

Per altre informazioni, vedere [Domande frequenti sul peering Internet](faqs.md).
