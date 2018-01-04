---
title: 'Azure AD Connect: Come eseguire il ripristino dal limite di 10 GB per LocalDB | Microsoft Docs'
description: Questo argomento descrive come eseguire il ripristino del servizio di sincronizzazione Azure AD Connect quando incontra il problema del limite di 10 GB per LocalDB.
services: active-directory
documentationcenter: 
author: cychua
manager: mtillman
editor: 
ms.assetid: 41d081af-ed89-4e17-be34-14f7e80ae358
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: billmath
ms.openlocfilehash: 7b54461a58fb6b60d0686743f90b6c85d7819f1f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-connect-how-to-recover-from-localdb-10-gb-limit"></a>Azure AD Connect: Come eseguire il ripristino dal limite di 10 GB per LocalDB
Per archiviare i dati sull'identità, Azure AD Connect richiede un database SQL. È possibile usare l'istanza predefinita di SQL Server 2012 Express LocalDB installata con Azure AD Connect oppure usare la versione di SQL completa. SQL Server Express impone un limite di 10 GB. Quando si usa LocalDB e viene raggiunto questo limite, il servizio di sincronizzazione Azure AD Connect non può più essere avviato o eseguire la sincronizzazione correttamente. Questo articolo illustra la procedura di ripristino.

## <a name="symptoms"></a>Sintomi
Esistono due sintomi comuni:

* Il servizio di sincronizzazione Azure AD Connect **è in esecuzione** ma non effettua la sincronizzazione e genera l'errore *"stopped-database-disk-full"*.

* Il servizio di sincronizzazione Azure AD Connect **non può essere avviato**. Quando si prova ad avviare il servizio, l'operazione non riesce e vengono generati l'evento 6323 il messaggio di errore *"Errore del server. Spazio su disco insufficiente per SQL Server".*

