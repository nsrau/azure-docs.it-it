<properties
   pageTitle="Come assegnare i tag a una VM | Microsoft Azure"
   description="Ulteriori informazioni sull’assegnazione di tag a una macchina virtuale di Azure creata con il modello di distribuzione di Gestione risorse."
   services="virtual-machines"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure-services"
   ms.date="11/10/2015"
   ms.author="dkshir;memccror"/>

# Come contrassegnare una macchina virtuale in Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.


In questo articolo vengono descritti diversi modi per contrassegnare una macchina virtuale in Azure tramite il gestore di risorse di Azure. I tag sono coppie chiave/valore definite dall'utente che possono essere inserite direttamente in una risorsa o un gruppo di risorse. Attualmente, Azure supporta fino a 15 tag per ogni risorsa e gruppo di risorse. I tag possono essere posizionati su una risorsa al momento della creazione o aggiunti a una risorsa esistente. Si noti che i tag sono supportati solo per le risorse create tramite la gestione risorse di Azure.

## Assegnazione di tag a una macchina virtuale tramite modelli

In primo luogo, diamo un'occhiata ai tag tramite modelli. [Questo modello](https://github.com/Azure/azure-quickstart-templates/tree/master/101-tags-vm)inserisce i tag per le risorse seguenti: calcolo (macchina virtuale), archiviazione (Account di archiviazione) e rete (indirizzo IP pubblico, rete virtuale e interfaccia di rete).

