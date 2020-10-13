---
title: Guida al controllo del comportamento di arresto di Windows in Azure Lab Services | Microsoft Docs
description: Viene descritta la procedura per arrestare automaticamente una macchina virtuale di Windows inattiva e rimuovere il comando di arresto di Windows.
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: c6021131787dde4fe23ec4caad107bda2e20158a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541561"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>Guida al controllo del comportamento di arresto di Windows

Azure Lab Services offre diversi controlli dei costi per garantire che le macchine virtuali (VM) Windows non siano in esecuzione imprevista:
 - [Impostare una pianificazione](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
 - [Impostare quote per gli utenti](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)
 - [Abilitare l'arresto automatico alla disconnessione](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

Anche con questi controlli dei costi, in alcune situazioni è possibile che una macchina virtuale Windows continui a funzionare; e, di conseguenza, dedurre dalla quota dello studente:

- **La finestra RDP è aperta**
  
    Quando uno studente si connette alla propria macchina virtuale tramite RDP, può inavvertitamente lasciare aperta la finestra RDP.  Fino a quando la finestra RDP rimane aperta, l' **arresto automatico in seguito all'impostazione di disconnessione** non avrà mai effetto perché viene attivato solo dopo la disconnessione della sessione RDP.

- **Il comando Shutdown di Windows viene usato per spegnere la macchina virtuale**
  
    Uno studente può usare il comando Shutdown di Windows o altri meccanismi di arresto disponibili in Windows per disattivare la VM invece di usare [Azure Lab Services pulsante Interrompi](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-classroom-lab#start-or-stop-the-vm).  Quando ciò accade, dal punto di vista del Azure Lab Services, la macchina virtuale è ancora in uso.
    
Per evitare che si verifichino queste situazioni, in questa guida vengono descritte le procedure per arrestare automaticamente una macchina virtuale inattiva di Windows e rimuovere il comando di arresto di Windows dal menu **Start** .  

> [!NOTE]
> Una macchina virtuale può anche dedurre in modo imprevisto dalla quota quando lo studente avvia la macchina virtuale, ma non si connette mai con RDP.  Questa guida attualmente *non* risolve questo scenario.  Gli studenti devono invece ricordare di connettersi immediatamente alla macchina virtuale tramite RDP dopo l'avvio; in alternativa, è necessario arrestare la macchina virtuale.

## <a name="remove-windows-shutdown-command-from-start-menu"></a>Rimuovere il comando Shutdown di Windows dal menu Start

Le impostazioni **criteri di gruppo locali** di Windows consentono inoltre di rimuovere il comando shutdown dal menu **Start** .

Per rimuovere il comando Shutdown, è possibile connettersi alla macchina virtuale del modello ed eseguire lo script di PowerShell seguente.

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

In alternativa, è possibile scegliere di seguire questi passaggi manuali usando la macchina virtuale modello:

1. Premere il tasto Windows, digitare **gpedit**, quindi selezionare **modifica criteri di gruppo (pannello di controllo)**.

1. Passare a **Configurazione Computer > Modelli amministrativi > menu Start e la barra delle applicazioni**.  

    ![Editor criteri di gruppo locali](./media/how-to-windows-shutdown/group-policy-shutdown.png)

1. Fare clic con il pulsante destro del mouse su **Rimuovi e impedire l'accesso ai comandi arresta, riavvia, Sospendi e Iberna**, quindi fare clic su **modifica**.

1. Selezionare l'impostazione **Enabled** , quindi fare clic su **OK**:
 
   ![Impostazione di chiusura](./media/how-to-windows-shutdown/edit-shutdown.png)

1. Si noti che il comando shutdown non viene più visualizzato nel menu **Start** di Windows. viene visualizzato solo il comando **Disconnetti** .

    ![Comando Shutdown](./media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo su come preparare una VM modello di Windows: [Guida alla configurazione di un computer modello Windows in Azure Lab Services](how-to-prepare-windows-template.md)
