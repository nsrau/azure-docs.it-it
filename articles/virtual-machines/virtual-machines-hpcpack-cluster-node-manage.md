<properties
 pageTitle="Gestire i nodi di calcolo del cluster HPC Pack | Microsoft Azure"
 description="Informazioni sugli strumenti di script di PowerShell per aggiungere, rimuovere, avviare e arrestare i nodi di calcolo del cluster HPC Pack in Azure"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="09/28/2015"
 ms.author="danlep"/>

# Gestire il numero e la disponibilità dei nodi di calcolo in un cluster HPC Pack in Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.


Se è stato creato un cluster HPC Pack nelle macchine virtuali di Azure, potrebbe essere utile conoscere i metodi per aggiungere, rimuovere, avviare (provisioning) o arrestare (deprovisioning) facilmente un certo numero di macchine virtuali dei nodi di calcolo nel cluster. Per eseguire queste attività, eseguire gli script di Azure PowerShell installati nella macchina virtuale del nodo head (a partire da HPC Pack 2012 R2 Update 1). Questi script consentono di controllare il numero e la disponibilità delle risorse del cluster HPC Pack in modo da poter controllare i costi.

>[AZURE.NOTE]Gli script sono disponibili nella cartella %CCP\_HOME%bin nel nodo head. È necessario eseguire gli script come amministratore.

## Prerequisiti

* **Cluster HPC Pack nelle macchine virtuali di Azure** - Creare un cluster HPC Pack nel modello di distribuzione (gestione dei servizi) classica usando almeno HPC Pack 2012 R2 Update 1. Ad esempio, è possibile automatizzare la distribuzione usando l'immagine di macchina virtuale di HPC Pack in Azure Marketplace e uno script di Azure PowerShell. Per informazioni e indicazioni sui prerequisiti, vedere [Creare un cluster HPC con lo script di distribuzione IaaS di HPC Pack](virtual-machines-hpcpack-cluster-powershell-script.md).

* **Certificato di gestione di Azure o file delle impostazioni di pubblicazione** - È necessario effettuare una delle operazioni seguenti sul nodo head:

    * **Importare il file delle impostazioni di pubblicazione di Azure**. A tale scopo, eseguire i cmdlet di Azure PowerShell seguenti nel nodo head: ```
        Get-AzurePublishSettingsFile  
Import-AzurePublishSettingsFile –PublishSettingsFile <publish settings file>
        ```
    * **Configurare il certificato di gestione di Azure nel nodo head**. Se si dispone del file con estensione cer, importarlo nell'archivio certificati CurrentUser\\My certificate e quindi eseguire il seguente cmdlet di Azure PowerShell per l'ambiente Azure (AzureCloud o AzureChinaCloud):

    ```
    Set-AzureSubscription -SubscriptionName <Sub Name> -SubscriptionId <Sub ID> -Certificate (Get-Item Cert:\CurrentUser\My<Cert Thrumbprint>) -Environment <AzureCloud | AzureChinaCloud>
    ```




## Aggiungere le macchine virtuali dei nodi di calcolo

Aggiungere nodi di calcolo con lo script **Add-HpcIaaSNode.ps1**.

### Sintassi
```
Add-HPCIaaSNode.ps1 [-ServiceName] <String> [-ImageName] <String>
 [-Quantity] <Int32> [-InstanceSize] <String> [-DomainUserName] <String> [[-DomainUserPassword] <String>]
 [[-NodeNameSeries] <String>] [<CommonParameters>]

```
### Parametri

* **ServiceName** - Nome del servizio cloud a cui verranno aggiunte le nuove macchine virtuali nei nodi di calcolo.

* **ImageName** - Nome dell'immagine di macchina virtuale di Azure, che può essere ottenuto tramite il portale di Azure classico o il cmdlet di Azure PowerShell **Get-AzureVMImage**. L'immagine deve soddisfare i seguenti requisiti:

    1. Deve essere installato un sistema operativo Windows.

    2. HPC Pack deve essere installato nel ruolo del nodo di calcolo.

    3. L'immagine deve essere un'immagine privata nella categoria utente, non un'immagine di macchina virtuale di Azure pubblica.

* **Quantity** - Numero di macchine virtuali dei nodi di calcolo da aggiungere.

* **InstanceSize** - Dimensioni delle macchine virtuali dei nodi di calcolo.

* **DomainUserName** - Nome utente di dominio, che verrà usato per aggiungere le nuove macchine virtuali al dominio.

* **DomainUserPassword*** Password dell'utente di dominio

* **NodeNameSeries** (facoltativo) 0 Modello di denominazione per i nodi di calcolo. Il formato deve essere &lt;*Nome\_radice*&gt;&lt;*Numero\_iniziale*&gt;%. Ad esempio, MyCN%10% significa una serie di nomi dei nodi di calcolo a partire da MyCN11. Se non specificato, lo script usa la serie di nomi dei nodi di calcolo configurata nel cluster HPC.

