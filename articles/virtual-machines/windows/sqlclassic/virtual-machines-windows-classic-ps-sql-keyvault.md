---
title: Integrare il Key Vault con SQL Server in macchine virtuali Windows in Azure (classico) | Documentazione Microsoft
description: Informazioni su come automatizzare la configurazione della crittografia di SQL Server per l'uso con Azure Key Vault. Questo argomento illustra come usare l'integrazione di Azure Key Vault con le macchine virtuali di SQL Server nel modello di distribuzione classico.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: ab8d41a7-1971-4032-ab71-eb435c455dc1
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/17/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: e20e2a094e1fd88dfc2a25b586dc6c894f92b418
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58312445"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Configurare l'integrazione di Azure Key Vault per SQL Server in macchine virtuali di Azure (distribuzione classica)
> [!div class="op_single_selector"]
> * [Gestione risorse](../sql/virtual-machines-windows-ps-sql-keyvault.md)
> * [Classico](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Panoramica
Esistono più funzionalità di crittografia di SQL Server, ad esempio [Transparent Data Encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [crittografia a livello di colonna (CLE)](https://msdn.microsoft.com/library/ms173744.aspx) e [crittografia di backup](https://msdn.microsoft.com/library/dn449489.aspx). Queste modalità di crittografia richiedono la gestione e l'archiviazione delle chiavi usate per la crittografia. Il servizio dell'insieme di credenziali delle chiavi di Azure (AKV) è progettato per migliorare la sicurezza e la gestione di queste chiavi in una posizione sicura e a elevata disponibilità. Il [connettore di SQL Server](https://www.microsoft.com/download/details.aspx?id=45344) consente a SQL Server di usare queste chiavi dall'insieme di credenziali delle chiavi di Azure.

> [!IMPORTANT] 
> Azure offre due modelli di distribuzione diversi per creare e usare le risorse: [Resource Manager e distribuzione classica](../../../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.

Se si esegue SQL Server con computer locali, sono disponibili [passaggi per accedere a Azure Key Vault dal computer locale con SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). Se si esegue SQL Server in macchine virtuali di Azure, è possibile risparmiare tempo usando la funzionalità di *Integrazione di Azure Key Vault* . Con alcuni cmdlet di Azure PowerShell che abilitano questa funzionalità, è possibile automatizzare la configurazione necessaria per l'accesso all'insieme di credenziali delle chiavi di una macchina virtuale di SQL.

Quando questa funzionalità è abilitata, installa automaticamente il connettore di SQL Server, configura il provider EKM per accedere ad Azure Key Vault e crea le credenziali per consentire l'accesso all'insieme di credenziali. Se sono stati esaminati i passaggi nella documentazione locale menzionati in precedenza, si noterà che questa funzionalità consente di automatizzare i passaggi 2 e 3. L'unica attività che è comunque necessario eseguire manualmente è la creazione delle chiavi e dell'insieme di credenziali delle chiavi. Una volta completata questa operazione, l'intera installazione della macchina virtuale di SQL è automatizzata. Quando la funzionalità ha completato l'installazione, è possibile eseguire istruzioni T-SQL per iniziare la crittografia dei database o del backup regolarmente.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Configurare l'integrazione di AKV
Usare PowerShell per configurare l'integrazione di Azure Key Vault. Le sezioni seguenti forniscono una panoramica dei parametri necessari e quindi uno script di PowerShell di esempio.

### <a name="install-the-sql-server-iaas-extension"></a>Installare l'estensione di SQL Server IaaS
Come prima operazione, [installare l'estensione di SQL Server IaaS](../classic/sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>Comprendere i parametri di input
La tabella seguente elenca i parametri necessari per eseguire lo script di PowerShell nella sezione successiva.

| Parametro | DESCRIZIONE | Esempio |
| --- | --- | --- |
| **$akvURL** |**URL dell'insieme di credenziali delle chiavi** |"https:\//contosokeyvault.vault.azure.net/" |
| **$spName** |**Nome entità servizio** |"fde2b411-33d5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**Segreto entità servizio** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=" |
| **$credName** |**Nome credenziali**: L'integrazione dell'insieme di credenziali delle chiavi di Azure crea le credenziali all'interno di SQL Server, consentendo alla VM di accedere all'insieme di credenziali delle chiavi. Scegliere un nome per la credenziale. |"mycred1" |
| **$vmName** |**Nome macchina virtuale**: il nome di una macchina virtuale di SQL creato in precedenza. |"myvmname" |
| **$serviceName** |**Nome servizio:** il nome del servizio cloud associato alla macchina virtuale di SQL. |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>Abilitare l'integrazione di AKV con PowerShell
Il cmdlet **New-AzureVMSqlServerKeyVaultCredentialConfig** crea un oggetto di configurazione per la funzionalità di integrazione di Azure Key Vault. Il cmdlet **Set-AzureVMSqlServerExtension**configura l'integrazione con il parametro **KeyVaultCredentialSettings**. I passaggi seguenti illustrano come usare questi comandi.

1. In Azure PowerShell configurare innanzitutto i parametri di input con i valori specifici, come descritto nelle sezioni precedenti di questo argomento. Di seguito è riportato uno script di esempio.
   
        $akvURL = "https:\//contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. Usare quindi lo script seguente per configurare e abilitare l'integrazione di AKV.
   
        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

L'estensione dell'agente IaaS di SQL aggiornerà la macchina virtuale di SQL con questa nuova configurazione.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

