<properties
   pageTitle="Individuare e selezionare le immagini delle macchine virtuali Windows | Microsoft Azure"
   description="Informazioni su come determinare l'editore, l'offerta e l'SKU per le immagini durante la creazione di una macchina virtuale Windows con il modello di distribuzione di Gestione risorse."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   />

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure"
   ms.date="12/08/2015"
   ms.author="rasquill"/>

# Esplorare e selezionare immagini di macchine virtuali Windows in Azure con PowerShell o l'interfaccia della riga di comando

Questo argomento descrive come trovare editori, offerte, SKU e versioni per ciascuna località di distribuzione. Per fare un esempio, alcune immagini di macchina virtuale Windows usate comunemente sono:

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.

## Tabella delle immagini Windows usate comunemente


| PublisherName | Offerta | Sku |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| CoreOS | CoreOS | Beta |
| CoreOS | CoreOS | Stabile |
| MicrosoftDynamicsNAV | DynamicsNAV | 2015 |
| MicrosoftSharePoint | MicrosoftSharePointServer | 2013 |
| Macchina virtuale | Oracle-Database-12c-Weblogic-Server-12c | Standard |
| Macchina virtuale | Oracle-Database-12c-Weblogic-Server-12c | Enterprise |
| MicrosoftSQLServer | SQL2014-WS2012R2 | Enterprise-Optimized-for-DW |
| MicrosoftSQLServer | SQL2014-WS2012R2 | Enterprise-Optimized-for-OLTP |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer | WindowsServer | 2008 R2-SP1 |
| MicrosoftWindowsServer | WindowsServer | Windows-Server-Technical-Preview |
| MicrosoftWindowsServerEssentials | WindowsServerEssentials | WindowsServerEssentials |
| MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 2012R2 |


[AZURE.INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]

<!---HONumber=AcomDC_0330_2016-->