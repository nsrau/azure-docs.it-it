---
title: La macchina virtuale non risponde durante l'applicazione del criterio 'Gruppi & utenti locali di Criteri di gruppo'
description: Questo articolo illustra la procedura per risolvere i problemi in cui la schermata di caricamento è bloccata applicando un criterio durante l'avvio in una macchina virtuale di Azure.This article provides steps to resolve issues where the load screen is stuck applying a policy during boot in an Azure Virtual Machine (VM).
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: ff113268-f5bf-4e6a-986e-63b9b0ceff20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 04/02/2020
ms.author: v-mibufo
ms.openlocfilehash: 085880122e9a80e976cfe59686748b58aeba1922
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80620857"
---
# <a name="virtual-machine-is-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>La macchina virtuale non risponde durante l'applicazione del criterio 'Gruppi & utenti locali di Criteri di gruppo'

Questo articolo illustra la procedura per risolvere i problemi in cui la schermata di caricamento è bloccata applicando un criterio, durante l'avvio, in una macchina virtuale di Azure.This article provides steps to resolve issues where the load screen is stuck a pplying a policy, during boot, in an Azure Virtual Machine (VM).

## <a name="symptom"></a>Sintomo

Quando si utilizza diagnostica di [avvio](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) per visualizzare la schermata della macchina virtuale, si noterà che la schermata è bloccata il caricamento con il messaggio: Applicazione dei *criteri di gruppo utenti e gruppi locali*.

