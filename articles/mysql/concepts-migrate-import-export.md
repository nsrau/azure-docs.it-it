---
title: Importazione ed esportazione nel database di Azure per MySQL | Microsoft Docs
description: Introduce i concetti di importazione ed esportazione di database nel database di Azure per MySQL
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: portal
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 582f83e3e80d855049abece42726748fcd113ab7
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---

# <a name="migrate-your-mysql-database-using-import-and-export"></a>Migrazione del database MySQL mediante l'importazione ed esportazione
In questa esercitazione verrà illustrato il modo più comune per importare ed esportare i dati nel database MySQL di Azure usando MySQL Workbench. 

## <a name="before-you-begin"></a>Prima di iniziare
Per proseguire con questa guida è necessario:
- Un database di Azure per il server MySQL [Progettare il primo database MySQL di Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- MySQL Workbench [MySQL Workbench Download](https://dev.mysql.com/downloads/workbench/) (Download di MySQL Workbench)

## <a name="use-common-tools"></a>Usare strumenti comuni
Usare strumenti comuni, come ad esempio MySQL Workbench, Toad o Navicat per connettersi in modalità remota e importare o esportare dati nel database di Azure per MySQL. Usare tali strumenti sul computer client con una connessione internet per connettersi al database di Azure per MySQL. Usare una connessione SSL crittografata per le procedure di sicurezza consigliate. Vedere anche [Configure SSL connectivity in Azure Database for MySQL](concepts-ssl-connection-security.md) (Configurare la connettività SSL nel database di Azure per MySQL). Durante la migrazione al database di Azure per MySQL non è necessario spostare i file di importazione ed esportazione in alcun percorso cloud speciale. 

## <a name="create-a-database-on-the-target-azure-database-for-mysql-service"></a>Creare un database sul database di Azure per il server MySQL di destinazione
È necessario creare un database vuoto nel database di Azure per il server MySQL di destinazione in cui si desidera migrare i dati con MySQL Workbench, Toad, Navicat o qualsiasi altro strumento di terze parti per MySQL. Il database può avere lo stesso nome del database che contiene i dati di dump; in alternativa, è possibile creare un database con un nome diverso.

![Stringa di connessione del database di Azure per MySQL](./media/concepts-migrate-import-export/p5.png)

![Stringa di connessione MySQL Workbench](./media/concepts-migrate-import-export/p4.png)

## <a name="import-and-export-using-mysql-workbench"></a>Importazione ed esportazione con MySQL Workbench
Esistono due modi per esportare e importare dati in MySQL Workbench, ciascuno destinato a uno scopo diverso. 

## <a name="table-data-export-and-import-wizard-using-object-browser-context-menu"></a>Esportazione e importazione guidata dei dati di una tabella tramite il menu di scelta rapida di Visualizzatore oggetti
![Importazione/esportazione con MySQL Workbench tramite il menu di scelta rapida di Visualizzatore oggetti](./media/concepts-migrate-import-export/p1.png)

Questa procedura guidata supporta le operazioni di importazione ed esportazione tramite file CSV e JSON e include diverse opzioni di configurazione (separatori, selezione colonne, selezione codifica e altro ancora). La procedura guidata può essere eseguita con i server MySQL connessi in modalità remota o in locale e l'azione di importazione include tabelle, colonne e mapping dei tipi. La procedura guidata è accessibile dal menu di scelta rapida del Visualizzatore oggetti facendo clic con il tasto destro su una tabella e scegliendo **Table Data Export Wizard** (Esportazione guidata di tabelle) o **Table Data Import Wizard** (Importazione guidata di tabelle). 

## <a name="table-data-export-wizard"></a>Esportazione guidata di tabelle
L'esempio seguente illustra l'esportazione di una tabella in un file CSV. 
- Fare clic sulla tabella del database da esportare. 
- Selezionare **Table Data Export Wizard** (Esportazione guidata di tabelle). Selezionare le colonne da esportare, l'offset delle righe (se presente) e il conteggio (se presente). 
- Fare clic su **Avanti** nella finestra "Selezionare i dati da esportare". Selezionare il Percorso file, il tipo di file (JSON o CSV), il Separatore di riga, Enclose Strings in (Racchiudi stringhe) e il Separatore campi. 
- Selezionare **Avanti** nella finestra "Select output file location" (Specificare il percorso del file di output), quindi Seleziona successivo nella finestra "Esporta dati".


## <a name="table-data-import-wizard"></a>Importazione guidata di tabelle
L'esempio seguente illustra l'importazione di una tabella da un file CSV.
- Fare clic con il tasto destro sulla tabella del database da importare. 
- Individuare e selezionare il file CSV da importare, quindi premere il pulsante Avanti. 
- Selezionare la tabella di destinazione (nuova o esistente), quindi selezionare o deselezionare la casella di controllo "Truncate table before import" (Tronca tabella prima dell'importazione) e fare clic sul pulsante Avanti.
- Selezionare la codifica e le colonne da importare, quindi selezionare il pulsante Avanti. 
- Selezionando la finestra Avanti o Importa dati, l'importazione dei dati avverrà di conseguenza.

## <a name="sql-data-export-and-import-wizard-from-management-navigator"></a>Esportazione e importazione guidata di dati SQL da Management Navigator
Usare questa procedura guidata per esportare o importare SQL generati da MySQL Workbench o tramite il comando mysqldump. Accedere a queste procedure guidate dal pannello Navigatore o selezionando Server dal menu principale, quindi Importazione dati o Esportazione dati. 

## <a name="data-export"></a>Esportazione dati
![Esportazione dati da MySQL Workbench tramite Management Navigator](./media/concepts-migrate-import-export/p2.png)

Questa scheda consente di esportare i dati di MySQL. 
- Selezionare ogni schema da esportare, se lo si desidera scegliere tabelle/oggetti specifici dello schema da ogni schema e generare l'esportazione. Le opzioni di configurazione includono l'esportazione in una cartella di progetto o in un file SQL autonomo, eventi e routine di dump archiviati (facoltativamente) o consentono di ignorare i dati della tabella. 
- In alternativa, usare **Export a Result Set** (Esporta un set di risultati) per esportare un risultato specifico impostato nell'editor SQL in un altro formato, come ad esempio CSV, JSON, HTML e XML. 
- Selezionare gli oggetti del database da esportare e configurare le opzioni correlate. 
- Fare clic su **Aggiorna** per caricare gli oggetti correnti. 
- Se lo si desidera, aprire la scheda Opzioni avanzate, che consente di definire l'operazione di esportazione. Ad esempio, aggiungere blocchi di tabella, usare istruzioni "replace" anziché "insert", segnalare gli identificatori con l'apice inverso e altro ancora. 
- Fare clic su **Avvia esportazione** per avviare il processo di esportazione. 


## <a name="data-import"></a>Importazione dati
![Importazione di dati da MySQL Workbench tramite Management Navigator](./media/concepts-migrate-import-export/p3.png)

Questa scheda consente di importare o ripristinare i dati esportati tramite l'operazione Esportazione dati o altri dati esportati tramite il comando mysqldump. 
- Scegliere la cartella di progetto o il file SQL autonomo, scegliere lo schema in cui importare i dati o scegliere Nuovo per definire un nuovo schema. 
- Fare clic su **Inizia importazione** per avviare la procedura di importazione corrispondente.

## <a name="next-steps"></a>Passaggi successivi
Se non si ha familiarità con l'introduzione di questo servizio database, vedere:
-  [Creare un database di Azure per il server MySQL tramite il portale di Azure](quickstart-create-mysql-server-database-using-azure-portal.md) 
- [Creare un database di Azure per il server MySQL tramite l'interfaccia della riga di comando di Azure](quickstart-create-mysql-server-database-using-azure-cli.md)

