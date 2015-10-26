<properties
   pageTitle="Comprendere le differenze tra Gestione risorse e i modelli di distribuzione classici"
   description="Vengono descritte le differenze tra il modello di distribuzione di Gestione risorse e il modello di distribuzione classico (o gestione dei servizi)."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2015"
   ms.author="tomfitz"/>

# Comprendere la distribuzione di Gestione delle risorse e distribuzione classica

Il modello di distribuzione di Gestione delle risorse fornisce un nuovo modo per distribuire e gestire i servizi che costituiscono l'applicazione. Questo nuovo modello contiene importanti differenze rispetto al modello di distribuzione classica e i due modelli non sono completamente compatibili tra loro. Per semplificare la distribuzione e la gestione delle risorse, Microsoft consiglia di utilizzare Gestione risorse per le nuove risorse e, se possibile, distribuire di nuovo le risorse esistenti tramite Gestione risorse.

È inoltre possibile conoscere il modello di distribuzione classico come modello di gestione dei servizi.

In questo argomento vengono descritte le differenze tra i due modelli, e alcuni dei problemi riscontrabili durante la transizione dal modello classico al modello Gestione risorse. Viene fornita una panoramica dei modelli, ma non vengono illustrate in dettaglio le differenze dei singoli servizi. Per ulteriori informazioni sul passaggio di calcolo, l’archiviazione e le risorse di rete, vedere [Provider di calcolo, rete e di archiviazione in Gestione risorse di Microsoft Azure](./virtual-machines/virtual-machines-azurerm-versus-azuresm.md).

Molte risorse funzionano senza problemi sia in Gestione risorse sia nel modello classico. Queste risorse supportano completamente Gestione risorse, anche se create nel modello classico. È possibile passare a Gestione risorse senza problemi o sforzi aggiuntivi.

Tuttavia, alcuni provider di risorse offrono due versioni della risorsa (uno per il modello classico e uno per la gestione risorse) a causa delle differenze architetturali tra i modelli. I provider di risorse che differenziano i due modelli sono:

- Calcolo
- Archiviazione
- Rete

Per questi tipi di risorsa, è necessario essere consapevoli della versione in uso poiché le operazioni supportate variano.

Per comprendere le differenze di architettura tra i due modelli, vedere [Architettura di Gestione risorse di Azure](virtual-machines/virtual-machines-azure-resource-manager-architecture.md).

## Caratteristiche di Gestione risorse

Le risorse create tramite Gestione risorse condividono le caratteristiche seguenti:

