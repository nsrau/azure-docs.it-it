---
title: Installare l'aggiornamento 2.2 nel dispositivo StorSimple | Microsoft Docs
description: Illustra come installare l'aggiornamento 2.2 di StorSimple serie 8000 sul dispositivo StorSimple serie 8000.
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
ms.date: 08/02/2016
ms.author: alkohli

---
# Installare l'aggiornamento 2.2 nel dispositivo StorSimple
## Panoramica
Questa esercitazione illustra come installare l'aggiornamento 2.2 in un dispositivo StorSimple dove è in esecuzione una versione precedente del software tramite il portale di Azure classico e usando il metodo hotfix. Il metodo hotfix è usato quando viene configurato un gateway su un'interfaccia di rete diversa da DATA 0 del dispositivo StorSimple e si sta tentando di aggiornare da una versione del software precedente all'aggiornamento 1.

L'aggiornamento 2.2 comprende gli aggiornamento del software del dispositivo, iSCSI e WMI. Se si esegue l'aggiornamento dalla versione 2.1, dovrà essere applicato solo l'aggiornamento del software del dispositivo. Se si esegue l'aggiornamento da una versione precedente all'aggiornamento 2, è necessario anche applicare driver LSI, Spaceport, Storport e gli aggiornamenti firmware del disco. Gli aggiornamenti del software del dispositivo, WMI, iSCSI, del driver LSI, Spaceport, e le correzioni Storport non sono problematici e possono essere applicati tramite il portale di Azure classico. Gli aggiornamenti del firmware del disco sono problematici e possono essere applicati solo tramite l'interfaccia di Windows PowerShell del dispositivo.

> [!IMPORTANT]
> * Si esegue una serie di controlli preliminari automatici e manuali prima dell'installazione per determinare l'integrità del dispositivo in termini di connettività di stato e di rete hardware. Questi controlli preliminari vengono eseguiti solo se si applicano gli aggiornamenti dal portale di Azure classico.
> * Si consiglia di installare gli aggiornamenti software e driver tramite il portale di Azure classico. Passare all'interfaccia di Windows PowerShell del dispositivo (per installare gli aggiornamenti) solo se il gateway di pre-aggiornamento ha esito negativo nel portale. A seconda della versione da cui viene eseguito l'aggiornamento, gli aggiornamenti possono richiedere 1,5 - 2,5 ore per essere installati. Gli aggiornamenti in modalità di manutenzione devono essere installati tramite l'interfaccia di Windows PowerShell del dispositivo. Dal momento che si tratta di aggiornamenti problematici, comporteranno un periodo di inattività per il dispositivo.
> * Se StorSimple Snapshot Manager facoltativo è in esecuzione, assicurarsi di aver aggiornato la versione di Snapshot Manager con l'aggiornamento 2.2 prima di aggiornare il dispositivo.
> 
> 

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## Installare l'aggiornamento 2.2 tramite il portale di Azure classico
Eseguire i passaggi seguenti per aggiornare il dispositivo a [Aggiornamento 2.2](storsimple-update21-release-notes.md).

> [!NOTE]
> Se si sta applicando l'aggiornamento 2 o una versione successiva (compreso l'aggiornamento 2.1), Microsoft è in grado di estrarre le informazioni di diagnostica aggiuntive dal dispositivo. Di conseguenza, quando il team addetto alle operazioni identifica i dispositivi che presentano problemi, sono disponibili più modi di raccogliere informazioni dal dispositivo e diagnosticare i problemi. Accettando l'aggiornamento 2, si acconsente a partecipare al supporto proattivo.
> 
> 

