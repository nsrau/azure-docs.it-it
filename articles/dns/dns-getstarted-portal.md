---
title: Introduzione a DNS Azure con il portale di Azure | Microsoft Docs
description: Informazioni su come creare una zona e un record DNS in DNS Azure. Si tratta di una guida dettagliata per creare e gestire la prima zona e il primo record DNS usando il portale di Azure.
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: jonatul
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 0fee02286f6d5dd4614a933590cdab43cc69237f
ms.lasthandoff: 03/11/2017

---

# <a name="get-started-with-azure-dns-using-the-azure-portal"></a>Introduzione a DNS Azure con il portale di Azure

> [!div class="op_single_selector"]
> * [Portale di Azure](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Interfaccia della riga di comando di Azure 1.0](dns-getstarted-cli-nodejs.md)
> * [Interfaccia della riga di comando di Azure 2.0](dns-getstarted-cli.md)

Questo articolo illustra i passaggi per creare la prima zona e il primo record DNS nel portale di Azure. È possibile eseguire questi passaggi usando Azure PowerShell o nell'interfaccia della riga di comando di Azure multipiattaforma.

Una zona DNS viene usata per ospitare i record DNS per un particolare dominio. Per iniziare a ospitare il dominio in DNS di Azure, è necessario creare una zona DNS per il nome di dominio. Ogni record DNS per il dominio viene quindi creato all'interno di questa zona DNS. Per pubblicare infine la zona DNS su Internet, è necessario configurare i server dei nomi per il dominio. Ogni passaggio è illustrato di seguito.

## <a name="create-a-dns-zone"></a>Creare una zona DNS

1. Accedere al portale di Azure
2. Nel menu Hub fare clic su **Nuovo > Rete >** e quindi fare clic su **Zona DNS** per aprire il pannello per creare una zona DNS.

    ![Zona DNS](./media/dns-getstarted-portal/openzone650.png)

4. Nel pannello **Crea zona DNS** assegnare un nome alla zona DNS. ad esempio *contoso.com*.
 
    ![Crea zona](./media/dns-getstarted-portal/newzone250.png)

5. Successivamente, specificare il gruppo di risorse da usare. È possibile creare un nuovo gruppo di risorse o selezionarne uno già esistente. Se si sceglie di creare un nuovo gruppo di risorse, usare il menu a discesa **Località** per specificare la località del gruppo di risorse. Si noti che questa impostazione indica la località del gruppo di risorse e non ha alcun impatto sulla zona DNS. La posizione della zona DNS è sempre "globale" e non viene visualizzata.

6. È possibile lasciare selezionata la casella di controllo **Aggiungi al dashboard** per trovare facilmente la nuova zona nel dashboard. Fare quindi clic su **Crea**.

    ![Aggiungi al dashboard](./media/dns-getstarted-portal/pindashboard150.png)

7. Dopo aver fatto clic su Crea, la nuova zona viene configurata nel dashboard.

    ![Creating](./media/dns-getstarted-portal/creating150.png)

8. Dopo aver creato la nuova zona, nel dashboard viene aperto il pannello per la nuova zona.


## <a name="create-a-dns-record"></a>Creare un record DNS

L'esempio seguente fornisce indicazioni dettagliate sul processo di creazione di un nuovo record 'A'. Per altri tipi di record e per modificare i record esistenti, vedere [Gestire record e set di record DNS con il portale di Azure](dns-operations-recordsets-portal.md). 


1. Nella parte superiore del pannello **Zona DNS** selezionare **+ Record set** (Aggiungi set di record) per aprire il pannello **Aggiungi set di record**.

    ![Nuovo set di record](./media/dns-getstarted-portal/newrecordset500.png)

4. Nel pannello **Aggiungi set di record** assegnare un nome al set di record, ad esempio "**www**".

    ![Aggiungi set di record](./media/dns-getstarted-portal/addrecordset500.png)

5. Selezionare il tipo di record da creare. Per questo esempio, selezionare **A**.
6. Impostare **TTL**. La durata predefinita è un'ora.
7. Aggiungere l'indirizzo IP del record.
8. Selezionare **OK** nella parte inferiore del pannello per creare il record DNS.


## <a name="view-records"></a>Visualizzare i record

Nella parte inferiore del pannello Zona DNS è possibile visualizzare i record per la zona DNS. Verranno visualizzati i record NS e SOA predefiniti, che vengono creati in ogni zona, ed eventuali nuovi record creati.

![zona](./media/dns-getstarted-portal/viewzone500.png)


## <a name="update-name-servers"></a>Aggiornare i server dei nomi

Dopo essersi assicurati che la zona e i record DNS siano stati configurati correttamente, è necessario configurare il nome di dominio in modo che usi i server dei nomi di DNS Azure. Ciò consente agli altri utenti su Internet di trovare i record DNS.

I server dei nomi per la zona sono presenti nel portale di Azure:

![zona](./media/dns-getstarted-portal/viewzonens500.png)

I server dei nomi devono essere configurati con il registrar dei nomi di dominio, in cui è stato acquistato il nome di dominio. Il registrar offrirà l'opzione per la configurazione dei server dei nomi per il dominio. Per altre informazioni, vedere [Delegare un dominio al servizio DNS Azure](dns-domain-delegation.md).


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul servizio DNS Azure, vedere [Panoramica di DNS Azure](dns-overview.md).

Per altre informazioni sulla gestione dei record DNS in DNS Azure, vedere [Gestire record e set di record DNS con il portale di Azure](dns-operations-recordsets-portal.md).


