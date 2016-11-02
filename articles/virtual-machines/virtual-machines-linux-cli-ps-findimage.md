<properties
   pageTitle="Selezionare immagini di VM Linux con l'interfaccia della riga di comando di Azure | Microsoft Azure"
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
   ms.date="08/23/2016"
   ms.author="rasquill"/>

# Selezionare immagini di VM Linux con l'interfaccia della riga di comando di Azure

Questo argomento descrive come trovare editori, offerte, SKU e versioni per ciascuna posizione di distribuzione. Per fare un esempio, alcune immagini di macchine virtuali Linux usate comunemente sono:

**Tabella delle immagini Linux di uso comune**


| PublisherName | Offerta | Sku |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| RedHat | RHEL | 7,2 |
| credativ | Debian | 8 | 
| SUSE | openSUSE | 13\.2 |
| SUSE | SLES | 12-SP1 |
| OpenLogic | CentOS | 7\.1 |
| Canonical | UbuntuServer | 14\.04.4-LTS |
| CoreOS | CoreOS | Stabile |


[AZURE.INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]

<!---HONumber=AcomDC_0824_2016-->