[!INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

1. Verificare che nel dispositivo sia in esecuzione l'**aggiornamento 2.2 della serie 8000 di StorSimple (6.3.9600.17708)**. Inoltre, è necessario modificare la **data dell'ultimo aggiornamento**.
   
   Se si sta eseguendo l'aggiornamento da una versione precedente all'aggiornamento 2, è possibile vedere che sono disponibili anche gli aggiornamenti in modalità manutenzione. Questo messaggio potrebbe essere visualizzato fino a 24 ore dopo l'installazione degli aggiornamenti.
   
   Gli aggiornamenti in modalità manutenzione sono aggiornamenti problematici che comportano tempi di inattività del dispositivo e possono essere applicati solo tramite l'interfaccia di Windows PowerShell del dispositivo. In alcuni casi quando si esegue l'Aggiornamento 1.2, il firmware del disco potrebbe essere già aggiornato, nel qual caso non è necessario installare eventuali aggiornamenti in modalità manutenzione.
   
   Se si sta aggiornando dall'aggiornamento 2, ora il dispositivo dovrebbe essere aggiornato. È possibile ignorare i passaggi rimanenti.
2. Scaricare gli aggiornamenti in modalità di manutenzione utilizzando la procedura indicata in [Scaricare gli aggiornamenti rapidi](#to-download-hotfixes) per cercare e scaricare KB3121899, che installa gli aggiornamenti del firmware del disco (gli altri aggiornamenti devono essere già installati a questo punto).
3. Seguire i passaggi elencati nella sezione [Installare e verificare gli aggiornamenti rapidi in modalità di manutenzione](#to-install-and-verify-maintenance-mode-hotfixes) per installare gli aggiornamenti in modalità manutenzione.

## Installare l'aggiornamento 2.2 come un hotfix
Usare questa procedura se la verifica del gateway non riesce quando si cerca di installare gli aggiornamenti tramite il portale di Azure classico. La verifica non riesce quando un gateway è assegnato a un'interfaccia di rete non DATA 0e sul dispositivo è in esecuzione una versione del software precedente all'aggiornamento 1.

Le versioni software che possono essere aggiornate usando il metodo hotfix sono:

* Aggiornamento 0.1, 0.2, 0.3
* Aggiornamento 1, 1.1, 1.2
* Aggiornamento 2, 2.1

> [!IMPORTANT]
> * Se il dispositivo esegue la versione finale (GA), contattare [il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md) per assistenza relativa all'aggiornamento.
> 
> 

Il metodo hotfix prevede i tre passaggi seguenti:

* Scaricare gli hotfix da Microsoft Update Catalog.
* Installare e verificare gli hotfix in modalità normale.
* Installare e verificare l'hotfix di modalità di manutenzione (solo durante l'aggiornamento dal software precedente all'aggiornamento 2).

#### Scaricare gli aggiornamenti per un dispositivo che esegue il software di aggiornamento 2.1
**Se il dispositivo esegue l'aggiornamento 2.1**, è necessario scaricare solo l'aggiornamento del software KB3179904. Installare solo il file binario preceduto da "all-hcsmdssoftwareudpate". Non installare gli elementi di configurazione e l'aggiornamento dell'agente MDS preceduti da `all-cismdsagentupdatebundle`. In caso contrario, verrà generato un errore. Questo aggiornamento non provoca interruzioni: l'I/O non verrà interrotto e il dispositivo non andrà incontro ad alcun tempo di inattività.

#### Scaricare gli aggiornamenti per un dispositivo che esegue il software di aggiornamento 2
**Se il dispositivo esegue l'aggiornamento 2**, è necessario scaricare e installare gli hotfix seguenti nell'ordine indicato:

| Ordine | KB | Descrizione | Tipo di aggiornamento | Tempo dell'installazione |
| --- | --- | --- | --- | --- |
| 1\. |KB3179904 |Aggiornamento software &#42; |Regolare <br></br>Senza interruzioni |~ 45 min. |
| 2\. |KB3146621 |Pacchetto iSCSI |Regolare <br></br>Senza interruzioni |~ 20 min. |
| 3\. |KB3103616 |Pacchetto WMI |Regolare <br></br>Senza interruzioni |~ 12 min. |

 & #42; *Tenere presente che l'aggiornamento del software è costituito da due file binari: l'aggiornamento del software del dispositivo preceduto da `all-hcsmdssoftwareupdate` e gli elementi di configurazione e l'aggiornamento dell'agente MDS preceduti da `all-cismdsagentupdatebundle`. L'aggiornamento del software del dispositivo deve essere installato prima degli elementi di configurazione e dell'agente MDS. È inoltre necessario riavviare il controller attivo tramite il cmdlet `Restart-HcsController` dopo aver applicato l'aggiornamento dell'agente Cis e Mds (e prima di applicare i restanti aggiornamenti).*

#### Scaricare gli aggiornamenti per un dispositivo che esegue il software di pre-aggiornamento 2
**Se il dispositivo sta eseguendo le versioni 0.2, 0.3, 1.0 e 1.1**, è necessario scaricare e installare l'aggiornamento del driver e del firmware LSI in aggiunta agli aggiornamenti software, iSCSI e WMI. Questo aggiornamento è già installato se si esegue l'aggiornamento 1.2 o 2.

| Ordine | KB | Descrizione | Tipo di aggiornamento | Tempo dell'installazione |
| --- | --- | --- | --- | --- |
| 4\. |KB3121900 |Driver e firmware LSI |Regolare <br></br>Senza interruzioni |~ 20 min. |

<br></br> **Se il dispositivo sta eseguendo le versioni 0.2, 0.3, 1.0, 1.1 e 1.2**, è necessario scaricare e installare Spaceport e la correzione Storport. Sono già installati se si sta eseguendo l'aggiornamento o 2.

| Ordine | KB | Descrizione | Tipo di aggiornamento | Tempo dell'installazione |
| --- | --- | --- | --- | --- |
| 5\. |KB3090322 |Correzione Spaceport </br> Windows Server 2012 R2 |Regolare <br></br>Senza interruzioni |~ 20 min. |
| 6\. |KB3080728 |Correzione Storport </br> Windows Server 2012 R2 |Regolare <br></br>Senza interruzioni |~ 20 min. |

<br></br> Può essere necessario anche installare gli aggiornamenti del firmware del disco. È possibile verificare se sono necessari gli aggiornamenti del firmware del disco eseguendo il cmdlet `Get-HcsFirmwareVersion`. Se si stanno eseguendo le versioni del firmware `XMGG`, `XGEG`, `KZ50`, `F6C2`, `VR08` non è quindi necessario installare questi aggiornamenti.

| Ordine | KB | Descrizione | Tipo di aggiornamento | Tempo dell'installazione |
| --- | --- | --- | --- | --- |
| 7\. |KB3121899 |Firmware del disco |Manutenzione <br></br>Con interruzioni |~ 30 min. |

<br></br>

> [!IMPORTANT]
> * Questa procedura deve essere eseguita solo una volta per applicare l'aggiornamento 2.2. È possibile utilizzare il portale di Azure classico per applicare gli aggiornamenti successivi.
> * Se si sta eseguendo l'aggiornamento da aggiornamento 2, il tempo totale richiesto per l'installazione è di circa 1,5 ore.
> * Prima di usare questa procedura per applicare l'aggiornamento, assicurarsi che entrambi i controller del dispositivo siano online e che tutti i componenti hardware siano integri.
> 
> 

Eseguire i passaggi seguenti per scaricare e importare gli hotfix.

[!INCLUDE [storsimple-install-update21-hotfix](../../includes/storsimple-install-update21-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## Passaggi successivi
Altre informazioni sulla [versione dell'aggiornamento 2.1](storsimple-update21-release-notes.md).

<!---HONumber=AcomDC_0803_2016-->