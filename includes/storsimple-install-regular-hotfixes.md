<properties
   pageTitle="Installare gli aggiornamenti rapidi regolari"
   description="Viene illustrato come  installare gli aggiornamenti rapidi regolari tramite Windows PowerShell per StorSimple"
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
   ms.date="04/23/2015"
   ms.author="v-sharos" />
### Per installare aggiornamenti rapidi regolari tramite Windows PowerShell per StorSimple

1. Connettersi alla console seriale del dispositivo. Per ulteriori informazioni, vedere [Per connettersi tramite la console seriale](#to-connect-through-the-serial-console).

2. Nel menu della console seriale, scegliere l'opzione 1, **Accedi con accesso completo**. Digitare la password. La password predefinita è **Password1**.

3. Al prompt dei comandi digitare:

     **Start-HcsHotfix**

   >[AZURE.IMPORTANT]
   >
   > - Questo comando si applica solo agli aggiornamenti rapidi regolari. È possibile eseguire questo comando in un solo controller, ma verranno aggiornati entrambi i controller. 
   > - Durante il processo di aggiornamento è possibile che venga eseguito il failover di un controller. Tale failover, tuttavia, non avrà effetto sulla disponibilità o sul funzionamento del sistema.

4. Quando richiesto, specificare il percorso della cartella condivisa di rete che contiene i file dell'aggiornamento rapido.

5. Verrà richiesto di confermare. Digitare **Y** per procedere con l'installazione dell'aggiornamento rapido.

<!--HONumber=52-->
