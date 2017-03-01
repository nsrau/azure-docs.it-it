---
title: Come creare e gestire una zona DNS nel portale di Azure | Documentazione Microsoft
description: Informazioni su come creare le zone DNS in DNS di Azure. Si tratta di una guida dettagliata per creare e gestire la prima zona DNS nel portale di Azure.
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f44c5ea1-4c85-469e-888e-5f5b34451664
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 7119ce18e04b427c268d4d8636b1a5fa02ed0cc3
ms.lasthandoff: 02/27/2017

---

# <a name="create-a-dns-zone-in-the-azure-portal"></a>Creare una zona DNS nel portale di Azure

> [!div class="op_single_selector"]
> * [Portale di Azure](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Interfaccia della riga di comando di Azure 1.0](dns-getstarted-create-dnszone-cli-nodejs.md)
> * [Interfaccia della riga di comando di Azure 2.0](dns-getstarted-create-dnszone-cli.md)

Questo articolo illustra i passaggi per creare una zona DNS nel portale di Azure. È anche possibile creare una zona DNS con PowerShell o l'interfaccia della riga di comando.

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="create-a-dns-zone"></a>Creare una zona DNS

1. Accedere al portale di Azure
2. Nel menu Hub fare clic su **Nuovo > Rete >** e quindi fare clic su **Zona DNS** per aprire il pannello per creare una zona DNS.

    ![Zona DNS](./media/dns-getstarted-create-dnszone-portal/openzone650.png)

4. Nel pannello **Crea zona DNS** assegnare un nome alla zona DNS. ad esempio *contoso.com*.
 
    ![Crea zona](./media/dns-getstarted-create-dnszone-portal/newzone250.png)

5. Successivamente, specificare il gruppo di risorse da usare. È possibile creare un nuovo gruppo di risorse o selezionarne uno già esistente. Se si sceglie di creare un nuovo gruppo di risorse, usare il menu a discesa **Località** per specificare la località del gruppo di risorse. Si noti che questa impostazione indica la località del gruppo di risorse e non ha alcun impatto sulla zona DNS. La posizione della zona DNS è sempre "globale" e non viene visualizzata.

6. È possibile lasciare selezionata la casella di controllo **Aggiungi al dashboard** per trovare facilmente la nuova zona nel dashboard. Fare quindi clic su **Crea**.

    ![Aggiungi al dashboard](./media/dns-getstarted-create-dnszone-portal/pindashboard150.png)

7. Dopo aver fatto clic su Crea, la nuova zona viene configurata nel dashboard.

    ![Creating](./media/dns-getstarted-create-dnszone-portal/creating150.png)

8. Dopo aver creato la nuova zona, nel dashboard viene aperto il pannello per la nuova zona.

## <a name="view-records"></a>Visualizzare i record

La creazione di una zona DNS comporta anche la creazione dei record seguenti:

* Il record Origine di autorità (SOA), presente nella radice di ogni zona DNS.
* I record del server del nomi autorevole (NS), che mostrano quali server dei nomi ospitano la zona. DNS di Azure usa un pool di server dei nomi e dunque diversi server dei nomi potrebbero essere assegnati ad aree diverse nel servizio DNS di Azure. Per altre informazioni, vedere [Delegare un dominio a DNS di Azure](dns-domain-delegation.md).

Nella parte inferiore del pannello Zona DNS è possibile visualizzare i set di record per la zona DNS.

![zona](./media/dns-getstarted-create-dnszone-portal/viewzone500.png)

## <a name="test-name-servers"></a>Verifica server dei nomi

È possibile verificare che la zona DNS sia presenti sui server di nomi DNS di Azure usando strumenti DNS, ad esempio nslookup o DIG o il [cmdlet di PowerShell Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).

Se non è stato ancora delegato il dominio per usare la nuova zona in DNS di Azure, sarà necessario indirizzare la query DNS direttamente a uno dei server dei nomi per la zona. I server dei nomi per la zona sono presenti nel portale di Azure:
    
![zona](./media/dns-getstarted-create-dnszone-portal/viewzonens500.png)

Assicurarsi di sostituire il server dei nomi corretto per la propria zona nel comando seguente.

    nslookup
    > set type=SOA
    > server ns1-01.azure-dns.com
    > contoso.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    contoso.com
            primary name server = ns1-01.azure-dns.com
            responsible mail addr = azuredns-hostmaster.microsoft.com
            serial  = 1
            refresh = 3600 (1 hour)
            retry   = 300 (5 mins)
            expire  = 2419200 (28 days)
            default TTL = 300 (5 mins)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato una zona DNS, [creare set di record e record](dns-getstarted-create-recordset-portal.md) per creare i record DNS per il dominio Internet.


