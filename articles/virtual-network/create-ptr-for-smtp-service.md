---
title: Configurare zone di ricerca inversa per un controllo del banner SMTP in Azure | Microsoft Docs
description: Descrive come configurare zone di ricerca inversa per un controllo del banner SMTP in Azure
services: virtual-network
documentationcenter: virtual-network
author: genlin
manager: WillChen
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/06/2018
ms.author: genli
ms.custom: 
ms.openlocfilehash: 1e95b00ea08105238a860265e46275c24ed7bfbd
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2018
---
#  <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Configurare zone di ricerca inversa per un controllo del banner SMTP

Questo articolo descrive come usare una zona inversa in DNS di Azure e creare un record DNS (PTR) inverso per il controllo del banner SMTP. 

## <a name="symptom"></a>Sintomo

Se si ospita un server SMTP in Microsoft Azure, quando si invia o si riceve un messaggio da server di posta remoti è possibile ricevere il messaggio di errore seguente:

**554: No PTR Record** (554: Nessun record PTR) 

## <a name="solution"></a>Soluzione

Per gli indirizzi IP virtuali in Azure, i record inversi vengono creati in zone del dominio di proprietà di Microsoft, non in zone del dominio personalizzate.

Per configurare record PTR in zone di proprietà di Microsoft, usare la proprietà -ReverseFqdn nella risorsa PublicIpAddress. Per altre informazioni, vedere [Configurare il DNS inverso per i servizi ospitati in Azure](../dns/dns-reverse-dns-for-azure-services.md). 

Quando si configurano i record PTR, assicurarsi che l'indirizzo IP e il nome di dominio completo (FQDN) inverso siano di proprietà della sottoscrizione. Se si tenta di impostare un nome di dominio completo inverso non appartenente alla sottoscrizione, viene visualizzato il messaggio di errore seguente:

    Set-AzureRmPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership: 
                        
    1) ReverseFqdn corrisponde al FQDN di tutte le risorse IP pubbliche nella sottoscrizione; 
    2) ReverseFqdn viene risolto nell'FQDN (attraverso la catena di record CName) di tutte le risorse IP pubbliche nella sottoscrizione; 
    3) ReverseFqdn viene risolto nell'indirizzo IP (attraverso la catena di record CName e A) di una risorsa IP pubblica statica nella sottoscrizione. 

Se si modifica manualmente il banner SMTP in modo che corrisponda al nome di dominio completo inverso predefinito, il server di posta remoto può comunque generare un errore, perché prevede che l'host del banner SMTP corrisponda al record MX per il dominio.