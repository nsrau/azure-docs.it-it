---
title: Aggiungere runbook di Automazione di Azure ai piani di ripristino in Azure Site Recovery| Microsoft Docs
description: "Informazioni su come Azure Site Recovery può essere utile per estendere i piani di ripristino tramite Automazione di Azure. Informazioni su come eseguire attività complesse durante il ripristino in Azure."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: ecece14d-5f92-4596-bbaf-5204addb95c2
ms.service: site-recovery
ms.devlang: powershell
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 03/09/2018
ms.author: ruturajd@microsoft.com
ms.openlocfilehash: 4802215f903eb196afbf05637ad5e38dbbbc09a3
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Aggiungere runbook di Automazione di Azure ai piani di ripristino
Questo articolo descrive come Azure Site Recovery si integra con Automazione di Azure per facilitare l'estensione dei piani di ripristino. I piani di ripristino possono orchestrare il ripristino di macchine virtuali protette con Site Recovery. I piani di ripristino possono essere usati sia per la replica in un cloud secondario che per la replica in Azure e consentono anche di ottenere un ripristino **costantemente accurato**, **ripetibile** e **automatizzato**. Se si esegue il failover delle macchine virtuali in Azure, l'integrazione con Automazione di Azure estende i piani di ripristino. È possibile usare questa funzionalità per eseguire runbook, che offrono attività di automazione dalle grandi potenzialità.

