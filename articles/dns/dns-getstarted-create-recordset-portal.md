---
title: Creare record e set di record per una zona DNS con il portale di Azure | Documentazione Microsoft
description: Come creare i record host per DNS di Azure e i set di record e i record usando il portale di Azure
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f93905f4-e82e-45db-b490-878d318e6aba
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 2752579b6277a27ca1e932fbd2c402ac3135e80c
ms.lasthandoff: 02/27/2017

---

# <a name="create-dns-record-sets-and-records-by-using-the-azure-portal"></a>Creare set di record e record DNS mediante il portale di Azure

> [!div class="op_single_selector"]
> * [Portale di Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Interfaccia della riga di comando di Azure 1.0](dns-getstarted-create-recordset-cli-nodejs.md)
> * [Interfaccia della riga di comando di Azure 2.0](dns-getstarted-create-recordset-cli.md)

Questo articolo illustra la creazione di record e set di record con il portale di Azure. A tale scopo, è necessario comprendere i record e i set di record DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Gli esempi in questa pagina usano tutti il tipo di record DNS "A". Il processo per altri tipi di record è simile.

Se il nuovo record ha lo stesso nome e lo stesso tipo di un record esistente, è necessario aggiungerlo al set di record esistente&mdash;vedere [Gestire record e set di record DNS con il portale di Azure](dns-operations-recordsets-portal.md). Se il nuovo record ha un nome e un tipo diversi rispetto a tutti i record esistenti, è necessario creare un nuovo set di record, come illustrato di seguito.

## <a name="create-records-in-a-new-record-set"></a>Creare record in un nuovo set di record

L’esempio seguente fornisce indicazioni sulla procedura per creare un set di record e record con il portale di Azure.

1. Accedere al portale.
2. Accedere al pannello **Zona DNS** in cui creare un set di record.
3. Nella parte superiore del pannello **Zona DNS** selezionare **+ Record set** (Aggiungi set di record) per aprire il pannello **Aggiungi set di record**.

    ![Nuovo set di record](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. Nel pannello **Aggiungi set di record** assegnare un nome al set di record, ad esempio "**www**".

    ![Aggiungi set di record](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. Selezionare il tipo di record da creare. Ad esempio, selezionare **A**.
6. Impostare **TTL**. La durata predefinita per la permanenze nel portale è un'ora.
7. Aggiungere i dettagli di ogni record nel set di record. In questo caso, poiché il tipo di record è "A", è necessario aggiungere gli indirizzi IP del record, uno per riga.
8. Dopo aver aggiunto gli indirizzi IP, selezionare **OK** nella parte inferiore del pannello. Verrà creato il set di record DNS.

### <a name="verify-name-resolution"></a>Verificare la risoluzione dei nomi

È possibile verificare che i record DNS siano presenti sui server di nomi DNS di Azure usando gli strumenti DNS come nslookup, DIG o il [cmdlet di PowerShell Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).

Se non è stato ancora delegato il dominio per usare la nuova zona in DNS di Azure, sarà necessario [indirizzare la query DNS direttamente a uno dei server dei nomi per la zona](dns-getstarted-create-dnszone.md#test-name-servers). Assicurarsi di sostituire i valori corretti per la propria zona di record nel comando seguente.

    nslookup
    > set type=A
    > server ns1-01.azure-dns.com
    > www.contoso.com

    Server:  ns1-01.azure-dns.com
    Address:  40.90.4.1

    Name:    www.contoso.com
    Address:  1.2.3.4

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [delegare il nome di dominio ai server dei nomi DNS di Azure](dns-domain-delegation.md)

Per gestire i set di record e i record, vedere le informazioni su [come creare e gestire record e set di record DNS mediante il portale di Azure](dns-operations-recordsets-portal.md).

