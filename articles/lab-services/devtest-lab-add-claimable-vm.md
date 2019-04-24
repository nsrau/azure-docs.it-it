---
title: Creare e gestire macchine virtuali richiedibili in un lab di Azure DevTest Labs | Microsoft Docs
description: Informazioni su come aggiungere una macchina virtuale a disposizione degli utenti in un lab in Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: f671e66e-9630-4e30-a131-a6bad9ed9c11
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: spelluru
ms.openlocfilehash: fdffa3862f45b99c2c3f2ed41934e09247808ca7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60311837"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Creare e gestire macchine virtuali richiedibili in Azure DevTest Labs
Per aggiungere una macchina virtuale a disposizione degli utenti a un lab è necessario seguire una procedura simile all'[aggiunta di una macchina virtuale standard](devtest-lab-add-vm.md), partendo da una *base* che può essere un'[immagine personalizzata](devtest-lab-create-template.md), una [formula](devtest-lab-manage-formulas.md) o un'[immagine del Marketplace](devtest-lab-configure-marketplace-images.md). In questa esercitazione viene descritto come usare il portale di Azure per aggiungere una macchina virtuale richiedibile a un lab in DevTest Labs e viene illustrato il processo che un utente deve seguire per richiedere e annullare la richiesta della macchina virtuale.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Procedura per aggiungere una macchina virtuale a disposizione degli utenti in un lab in Azure DevTest Labs
1. Accedere al [portale di Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nella sezione **DEVOPS**. Selezionare * (stella) accanto a **DevTest Labs** nella sezione **DEVOPS**. Questa azione aggiunge **DevTest Labs** al menu di spostamento a sinistra in modo che sia facilmente accessibile la volta successiva. Selezionare quindi **DevTest Labs** nel menu di spostamento a sinistra.

    ![Tutti i servizi - Selezionare DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
1. Nell'elenco di lab selezionare il lab in cui si vuole creare la nuova VM.
2. Nella pagina **Panoramica** del lab selezionare **+ Aggiungi**.

    ![Pulsante Aggiungi VM](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. Nella pagina **Scegli una base** selezionare un'immagine del marketplace per la macchina virtuale.
1. Nella scheda **Impostazioni di base** della pagina **Macchina virtuale** eseguire queste operazioni:
    1. Nella casella di testo **Nome macchina virtuale** immettere un nome per la macchina virtuale. La casella di testo viene precompilata con un nome univoco generato automaticamente, costituito dal nome utente incluso nell'indirizzo di posta elettronica seguito da un numero univoco a tre cifre. Ciò consente di evitare di perdere tempo a definire un nome per la macchina virtuale e digitarlo ogni volta che se ne crea una. Se si vuole, è possibile sostituire il nome compilato automaticamente con un nome di propria scelta. Per eseguire questa operazione, immettere un nome nella casella di testo **Nome macchina virtuale**.
    2. Immettere un **Nome utente** a cui vengono concessi privilegi di amministratore nella macchina virtuale. Il **nome utente** per la macchina viene precompilato con un nome univoco generato automaticamente, costituito dal nome utente incluso nell'indirizzo di posta elettronica. Ciò consente di evitare di perdere tempo a definire un nome utente ogni volta che si crea una nuova macchina. Anche in questo caso, se si vuole, è possibile sostituire il nome utente compilato automaticamente con un nome di propria scelta. Per eseguire questa operazione, immettere un valore nella casella di testo **Nome utente**. A questo utente vengono concessi i privilegi di **amministratore** sulla macchina virtuale.
    3. Se si sta creando la prima macchina virtuale nel lab, immettere una **password** per l'utente. Per salvare questa password come predefinita nell'insieme di credenziali delle chiavi di Azure associato al lab, selezionare **Salva come password predefinita**. La password predefinita viene salvata nell'insieme di credenziali delle chiavi con il nome: **VmPassword**. Quando si prova a creare altre macchine virtuali nel lab, **VmPassword** viene selezionata automaticamente come **password**. Per sostituire il valore, deselezionare la casella di controllo **Usa un segreto salvato** e immettere una password.

        È anche possibile salvare prima un segreto nell'insieme di credenziali delle chiavi e quindi usarlo per creare una macchina virtuale nel lab. Per altre informazioni, vedere [Archiviare segreti in un insieme di credenziali delle chiavi](devtest-lab-store-secrets-in-key-vault.md). Per usare la password archiviata nell'insieme di credenziali delle chiavi, selezionare **Usa un segreto salvato** e specificare un valore di chiave corrispondente al segreto (password).
    4. Nella sezione **Altre opzioni** selezionare **Modifica dimensioni**. Selezionare uno degli elementi predefiniti che specificano i core del processore, la dimensione della RAM e le dimensioni dell'unità disco rigido della macchina virtuale da creare.
    5. Selezionare **Aggiungi o rimuovi artefatti**. Selezionare e configurare gli artefatti da aggiungere all'immagine di base.
    **Nota:** Se non si ha familiarità con DevTest Labs o con la configurazione di elementi, vedere la sezione [Aggiungere un elemento esistente in una macchina virtuale](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) e tornare qui al termine dell'operazione.
2. Passare alla scheda **Impostazioni avanzate** nella parte superiore della pagina ed eseguire le operazioni seguenti:
    1. Per cambiare la rete virtuale in cui si trova la macchina virtuale, selezionare **Cambia rete virtuale**.
    2. Per cambiare la subnet, selezionare **Cambia subnet**.
    3. Specificare se l'indirizzo IP della macchina virtuale è **pubblico, privato o condiviso**.
    4. Per eliminare automaticamente la macchina virtuale, specificare **la data e l'ora di scadenza**.
    5. Per rendere la macchina virtuale a disposizione di un utente del lab, selezionare **Sì** per **Make this machine claimable** (Rendi attestabile questa macchina).
    6. Specificare il numero di **istanze della macchina virtuale** che si intende mettere a disposizione degli utenti del lab.
3. Selezionare **Crea** per aggiungere la macchina virtuale specificata al lab.

   Nella pagina del lab viene visualizzato lo stato di creazione della macchina virtuale, prima come **Creazione** e poi come **Esecuzione** dopo l'avvio della macchina virtuale.

> [!NOTE]
> Se si distribuiscono macchine virtuali lab tramite i  [modelli di Azure Resource Manager](devtest-lab-create-environment-from-arm.md), è possibile creare macchine virtuali a disposizione degli utenti impostando la proprietà **allowClaim** su true nella sezione delle proprietà.


## <a name="using-a-claimable-vm"></a>Usare una macchina virtuale a disposizione degli utenti

Un utente può richiedere qualsiasi macchina virtuale dall'elenco "Claimable virtual machines" (Macchine virtuali a disposizione degli utenti) effettuando una delle seguenti operazioni:

* Dall'elenco "Macchine virtuali richiedibili", nella parte inferiore del riquadro Panoramica del lab, fare clic con il pulsante destro del mouse su una delle macchine virtuali nell'elenco e scegliere **Richiedi macchina**.

  ![Richiedere una specifica macchina virtuale a disposizione degli utenti.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* Nella parte superiore del riquadro Panoramica scegliere **Richiedi qualsiasi**. Una macchina virtuale casuale viene assegnata dall'elenco di macchine virtuali a disposizione degli utenti.

  ![Richiedere una macchina virtuale a disposizione degli utenti.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


Quando una macchina virtuale viene richiesta da un utente, questa viene spostata in alto nell'elenco "My virtual machines" (Le mie macchine virtuali) e non è più disponibile per un altro utente.

## <a name="unclaim-a-vm"></a>Annullare la richiesta di una macchina virtuale

Quando un utente non ha più bisogno di una macchina virtuale richiesta e desidera renderla disponibile ad altri utenti, è possibile restituire la macchina virtuale richiesta all'elenco di macchine virtuali richiedibili effettuando una delle operazioni seguenti:

- Nell'elenco "Macchine virtuali personali" fare clic con il pulsante destro del mouse su una delle macchine virtuali nell'elenco, oppure selezionare i puntini di sospensione (...), e scegliere **Annulla richiesta**.

  ![Annullare la richiesta di una macchina virtuale nell'elenco di macchine virtuali.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- Nell'elenco "Macchine virtuali personali" selezionare una macchina virtuale per aprire il riquadro di gestione, quindi selezionare **Annulla richiesta** nella barra dei menu superiore.

  ![Annullare la richiesta di una macchina virtuale nel riquadro di gestione della macchina virtuale.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

Quando un utente annulla la richiesta di una macchina virtuale, non dispone più delle autorizzazioni per tale macchina virtuale del lab.

### <a name="transferring-the-data-disk"></a>Trasferimento del disco dati
Se una macchina virtuale richiedibile dispone di un disco dati collegato a questa e un utente ne annulla la richiesta, il disco dati rimane associato alla macchina virtuale. Quando un altro utente richiede quella macchina virtuale, richiede sia il disco dati che la macchina virtuale.

Si chiama "trasferimento del disco dati". Il disco dati viene quindi reso disponibile nell'elenco **My data disks** (Dischi dati personali) del nuovo utente e da qui può essere gestito.

![Annullare la richiesta di dischi dati.](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>Passaggi successivi
* Dopo la creazione, è possibile connettersi alla macchina virtuale selezionando **Connetti** nel riquadro di gestione.
* Esplorare la [raccolta dei modelli di avvio rapido di Azure Resource Manager in DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
