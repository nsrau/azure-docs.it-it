---
title: File di inclusione
titleSuffix: Azure
description: File di inclusione
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: beffb2babefd86c2807e21e9337cba66f42fcfc2
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678461"
---
Prima di iniziare la configurazione, installare e importare i moduli necessari. Per installare i moduli in PowerShell, è necessario disporre dei privilegi di amministratore.

1. Installare e importare il modulo Az.
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Installare e importare il modulo Az.Peering.
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Verificare che i moduli importati correttamente utilizzando questo comando:
    ```powershell
    Get-Module
    ```
1. Accedere al proprio account di Azure con il seguente comando:
    ```powershell
    Connect-AzAccount
    ```
1. Controllare le sottoscrizioni per l'account e selezionare la sottoscrizione in cui si vuole creare un peering.
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. Se non si dispone già di un gruppo di risorse, è necessario crearne uno prima di creare un peering. A questo scopo, eseguire il comando seguente:

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> Se non sono già stati associati l'ASN e la sottoscrizione, seguire i passaggi descritti in [Associate Peer ASN](../howto-subscription-association-powershell.md). Questa azione è necessaria per richiedere un peering.

> [!NOTE]
> La posizione di un gruppo di risorse è indipendente dalla posizione in cui si sceglie di configurare un peering.
&nbsp;
