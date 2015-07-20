<properties
   pageTitle="Aggiornare il dispositivo StorSimple"
   description="Viene illustrato come utilizzare la funzionalità di aggiornamento StorSimple per installare gli aggiornamenti rapidi in modalità normale e manutenzione."
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
   ms.date="06/15/2015"
   ms.author="v-sharos" />

# Aggiornare il dispositivo StorSimple

## Panoramica

La funzionalità di aggiornamento StorSimple consentono di tenere aggiornato il dispositivo StorSimple. A seconda del tipo di aggiornamento, è possibile applicare gli aggiornamenti al dispositivo tramite il portale di gestione di Microsoft Azure o tramite l'interfaccia Windows PowerShell. In questa esercitazione vengono descritti i tipi di aggiornamento e come installarli.

È possibile applicare due tipi di aggiornamenti del dispositivo:

- Aggiornamenti regolari (o in modalità normale)
- Aggiornamenti in modalità manutenzione

È possibile installare aggiornamenti regolari tramite il portale di gestione o Windows PowerShell; tuttavia, è necessario utilizzare Windows PowerShell per installare gli aggiornamenti in modalità manutenzione.

Di seguito viene separatamente descritto ogni tipo di aggiornamento.

### Aggiornamenti regolari

Gli aggiornamenti regolari sono aggiornamenti senza interruzioni che possono essere installati quando il dispositivo è in modalità normale. Questi aggiornamenti vengono applicati tramite il sito Web Microsoft Update per ogni controller del dispositivo.

> [AZURE.IMPORTANT]Durante il processo di aggiornamento può verificarsi un failover del controller. Tuttavia, questa operazione non modificherà la disponibilità o l'operatività del sistema.

