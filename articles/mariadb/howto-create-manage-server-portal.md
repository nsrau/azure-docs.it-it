---
title: Creare e gestire database di Azure per il server MariaDB usando portale di Azure
description: Questo articolo descrive come è possibile creare rapidamente un nuovo database di Azure per il server MariaDB e gestire il server usando il portale di Azure.
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 08/09/2019
ms.openlocfilehash: 5aae3eb0582956ccb45cc41d8400f489f1077bad
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70143440"
---
# <a name="create-and-manage-azure-database-for-mariadb-server-using-azure-portal"></a>Creare e gestire database di Azure per il server MariaDB usando portale di Azure
Questo argomento descrive come è possibile creare rapidamente un nuovo database di Azure per il server MariaDB. Include anche informazioni sulla gestione del server tramite il portale di Azure. La gestione del server include la visualizzazione di database e dettagli del server, la reimpostazione della password, il ridimensionamento delle risorse e l'eliminazione del server.

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure
Accedere al [Portale di Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-mariadb-server"></a>Creare un database di Azure per un server MariaDB
Seguire questa procedura per creare un database di Azure per il server MariaDB denominato "mydemoserver".

1. Fare clic sul pulsante **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.

2. Nella pagina nuovo selezionare database equindi nella pagina database selezionare **database di Azure per MariaDB**.

    > Un database di Azure per il server MariaDB viene creato con un set definito di risorse di [calcolo e di archiviazione](./concepts-pricing-tiers.md) . Il database viene creato in un gruppo di risorse di Azure e in un database di Azure per il server MariaDB.

   ![create-new-server](./media/howto-create-manage-server-portal/create-new-server.png)

3. Compilare il modulo database di Azure per MariaDB usando le seguenti informazioni:

    | **Campo modulo** | **Descrizione campo** |
    |----------------|-----------------------|
    | *Nome server* | mydemoserver (il nome server è univoco a livello globale) |
    | *Sottoscrizione* | mysubscription (selezionare la voce dal menu a discesa) |
    | *Gruppo di risorse* | myresourcegroup (creare un nuovo gruppo di risorse o usarne uno esistente) |
    | *Seleziona origine* | Blank (crea un server MariaDB vuoto) |
    | *Accesso amministratore server* | myadmin (configurare il nome dell'account amministratore) |
    | *Password* | Definire la password dell'account amministratore |
    | *Conferma password* | Confermare la password dell'account amministratore |
    | *Location* | Asia sud-orientale (scegliere tra Europa settentrionale e Stati Uniti occidentali) |
    | *Versione* | 10,3 (scegliere la versione del server di database di Azure per MariaDB) |

   ![create-new-server](./media/howto-create-manage-server-portal/form-field.png)

4. Fare clic su **Configura server** per specificare il livello di servizio e il livello di prestazioni per il nuovo server. Selezionare la scheda **Utilizzo generico**. *Generazione 5*, *4 vCore*, *100 GB* e *7 giorni* sono i valori predefiniti per **Generazione di calcolo**, **vCore**, **Archiviazione** e **Periodo di conservazione backup**. È possibile lasciare i dispositivi di scorrimento così come sono. Per abilitare l'archiviazione con ridondanza geografica dei backup del server, selezionare **Con ridondanza geografica** in **Opzioni di ridondanza per il backup**.

   ![create-server-pricing-tier](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5. Fare clic su **Verifica + crea** per passare alla schermata di verifica e verificare tutti i dettagli. Fare clic su **Crea** per eseguire il provisioning del server. Il provisioning richiede alcuni minuti.

    > Selezionare l'opzione **Aggiungi al dashboard** per tenere facilmente traccia delle distribuzioni.

## <a name="update-an-azure-database-for-mariadb-server"></a>Aggiornare un database di Azure per il server MariaDB
Al termine del provisioning del nuovo server, l'utente dispone di diverse opzioni per la configurazione del server esistente, inclusa la reimpostazione della password amministratore, la modifica del piano tariffario e la scalabilità verticale del server cambiando vCore o archiviazione.

### <a name="change-the-administrator-user-password"></a>Modificare la password amministratore
1. Nella pagina **Panoramica** del server fare clic su **Reimposta password** per visualizzare la finestra di reimpostazione della password.

   ![panoramica](./media/howto-create-manage-server-portal/overview.png)

2. Immettere una nuova password e confermarla nella finestra come illustrato di seguito:

   ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3. Fare clic su **OK** per salvare la nuova password.

### <a name="change-the-pricing-tier"></a>Modificare il piano tariffario
> [!NOTE]
> Il ridimensionamento è supportato solo da per utilizzo generico a livelli di servizio con ottimizzazione per la memoria e viceversa. Si noti che la modifica da e verso il piano tariffario di base dopo la creazione del server non è supportata nel database di Azure per MariaDB.
> 
1. Fare clic su **Piano tariffario** in **Impostazioni**.
2. Selezionare il piano tariffario che si desidera modificare.

    ![modifica-piano tariffario](./media/howto-create-manage-server-portal/change-pricing-tier.png)

4. Fare clic su **OK** per salvare le modifiche. 

### <a name="scale-vcores-updown"></a>Ridimensionamento di vCore

1. Fare clic su **Piano tariffario** in **Impostazioni**.

2. Modificare l'impostazione di **vCore** spostando il dispositivo di scorrimento sul valore desiderato.

    ![scale-compute](./media/howto-create-manage-server-portal/scale-compute.png)

3. Fare clic su **OK** per salvare le modifiche.

### <a name="scale-storage-up"></a>Aumentare le prestazioni delle risorse di archiviazione

1. Fare clic su **Piano tariffario** in **Impostazioni**.

2. Modificare l'impostazione di **Archiviazione** spostando il dispositivo di scorrimento sul valore desiderato.

    ![scale-storage](./media/howto-create-manage-server-portal/scale-storage.png)

3. Fare clic su **OK** per salvare le modifiche.

## <a name="delete-an-azure-database-for-mariadb-server"></a>Eliminare un database di Azure per il server MariaDB

1. Nella pagina **Panoramica** del server fare clic sul pulsante **Elimina** per visualizzare la richiesta di conferma dell'eliminazione.

    ![delete](./media/howto-create-manage-server-portal/delete.png)

2. Digitare il nome del server nella casella di input per la doppia conferma.

    ![confirm-delete](./media/howto-create-manage-server-portal/confirm.png)

3. Fare clic sul pulsante **Elimina** per confermare l'eliminazione del server. Attendere che venga visualizzato il messaggio di spunta "il server MariaDB" è stato eliminato correttamente nella barra di notifica.

## <a name="list-the-azure-database-for-mariadb-databases"></a>Elencare i database di Azure per i database MariaDB
Nella pagina **Panoramica** del server scorrere verso il basso fino al riquadro dei database nella parte inferiore. Tutti i database nel server sono elencati nella tabella.

   ![show-databases](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mariadb-server"></a>Mostra i dettagli di un database di Azure per il server MariaDB
Fare clic su **Proprietà** in **Impostazioni** per visualizzare informazioni dettagliate sul server.

![properties](./media/howto-create-manage-server-portal/properties.png)

## <a name="next-steps"></a>Passaggi successivi

[Avvio rapido: Creare un server di Database di Azure per MariaDB usando il portale di Azure](./quickstart-create-mariadb-server-database-using-azure-portal.md)