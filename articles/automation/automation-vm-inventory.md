---
title: Gestire una macchina virtuale di Azure con la raccolta dell'inventario | Microsoft Docs
description: Gestire una macchina virtuale di Azure con la raccolta dell'inventario
services: automation
ms.subservice: change-inventory-management
keywords: inventario, automazione, modifica, gestione
ms.date: 01/28/2020
ms.topic: conceptual
ms.openlocfilehash: 0627d2daa70c276535dc43b722e22e1d73b0c8d2
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617365"
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Gestire una macchina virtuale di Azure con la raccolta dell'inventario

È possibile abilitare il monitoraggio dell'inventario per una macchina virtuale di Azure dalla pagina delle risorse della macchina virtuale. È possibile raccogliere e visualizzare le seguenti informazioni di inventario nei computer:

- Software Windows (applicazioni Windows e aggiornamenti di Windows), servizi, file e chiavi del Registro di sistema
- Daemon (pacchetti) software Linux e file

Questo metodo offre un'interfaccia utente basata sul browser per la configurazione della raccolta dell'inventario.

## <a name="before-you-begin"></a>Prima di iniziare

Se non si dispone di una sottoscrizione di Azure, [creare un account gratuito.](https://azure.microsoft.com/free/)

Questo articolo presuppone che sia disponibile una macchina virtuale in cui configurare la soluzione. Se non si ha una macchina virtuale di Azure, [crearne una](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Abilitare la raccolta dell'inventario dalla pagina delle risorse della macchina virtuale

1. Nel riquadro sinistro del portale di Azure selezionare **Macchine virtuali**.
2. Nell'elenco delle macchine virtuali selezionare una macchina virtuale.
3. In **Operazioni** scegliere **Inventario** dal menu **Risorsa**.
4. Selezionare un'area di lavoro di Log Analytics per archiviare i log dei dati.
    Se non sono disponibili aree di lavoro per l'area, viene chiesto se creare un'area di lavoro e un account di automazione predefiniti.
5. Per avviare il caricamento del computer, selezionare **Abilita**.

   ![Visualizzare le opzioni di onboarding](./media/automation-vm-inventory/inventory-onboarding-options.png)

    Una barra di stato notifica che si sta abilitando la soluzione. Il processo potrebbe richiedere fino a 15 minuti, durante i quali è possibile chiudere la finestra oppure tenerla aperta per visualizzare una notifica quando la soluzione viene abilitata. È possibile monitorare lo stato della distribuzione dal riquadro delle notifiche.

   ![Visualizzare la soluzione di inventario immediatamente dopo l'onboarding](./media/automation-vm-inventory/inventory-onboarded.png)

Al termine della distribuzione, la barra di stato scompare. Il sistema sta ancora raccogliendo i dati di inventario, che quindi potrebbero non essere ancora visibili. La raccolta completa dei dati può richiedere 24 ore.

## <a name="configure-your-inventory-settings"></a>Configurare le impostazioni di inventario

Per impostazione predefinita, sono configurati per la raccolta il software, i servizi di Windows e i daemon Linux. Per raccogliere i dati di inventario dei file e del Registro di sistema di Windows, configurare le impostazioni della raccolta dell'inventario.

1. Nella pagina Inventario fare clic su **Modifica impostazioni** nella parte superiore della pagina.
2. Per aggiungere una nuova impostazione di raccolta, passare alla categoria di impostazione che si desidera aggiungere selezionando la scheda **Registro di sistema**di Windows, File di **Windows**o **File Linux.**
3. Selezionare la categoria appropriata e fare clic su **Aggiungi** nella parte superiore della pagina.

Le tabelle seguenti forniscono informazioni sulle proprietà che possono essere configurate per le diverse categorie.

### <a name="windows-registry"></a>Registro di sistema di Windows

|Proprietà  |Descrizione  |
|---------|---------|
|Attivato     | Determina se l'impostazione viene applicata        |
|Nome elemento     | Nome descrittivo del file da rilevare        |
|Gruppo     | Nome del gruppo per il raggruppamento logico dei file        |
|Chiave del Registro di sistema di Windows   | Percorso in cui cercare il file, ad esempio "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

### <a name="windows-files"></a>File Windows

|Proprietà  |Descrizione  |
|---------|---------|
|Attivato     | True se l'impostazione viene applicata e False in caso contrario.        |
|Nome elemento     | Nome descrittivo del file di cui tenere traccia.        |
|Gruppo     | Nome del gruppo per il raggruppamento logico dei file.       |
|Immettere il percorso     | Il percorso in cui verificare la presenza del file, ad esempio **c:**

### <a name="linux-files"></a>File di Linux

|Proprietà  |Descrizione  |
|---------|---------|
|Attivato     | True se l'impostazione viene applicata e False in caso contrario.        |
|Nome elemento     | Nome descrittivo del file di cui tenere traccia.        |
|Gruppo     | Nome del gruppo per il raggruppamento logico dei file.        |
|Immettere il percorso     | Il percorso in cui verificare la presenza del file, ad esempio **/etc/**       |
|Tipo di percorso     | Tipo di elemento di cui tenere traccia. I valori sono File e Directory.        |
|Ricorsione     | True se la ricorsione viene utilizzata durante la ricerca dell'elemento da tenere traccia e False in caso contrario.        |
|Usa Sudo     | True se sudo viene utilizzato durante il controllo dell'elemento e False in caso contrario.         |
|Collegamenti     | Valore che indica come vengono trattati i collegamenti simbolici durante l'attraversamento delle directory. I valori possibili sono: <br> Ignora - Ignora i collegamenti simbolici e non include i file/directory a cui si fa riferimento<br>Segui : segue i collegamenti simbolici durante la ricorsione e include anche i file/directory a cui si fa riferimento<br>Gestisci: segue i collegamenti simbolici e consente la modifica del trattamento del contenuto restituito      |

## <a name="manage-machine-groups"></a>Gestire i gruppi di computer

Inventario consente di creare e visualizzare i gruppi di computer nei log di Monitoraggio di Azure.Inventory allows you to create and view machine groups in Azure Monitor logs. I gruppi di computer sono raccolte di computer definite da una query nei log di Monitoraggio di Azure.Machine groups are collections of machines defined by a query in Azure Monitor logs.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Per visualizzare i gruppi di computer, selezionare la scheda **Gruppi di computer** nella pagina Inventario.

![Visualizzare i gruppi di computer nella pagina Inventario](./media/automation-vm-inventory/inventory-machine-groups.png)

Selezionando un gruppo di computer nell'elenco, si apre la pagina Gruppi di computer. Questa pagina mostra i dettagli del gruppo di computer. Questi dettagli includono la query di Log Analytics usata per definire il gruppo. Nella parte inferiore della pagina è presente un elenco su più pagine dei computer che fanno parte di tale gruppo.

![Visualizzare la pagina del gruppo di computer](./media/automation-vm-inventory/machine-group-page.png)

Fare clic su **Clona** per clonare il gruppo di macchine. È necessario assegnare al gruppo un nuovo nome e un nuovo alias per il gruppo. In questa fase è possibile modificare la definizione. Dopo aver modificato la query, fare clic su **Convalida query** per visualizzare in anteprima i computer che verranno selezionati. Quando si è soddisfatti del gruppo, fare clic su **Crea** per creare il gruppo di macchine.

Se si desidera creare un nuovo gruppo di computer, fare clic su **Crea un gruppo**di computer . Questo pulsante consente di aprire la pagina Crea un gruppo di **computer,** in cui è possibile definire il nuovo gruppo. Fare clic su **Crea** per creare il gruppo.

![Creare il nuovo gruppo di computer](./media/automation-vm-inventory/create-new-group.png)

## <a name="disconnect-your-virtual-machine-from-management"></a>Disconnettere la macchina virtuale dalla gestione

Per rimuovere la macchina virtuale dalla gestione dell'inventario:

1. Nel riquadro sinistro del portale di Azure selezionare **Log Analytics** e quindi selezionare l'area di lavoro usata durante il caricamento della macchina virtuale.
2. Nella pagina Log Analytics aprire il menu **Risorsa.**
3. Selezionare **Macchine virtuali** in **Origini dati area di lavoro**.
4. Nell'elenco selezionare la macchina virtuale che si vuole disconnettere. La macchina virtuale avrà un segno di spunta verde accanto alla voce **Questa area di lavoro** nella colonna **Connessione OMS**.

   >[!NOTE]
   >Operations Management Suite (OMS) è ora indicato come log di Monitoraggio di Azure.Operations Management Suite (OMS) is now referred to as Azure Monitor logs.
   
5. Nella parte superiore della pagina successiva fare clic su **Disconnetti**.
6. Nella finestra di conferma, fare clic su **Sì** per disconnettere la macchina dalla gestione.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla gestione delle modifiche nelle impostazioni dei file e del Registro di sistema nelle macchine virtuali, vedere [Rilevare le modifiche software nell'ambiente in uso con la soluzione di rilevamento modifiche](../log-analytics/log-analytics-change-tracking.md).
* Per informazioni sulla gestione degli aggiornamenti di Windows e dei pacchetti nelle macchine virtuali, vedere [Soluzione Gestione aggiornamenti in Azure](../operations-management-suite/oms-solution-update-management.md).