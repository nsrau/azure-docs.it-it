<properties 
   pageTitle="Installare l'aggiornamento 1 nel dispositivo StorSimple"
   description="Viene illustrato come installare StorSimple 8000 serie Update 1 sul dispositivo."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/18/2015"
   ms.author="alkohli" />

# Installare l'aggiornamento 1 nel dispositivo StorSimple

## Panoramica

In questa esercitazione viene illustrato come installare l'aggiornamento 1 in un dispositivo StorSimple in cui è in esecuzione una versione del software prima dell'aggiornamento 1. Il dispositivo potrebbe essere in esecuzione la versione (GA) disponibile in genere, aggiornamento 0,1, aggiornamento 0,2 o 0,3 di aggiornamento software.

Durante l'installazione, se nel dispositivo è in esecuzione una versione precedente all'aggiornamento 1, i controlli vengono eseguiti sul dispositivo. Questi controlli determinano l'integrità del dispositivo in termini di connettività di stato e di rete hardware.

Verrà richiesto di eseguire una pre-verifica di manuale per assicurarsi che:

- Il controller fisso di indirizzi IP sono instradabile e possono connettersi a Internet. Questi indirizzi IP utilizzati per gestire gli aggiornamenti al dispositivo StorSimple. È possibile verificarlo eseguendo il cmdlet seguente in ogni controller:

    `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network> `
 
	**Output di esempio di Test-Connection quando IP fissi non riescono a connettersi a Internet**

	    
		Controller0>Test-Connection -Source 10.126.173.91 -Destination bing.com
	    
	    Source	  Destination 	IPV4Address      IPV6Address
	    ----------------- -----------  -----------
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	
		Controller0>Test-Connection -Source 10.126.173.91 -Destination  204.79.197.200

	    Source	  Destination 	  IPV4Address    IPV6Address
	    ----------------- -----------  -----------
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200
	    
	    


- Prima di aggiornare il dispositivo, è consigliabile eseguire uno snapshot dei dati del dispositivo nel cloud.

Dopo aver verificato e riconosciuto i controlli manuali (sopra), verrà eseguita una serie di controlli di pre-aggiornamento automatici. Sono state illustrate le seguenti operazioni:

- **Controlli di integrità del controller** per verificare che entrambi i controller dei dispositivi siano integri e online.

- **Controlli di integrità del componente hardware** per verificare che tutti i componenti hardware del dispositivo StorSimple siano integri.

- **Controlla DATA 0** per verificare che DATA 0 è attivato sul dispositivo. Se questa interfaccia non è abilitata, è necessario abilitarlo e riprovare.

- **Controlli DATA 2 e 3 dati** per verificare che la DATA 2 e 3 dati interfacce di rete non sono abilitate. Se queste interfacce sono abilitate, sarà necessario disattivarli e quindi si tenta di aggiornare il dispositivo. Questo controllo viene eseguito solo se si sta aggiornando un dispositivo che esegue GA. Dispositivi che eseguono versioni 0,3, 0,2 o 0,1 questo controllo non è necessario.

- **Controllo gateway** su qualsiasi dispositivo esegue una versione precedente Update 1. Questo controllo viene eseguito solo sui dispositivi con un gateway configurato per un'interfaccia di rete diverso da DATA 0.
 
Aggiornamento 1 verrà applicata solo se tutti i controlli di pre-aggiornamento siano stati completati correttamente. Dopo aver applicato Update 1 nel dispositivo StorSimple, gli aggiornamenti futuri non avranno i controlli dati 2 e 3 di dati e la verifica del Gateway. I controlli preliminari su altri verranno eseguita.

## Utilizzare il portale di gestione per installare l'aggiornamento 1

È consigliabile utilizzare il portale di gestione di Azure per l'aggiornamento di un dispositivo che esegue la versione GA. Eseguire i passaggi seguenti per aggiornare il dispositivo.

[AZURE.INCLUDE [storsimple-install-aggiornamento-tramite-portale](../../includes/storsimple-install-update-via-portal.md)]


## Risoluzione degli errori di aggiornamento

**Se viene visualizzata una notifica che i controlli di pre-aggiornamento non è stato?**

Se un controllo preliminare ha esito negativo, verificare che siano presenti nella barra di notifica dettagliate nella parte inferiore della pagina. Fornisce informazioni aggiuntive quali controllo preliminare non riuscita. Nella figura seguente è illustrata un'istanza in cui viene visualizzata una notifica. In questo caso, il controllo di integrità del controller e il controllo di integrità del componente hardware non sono riusciti. Sotto il **stato Hardware** sezione, noterete che il Controller 0 e 1 Controller componenti richiedono attenzione.
 
  ![Errore di pre-controllo](./media/storsimple-install-update-1/HCS_PreUpdateCheckFailed-include.png)

È necessario assicurarsi che entrambi i controller siano integri e online. È necessario anche per assicurarsi che vengano visualizzati tutti i componenti hardware del dispositivo StorSimple sia integro nella pagina manutenzione. È quindi possibile provare a installare gli aggiornamenti. Se non sono in grado di risolvere i problemi del componente hardware, è necessario contattare il supporto Microsoft per la procedura successiva.

**Se si riceve un messaggio di errore "Impossibile installare gli aggiornamenti" e l'indicazione è possibile fare riferimento all'aggiornamento della Guida per determinare la causa dell'errore di risoluzione dei problemi?**

Una causa probabile di questo è possibile che non disporre della connettività al server di Microsoft Update. Si tratta di una verifica manuale che deve essere eseguita. Se si perde la connessione al server di aggiornamento, il processo di aggiornamento avrà esito negativo. È possibile controllare la connettività eseguendo il cmdlet seguente dall'interfaccia di Windows PowerShell del dispositivo StorSimple:

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

Eseguire il cmdlet in entrambi i controller.
 
Se si è verificato è presente la connettività e continuare a visualizzare questo problema, contattare il supporto Microsoft per la procedura successiva.

## Passaggi successivi

Ulteriori informazioni su [Microsoft Azure StorSimple](storsimple-overview.md)

<!---HONumber=July15_HO2-->