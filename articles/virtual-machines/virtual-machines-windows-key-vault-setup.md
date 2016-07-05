<properties
	pageTitle="Configurare l'insieme di credenziali delle chiavi per le macchine virtuali in Azure Resource Manager | Microsoft Azure"
	description="Come configurare un insieme di credenziali delle chiavi da usare con una macchina virtuale di Azure Resource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="singhkay"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="singhkay"/>

# Configurare l'insieme di credenziali delle chiavi per le macchine virtuali in Azure Resource Manager

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] modello di distribuzione classica

In Azure Resource Manager gli stack, i segreti e i certificati vengono modellati come risorse fornite dal provider di risorse dell'insieme di credenziali delle chiavi. Per altre informazioni sugli insiemi di credenziali delle chiavi, vedere [Informazioni sull'insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-whatis.md)

Per consentire l'uso dell'insieme di credenziali delle chiavi con le macchine virtuali di Azure Resource Manager, è necessario impostare su true la proprietà *EnabledForDeployment* nell'insieme di credenziali delle chiavi. È possibile farlo in vari tipi di client.

## Utilizzare PowerShell per configurare l'insieme di credenziali delle chiavi
Per creare un insieme di credenziali delle chiavi usando PowerShell, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-get-started.md#vault).

Per i nuovi insiemi di credenziali delle chiavi, è possibile usare questo cmdlet di PowerShell:

	New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Per gli insiemi di credenziali delle chiavi esistenti, è possibile usare questo cmdlet di PowerShell:

	Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## Usare l'interfaccia della riga di comando per impostare l'insieme di credenziali delle chiavi
Per creare un insieme di credenziali delle chiavi usando l'interfaccia della riga di comando (CLI), vedere l'articolo su come [gestire l'insieme di credenziali delle chiavi tramite l'interfaccia della riga di comando](../key-vault/key-vault-manage-with-cli.md#create-a-key-vault).

Per l'interfaccia della riga di comando, prima di assegnare i criteri di distribuzione è necessario creare l'insieme di credenziali delle chiavi. A questo scopo, è possibile eseguire questo comando:

	azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## Utilizzare modelli per configurare l'insieme di credenziali delle chiavi
Se si usa un modello, è necessario impostare la proprietà `enabledForDeployment` su `true` per la risorsa dell'insieme di credenziali delle chiavi.

	{
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

Per altre opzioni che è possibile configurare quando si crea un insieme di credenziali delle chiavi utilizzando i modelli, vedere l'articolo su come [creare un insieme di credenziali delle chiavi](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).

<!---HONumber=AcomDC_0622_2016-->