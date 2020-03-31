---
title: Risolvere gli errori di macchine virtuali e di ambiente Azure DevTest LabsTroubleshoot VM and environment failures Azure DevTest Labs
description: Informazioni su come risolvere gli errori di creazione di macchine virtuali (VM) e di ambiente in Azure DevTest Labs.Learn how to troubleshoot virtual machine (VM) and environment creation failures in Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 155a359608cf6d846578306545f5ce0b4003949c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166338"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Risolvere gli errori di creazione di macchine virtuali (VM) e di ambiente in Azure DevTest LabsTroubleshoot virtual machine (VM) and environment creation failures in Azure DevTest Labs
DevTest Labs genera avvisi se il nome di un computer non è valido o se si sta per violare un criterio lab. A volte viene `X` visualizzato rosso accanto alla macchina virtuale del lab o allo stato dell'ambiente che informa che si è verificato un errore.  Questo articolo fornisce alcuni trucchi che è possibile utilizzare per trovare il problema sottostante e, si spera, evitare il problema in futuro.

## <a name="portal-notifications"></a>Notifiche del portale
Se si usa il portale di Azure, il primo punto da esaminare è il **pannello delle notifiche.**  Il pannello delle notifiche, disponibile nella barra dei comandi principale facendo clic **sull'icona**a campana , indicherà se la creazione della macchina virtuale o dell'ambiente di laboratorio è stata eseguita correttamente o meno.  Se si è verificato un errore, viene visualizzato il messaggio di errore associato all'errore di creazione. I dettagli spesso forniscono ulteriori informazioni per aiutarti a risolvere il problema. Nell'esempio seguente, la creazione della macchina virtuale non è riuscita a causa dell'esaurimento dei core. Il messaggio dettagliato spiega come risolvere il problema e richiedere un aumento della quota principale.

![Notifica del portale di AzureAzure portal notification](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)

### <a name="vm-in-corruption-state"></a>VM in stato di danneggiamentoVM in corruption state
Se lo stato della macchina virtuale nel lab è **danneggiato,** è possibile che la macchina virtuale sottostante sia stata eliminata dalla pagina **Macchina virtuale** a cui l'utente può passare dalla pagina **Macchine virtuali** (non dalla pagina DevTest Labs). Pulire il lab in DevTest Labs eliminando la macchina virtuale dal lab. Quindi, ricreare la macchina virtuale nel lab. 

![VM in stato danneggiato](./media/troubleshoot-vm-environment-creation-failures/vm-corrupted-state.png)



## <a name="activity-logs"></a>Log attività
Esaminare i log attività se si sta analizzando un errore qualche tempo dopo aver tentato la creazione della macchina virtuale o dell'ambiente. In questa sezione viene illustrato come trovare i log per le macchine virtuali e gli ambienti.

## <a name="activity-logs-for-virtual-machines"></a>Registri attività per le macchine virtuali

1. Nella home page del lab selezionare la macchina virtuale per avviare la pagina **Macchina virtuale.**
2. Nella sezione **MONITORING** del menu a sinistra della pagina **Macchina virtuale** selezionare **Log attività** per visualizzare tutti i log associati alla macchina virtuale.
3. Nel log attività degli elementi selezionare l'operazione non riuscita. In genere, l'operazione non riuscita viene chiamata `Write Virtualmachines`.
4. Nel riquadro destro passare alla scheda JSON. Vengono visualizzati i dettagli nella visualizzazione JSON del log.

    ![Log attività per una macchina virtuale](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. Esaminare il log JSON `statusMessage` fino a trovare la proprietà. Fornisce il messaggio di errore principale e ulteriori informazioni dettagliate, se applicabile. Il codice JSON seguente è un esempio per l'errore principale citato superato visualizzato in precedenza in questo articolo.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>Registro attività per un ambiente

Per visualizzare il registro attività per la creazione di un ambiente, attenersi alla seguente procedura:

1. Nella home page del lab selezionare **Configurazione e criteri** nel menu a sinistra.
2. nella pagina **Configurazione e criteri** selezionare Log **attività** dal menu.
3. Cercare l'errore nell'elenco delle attività nel registro e selezionarlo.
4. Nel riquadro destro passare alla scheda JSON e cercare **statusMessage**.

    ![Registro attività ambiente](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Log di distribuzione dei modelli di Resource ManagerResource Manager template deployment logs
Se l'ambiente o la macchina virtuale è stata creata tramite l'automazione, è possibile cercare le informazioni sull'errore in un'ultima posizione. Questo è il log di distribuzione del modello di Azure Resource Manager.This's the Azure Resource Manager template deployment log. Quando una risorsa lab viene creata tramite l'automazione, viene spesso eseguita tramite una distribuzione di modelli di Azure Resource Manager.When a lab resource is created through automation, it's often done through an Azure Resource Manager template deployment. Vedere[https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) per i modelli di Azure Resource Manager di esempio che creano risorse DevTest Labs.See for sample Azure Resource Manager templates that create DevTest Labs resources.

Per visualizzare i log di distribuzione del modello lab, attenersi alla seguente procedura:

1. Avviare la pagina per il gruppo di risorse in cui è presente il lab.
2. Selezionare **Distribuzioni** nel menu a sinistra in **Impostazioni**.
3. Cercare le distribuzioni con stato non riuscito e selezionarlo.
4. Nella pagina **Distribuzione** selezionare Il collegamento **Dettagli operazione** per l'operazione non riuscita.
5. Vengono visualizzati i dettagli sull'operazione non riuscita nella finestra **Dettagli operazione.**

## <a name="next-steps"></a>Passaggi successivi
Vedere Risoluzione dei problemi relativi agli [errori degli elementiSee Troubleshooting artifact failures](devtest-lab-troubleshoot-artifact-failure.md)
