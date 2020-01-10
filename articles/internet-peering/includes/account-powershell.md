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
ms.openlocfilehash: 3a5f7157ef8f3645dd03ec93684238dd8bbc067e
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774224"
---
Prima di iniziare la configurazione, installare e importare i moduli richiesti. Per installare i moduli in PowerShell sono necessari i privilegi di amministratore.

1. Installare e importare AZ Module
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Installare e importare il modulo AZ. peering
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Verificare che i moduli vengano importati correttamente usando il comando seguente.
    ```powershell
    Get-Module
    ```
1. Accedere al proprio account Azure usando il comando seguente.
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
> Se non è già stato associato il numero ASN e la sottoscrizione, seguire la procedura per associare il numero [ASN peer](../howto-subscription-association-powershell.md). Questa operazione è necessaria per richiedere un peering.

> [!NOTE]
> Il percorso del gruppo di risorse è indipendente dalla posizione in cui si sceglie di configurare un peering.
&nbsp;
