<properties 
 pageTitle="Gestire le estensioni delle macchine virtuali | Microsoft Azure" 
 description="Illustra come aggiungere, trovare, aggiornare e rimuovere le estensioni." 
 services="virtual-machines" 
 documentationCenter="" 
 authors="squillace" 
 manager="timlt" 
 editor=""/>
<tags 
 ms.service="virtual-machines" 
 ms.devlang="na" 
 ms.topic="article" 
 ms.tgt_pltfrm="vm-multiple" 
 ms.workload="infrastructure-services"
 ms.date="03/10/2015" 
 ms.author="rasquill"/>
#Gestire le estensioni delle macchine virtuali
Illustra come trovare, aggiungere, modificare o rimuovere estensioni di VM con macchine virtuali Windows o Linux in Azure.

##Uso delle estensioni di macchina virtuale

Le estensioni di macchina virtuale di Azure implementano comportamenti o funzionalità che semplificano l'esecuzione di altri programmi su macchine virtuali di Azure (ad esempio, l'estensione **WebDeployForVSDevTest** consente a Visual Studio di eseguire distribuzioni Web di soluzioni nella macchina virtuale di Azure) o permettono di interagire con la macchina virtuale per supportare altri comportamenti (ad esempio, è possibile usare le estensioni di accesso alle macchine virtuali di Powershell, xplat-cli, e i client REST per reimpostare o modificare i valori di accesso remoto nella macchina virtuale di Azure).

