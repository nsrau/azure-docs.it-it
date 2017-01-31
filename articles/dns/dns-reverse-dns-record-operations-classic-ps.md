---
title: Gestire i record DNS inversi per i servizi di Azure (classico) con PowerShell | Documentazione Microsoft
description: 'Come gestire i record DNS inversi o i record PTR per i servizi di Azure tramite PowerShell nel modello di distribuzione classico. '
services: DNS
documentationcenter: na
author: s-malone
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 9c24d176-6bce-4277-a14c-80fe44a20a87
ms.service: DNS
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2016
ms.author: smalone
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: fe5c9cfe0edd769635301fdcb40fc1f71d430165


---
# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-classic-using-azure-powershell"></a>Come gestire i record DNS inversi per i servizi (classico) tramite PowerShell

[!INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]


[!INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]


> [!IMPORTANT]
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. Informazioni su come [eseguire questa procedura con il modello di Resource Manager](dns-reverse-dns-record-operations-ps.md).

## <a name="validation-of-reverse-dns-records"></a>Convalida dei record DNS inversi
Per impedire che qualcun altro possa creare record DNS inversi mappati ai propri domini DNS, Azure consente la creazione di un record DNS inverso solo se una delle condizioni seguenti è vera:

* Il nome di dominio completo (FQDN) del DNS inverso è il nome del servizio cloud per il quale è stato specificato o il nome di un qualsiasi servizio cloud nella stessa sottoscrizione, ad esempio il DNS inverso è "contosoapp1.cloudapp.net.".
* Il nome di dominio completo del DNS inverso si risolve in avanti nel nome o indirizzo IP del servizio cloud per cui è stato specificato o nel nome di qualsiasi servizio cloud o indirizzo IP nella stessa sottoscrizione, ad esempio il DNS inverso è "app1.contoso.com.", un alias CName per contosoapp1.cloudapp.net.

Controlli di convalida vengono eseguiti solo quando la proprietà DNS inverso per un servizio cloud viene impostata o modificata. La convalida non viene ripetuta periodicamente.

## <a name="add-reverse-dns-to-existing-cloud-services"></a>Aggiungere il DNS inverso ai servizi cloud esistenti
È possibile aggiungere un record DNS inverso a un servizio cloud esistente usando il cmdlet "Set-AzureService".

    PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## <a name="create-a-cloud-service-with-reverse-dns"></a>Creare un servizio cloud con il DNS inverso
È possibile aggiungere un nuovo servizio cloud con la proprietà DNS inverso specificata usando il cmdlet “Set-AzureService”:

    PS C:\> New-AzureService –ServiceName “contosoapp1” –Location “West US” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## <a name="view-reverse-dns-for-existing-cloud-services"></a>Visualizzare il DNS inverso per i servizi cloud esistenti
È possibile visualizzare il valore configurato per un servizio cloud esistente usando il cmdlet "Get-AzureService".

    PS C:\> Get-AzureService "contosoapp1"

## <a name="remove-reverse-dns-from-existing-cloud-services"></a>Rimuovere il DNS inverso dai servizi cloud esistenti
È possibile rimuovere una proprietà DNS inverso da un servizio cloud esistente utilizzando il cmdlet “Set-AzureService”. Questa operazione viene eseguita impostando il valore della proprietà DNS inverso su un valore vuoto:

    PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “”

[!INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[!INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-asm-include.md)]




<!--HONumber=Dec16_HO2-->


