---
title: Guida al controllo del comportamento di arresto di Windows in Azure Lab Services - Documenti Microsoft
description: Passaggi per arrestare automaticamente una macchina virtuale Windows inattiva e rimuovere il comando di arresto di Windows.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.topic: article
ms.date: 3/30/2020
ms.author: spelluru
ms.openlocfilehash: 39ff4f42457451dfa4aae90b281d6b163c56b4cd
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522238"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>Guida al controllo del comportamento di arresto di Windows

Servizi di laboratorio di Azure offre diversi controlli di costo per garantire che le macchine virtuali Windows (VM) non vengano eseguite in modo imprevisto:Azure Lab Services provides several cost controls to ensure that Windows virtual machines (VMs) are not running unexpectedly:
 - [Impostare una pianificazione](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
 - [Impostare quote per gli utenti](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)
 - [Abilitare l'arresto automatico alla disconnessione](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

Anche con questi controlli dei costi, esistono situazioni in cui una macchina virtuale Windows può continuare inaspettatamente l'esecuzione. e, di conseguenza, detrarre dalla quota dello studente:

- **La finestra RDP è rimasta aperta**
  
    Quando uno studente si connette alla propria macchina virtuale tramite RDP, potrebbe inavvertitamente lasciare aperta la finestra RDP.  Finché la finestra RDP rimane aperta, **l'impostazione di arresto automatico alla disconnessione** non avrà mai effetto poiché viene attivata solo dopo la disconnessione della sessione RDP.

- **Il comando di arresto di Windows viene usato per disattivare la macchina virtuale**
  
    Uno studente può usare il comando di arresto di Windows o altri meccanismi di arresto forniti in Windows per disattivare la macchina virtuale anziché il pulsante di arresto di [Servizi di Azure Lab.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-classroom-lab#start-or-stop-the-vm)  In questo caso, dal punto di vista di Azure Lab Services, la macchina virtuale è ancora in uso.
    
Per evitare che si verifichino queste situazioni, in questa guida vengono descritti i passaggi per arrestare automaticamente una macchina virtuale Windows inattiva e rimuovere il comando di arresto di Windows dal menu **Start.**  

> [!NOTE]
> Una macchina virtuale può anche detrarre in modo imprevisto dalla quota quando lo studente avvia la macchina virtuale, ma non si connette mai effettivamente a essa tramite RDP.  Questa guida *non* affronta attualmente questo scenario.  Al contrario, gli studenti devono essere ricordati di connettersi immediatamente alla propria macchina virtuale usando RDP dopo l'avvio. oppure, devono arrestare la macchina virtuale.

## <a name="automatic-rdp-disconnect-and-shutdown-for-idle-vm"></a>Disconnessione e arresto automatico DEL sistema RDP per la macchina virtuale inattiva

Windows fornisce le impostazioni di criteri di **gruppo locali** che è possibile utilizzare per impostare un limite di tempo per disconnettere automaticamente una sessione RDP quando diventa inattiva.  Una sessione è determinata come inattiva quando *non* è presente alcun input del mouse.  Tutte le attività a esecuzione prolungata che non implicano l'input del mouse e della tastiera fanno sì che la macchina virtuale sia in uno stato di inattività.  Ciò include l'esecuzione di una lunga query, lo streaming di video, la compilazione e così via.  A seconda delle esigenze della classe, è possibile scegliere di impostare il limite di tempo di inattività in modo che sia sufficientemente lungo per gestire questi tipi di attività.  Ad esempio, è possibile impostare il limite di tempo di inattività su 1 o più ore, se necessario.

Ecco l'esperienza dello studente quando configuri il limite di **sessioni inattive** in combinazione con l'impostazione [**di arresto automatico alla disconnessione:**](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)
 1. Lo studente si connette alla macchina virtuale Windows tramite RDP.
 2. Quando lo studente lascia aperta la finestra RDP e la macchina virtuale è inattiva per il limite di **sessione inattiva** specificato (ad esempio 5 minuti), lo studente visualizzerà la finestra seguente:

    ![Finestra di dialogo Limite di tempo di inattività scaduto](../media/how-to-windows-shutdown/idle-time-expired.png)

1. Se lo studente *non* fa clic su **OK**, la sessione RDP si disconnetterà automaticamente dopo 2 minuti.
2. Dopo la disconnessione della sessione RDP, una volta raggiunto l'intervallo di tempo specificato per l'impostazione **di arresto automatico alla disconnessione,** la macchina virtuale viene arrestata automaticamente da Azure Lab Services.After the RDP session disconnects, once the specified time time setting for the automatic shutdown on disconnect setting, the VM is automatically shutdown by Azure Lab Services.

### <a name="set-rdp-idle-session-time-limit-on-the-template-vm"></a>Impostare il limite di tempo per la sessione di inattività RDP per la macchina virtuale modelloSet RDP idle session time limit on the template VM

Per impostare il limite di tempo di inattività della sessione RDP, è possibile connettersi alla macchina virtuale del modello ed eseguire lo script di PowerShell seguente.

```powershell
# The MaxIdleTime is in milliseconds; by default, this script sets MaxIdleTime to 15 minutes.
$maxIdleTime = 15 * 60 * 90

Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" -Name "MaxIdleTime" -Value $maxIdleTime -Force
```
In alternativa, è possibile scegliere di seguire questi passaggi manuali usando la macchina virtuale del modello:Or, you can choose to follow these manual steps using the template VM:

1. Premere il tasto Windows, digitare **gpedit**, quindi selezionare **Modifica criteri di gruppo (Pannello di controllo)**.

1. Passare a **Configurazione computer > Modelli amministrativi > Componenti**di Windows > Servizi Desktop remoto > Host sessione Desktop remoto > limiti di tempo sessione .  

    ![Editor criteri di gruppo locali](../media/how-to-windows-shutdown/group-policy-idle.png)
   
1. Fare clic con il pulsante destro del mouse su Imposta limite di tempo per le sessioni di **Servizi Desktop remoto attive ma inattive**e scegliere **Modifica**.

1. Immetti le impostazioni seguenti e quindi fai clic su **OK**:
   1. Selezionare **Abilitato**.
   1. In **Opzioni**specificare il limite di **sessioni inattive**.

    ![Limite sessione inattiva](../media/how-to-windows-shutdown/edit-idle-time-limit.png)

1. Infine, per combinare questo comportamento con l'impostazione **di arresto automatico alla disconnessione,** è necessario seguire i passaggi descritti nell'articolo: [Abilitare l'arresto automatico delle macchine virtuali alla disconnessione](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect).

## <a name="remove-windows-shutdown-command-from-start-menu"></a>Rimuovere il comando di arresto di Windows dal menu Start

Le impostazioni di **Criteri di gruppo locali** di Windows consentono inoltre di rimuovere il comando di arresto dal menu **Start.**

Per rimuovere il comando shutdown, è possibile connettersi alla macchina virtuale del modello ed eseguire lo script di PowerShell seguente.

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

In alternativa, è possibile scegliere di seguire questi passaggi manuali usando la macchina virtuale del modello:Or, you can choose to follow these manual steps using the template VM:

1. Premere il tasto Windows, digitare **gpedit**, quindi selezionare **Modifica criteri di gruppo (Pannello di controllo)**.

1. Passare a **Configurazione computer > Modelli amministrativi > Menu start e barra delle applicazioni**.  

    ![Editor criteri di gruppo locali](../media/how-to-windows-shutdown/group-policy-shutdown.png)

1. Fare clic con il pulsante destro del mouse su **Rimuovi e impedire l'accesso ai comandi Arresta, Riavvia, Sospensione e Ibernazione**, quindi scegliere **Modifica**.

1. Selezionare l'impostazione **Abilitato** e quindi fare clic su **OK**:
 
   ![Impostazione di spegnimento](../media/how-to-windows-shutdown/edit-shutdown.png)

1. Si noti che il comando di arresto non viene più visualizzato nel menu **Start** di Windows; viene visualizzato solo il comando **Disconnetti.**

    ![Comando Shutdown](../media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo su come preparare una macchina virtuale modello di Windows: Guida alla configurazione di [un computer modello di Windows in Azure Lab ServicesSee](how-to-prepare-windows-template.md) the article on how to prepare a Windows template VM: Guide to setting up a Windows template machine in Azure Lab Services