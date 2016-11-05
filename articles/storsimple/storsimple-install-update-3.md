---
title: Installare l'aggiornamento 3 nel dispositivo StorSimple | Microsoft Docs
description: Illustra come installare l'aggiornamento 3 di StorSimple serie 8000 in un dispositivo StorSimple serie 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/21/2016
ms.author: alkohli

---
# Installare l'aggiornamento 3 nel dispositivo StorSimple
## Overview
Questa esercitazione illustra come installare l'aggiornamento 3 in un dispositivo StorSimple dove è in esecuzione una versione precedente del software tramite il portale di Azure classico e usando il metodo hotfix. Il metodo hotfix è usato quando viene configurato un gateway su un'interfaccia di rete diversa da DATA 0 del dispositivo StorSimple e si sta tentando di aggiornare da una versione del software precedente all'aggiornamento 1.

L'aggiornamento 3 include gli aggiornamenti del software della periferica, di driver e firmware LSI, di Storport e Spaceport. Se si proviene dall'aggiornamento 2 o da una versione precedente, è necessario anche applicare iSCSI, WMI e, in alcuni casi, gli aggiornamenti del firmware del disco. Gli aggiornamenti del software del dispositivo, WMI, iSCSI, del driver LSI, Spaceport, e le correzioni Storport non sono problematici e possono essere applicati tramite il portale di Azure classico. Gli aggiornamenti del firmware del disco sono problematici e possono essere applicati solo tramite l'interfaccia di Windows PowerShell del dispositivo.

> [!IMPORTANT]
> * Si esegue una serie di controlli preliminari automatici e manuali prima dell'installazione per determinare l'integrità del dispositivo in termini di connettività di stato e di rete hardware. Questi controlli preliminari vengono eseguiti solo se si applicano gli aggiornamenti dal portale di Azure classico.
> * Si consiglia di installare gli aggiornamenti software e driver tramite il portale di Azure classico. Passare all'interfaccia di Windows PowerShell del dispositivo (per installare gli aggiornamenti) solo se il gateway di pre-aggiornamento ha esito negativo nel portale. A seconda della versione da cui viene eseguito l'aggiornamento, gli aggiornamenti possono richiedere 1,5 - 2,5 ore per essere installati. Gli aggiornamenti in modalità di manutenzione devono essere installati tramite l'interfaccia di Windows PowerShell del dispositivo. Dal momento che si tratta di aggiornamenti problematici, comporteranno un periodo di inattività per il dispositivo.
> * Se StorSimple Snapshot Manager facoltativo è in esecuzione, assicurarsi di aver aggiornato la versione di Snapshot Manager con l'aggiornamento 2 prima di aggiornare il dispositivo.
> 
> 

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## Installare l'aggiornamento 3 tramite il portale di Azure classico
Seguire questa procedura per aggiornare il dispositivo all'[aggiornamento 3](storsimple-update3-release-notes.md).

> [!NOTE]
> Se si sta applicando l'aggiornamento 2 o una versione successiva (compreso l'aggiornamento 2.1), Microsoft è in grado di estrarre le informazioni di diagnostica aggiuntive dal dispositivo. Di conseguenza, quando il team addetto alle operazioni identifica i dispositivi che presentano problemi, sono disponibili più modi di raccogliere informazioni dal dispositivo e diagnosticare i problemi. Accettando l'aggiornamento 2, si acconsente a partecipare al supporto proattivo.
> 
> 

