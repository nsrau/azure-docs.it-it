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
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: aa82dda778af927db0a204eb88bae445c086e309
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017

---

# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Installare e configurare Terraform per eseguire il provisioning di macchine virtuali e altra infrastruttura in Azure 
In questo articolo vengono illustrati in dettaglio i passaggi necessari per installare e configurare Terraform al fine di eseguire il provisioning di risorse, ad esempio macchine virtuali in Azure. Verrà anche spiegato come creare e usare le credenziali di Azure per abilitare Terraform al provisioning di risorse cloud in modo sicuro.

HashiCorp Terraform offre un modo semplice per definire e distribuire l'infrastruttura cloud usando un linguaggio di modelli personalizzato, denominato HCL. Questo linguaggio personalizzato è [facile da scrivere e capire](terraform-create-complete-vm.md). Tramite il comando "terraform plan", Terraform consente anche di visualizzare le modifiche all'infrastruttura prima di eseguirne il commit. Eseguire i passaggi seguenti per iniziare a usare Terraform con Azure.

## <a name="installing-terraform"></a>Installazione di Terraform
Per installare Terraform, [scaricare](https://www.terraform.io/downloads.html) il pacchetto appropriato per il sistema operativo in uso in una directory di installazione distinta. Il download contiene un singolo file eseguibile, per cui è anche necessario definire un percorso (PATH) globale. Le istruzioni su come impostare il percorso in Linux e Mac sono disponibili in [questa pagina](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux), mentre [questa pagina](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows) contiene istruzioni per impostare il percorso in Windows. Verificare l'installazione eseguendo il comando "terraform". Verrà visualizzato un elenco delle opzioni Terraform disponibili come output.

Successivamente, sarà necessario consentire l'accesso di Terraform alla sottoscrizione di Azure per eseguire il provisioning dell'infrastruttura.

## <a name="setting-up-terraform-access-to-azure"></a>Impostazione dell'accesso di Terraform in Azure
Per abilitare Terraform al provisioning delle risorse in Azure, è necessario creare due entità in Azure Active Directory (AAD): un'applicazione AAD e un'entità servizio AAD. Gli identificatori di queste entità verranno usati negli script Terraform. L'entità servizio è un'istanza locale di un'applicazione AAD globale. Un'entità servizio consente il controllo di accesso locale granulare a risorse globali.

Esistono diversi modi per creare un'applicazione AAD e un'entità servizio AAD. Il modo più semplice e rapido oggi consiste nell'usare l'interfaccia della riga di comando Azure 2.0, che [è possibile scaricare e installare in Linux/Windows/Mac](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli). È anche possibile usare PowerShell o l'interfaccia della riga di comando di Azure 1.0 per creare l'infrastruttura di sicurezza necessaria. Le istruzioni riportate di seguito illustrano come configurare Terraform per Azure usando tutti questi approcci.

### <a name="windowslinuxmac-users-using-azure-cli-20"></a>Utenti di Linux/Windows/Mac che usano l'interfaccia della riga di comando di Azure 2.0
Dopo aver scaricando e installato [l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli), eseguire l'accesso per amministrare la sottoscrizione di Azure eseguendo il comando seguente

```
az login
```

>[!NOTE]
>Se si usa il cloud di Azure Cina, Azure Germania o Azure per enti pubblici, è necessario innanzitutto configurare l'interfaccia della riga di comando di Azure per garantirne la compatibilità con il cloud specifico. A tale scopo, eseguire:

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

Vengono restituiti valori per client_id, client_secret (password), sp_name e tenant. Prendere nota dei valori di **client_id** e **client_secret**.

È possibile confermare le credenziali (entità servizio) aprendo una nuova shell ed eseguendo i comandi seguenti, sostituendo i valori restituiti per **sp_name**, **client_secret** e **tenant**:

```
az login --service-principal -u SP_NAME -p CLIENT_SECRET --tenant TENANT
az vm list-sizes --location westus
```

### <a name="windows-users-using-powershell"></a>Utenti Windows che usano PowerShell
Se si usa un computer Windows per scrivere ed eseguire gli script Terraform e si preferisce usare PowerShell per attività di configurazione, è necessario configurare il computer con gli strumenti PowerShell appropriati. A tale scopo, (1) [installare gli strumenti di Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps) e (2) scaricare ed eseguire lo script [azure-setup.ps1](https://github.com/echuvyrov/terraform101/blob/master/azureSetup.ps1) dalla console di PowerShell. Per eseguire lo script azure-setup.ps1, scaricarlo, eseguire il comando "./azure-setup.ps1 setup" dalla console di PowerShell ed eseguire l'accesso alla sottoscrizione Azure con privilegi amministrativi. Specificare quindi il nome di un'applicazione (stringa arbitraria, obbligatoria) quando richiesto e, facoltativamente, fornire una password complessa. Se non si fornisce la password, viene automaticamente generata una password complessa mediante le librerie di sicurezza .NET.

### <a name="linuxmac-users-using-azure-cli-10"></a>Utenti di Linux o Mac che usano l'interfaccia della riga di comando di Azure 1.0
Per iniziare a usare Terraform su computer Linux o Mac con l'interfaccia della riga di comando di Azure 1.0, è necessario verificare che nel computer siano installate le librerie appropriate. A tale scopo, (1) [installare gli strumenti dell'interfaccia della riga di comando di Azure xPlat](https://docs.microsoft.com/cli/azure/install-azure-cli), (2) [scaricare e installare ](https://stedolan.github.io/jq/download/) il processore JSON jq e (3) scaricare ed eseguire lo script bash [azure-setup.sh](https://github.com/mitchellh/packer/blob/master/contrib/azure-setup.sh) dalla console. Per eseguire lo script azure-setup.sh, scaricarlo, eseguire il comando "./azure-setup setup" dalla console ed eseguire l'accesso alla sottoscrizione Azure con privilegi amministrativi. Specificare quindi il nome di un'applicazione (stringa arbitraria, obbligatoria) quando richiesto e, facoltativamente, fornire una password complessa. Se non si fornisce la password, viene automaticamente generata una password complessa mediante le librerie di sicurezza .NET.

Tutti gli script precedenti creano un'applicazione AAD e un'entità servizio, conferendo a quest'ultima un accesso a livello di collaboratore o proprietario per la sottoscrizione. Per via del livello elevato di accesso concesso, è consigliabile proteggere sempre le informazioni di sicurezza generate da tali script. Prendere nota di tutte e quattro le informazioni di sicurezza fornite da tali script: client_id, client_secret, subscription_id e tenant_id. 

## <a name="setting-environment-variables"></a>Impostazioni delle variabili di ambiente
Dopo aver creato e configurato l'entità servizio AAD, è necessario comunicare a Terraform l'ID tenant, l'ID sottoscrizione, l'ID client e il segreto client da usare. A tale scopo, incorporare tali valori negli script Terraform (come descritto nella [sezione successiva](terraform-create-complete-vm.md)). In alternativa, è possibile impostare le variabili di ambiente seguenti (e pertanto evitare di archiviare/condividere accidentalmente le credenziali):

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID

Di seguito è riportato uno script della shell di esempio che è possibile usare per impostare tali variabili:

```
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_client_id
export ARM_CLIENT_SECRET=your_client_secret
export ARM_TENANT_ID=your_tenant_id
```

Se si usa Terraform con Azure per enti pubblici, Azure Germania o Azure Cina, è anche necessario impostare la variabile di ambiente in modo appropriato.

## <a name="next-steps"></a>Passaggi successivi
Abbiamo installato Terraform e configurato le credenziali di Azure per avviare la distribuzione dell'infrastruttura nella sottoscrizione di Azure. Vediamo ora come [creare l'infrastruttura con Terraform](terraform-create-complete-vm.md).
