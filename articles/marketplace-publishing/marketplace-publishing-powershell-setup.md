<properties
   pageTitle="Configurazione di PowerShell per la creazione di una VM per Marketplace | Microsoft Azure"
   description="Istruzioni per la configurazione di Azure PowerShell e l'uso come un flusso di processo facoltativo per la creazione di immagini di VM da distribuire e vendere in Azure Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/04/2016"
   ms.author="hascipio"/>

# Configurare Azure PowerShell per la creazione di un'offerta per Azure Marketplace
Per informazioni dettagliate su come configurare PowerShell in Azure, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md). Un approccio semplice consiste nell'usare il metodo basato sul certificato, che scarica e importa un certificato necessario per l'autenticazione. Per ottenere il certificato richiesto, usare il cmdlet **Get-AzurePublishSettingsFile**. Quando richiesto, salvare il file. Per importare il certificato in una sessione di PowerShell, usare il cmdlet **Import-AzurePublishSettingsFile**.

Per configurare e archiviare le impostazioni comuni della sottoscrizione di Microsoft Azure per la sessione di PowerShell, usare i cmdlet **Set-AzureSubscription** e **Select-AzureSubscription**:

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

Il primo comando associa un account di archiviazione predefinito alla sottoscrizione (necessario per alcune operazioni di provisioning delle macchine virtuali). Il secondo rende corrente la sottoscrizione (riconosciuta da altri cmdlet).

## Vedere anche
- [Guida introduttiva: Come pubblicare un'offerta in Azure Marketplace](marketplace-publishing-getting-started.md)
- [Creazione di un'immagine di macchina virtuale per Marketplace](marketplace-publishing-vm-image-creation.md)

<!---HONumber=AcomDC_0211_2016-->