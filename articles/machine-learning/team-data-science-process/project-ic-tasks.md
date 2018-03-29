---
title: Attività del processo di data science per i team di un singolo collaboratore - Azure | Microsoft Docs
description: Descrizione delle attività di un singolo collaboratore in un progetto di data science in team.
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: bradsev
ms.openlocfilehash: af60b188fe8db278297e1ece400bf4398e36094a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="individual-contributor-tasks"></a>Attività del singolo collaboratore

Questo argomento illustra le attività che un collaboratore singolo deve completare per il proprio team di data science. L'obiettivo è di stabilire un ambiente di collaborazione per normalizzare il [processo di data science per il team](overview.md) (TDSP, Team Data Science Process). Per una descrizione dei ruoli del personale e delle relative attività associate gestiti da un team di data science, che vengono standardizzati con il processo, vedere [Team Data Science Process roles and tasks](roles-tasks.md) (Ruoli e attività del processo di data science per i team).

Le attività dei singoli collaboratori al progetto (data scientist) per configurare l'ambiente TDSP per il progetto sono illustrate di seguito: 

![1](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **GroupUtilities** è il repository gestito dal gruppo per condividere con l'intero gruppo utilità vantaggiose. 
- **TeamUtilities** è il repository gestito dal team per il team. 

Per istruzioni su come eseguire un progetto di data science con TDSP, vedere [Execution of Data Science Projects](project-execution.md) (Esecuzione di progetti di data science). 

>[AZURE.NOTE] Nelle istruzioni seguenti vengono illustrati i passaggi necessari per configurare un ambiente per il team TDSP usando Visual Studio Team Services (VSTS). Viene specificato come eseguire queste attività con VSTS perché questa è la modalità di implementazione di TDSP in Microsoft. Se si usa un'altra piattaforma di hosting del codice per il proprio gruppo, le attività che il responsabile del team deve completare di per sé non cambiano, Sarà tuttavia diverso il modo in cui queste attività vengono completate.


## <a name="repositories-and-directories"></a>Repository e directory

Questa esercitazione usa nomi abbreviati per i repository e le directory. Questi nomi consentono di seguire più facilmente le operazioni tra i repository e le directory. Questa notazione (**R** per i repository Git e **D** per le directory locali nella DSVM) viene usata nelle sezioni seguenti:

- **R2**: il repository GroupUtilities in Git che il responsabile del gruppo ha configurato nel server di gruppo VSTS.
- **R4**: il repository TeamUtilities in Git che il responsabile del team ha configurato.
- **R5**: il repository Project in Git che è stato configurato dal responsabile del progetto.
- **D2**: la directory locale clonata da R2.
- **D4**: la directory locale clonata da R4.
- **D5**: la directory locale clonata da R5.


## <a name="step-0-prerequisites"></a>Passaggio 0: Prerequisiti

I prerequisiti vengono soddisfatti completando le attività assegnate al responsabile del gruppo delineate in [Attività del responsabile del gruppo per un team di data science](group-manager-tasks.md). Di seguito vengono riepilogati i prerequisiti che è necessario soddisfare prima di iniziare le attività del responsabile del team: 
- Il responsabile del gruppo ha configurato il repository **GroupUtilities** (se presente). 
- Il responsabile del team ha configurato il repository **TeamUtilities** (se presente).
- Il responsabile del progetto ha configurato il repository Project. 
- Si è stati aggiunti al repository Project dal responsabile del progetto con il privilegio di eseguire la clonazione e il push nel repository Project.

Il secondo prerequisito, relativo al repository **TeamUtilities**, è facoltativo, a seconda che il team abbia un repository di utilità specifico del team. Se uno degli altri tre prerequisiti non è stato completato, contattare il responsabile del team, il responsabile del progetto o i delegati per configurarlo seguendo le istruzioni contenute in [Team Lead tasks for a data science team](team-lead-tasks.md) (Attività del responsabile del team per un team di data science) o in [Attività del responsabile di progetto per un team di data science](project-lead-tasks.md).

- Git deve essere installato nel computer. Se si usa una macchina virtuale di data science (DSVM, Data Science Virtual Machine), Git è stato preinstallato ed è possibile iniziare. In caso contrario, vedere l'[appendice Strumenti e piattaforme](platforms-and-tools.md#appendix).  
- Se si usa una **DSVM per Windows**, è necessario avere installato [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) nel computer. Nel file README.md scorrere fino alla sezione **Download and Install** (Download e installazione) e fare clic su *latest installer* (programma di installazione più recente). Si passerà alla pagina del programma di installazione più recente. Scaricare da qui il file con estensione exe del programma di installazione ed eseguirlo. 
- Se si usa una **DSVM per Linux**, creare una chiave pubblica SSH nella DSVM e aggiungerla al server VSTS del gruppo. Per altre informazioni sul protocollo SSH, vedere la sezione **Creare una chiave pubblica SSH** nell'[appendice Strumenti e piattaforme](platforms-and-tools.md#appendix). 
- Se il responsabile del team e/o del progetto ha creato una risorsa di archiviazione file di Azure che è necessario montare nella DSVM, è consigliabile chiedergli informazioni in proposito. 

## <a name="step-1-3-clone-group-team-and-project-repositories-to-local-machine"></a>Passaggi 1-3: Clonare i repository del gruppo, del team e del progetto nel computer locale

Questa sezione contiene le istruzioni per completare le prime tre attività dei singoli collaboratori al progetto: 

- Clonare il repository **GroupUtilities** R2 in D2
- Clonare il repository **TeamUtilities** R4 in D4 
- Clonare il repository **Project** R5 in D5.

Nel computer locale creare una directory ***C:\GitRepos*** (per Windows) o ***$home/GitRepos*** (per Linux) e quindi passare a tale directory. 

Eseguire uno dei comandi seguenti (a seconda del sistema operativo) per clonare i repository **GroupUtilities**, **TeamUtilities** e **Project** nelle directory sul computer locale: 

**Windows**
    
    git clone <the URL of the GroupUtilities repository>
    git clone <the URL of the TeamUtilities repository>
    git clone <the URL of the Project repository>
    
![2](./media/project-ic-tasks/project-ic-2-clone-three-repo-to-ic.png)

Assicurarsi che le tre cartelle siano visibili nella directory del progetto.

![3](./media/project-ic-tasks/project-ic-3-three-repo-cloned-to-ic.png)

**Linux**
    
    git clone <the SSH URL of the GroupUtilities repository>
    git clone <the SSH URL of the TeamUtilities repository>
    git clone <the SSH URL of the Project repository>

![4](./media/project-ic-tasks/project-ic-4-clone-three-repo-to_ic-linux.png)

Assicurarsi che le tre cartelle siano visibili nella directory del progetto.

![5](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="step-4-5-mount-azure-file-storage-to-your-dsvm-optional"></a>Passaggi 4-5: Montare la risorsa di archiviazione file di Azure nella DSVM (facoltativo)

Per montare la risorsa di archiviazione file di Azure nella DSVM, vedere le istruzioni della sezione 4 di [Team lead tasks for a data science team (Attività del responsabile del team per un team di data science)](team-lead-tasks.md)

## <a name="next-steps"></a>Passaggi successivi

Ecco i collegamenti alle descrizioni più dettagliate dei ruoli e delle attività definite dal processo di data science per i team:

- [Attività del responsabile del gruppo per un team di data science](group-manager-tasks.md)
- [Team Lead tasks for a data science team (Attività del responsabile del team per un team di data science)](team-lead-tasks.md)
- [Attività del responsabile di progetto per un team di data science](project-lead-tasks.md)
- [Singoli collaboratori al progetto per un team di data science](project-ic-tasks.md)