### Esempio

L'esempio seguente aggiunge 20 macchine virtuali dei nodi di calcolo di grandi dimensioni nel servizio cloud hpcservice1, in base all'immagine di macchina virtuale hpccnimage1.

```
Add-HPCIaaSNode.ps1 –ServiceName hpcservice1 –ImageName hpccniamge1
–Quantity 20 –InstanceSize Large –DomainUserName <username>
-DomainUserPassword <password>
```


## Rimuovere le macchine virtuali dei nodi di calcolo

Rimuovere i nodi di calcolo con lo script **Remove-HpcIaaSNode.ps1**.

### Sintassi

```
Remove-HPCIaaSNode.ps1 -Name <String[]> [-DeleteVHD] [-Force] [-WhatIf] [-Confirm] [<CommonParameters>]

Remove-HPCIaaSNode.ps1 -Node <Object> [-DeleteVHD] [-Force] [-Confirm] [<CommonParameters>]
```

### Parametri

 * **Name** - Nomi dei nodi del cluster da rimuovere. Sono supportati caratteri jolly. Il nome del set di parametri è Name. Non è possibile specificare entrambi i parametri **Name** e **Node**.

* **Node** * Oggetto HpcNode per i nodi da rimuovere, che può essere ottenuto tramite il cmdlet di PowerShell per HPC [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Il nome del set di parametri è Node. Non è possibile specificare entrambi i parametri **Name** e **Node**.

* **DeleteVHD** (facoltativo) - Impostazione per eliminare i dischi associati per le macchine virtuali rimosse.

* **Force** (facoltativo) - Impostazione per portare forzatamente offline i nodi HPC prima della rimozione.

* **Confirm** (facoltativo) - Richiesta di conferma prima dell'esecuzione del comando.

* **WhatIf** - Impostazione per descrivere le conseguenze dell'esecuzione del comando senza eseguirlo effettivamente.

### Esempio

Nell'esempio seguente vengono portati forzatamente offline i nodi con nomi che iniziano con *HPCNode-CN-* e poi vengono rimossi i nodi e i dischi associati corrispondenti.

```
Remove-HPCIaaSNode.ps1 –Name HPCNodeCN-* –DeleteVHD -Force
```

## Avviare le macchine virtuali dei nodi di calcolo

Avviare i nodi di calcolo con lo script **Start-HpcIaaSNode.ps1**.

### Sintassi

```
Start-HPCIaaSNode.ps1 -Name <String[]> [<CommonParameters>]

Start-HPCIaaSNode.ps1 -Node <Object> [<CommonParameters>]
```
### Parametri

* **Name** - Nomi dei nodi del cluster da avviare. Sono supportati caratteri jolly. Il nome del set di parametri è Name. Non è possibile specificare entrambi i parametri **Name** e **Node**.

* **Node** * Oggetto HpcNode per i nodi da avviare, che può essere ottenuto tramite il cmdlet di PowerShell per HPC [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Il nome del set di parametri è Node. Non è possibile specificare entrambi i parametri **Name** e **Node**.

### Esempio

Nell'esempio seguente vengono avviati i nodi i cui nomi iniziano con *HPCNode-CN-*.

```
Start-HPCIaaSNode.ps1 –Name HPCNodeCN-*
```

## Arrestare le macchine virtuali dei nodi di calcolo

Arrestare i nodi di calcolo con lo script **Stop-HpcIaaSNode.ps1**.

### Sintassi

```
Stop-HPCIaaSNode.ps1 -Name <String[]> [-Force] [<CommonParameters>]

Stop-HPCIaaSNode.ps1 -Node <Object> [-Force] [<CommonParameters>]
```

### Parametri


* **Name** - Nomi dei nodi del cluster da arrestare. Sono supportati caratteri jolly. Il nome del set di parametri è Name. Non è possibile specificare entrambi i parametri **Name** e **Node**.

* **Node** - Oggetto HpcNode per i nodi da arrestare, che può essere ottenuto tramite il cmdlet di PowerShell per HPC Get-HpcNode. Il nome del set di parametri è Node. Non è possibile specificare entrambi i parametri **Name** e **Node**.

* **Force** (facoltativo) - Impostazione per portare forzatamente offline i nodi HPC prima dell'arresto.

### Esempio

Nell'esempio seguente vengono portati forzatamente offline i nodi con nomi che iniziano con *HPCNode-CN-* e poi i nodi vengono arrestati.

Stop-HPCIaaSNode.ps1 –Name HPCNodeCN-* -Force

## Passaggi successivi

* Se si desidera avere a disposizione un modo per aumentare o ridurre automaticamente le risorse di elaborazione di Azure in base al carico di lavoro corrente dei processi e delle attività del cluster, vedere [Aumentare e ridurre le risorse di calcolo di Azure in un cluster HPC Pack](virtual-machines-hpcpack-cluster-node-autogrowshrink.md).

<!---HONumber=AcomDC_1203_2015-->