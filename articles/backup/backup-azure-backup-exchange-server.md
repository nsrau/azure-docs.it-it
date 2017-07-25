---
title: Eseguire il backup di un server di Exchange in Backup di Azure con System Center 2012 R2 DPM |Documentazione Microsoft
description: Informazioni su come eseguire il backup di un server di Exchange in Backup di Azure con System Center 2012 R2 DPM
services: backup
documentationcenter: 
author: MaanasSaran
manager: NKolli1
editor: 
ms.assetid: 13f32256-888e-416e-a78b-40c2a26a5939
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: masaran;jimpark;delhan;trinadhk;markgal
ms.translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 2ee4bfa8c7dce45c6366ddc8aa6e723fe91431a7
ms.contentlocale: it-it
ms.lasthandoff: 11/17/2016


---
<a id="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm" class="xliff"></a>

# Eseguire il backup di un server di Exchange in Backup di Azure con System Center 2012 R2 DPM
Questo articolo descrive come configurare un server di System Center 2012 R2 Data Protection Manager (DPM) per eseguire il backup di un server di Microsoft Exchange per Backup di Azure.  

<a id="updates" class="xliff"></a>

## Aggiornamenti
Per registrare correttamente il server DPM con Backup di Azure, è necessario installare l'aggiornamento cumulativo più recente per System Center 2012 R2 DPM e la versione più recente dell'agente di Backup di Azure. Ottenere l'aggiornamento cumulativo più recente dal [catalogo Microsoft](http://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager).

> [!NOTE]
> Per gli esempi in questo articolo, è installata la versione 2.0.8719.0 dell'agente di Backup di Azure e l'aggiornamento cumulativo 6 è installato in System Center 2012 R2 DPM.
>
>

<a id="prerequisites" class="xliff"></a>

