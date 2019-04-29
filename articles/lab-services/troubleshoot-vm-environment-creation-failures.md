---
title: Risoluzione dei problemi di creazione della macchina virtuale e l'ambiente Azure DevTest Labs | Microsoft Docs
description: Informazioni su come risolvere i problemi di macchina virtuale (VM) e gli errori di creazione ambiente in Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.openlocfilehash: a653a785e99619c3e256613d6a4d2c7592f54c8c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848494"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Risolvere i problemi di macchina virtuale (VM) e gli errori di creazione ambiente in Azure DevTest Labs
DevTest Labs offre avvisi se un nome di computer non è valido o se sta tentando di violano un criterio del lab. In alcuni casi, noterete che rosso `X` accanto alla macchina virtuale o ambiente di stato che informa che si è verificato un lab.  Questo articolo fornisce alcuni consigli che è possibile usare per trovare il problema sottostante e, probabilmente, evitare il problema in futuro.

## <a name="portal-notifications"></a>Notifiche del portale
Se si usa il portale di Azure, il primo elemento da esaminare è il **Pannello di notifiche**.  Pannello di notifiche, disponibili sulla barra dei comandi principale facendo il **sull'icona a forma di campanello**, indicherà se la creazione della macchina virtuale o ambiente di laboratorio esito positivo o negativo.  Se si è verificato un errore, è visualizzato il messaggio di errore associato all'errore di creazione. I dettagli ulteriormente forniscono spesso informazioni che consentono di risolvere il problema. Nell'esempio seguente, la creazione della macchina virtuale non è riuscita a causa di esaurimento di core. Il messaggio dettagliato descrive come risolvere il problema e richiedere un aumento della quota di core.

![Notifiche del portale di Azure](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)


## <a name="activity-logs"></a>Log attività
Se si sta esaminando un errore di qualche minuto dopo aver tentato la creazione della macchina virtuale o ambiente, esaminare i log attività. Questa sezione illustra come trovare i log per le macchine virtuali e ambienti.

## <a name="activity-logs-for-virtual-machines"></a>Log attività per le macchine virtuali

1. Nella home page per l'ambiente lab, selezionare la macchina virtuale per avviare il **macchina virtuale** pagina.
2. Nel **macchina virtuale** nella pagina il **MONITORING** sezione del menu a sinistra, seleziona **log attività** per visualizzare tutti i registri associati alla macchina virtuale.
3. Voci di log attività, selezionare l'operazione non riuscita. In genere, viene chiamato l'operazione non riuscita `Write Virtualmachines`.
4. Nel riquadro destro passare alla scheda JSON. Vengono visualizzati i dettagli nella visualizzazione JSON del log.

    ![Log attività per una macchina virtuale](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. Esaminare i log JSON finché non si trova il `statusMessage` proprietà. Offre il messaggio di errore principale e altre informazioni dettagliate, se applicabile. Il codice JSON seguente è un errore di esempio per i core racchiusi tra virgolette superato visualizzato in precedenza in questo articolo.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at http://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>Log attività per un ambiente

Per visualizzare il log attività per la creazione di un ambiente, seguire questa procedura:

1. Nella home page per l'ambiente lab, selezionare **configurazione e criteri** nel menu a sinistra.
2. nel **configurazione e criteri** pagina, selezionare **i log attività** nel menu.
3. Individuare gli errori nell'elenco attività nel log e selezionarlo.
4. Nel riquadro destro passare alla scheda JSON e cercare il **messaggio di stato**.

    ![Log attività di ambiente](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Log di distribuzione di modelli di Resource Manager
Se l'ambiente o una macchina virtuale è stata creata tramite l'automazione, è un'unica posizione ultimo per individuare informazioni sull'errore. Ovvero il log di distribuzione modello Azure Resource Manager. Quando viene creata una risorsa lab tramite l'automazione, spesso avviene tramite la distribuzione di un modello di Azure Resource Manager. Visualizzare[ https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) per modelli di Azure Resource Manager di esempio che creano risorse di DevTest Labs.

Per visualizzare i log di distribuzione del modello lab, seguire questa procedura:

1. Avvio della pagina per il gruppo di risorse in cui si trova il lab.
2. Selezionare **distribuzioni** nel menu a sinistra sotto **impostazioni**.
3. Cercare le distribuzioni con stato non riuscito e selezionarlo.
4. Nel **Deployment** pagina, selezionare **i dettagli dell'operazione** collegamento per l'operazione non riuscita.
5. Vengono visualizzati dettagli sull'operazione non riuscita nel **dettagli dell'operazione** finestra.

## <a name="next-steps"></a>Passaggi successivi
Vedere [risoluzione dei problemi relativi agli elementi](devtest-lab-troubleshoot-artifact-failure.md)