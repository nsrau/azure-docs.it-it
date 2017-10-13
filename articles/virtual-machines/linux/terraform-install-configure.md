---
title: Installare e configurare Terraform per eseguire il provisioning di macchine virtuali e altra infrastruttura in Azure | Microsoft Docs
description: Informazioni su come installare e configurare Terraform per la creazione di risorse di Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jtalkar
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/14/2017
ms.author: echuvyrov
ms.openlocfilehash: da567097be38ac649c6bf1de1508de24d21cb877
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Installare e configurare Terraform per eseguire il provisioning di macchine virtuali e altra infrastruttura in Azure 
In questo articolo vengono illustrati i passaggi necessari per installare e configurare Terraform al fine di eseguire il provisioning di risorse, ad esempio macchine virtuali in Azure. Verrà anche spiegato come creare e usare le credenziali di Azure per abilitare Terraform al provisioning di risorse cloud in modo sicuro.

HashiCorp Terraform offre un modo semplice per definire e distribuire l'infrastruttura cloud usando un linguaggio di modelli personalizzato, denominato linguaggio di configurazione HashiCorp (HCL). Questo linguaggio personalizzato è [facile da scrivere e capire](terraform-create-complete-vm.md). Inoltre, tramite il comando `terraform plan` è possibile visualizzare le modifiche all'infrastruttura prima del commit. Seguire questi passaggi per iniziare a usare Terraform con Azure.

