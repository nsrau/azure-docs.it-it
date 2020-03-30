---
title: Aggiungere runbook di Automazione di Azure ai piani di ripristino di Site RecoveryAdd Azure Automation runbooks to Site Recovery recovery plans
description: Informazioni su come estendere i piani di ripristino con Automazione di Azure per il ripristino di emergenza tramite Azure Site Recovery.Learn how to extend recovery plans with Azure Automation for disaster recovery using Azure Site Recovery.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: rajanaki
ms.openlocfilehash: ecfe993a137ca63c84438870ec54ac1e6d6707da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257485"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Aggiungere runbook di Automazione di Azure ai piani di ripristino

Questo articolo descrive come integrare i runbook di Automazione di Azure per estendere i piani di ripristino di [Azure Site Recovery.This](site-recovery-overview.md) article describes how to integrate Azure Automation runbooks, to extend Azure Site Recovery recovery plans. Viene illustrato come automatizzare le attività di base che altrimenti avrebbero bisogno di un intervento manuale e come convertire un ripristino in più passaggi in un'azione con un solo clic.

## <a name="recovery-plans"></a>Piani di ripristino 

You can use recovery plans when you fail over on-premises machines, or Azure VMs. I piani di ripristino consentono di definire un processo di ripristino sistematico che definisce il modo in cui i computer eseguono il failover e come vengono avviati e ripristinati dopo il failover. 

Le app di grandi dimensioni di ripristino possono essere complesse. I piani di ripristino consentono di imporre l'ordine in modo che il ripristino sia costantemente accurato, ripetibile e automatizzato. È possibile automatizzare le attività all'interno di un piano di ripristino usando gli script e i runbook di Automazione di Azure.You can automate tasks within a recovery plan using scripts, as well as Azure Automation runbooks. Esempi tipici possono essere la configurazione delle impostazioni in una macchina virtuale di Azure dopo il failover o la riconfigurazione di un'app in esecuzione nella macchina virtuale.

- [Ulteriori informazioni](recovery-plan-overview.md) sui piani di ripristino.
- [Altre informazioni](../automation/automation-runbook-types.md) sui runbook di Automazione di Azure.Learn more about Azure Automation runbooks.



## <a name="runbooks-in-recovery-plans"></a>Runbook nei piani di ripristino

Aggiungere un account di Automazione di Azure e runbook a un piano di ripristino. Il runbook viene richiamato quando viene eseguito il piano di ripristino.

- L'account di automazione può trovarsi in qualsiasi area di Azure e deve trovarsi nella stessa sottoscrizione dell'insieme di Site Recovery.The Automation account can be in any Azure region, and must be in the same subscription as the Site Recovery vault. 
- Un runbook può essere eseguito in un piano di ripristino durante il failover da una posizione primaria a quella secondaria o durante il failback dalla posizione secondaria a quella primaria.
- I runbook in un piano di ripristino vengono eseguiti in modo seriale, uno dopo l'altro, nell'ordine impostato.
- Se i runbook in un piano di ripristino configurano le macchine virtuali per l'avvio in gruppi diversi, il piano di ripristino continuerà solo quando Azure segnala tutte le macchine virtuali come in esecuzione.
- I piani di ripristino continuano a essere eseguiti, anche se uno script ha esito negativo.

### <a name="recovery-plan-context"></a>Contesto del piano di ripristino

Quando uno script viene eseguito, inserisce un contesto del piano di ripristino nel runbook. Il contesto contiene le variabili riepilogate nella tabella.

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
| RoleName |The name of the Azure VM. |
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

Se si desidera accedere a tutte le macchine virtuali in VMMap in un ciclo, è possibile usare il codice seguente:If you want to access all VMs in VMMap in a loop, you can use the following code:

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