- Creato tramite uno dei metodi seguenti:

  - Il [portale di anteprima](https://portal.azure.com/).

        ![preview portal](./media/resource-manager-deployment-model/preview-portal.png)

        For Compute, Storage, and Networking resources, you have the option of using either Resourece Manager or Classic deployment. Select **Resource Manager**.

        ![Resource Manager deployment](./media/resource-manager-deployment-model/select-resource-manager.png)

  - Per le versioni di Azure PowerShell precedenti alla versione di anteprima 1.0, i comandi vengono eseguiti nella modalità **AzureResourceManager**.

            PS C:\> Switch-AzureMode -Name AzureResourceManager

  - Per l'anteprima di Azure PowerShell 1.0, usare la versione di Gestione risorse dei comandi. Questi comandi hanno il formato *verb-AzureRm*, come illustrato di seguito.

            PS C:\> Get-AzureRmResourceGroupDeployment

  - [API REST di Gestione risorse di Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx) per le operazioni REST.
  - Comandi dell'interfaccia della riga di comando di Azure eseguiti nella modalità **arm**.

            azure config mode arm

- Il tipo di risorsa non include **(classico)** nel nome. L'immagine seguente mostra il tipo come **Account di archiviazione**.

    ![App Web](./media/resource-manager-deployment-model/resource-manager-type.png)

## Caratteristiche della distribuzione classica

Le risorse create nel modello di distribuzione classica condividono le caratteristiche seguenti:

- Creato tramite uno dei metodi seguenti:

  - [Portale di Azure](https://manage.windowsazure.com)

        ![Azure portal](./media/resource-manager-deployment-model/azure-portal.png)

        Or, the preview portal and you specify **Classic** deployment (for Compute, Storage, and Networking).

        ![Classic deployment](./media/resource-manager-deployment-model/select-classic.png)

  - Per le versioni di Azure PowerShell precedenti alla versione di anteprima 1.0, eseguire i comandi di PowerShell nella modalità **AzureServiceManagement** (che è la modalità predefinita, pertanto, se non si passa in modo specifico a AzureResourceManager, viene eseguito in modalità AzureServiceManagement).

            PS C:\> Switch-AzureMode -Name AzureServiceManagement

  - Per l'anteprima di Azure PowerShell 1.0, usare la versione di Gestione servizi dei comandi. Questi nomi di comandi **non** hanno il formato *verb-AzureRm*, come illustrato di seguito.

            PS C:\> Get-AzureDeployment

  - [API REST di Gestione servizi](https://msdn.microsoft.com/library/azure/ee460799.aspx) per le operazioni REST.
  - Comandi dell'interfaccia della riga di comando di Azure eseguiti in modalità **asm** o predefinita.
- Il tipo di risorsa include **(classico)** nel nome. L'immagine seguente mostra il tipo come **Account di archiviazione (classico)**.

    ![tipo classico](./media/resource-manager-deployment-model/classic-type.png)

È comunque possibile utilizzare il portale di anteprima per gestire le risorse che sono state create tramite distribuzione classica.

## Vantaggi dell'utilizzo di Gestione risorse e di gruppi di risorse

Gestione risorse ha aggiunto il concetto di gruppo di risorse. Tutte le risorse create tramite Gestione risorse esiste all'interno di un gruppo di risorse. Il modello di distribuzione di Gestione risorse offre diversi vantaggi:

- È possibile distribuire, gestire e monitorare tutti i servizi per la soluzione come un gruppo, anziché gestire singolarmente questi servizi.
- è possibile distribuire l'applicazione in tutto il ciclo di vita dell'app ripetutamente e avere la certezza che le risorse vengano distribuite in uno stato coerente.
- È possibile utilizzare modelli dichiarativi per definire la distribuzione. 
- È possibile definire le dipendenze tra risorse e pertanto esse vengono distribuite nell'ordine corretto.
- è possibile applicare il controllo di accesso a tutti i servizi nel gruppo di risorse perché il controllo di accesso basato sui ruoli (RBAC) è integrato in modo nativo nella piattaforma di gestione.
- È possibile applicare i tag alle risorse per organizzare logicamente tutte le risorse nella sottoscrizione.


Ogni risorsa creata da una distribuzione classica prima della Gestione risorse, non esiste all'interno di un gruppo di risorse. Quando è stato aggiunto Gestione risorse, tutte le risorse retroattive sono state aggiunti ai gruppi di risorse predefiniti. Se ora si crea una risorsa tramite la distribuzione classica, la risorsa viene creata automaticamente all'interno di un gruppo di risorse predefinito per tale servizio anche se non è stato specificato il gruppo di risorse in fase di distribuzione. Tuttavia, il solo fatto di esister all'interno di un gruppo di risorse non significa che la risorsa è stata convertita nel modello di Gestione risorse. Per macchine virtuali, archiviazione e reti virtuali, se la risorsa è stata creata da una distribuzione classica, è necessario continuare a operare su di essa tramite operazioni classiche.

È possibile spostare le risorse a un gruppo di risorse diverso e aggiungere nuove risorse al gruppo di risorse. Pertanto, il gruppo di risorse può contenere una combinazione di risorse creato tramite Gestione risorse e distribuzione classica. Questa combinazione di risorse può produrre risultati imprevisti perché le risorse non supportano le stesse operazioni.

Utilizzando i modelli dichiarativi, potrebbe essere in grado di semplificare gli script per la distribuzione. Anziché tentare di convertire gli script esistenti dalla gestione dei servizi di gestione risorse, considerare la rielaborazione della strategia di distribuzione per sfruttare i vantaggi di definire l'infrastruttura e la configurazione del modello.

## Utilizzo di tag

I Tag consentono di organizzare logicamente le risorse. Solo le risorse create tramite il tag di supporto di Gestione risorse. Non è possibile applicare i tag alle risorse classiche.

Per altre informazioni sull'uso dei tag in Gestione risorse, vedere [Uso dei tag per organizzare le risorse di Azure](resource-group-using-tags.md).

## Operazioni supportate per i modelli di distribuzione

Le risorse create con il modello di distribuzione classica non supportano le operazioni di Gestione risorse. In alcuni casi, un comando di Gestione risorse può recuperare informazioni su una risorsa creata da una distribuzione classica o può eseguire attività amministrative, quali lo spostamento di una risorsa classica a un altro gruppo di risorse, ma questi casi non dovrebbero dare l'impressione che il tipo supporti operazioni di Gestione risorse. Si supponga, ad esempio, che si disponga di un gruppo di risorse contenente le macchine virtuali che sono stati creati con Gestione risorse e con il modello classico. Se si esegue il comando PowerShell seguente, si noteranno tutte le macchine virtuali:

    PS C:\> Get-AzureRmResourceGroup -Name ExampleGroup
    ...
    Resources :
     Name                 Type                                          Location
     ================     ============================================  ========
     ExampleClassicVM     Microsoft.ClassicCompute/domainNames          eastus
     ExampleClassicVM     Microsoft.ClassicCompute/virtualMachines      eastus
     ExampleResourceVM    Microsoft.Compute/virtualMachines             eastus
    ...

Tuttavia, se si esegue il comando Get-AzureVM, si otterranno solo macchine virtuali che sono state create con Gestione risorse.

    PS C:\> Get-AzureVM -ResourceGroupName ExampleGroup
    ...
    Id       : /subscriptions/xxxx/resourceGroups/ExampleGroup/providers/Microsoft.Compute/virtualMachines/ExampleResourceVM
    Name     : ExampleResourceVM
    ...

In generale, non è possibile aspettarsi che le risorse create da una distribuzione classica funzionino con i comandi di Gestione risorse.

Quando si utilizzano le risorse create tramite Gestione risorse, è necessario utilizzare le operazioni di Gestione risorse e non tornare a operazioni di gestione del servizio.

## Considerazioni sulle macchine virtuali

Esistono alcune importanti considerazioni da tenere presente quando si utilizzano le macchine virtuali.

- Le macchine virtuali distribuite con il modello di distribuzione classica non possono essere incluse in una rete virtuale distribuita con Gestione risorse.
- Le macchine virtuali distribuite con il modello di distribuzione di Gestione risorse deve essere incluso in una rete virtuale.
- Le macchine virtuali distribuite con il modello di distribuzione di Gestione risorse deve essere incluso in una rete virtuale.

Per un elenco di comandi equivalenti dell'interfaccia della riga di comando di Azure durante la transizione dalla distribuzione classica per Gestione risorse, vedere [Comandi della Gestione risorse equivalente e della gestione dei servizi per le operazioni della macchina virtuale](./virtual-machines/xplat-cli-azure-manage-vm-asm-arm.md).

Per ulteriori informazioni sul passaggio di calcolo, l'archiviazione e le risorse di rete, vedere [Provider di calcolo, rete e di archiviazione in Gestione risorse di Microsoft Azure](./virtual-machines/virtual-machines-azurerm-versus-azuresm.md).

Per altre informazioni sulla connessione di reti virtuali da diversi modelli di distribuzione, vedere [Connessione di reti virtuali classiche a nuove reti virtuali](./virtual-network/virtual-networks-arm-asm-s2s.md).

## Passaggi successivi

- Per altre informazioni sulla creazione dei modelli di distribuzione dichiarativa, vedere [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md).
- Per vedere i comandi per la distribuzione di un modello, vedere [Distribuire un'applicazione con il modello di Gestione risorse di Azure](resource-group-template-deploy.md).

<!---HONumber=Oct15_HO3-->