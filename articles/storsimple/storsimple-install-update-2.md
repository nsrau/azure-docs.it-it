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
   ms.date="09/21/2016"
   ms.author="alkohli" /> 

# Installare l'aggiornamento 2 nel dispositivo StorSimple

## Overview

Questa esercitazione illustra come installare l'aggiornamento 2 in un dispositivo StorSimple dove è in esecuzione una versione precedente del software tramite il portale di Azure classico. L'esercitazione illustra anche i passaggi necessari per l'aggiornamento quando viene configurato un gateway su un'interfaccia di rete diversa da DATA 0 del dispositivo StorSimple e si sta tentando di aggiornare da una versione del software precedente all'aggiornamento 1.

L'aggiornamento 2 include aggiornamenti del software del dispositivo, aggiornamenti del driver LSI e aggiornamenti del firmware del disco. Gli aggiornamenti del software del dispositivo e di LSI non sono problematici e possono essere applicati tramite il portale di Azure classico. Gli aggiornamenti del firmware del disco sono problematici e possono essere applicati solo tramite l'interfaccia di Windows PowerShell del dispositivo.

> [AZURE.IMPORTANT]

> -  L'aggiornamento 2 potrebbe non essere immediatamente visibile perché viene effettuata un'implementazione graduale degli aggiornamenti. Provare a cercare nuovamente l'aggiornamento dopo qualche giorno perché verrà presto reso disponibile.
> - Si esegue una serie di controlli preliminari automatici e manuali prima dell'installazione per determinare l'integrità del dispositivo in termini di connettività di stato e di rete hardware. Questi controlli preliminari vengono eseguiti solo se si applicano gli aggiornamenti dal portale di Azure classico.
> - Si consiglia di installare gli aggiornamenti software e driver tramite il portale di Azure classico. Passare all'interfaccia di Windows PowerShell del dispositivo (per installare gli aggiornamenti) solo se il gateway di pre-aggiornamento ha esito negativo nel portale. L'installazione degli aggiornamenti, inclusi gli aggiornamenti di Windows, potrebbe richiedere fino a 4-7 ore. Gli aggiornamenti in modalità di manutenzione devono essere installati tramite l'interfaccia di Windows PowerShell del dispositivo. Dal momento che si tratta di aggiornamenti problematici, comporteranno un periodo di inattività per il dispositivo.
> - Se StorSimple Snapshot Manager facoltativo è in esecuzione, assicurarsi di aver aggiornato la versione di Snapshot Manager con l'aggiornamento 2 prima di aggiornare il dispositivo.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## Installare l'aggiornamento 2 tramite il portale di Azure classico

Eseguire i passaggi seguenti per aggiornare il dispositivo a [Aggiornamento 2](storsimple-update2-release-notes.md).


> [AZURE.NOTE]
L'aggiornamento 2 consente a Microsoft di estrarre informazioni di diagnostica aggiuntive dal dispositivo. Di conseguenza, quando il team addetto alle operazioni identifica i dispositivi che presentano problemi, sono disponibili più modi di raccogliere informazioni dal dispositivo e diagnosticare i problemi. Accettando l'aggiornamento 2, si acconsente a partecipare al supporto proattivo.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Verificare che nel dispositivo sia in esecuzione l'**aggiornamento 2 della serie 8000 di StorSimple (6.3.9600.17673)**. Inoltre, è necessario modificare la **data dell'ultimo aggiornamento**. Saranno anche disponibili gli aggiornamenti in modalità manutenzione. Questo messaggio potrebbe essere visualizzato fino a 24 ore dopo l’installazione degli aggiornamenti.

    Gli aggiornamenti in modalità manutenzione sono aggiornamenti problematici che comportano tempi di inattività del dispositivo e possono essere applicati solo tramite l'interfaccia di Windows PowerShell del dispositivo. In alcuni casi quando si esegue l'Aggiornamento 1.2, il firmware del disco potrebbe essere già aggiornato, nel qual caso non è necessario installare eventuali aggiornamenti in modalità manutenzione.

13. Scaricare gli aggiornamenti in modalità di manutenzione utilizzando la procedura indicata in [Scaricare gli aggiornamenti rapidi](#to-download-hotfixes) per cercare e scaricare KB3121899, che installa gli aggiornamenti del firmware del disco (gli altri aggiornamenti devono essere già installati a questo punto).

13. Seguire i passaggi elencati nella sezione [Installare e verificare gli aggiornamenti rapidi in modalità di manutenzione](#to-install-and-verify-maintenance-mode-hotfixes) per installare gli aggiornamenti in modalità manutenzione.


## Installare l'aggiornamento 2 come un hotfix

Usare questa procedura se la verifica del gateway non riesce quando si cerca di installare gli aggiornamenti tramite il portale di Azure classico. La verifica non riesce quando un gateway è assegnato a un'interfaccia di rete non DATA 0e sul dispositivo è in esecuzione una versione del software precedente all'aggiornamento 1.

Le versioni del software che possono essere aggiornate tramite hotfix sono aggiornamento 0.1, aggiornamento 0.2 e aggiornamento 0.3, aggiornamento 1, aggiornamento 1.1 e aggiornamento 1.2. Il metodo hotfix prevede i tre passaggi seguenti:

- Scaricare gli hotfix da Microsoft Update Catalog.
- Installare e verificare gli hotfix in modalità normale.
- Installare e verificare l'hotfix in modalità di manutenzione.

Per installare l'Aggiornamento 2 come un aggiornamento rapido, è necessario scaricare e installare i seguenti aggiornamenti rapidi:

| Ordine | KB | Descrizione | Tipo di aggiornamento |
|--------|-----------|-------------------------|------------- |
| 1 | KB3121901 | Aggiornamento software | Normale |
| 2 | KB3121900 | Driver LSI | Normale |
| 3 | KB3080728 | Correzione Storport </br> Windows Server 2012 R2 | Normale |
| 4 | KB3090322 | Correzione Spaceport </br> Windows Server 2012 R2 | Normale |
| 5 | KB3121899 | Firmware del disco | Manutenzione |


> [AZURE.IMPORTANT]
>
> - Se il dispositivo esegue la versione finale (GA), contattare [il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md) per assistenza relativa all'aggiornamento.
> - Questa procedura deve essere eseguita solo una volta per applicare l'aggiornamento 2. È possibile utilizzare il portale di Azure classico per applicare gli aggiornamenti successivi.
> - Ogni installazione di hotfix richiede circa 20 minuti per completare. Tempo totale di installazione di quasi 2 ore.
> - Prima di usare questa procedura per applicare l'aggiornamento, assicurarsi che entrambi i controller di dispositivo siano online e che tutti i componenti hardware siano integri.

Eseguire i seguenti passaggi per applicare l'aggiornamento come un aggiornamento rapido.

[AZURE.INCLUDE [storsimple-install-update2-hotfix](../../includes/storsimple-install-update2-hotfix.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]



## Passaggi successivi

Altre informazioni sulla [versione dell'aggiornamento 2](storsimple-update2-release-notes.md).

<!---HONumber=AcomDC_0921_2016-->