[!INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

1. Verificare che nel dispositivo sia in esecuzione l'**aggiornamento 3 della serie 8000 di StorSimple (6.3.9600.17759)**. Inoltre, è necessario modificare la **data dell'ultimo aggiornamento**.
   
   Se si sta eseguendo l'aggiornamento da una versione precedente all'aggiornamento 2, è possibile vedere che sono disponibili anche gli aggiornamenti in modalità manutenzione. Questo messaggio potrebbe essere visualizzato fino a 24 ore dopo l'installazione degli aggiornamenti.
   
   Gli aggiornamenti in modalità manutenzione sono aggiornamenti problematici che comportano tempi di inattività del dispositivo e possono essere applicati solo tramite l'interfaccia di Windows PowerShell del dispositivo. In alcuni casi quando si esegue l'Aggiornamento 1.2, il firmware del disco potrebbe essere già aggiornato, nel qual caso non è necessario installare eventuali aggiornamenti in modalità manutenzione.
   
   Se si parte dall'aggiornamento 2 o versione successiva, ora il dispositivo dovrebbe essere aggiornato. È possibile ignorare i passaggi rimanenti.
2. Scaricare gli aggiornamenti in modalità di manutenzione utilizzando la procedura indicata in [Scaricare gli aggiornamenti rapidi](#to-download-hotfixes) per cercare e scaricare KB3121899, che installa gli aggiornamenti del firmware del disco (gli altri aggiornamenti devono essere già installati a questo punto).
3. Seguire i passaggi elencati nella sezione [Installare e verificare gli aggiornamenti rapidi in modalità di manutenzione](#to-install-and-verify-maintenance-mode-hotfixes) per installare gli aggiornamenti in modalità manutenzione.

## Installare l'aggiornamento 3 come un hotfix
Usare questa procedura se la verifica del gateway non riesce quando si cerca di installare gli aggiornamenti tramite il portale di Azure classico. La verifica non riesce quando un gateway è assegnato a un'interfaccia di rete non DATA 0e sul dispositivo è in esecuzione una versione del software precedente all'aggiornamento 1.

Le versioni software che possono essere aggiornate usando il metodo hotfix sono:

* Aggiornamento 0.1, 0.2, 0.3
* Aggiornamento 1, 1.1, 1.2
* Aggiornamento 2, 2.1, 2.2

> [!IMPORTANT]
> * Se il dispositivo esegue la versione finale (GA), contattare [il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md) per assistenza relativa all'aggiornamento.
> 
> 

Il metodo hotfix prevede i tre passaggi seguenti:

1. Scaricare gli hotfix da Microsoft Update Catalog.
2. Installare e verificare gli hotfix in modalità normale.
3. Installare e verificare l'hotfix di modalità di manutenzione (solo durante l'aggiornamento dal software precedente all'aggiornamento 2).

#### Scaricare gli aggiornamenti per il dispositivo
**Se il dispositivo esegue l'aggiornamento 2.1 o 2.2**, è necessario scaricare e installare gli hotfix seguenti nell'ordine indicato:

| Ordine | KB | Descrizione | Tipo di aggiornamento | Tempo dell'installazione |
| --- | --- | --- | --- | --- |
| 1\. |KB3186843 |Aggiornamento software &#42; |Regolare <br></br>Senza interruzioni |~ 45 min. |
| 2\. |KB3186859 |Driver e firmware LSI |Regolare <br></br>Senza interruzioni |~ 20 min. |
| 3\. |KB3121261 |Correzione Storport e Spaceport </br> Windows Server 2012 R2 |Regolare <br></br>Senza interruzioni |~ 20 min. |

& #42; *Tenere presente che l'aggiornamento del software è costituito da due file binari: l'aggiornamento del software del dispositivo preceduto da `all-hcsmdssoftwareupdate` e gli elementi di configurazione e l'aggiornamento dell'agente MDS preceduti da `all-cismdsagentupdatebundle`. L'aggiornamento del software del dispositivo deve essere installato prima degli elementi di configurazione e dell'agente MDS. Inoltre, è necessario riavviare il controller attivo tramite il cmdlet `Restart-HcsController` dopo aver applicato l'aggiornamento dell'agente Cis e Mds (e prima di applicare i restanti aggiornamenti).*

**Se il dispositivo esegue l'aggiornamento 0.1, 0.2, 0.3, 1.0, 1.1, 1.2 o 2.0**, è necessario scaricare e installare i seguenti hotfix oltre agli aggiornamenti del software, del driver LSI e del firmware (indicati nella tabella precedente), nell'ordine prescritto:

| Ordine | KB | Descrizione | Tipo di aggiornamento | Tempo dell'installazione |
| --- | --- | --- | --- | --- |
| 4\. |KB3146621 |Pacchetto iSCSI |Regolare <br></br>Senza interruzioni |~ 20 min. |
| 5\. |KB3103616 |Pacchetto WMI |Regolare <br></br>Senza interruzioni |~ 12 min. |

<br></br>

**Se il dispositivo esegue le versioni 0.2, 0.3, 1.0, 1.1 e 1.2**, potrebbe essere necessario anche installare gli aggiornamenti del firmware del disco sopra tutti gli aggiornamenti indicati nelle tabelle precedenti. È possibile verificare se sono necessari gli aggiornamenti del firmware del disco eseguendo il cmdlet `Get-HcsFirmwareVersion`. Se si stanno eseguendo le versioni del firmware `XMGG`, `XGEG`, `KZ50`, `F6C2`, `VR08` non è quindi necessario installare questi aggiornamenti.

| Ordine | KB | Descrizione | Tipo di aggiornamento | Tempo dell'installazione |
| --- | --- | --- | --- | --- |
| 6\. |KB3121899 |Firmware del disco |Manutenzione <br></br>Con interruzioni |~ 30 min. |

<br></br>

> [!IMPORTANT]
> * Questa procedura deve essere eseguita solo una volta per applicare l'aggiornamento 3. È possibile utilizzare il portale di Azure classico per applicare gli aggiornamenti successivi.
> * Se si parte dall'aggiornamento 2.2, il tempo totale richiesto per l'installazione è di circa 1,1 ore.
> * Prima di usare questa procedura per applicare l'aggiornamento, assicurarsi che entrambi i controller del dispositivo siano online e che tutti i componenti hardware siano integri.
> 
> 

Eseguire i passaggi seguenti per scaricare e importare gli hotfix.

[!INCLUDE [storsimple-install-update3-hotfix](../../includes/storsimple-install-update3-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## Passaggi successivi
Altre informazioni sulla [versione dell'aggiornamento 3](storsimple-update3-release-notes.md).

<!---HONumber=AcomDC_0921_2016-->