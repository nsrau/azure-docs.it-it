---
title: Aggiungere manuali operativi di automazione di Azure ai piani di ripristino Site Recovery
description: Informazioni su come estendere i piani di ripristino con automazione di Azure per il ripristino di emergenza con Azure Site Recovery.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: rajanaki
ms.openlocfilehash: ecfe993a137ca63c84438870ec54ac1e6d6707da
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72173492"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Aggiungere runbook di Automazione di Azure ai piani di ripristino

Questo articolo descrive come integrare manuali operativi di automazione di Azure per estendere i piani di ripristino del [Azure Site Recovery](site-recovery-overview.md) . Viene illustrato come automatizzare le attività di base che altrimenti richiedono interventi manuali e come convertire un ripristino in più passaggi in un'azione con un solo clic.

## <a name="recovery-plans"></a>Piani di ripristino 

È possibile usare i piani di ripristino quando si esegue il failover dei computer locali o delle VM di Azure. I piani di ripristino consentono di definire un processo di ripristino sistematico che definisce la modalità di failover dei computer e il modo in cui vengono avviati e ripristinati dopo il failover. 

Il ripristino di app di grandi dimensioni può risultare complesso. I piani di ripristino consentono di imporre l'ordine in modo che il ripristino sia accurato, ripetibile e automatizzato. È possibile automatizzare le attività all'interno di un piano di ripristino usando gli script, nonché i manuali operativi di automazione di Azure. Esempi tipici potrebbero essere la configurazione di impostazioni in una macchina virtuale di Azure dopo il failover o la riconfigurazione di un'app in esecuzione nella macchina virtuale.

- [Ulteriori informazioni](recovery-plan-overview.md) sui piani di ripristino.
- [Altre](../automation/automation-runbook-types.md) informazioni sui manuali operativi di automazione di Azure.



## <a name="runbooks-in-recovery-plans"></a>Manuali operativi nei piani di ripristino

Si aggiunge un account di automazione di Azure e manuali operativi a un piano di ripristino. Runbook viene richiamato quando viene eseguito il piano di ripristino.

- L'account di automazione può trovarsi in qualsiasi area di Azure e deve trovarsi nella stessa sottoscrizione dell'insieme di credenziali Site Recovery. 
- Un Runbook può essere eseguito in un piano di ripristino durante il failover da una posizione primaria a una secondaria o durante il failback dalla posizione secondaria al database primario.
- Manuali operativi in un piano di ripristino vengono eseguite in modo seriale, una dopo l'altra, nell'ordine del set.
- Se manuali operativi in un piano di ripristino configura le macchine virtuali per l'avvio in gruppi diversi, il piano di ripristino continuerà solo quando Azure segnala tutte le macchine virtuali come in esecuzione.
- I piani di ripristino continuano l'esecuzione, anche in caso di errore di uno script.

### <a name="recovery-plan-context"></a>Contesto del piano di ripristino

Quando viene eseguito uno script, inserisce un contesto del piano di ripristino in Runbook. Il contesto contiene le variabili riepilogate nella tabella.

| **Nome variabile** | **Descrizione** |
| --- | --- |
| RecoveryPlanName |Nome del piano di ripristino. Utilizzato nelle azioni in base al nome. |
| FailoverType |Specifica se si tratta di un failover di test o di produzione. 
| FailoverDirection | Specifica se il ripristino è in una posizione primaria o secondaria. |
| GroupID |Identifica il numero del gruppo nel piano di ripristino quando il piano è in esecuzione. |
| VmMap |Matrice di tutte le macchine virtuali nel gruppo. |
| VMMap key |Chiave univoca (GUID) per ogni macchina virtuale. |
| SubscriptionId |ID della sottoscrizione di Azure in cui viene creata la macchina virtuale. |
| ResourceGroupName | Nome del gruppo di risorse in cui si trova la macchina virtuale.
| CloudServiceName |Nome del servizio cloud di Azure in cui è stata creata la macchina virtuale. |
| RoleName |Nome della macchina virtuale di Azure. |
| RecoveryPointId|Timestamp per il ripristino della macchina virtuale. |

L'esempio seguente mostra una variabile di contesto:

```
{"RecoveryPlanName":"hrweb-recovery",
"FailoverType":"Test",
"FailoverDirection":"PrimaryToSecondary",
"GroupId":"1",
"VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":
    { "SubscriptionId":"7a1111111-c1d6-49c5-8c5d-111ce8dd183",
    "ResourceGroupName":"ContosoRG",
    "CloudServiceName":"pod02hrweb-Chicago-test",
    "RoleName":"Fabrikam-Hrweb-frontend-test",
    "RecoveryPointId":"TimeStamp"}
    }
}
```

