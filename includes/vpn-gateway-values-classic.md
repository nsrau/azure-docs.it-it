---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/08/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 11d2172d085fe9b47587f4084908f99d7b54437e
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92103238"
---
Quando si creano reti virtuali classiche nel portale di Azure, il nome da visualizzare non è il nome completo usato per PowerShell. Ad esempio, una rete virtuale che sembra avere il nome **TestVNet1** nel portale potrebbe avere un nome molto più lungo nel file di configurazione di rete. Per un VNet nel gruppo di risorse "ClassicRG", il nome potrebbe essere simile al seguente: **Group ClassicRG TestVNet1**. Quando si creano le connessioni, è importante usare i valori visualizzati nel file di configurazione di rete.

Nelle procedure seguenti, ci si connetterà al proprio account Azure per scaricare e visualizzare il file di configurazione di rete per ottenere i valori necessari alle connessioni.

1. Scaricare e installare la versione più recente dei cmdlet di PowerShell per Gestione del servizio di Azure. La maggior parte degli utenti ha i moduli Gestione risorse installati localmente, ma non i moduli di gestione dei servizi. I moduli di gestione dei servizi sono legacy e devono essere installati separatamente. Per ulteriori informazioni, vedere [installare i cmdlet di gestione dei servizi](/powershell/azure/servicemanagement/install-azure-ps).

1. Aprire la console di PowerShell con diritti elevati e connettersi all'account. Usare gli esempi seguenti per facilitare la connessione. È necessario eseguire questi comandi localmente usando il modulo di gestione dei servizi di PowerShell. Connettersi all'account. Per eseguire la connessione, usare gli esempi che seguono:

   ```powershell
   Add-AzureAccount
   ```
1. Controllare le sottoscrizioni per l'account.

   ```powershell
   Get-AzureSubscription
   ```
1. Se sono disponibili più sottoscrizioni, selezionare la sottoscrizione da usare.

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```
1. Creare una directory nel computer. Ad esempio, C:\AzureVNet
1. Esportare il file di configurazione di rete nella directory. In questo esempio, il file di configurazione di rete viene esportato in **C:\AzureNet**.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
1. Aprire il file con un editor di testo e visualizzare il nome delle reti virtuali e dei siti. Questi nomi saranno i nomi usati quando si creano le connessioni.<br>I nomi di **VNet** sono elencati come **nome VirtualNetworkSite =**<br>I nomi dei **siti** sono elencati come **nome LocalNetworkSiteRef =**