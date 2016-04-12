<properties
   pageTitle="Individuare e selezionare immagini di macchine virtuali Linux | Microsoft Azure"
   description="Informazioni su come determinare l'editore, l'offerta e lo SKU per le immagini durante la creazione di una macchina virtuale Linux con il modello di distribuzione di Resource Manager."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   />

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="03/14/2016"
   ms.author="rasquill"/>

# Individuare e selezionare immagini di macchine virtuali Linux in Azure con l'interfaccia della riga di comando o PowerShell

Questo argomento descrive come trovare editori, offerte, SKU e versioni per ciascuna località di distribuzione. Per fare un esempio, alcune immagini di macchine virtuali Linux usate comunemente sono:

**Tabella delle immagini Linux di uso comune**


| PublisherName | Offerta | Sku |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| OpenLogic | CentOS | 7 |
| OpenLogic | CentOS | 7\.1 |
| Canonical | UbuntuServer | 12\.04.5-LTS |
| Canonical | UbuntuServer | 14\.04.2-LTS |


[AZURE.INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]

<!---HONumber=AcomDC_0330_2016-->