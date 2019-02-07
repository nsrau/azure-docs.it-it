---
title: Come ripristinare un server in Database di Azure per PostgreSQL | Microsoft Docs
description: In questo articolo viene descritta la procedura per ripristinare un server nel Database di Azure per PostgreSQL usando il portale di Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2018
ms.openlocfilehash: 62a953467e212d0cb78686e9b0d98a7f1fcb5135
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2019
ms.locfileid: "55699011"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-postgresql-using-the-azure-portal"></a>Come eseguire la procedura di backup e ripristino di un server in Database di Azure per PostgreSQL usando il portale di Azure

## <a name="backup-happens-automatically"></a>Il backup viene eseguito automaticamente
Il backup dei server Database di Azure per PostgreSQL viene eseguito periodicamente per abilitare le funzionalità di ripristino. L'uso di questa funzionalità consente di ripristinare il server e tutti i suoi database a un momento precedente nel nuovo server.

## <a name="set-backup-configuration"></a>Impostare la configurazione del backup

La scelta tra backup con ridondanza locale o ridondanza geografica si effettua al momento della creazione del server, nella finestra **Piano tariffario**.

> [!NOTE]
> Dopo aver creato il server, il tipo di ridondanza (locale o geografica) non può essere modificato.
>

Quando si crea un server nel portale di Azure, la finestra **Piano tariffario** consente di selezionare backup **con ridondanza locale** o **ridondanza geografica** per il server. In questa finestra è anche possibile specificare il **periodo di conservazione dei backup**, ovvero per quanti giorni si vogliono archiviare i backup del server.

   ![Piano tariffario: scegliere la ridondanza del backup](./media/howto-restore-server-portal/pricing-tier.png)

Per altre informazioni sull'impostazione di questi valori durante la creazione, vedere la [guida introduttiva del server Database di Azure per PostgreSQL](quickstart-create-server-database-portal.md).

È possibile modificare il periodo di conservazione dei backup di un server seguendo questa procedura:
1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare il server di Database di Azure per PostgreSQL. Questa azione apre la pagina **Panoramica**.
3. Selezionare **Piano tariffario** nel menu in **IMPOSTAZIONI**. Con il dispositivo di scorrimento è possibile modificare il **periodo di conservazione dei backup** impostandolo su un numero di giorni compreso tra 7 e 35.
Nello screenshot seguente, il periodo è stato aumentato a 34 giorni.
![Periodo di conservazione dei backup aumentato](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Fare clic su **OK** per confermare la modifica.

Il periodo di conservazione dei backup determina quanto è possibile tornare indietro nel tempo con un ripristino temporizzato, essendo il ripristino basato sui backup disponibili. Il ripristino temporizzato è descritto con altri dettagli nella sezione seguente. 

## <a name="point-in-time-restore"></a>Ripristino temporizzato
Database di Azure per PostgreSQL consente di ripristinare il server a un momento specifico e in una nuova copia del server. È possibile usare questo nuovo server per ripristinare i dati oppure configurare le applicazioni perché puntino al nuovo server.

Ad esempio, se oggi una tabella è stata accidentalmente eliminata a mezzogiorno, è possibile eseguire il ripristino a un'ora prima di mezzogiorno per recuperare la tabella e i dati mancanti dalla nuova copia del server. Il ripristino temporizzato viene eseguito a livello di server, non a livello di database.

La procedura seguente consente di ripristinare il server di esempio a un momento specifico:
1. Nel portale di Azure selezionare il server Database di Azure per PostgreSQL. 

2. Nella barra degli strumenti della pagina **Panoramica** del server selezionare **Ripristina**.

   ![Database di Azure per PostgreSQL - Panoramica - Pulsante Ripristino](./media/howto-restore-server-portal/2-server.png)

3. Compilare il modulo Ripristina con le informazioni obbligatorie:

   ![Database di Azure per PostgreSQL - Informazioni di ripristino ](./media/howto-restore-server-portal/3-restore.png)
  - **Punto di ripristino**: selezionare il punto di ripristino temporizzato desiderato.
  - **Server di destinazione**: Specificare un nome per il nuovo server.
  - **Posizione**: non è possibile selezionare l'area. Per impostazione predefinita è uguale al server di origine.
  - **Piano tariffario**: non è possibile modificare questi parametri quando si esegue un ripristino temporizzato. È uguale al server di origine. 

4. Fare clic su **OK** per ripristinare il server a un momento specifico. 

5. Al termine del ripristino, individuare il nuovo server creato per verificare che il ripristino dei dati sia avvenuto come previsto.

>[!Note]
>Il nuovo server creato con il ripristino temporizzato ha il nome e la password di accesso dell'amministratore validi per il server esistente nel momento scelto per il ripristino. È possibile modificare la password dalla pagina **Panoramica** del nuovo server.

## <a name="geo-restore"></a>Ripristino geografico
Se il server è stato configurato per backup con ridondanza geografica, è possibile creare un nuovo server dal backup di quel server esistente. Questo nuovo server può essere creato in qualsiasi area in cui è disponibile Database di Azure per PostgreSQL.  

1. Selezionare il pulsante **Crea una risorsa** (+) nell'angolo superiore sinistro del portale. Selezionare **Database** > **Database di Azure per PostgreSQL**.

   ![Opzione "Database di Azure per PostgreSQL"](./media/howto-restore-server-portal/1-navigate-to-postgres.png)

2. Nell'elenco a discesa **Seleziona origine** scegliere **Backup**. Questa azione consente di caricare un elenco di server per i quali è stato abilitato il backup con ridondanza geografica. Selezionare uno di questi backup come origine del nuovo server.
   ![Seleziona origine: backup ed elenco di backup con ridondanza geografica](./media/howto-restore-server-portal/2-georestore.png)

   > [!NOTE]
   > Quando un server viene creato per la prima volta, potrebbe non essere subito disponibile per il ripristino geografico. Potrebbero essere necessarie alcune ore per popolare i metadati necessari.
   >

3. Compilare il resto del modulo con le proprie preferenze. È possibile selezionare qualsiasi **posizione**. Dopo aver selezionato la posizione è possibile selezionare **Piano tariffario**. Per impostazione predefinita vengono visualizzati i parametri del server esistente dal quale si esegue il ripristino. È possibile fare clic su **OK** senza apportare alcuna modifica per ereditare tali impostazioni. In alternativa è possibile modificare la **generazione di calcolo** (se disponibile nell'area selezionata), il numero di **vCore**, il **periodo di conservazione dei backup** e l'**opzione di ridondanza dei backup**. La modifica del **piano tariffario** (Basic, Utilizzo generico o Con ottimizzazione per la memoria) o delle dimensioni della **risorsa di archiviazione** non è supportata durante il ripristino.

>[!Note]
>Il nuovo server creato con il ripristino geografico ha il nome e la password di accesso dell'amministratore validi per il server esistente al momento dell'avvio del ripristino. È possibile modificare la password dalla pagina **Panoramica** del nuovo server.


## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sui [backup](concepts-backup.md) del servizio.
- Altre informazioni sulle opzioni di [continuità aziendale](concepts-business-continuity.md).
