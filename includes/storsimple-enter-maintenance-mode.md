<properties
   pageTitle="Attivare la modalità di manutenzione"
   description="Viene illustrato come attivare la modalità di manutenzione per il dispositivo StorSimple."
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

### Per attivare la modalità di manutenzione

1. Nel menu della console seriale, scegliere l'opzione 1, **Accedi con accesso completo**.

2. Digitare la password. La password predefinita è **Password1**.

3. Al prompt dei comandi digitare

    **Enter-HcsMaintenanceMode**

4. Verrà visualizzato un messaggio di avviso indicante che la modalità di manutenzione interromperà tutte le richieste I/O e la connessione al portale di gestione e verrà richiesto di confermare che si desidera procedere. Digitare **Y** per attivare la modalità di manutenzione.

    Entrambi i controller verranno riavviati. Una volta completato il riavvio, verrà visualizzato un altro messaggio che indica che il dispositivo è in modalità di manutenzione.

<!--HONumber=52-->
