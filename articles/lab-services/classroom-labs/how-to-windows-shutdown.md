---
title: Guida al controllo del comportamento di arresto di Windows in Azure Lab Services | Microsoft Docs
description: Viene descritta la procedura per arrestare automaticamente una macchina virtuale di Windows inattiva e rimuovere il comando di arresto di Windows.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.topic: article
ms.date: 3/30/2020
ms.author: spelluru
ms.openlocfilehash: 7b839df5940ab26e5c1a99a1bda1fbd2545f8cc4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81113128"
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

## <a name="automatic-rdp-disconnect-and-shutdown-for-idle-vm"></a>Disconnessione e arresto RDP automatici per la macchina virtuale inattiva

Windows fornisce le impostazioni **criteri di gruppo locali** che è possibile usare per impostare un limite di tempo per disconnettere automaticamente una sessione RDP quando diventa inattiva.  Una sessione viene determinata come inattiva quando *non* è presente alcun input di mouse\keyboard.  Tutte le attività a esecuzione prolungata che non coinvolgono input mouse\keyboard comportano lo stato di inattività della macchina virtuale.  Questa operazione include l'esecuzione di una query estesa, lo streaming di video, la compilazione e così via.  A seconda delle esigenze della classe, è possibile scegliere di impostare il limite di tempo di inattività in modo che sia sufficientemente lungo da gestire questi tipi di attività.  Se necessario, ad esempio, è possibile impostare il limite di tempo di inattività su 1 o più ore.

Di seguito è illustrata l'esperienza dello studente quando si configura il **limite della sessione inattiva** in combinazione con l'impostazione [**arresto automatico in caso di disconnessione**](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect) :
 1. Lo studente si connette alla propria macchina virtuale Windows tramite RDP.
 2. Quando lo studente lascia aperta la finestra RDP e la macchina virtuale è inattiva per il **limite di sessioni inattive** specificato (ad esempio 5 minuti), lo studente visualizzerà la finestra di dialogo seguente:

    ![Finestra di dialogo Limite tempo di inattività scaduto](../media/how-to-windows-shutdown/idle-time-expired.png)

1. Se lo *studente non fa clic su* **OK**, la sessione RDP verrà disconnessa automaticamente dopo 2 minuti.
2. Dopo la disconnessione della sessione RDP, una volta raggiunto l'intervallo di tempo specificato per l'impostazione di **arresto automatico in caso di disconnessione** , la macchina virtuale viene arrestata automaticamente Azure Lab Services.

### <a name="set-rdp-idle-session-time-limit-on-the-template-vm"></a>Impostare il limite di tempo della sessione inattiva RDP per la macchina virtuale modello

Per impostare il limite di tempo di inattività della sessione RDP, è possibile connettersi alla macchina virtuale del modello ed eseguire lo script di PowerShell seguente.

```powershell
# The MaxIdleTime is in milliseconds; by default, this script sets MaxIdleTime to 15 minutes.
$maxIdleTime = 15 * 60 * 1000

Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" -Name "MaxIdleTime" -Value $maxIdleTime -Force
```
In alternativa, è possibile scegliere di seguire questi passaggi manuali usando la macchina virtuale modello:

1. Premere il tasto Windows, digitare **gpedit**, quindi selezionare **modifica criteri di gruppo (pannello di controllo)**.

1. Passare a **Configurazione Computer > Modelli amministrativi > componenti Windows > Servizi Desktop remoto**> host sessione Desktop remoto limiti di tempo della sessione.  

    ![Editor criteri di gruppo locali](../media/how-to-windows-shutdown/group-policy-idle.png)
   
1. Fare clic con il pulsante destro del mouse su **Imposta limite di tempo per le sessioni attive ma inattive Servizi Desktop remoto**, quindi scegliere **modifica**.

1. Immetti le impostazioni seguenti e quindi fai clic su **OK**:
   1. Seleziona **Attivata**.
   1. In **Opzioni**specificare il **limite della sessione inattiva**.

    ![Limite sessione inattiva](../media/how-to-windows-shutdown/edit-idle-time-limit.png)

1. Infine, per combinare questo comportamento con l'impostazione **Spegni automaticamente durante la disconnessione** , è necessario seguire la procedura descritta nell'articolo procedura: [abilitare l'arresto automatico delle macchine virtuali alla disconnessione](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect).

> [!WARNING]
> Dopo aver configurato questa impostazione usando PowerShell per modificare direttamente l'impostazione del registro di sistema o manualmente usando l'editor di Criteri di gruppo, è necessario riavviare la macchina virtuale per rendere effettive le impostazioni.  Inoltre, se si configura l'impostazione utilizzando il registro di sistema, l'editor di Criteri di gruppo non sempre viene aggiornato per riflettere le modifiche apportate all'impostazione del registro di sistema. Tuttavia, l'impostazione del registro di sistema diventa effettiva come previsto e la sessione RDP viene disconnessa quando è inattiva per il periodo di tempo specificato.

## <a name="remove-windows-shutdown-command-from-start-menu"></a>Rimuovere il comando Shutdown di Windows dal menu Start

Le impostazioni **criteri di gruppo locali** di Windows consentono inoltre di rimuovere il comando shutdown dal menu **Start** .

Per rimuovere il comando Shutdown, è possibile connettersi alla macchina virtuale del modello ed eseguire lo script di PowerShell seguente.

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

In alternativa, è possibile scegliere di seguire questi passaggi manuali usando la macchina virtuale modello:

1. Premere il tasto Windows, digitare **gpedit**, quindi selezionare **modifica criteri di gruppo (pannello di controllo)**.

1. Passare a **Configurazione Computer > Modelli amministrativi > menu Start e la barra delle applicazioni**.  

    ![Editor criteri di gruppo locali](../media/how-to-windows-shutdown/group-policy-shutdown.png)

1. Fare clic con il pulsante destro del mouse su **Rimuovi e impedire l'accesso ai comandi arresta, riavvia, Sospendi e Iberna**, quindi fare clic su **modifica**.

1. Selezionare l'impostazione **Enabled** , quindi fare clic su **OK**:
 
   ![Impostazione di chiusura](../media/how-to-windows-shutdown/edit-shutdown.png)

1. Si noti che il comando shutdown non viene più visualizzato nel menu **Start** di Windows. viene visualizzato solo il comando **Disconnetti** .

    ![Comando Shutdown](../media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo su come preparare una VM modello di Windows: [Guida alla configurazione di un computer modello Windows in Azure Lab Services](how-to-prepare-windows-template.md)