Il blog di Aman Sharma su [Harvesting Clouds](http://harvestingclouds.com) ha un utile esempio di script di contesto del piano di [recupero.](http://harvestingclouds.com/post/script-sample-azure-automation-runbook-for-asr-recovery-plan/)



## <a name="before-you-start"></a>Prima di iniziare

- Se non si ha familiarità con Automazione di Azure, è possibile [iscriversi](https://azure.microsoft.com/services/automation/) e [scaricare script di esempio.](https://azure.microsoft.com/documentation/scripts/)
- Assicurarsi che l'account di Automazione abbia i moduli seguenti:
    - AzureRM.profile
    - AzureRM.Resources
    - AzureRM.Automation
    - AzureRM.Network
    - AzureRM.Compute

    Tutti i moduli devono essere di versioni compatibili. Il modo più semplice è quello di utilizzare sempre le versioni più recenti di tutti i moduli.



## <a name="customize-the-recovery-plan"></a>Personalizzare il piano di ripristino

1. Nell'insieme di credenziali selezionare **Piani di ripristino (Site Recovery)**
2. Per creare un piano di ripristino, fare clic su **Piano di ripristino**. [Scopri di più](site-recovery-create-recovery-plans.md). Se si dispone già di un piano di ripristino, selezionare per aprirlo.
3. Nella pagina del piano di ripristino fare clic su **Personalizza**.

    ![Fare clic sul pulsante Personalizza](media/site-recovery-runbook-automation-new/custom-rp.png)

2. Fare clic sui puntini di sospensione (...) accanto all'azione **Gruppo 1: Avvia** > **post**.
3. In **Azione di inserimento**verificare che **script** sia selezionato e specificare un nome per lo script (**Hello World**).
4. Specificare un account di automazione e selezionare un runbook. Fare clic su **OK** per salvare lo script. Lo script viene aggiunto a **Gruppo 1: passaggi successivi**.


## <a name="reuse-a-runbook-script"></a>Riutilizzare uno script del runbook

È possibile usare un singolo script runbook in più piani di ripristino usando variabili esterne. 

- Usare le variabili di [Automazione di Azure](../automation/automation-variables.md) per archiviare i parametri per l'esecuzione di un piano di ripristino.
- Aggiungendo il nome del piano di ripristino come prefisso per la variabile, è possibile creare singole variabili per ogni piano di ripristino. Usare quindi le variabili come parametri.
- È possibile modificare un parametro senza modificare lo script, cambiando comunque il funzionamento dello script.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Usare una variabile di tipo stringa semplice nello script di un runbook

In questo esempio, uno script accetta l'input di un gruppo di sicurezza di rete (NSG) e lo applica alle macchine virtuali in un piano di ripristino. 

1. In modo che lo script possa rilevare quale piano di ripristino è in esecuzione, usare questo contesto del piano di ripristino:To that the script can in detect which recovery plan is running, use this recovery plan context:

    ```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
    ```

2. Prendere nota del nome del gruppo di sicurezza di rete e del gruppo di risorse. Queste variabili vengono utilizzate come input per gli script del piano di ripristino. 
1. Negli asset dell'account Di automazione. creare una variabile per archiviare il nome del gruppo di sicurezza di gruppo. Aggiungere un prefisso al nome della variabile con il nome del piano di ripristino.

    ![Creare una variabile per il nome del gruppo sicurezza di rete](media/site-recovery-runbook-automation-new/var1.png)

2. Creare una variabile per archiviare il nome del gruppo di risorse per la risorsa del gruppo di sicurezza di rete. Aggiungere un prefisso al nome della variabile con il nome del piano di ripristino.

    ![Creare un nome del gruppo di risorse del gruppo sicurezza di rete](media/site-recovery-runbook-automation-new/var2.png)


3.  Nello script usare questo codice di riferimento per ottenere i valori delle variabili:In the script, use this reference code to get the variable values:

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

Per uno script completo end-to-end per questo scenario, esaminare [questo script](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee).


### <a name="use-a-complex-variable-to-store-more-information"></a>Usare una variabile complessa per archiviare altre informazioni

In alcuni scenari potrebbe non essere possibile creare variabili separate per ogni piano di ripristino. Si consideri uno scenario in cui si vuole un singolo script per assegnare un indirizzo IP pubblico in macchine virtuali specifiche. In un altro scenario potrebbe essere necessario applicare gruppi di sicurezza di rete diversi in diverse macchine virtuali e non in tutte le macchine virtuali. Tenere presente quanto segue:

- È possibile creare uno script riutilizzabile per qualsiasi piano di ripristino.
- Ogni piano di ripristino può avere un numero variabile di macchine virtuali.
- Ad esempio, un ripristino di SharePoint ha due front-end. Un'applicazione line-of-business (LOB) semplice ha un solo front-end.
- In questo scenario non è possibile creare variabili separate per ogni piano di ripristino.

Nell'esempio seguente viene creata una [variabile complessa](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureautomationvariable) nell'account di Automazione di Azure.In the following example, we create a complex variable in the Azure Automation account.

A tale scopo, specificare più valori, usando Azure PowerShell.We do this by specifying multiple values, using Azure PowerShell.

1. In PowerShell accedere alla sottoscrizione di Azure:

    ```
    Connect-AzureRmAccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. Per archiviare i parametri, creare la variabile complessa utilizzando il nome del piano di ripristino:

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

[![Distribuire in AzureDeploy to Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Questo video fornisce un altro esempio. che mostra come ripristinare un'applicazione di WordPress a due livelli in Azure:


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su un [account RunAs di Automazione di AzureLearn](../automation/automation-create-runas-account.md) about an Azure Automation RunAs account
- Esaminare gli [script di esempio di Automazione di Azure](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=User&f%5B0%5D.Value=SC%20Automation%20Product%20Team&f%5B0%5D.Text=SC%20Automation%20Product%20Team).
- [Altre informazioni](site-recovery-failover.md) sull'esecuzione dei failover.