## <a name="install-terraform"></a>Installazione di Terraform
Per installare Terraform, [scaricare](https://www.terraform.io/downloads.html) il pacchetto appropriato per il sistema operativo in uso in una directory di installazione distinta. Il download contiene un singolo file eseguibile, per cui è anche necessario definire un percorso globale. Per istruzioni su come impostare il percorso in Mac e Linux, vedere [questa pagina Web](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Per istruzioni su come impostare il percorso in Windows, vedere [questa pagina Web](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows). Per verificare l'installazione, eseguire il comando `terraform`. Si dovrebbe vedere un elenco di opzioni Terraform disponibili come output.

Successivamente, sarà necessario consentire l'accesso di Terraform alla sottoscrizione di Azure per eseguire il provisioning dell'infrastruttura.

## <a name="set-up-terraform-access-to-azure"></a>Impostazione dell'accesso di Terraform in Azure
Per abilitare Terraform al provisioning delle risorse in Azure, è necessario creare due entità in Azure Active Directory (AAD): un'applicazione Azure AD e un'entità servizio Azure AD. Gli identificatori di queste entità verranno usati negli script Terraform. L'entità servizio è un'istanza locale di un'applicazione Azure AD globale. Un'entità servizio consente il controllo di accesso locale granulare a risorse globali.

Esistono diversi modi per creare un'applicazione Azure AD e un'entità servizio Azure AD. Il modo più semplice e rapido oggi consiste nell'usare l'interfaccia della riga di comando di Azure 2.0, che [è possibile scaricare e installare in Windows, Linux o Mac](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli). È anche possibile usare PowerShell o l'interfaccia della riga di comando di Azure 1.0 per creare l'infrastruttura di sicurezza necessaria. Le istruzioni riportate di seguito illustrano come configurare Terraform per Azure usando tutti questi approcci.

### <a name="use-azure-cli-20-for-windows-linux-or-mac-users"></a>Usare l'interfaccia della riga di comando di Azure 2.0 (per gli utenti di Windows, Linux o Mac) 
Dopo avere scaricato e installato [l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli), eseguire l'accesso per amministrare la sottoscrizione di Azure eseguendo il comando seguente:

```
az login
```

>[!NOTE]
>Se si usa il cloud di Azure Cina, Azure Germania o Azure per enti pubblici, è necessario innanzitutto configurare l'interfaccia della riga di comando di Azure per garantirne la compatibilità con il cloud specifico. Per completare questa operazione, procedere come segue:

```
az cloud set --name AzureChinaCloud|AzureGermanCloud|AzureUSGovernment
```

Se si dispone di più sottoscrizioni di Azure, i relativi dettagli vengono restituiti dal comando `az login`. Impostare la variabile di ambiente `SUBSCRIPTION_ID` che conterrà il valore del campo `id` restituito dalla sottoscrizione che si intende usare. 

Selezionare la sottoscrizione da usare per la sessione.

```
az account set --subscription="${SUBSCRIPTION_ID}"
```

Eseguire una query dell'account per ottenere i valori di ID sottoscrizione e tenant.

```
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

Successivamente, creare credenziali separate per Terraform.

```
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Vengono restituiti appId, password, sp_name e tenant. Prendere nota di appId e password.

Confermare le proprie credenziali (entità servizio) aprendo una nuova shell ed eseguire i comandi seguenti. Sostituire i valori restituiti per sp_name, password e tenant:

```
az login --service-principal -u SP_NAME -p PASSWORD --tenant TENANT
az vm list-sizes --location westus
```

### <a name="use-powershell-for-windows-users"></a>Uso di PowerShell (per gli utenti di Windows) 
Per usare un computer Windows per scrivere ed eseguire gli script Terraform e usare PowerShell per attività di configurazione, è necessario configurare il computer con gli strumenti PowerShell appropriati. 

1. Installare gli strumenti di PowerShell seguendo i passaggi descritti in [Installare e configurare Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps). 

2. Scaricare ed eseguire lo [script azure-setup.ps1](https://github.com/echuvyrov/terraform101/blob/master/azureSetup.ps1) dalla console di PowerShell.

3. Per eseguire lo script azure-setup.ps1, scaricarlo ed eseguire il comando `./azure-setup.ps1 setup` dalla console di PowerShell. Accedere quindi alla sottoscrizione di Azure con privilegi amministrativi.

4. Specificare un nome di applicazione (stringa arbitraria, richiesto) quando richiesto. Facoltativamente, scegliere una password complessa quando viene richiesto. Se non si fornisce la password, viene automaticamente generata una password complessa mediante le librerie di sicurezza .NET.

### <a name="use-azure-cli-10-for-linux-or-mac-users"></a>Usare l'interfaccia della riga di comando di Azure 1.0 (per gli utenti Linux o Mac)
Per iniziare a usare Terraform su computer Linux o Mac con l'interfaccia della riga di comando di Azure 1.0, è necessario che nel computer siano installate le librerie appropriate.  

1. Installare gli strumenti dell'interfaccia della riga di comando di Azure xPlat seguendo i passaggi descritti in [Installare l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

2. Scaricare e installare un processore JSON seguendo le istruzioni in [Download jq](https://stedolan.github.io/jq/download/).

3. Scaricare ed eseguire il [bash script azure-setup.sh](https://github.com/mitchellh/packer/blob/master/contrib/azure-setup.sh) dalla console.

4. Per eseguire lo script azure-setup.sh, scaricarlo ed eseguire il comando `./azure-setup setup` dalla console. Accedere quindi alla sottoscrizione di Azure con privilegi amministrativi.
 
5. Specificare un nome di applicazione (stringa arbitraria, richiesto) quando richiesto. Facoltativamente, scegliere una password complessa quando viene richiesto. Se non si fornisce la password, viene automaticamente generata una password complessa mediante le librerie di sicurezza .NET.

Tutti gli script precedenti creano un'applicazione e un'entità servizio Azure AD. L'entità servizio ottiene un collaboratore o l'accesso a livello di proprietario nella sottoscrizione. Per via del livello elevato di accesso concesso, è consigliabile proteggere sempre le informazioni di sicurezza generate da tali script. Prendere nota di tutte e quattro le informazioni di sicurezza fornite da tali script: appId, password, subscription_id e tenant_id.

## <a name="set-environment-variables"></a>Impostare le variabili di ambiente
Dopo aver creato e configurato un'entità servizio Azure AD, è necessario comunicare a Terraform l'ID tenant, l'ID sottoscrizione, l'ID client e il segreto client da usare. A tale scopo, incorporare tali valori negli script Terraform, come descritto in [Creare un'infrastruttura di base usando Terraform](terraform-create-complete-vm.md). In alternativa, è possibile impostare le variabili di ambiente seguenti (e pertanto evitare di archiviare o condividere accidentalmente le credenziali):

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID

Di seguito è riportato uno script della shell di esempio che è possibile usare per impostare tali variabili:

```
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id
```

Se si usa Terraform con Azure per enti pubblici, Azure Germania o Azure Cina, è anche necessario impostare la variabile ENVIRONMENT in modo appropriato.

## <a name="next-steps"></a>Passaggi successivi
Abbiamo installato Terraform e configurato le credenziali di Azure per avviare la distribuzione dell'infrastruttura nella sottoscrizione di Azure. Vediamo ora come [creare l'infrastruttura con Terraform](terraform-create-complete-vm.md).
