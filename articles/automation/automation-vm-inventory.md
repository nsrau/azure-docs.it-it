---
title: Gestire la raccolta di inventario di Automazione di Azure dalle macchine virtuali | Microsoft Docs
description: Questo articolo descrive come gestire la raccolta di inventario dalle macchine virtuali.
services: automation
ms.subservice: change-inventory-management
keywords: inventario, automazione, modifica, gestione
ms.date: 06/30/2020
ms.topic: conceptual
ms.openlocfilehash: 32d3c17a5f3d152f32b19ffbfd5c9793a7a34b80
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185722"
---
# <a name="manage-inventory-collection-from-vms"></a>Gestire la raccolta dell'inventario dalle VM

È possibile abilitare il monitoraggio dell'inventario per una macchina virtuale di Azure dalla pagina delle risorse della macchina virtuale. È possibile raccogliere e visualizzare le informazioni di inventario seguenti nei computer:

- Aggiornamenti di Windows, applicazioni Windows, servizi, file e chiavi del registro di sistema
- Pacchetti software Linux, daemon e file

Rilevamento modifiche e inventario di Automazione di Azure offre un'interfaccia utente basata sul browser per la configurazione della raccolta dell'inventario.

## <a name="before-you-begin"></a>Prima di iniziare

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/).

