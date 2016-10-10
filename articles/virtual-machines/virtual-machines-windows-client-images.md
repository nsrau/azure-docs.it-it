<properties
   pageTitle="Uso delle immagini di client Windows per scenari di sviluppo/test | Microsoft Azure"
   description="Come usare i vantaggi della sottoscrizione di Visual Studio per distribuire Windows 7/8/10 in Azure per scenari di sviluppo/test"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="iainfou"/>

# Uso di client Windows in Azure per scenari di sviluppo/test

A condizione di disporre di una sottoscrizione appropriata di Visual Studio (in precedenza MSDN), è possibile usare Windows 7, Windows 8 o Windows 10 in Azure per scenari di sviluppo/test. In questo articolo sono descritti i requisiti di idoneità per l'esecuzione di client Windows in Azure e l'utilizzo delle immagini della raccolta di Azure.


## Idoneità della sottoscrizione
I sottoscrittori di Visual Studio attivi (ovvero le persone che hanno acquistato una licenza di sottoscrizione per Visual Studio) possono usare client Windows per le finalità di sviluppo e test. Il client Windows può essere usato nel proprio hardware e nelle proprie macchine virtuali di Azure in esecuzione in qualsiasi tipo di sottoscrizione di Azure. Potrebbe non essere possibile distribuire o usare il client Windows in Azure per normali finalità di produzione oppure da parte degli utenti che non possiedono una sottoscrizione a Visual Studio attiva.

Per comodità, sono state rese disponibili alcune immagini di Windows 10 nella raccolta di Azure in [eligible dev/test offers](#eligible-offers). I sottoscrittori di Visual Studio per qualsiasi tipo di offerta possono anche [preparare e creare](virtual-machines-windows-prepare-for-upload-vhd-image.md) un'immagine a 64 bit di Windows 7, Windows 8 o Windows 10 e quindi [caricarla in Azure](virtual-machines-windows-upload-image.md). L'utilizzo rimane limitato alle attività di sviluppo e test da parte dei sottoscrittori di Visual Studio attivi.


## Offerte idonee
La tabella seguente descrive gli ID idonei per la distribuzione di Windows 10 tramite la raccolta di Azure. Le immagini di Windows 10 sono visibili solo per le offerte seguenti. I sottoscrittori di Visual Studio che necessitano di eseguire il client Windows in un altro tipo di offerta devono [preparare e creare](virtual-machines-windows-prepare-for-upload-vhd-image.md) un'immagine a 64 bit di Windows 7, Windows 8 o Windows 10 e quindi [caricarla in Azure](virtual-machines-windows-upload-image.md).

| Nome offerta | Numero offerta | Immagini client disponibili |
|:-----------|:------------:|:-----------------------:|
| [Sviluppo/test con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0023p/) | 0023P | Windows 10 |
| [Sottoscrittori di Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) | 0029P | Windows 10 |
| [Sottoscrittori di Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) | 0059P | Windows 10 |
| [Sottoscrittori di Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) | 0060P | Windows 10 |
| [Visual Studio Premium con MSDN (vantaggio)](https://azure.microsoft.com/offers/ms-azr-0061p/) | 0061P | Windows 10 |
| [Sottoscrittori di Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) | 0063P | Windows 10 |
| [Sottoscrittori di Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) | 0064P | Windows 10 |
| [Sviluppo/test Enterprise](https://azure.microsoft.com/ofers/ms-azr-0148p/) | 0148P | Windows 10 |


## Controllare la sottoscrizione di Azure
Se non si conosce l'ID offerta, è possibile risalirvi mediante il portale di Azure o il portale degli account.

L'ID offerta relativo alla sottoscrizione è indicato nel portale di Azure nel pannello delle sottoscrizioni:

![Dettagli dell'ID offerta sul portale di Azure](./media/virtual-machines-windows-client-images/offer_id_azure_portal.png)

È inoltre possibile visualizzare l'ID offerta nella [scheda delle sottoscrizioni](http://account.windowsazure.com/Subscriptions) del portale degli account di Azure:

![Dettagli dell'ID offerta sul portale degli account di Azure](./media/virtual-machines-windows-client-images/offer_id_azure_account_portal.png)


## Passaggi successivi
Ora è possibile distribuire le VM con [PowerShell](virtual-machines-windows-ps-create.md), i [modelli di Resource Manager](virtual-machines-windows-ps-template.md) o [Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

<!---HONumber=AcomDC_0928_2016-->