---
services: virtual-machines
title: Configurazione di PowerShell
author: JoeDavies-MSFT
solutions: 
manager: timlt
editor: tysonn
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 05/12/2015
ms.author: rasquill
ms.openlocfilehash: 19c704d965ff3e2fc9ac8c5b623aeb386cb0b974
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2018
---
## <a name="setting-up-powershell"></a>Configurazione di PowerShell
Prima di usare Azure PowerShell, seguire questi passaggi.

### <a name="verify-powershell-versions"></a>Verificare le versioni di PowerShell
Per poter usare Windows PowerShell, è necessario disporre di Windows PowerShell versione 3.0 o 4.0. Per individuare la versione di Windows PowerShell, digitare il comando seguente al prompt dei comandi di Windows PowerShell.

    $PSVersionTable

Dovrebbe essere visualizzata una schermata analoga alla seguente.

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2

Verificare che il valore di **PSVersion** sia 3.0 o 4.0. Per installare una versione compatibile, vedere [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

È necessario disporre anche di Azure PowerShell versione 0.8.0 o successiva. Per verificare quale versione di Azure PowerShell è installata, è possibile utilizzare il comando del relativo prompt di Microsoft PowerShell.

    Get-Module azure | format-table version

Dovrebbe essere visualizzata una schermata analoga alla seguente.

    Version
    -------
    0.8.16.1

Per istruzioni e un collegamento alla versione più recente, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).

### <a name="set-your-azure-account-and-subscription"></a>Impostare l'account e la sottoscrizione di Azure
Se non si ha già una sottoscrizione di Azure, è possibile attivare i [benefici della sottoscrizione MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

Aprire un prompt dei comandi di Azure PowerShell e usare il comando seguente per accedere ad Azure.

    Add-AzureAccount

Se si dispone di più sottoscrizioni di Azure, è possibile usare il comando seguente per visualizzarne l'elenco.

    Get-AzureSubscription

Verrà visualizzato il tipo di informazioni seguente:

    SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
    SubscriptionName          : Visual Studio Ultimate with MSDN
    Environment               : AzureCloud
    SupportedModes            : AzureServiceManagement,AzureResourceManager
    DefaultAccount            : johndoe@contoso.com
    Accounts                  : {johndoe@contoso.com}
    IsDefault                 : True
    IsCurrent                 : True
    CurrentStorageAccountName : 
    TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

Per impostare la sottoscrizione di Azure corrente, eseguire i comandi seguenti al prompt dei comandi di Azure PowerShell. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < e >, con il nome corretto.

    $subscr="<SubscriptionName from the display of Get-AzureSubscription>"
    Select-AzureSubscription -SubscriptionName $subscr -Current    

Per altre informazioni su sottoscrizioni e account di Azure, vedere [Procedura: Connettersi alla sottoscrizione](/powershell/azureps-cmdlets-docs#Connect).

