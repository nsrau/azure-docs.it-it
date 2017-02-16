---
title: Aggiungere runbook di Automazione di Azure ai piani di ripristino in Site Recovery| Documentazione Microsoft
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
ms.workload: required
ms.date: 02/06/2017
ms.author: ruturajd@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 44b6ff6e588d529fd833a4a7fdd61df7e933ddd8
ms.openlocfilehash: b88974ef713211a40b52aafab1b079ed8dbfec49


---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Aggiungere runbook di automazione di Azure ai piani di ripristino
In questa esercitazione viene descritto come Azure Site Recovery si integra con Automazione di Azure per fornire estendibilità ai piani di ripristino. I piani di ripristino possono gestire il ripristino delle macchine virtuali protette tramite Azure Site Recovery per la replica nel cloud secondario e la replica negli scenari di Azure. Consentono inoltre di effettuare il ripristino **costantemente accurato**, **ripetibile** e **automatizzato**. Se viene eseguito il failover delle macchine virtuali in Azure, l’integrazione con Automazione di Azure si estende ai piani di ripristino e offre la possibilità di eseguire i runbook, consentendo in tal modo attività di automazione efficaci.

Se non si è ancora sentito parlare di Automazione di Azure, effettuare l'iscrizione [qui](https://azure.microsoft.com/services/automation/) e scaricare gli script di esempio [qui](https://azure.microsoft.com/documentation/scripts/). Altre informazioni su [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) e su come gestire il ripristino in Azure usando piani di ripristino sono disponibili [qui](https://azure.microsoft.com/blog/?p=166264).

In questa esercitazione, verrà illustrato come integrare i runbook di Automazione Azure nei piani di ripristino. Verranno automatizzate attività semplici che in precedenza richiedevano un intervento manuale e verrà illustrato come convertire un ripristino in più passaggi in un'azione di ripristino a singolo clic. Verrà esaminato inoltre come risolvere eventuali problemi di un semplice script.

## <a name="customize-the-recovery-plan"></a>Personalizzare il piano di ripristino
1. Per prima cosa aprire il pannello delle risorse del piano di ripristino. Come è possibile vedere, al piano di ripristino sono state aggiunte due macchine virtuali per il ripristino.

    ![](media/site-recovery-runbook-automation-new/essentials-rp.PNG)
- - -
1. Fare clic sul pulsante Personalizza per iniziare ad aggiungere un runbook aprendo il pannello di personalizzazione del piano di ripristino.

    ![](media/site-recovery-runbook-automation-new/customize-rp.PNG)


1. Fare clic con il pulsante destro sul gruppo di inizio 1 e selezionare questa opzione per aggiungere "Aggiungi post-azione".
2. Selezionare questa opzione per scegliere uno script nel nuovo pannello.
3. Rinominare lo script "Hello World".
4. Scegliere un nome dell'account di Automazione. Questo è l'account di Automazione di Azure. Si noti che questo account può essere in qualsiasi area geografica di Azure, ma deve appartenere alla stessa sottoscrizione dell'insieme di credenziali di Site Recovery.
5. Selezionare un runbook dall'account di Automazione. Si tratta dello script che verrà eseguito durante l'esecuzione del piano di ripristino dopo il ripristino del primo gruppo.

    ![](media/site-recovery-runbook-automation-new/update-rp.PNG)
6. Fare clic su OK per salvare lo script, che verrà aggiunto al gruppo di azione post del Gruppo 1: avvio.

    ![](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="salient-points-of-adding-a-script"></a>Aspetti principali dell'aggiunta di uno script
1. È possibile fare clic con il pulsante destro sullo script e scegliere "elimina passaggio" o "aggiorna script".
2. È possibile eseguire uno script in Azure durante il failover dalla posizione locale ad Azure ed è possibile eseguirlo come script lato primario prima dell'arresto, durante il failback da Azure alla posizione locale.
3. Quando è in esecuzione, lo script inserirà un contesto del piano di ripristino.

La variabile di contesto avrà un aspetto simile a quello riportato nell’esempio seguente.

        {"RecoveryPlanName":"hrweb-recovery",

        "FailoverType":"Test",

        "FailoverDirection":"PrimaryToSecondary",

        "GroupId":"1",

        "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                {"CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test"}

                }

        }


La tabella che segue contiene il nome e la descrizione di ciascuna variabile nel contesto.

