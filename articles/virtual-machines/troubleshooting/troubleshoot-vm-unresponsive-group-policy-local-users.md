---
title: La macchina virtuale non risponde durante l'applicazione dei criteri ' Criteri di gruppo utenti locali & gruppi '
description: Questo articolo illustra la procedura per risolvere i problemi in cui la schermata di caricamento è bloccata applicando un criterio durante l'avvio in una macchina virtuale (VM) di Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80620857"
---
# <a name="virtual-machine-is-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>La macchina virtuale non risponde durante l'applicazione dei criteri ' Criteri di gruppo utenti locali & gruppi '

Questo articolo illustra la procedura per risolvere i problemi in cui la schermata di caricamento è bloccata applicando un criterio, durante l'avvio, in una macchina virtuale (VM) di Azure.

## <a name="symptom"></a>Sintomo

Quando si usa la [diagnostica di avvio](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) per visualizzare lo screenshot della macchina virtuale, si noterà che lo schermo è bloccato con il messaggio: *applicazione di criteri di gruppo criteri per utenti e gruppi locali*.

![Testo alternativo: schermata che mostra l'applicazione di Criteri di gruppo il caricamento dei criteri per utenti e gruppi locali (Windows Server 2012).](media/troubleshoot-vm-unresponsive-group-policy-local-users/1.png)

Windows Server 2012

![Testo alternativo: schermata che mostra l'applicazione di Criteri di gruppo il caricamento dei criteri per utenti e gruppi locali (Windows Server 2012 R2).](media/troubleshoot-vm-unresponsive-group-policy-local-users/2.png)

Windows Server 2012 R2

## <a name="cause"></a>Causa

I sintomi di questo blocco sono causati da un difetto del codice nella libreria a collegamento dinamico del servizio profili Windows (*profsvc. dll*).

> [!NOTE]
> Questo difetto si applica solo a Windows Server 2012 e Windows Server 2012 R2.

### <a name="the-policy-in-question"></a>Criteri in questione

Il criterio applicato non completerà i processi seguenti:

* *Modelli Configurazione computer\Modelli \ System/User Profiles\Delete profili utente anteriori a un numero specificato di giorni al riavvio del sistema*

Questo criterio si bloccherà solo se le sei condizioni seguenti sono vere:

* I *profili utente Delete più vecchi di un numero specificato di giorni nei criteri di riavvio del sistema* sono abilitati.
* Sono presenti profili che soddisfano i requisiti di validità per richiedere la pulizia.
* Sono presenti componenti che sono stati registrati per la notifica di eliminazione per i profili.
* I componenti eseguono chiamate (dirette o indirette) che devono acquisire dati dai componenti di gestione controllo servizi di Windows, ad esempio l'avvio, l'arresto o l'esecuzione di query sulle informazioni relative a un servizio.
* Si dispone di un servizio configurato per l'avvio *automatico*.
* Questo servizio è impostato per essere eseguito nel contesto di un account di dominio (a differenza dell'utilizzo di un account predefinito, ad esempio un sistema locale).

### <a name="the-code-defect"></a>Difetto del codice

L'errore del codice è dovuto a gestione controllo servizi e ai servizi del profilo che tentano di applicare i blocchi l'uno sull'altro simultaneamente. Sono presenti blocchi per evitare che più servizi modifichino contemporaneamente gli stessi dati, cosa che provocherebbe un danneggiamento. In genere, più richieste di blocco non causano un problema. Tuttavia, poiché questa situazione si verifica durante l'avvio, nessuno dei due è in grado di completare i processi, perché sono bloccati in attesa di un altro utente.

### <a name="os-bug-5880648---service-control-manager-deadlocks-with-the-delete-user-profiles-on-restart-policy"></a>Bug del sistema operativo 5880648-deadlock di gestione controllo servizi con il criterio "Elimina profili utente al riavvio"

Esistono due azioni che si sovrappongono in modo che:

* L'azione 1 acquisisce il blocco del profilo, ma non ha ancora acquisito il blocco SCM.

  **E**

* L'azione 2 acquisisce il blocco SCM ma non ha ancora acquisito il blocco del profilo.

Quando si verifica questo deadlock, il tentativo di acquisire il secondo blocco obbligatorio blocca l'azione.

### <a name="action-1---old-profile-deletion-notification-has-profile-lock-needs-scm-lock"></a>Azione 1-notifica di eliminazione del profilo precedente (con **blocco del profilo**, necessario **blocco SCM**)

1. Per prima cosa, il set di criteri per eliminare i profili obsoleti acquisisce un blocco del servizio del profilo interno.

   * Questo blocco impedisce a due thread di interagire con i profili mentre è in corso l' *operazione di eliminazione* .

2. Il criterio individua i profili sufficientemente vecchi da eliminare.
3. Nell'ambito dell'eliminazione del profilo, un componente che si è registrato per le notifiche delle eliminazioni di un profilo tenta di **avviare un servizio**.
4. Prima di avviare il servizio, gestione controllo servizi deve acquisire un **blocco SCM interno** mantenuto dai thread nell' **azione 2**.

### <a name="action-2---profile-loadcreation-for-user-specific-data-has-scm-lock-needs-profile-lock"></a>Azione 2-Caricamento/creazione del profilo per dati specifici dell'utente (con **blocco SCM**, necessità del **blocco del profilo**)

1. Al momento dell'avvio, SCM deve ordinare tutti i servizi di *avvio automatico* in base al proprio gruppo, nonché a tutti i servizi da cui dipendono tali servizi.

2. **SCM acquisisce un blocco SCM interno** usato per controllare l'accesso a avvio, arresto o configurazione dei servizi durante l'ordinamento dei servizi.

3. Una volta che i servizi sono in ordine, il controllo SCM esegue il ciclo di ogni servizio e lo avvia.

4. Se il servizio viene eseguito nel contesto di un account di dominio, è necessario caricare o creare un profilo per l'account di dominio, in modo che possa archiviare dati specifici dell'utente.

5. Questa richiesta viene inviata al **servizio profili**.

6. Il servizio profili deve accedere al **blocco interno** acquisito nell' **azione 1**.

## <a name="solution"></a>Soluzione

### <a name="process-overview"></a>Panoramica del processo

1. Creare e accedere a una macchina virtuale di ripristino
2. Abilitare la raccolta di immagini di memoria e console seriale
3. Ricompilare la macchina virtuale
4. Raccogli il file di dump della memoria

   > [!NOTE]
   > Quando si verifica questo errore di avvio, il sistema operativo guest non è operativo. Per risolvere il problema, è necessario eseguire la risoluzione dei problemi in modalità offline.

### <a name="create-and-access-a-repair-vm"></a>Creare e accedere a una macchina virtuale di ripristino

1. Usare [i passaggi 1-3 dei comandi di ripristino della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) per preparare una macchina virtuale di ripristino.
2. Usare Connessione Desktop remoto connettersi alla macchina virtuale di ripristino.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Abilitare la raccolta di immagini di memoria e console seriale

Per abilitare la raccolta di dump della memoria e la console seriale, eseguire lo script seguente:

1. Aprire una sessione del prompt dei comandi con privilegi elevati (Esegui come amministratore).
2. Eseguire i comandi seguenti:

   * Abilita console seriale:

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. Verificare che lo spazio disponibile sul disco del sistema operativo sia pari alla dimensione della memoria (RAM) nella macchina virtuale.

   * Se lo spazio sul disco del sistema operativo non è sufficiente, è necessario modificare il percorso in cui verrà creato il file di dump della memoria e fare riferimento a qualsiasi disco dati collegato alla VM con spazio libero sufficiente. Per modificare il percorso, sostituire `%SystemRoot%` con la lettera di unità (ad esempio "F:") del disco dati nei comandi seguenti.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Configurazione consigliata per abilitare il dump del sistema operativo

**Carica disco del sistema operativo danneggiato:**

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Abilita in ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Abilita in ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

### <a name="rebuild-the-vm"></a>Ricompilare la macchina virtuale

Usare [il passaggio 5 dei comandi di ripristino della macchina](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) virtuale per riassemblare la macchina virtuale.

### <a name="collect-the-memory-dump-file"></a>Raccogli il file di dump della memoria

Per risolvere il problema, è necessario innanzitutto raccogliere il file di dump della memoria per l'arresto anomalo e contattare il supporto tecnico con il file di dump della memoria. Per raccogliere il file di dump, attenersi alla procedura seguente:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Connetti il disco del sistema operativo a una nuova macchina virtuale di ripristino

1. Usare [i passaggi 1-3 dei comandi di ripristino della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) per preparare una nuova macchina virtuale di ripristino.

2. Usare Connessione Desktop remoto connettersi alla macchina virtuale di ripristino.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Individuare il file dump e inviare un ticket di supporto

1. Nella macchina virtuale di ripristino andare alla cartella Windows nel disco del sistema operativo collegato. Se la lettera di unità assegnata al disco del sistema operativo collegato è F, è necessario passare a F:\Windows.

2. Individuare il file Memory. dmp, quindi [inviare un ticket di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) con il file di dump della memoria.

3. Se si verificano problemi durante l'individuazione del file Memory. dmp, è possibile usare invece le [chiamate di interrupt non mascherabili (NMI) nella console seriale](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) . È possibile seguire la guida per [generare un kernel o completare un file di dump di arresto anomalo](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump) del sistema usando le chiamate a NMI.
