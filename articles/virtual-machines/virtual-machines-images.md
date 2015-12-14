<properties
	pageTitle="Informazioni sulle immagini per le macchine virtuali | Microsoft Azure"
	description="Informazioni sull'utilizzo delle immagini con macchine virtuali in Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/13/2015"
	ms.author="cynthn"/>

# Informazioni sulle immagini per le macchine virtuali

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.

Le immagini vengono utilizzate in Azure per fornire una nuova macchina virtuale con un sistema operativo. Un'immagine potrebbe inoltre essere uno o più dischi dati. Sono disponibili immagini da diverse origini:

-	Azure offre immagini di [Marketplace](http://azure.microsoft.com/gallery/virtual-machines/). Sono disponibili versioni recenti di Windows Server e distribuzioni del sistema operativo Linux. Alcune immagini contengono anche le applicazioni, ad esempio SQL Server. Gli abbonati MSDN vantaggio e uso prepagato MSDN hanno accesso a immagini aggiuntive.
-	Community open source offre immagini tramite [VM Depot](http://vmdepot.msopentech.com/List/Index).
-	È inoltre possibile archiviare e utilizzare le proprie immagini in Azure, dal caricamento di un'immagine o una macchina virtuale di Azure esistente da utilizzare come immagine di acquisizione.

## Sulle immagini di macchina virtuale e immagini del sistema operativo

È possibile utilizzare due tipi di immagini in Azure: *immagine di macchina virtuale* e *immagine del sistema operativo*. Immagine di macchina virtuale include un sistema operativo e tutti i dischi collegati a una macchina virtuale quando viene creata l'immagine. Si tratta del tipo più recente dell'immagine. Prima dell'introduzione immagini di macchina virtuale, un'immagine in Azure potrebbe contenere solo un sistema operativo generalizzato e non altri dischi. Immagine di macchina virtuale che contiene solo un sistema operativo generalizzato è sostanzialmente identico al tipo originale dell'immagine, l'immagine del sistema operativo.

È possibile creare immagini personalizzate, in base a una macchina virtuale in Azure o una macchina virtuale in esecuzione in un' posizione che copiata e caricata. Se si desidera utilizzare un'immagine per creare più di una macchina virtuale, sarà necessario prepararla per l'utilizzo come immagine generalizzandola. Per creare un'immagine di Windows Server, eseguire il comando Sysprep nel server per generalizzarla prima di caricare il file con estensione vhd. Per altre informazioni su Sysprep, vedere [Come usare Sysprep: Introduzione](http://go.microsoft.com/fwlink/p/?LinkId=392030). Per creare un'immagine Linux, a seconda della distribuzione software, è necessario eseguire un set di comandi che sono specifici per la distribuzione, nonché di eseguire l'agente Linux di Azure.

## Utilizzo di immagini

È possibile utilizzare l'interfaccia della riga di comando di Azure (CLI) per modulo Mac, Linux e Windows o Azure PowerShell per gestire le immagini disponibili per la sottoscrizione di Azure. È inoltre possibile utilizzare il portale di Azure classico per alcune attività di immagine, ma la riga di comando offre ulteriori opzioni.

Per informazioni sull'utilizzo di questi strumenti con le distribuzioni di gestione delle risorse, vedere [Immagini Navigating e selezione di macchina virtuale di Azure con PowerShell e Azure CLI](resource-groups-vm-searching.md).

Per esempi dell'utilizzo di strumenti in una distribuzione classica:

- Per CLI, vedere "Comandi per gestire le immagini di macchina virtuale di Azure" in [Uso dell’interfaccia della riga di comando di Azure per Mac, Linux e Windows con Gestione servizi di Azure](virtual-machines-command-line-tools.md)
- Per Azure PowerShell, vedere il seguente elenco di comandi. Per un esempio di ricerca di un'immagine per creare una macchina virtuale, vedere "Passaggio 3: determinare la Imagefamily" in [Uso di Azure PowerShell per creare e preconfigurare macchine virtuali basate su Windows](virtual-machines-ps-create-preconfigure-windows-vms.md)

-	**Ottenere tutte le immagini**:`Get-AzureVMImage`restituisce un elenco di tutte le immagini disponibili nella sottoscrizione corrente: le immagini presenti, nonché quelle forniti da Azure o partner. Ciò significa che è possibile ottenere un elenco di grandi dimensioni. Negli esempi successivi viene illustrato come ottenere un elenco più breve.
-	**Recupero delle famiglie di immagine**:`Get-AzureVMImage | select ImageFamily` ottiene un elenco delle famiglie di immagine mostrando stringhe di proprietà **ImageFamily**.
-	**Ottenere tutte le immagini in una famiglia specifica**: `Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
-	**Trovare immagini VM**: `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` funziona filtrando la proprietà DataDiskConfiguration, che si applica solo alle immagini di macchina virtuale. In questo esempio viene inoltre Filtra l'output solo il nome di etichetta e l'immagine.
-	**Salvare un'immagine generalizzata**: `Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
-	**Salvare un'immagine specializzata**: `Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`
>[Azure.Tip]Il parametro OSState è obbligatorio se si desidera creare un'immagine di macchina virtuale, che include dischi dati, nonché il disco del sistema operativo. Se non si utilizza il parametro, il cmdlet crea un'immagine del sistema operativo. Il valore del parametro indica se l'immagine è generalizzata o specializzata, basati su se il disco del sistema operativo è stato preparato per il riutilizzo.
-	**Eliminare un'immagine**: `Remove-AzureVMImage –ImageName "MyOldVmImage"`

## Risorse aggiuntive

[Diversi modi per creare una macchina virtuale Linux](virtual-machines-linux-choices-create-vm.md)

[Diversi modi per creare una macchina virtuale Windows](virtual-machines-windows-choices-create-vm.md)

<!---HONumber=AcomDC_1203_2015-->