![Testo alternativo: schermata che mostra l'applicazione dei criteri Utenti e gruppi locali di Criteri di gruppo (Windows Server 2012).](media/troubleshoot-vm-unresponsive-group-policy-local-users/1.png)

Windows Server 2012

![Testo alternativo: schermata che mostra l'applicazione dei criteri Utenti e gruppi locali di Criteri di gruppo (Windows Server 2012 R2).](media/troubleshoot-vm-unresponsive-group-policy-local-users/2.png)

Windows Server 2012 R2

## <a name="cause"></a>Causa

I sintomi di questo blocco sono causati da un difetto del codice nella libreria a collegamento dinamico del servizio profili di Windows (*profsvc.dll*).

> [!NOTE]
> Questo difetto si applica solo in Windows Server 2012 e Windows Server 2012 R2.

### <a name="the-policy-in-question"></a>La politica in questione

Il criterio applicato che non termina i processi è:

* *Configurazione computer, Criteri, Modelli amministrativi, Sistema/Profili utente, Elimina i profili utente anteriori a un numero di giorni specificato al riavvio del sistema*

Questo criterio si bloccherà solo se sono vere le sei condizioni seguenti:

* Il criterio *Elimina profili utente anteriori a un numero specificato di giorni al riavvio* del sistema è abilitata.
* Si dispone di profili che hanno soddisfatto i requisiti di età per richiedere la pulizia.
* Si dispone di componenti che sono stati registrati per la notifica di eliminazione per i profili.
* I componenti effettuano chiamate (dirette o indirette) che devono acquisire dati dai componenti Gestione controllo servizi (SCM) di Windows, ad esempio Start, Arresta o Esegui query su un servizio.
* Si dispone di un servizio configurato per l'avvio come *automatico*.
* Questo servizio è impostato per l'esecuzione nel contesto di un account di dominio (anziché utilizzare un account predefinito, ad esempio un sistema locale).

### <a name="the-code-defect"></a>Il difetto del codice

Il difetto del codice è dovuto a Gestione controllo servizi (SCM) e ai servizi profili che tentano di applicare i blocchi l'uno all'altro contemporaneamente. Esistono blocchi per impedire a più servizi di apportare modifiche sugli stessi dati contemporaneamente, con conseguenti danneggiamenti. In genere, più richieste di blocco non causerebbero un problema. Tuttavia, poiché questo accade durante l'avvio, nessuno dei due servizi può completare i processi, in quanto sono bloccati l'uno dall'altro.

### <a name="os-bug-5880648---service-control-manager-deadlocks-with-the-delete-user-profiles-on-restart-policy"></a>OS Bug 5880648 - deadlock di Gestione controllo servizi con il criterio "Elimina profili utente al riavvio"

Ci sono due azioni che si sovrappongono in modo che:

* L'azione 1 acquisisce il blocco del profilo ma non ha ancora acquisito il blocco SCM.

  **AND**

* Azione 2 acquisisce il blocco SCM ma non ha ancora acquisito il blocco del profilo.

Quando si verifica questo deadlock, il tentativo di acquisire il secondo blocco richiesto blocca l'azione.

### <a name="action-1---old-profile-deletion-notification-has-profile-lock-needs-scm-lock"></a>Azione 1 - Notifica di eliminazione del profilo precedente (con **blocco profilo**, ha bisogno di **Blocco SCM**)

1. In primo luogo, il criterio impostato per eliminare i profili precedenti acquisisce un blocco del servizio profili interno.

   * Questo blocco è presente per impedire a due thread di interagire con i profili mentre *l'operazione* di eliminazione è in corso.

2. Il criterio trova profili abbastanza vecchi da essere eliminati.
3. Come parte dell'eliminazione del profilo, un componente registrato per le notifiche delle eliminazioni di un profilo tenta di **avviare un servizio.**
4. Prima di avviare il servizio, Gestione controllo servizi (SCM) deve acquisire un **blocco SCM interno** mantenuto dai thread **nell'azione 2**.

### <a name="action-2---profile-loadcreation-for-user-specific-data-has-scm-lock-needs-profile-lock"></a>Azione 2 - Caricamento/creazione del profilo per dati specifici dell'utente (con **blocco SCM**, richiede **blocco profilo)**

1. All'avvio, SCM deve ordinare tutti i servizi di *avvio automatico* dal proprio gruppo, nonché tutti i servizi da cui dipendono tali servizi.

2. **Gestione controllo servizi acquisisce un blocco Interno** di Gestione controllo servizi utilizzato per controllare l'accesso all'avvio, all'arresto o alla configurazione dei servizi durante l'ordine dei servizi.

3. Una volta che i servizi sono in ordine, il controllo gestione richieste di gestione file esegue un ciclo in ogni servizio e lo avvia.

4. Se il servizio viene eseguito nel contesto di un account di dominio, è necessario caricare o creare un profilo per l'account di dominio, in modo da poter archiviare dati specifici dell'utente.

5. Questa richiesta viene inviata al **servizio profili**.

6. Il servizio profili deve accedere al **blocco interno** acquisito **nell'azione 1**.

## <a name="solution"></a>Soluzione

### <a name="process-overview"></a>Panoramica del processo

1. Creare e accedere a una macchina virtuale di ripristinoCreate and Access a Repair VM
2. Abilita la console di serializzazione e la raccolta di dump di memoria
3. Ricompilare la macchina virtualeRebuild the VM
4. Raccogliere il file di dump della memoriaCollect the Memory Dump File

   > [!NOTE]
   > Quando si verifica questo errore di avvio, il sistema operativo guest non è operativo. La risoluzione dei problemi in modalità non in linea verrà tramite la risoluzione di questo problema.

### <a name="create-and-access-a-repair-vm"></a>Creare e accedere a una macchina virtuale di ripristinoCreate and Access a Repair VM

1. Usare [i passaggi da 1 a 3 dei comandi](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) di ripristino della macchina virtuale per preparare una macchina virtuale di ripristino.
2. L'uso di Connessione desktop remoto si connette alla macchina virtuale di ripristino.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Abilita la console di serializzazione e la raccolta di dump di memoria

Per abilitare la raccolta delle informazioni di immagine della memoria e la console seriale, eseguire lo script seguente:

1. Aprire una sessione del prompt dei comandi con privilegi elevati (Esegui come amministratore).
2. Eseguire i comandi seguenti:

   * Abilita console seriale:

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. Verificare che lo spazio disponibile sul disco del sistema operativo sia tanto quanto la dimensione della memoria (RAM) nella macchina virtuale.

   * Se lo spazio sul disco del sistema operativo non è sufficiente, è necessario modificare il percorso in cui verrà creato il file di immagine della memoria e fare riferimento a qualsiasi disco dati collegato alla macchina virtuale che dispone di spazio libero sufficiente. Per modificare la `%SystemRoot%` posizione, sostituire con la lettera di unità (ad esempio "F:") del disco dati nei comandi seguenti.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Configurazione consigliata per abilitare il dump del sistema operativo

**Carica disco del sistema operativo interrotto:**

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Abilita su ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Abilita su ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

### <a name="rebuild-the-vm"></a>Ricompilare la macchina virtualeRebuild the VM

Usare [il passaggio 5 dei comandi](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) di ripristino della macchina virtuale per riassemblare la macchina virtuale.

### <a name="collect-the-memory-dump-file"></a>Raccogliere il file di dump della memoriaCollect the Memory Dump File

Per risolvere questo problema, è necessario innanzitutto raccogliere il file di immagine della memoria per l'arresto anomalo e contattare il supporto con il file di immagine della memoria. Per raccogliere il file dump, attenersi alla seguente procedura:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Collegare il disco del sistema operativo a una nuova macchina virtuale di ripristino

1. Usare i passaggi [da 1 a 3 dei comandi](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) di ripristino della macchina virtuale per preparare una nuova macchina virtuale di ripristino.

2. L'uso di Connessione desktop remoto si connette alla macchina virtuale di ripristino.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Individuare il file dump e inviare un ticket di supporto

1. Nella macchina virtuale di ripristino passare alla cartella Windows nel disco del sistema operativo collegato. Se la lettera di unità assegnata al disco del sistema operativo collegato è F, è necessario passare a F:\Windows.

2. Individuare il file memory.dmp e quindi [inviare un ticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) di supporto con il file di immagine della memoria.

3. Se si verificano problemi nell'individuazione del file memory.dmp, è possibile utilizzare [chiamate di interrupt (NMI) non mascherabili nella console seriale.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) È possibile seguire la guida per [generare un kernel o un](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump) file di dump di arresto anomalo del sistema utilizzando le chiamate NMI.
