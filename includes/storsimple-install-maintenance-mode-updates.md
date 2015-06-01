<properties
   pageTitle="Installare gli aggiornamenti in modalità di manutenzione"
   description="Viene illustrato come  installare gli aggiornamenti in modalità di manutenzione tramite Windows PowerShell per StorSimple"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" /> 
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/21/2015"
   ms.author="v-sharos" />

### Per installare gli aggiornamenti in modalità di manutenzione tramite Windows PowerShell per StorSimple

1. Se non è già stato fatto, accedere alla console seriale del dispositivo e selezionare l'opzione 1, **Accedi con accesso completo**. 

2. Digitare la password. La password predefinita è **Password1**.

3. Al prompt dei comandi digitare:

   **Get-HcsUpdateAvailability**
    
    Verrà notificato se sono disponibili aggiornamenti e se gli aggiornamenti sono problematici o meno.

4. Per applicare aggiornamenti problematici, è necessario attivare la modalità di manutenzione per il dispositivo. Vedere [Per attivare la modalità di manutenzione](#to-enter-maintenance-mode) per le istruzioni.

5. Quando il dispositivo è in modalità di manutenzione, al prompt dei comandi digitare:

    **Start-HcsUpdate**

6. Verrà richiesto di confermare. Dopo la conferma, gli aggiornamenti verranno installati nel controller a cui si è effettuato l’accesso. Una volta installati gli aggiornamenti, il controller verrà riavviato.

7. Al termine del riavvio del primo controller, connettersi all’altro controller ed eseguire i passaggi da 1 a 6.

8. Dopo l'aggiornamento di entrambi i controller, uscire dalla modalità di manutenzione. Vedere [Per uscire dalla modalità di manutenzione](#to-exit-maintenance-mode) per le istruzioni.

<!--HONumber=52-->