Se non si ha familiarità con Automazione di Azure, è possibile [iscriversi](https://azure.microsoft.com/services/automation/) e [scaricare script di esempio](https://azure.microsoft.com/documentation/scripts/). Per altre informazioni e per scoprire come orchestrare il ripristino in Azure tramite i [piani di ripristino](./site-recovery-create-recovery-plans.md), vedere [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/).

Questo articolo descrive come è possibile integrare i runbook di Automazione di Azure nei piani di ripristino. Vengono usati alcuni esempi per automatizzare attività di base che richiedevano in precedenza un intervento manuale. Viene anche descritto come convertire un ripristino in più passaggi in un'azione di ripristino con clic singolo.

## <a name="customize-the-recovery-plan"></a>Personalizzare il piano di ripristino
1. Passare al pannello delle risorse per il piano di ripristino di **Site Recovery**. In questo esempio, al piano di ripristino sono state aggiunte due macchine virtuali per il ripristino. Per iniziare ad aggiungere un runbook, fare clic sulla scheda **Personalizza**.

    ![Fare clic sul pulsante Personalizza](media/site-recovery-runbook-automation-new/essentials-rp.png)


2. Fare clic con il pulsante destro del mouse su **Gruppo 1: Avvio** e quindi scegliere **Aggiungi post-azione**.

    ![Fare clic con il pulsante destro del mouse su Gruppo 1: Avvio e scegliere Aggiungi post-azione](media/site-recovery-runbook-automation-new/customize-rp.png)

3. Fare clic su **Choose a script** (Scegli uno script).

4. Nel pannello **Aggiorna azione** assegnare il nome **Hello World** allo script.

    ![Pannello Aggiorna azione](media/site-recovery-runbook-automation-new/update-rp.png)

5. Immettere il nome di un account di Automazione.
    >[!NOTE]
    > L'account di Automazione può essere in qualsiasi area di Azure e deve trovarsi nella stessa sottoscrizione dell'insieme di credenziali di Azure Site Recovery.

6. Nell'account di Automazione selezionare un runbook. Questo runbook è lo script che viene eseguito durante l'esecuzione del piano di ripristino dopo il ripristino del primo gruppo.

7. Fare clic su **OK** per salvare lo script. Lo script viene aggiunto a **Gruppo 1: passaggi successivi**.

    ![Post-azione Gruppo 1: Avvio](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="considerations-for-adding-a-script"></a>Considerazioni per l'aggiunta di uno script

* Per accedere alle opzioni per **eliminare un passaggio** o **aggiornare lo script**, fare clic con il pulsante destro del mouse sullo script.
* È possibile eseguire uno script in Azure durante il failover da un computer locale ad Azure. È anche possibile eseguirlo in Azure come script del sito primario prima dell'arresto, durante il failback da Azure a un computer locale.
* Quando è in esecuzione, inserisce un contesto del piano di ripristino. L'esempio seguente mostra una variabile di contesto:

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

    La tabella seguente indica il nome e la descrizione di ogni variabile nel contesto.

    | **Nome variabile** | **Descrizione** |
    | --- | --- |
    | RecoveryPlanName |Nome del piano in esecuzione. Questa variabile consente di eseguire azioni diverse in base al nome del piano di ripristino. È anche possibile riutilizzare lo script. |
    | FailoverType |Specifica se il failover è di test, pianificato o non pianificato. |
    | FailoverDirection |Specifica se il ripristino avviene in un sito primario o secondario. |
    | GroupID |Identifica il numero del gruppo nel piano di ripristino quando il piano è in esecuzione. |
    | VmMap |Matrice di tutte le macchine virtuali nel gruppo. |
    | VMMap key |Chiave univoca (GUID) per ogni macchina virtuale. È uguale all'ID di Azure Virtual Machine Manager (VMM) della macchina virtuale, se applicabile. |
    | SubscriptionId |ID della sottoscrizione di Azure in cui viene creata la macchina virtuale. |
    | RoleName |Nome della macchina virtuale di Azure in corso di ripristino. |
    | CloudServiceName |Nome del servizio cloud di Azure in cui è stata creata la macchina virtuale. |
    | ResourceGroupName|Nome del gruppo di risorse di Azure in cui è stata creata la macchina virtuale. |
    | RecoveryPointId|Timestamp del ripristino della macchina virtuale. |

* Assicurarsi che l'account di Automazione abbia i moduli seguenti:
    * AzureRM.profile
    * AzureRM.Resources
    * AzureRM.Automation
    * AzureRM.Network
    * AzureRM.Compute

Tutti i moduli devono essere di versioni compatibili. Un modo semplice per assicurarsi che tutti i moduli siano compatibili consiste nell'usare le versioni più recenti di tutti i moduli.

### <a name="access-all-vms-of-the-vmmap-in-a-loop"></a>Accedere a tutte le macchine virtuali di VMMap in ciclo
Usare il frammento di codice seguente per accedere a tutte le macchine virtuali di Microsoft VMMap in ciclo:

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

> [!NOTE]
> Il nome del gruppo di risorse e il nome del ruolo sono vuoti quando lo script è una pre-azione per un gruppo di avvio. I valori vengono popolati solo se il failover della macchina virtuale di tale gruppo ha esito positivo. Lo script è una post-azione del gruppo di avvio.

## <a name="use-the-same-automation-runbook-in-multiple-recovery-plans"></a>Usare lo stesso runbook di Automazione in più piani di ripristino

È possibile usare un unico script in più piani di ripristino servendosi di variabili esterne. È possibile usare le [variabili di Automazione di Azure](../automation/automation-variables.md) per archiviare i parametri da passare per l'esecuzione di un piano di ripristino. Aggiungendo il nome del piano di ripristino come prefisso per la variabile, è possibile creare singole variabili per ogni piano di ripristino. Usare quindi le variabili come parametri. È possibile modificare un parametro senza modificare lo script, cambiando comunque il funzionamento dello script.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Usare una variabile di tipo stringa semplice nello script di un runbook

In questo esempio, uno script accetta l'input di un gruppo di sicurezza di rete e lo applica alle macchine virtuali di un piano di ripristino.

Per fare in modo che lo script rilevi quale piano di ripristino è in esecuzione, usare il contesto del piano di ripristino:

```
workflow AddPublicIPAndNSG {
    param (
          [parameter(Mandatory=$false)]
          [Object]$RecoveryPlanContext
    )

    $RPName = $RecoveryPlanContext.RecoveryPlanName
```

Per applicare un gruppo di sicurezza di rete esistente, è necessario conoscere il nome e il gruppo di risorse del gruppo sicurezza di rete. Usare queste variabili come input per gli script dei piani di ripristino. A tale scopo, creare due variabili negli asset dell'account di Automazione. Aggiungere il nome del piano di ripristino per il quale si stanno creando i parametri come prefisso del nome della variabile.

1. Creare una variabile per archiviare il nome del gruppo sicurezza di rete. Aggiungere un prefisso al nome della variabile usando il nome del piano di ripristino.

    ![Creare una variabile per il nome del gruppo sicurezza di rete](media/site-recovery-runbook-automation-new/var1.png)

2. Creare una variabile per archiviare il nome del gruppo di risorse del gruppo sicurezza di rete. Aggiungere un prefisso al nome della variabile usando il nome del piano di ripristino.

    ![Creare un nome del gruppo di risorse del gruppo sicurezza di rete](media/site-recovery-runbook-automation-new/var2.png)


3.  Nello script usare il codice di riferimento seguente per ottenere i valori delle variabili:

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

Creare variabili indipendenti per ogni piano di ripristino, in modo che sia possibile riutilizzare lo script. Aggiungere un prefisso con il nome del piano di ripristino. Per uno script completo per questo scenario, vedere [Add a public IP and NSG to VMs during test failover of a Site Recovery recovery plan](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee) (Aggiungere un indirizzo IP pubblico e un gruppo di sicurezza di rete alle macchina virtuali durante il failover di test di un piano di ripristino di Site Recovery).


### <a name="use-a-complex-variable-to-store-more-information"></a>Usare una variabile complessa per archiviare altre informazioni

Si consideri uno scenario in cui si vuole usare un singolo script per attivare un indirizzo IP pubblico in macchine virtuali specifiche. In un altro scenario potrebbe essere necessario applicare gruppi di sicurezza di rete diversi in diverse macchine virtuali e non in tutte le macchine virtuali. È possibile creare uno script riutilizzabile per qualsiasi piano di ripristino. Ogni piano di ripristino può avere un numero variabile di macchine virtuali. Ad esempio, un ripristino di SharePoint ha due front-end. Un'applicazione line-of-business (LOB) semplice ha un solo front-end. Non è possibile creare variabili separate per ogni piano di ripristino.

Nell'esempio seguente viene usata una nuova tecnica e viene creata una [variabile complessa](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396) negli asset dell'account di Automazione di Azure. Questo risultato si ottiene specificando più valori. Per eseguire questa procedura, è necessario usare Azure PowerShell:

1. In PowerShell accedere alla sottoscrizione di Azure:

    ```
    login-azurermaccount
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
    $VMDetailsObj = Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName
    ```

4. Nel runbook scorrere in ciclo le macchine virtuali del contesto del piano di ripristino. Controllare se la macchina virtuale esiste in **$VMDetailsObj**. Se esiste, accedere alle proprietà della variabile per applicare il gruppo di sicurezza di rete:

    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap

        foreach($VMID in $VMinfo) {
            Write-output $VMDetailsObj.value.$VMID

            if ($VMDetailsObj.value.$VMID -ne $Null) { #If the VM exists in the context, this will not b Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetailsObj.value.$VMID.'NSGName'
                $NSGRGname = $VMDetailsObj.value.$VMID.'NSGResourceGroupName'

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

È possibile usare lo stesso script per piani di ripristino diversi. Immettere parametri diversi archiviando il valore corrispondente a un piano di ripristino in variabili diverse.

## <a name="sample-scripts"></a>Script di esempio

Per distribuire gli script di esempio nell'account di Automazione, fare clic sul pulsante **Distribuisci in Azure**.

[![Distribuzione in Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Per un altro esempio, vedere il video seguente che mostra come ripristinare un'applicazione di WordPress a due livelli in Azure:


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="additional-resources"></a>Risorse aggiuntive
* [Account RunAs per il servizio Automazione di Azure](../automation/automation-create-runas-account.md)
* [Panoramica di Automazione di Azure](http://msdn.microsoft.com/library/azure/dn643629.aspx "Panoramica di Automazione di Azure")
* [Script di esempio di Automazione di Azure](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Script di esempio di Automazione di Azure")

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni](site-recovery-failover.md) sull'esecuzione dei failover.
