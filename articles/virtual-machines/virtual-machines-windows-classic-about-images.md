<properties
	pageTitle="Informazioni sulle immagini per le macchine virtuali Windows | Microsoft Azure"
	description="Informazioni sull'utilizzo delle immagini con macchine virtuali Windows in Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="cynthn"/>

# Informazioni sulle immagini per le macchine virtuali Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-about-images](../../includes/virtual-machines-common-classic-about-images.md)]



## Utilizzo di immagini

È possibile utilizzare il modulo Azure PowerShell per gestire le immagini disponibili per la sottoscrizione di Azure. È inoltre possibile utilizzare il portale di Azure classico per alcune attività di immagine, ma la riga di comando offre ulteriori opzioni.


-	**Ottenere tutte le immagini**:`Get-AzureVMImage`restituisce un elenco di tutte le immagini disponibili nella sottoscrizione corrente: le immagini, nonché quelli forniti da Azure o partner. Ciò significa che è possibile ottenere un elenco di grandi dimensioni. Negli esempi successivi viene illustrato come ottenere un elenco più breve.
-	**Recupero delle famiglie di immagine**:`Get-AzureVMImage | select ImageFamily` Ottiene un elenco delle famiglie di immagine mostrando stringhe **family** proprietà.
-	**Ottenere tutte le immagini in un gruppo specifico**: `Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
-	**Trovare immagini VM**: `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` funziona filtrando la proprietà DataDiskConfiguration, che si applica solo alle immagini di macchina virtuale. In questo esempio viene inoltre Filtra l'output solo il nome di etichetta e l'immagine.
-	**Salvare un'immagine generalizzata**: `Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
-	**Salvare un'immagine specializzata**: `Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`
>[Azure.Tip] Il parametro OSState è obbligatorio se si desidera creare un'immagine di macchina virtuale, che include dischi dati, nonché il disco del sistema operativo. Se non si utilizza il parametro, il cmdlet crea un'immagine del sistema operativo. Il valore del parametro indica se l'immagine è generalizzata o specializzata, basati su se il disco del sistema operativo è stato preparato per il riutilizzo.
-	**Eliminare un'immagine**: `Remove-AzureVMImage –ImageName "MyOldVmImage"`


## Passaggi successivi

È anche possibile [creare una macchina Windows mediante il portale classico](virtual-machines-windows-classic-tutorial.md)

<!---HONumber=AcomDC_0727_2016-->