- Per informazioni dettagliate su come installare gli aggiornamenti regolari tramite il portale di gestione, vedere [Installare gli aggiornamenti regolari tramite il portale di gestione](#install-regular-updates-via-the-management-portal).

- È anche possibile installare aggiornamenti regolari tramite Windows PowerShell per StorSimple. Per dettagli, vedere [Installare aggiornamenti regolari tramite Windows PowerShell per StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple)

### Aggiornamenti in modalità manutenzione

Gli aggiornamenti in modalità manutenzione sono aggiornamenti problematici, come gli aggiornamenti del firmware del disco o del firmware USM. Questi aggiornamenti richiedono che il dispositivo sia in modalità manutenzione. Per altri dettagli, vedere [Passaggio 2: Attivare la modalità di manutenzione](#to-enter-maintenance-mode). Per installare gli aggiornamenti in modalità manutenzione, non è possibile utilizzare il portale di gestione. Pertanto è necessario utilizzare Windows PowerShell per StorSimple.

Per informazioni dettagliate su come installare gli aggiornamenti in modalità manutenzione, vedere [Installare gli aggiornamenti in modalità manutenzione tramite Windows PowerShell per StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [AZURE.IMPORTANT]Gli aggiornamenti in modalità manutenzione devono essere applicati separatamente a ogni controller.

## Installare gli aggiornamenti regolari tramite il portale di gestione

È possibile utilizzare il portale di gestione per applicare gli aggiornamenti al dispositivo StorSimple.


[AZURE.INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## Installare aggiornamenti regolari tramite Windows PowerShell per StorSimple

In alternativa, è possibile utilizzare Windows PowerShell per StorSimple per applicare gli aggiornamenti regolari (modalità normale).

> [AZURE.IMPORTANT]Sebbene sia possibile installare aggiornamenti regolari tramite Windows PowerShell per StorSimple, è consigliabile installare gli aggiornamenti periodici tramite il portale di gestione. A partire dall'aggiornamento 1, vengono eseguiti controlli preliminari prima di installare gli aggiornamenti dal portale, che consentono di prevenire errori e garantiscono un'esperienza più lineare.

[AZURE.INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## Per installare gli aggiornamenti in modalità manutenzione tramite Windows PowerShell per StorSimple

Utilizzare Windows PowerShell per StorSimple per applicare gli aggiornamenti in modalità manutenzione al dispositivo StorSimple. In questa modalità, tutte le richieste I/O sono sospese. Inoltre vengono arrestati servizi come la memoria ad accesso casuale non volatile (NVRAM) o il servizio cluster. Entrambi i controller vengono riavviati quando si entra o esce dalla modalità. Quando si esce da questa modalità, tutti i servizi vengono ripresi e devono essere integri. L'operazione potrebbe richiedere alcuni minuti.

Se è necessario applicare gli aggiornamenti in modalità manutenzione, si riceverà un avviso tramite il portale di gestione riguardo gli aggiornamenti che devono essere installati. Questo avviso include le istruzioni sull'utilizzo di Windows PowerShell per StorSimple per installare gli aggiornamenti. Dopo aver aggiornato il dispositivo, utilizzare la stessa procedura per modificare il dispositivo alla modalità normale. Per istruzioni dettagliate, vedere [Passaggio 4: Uscire dalla modalità di manutenzione](#to-exit-maintenance-mode).

> [AZURE.IMPORTANT]
> 
> - Prima di entrare in modalità manutenzione, verificare che entrambi i controller di dispositivi siano integri controllando lo **Stato Hardware** sulla pagina **Manutenzione** nel portale di gestione. Se il controller non è integro, contattare il supporto tecnico Microsoft per i passaggi successivi. Per altre informazioni, contattare il supporto tecnico Microsoft. 
> - In modalità manutenzione, è necessario applicare l’aggiornamento rapido prima in un controller e poi nell’altro.

### Passaggio 1: Connettersi alla console seriale

In primo luogo, utilizzare un'applicazione come PuTTY per accedere alla console seriale. La procedura seguente illustra come utilizzare PuTTY per connettersi alla console seriale.

[AZURE.INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### Passaggio 2: Attivare la modalità di manutenzione

Dopo la connessione alla console, determinare se sono presenti aggiornamenti da installare e attivare la modalità manutenzione per installarli.

[AZURE.INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### Passaggio 3: Installare gli aggiornamenti

Successivamente, installare gli aggiornamenti.

[AZURE.INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]

    
### Passaggio 4: Uscire dalla modalità di manutenzione

Per terminare, uscire dalla modalità manutenzione.

[AZURE.INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## Installare gli aggiornamenti rapidi tramite Windows PowerShell per StorSimple

A differenza degli aggiornamenti per Microsoft Azure StorSimple, gli aggiornamenti rapidi vengono installati da una cartella condivisa. Come con gli aggiornamenti, sono disponibili due tipi di aggiornamenti rapidi:

- Aggiornamenti rapidi regolari 
- Aggiornamenti rapidi in modalità manutenzione  

Nelle procedure seguenti viene illustrato come utilizzare Windows PowerShell per StorSimple per installare aggiornamenti rapidi regolari e di modalità manutenzione.

[AZURE.INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[AZURE.INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## Cosa accade agli aggiornamenti se si esegue un ripristino delle impostazioni predefinite?

Se il dispositivo viene ripristinato alle impostazioni predefinite, tutti gli aggiornamenti vengono persi. Dopo aver registrato e configurato il dispositivo ripristinato alle impostazioni predefinite, sarà necessario installare manualmente gli aggiornamenti tramite il portale di gestione e/o Windows PowerShell per StorSimple. Per altre informazioni sul ripristino delle impostazioni predefinite, vedere [Ripristinare le impostazioni di fabbrica del dispositivo](https://msdn.microsoft.com/library/azure/dn772373.aspx).

## Passaggi successivi

Altre informazioni su [Windows PowerShell per StorSimple](https://msdn.microsoft.com/library/azure/dn772425.aspx).
 

<!---HONumber=July15_HO2-->