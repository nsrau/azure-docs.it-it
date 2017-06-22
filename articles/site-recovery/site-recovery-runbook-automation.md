---
title: Aggiungere runbook di Automazione di Azure ai piani di ripristino in Site Recovery| Microsoft Docs
description: "In questo articolo viene descritto come Azure Site Recovery consente ora di estendere i piani di ripristino tramite Automazione di Azure per completare attività complesse durante il ripristino in Azure"
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
ms.date: 02/22/2017
ms.author: ruturajd@microsoft.com
ms.translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 198caeea693fbc48b6e0eb1c9c8ee559e0553261
ms.contentlocale: it-it
ms.lasthandoff: 03/31/2017


---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Aggiungere runbook di automazione di Azure ai piani di ripristino
In questa esercitazione viene descritto come Azure Site Recovery si integra con Automazione di Azure per fornire estendibilità ai piani di ripristino. I piani di ripristino possono gestire il ripristino delle macchine virtuali protette tramite Azure Site Recovery per la replica nel cloud secondario e la replica negli scenari di Azure. Consentono inoltre di effettuare il ripristino **costantemente accurato**, **ripetibile** e **automatizzato**. Se viene eseguito il failover delle macchine virtuali in Azure, l’integrazione con Automazione di Azure si estende ai piani di ripristino e offre la possibilità di eseguire i runbook, consentendo in tal modo attività di automazione efficaci.

Se non si è ancora sentito parlare di Automazione di Azure, effettuare l'iscrizione [qui](https://azure.microsoft.com/services/automation/) e scaricare gli script di esempio [qui](https://azure.microsoft.com/documentation/scripts/). Altre informazioni su [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) e su come gestire il ripristino in Azure usando piani di ripristino sono disponibili [qui](https://azure.microsoft.com/blog/?p=166264).

Questa esercitazione illustra come integrare i runbook di Automazione di Azure nei piani di ripristino. Vengono automatizzate attività semplici che in precedenza richiedevano un intervento manuale e viene illustrato come convertire un'azione di ripristino in più passaggi in un'azione di ripristino con un solo clic.

## <a name="customize-the-recovery-plan"></a>Personalizzare il piano di ripristino
1. Per prima cosa aprire il pannello delle risorse del piano di ripristino. Come è possibile vedere, al piano di ripristino sono state aggiunte due macchine virtuali per il ripristino.

    ![](media/site-recovery-runbook-automation-new/essentials-rp.PNG)
- - -
1. Fare clic sul pulsante Personalizza per iniziare ad aggiungere un runbook.

    ![](media/site-recovery-runbook-automation-new/customize-rp.PNG)


1. Fare clic con il pulsante destro del mouse sul gruppo di avvio 1 e selezionare "Aggiungi post-azione".
2. Selezionare questa opzione per scegliere uno script nel nuovo pannello.
3. Rinominare lo script "Hello World".
4. Scegliere un nome dell'account di Automazione.
    >[!NOTE]
    > L'account di Automazione può trovarsi in qualsiasi area geografica di Azure, ma deve appartenere alla stessa sottoscrizione dell'insieme di credenziali di Site Recovery.

5. Selezionare un runbook dall'account di Automazione. Questo runbook è lo script che verrà eseguito durante l'esecuzione del piano di ripristino dopo il ripristino del primo gruppo.

    ![](media/site-recovery-runbook-automation-new/update-rp.PNG)