Se si vuole accedere a tutte le macchine virtuali in VMMap in un ciclo, è possibile usare il codice seguente:

```
$VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
$vmMap = $RecoveryPlanContext.VmMap
    foreach($VMID in $VMinfo)
    {
        $VM = $vmMap.$VMID                
            if( !(($VM -eq $Null) -Or ($VM.ResourceGroupName -eq $Null) -Or ($VM.RoleName -eq $Null))) {
            #this check is to ensure that we skip when some data is not available else it will fail
    Write-output "Resource group name ", $VM.ResourceGroupName
    Write-output "Rolename " = $VM.RoleName
            }
        }
```


Il Blog di Aman Sharma sulla [raccolta di cloud](http://harvestingclouds.com) è un esempio utile di uno [script del contesto del piano di ripristino](http://harvestingclouds.com/post/script-sample-azure-automation-runbook-for-asr-recovery-plan/).



## <a name="before-you-start"></a>Prima di iniziare

- Se non si ha familiarità con automazione di Azure, è possibile [iscriversi](https://azure.microsoft.com/services/automation/) e [scaricare gli script di esempio](https://azure.microsoft.com/documentation/scripts/).
- Assicurarsi che l'account di Automazione abbia i moduli seguenti:
    - AzureRM.profile
    - AzureRM.Resources
    - AzureRM.Automation
    - AzureRM.Network
    - AzureRM.Compute

    Tutti i moduli devono essere di versioni compatibili. Il modo più semplice consiste nell'usare sempre le versioni più recenti di tutti i moduli.



## <a name="customize-the-recovery-plan"></a>Personalizzare il piano di ripristino

1. Nell'insieme di credenziali selezionare **piani di ripristino (Site Recovery)**
2. Per creare un piano di ripristino, fare clic su **+ piano di ripristino**. [Altre informazioni](site-recovery-create-recovery-plans.md). Se si dispone già di un piano di ripristino, selezionare per aprirlo.
3. Nella pagina piano di ripristino fare clic su **Personalizza**.

    ![Fare clic sul pulsante Personalizza](media/site-recovery-runbook-automation-new/custom-rp.png)

2. Fare clic sui puntini di sospensione (...) accanto al **gruppo 1: avviare** > **Aggiungi post-azione**.
3. In **Inserisci azione**verificare che sia selezionato **script** e specificare un nome per lo script (**Hello World**).
4. Specificare un account di automazione e selezionare un Runbook. Fare clic su **OK** per salvare lo script. Lo script viene aggiunto a **Gruppo 1: passaggi successivi**.


## <a name="reuse-a-runbook-script"></a>Riutilizza uno script Runbook

È possibile usare un singolo script Runbook in più piani di ripristino, usando variabili esterne. 

- Usare le [variabili di automazione di Azure](../automation/automation-variables.md) per archiviare i parametri per l'esecuzione di un piano di ripristino.
- Aggiungendo il nome del piano di ripristino come prefisso per la variabile, è possibile creare singole variabili per ogni piano di ripristino. Usare quindi le variabili come parametri.
- È possibile modificare un parametro senza modificare lo script, cambiando comunque il funzionamento dello script.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Usare una variabile di tipo stringa semplice nello script di un runbook

In questo esempio uno script accetta l'input di un gruppo di sicurezza di rete (NSG) e lo applica alle macchine virtuali in un piano di ripristino. 

1. In modo che lo script possa rilevare il piano di ripristino in esecuzione, usare questo contesto del piano di ripristino:

    ```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
    ```

2. Prendere nota del nome e del gruppo di risorse di NSG. Queste variabili vengono usate come input per gli script del piano di ripristino. 
1. Negli asset dell'account di automazione. creare una variabile per archiviare il nome del NSG. Aggiungere un prefisso al nome della variabile con il nome del piano di ripristino.

    ![Creare una variabile per il nome del gruppo sicurezza di rete](media/site-recovery-runbook-automation-new/var1.png)

2. Creare una variabile per archiviare il nome del gruppo di risorse per la risorsa NSG. Aggiungere un prefisso al nome della variabile con il nome del piano di ripristino.

    ![Creare un nome del gruppo di risorse del gruppo sicurezza di rete](media/site-recovery-runbook-automation-new/var2.png)


3.  Nello script usare questo codice di riferimento per ottenere i valori delle variabili:

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  Usare le variabili nel runbook per applicare il gruppo di sicurezza di rete all'interfaccia di rete della macchina virtuale di cui è stato eseguito il failover:

    ```
    InlineScript {
    if (($Using:NSGname -ne $Null) -And ($Using:NSGRGname -ne $Null)) {
            $NSG = Get-AzureRmNetworkSecurityGroup -Name $Using:NSGname -ResourceGroupName $Using:NSGRGname
            Write-output $NSG.Id
            #Apply the NSG to a network interface
            #$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
            #Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            #  -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $NSG
        }
    }
    ```


Creare variabili indipendenti per ogni piano di ripristino, in modo che sia possibile riutilizzare lo script. Aggiungere un prefisso con il nome del piano di ripristino. 

Per uno script end-to-end completo per questo scenario, esaminare [questo script](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee).


### <a name="use-a-complex-variable-to-store-more-information"></a>Usare una variabile complessa per archiviare altre informazioni

In alcuni scenari potrebbe non essere possibile creare variabili separate per ogni piano di ripristino. Si consideri uno scenario in cui si vuole che un singolo script assegni un indirizzo IP pubblico in macchine virtuali specifiche. In un altro scenario potrebbe essere necessario applicare gruppi di sicurezza di rete diversi in diverse macchine virtuali e non in tutte le macchine virtuali. Si noti che:

- È possibile creare uno script riutilizzabile per qualsiasi piano di ripristino.
- Ogni piano di ripristino può avere un numero variabile di macchine virtuali.
- Ad esempio, un ripristino di SharePoint ha due front-end. Un'applicazione line-of-business (LOB) semplice ha un solo front-end.
- In questo scenario non è possibile creare variabili separate per ogni piano di ripristino.

Nell'esempio seguente viene creata una [variabile complessa](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureautomationvariable) nell'account di automazione di Azure.

Questa operazione viene eseguita specificando più valori, usando Azure PowerShell.

1. In PowerShell accedere alla sottoscrizione di Azure:

    ```
    Connect-AzureRmAccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. Per archiviare i parametri, creare la variabile complessa usando il nome del piano di ripristino:

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. In questa variabile complessa **VMDetails** è l'ID della macchina virtuale protetta. Per ottenere l'ID di macchina virtuale, visualizzare le proprietà della macchina virtuale nel portale di Azure. Lo screenshot seguente mostra una variabile che archivia i dettagli di due macchine virtuali:

    ![Usare l'ID della macchina virtuale come GUID](media/site-recovery-runbook-automation-new/vmguid.png)

4. Usare questa variabile nel runbook. Se il GUID della macchina virtuale indicato viene trovato nel contesto del piano di ripristino, è possibile applicare il gruppo di sicurezza di rete nella macchina virtuale:

    ```
    $VMDetailsObj = (Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName).ToObject([hashtable])
    ```

4. Nel runbook scorrere in ciclo le macchine virtuali del contesto del piano di ripristino. Controllare se la macchina virtuale esiste in **$VMDetailsObj**. Se esiste, accedere alle proprietà della variabile per applicare il gruppo di sicurezza di rete:

    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap

        foreach($VMID in $VMinfo) {
            $VMDetails = $VMDetailsObj[$VMID].ToObject([hashtable]);
            Write-output $VMDetails
            if ($VMDetails -ne $Null) { #If the VM exists in the context, this will not be Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetails.NSGName
                $NSGRGname = $VMDetails.NSGResourceGroupName

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

È possibile usare lo stesso script per piani di ripristino diversi. Immettere parametri diversi archiviando il valore corrispondente a un piano di ripristino in variabili diverse.

## <a name="sample-scripts"></a>Script di esempio

Per distribuire gli script di esempio nell'account di Automazione, fare clic sul pulsante **Distribuisci in Azure**.

[![Distribuzione in Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Questo video fornisce un altro esempio. che mostra come ripristinare un'applicazione di WordPress a due livelli in Azure:


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su un [account RunAs di automazione di Azure](../automation/automation-create-runas-account.md)
- Esaminare gli [script di esempio di automazione di Azure](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=User&f%5B0%5D.Value=SC%20Automation%20Product%20Team&f%5B0%5D.Text=SC%20Automation%20Product%20Team).
- [Altre informazioni](site-recovery-failover.md) sull'esecuzione dei failover.