Fare clic sul pulsante**Distribuisci in Azure**dal [collegamento modello](https://github.com/Azure/azure-quickstart-templates/tree/master/101-tags-vm). Verrà visualizzato il [portale di anteprima di Azure](http://portal.azure.com/) in cui è possibile distribuire il modello.

![Distribuzione semplice di tag](./media/virtual-machines-tagging-arm/deploy-to-azure-tags.png)

Questo modello include i seguenti tag:*Reparto**Applicazione*e*Creato da*. È possibile aggiungere o modificare questi tag direttamente nel modello se si desiderano diversi nomi di tag.

![Tag di Azure in un modello](./media/virtual-machines-tagging-arm/azure-tags-in-a-template.png)

Come si può vedere, i tag vengono definiti come coppie chiave/valore, separate da due punti (:). I tag devono essere definiti in questo formato:

        “tags”: {
            “Key1” : ”Value1”,
            “Key2” : “Value2”
        }

Salvare il file di modello al termine della modifica, con i tag di propria scelta.

Successivamente, nella sezione**Modifica parametri**, è possibile compilare i valori per i tag.

![Modificare tag nel portale di anteprima di Azure](./media/virtual-machines-tagging-arm/edit-tags-in-azure-portal.png)

Fare clic su**Crea**per distribuire il modello con i valori dei tag.


## Assegnazione di tag tramite il portale

Dopo aver creato le risorse con i tag, è possibile visualizzare, aggiungere ed eliminare i tag nel portale.

Selezionare l'icona di tag per visualizzare i tag:

![Icona di tag nel portale di anteprima di Azure](./media/virtual-machines-tagging-arm/azure-portal-tags-icon.png)

Aggiungere un nuovo tag tramite il portale definendo la propria coppia chiave/valore e salvarlo.

![Aggiunti nuovo tag nel portale di anteprima di Azure](./media/virtual-machines-tagging-arm/azure-portal-add-new-tag.png)

Il nuovo tag verrà visualizzato nell'elenco dei tag per la risorsa.

![Nuovo tag salvato nel portale di anteprima di Azure](./media/virtual-machines-tagging-arm/azure-portal-saved-new-tag.png)


## Assegnazione di tag tramite PowerShell

Per creare, aggiungere ed eliminare i tag tramite PowerShell, è prima necessario configurare l’[ambiente PowerShell con Gestione risorse di Azure][]. Dopo aver completato l'installazione, è possibile inserire tag su risorse di calcolo, rete e archiviazione al momento della creazione o dopo la creazione della risorsa tramite PowerShell. Questo articolo si concentrerà su come visualizzare o modificare tag inseriti nelle macchine virtuali.

Per prima cosa, spostarsi su una macchina virtuale tramite il`Get-AzureVM`cmdlet.

        PS C:\> Get-AzureVM -ResourceGroupName "MyResourceGroup" -Name "MyWindowsVM"

Se la macchina virtuale contiene già dei tag, verranno visualizzati tutti i tag per la risorsa:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Se si desidera aggiungere i tag tramite PowerShell, è possibile utilizzare il comando `Set-AzureResource`. Nota: Quando si aggiornano i tag tramite PowerShell, i tag vengono aggiornati nel loro complesso. Se si aggiunge un tag a una risorsa che già dispone di tag, sarà pertanto necessario includere tutti i tag che si desidera inserire nella risorsa. Di seguito è riportato un esempio di come aggiungere ulteriori tag a una risorsa tramite Cmdlets di PowerShell.

Questo primo cmdlet imposta tutti i tag inseriti in *MyWindowsVM* per la variabile *tag*, utilizzando la funzione `Get-AzureResource` e `Tags`. Si noti che il parametro `ApiVersion` è facoltativo. Se non è specificato, viene usata la versione più recente dell'API del provider di risorse.

        PS C:\> $tags = (Get-AzureResource -Name MyWindowsVM -ResourceGroupName MyResourceGroup -ResourceType "Microsoft.Compute/virtualmachines" -ApiVersion 2015-05-01-preview).Tags

Il secondo comando consente di visualizzare i tag per la variabile specificata.

        PS C:\> $tags

        Name		Value
        ----                           -----
        Value		MyDepartment
        Name		Department
        Value		MyApp1
        Name		Application
        Value		MyName
        Name		Created By
        Value		Production
        Name		Environment

Il terzo comando aggiunge un altro tag alla variabile *tags*. Si noti l'uso di **+=** per aggiungere la nuova coppia chiave/valore all'elenco *tags*.

        PS C:\> $tags +=@{Name="Location";Value="MyLocation"}

Il quarto comando imposta tutti i tag definiti nella variabile *tags* sulla risorsa specificata. In questo caso, è MyWindowsVM.

        PS C:\> Set-AzureResource -Name MyWindowsVM -ResourceGroupName MyResourceGroup -ResourceType "Microsoft.Compute/VirtualMachines" -ApiVersion 2015-05-01-preview -Tag $tags

Il quinto comando visualizza tutti i tag sulla risorsa. Come si può vedere, *Location* è ora definito come un tag con *MyLocation* come valore.

        PS C:\> (Get-AzureResource -ResourceName "MyWindowsVM" -ResourceGroupName "MyResourceGroup" -ResourceType "Microsoft.Compute/VirtualMachines" -ApiVersion 2015-05-01-preview).Tags

        Name		Value
        ----                           -----
        Value		MyDepartment
        Name		Department
        Value		MyApp1
        Name		Application
        Value		MyName
        Name		Created By
        Value		Production
        Name		Environment
        Value		MyLocation
        Name		Location

Per altre informazioni sull'assegnazione di tag tramite PowerShell, consultare i [cmdlet di Azure per le risorse][].


## Assegnazione di tag con Azure CLI

L’assegnazione di tag è supportata per le risorse che sono già state create tramite la CLI di Azure. Per iniziare, impostare l'[ambiente CLI di Azure][]. Accedere alla sottoscrizione tramite la CLI di Azure e passare alla modalità ARM. È possibile visualizzare tutte le proprietà per una determinata macchina virtuale, compresi i tag, utilizzando questo comando:

        azure vm show -g MyResourceGroup -n MyVM

A differenza di PowerShell, se si sta aggiungendo tag a una risorsa che già contiene tag, non occorre specificare tutti i tag (vecchi e nuovi) prima di usare il comando `azure vm set`. Questo comando consente invece di aggiungere un tag per la risorsa. Per aggiungere un nuovo tag della macchia virtuale tramite la CLI di Azure, è possibile usare il comando `azure vm set` insieme al parametro tag **-t**:

        azure vm set -g MyResourceGroup -n MyVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

Per rimuovere tutti i tag, è possibile usare il parametro **–T** nel comando `azure vm set`.

        azure vm set – g MyResourceGroup –n MyVM -T


Ora che sono stati applicati tag alle risorse tramite PowerShell, la CLI di Azure e il portale, esaminiamo i dettagli di utilizzo per visualizzare i tag nel portale di fatturazione.


## Visualizzazione dei tag nei dettagli dell'utilizzo

I tag applicati a risorse di calcolo, rete e archiviazione tramite Gestione risorse di Azure verranno compilati nei dettagli di utilizzo nel [portale di fatturazione](https://account.windowsazure.com/).

Fare clic su **Scarica dettagli dell'utilizzo** per visualizzare i dettagli di utilizzo nella sottoscrizione.

![Dettagli di utilizzo nel portale di anteprima di Azure](./media/virtual-machines-tagging-arm/azure-portal-tags-usage-details.png)

Selezionare l'estratto conto e i dettagli di utilizzo **Versione 2**:

![Dettagli di utilizzo dell'anteprima di Versione 2 nel portale di anteprima di Azure](./media/virtual-machines-tagging-arm/azure-portal-version2-usage-details.png)

Dai dettagli di utilizzo è possibile visualizzare tutti i tag nella colonna **Tag**:

![Colonna di tag nel portale di anteprima di Azure](./media/virtual-machines-tagging-arm/azure-portal-tags-column.png)

Analizzando i tag e il loro utilizzo, le organizzazioni saranno in grado di acquisire nuove informazioni nei dati relativi al consumo.


## Risorse aggiuntive

* [Panoramica di Gestione risorse di Microsoft Azure][]
* [Utilizzo di tag per organizzare le risorse di Azure][]
* [Informazioni sulla fatturazione di Azure][]
* [Ottenere informazioni dettagliate sul consumo di risorse di Microsoft Azure][]




[ambiente PowerShell con Gestione risorse di Azure]: ../powershell-azure-resource-manager.md
[cmdlet di Azure per le risorse]: https://msdn.microsoft.com/it-IT/library/azure/dn757692.aspx
[ambiente CLI di Azure]: ./xplat-cli-azure-resource-manager.md
[Panoramica di Gestione risorse di Microsoft Azure]: ../resource-group-overview.md
[Utilizzo di tag per organizzare le risorse di Azure]: ../resource-group-using-tags.md
[Informazioni sulla fatturazione di Azure]: ../billing-understand-your-bill.md
[Ottenere informazioni dettagliate sul consumo di risorse di Microsoft Azure]: ../billing-usage-rate-card-overview.md

<!---HONumber=AcomDC_1125_2015-->