6. Fare clic su OK per salvare lo script, che viene aggiunto al gruppo post-azione di Gruppo 1: avvio.

    ![](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="salient-points-of-adding-a-script"></a>Aspetti principali dell'aggiunta di uno script
1. È possibile fare clic con il pulsante destro del mouse sullo script e scegliere Elimina passaggio o Aggiorna script.
2. È possibile eseguire uno script in Azure durante il failover dalla posizione locale ad Azure ed è possibile eseguirlo come script lato primario prima dell'arresto, durante il failback da Azure alla posizione locale.
3. Quando è in esecuzione, inserisce un contesto del piano di ripristino.

La variabile di contesto ha un aspetto simile all'esempio seguente.

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


La tabella seguente contiene il nome e la descrizione di ogni variabile nel contesto.

| **Nome variabile** | **Descrizione** |
| --- | --- |
| RecoveryPlanName |Nome del piano di esecuzione. Questa variabile consente di eseguire azioni diverse in base al nome e riutilizzando lo script. |
| FailoverType |Specifica se il failover è di test, pianificato o non pianificato. |
| FailoverDirection |Specifica se il ripristino avviene su primario o secondario |
| GroupID |Identifica il numero del gruppo all'interno del piano di ripristino quando il piano è in esecuzione |
| VmMap |Array di tutte le macchine virtuali presenti nel gruppo. |
| VMMap key |Chiave univoca (GUID) per ciascuna VM. È uguale all'ID di VMM della macchina virtuale, se applicabile. |
| SubscriptionId |ID sottoscrizione di Azure in cui viene creata la macchina virtuale. |
| RoleName |Nome della VM di Azure in fase di ripristino |
| CloudServiceName |Nome servizio cloud di Azure in cui viene creata la macchina virtuale. |
| ResourceGroupName|Nome del gruppo di risorse di Azure in cui viene creata la macchina virtuale. |
| RecoveryPointId|Timestamp in cui viene ripristinata la macchina virtuale. |

È necessario assicurarsi che all'account di automazione vengano aggiunti i moduli indicati di seguito. Tutti i moduli devono essere di versioni compatibili. Una soluzione semplice consiste nell'assicurarsi che tutti i moduli siano aggiornati alla versione più recente disponibile.
* AzureRM.profile
* AzureRM.Resources
* AzureRM.Automation
* AzureRM.Network
* AzureRM.Compute


### <a name="accessing-all-vms-of-the-vmmap-in-a-loop"></a>Accesso a tutte le macchine virtuali di VmMap in un ciclo
Usare il frammento di codice seguente per riprodurre a ciclo continuo tutte le macchine virtuali di VmMap.

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
> I valori Resource group name e Rolename sono vuoti quando lo script è una pre-azione per l'avvio di un gruppo. Tali valori vengono popolati solo se la macchina virtuale del gruppo riesce a eseguire il failover e lo script è una post-azione del gruppo di avvio.

## <a name="using-the-same-automation-runbook-with-multiple-recovery-plans"></a>Uso dello stesso runbook di Automazione con più piani di ripristino

È possibile usare un unico script in più piani di ripristino servendosi di variabili esterne. È possibile usare le [variabili di Automazione di Azure](../automation/automation-variables.md) per archiviare i parametri da passare per l'esecuzione di un piano di ripristino. Anteponendo il nome del piano di ripristino alla variabile è possibile creare variabili singole per ogni piano di ripristino e usarle come parametro. È possibile modificare il parametro senza modificare lo script cambiando il funzionamento dello script.

### <a name="using-simple-string-variables-in-runbook-script"></a>Uso di variabili di tipo stringa semplici nello script del runbook

Si prenda ad esempio uno script che accetta l'input di un gruppo di sicurezza di rete e lo applica alle macchine virtuali di un piano di ripristino.

Per fare in modo che lo script capisca quale piano di ripristino è in esecuzione, è possibile usare il contesto del piano di ripristino.

```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
```

Per applicare un gruppo di sicurezza di rete esistente, sono necessari il nome e il gruppo di risorse del gruppo sicurezza di rete. Queste variabili vengono specificate come input per gli script dei piani di ripristino. A questo scopo, creare due variabili negli asset degli account di automazione e anteporvi il nome del piano di ripristino per il quale si creano i parametri.

1. Creare una variabile per archiviare il nome del gruppo sicurezza di rete. Anteporre il nome del piano di ripristino.
    ![Creare una variabile per il nome del gruppo sicurezza di rete](media/site-recovery-runbook-automation-new/var1.png)

2. Creare una variabile per archiviare il nome del gruppo di risorse del gruppo sicurezza di rete. Anteporre il nome del piano di ripristino.
    ![Creare una variabile per il nome del gruppo di risorse del gruppo sicurezza di rete](media/site-recovery-runbook-automation-new/var2.png)


Per acquisire i valori delle variabili nello script, usare il codice di riferimento seguente:

```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
```

Successivamente, è possibile usare le variabili nel runbook e applicare il gruppo di sicurezza di rete all'interfaccia di rete della macchina virtuale di cui è stato eseguito il failover.

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

Creare variabili indipendenti per ogni piano di ripristino, in modo che sia possibile riutilizzare lo script e anteporvi il nome del piano di ripristino. Uno script end-to-end completo per questo esempio è disponibile [qui](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee).


### <a name="using-complex-variable-to-store-more-information"></a>Uso di una variabile complessa per archiviare altre informazioni

Si prenda ad esempio uno scenario in cui uno script deve abilitare un indirizzo IP pubblico in macchine virtuali specifiche. Un altro esempio potrebbe essere l'applicazione di gruppi di sicurezza di rete diversi in macchine virtuali diverse, ma non in tutte. Lo script deve essere riutilizzabile per qualsiasi altro piano di ripristino. Ogni piano di ripristino può includere un numero variabile di macchine virtuali. Ad esempio, un ripristino SharePoint include due front-end, mentre un'applicazione LOB semplice include un solo front-end. Per ottenere questo risultato, non è possibile creare variabili separate per ogni piano di ripristino. Qui viene usata una nuova tecnica e viene creata una [variabile complessa](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396) negli asset dell'account di automazione di Azure, specificando più valori. Per eseguire questa procedura è necessario Azure PowerShell.

1. Accedere alla sottoscrizione da Azure PowerShell.

    ```
        login-azurermaccount
        $sub = Get-AzureRmSubscription -Name <SubscriptionName>
        $sub | Select-AzureRmSubscription
    ```

2. Per archiviare i parametri, creare la variabile complessa attribuendole lo stesso nome del piano di ripristino.

    ```
        $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

    In questa variabile complessa **VMDetails* è l'ID macchina virtuale della macchina virtuale protetta. Questo dato è reperibile tra le proprietà della macchina virtuale nel portale. Qui è stata creata una variabile per l'archiviazione dei dettagli di due macchine virtuali.

    ![ID della macchina virtuale da usare come GUID](media/site-recovery-runbook-automation-new/vmguid.png)

3. Se uno dei VMGUID specificati viene trovato nel contesto del piano di ripristino, usare questa variabile nel runbook e applicare il gruppo di sicurezza di rete alla macchina virtuale.

    ```
        $VMDetailsObj = Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName
    ```

4. Riprodurre a ciclo continuo le macchine virtuali del contesto del piano di ripristino nel runbook e verificare se la macchina virtuale esiste anche in **$VMDetailsObj**. Se esiste, applicare il gruppo di sicurezza di rete tramite l'accesso alle proprietà della variabile.
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

È possibile usare lo stesso script con piani di ripristino diversi e specificare parametri differenti archiviando il valore corrispondente a vari piani di ripristino in variabili differenti.

## <a name="sample-scripts"></a>Script di esempio
Distribuire gli script di esempio nell'account di Automazione usando il pulsante Distribuisci in Azure sotto.

[![Distribuzione in Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Guardare anche un breve video sul ripristino di un'applicazione WordPress a due livelli in Azure.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="additional-resources"></a>Risorse aggiuntive
[Account RunAs per il servizio di Automazione di Azure](../automation/automation-sec-configure-azure-runas-account.md)

[Panoramica di Automazione di Azure](http://msdn.microsoft.com/library/azure/dn643629.aspx "Panoramica di Automazione di Azure")

[Script di esempio di Automazione di Azure](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Script di esempio di Automazione di Azure")

