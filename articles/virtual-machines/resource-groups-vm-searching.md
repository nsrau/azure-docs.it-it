<properties
   pageTitle="Individuare e selezionare immagini VM | Microsoft Azure"
   description="Informazioni su come determinare l'editore, l'offerta e lo SKU per le immagini durante la creazione di una macchina virtuale di Azure con il modello di distribuzione di Gestione risorse."
   services="virtual-machines"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   />

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="command-line-interface"
   ms.workload="infrastructure"
   ms.date="08/25/2015"
   ms.author="rasquill"/>

# Esplorazione e selezione di immagini delle macchine virtuali di Azure con Windows PowerShell e l'interfaccia della riga di comando di Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Questo articolo è dedicato alla ricerca di immagini di macchine virtuali nel modello di distribuzione di Gestione risorse. È inoltre possibile cercare immagini nel [modello di distribuzione classica](https://msdn.microsoft.com/library/azure/dn499770.aspx).

Questo articolo descrive come individuare e selezionare le immagini di macchina virtuale, utilizzando un'installazione recente di Azure PowerShell o Azure CLI. Come prerequisito, è necessario modificare la modalità di gestione delle risorse. Con l'interfaccia della riga di comando di Azure, immettere tale modalità digitando `azure config mode arm`. Con PowerShell digitare `Switch-AzureMode AzureResourceManager`. Per informazioni più complete sull'aggiornamento e la configurazione, vedere [Interfaccia della riga di comando di Azure con Gestione risorse](xplat-cli-azure-resource-manager.md) e [Azure PowerShell con Gestione risorse di Azure](../powershell-azure-resource-manager.md).

## Tabella delle immagini di uso comune


| PublisherName | Offerta | Sku |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| OpenLogic | CentOS | 7 |
| OpenLogic | CentOS | 7\.1 |
| CoreOS | CoreOS | Beta |
| CoreOS | CoreOS | Stabile |
| MicrosoftDynamicsNAV | DynamicsNAV | 2015 |
| MicrosoftSharePoint | MicrosoftSharePointServer | 2013 |
| Macchina virtuale | Oracle-Database-12c-Weblogic-Server-12c | Standard |
| Macchina virtuale | Oracle-Database-12c-Weblogic-Server-12c | Enterprise |
| MicrosoftSQLServer | SQL2014-WS2012R2 | Enterprise-Optimized-for-DW |
| MicrosoftSQLServer | SQL2014-WS2012R2 | Enterprise-Optimized-for-OLTP |
| Canonical | UbuntuServer | 12\.04.5-LTS |
| Canonical | UbuntuServer | 14\.04.2-LTS |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer | WindowsServer | 2008 R2-SP1 |
| MicrosoftWindowsServer | WindowsServer | Windows-Server-Technical-Preview |
| MicrosoftWindowsServerEssentials | WindowsServerEssentials | WindowsServerEssentials |
| MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 2012R2 |


## Interfaccia della riga di comando di Azure

Il modo più semplice e rapido per individuare un'immagine da usare con `azure vm quick-create` o per creare un file dei modelli di gruppi di risorse consiste nel chiamare il comando `azure vm image list` e passare il percorso, il nome dell'editore (non distingue tra maiuscole e minuscole) e, se è nota, un'offerta. Contrariamente a molti elenchi, solitamente lunghi, l'elenco seguente rappresenta un breve esempio, in cui si sa che "Canonical" è un editore per l'offerta "UbuntuServer".

    azure vm image list westus canonical ubuntuserver
    info:    Executing command vm image list
    warn:    The parameter --sku if specified will be ignored
    + Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
    data:    Publisher  Offer         Sku          Version          Location  Urn
    data:    ---------  ------------  -----------  ---------------  --------  --------------------------------------------------
    data:    canonical  ubuntuserver  12.04-DAILY  12.04.201504201  westus    canonical:ubuntuserver:12.04-DAILY:12.04.201504201
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201302250  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201302250
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201303250  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201303250
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201304150  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201304150
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201305160  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201305160
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201305270  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201305270
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201306030  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201306030
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201306240  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201306240