>[AZURE.IMPORTANT]Per un elenco completo delle estensioni in base alle funzionalità che supportano, vedere [Estensioni VM e funzionalità di Azure](https://msdn.microsoft.com/library/dn606311.aspx). Poiché ogni estensione di macchina virtuale supporta una funzionalità specifica, tutte le operazioni esattamente possibili o meno mediante un'estensione dipendono dall'estensione stessa. Di conseguenza, prima di modificare la macchina virtuale, assicurarsi di aver letto la documentazione per l'estensione di macchina virtuale che si vuole usare. La rimozione di alcune estensioni di macchina virtuale non è supportata, mentre altre includono proprietà che possono essere impostate e che modificano radicalmente il comportamento della macchina virtuale.

Attività più comuni:

1.  Individuazione delle estensioni disponibili
    
2.  Aggiornamento delle estensioni caricate

3.  Aggiunta di estensioni

4.  Rimozione di estensioni

##Individuare le estensioni disponibili

Le estensioni di macchina virtuale di Azure sono (per un elenco completo delle estensioni in base alle funzionalità supportate, vedere [Estensioni VM e funzionalità di Azure](https://msdn.microsoft.com/library/dn606311.aspx)). È possibile individuare l'estensione, insieme a informazioni estese, usando:

-   PowerShell
-   Interfaccia multipiattaforma di Azure (xplat-cli)
-   API REST di gestione dei servizi

Per informazioni sulle estensioni disponibili, è possibile usare i cmdlet di [Azure PowerShell](https://msdn.microsoft.com/library/azure/dn495240.aspx) o le [API REST di gestione dei servizi](https://msdn.microsoft.com/library/ee460799.aspx).

###Azure PowerShell

Per alcune estensioni sono disponibili cmdlet di PowerShell specifici, che ne rendono più semplice la configurazione da PowerShell. Tuttavia, i seguenti cmdlet funzionano per tutte le estensioni di macchina virtuale.

Per ottenere informazioni sulle estensioni disponibili, è possibile usare i cmdlet seguenti:

-   Per le istanze dei ruoli Web o dei ruoli di lavoro, è possibile usare il cmdlet [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx).
-   Per le istanze delle macchine virtuali, è possibile usare il cmdlet [Get-AzureVMAvailableExtension](https://msdn.microsoft.com/library/azure/dn722480.aspx).

     L'esempio di codice seguente illustra come elencare le informazioni relative all'estensione **IaaSDiagnostics** usando PowerShell.
    
        PS C:\PowerShell> Get-AzureVMAvailableExtension -ExtensionName IaaSDiagnostics
        VERBOSE: 5:09:01 PM - Begin Operation: Get-AzureVMAvailableExtension
        VERBOSE: 5:09:06 PM - Completed Operation: Get-AzureVMAvailableExtension

        Publisher                   : Microsoft.Azure.Diagnostics
        ExtensionName               : IaaSDiagnostics
        Version                     : 1.2
        Label                       : Microsoft Monitoring Agent Diagnostics
        Description                 : Microsoft Monitoring Agent Extension
        PublicConfigurationSchema   :
        PrivateConfigurationSchema  :
        IsInternalExtension         : False
        SampleConfig                :
        ReplicationCompleted        : True
        Eula                        :
        PrivacyUri                  :
        HomepageUri                 :
        IsJsonExtension             : True
        DisallowMajorVersionUpgrade : False
        SupportedOS                 :
        PublishedDate               :
        CompanyName                 :


###Interfaccia della riga di comando di Azure (xplat-cli)

Alcune estensioni includono comandi xplat-cli specifiche (l'estensione di macchina virtuale Docker è un esempio) che possono semplificarne la configurazione. I comandi seguenti, invece, funzionano per tutte le estensioni di macchina virtuale.

È possibile usare il comando **azure vm extension list** per ottenere informazioni sulle estensioni disponibili e l'opzione **–-json** per visualizzare tutte le informazioni disponibili su una o più estensioni. Se non si usa un nome di estensione, il comando restituisce una descrizione json di tutte le estensioni disponibili.

L'esempio di codice seguente mostra come elencare le informazioni per l'estensione **IaaSDiagnostics** usando il comando xplat-cli **azure vm extension list** e usa l'opzione **–-json** per restituire informazioni complete.


    $ azure vm extension list -n IaaSDiagnostics --json
    [
      {
        "publisher": "Microsoft.Azure.Diagnostics",
        "name": "IaaSDiagnostics",
        "version": "1.2",
        "label": "Microsoft Monitoring Agent Diagnostics",
        "description": "Microsoft Monitoring Agent Extension",
        "replicationCompleted": true,
        "isJsonExtension": true
      }
    ]



###API REST di gestione dei servizi

Per ottenere informazioni sulle estensioni disponibili, è possibile usare le seguenti API REST:

-   Per le istanze dei ruoli Web o dei ruoli di lavoro, è possibile usare l'operazione [List Available Extensions](https://msdn.microsoft.com/library/dn169559.aspx). Per elencare le versioni delle estensioni disponibili, è possibile usare l'operazione [List Extension Versions](https://msdn.microsoft.com/library/dn495437.aspx).

-   Per le istanze delle macchine virtuali, è possibile usare l'operazione [List Resource Extensions](https://msdn.microsoft.com/library/dn495441.aspx). Per elencare le versioni delle estensioni disponibili, è possibile usare l'operazione [List Resource Extension Versions](https://msdn.microsoft.com/library/dn495440.aspx).

##Aggiungere, aggiornare o disabilitare le estensioni

È possibile aggiungere le estensioni al momento della creazione di un'istanza o a un'istanza già in esecuzione. Le estensioni possono essere aggiornate, disabilitate o rimosse. È possibile eseguire queste azioni usando i cmdlet di Azure PowerShell o le operazioni delle API REST di gestione dei servizi. Per installare e configurare alcune estensioni, è necessario specificare i parametri. Per le estensioni sono supportati parametri pubblici e privati.


###Azure PowerShell

Il modo più semplice per aggiungere e aggiornare le estensioni consiste nell'usare i cmdlet di Azure PowerShell. Quando si usano i cmdlet, la maggior parte della configurazione dell'estensione viene eseguita automaticamente. In alcuni casi può essere necessario aggiungere un'estensione a livello di codice. Quando si esegue questa operazione, è necessario specificare la configurazione dell'estensione.

Per sapere se un'estensione richiede una configurazione di parametri pubblici e privati, è possibile usare i seguenti cmdlet:

-   Per le istanze dei ruoli Web o dei ruoli di lavoro, è possibile usare il cmdlet **Get-AzureServiceAvailableExtension**.

-   Per le istanze delle macchine virtuali, è possibile usare il cmdlet **Get-AzureVMAvailableExtension**.

###API REST di gestione dei servizi

Quando si recupera un elenco di estensioni disponibili tramite le API REST, si ottengono informazioni su come configurare l'estensione. Nelle informazioni restituite possono essere presenti informazioni relative ai parametri rappresentate da uno schema pubblico e da uno schema privato. I valori dei parametri pubblici vengono restituiti in query sulle istanze. I valori dei parametri privati non vengono restituiti.

Per sapere se un'estensione richiede una configurazione di parametri pubblici e privati, è possibile usare le seguenti API REST:

-   Per le istanze dei ruoli Web o dei ruoli di lavoro, gli elementi **PublicConfigurationSchema** e **PrivateConfigurationSchema** contengono le informazioni nella risposta dall'operazione [List Available Extensions](https://msdn.microsoft.com/library/dn169559.aspx).

-   Per le istanze delle macchine virtuali, gli elementi **PublicConfigurationSchema** e **PrivateConfigurationSchema** contengono le informazioni nella risposta dall'operazione [List Resource Extensions](https://msdn.microsoft.com/library/dn495441.aspx).

>[AZURE.NOTE]Le estensioni possono anche usare configurazioni definite con JSON. Quando si applicano questi tipi di estensioni, viene usato solo l'elemento **SampleConfig**.

<!---HONumber=August15_HO7-->