---
title: includere file
description: includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/08/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 75aec9f3509881c35de9309fa1532b961fb2bc03
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875559"
---
Quando si creano reti virtuali classiche nel portale di Azure, il nome da visualizzare non è il nome completo usato per PowerShell. Ad esempio, una rete virtuale che sembra avere il nome **TestVNet1** nel portale potrebbe avere un nome molto più lungo nel file di configurazione di rete. Il nome potrebbe essere simile a: **Gruppo ClassicRG TestVNet1**. Quando si creano le connessioni, è importante usare i valori visualizzati nel file di configurazione di rete.

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
1. Esportare e visualizzare il file di configurazione di rete. Creare una directory nel computer ed esportarvi il file di configurazione di rete. In questo esempio, il file di configurazione di rete viene esportato in **C:\AzureNet**.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
1. Aprire il file con un editor di testo e visualizzare il nome delle reti virtuali e dei siti. Questi nomi saranno i nomi usati quando si creano le connessioni.<br>I nomi delle reti virtuali sono elencati come **Nome VirtualNetworkSite =**<br>I nomi dei siti sono elencati come **Nome LocalNetworkSiteRef =**