La colonna **Urn** rappresenta il formato che verrà passato a `azure vm quick-create`.

Tuttavia, spesso non si conoscono gli elementi disponibili. In questo caso, è possibile spostarsi tra le immagini individuando gli editori tramite `azure vm image list-publishers` e rispondendo alla richiesta di percorso con un percorso al data center che si prevede di usare per il gruppo di risorse. Ad esempio, di seguito sono elencati tutti gli editori di immagini nel percorso degli Stati Uniti occidentali (per passare l'argomento location, usare caratteri minuscoli e rimuovere gli spazi dai percorsi standard)

    azure vm image list-publishers
    info:    Executing command vm image list-publishers
    Location: westus
    + Getting virtual machine image publishers (Location: "westus")
    data:    Publisher                                       Location
    data:    ----------------------------------------------  --------
    data:    a10networks                                     westus  
    data:    aiscaler-cache-control-ddos-and-url-rewriting-  westus  
    data:    alertlogic                                      westus  
    data:    AlertLogic.Extension                            westus  


Questi elenchi possono essere piuttosto lunghi, quindi nell'esempio ne viene riportato solo un frammento. Si supponga che Canonical sia un editore di immagini nel percorso degli Stati Uniti occidentali. È possibile individuare le offerte chiamando `azure vm image list-offers`, quindi passare il percorso e l'editore quando richiesto, come nell'esempio seguente:

    azure vm image list-offers
    info:    Executing command vm image list-offers
    Location: westus
    Publisher: canonical
    + Getting virtual machine image offers (Publisher: "canonical" Location:"westus")
    data:    Publisher  Offer         Location
    data:    ---------  ------------  --------
    data:    canonical  UbuntuServer  westus  
    info:    vm image list-offers command OK

A questo punto, si sa che nell'area degli Stati Uniti occidentali Canonical pubblica l'offerta **UbuntuServer** in Azure. Ma quali sono le SKU? Per saperlo, chiamare `azure vm image list-skus` e rispondere alla richiesta indicando il percorso, l'editore e l'offerta individuati.

    azure vm image list-skus
    info:    Executing command vm image list-skus
    Location: westus
    Publisher: canonical
    Offer: ubuntuserver
    + Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
    data:    Publisher  Offer         sku          Location
    data:    ---------  ------------  -----------  --------
    data:    canonical  ubuntuserver  12.04-DAILY  westus  
    data:    canonical  ubuntuserver  12.04.2-LTS  westus  
    data:    canonical  ubuntuserver  12.04.3-LTS  westus  
    data:    canonical  ubuntuserver  12.04.4-LTS  westus  
    data:    canonical  ubuntuserver  12.04.5-LTS  westus  
    data:    canonical  ubuntuserver  12.10        westus  
    data:    canonical  ubuntuserver  14.04-beta   westus  
    data:    canonical  ubuntuserver  14.04-DAILY  westus  
    data:    canonical  ubuntuserver  14.04.0-LTS  westus  
    data:    canonical  ubuntuserver  14.04.1-LTS  westus  
    data:    canonical  ubuntuserver  14.04.2-LTS  westus  
    data:    canonical  ubuntuserver  14.10        westus  
    data:    canonical  ubuntuserver  14.10-beta   westus  
    data:    canonical  ubuntuserver  14.10-DAILY  westus  
    data:    canonical  ubuntuserver  15.04        westus  
    data:    canonical  ubuntuserver  15.04-beta   westus  
    data:    canonical  ubuntuserver  15.04-DAILY  westus  
    info:    vm image list-skus command OK

Con queste informazioni è possibile individuare precisamente l'immagine desiderata chiamando la chiamata originale riportata nella parte superiore.

    azure vm image list westus canonical ubuntuserver 14.04.2-LTS
    info:    Executing command vm image list
    + Getting virtual machine images (Publisher:"canonical" Offer:"ubuntuserver" Sku: "14.04.2-LTS" Location:"westus")
    data:    Publisher  Offer         Sku          Version          Location  Urn
    data:    ---------  ------------  -----------  ---------------  --------  --------------------------------------------------
    data:    canonical  ubuntuserver  14.04.2-LTS  14.04.201503090  westus    canonical:ubuntuserver:14.04.2-LTS:14.04.201503090
    data:    canonical  ubuntuserver  14.04.2-LTS  14.04.20150422   westus    canonical:ubuntuserver:14.04.2-LTS:14.04.20150422
    data:    canonical  ubuntuserver  14.04.2-LTS  14.04.201504270  westus    canonical:ubuntuserver:14.04.2-LTS:14.04.201504270
    info:    vm image list command OK

A questo punto è possibile scegliere con precisione l'immagine da usare. Per creare rapidamente una macchina virtuale utilizzando le informazioni URN trovate o per utilizzare un modello con tali informazioni URN vedere [Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Gestione risorse di Azure](xplat-cli-azure-resource-manager.md).

### Video con la procedura dettagliata

Questo video illustra i passaggi precedenti usando l'interfaccia della riga di comando.

[AZURE.VIDEO resource-groups-vm-searching-cli]


## PowerShell

Quando si crea una nuova macchina virtuale con Gestione risorse di Azure, in alcuni casi è necessario specificare un'immagine combinando le seguenti proprietà dell'immagine:

- Editore
- Offerta
- SKU

Ad esempio, questi valori sono necessari per il cmdlet di PowerShell **Set-AzureVMSourceImage** o con un file dei modelli di gruppi di risorse in cui è necessario specificare il tipo di macchina virtuale da creare.

Se necessario, è possibile esplorare le immagini per determinare i valori nei seguenti modi:

1. Elencando gli editori di immagini.
2. Elencando le offerte di un determinato editore.
3. Elencando le SKU di una determinata offerta.

Per eseguire queste operazioni in PowerShell, passare in modalità Gestione risorse di Azure PowerShell.

	Switch-AzureMode AzureResourceManager

Per il primo passaggio riportato in precedenza, elencare gli editori con questi comandi.

	$locName="<Azure location, such as West US>"
	Get-AzureVMImagePublisher -Location $locName | Select PublisherName

Specificare il nome dell'editore prescelto ed eseguire questi comandi.

	$pubName="<publisher>"
	Get-AzureVMImageOffer -Location $locName -Publisher $pubName | Select Offer

Specificare il nome dell'offerta prescelta ed eseguire questi comandi.

	$offerName="<offer>"
	Get-AzureVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus

Nella visualizzazione del comando **Get-AzureVMImageSku** si hanno tutte le informazioni necessarie per specificare l'immagine per una nuova macchina virtuale.

Di seguito è fornito un esempio.

	PS C:\> $locName="West US"
	PS C:\> Get-AzureVMImagePublisher -Location $locName | Select PublisherName

	PublisherName
	-------------
	a10networks
	aiscaler-cache-control-ddos-and-url-rewriting-
	alertlogic
	AlertLogic.Extension
	Barracuda.Azure.ConnectivityAgent
	barracudanetworks
	basho
	boxless
	bssw
	Canonical
	...

Per l'editore "MicrosoftWindowsServer":

	PS C:\> $pubName="MicrosoftWindowsServer"
	PS C:\> Get-AzureVMImageOffer -Location $locName -Publisher $pubName | Select Offer

	Offer
	-----
	WindowsServer

Per l'offerta "WindowsServer":

	PS C:\> $offerName="WindowsServer"
	PS C:\> Get-AzureVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus

	Skus
	----
	2008-R2-SP1
	2012-Datacenter
	2012-R2-Datacenter
	Windows-Server-Technical-Preview

In questo elenco, copiare il nome della SKU prescelta per disporre di tutte le informazioni per il cmdlet di PowerShell **Set-AzureVMSourceImage** o per un file dei modelli di gruppi di risorse che richiede di specificare l'editore, l'offerta e la SKU per un'immagine.

### Video con la procedura dettagliata

Questo video illustra i passaggi precedenti usando PowerShell.

[AZURE.VIDEO resource-groups-vm-searching-posh]


<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/
[msn]: http://search.msn.com/

<!---HONumber=Oct15_HO2-->