## <a name="short-term-recovery-steps"></a>Procedura di ripristino a breve termine
Questa sezione illustra come recuperare lo spazio del DB necessario per ripristinare il funzionamento del servizio di sincronizzazione Azure AD Connect. I passaggi includono:
1. [Determinare lo stato del servizio di sincronizzazione](#determine-the-synchronization-service-status)
2. [Compattare il database](#shrink-the-database)
3. [Eliminare i dati della cronologia di esecuzione](#delete-run-history-data)
4. [Abbreviare il periodo di conservazione dei dati della cronologia di esecuzione](#shorten-retention-period-for-run-history-data)

### <a name="determine-the-synchronization-service-status"></a>Determinare lo stato del servizio di sincronizzazione
Determinare prima se il servizio di sincronizzazione è ancora in esecuzione:

1. Accedere al server di Azure AD Connect come amministratore.

2. Passare a **Gestione controllo servizi**.

3. Verificare lo stato di **Microsoft Azure AD Sync**.


4. Se è in esecuzione, non arrestare o riavviare il servizio. Ignorare il passaggio [Compattare il database](#shrink-the-database) e proseguire con il passaggio [Eliminare i dati della cronologia di esecuzione](#delete-run-history-data).

5. Se non è in esecuzione, provare ad avviare il servizio. Se il servizio si avvia, ignorare il passaggio [Compattare il database](#shrink-the-database) e proseguire con il passaggio [Eliminare i dati della cronologia di esecuzione](#delete-run-history-data). In caso contrario, proseguire con il passaggio [Compattare il database](#shrink-the-database).

### <a name="shrink-the-database"></a>Compattare il database
Usare l'operazione di compattazione per liberare spazio del database sufficiente per avviare il servizio di sincronizzazione. Lo spazio verrà liberato rimuovendo gli spazi vuoti presenti nel database. Si tratta del miglior tentativo possibile, dato che non è certo che sia sempre possibile recuperare lo spazio. Per altre informazioni sull'operazione di compattazione, vedere l'articolo [Compattare un database](https://msdn.microsoft.com/library/ms189035.aspx).

> [!IMPORTANT]
> Ignorare questo passaggio se è possibile eseguire il servizio di sincronizzazione. Non è consigliabile compattare il database SQL, perché può causare una riduzione delle prestazioni a causa di una maggiore frammentazione.

Il nome del database creato per Azure AD Connect è **ADSync**. Per eseguire un'operazione di compattazione è necessario accedere come amministratore di sistema o DBO del database. Durante l'installazione di Azure AD Connect vengono concessi diritti di amministratore di sistema agli account seguenti:
* Amministratori locali
* Account utente usato per eseguire l'installazione di Azure AD Connect.
* Account del servizio di sincronizzazione usato come contesto operativo del servizio di sincronizzazione Azure AD Connect.
* Gruppo locale ADSyncAdmins creato durante l'installazione.

1. Eseguire il backup del database copiando in una posizione sicura i file **ADSync.mdf** e **ADSync_log.ldf** presenti in `%ProgramFiles%\Microsoft Azure AD Sync\Data`.

2. Avviare una nuova sessione di PowerShell.

3. Passare alla cartella `%ProgramFiles%\Microsoft SQL Server\110\Tools\Binn`.

4. Avviare l'utilità **sqlcmd** eseguendo il comando `./SQLCMD.EXE -S “(localdb)\.\ADSync” -U <Username> -P <Password>`, usando le credenziali di un amministratore di sistema o del DBO del database.

5. Per compattare il database, al prompt di sqlcmd (1>) immettere `DBCC Shrinkdatabase(ADSync,1);` seguito da `GO` nella riga successiva.

6. Se l'operazione ha esito positivo, provare nuovamente ad avviare il servizio di sincronizzazione. Se è possibile avviare il servizio di sincronizzazione, proseguire con il passaggio [Eliminare i dati della cronologia di esecuzione](#delete-run-history-data). In caso contrario, contattare il supporto tecnico.

### <a name="delete-run-history-data"></a>Eliminare i dati della cronologia di esecuzione
Per impostazione predefinita, Azure AD Connect conserva i dati della cronologia di esecuzione per sette giorni. In questo passaggio si eliminano i dati della cronologia di esecuzione per recuperare spazio del database e riavviare il servizio di sincronizzazione Azure AD Connect.

1.  Avviare **Synchronization Service Manager** passando a START → Synchronization Service (Servizio di sincronizzazione).

2.  Passare alla scheda **Operations** (Operazioni).

3.  In **Actions** (Azioni) selezionare **Clear Runs** (Cancella esecuzioni).

4.  È possibile scegliere **Clear all runs** (Cancella tutte le esecuzioni) oppure **Clear runs before<date>** (Cancella esecuzioni precedenti a). È consigliabile iniziare cancellando i dati della cronologia di esecuzione che risalgono a più di due giorni di prima. Se il problema delle dimensioni del database persiste, scegliere l'opzione **Clear all runs** (Cancella tutte le esecuzioni).

### <a name="shorten-retention-period-for-run-history-data"></a>Abbreviare il periodo di conservazione dei dati della cronologia di esecuzione
Questo passaggio consente di ridurre la probabilità che si verifichi il problema del limite di 10 GB dopo più cicli di sincronizzazione.

1. Aprire una nuova sessione di PowerShell.

2. Eseguire `Get-ADSyncScheduler` e prendere nota della proprietà PurgeRunHistoryInterval, che specifica il periodo di conservazione corrente.

3. Eseguire `Set-ADSyncScheduler -PurgeRunHistoryInterval 2.00:00:00` per impostare il periodo di conservazione di due giorni. Modificare il periodo di conservazione secondo le esigenze.

## <a name="long-term-solution--migrate-to-full-sql"></a>Soluzione a lungo termine: migrazione alla versione di SQL completa
In generale, il problema indica che le dimensioni del database di 10 GB non sono più sufficienti per eseguire la sincronizzazione dall'istanza di Active Directory locale ad Azure AD tramite Azure AD Connect. È consigliabile passare alla versione completa di SQL Server. Non è possibile sostituire direttamente il database locale di una distribuzione di Azure AD Connect esistente con il database della versione completa di SQL. È invece necessario distribuire un nuovo server di Azure AD Connect con la versione completa di SQL. È consigliabile eseguire una migrazione swing in cui il nuovo server di Azure AD Connect (con il database SQL) viene distribuito come server di staging, accanto al server di Azure AD Connect esistente (con LocalDB). 
* Per istruzioni su come configurare un'istanza SQL remota con Azure AD Connect, vedere l'articolo [Installazione personalizzata di Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-custom).
* Per istruzioni sulla migrazione swing per l'aggiornamento di Azure AD Connect, vedere l'articolo [Azure AD Connect: Eseguire l'aggiornamento da una versione precedente alla versione più recente](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#swing-migration).

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).
