<properties 
   pageTitle="Installare l'aggiornamento 2 nel dispositivo StorSimple | Microsoft Azure"
   description="Illustra come installare l'aggiornamento 2 di StorSimple serie 8000 sul dispositivo StorSimple serie 8000."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="01/12/2016"
   ms.author="alkohli" />

# Installare l'aggiornamento 2 nel dispositivo StorSimple

## Panoramica

Questa esercitazione illustra come installare l'aggiornamento 2 in un dispositivo StorSimple dove è in esecuzione una versione precedente del software tramite il portale di Azure classico. L'esercitazione illustra anche i passaggi necessari per l'aggiornamento quando viene configurato un gateway su un'interfaccia di rete diversa da DATA 0 del dispositivo StorSimple e si sta tentando di aggiornare da una versione del software precedente all'aggiornamento 1.

L'aggiornamento 2 include aggiornamenti del software del dispositivo, aggiornamenti del driver LSI e aggiornamenti del firmware del disco. Gli aggiornamenti del software del dispositivo e di LSI non sono problematici e possono essere applicati tramite il portale di Azure classico. Gli aggiornamenti del firmware del disco sono problematici e possono essere applicati solo tramite l'interfaccia di Windows PowerShell del dispositivo.

> [AZURE.IMPORTANT]
 
> -  L'aggiornamento 2 potrebbe non essere immediatamente visibile perché viene effettuata un'implementazione graduale degli aggiornamenti. Provare a cercare nuovamente l'aggiornamento dopo qualche giorno perché verrà presto reso disponibile.
> - Si esegue una serie di controlli preliminari automatici e manuali prima dell'installazione per determinare l'integrità del dispositivo in termini di connettività di stato e di rete hardware. Questi controlli preliminari vengono eseguiti solo se si applicano gli aggiornamenti dal portale di Azure classico. 
> - Si consiglia di installare gli aggiornamenti software e driver tramite il portale di Azure classico. Passare all'interfaccia di Windows PowerShell del dispositivo (per installare gli aggiornamenti) solo se il gateway di pre-aggiornamento ha esito negativo nel portale. L'installazione degli aggiornamenti, inclusi gli aggiornamenti di Windows, potrebbe richiedere fino a 4-7 ore. Gli aggiornamenti in modalità di manutenzione devono essere installati tramite l'interfaccia di Windows PowerShell del dispositivo. Dal momento che si tratta di aggiornamenti problematici, comporteranno un periodo di inattività per il dispositivo.
> - Se StorSimple Snapshot Manager facoltativo è in esecuzione, assicurarsi di aver aggiornato la versione di Snapshot Manager con l'aggiornamento 2 prima di aggiornare il dispositivo.

## Preparazione per gli aggiornamenti
È necessario effettuare le seguenti operazioni prima di analizzare e applicare l'aggiornamento:


1. Creare uno snapshot cloud dei dati del dispositivo.

2. Assicurarsi che il controller fisso di indirizzi IP sia instradabile e possa connettersi a Internet. Questi indirizzi IP fissi verranno usati per gestire gli aggiornamenti al dispositivo. È possibile effettuare una prova eseguendo il cmdlet seguente su ciascun controller dall'interfaccia di Windows PowerShell del dispositivo:

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

Dopo aver completato correttamente i controlli preliminari manuali, è possibile procedere all'analisi e installazione degli aggiornamenti.

## Installare l'aggiornamento 2 tramite il portale di Azure classico 

Questa è la procedura consigliata per aggiornare il dispositivo. Seguire questa procedura.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

## Installare l'aggiornamento 2 come aggiornamento rapido 

Usare questa procedura solo se la verifica del gateway non riesce quando si cerca di installare gli aggiornamenti tramite il portale di Azure classico. La verifica non riesce quando un gateway è assegnato a un'interfaccia di rete non DATA 0e sul dispositivo è in esecuzione una versione del software precedente all'aggiornamento 1.

Le versioni del software che possono essere aggiornate con il metodo di aggiornamento rapido sono aggiornamento 0.1, aggiornamento 0.2 e aggiornamento 0.3, aggiornamento 1, aggiornamento 1.1 e aggiornamento 1.2. Il metodo di aggiornamento rapido prevede i tre passaggi seguenti:

