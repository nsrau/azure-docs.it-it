---
title: Modifica sul posto della versione SQL Server
description: Informazioni su come modificare la versione di SQL Server macchina virtuale in Azure.
services: virtual-machines-windows
documentationcenter: na
author: ramakoni1
manager: ramakoni1
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/08/2020
ms.author: RamaKoni
ms.reviewer: sqlblt, daleche
ms.custom: seo-lt-2019
ms.openlocfilehash: a0ecc36d78ffde002dac971a749889104ff10073
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94556455"
---
# <a name="in-place-change-of-sql-server-version-on-azure-vm"></a>Modifica sul posto della versione SQL Server in una macchina virtuale di Azure

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo articolo descrive come modificare la versione di Microsoft SQL Server in una macchina virtuale (VM) Windows in Microsoft Azure.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire un aggiornamento sul posto delle SQL Server, si applicano le condizioni seguenti:

- Il supporto di installazione della versione desiderata di SQL Server è obbligatorio. I clienti che usano [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) possono ottenere il supporto di installazione dal [Centro per i contratti multilicenza](https://www.microsoft.com/Licensing/servicecenter/default.aspx). I clienti che non hanno Software Assurance possono usare il supporto di installazione di Azure Marketplace SQL Server immagine di macchina virtuale con una versione più recente di SQL Server, in genere disponibile in C:\SQLServerFull.
- Gli aggiornamenti dell'edizione devono seguire i [percorsi di aggiornamento del supporto](/sql/database-engine/install-windows/supported-version-and-edition-upgrades-version-15).

## <a name="planning-for-version-change"></a>Pianificazione della modifica della versione

Prima di eseguire la modifica della versione, è consigliabile esaminare gli elementi seguenti:

1. Verificare le novità della versione a cui si intende eseguire l'aggiornamento:

   - Novità di [SQL 2019](/sql/sql-server/what-s-new-in-sql-server-ver15)
   - Novità di [SQL 2017](/sql/sql-server/what-s-new-in-sql-server-2017)
   - Novità di [SQL 2016](/sql/sql-server/what-s-new-in-sql-server-2016)


1. Si consiglia di controllare la [certificazione di compatibilità](/sql/database-engine/install-windows/compatibility-certification) per la versione che si intende modificare in, in modo da poter utilizzare le modalità di compatibilità del database per ridurre al minimo l'effetto dell'aggiornamento.
1. È possibile esaminare gli articoli seguenti per garantire un esito positivo:

   - [Video: modernizzazione SQL Server | Pam Laurora & Pedro Lopes | 20 anni di PASS](https://www.youtube.com/watch?v=5RPkuQHcxxs&feature=youtu.be)
   - [Database Experimentation Assistant per i test AB](/sql/dea/database-experimentation-assistant-overview)
   - [Aggiornamento di database mediante l'Assistente ottimizzazione query](/sql/relational-databases/performance/upgrade-dbcompat-using-qta)
   - [Modificare il livello di compatibilità del database e usare Query Store](/sql/database-engine/install-windows/change-the-database-compatibility-mode-and-use-the-query-store)

## <a name="upgrade-sql-version"></a>Aggiornare la versione di SQL

> [!WARNING]
> Se si aggiorna la versione di SQL Server, il servizio viene riavviato per SQL Server oltre a tutti i servizi associati, ad esempio Analysis Services e R Services.

Per aggiornare la versione di SQL Server, ottenere i supporti di installazione SQL Server per la versione successiva che [supporterà il percorso di aggiornamento](/sql/database-engine/install-windows/supported-version-and-edition-upgrades-version-15) di SQL Server e seguire questa procedura:

1. Prima di avviare il processo, eseguire il backup dei database, inclusi System (eccetto tempdb) e database utente. È anche possibile creare un backup a livello di VM coerente con l'applicazione usando i servizi di backup di Azure.
1. Avviare Setup.exe dal supporto di installazione di SQL Server.
1. L'installazione guidata avvia il centro installazione SQL Server. Per aggiornare un'istanza esistente di SQL Server, selezionare **installazione** nel riquadro di spostamento e quindi selezionare **Aggiorna da una versione precedente di SQL Server**.

   :::image type="content" source="./media/change-sql-server-version/upgrade.png" alt-text="Selezione per l'aggiornamento della versione di SQL Server":::

1. Nella pagina **codice Product Key** selezionare un'opzione per indicare se si intende eseguire l'aggiornamento a un'edizione gratuita di SQL Server o se si dispone di una chiave PID per una versione di produzione del prodotto. Per ulteriori informazioni, vedere [edizioni e funzionalità supportate di SQL Server 2019 (15. x)](/sql/sql-server/editions-and-components-of-sql-server-version-155) e [aggiornamenti di versione ed edizione supportati (SQL Server 2016)](/sql/database-engine/install-windows/supported-version-and-edition-upgrades).
1. Selezionare **Avanti** fino a quando non si raggiunge la pagina inizio **aggiornamento** , quindi selezionare **Aggiorna**. La finestra di installazione potrebbe smettere di rispondere per alcuni minuti mentre la modifica ha effetto. In una pagina **completa** verrà verificato che l'aggiornamento sia stato completato. Per una procedura dettagliata per l'aggiornamento, vedere [la procedura completa](/sql/database-engine/install-windows/upgrade-sql-server-using-the-installation-wizard-setup#procedure).

   :::image type="content" source="./media/change-sql-server-version/complete-page.png" alt-text="Pagina completa":::

Se è stata modificata la SQL Server edizione, oltre alla modifica della versione, aggiornare anche l'edizione e fare riferimento alla sezione **verificare la versione e l'edizione nel portale** per modificare l'istanza di macchina virtuale SQL.

   :::image type="content" source="./media/change-sql-server-version/change-portal.png" alt-text="Modificare i metadati della versione":::

## <a name="downgrade-the-version-of-sql-server"></a>Eseguire il downgrade della versione di SQL Server

Per eseguire il downgrade della versione di SQL Server, è necessario disinstallare completamente SQL Server e reinstallarla nuovamente utilizzando la versione desiderata. Questa operazione è simile a una nuova installazione di SQL Server perché non sarà possibile ripristinare il database precedente da una versione successiva alla versione precedente installata. Sarà necessario ricreare i database da zero. Se è stata modificata anche l'edizione di SQL Server durante l'aggiornamento, modificare la proprietà **Edition** della macchina virtuale SQL Server nel portale di Azure al nuovo valore dell'edizione. Questa operazione Aggiorna i metadati e la fatturazione associati a questa macchina virtuale.

> [!WARNING]
> Il downgrade sul posto del SQL Server non è supportato.

È possibile effettuare il downgrade della versione di SQL Server attenendosi alla seguente procedura:

1. Assicurarsi di non usare funzionalità [disponibili solo nella versione successiva](https://social.technet.microsoft.com/wiki/contents/articles/24222.find-enterprise-only-features-in-your-database.aspx).
1. Eseguire il backup di tutti i database, inclusi System (ad eccezione di tempdb) e database utente.
1. Esportare tutti gli oggetti a livello di server necessari, ad esempio i trigger del server, i ruoli, gli account di accesso, i server collegati, i processi, le credenziali e i certificati.
1. Se non si dispone di script per ricreare i database utente nella versione precedente, è necessario inserire tutti gli oggetti in uno script ed esportare tutti i dati utilizzando BCP.exe, SSIS o DACPAC.

   Assicurarsi di selezionare le opzioni corrette quando si crea uno script di tali elementi come versione di destinazione, oggetti dipendenti e opzioni avanzate.

   :::image type="content" source="./media/change-sql-server-version/scripting-options.png" alt-text="Opzioni di scripting":::

1. Disinstallare completamente SQL Server e tutti i servizi associati.
1. Riavviare la VM.
1. Installare SQL Server utilizzando il supporto per la versione desiderata del programma.
1. Installare i Service Pack e gli aggiornamenti cumulativi più recenti.
1. Importare tutti gli oggetti a livello di server necessari, esportati nel passaggio 3.
1. Ricreare tutti i database utente necessari da zero (usando gli script creati o i file del passaggio 4).

## <a name="verify-the-version-and-edition-in-the-portal"></a>Verificare la versione e l'edizione nel portale

Dopo aver modificato la versione di SQL Server, registrare di nuovo la VM SQL Server con l' [estensione SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md) in modo che sia possibile usare la portale di Azure per visualizzare la versione di SQL Server. Il numero di versione elencato dovrebbe ora riflettere la versione e l'edizione appena aggiornate dell'installazione del SQL Server.

:::image type="content" source="./media/change-sql-server-version/verify-portal.png" alt-text="Verifica versione":::

> [!NOTE]
> Se è già stata eseguita la registrazione con l'estensione SQL IaaS Agent, [annullare la registrazione dalla RELYING Party](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) e quindi [registrare nuovamente la risorsa VM SQL](sql-agent-extension-manually-register-single-vm.md#register-with-extension) in modo che rilevi la versione e l'edizione corrette del SQL Server installato nella macchina virtuale. Questa operazione Aggiorna i metadati e le informazioni di fatturazione associate a questa macchina virtuale.

## <a name="remarks"></a>Osservazioni

- È consigliabile avviare backup/Aggiorna statistiche/Ricompila indici/Verifica coerenza al termine dell'aggiornamento. È anche possibile controllare i singoli livelli di compatibilità del database per assicurarsi che riflettano il livello desiderato.
- Dopo l'aggiornamento di SQL Server nella macchina virtuale, assicurarsi che la proprietà **edizione** di SQL Server nel portale di Azure corrisponda al numero di edizione installato per la fatturazione.
- La possibilità di [modificare l'edizione](change-sql-server-edition.md#change-edition-in-portal) è una funzionalità dell'estensione SQL IaaS Agent. La distribuzione di un'immagine di Azure Marketplace tramite il portale di Azure registra automaticamente una macchina virtuale di SQL Server con l'estensione. Tuttavia, i clienti che eseguono l'installazione automatica SQL Server dovranno registrare manualmente [la macchina virtuale SQL Server](sql-agent-extension-manually-register-single-vm.md).
- Se si elimina la risorsa SQL Server VM, viene ripristinata l'impostazione dell'edizione hardcoded dell'immagine.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti:

- [Panoramica di SQL Server in una macchina virtuale Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Domande frequenti su SQL Server in una VM di Windows](frequently-asked-questions-faq.md)
- [Informazioni sui prezzi di SQL Server in una VM di Windows](pricing-guidance.md)
- [Note sulla versione di SQL Server in una VM di Windows](doc-changes-updates-release-notes.md)