Questo articolo presuppone che si disponga di una macchina virtuale da abilitare con Rilevamento modifiche e inventario. Se non si dispone di una macchina virtuale di Azure, è possibile [crearne una](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-vm-resource-page"></a>Abilitare la raccolta dell'inventario dalla pagina delle risorse della macchina virtuale

1. Nel riquadro sinistro del portale di Azure selezionare **Macchine virtuali**.
2. Nell'elenco delle macchine virtuali selezionare una macchina virtuale.
3. In **Operazioni** scegliere **Inventario** dal menu **Risorsa**.
4. Selezionare un'area di lavoro Log Analytics per archiviare i log di dati.
    Se non sono disponibili aree di lavoro per l'area, viene chiesto se creare un'area di lavoro e un account di automazione predefiniti.
5. Per avviare l'abilitazione del computer, selezionare **Abilita**.

   ![Visualizzare le opzioni di onboarding](./media/automation-vm-inventory/inventory-onboarding-options.png)

    Una barra di stato informa che è in corso l'abilitazione della funzionalità Rilevamento modifiche e inventario. Il processo potrebbe richiedere fino a 15 minuti, durante i quali è possibile chiudere la finestra oppure tenerla aperta per visualizzare una notifica quando la funzionalità viene abilitata. È possibile monitorare lo stato della distribuzione dal riquadro delle notifiche.

   ![Visualizzare l'inventario](./media/automation-vm-inventory/inventory-onboarded.png)

Al termine della distribuzione, la barra di stato scompare. Il sistema sta ancora raccogliendo i dati di inventario, che quindi potrebbero non essere ancora visibili. La raccolta completa dei dati può richiedere 24 ore.

## <a name="configure-your-inventory-settings"></a>Configurare le impostazioni di inventario

Per impostazione predefinita, sono configurati per la raccolta il software, i servizi di Windows e i daemon Linux. Per raccogliere i dati di inventario dei file e del Registro di sistema di Windows, configurare le impostazioni della raccolta dell'inventario.

1. Nella pagina Inventario fare clic su **Modifica impostazioni** nella parte superiore.
2. Per aggiungere una nuova impostazione della raccolta, passare alla categoria delle impostazioni che si vuole aggiungere selezionando le schede **Registro di sistema di Windows**, **File Windows** o **File Linux**.
3. Selezionare la categoria appropriata e fare clic su **Aggiungi** nella parte superiore della pagina.

Le sezioni seguenti forniscono informazioni sulle proprietà che possono essere configurate per le diverse categorie.

### <a name="windows-registry"></a>Registro di sistema di Windows

|Proprietà  |Descrizione  |
|---------|---------|
|Attivato     | Determina se l'impostazione viene applicata        |
|Nome elemento     | Nome descrittivo del file da rilevare        |
|Gruppo     | Nome del gruppo per il raggruppamento logico dei file        |
|Chiave del Registro di sistema di Windows   | Percorso in cui cercare il file, ad esempio: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

### <a name="windows-files"></a>File Windows

|Proprietà  |Descrizione  |
|---------|---------|
|Attivato     | True se viene applicata l'impostazione e False in caso contrario.        |
|Nome elemento     | Il nome descrittivo del file da rilevare.        |
|Gruppo     | Nome del gruppo per il raggruppamento logico dei file.       |
|Immettere il percorso     | Percorso in cui cercare il file, ad esempio **c:\temp\myfile.txt**.

### <a name="linux-files"></a>File di Linux

|Proprietà  |Descrizione  |
|---------|---------|
|Attivato     | True se viene applicata l'impostazione e False in caso contrario.        |
|Nome elemento     | Il nome descrittivo del file da rilevare.        |
|Gruppo     | Nome del gruppo per il raggruppamento logico dei file.        |
|Immettere il percorso     | Percorso in cui cercare il file, ad esempio **/etc/*.conf**.       |
|Tipo di percorso     | Tipo di elemento da registrare. I valori sono File e Directory.        |
|Ricorsione     | True se viene usata la ricorsione per la ricerca dell'elemento da rilevare, False in caso contrario.        |
|Usare sudo     | True se si utilizza sudo quando si cerca l'elemento e False in caso contrario.         |
|Collegamenti     | Valore che determina come vengono gestiti i collegamenti simbolici durante l'attraversamento delle directory. I valori possibili sono: <br> Ignora: ignora i collegamenti simbolici e non include i file e le directory a cui viene fatto riferimento<br>Segui: segue i collegamenti simbolici durante la ricorsione e include anche i file e le directory a cui viene fatto riferimento<br>Gestisci: segue i collegamenti simbolici e consente la modifica del trattamento del contenuto restituito      |

## <a name="manage-machine-groups"></a>Gestire i gruppi di computer

L'inventario consente di creare e visualizzare gruppi di computer nei log di Monitoraggio di Azure. I gruppi di computer sono raccolte di computer definite da una query nei log di Monitoraggio di Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Per visualizzare i gruppi di computer, selezionare la scheda **Gruppi di computer** nella pagina Inventario.

![Visualizzare i gruppi di computer nella pagina Inventario](./media/automation-vm-inventory/inventory-machine-groups.png)

Selezionando un gruppo di computer nell'elenco, si apre la pagina Gruppi di computer. Questa pagina mostra i dettagli del gruppo di computer. Questi dettagli includono la query del log di monitoraggio di Azure usata per definire il gruppo. Nella parte inferiore della pagina è presente un elenco su più pagine dei computer che fanno parte di tale gruppo.

![Visualizzare la pagina del gruppo di computer](./media/automation-vm-inventory/machine-group-page.png)

Fare clic su **+ Clona** per clonare il gruppo di computer. È necessario assegnare al gruppo un nuovo nome e un alias. In questa fase è possibile modificare la definizione. Dopo aver modificato la query, fare clic su **Convalida query** per visualizzare in anteprima i computer che verranno selezionati. Quando si è ottenuto il gruppo desiderato, fare clic su **Crea** per creare il gruppo di computer.

Per creare un nuovo gruppo di computer, fare clic su **+ Crea un gruppo di computer**. Questo pulsante apre la pagina **Crea un gruppo di computer** in cui è possibile definire il nuovo gruppo. Fare clic su **Crea** per creare il gruppo.

![Creare il nuovo gruppo di computer](./media/automation-vm-inventory/create-new-group.png)

## <a name="disconnect-your-vm-from-management"></a>Disconnettere la macchina virtuale dalla gestione

Per rimuovere la macchina virtuale da Rilevamento modifiche e inventario:

1. Nel riquadro sinistro del portale di Azure selezionare **Log Analytics** e quindi selezionare l'area di lavoro usata durante l'abilitazione della macchina virtuale per Rilevamento modifiche e inventario.
2. Nella pagina **log Analytics** aprire il menu **delle risorse** .
3. Selezionare **Macchine virtuali** in **Origini dati dell'area di lavoro**.
4. Nell'elenco selezionare la macchina virtuale da disconnettere. La macchina avrà un segno di spunta verde accanto alla voce **Questa area di lavoro** nella colonna **Connessione OMS**.

   >[!NOTE]
   >Operations Management Suite (OMS) è ora indicato come log di Monitoraggio di Azure.

5. Nella parte superiore della pagina successiva selezionare **Disconnetti**.
6. Nella finestra di conferma fare clic su **Sì** per disconnettere la macchina virtuale dalla gestione.

>[!NOTE]
>Le macchine virtuali vengono comunque visualizzate dopo aver annullato la registrazione, perché si segnalano tutti i computer in inventario nelle ultime 24 ore. Dopo la disconnessione del computer, è necessario attendere 24 ore prima che non vengano più elencate.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni dettagliate sull'uso della funzionalità, vedere [Gestire Rilevamento modifiche e inventario](change-tracking-file-contents.md).
* Per altre informazioni sul rilevamento delle modifiche software, vedere [Rilevare le modifiche software nell'ambiente in uso con Rilevamento modifiche](./change-tracking.md).
* Per risolvere i problemi generali relativi alla funzionalità, vedere [Risolvere i problemi relativi a Rilevamento modifiche e inventario](troubleshoot/change-tracking.md).