| **Nome variabile** | **Descrizione** |
| --- | --- |
| RecoveryPlanName |Nome del piano di esecuzione. Consente di agire in base al nome utilizzando lo stesso script |
| FailoverType |Specifica se il failover è di test, pianificato o non pianificato. |
| FailoverDirection |Specifica se il ripristino avviene su primario o secondario |
| GroupID |Identifica il numero del gruppo all'interno del piano di ripristino quando il piano è in esecuzione |
| VmMap |Array di tutte le macchine virtuali presenti nel gruppo. |
| VMMap key |Chiave univoca (GUID) per ciascuna VM. È uguale all'ID di VMM della macchina virtuale, se applicabile. |
| RoleName |Nome della VM di Azure in fase di ripristino |
| CloudServiceName |Nome servizio cloud di Azure in cui viene creata la macchina virtuale. |
| CloudServiceName nel modello di distribuzione di Resource Manager |Nome del gruppo di risorse di Azure in cui viene creata la macchina virtuale. |

## <a name="using-complex-variables-per-recovery-plan"></a>Uso di variabili complesso per piano di ripristino
In alcuni casi, un runbook richiede un maggior numero di informazioni rispetto al semplice RecoveryPlanContext. Non esiste alcun meccanismo di prima classe per passare un parametro a un runbook. Tuttavia, se si desidera usare lo stesso script attraverso più piani di ripristino è possibile usare la variabile di contesto piano di ripristino "RecoveryPlanName" e ricorrere alla tecnica sperimentale seguente per usare una variabile complessa di Automazione di Azure in un runbook. Nell'esempio seguente viene illustrato come creare tre diversi asset di variabili complesse e usarli in runbook basato sul nome del piano di ripristino.

Supporre di voler usare 3 parametri aggiuntivi in un runbook. Codificarli in un formato JSON {"Var1":"testautomation","Var2":"Unplanned","Var3":"PrimaryToSecondary"}

Usare la [variabile complessa AA](../automation/automation-variables.md#variable-types) per creare un nuovo asset di Automazione.
Rinominare la variabile come <RecoveryPlanName>-param.
È possibile usare il riferimento indicato qui per creare una [variabile complessa](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396).

Per i piani di ripristino differenti, rinominare la variabile come

1. recoveryPlanName1>-param
2. recoveryPlanName2>-param
3. recoveryPlanName3>-param

A questo punto, nello script fare riferimento ai parametri come

1. Ottenere il nome RP dalla variabile $rpname = $Recoveryplancontext
2. Ottenere l'asset di $paramValue = "$($rpname)-params"
3. Usarlo come una variabile complessa per il piano di ripristino chiamando Get-AzureAutomationVariable [-AutomationAccountName] <String> -Name $paramValue.

Ad esempio, per ottenere il parametro/variabile complessa per il piano di ripristino SharepointApp, creare una variabile complessa di Automazione di Azure denominata "SharepointApp-params".

Usarla nel piano di ripristino estraendo la variabile dall'asset tramite l'istruzione Get-AzureAutomationVariable [-AutomationAccountName] <String> [-Name] $paramValue. [Consultare qui per altri dettagli](https://msdn.microsoft.com/library/dn913772.aspx)

In questo modo lo stesso script può essere usato per un piano di ripristino differente archiviando la variabile complessa specifico per il piano negli asset.

## <a name="sample-scripts"></a>Script di esempio
Per un repository di script che è possibile importare direttamente nell'account di Automazione, vedere [repository OMS di Kristian Nese per gli script](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/Solutions/asrautomation)

Lo script qui presente è un modello di Azure Resource Manager che distribuirà tutti gli script di seguito

* NSG

Il runbook NSG assegnerà gli indirizzi IP pubblici a tutte le macchine virtuali inserite nel piano di ripristino e collegherà le loro schede di rete virtuale a un gruppo di sicurezza di rete che consentirà la comunicazione predefinita

* IP pubblico

Il runbook dell'indirizzo IP pubblico assegnerà gli indirizzi IP pubblici a tutte le macchine virtuali inserite nel piano di ripristino. L'accesso ai computer e alle applicazioni dipenderà dalle impostazioni del firewall all'interno di ciascun guest

* CustomScript

Il runbook CustomScript assegnerà gli indirizzi IP pubblici a tutte le macchine virtuali inserite nel piano di ripristino e installerà un'estensione dello script personalizzato che eseguirà il pull dello script a cui si fa riferimento durante la distribuzione del modello

* NSGwithCustomScript

Il runbook NSGwithCustomScript assegnerà gli indirizzi IP pubblici a tutte le macchine virtuali inserite nel piano di ripristino, installerà uno script personalizzato usando l'estensione e stabilirà una connessione tra le schede di rete virtuale e un NSG consentendo le comunicazioni in ingresso e in uscita predefinite per l'accesso remoto

## <a name="additional-resources"></a>Risorse aggiuntive
[Account RunAs per il servizio di Automazione di Azure](../automation/automation-sec-configure-azure-runas-account.md)

[Panoramica di Automazione di Azure](http://msdn.microsoft.com/library/azure/dn643629.aspx "Panoramica di Automazione di Azure")

[Script di esempio di Automazione di Azure](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Script di esempio di Automazione di Azure")



<!--HONumber=Jan17_HO5-->