## Prerequisiti
Prima di continuare, assicurarsi che tutti i [prerequisiti](backup-azure-dpm-introduction.md#prerequisites) per l'uso di Backup di Microsoft Azure per proteggere i carichi di lavoro siano stati soddisfatti. I prerequisiti includono i seguenti:

* È stato creato un insieme di credenziali per il backup nel sito di Azure.
* Le credenziali dell'agente e dell'insieme di credenziali sono state scaricate nel server DPM.
* L'agente è installato nel server DPM.
* Le credenziali dell'insieme di credenziali sono state usate per registrare il server DPM.
* Se si sta proteggendo Exchange 2016, eseguire l'aggiornamento a DPM 2012 R2 UR9 o versioni successive

<a id="dpm-protection-agent" class="xliff"></a>

## Agente protezione DPM
Per installare l'agente protezione DPM nel server di Exchange, seguire questi passaggi:

1. Assicurarsi che i firewall siano configurati correttamente. Vedere [Configurare le eccezioni del firewall per l'agente](https://technet.microsoft.com/library/Hh758204.aspx).
2. Per installare l'agente nel server di Exchange, fare clic su **Gestione > Agenti > Installa** nella Console amministrazione DPM. Per la procedura dettagliata, vedere [Installare l'agente protezione DPM](https://technet.microsoft.com/library/hh758186.aspx?f=255&MSPPError=-2147217396) .

<a id="create-a-protection-group-for-the-exchange-server" class="xliff"></a>

## Creare un gruppo di protezione per il server di Exchange
1. Nella Console amministrazione DPM fare clic su **Protezione** e quindi fare clic su **Nuovo** nella barra multifunzione per aprire la procedura guidata **Crea nuovo gruppo protezione dati**.
2. Nella schermata **iniziale** della procedura guidata fare clic su **Avanti**.
3. Nella schermata **Selezione tipo di gruppo protezione dati** selezionare **Server** e fare clic su **Avanti**.
4. Selezionare il database di Exchange Server che si vuole proteggere e fare clic su **Avanti**.

   > [!NOTE]
   > Se si vuole proteggere Exchange 2013, controllare i [Prerequisiti di Exchange 2013](https://technet.microsoft.com/library/dn751029.aspx).
   >
   >

    Nell'esempio seguente è selezionato il database di Exchange 2010.

    ![Seleziona membri del gruppo](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Selezionare il metodo di protezione dati.

    Assegnare un nome al gruppo protezione dati e quindi selezionare entrambe le opzioni seguenti:

   * Protezione dati breve termine tramite: Disco.
   * Protezione dati online.
6. Fare clic su **Next**.
7. Selezionare l'opzione **Esegui Eseutil per controllare l'integrità dei dati** se si vuole controllare l'integrità dei database di Exchange Server.

    Dopo aver selezionato questa opzione, la verifica coerenza del backup verrà eseguito nel server DPM per evitare il traffico di I/O che viene generato eseguendo il comando **eseutil** sul server di Exchange.

   > [!NOTE]
   > Per usare questa opzione, è necessario copiare i file Ese.dll and Eseutil.exe nella directory C:\Programmi\Microsoft System Center 2012 R2\DPM\DPM\bin nel server DPM. In caso contrario, viene generato l'errore seguente:   
   > ![Errore di Eseutil](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Fare clic su **Next**.
9. Selezionare il database per **Backup di copia**, quindi fare clic su **Avanti**.

   > [!NOTE]
   > Se non si seleziona "Backup completo" per almeno una copia del gruppo di disponibilità dei database di un database, i registri non verranno troncati.
   >
   >
10. Configurare gli obiettivi per **Backup a breve termine**, quindi fare clic su **Avanti**.
11. Controllare lo spazio disponibile su disco e quindi fare clic su **Avanti**.
12. Selezionare l'ora in cui il server DPM dovrà creare la replica iniziale e quindi fare clic su **Avanti**.
13. Selezionare le opzioni di verifica coerenza e quindi fare clic su **Avanti**.
14. Scegliere il database di cui si vuole eseguire il backup in Azure e quindi fare clic su **Avanti**. Ad esempio:

    ![Specifica i dati da proteggere online](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Definire la pianificazione per **Backup di Azure**, quindi fare clic su **Avanti**. ad esempio:

    ![Specificare la pianificazione dei backup online](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Tenere presente che i punti di ripristino online sono basati sui punti di ripristino di backup completo rapido. È quindi necessario pianificare il punto di ripristino in linea dopo il tempo specificato per il punto di ripristino di backup completo rapido.
    >
    >
16. Configurare i criteri di conservazione per **Backup di Azure**, quindi fare clic su **Avanti**.
17. Scegliere un'opzione di replica online e fare clic su **Avanti**.

    Un database di grandi dimensioni potrebbe richiedere molto tempo per creare il backup iniziale in rete. Per evitare questo problema, è possibile creare un backup offline.  

    ![Specificare i criteri di mantenimento online](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Verificare le impostazioni e quindi fare clic su **Crea gruppo**.
19. Fare clic su **Close**.

<a id="recover-the-exchange-database" class="xliff"></a>

## Ripristinare il database di Exchange
1. Per ripristinare un database di Exchange, fare clic su **Ripristino** nella Console amministrazione DPM.
2. Individuare il database di Exchange che si vuole ripristinare.
3. Selezionare un punto di ripristino online dall'elenco a discesa *Ora ripristino* .
4. Fare clic su **Ripristina** per avviare il **Ripristino guidato**.

Per i punti di ripristino online sono disponibili cinque tipi:

* **Ripristina nel percorso originale di Exchange Server:** i dati verranno ripristinati nel server di Exchange originale.
* **Ripristina in un altro database in un Server di Exchange:** i dati verranno ripristinati in un altro database in un altro server di Exchange.
* **Ripristina in un database di ripristino:** i dati verranno ripristinati in un database di ripristino di Exchange (RDB).
* **Copia in una cartella di rete:** i dati verranno ripristinati in una cartella di rete.
* **Copia su nastro:** se si ha una libreria di nastri o un'unità nastro autonoma collegata e configurata nel server DPM, il punto di ripristino verrà copiato su un nastro disponibile.

    ![Scegliere la replica online](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

<a id="next-steps" class="xliff"></a>

## Passaggi successivi
* [Backup di Azure - Domande frequenti](backup-azure-backup-faq.md)

