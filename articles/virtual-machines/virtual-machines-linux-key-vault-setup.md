<properties
	pageTitle="Configurare l'insieme di credenziali delle chiavi per le macchine virtuali in Azure Resource Manager | Microsoft Azure"
	description="Come configurare un insieme di credenziali delle chiavi da usare con una macchina virtuale di Azure Resource Manager"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="singhkay"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="singhkay"/>

# Configurare l'insieme di credenziali delle chiavi per le macchine virtuali in Azure Resource Manager

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modello di distribuzione classica

In Azure Resource Manager gli stack, i segreti e i certificati vengono modellati come risorse fornite dal provider di risorse dell'insieme di credenziali delle chiavi. Per altre informazioni sugli insiemi di credenziali delle chiavi, vedere [Informazioni sull'insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-whatis.md)

## Impostazione
Per consentire l'uso dell'insieme di credenziali delle chiavi con le macchine virtuali di Azure Resource Manager, è necessario impostare su true la proprietà *EnabledForDeployment* nell'insieme di credenziali delle chiavi. È possibile eseguire questa operazione nei diversi client, come illustrato di seguito.

## CLI
Per creare un insieme di credenziali delle chiavi usando l'interfaccia della riga di comando, vedere [Gestire l'insieme di credenziali delle chiavi tramite l'interfaccia della riga di comando](../key-vault/key-vault-manage-with-cli.md#create-a-key-vault)

Per l'interfaccia della riga di comando è necessario creare prima di tutto l'insieme di credenziali delle chiavi, quindi abilitare i criteri di distribuzione. A questo scopo, è possibile eseguire questo comando:

	azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## Modelli
Se si usano i modelli, è necessario impostare la proprietà `enabledForDeployment` su `true` per la risorsa insieme di credenziali delle chiavi.

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

Per le altre opzioni configurabili durante la creazione di un insieme di credenziali delle chiavi tramite i modelli, vedere [qui](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)

<!---HONumber=AcomDC_0608_2016-->