- Scaricare gli aggiornamenti rapidi da Microsoft Update Catalog.
- Installare e verificare gli aggiornamenti rapidi in modalità normale.
- Installare e verificare l'aggiornamento rapido in modalità di manutenzione.

Gli aggiornamenti rapidi applicati tramite questo metodo sono catalogati sotto:

| Ordine | KB | Nome | Descrizione del pacchetto | Tipo di aggiornamento |
|--------|-----------|-------------------------|-----------------------------|-------------|
| 1 | KB3121901 | Aggiornamento software | HcsMdsSfotwareUpdate.exe <br></br> CisMdsAgentUpdateBundle.exe | Normale |
| 2 | KB3121900 | Driver LSI | HcsLsiUpdate.exe | Normale |
| 3 | KB3080728 | Correzione Storport | Storport-KB3080728-x64.msu | Normale |
| 4 | KB3090322 | Correzione Spaceport | Spaceport-KB3090322-x64.msu | Normale |
| 5 | KB3121899 | Firmware del disco | DiskFirmwarePackage.exe | Manutenzione |


> [AZURE.IMPORTANT]
> 
> - Se il dispositivo esegue la versione finale (GA), contattare [il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md) per assistenza relativa all'aggiornamento.
> - Questa procedura deve essere eseguita solo una volta per applicare l'aggiornamento 2. È possibile utilizzare il portale di Azure classico per applicare gli aggiornamenti successivi.
> - Ogni installazione di aggiornamento rapido richiede circa 20 minuti per completare. Tempo totale di installazione di quasi 2 ore. 
> - Prima di usare questa procedura per applicare l'aggiornamento, assicurarsi che entrambi i controller di dispositivo siano online e che tutti i componenti hardware siano integri.

Eseguire i seguenti passaggi per applicare l'aggiornamento 2 come un aggiornamento rapido.

[AZURE.INCLUDE [storsimple-install-update2-hotfix](../../includes/storsimple-install-update2-hotfix.md)]


## Risoluzione degli errori di aggiornamento

**Se viene visualizzata una notifica che i controlli di pre-aggiornamento non è stato?**

Se un controllo preliminare ha esito negativo, verificare che siano presenti nella barra di notifica dettagliate nella parte inferiore della pagina. Fornisce informazioni aggiuntive quali controllo preliminare non riuscita. Nella figura seguente è illustrata un'istanza in cui viene visualizzata una notifica. In questo caso, il controllo di integrità del controller e il controllo di integrità del componente hardware non sono riusciti. Nella sezione **Stato hardware** è possibile vedere e i componenti **Controller 0** e **Controller 1** richiedono attenzione.
 
  ![Errore di pre-controllo](./media/storsimple-install-update-2/HCS_PreUpdateCheckFailed-include.png)

È necessario assicurarsi che entrambi i controller siano integri e online. È necessario anche per assicurarsi che vengano visualizzati tutti i componenti hardware del dispositivo StorSimple sia integro nella pagina manutenzione. È quindi possibile provare a installare gli aggiornamenti. Se non sono in grado di risolvere i problemi del componente hardware, è necessario contattare il supporto Microsoft per la procedura successiva.

**Se si riceve un messaggio di errore "Impossibile installare gli aggiornamenti" e l'indicazione è possibile fare riferimento all'aggiornamento della Guida per determinare la causa dell'errore di risoluzione dei problemi?**

Una causa probabile di questo è possibile che non disporre della connettività al server di Microsoft Update. Si tratta di una verifica manuale che deve essere eseguita. Se si perde la connessione al server di aggiornamento, il processo di aggiornamento avrà esito negativo. È possibile controllare la connettività eseguendo il cmdlet seguente dall'interfaccia di Windows PowerShell del dispositivo StorSimple:

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

Eseguire il cmdlet in entrambi i controller.
 
Se si è verificato è presente la connettività e continuare a visualizzare questo problema, contattare il supporto Microsoft per la procedura successiva.


## Passaggi successivi

Per sapere di più sulla [versione dell'aggiornamento 2](storsimple-update2-release-notes.md).

<!---HONumber=AcomDC_0114_2016-->