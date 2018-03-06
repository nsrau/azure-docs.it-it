---
title: Creare e gestire Database di Azure per il server MySQL con il portale di Azure
description: Questo articolo descrive come creare rapidamente una nuova istanza di Database di Azure per il server MySQL e gestire il server con il portale di Azure.
services: mysql
author: ajlam
ms.author: nolanwu
editor: jasonwhowell
manager: kfile
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 0e274c0ada3de5e9000ae41516e5b9b67ef1490b
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="create-and-manage-azure-database-for-mysql-server-using-azure-portal"></a>Creare e gestire Database di Azure per il server MySQL con il portale di Azure
Questo argomento descrive come creare rapidamente una nuova istanza di Database di Azure per il server MySQL. Include anche informazioni sulla gestione del server tramite il portale di Azure. La gestione del server include la visualizzazione di database e dettagli del server, la reimpostazione della password, il ridimensionamento delle risorse e l'eliminazione del server.

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure
Accedere al [Portale di Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-mysql-server"></a>Creare un'istanza di Database di Azure per il server MySQL
Seguire questa procedura per creare un server di Database di Azure per MySQL denominato "mydemoserver".

1. Fare clic sul pulsante **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.

2. Nella pagina Nuovo selezionare **Database** e nella pagina Database selezionare **Database di Azure per MySQL**.

    > Verrà creata un'istanza di Database di Azure per il server MySQL con un set definito di risorse di [calcolo e di archiviazione](./concepts-pricing-tiers.md). Il database viene creato in un gruppo di risorse di Azure e in un'istanza di Database di Azure per il server MySQL.

   ![create-new-server](./media/howto-create-manage-server-portal/create-new-server.png)

3. Compilare il modulo Database di Azure per MySQL con le informazioni seguenti:

    | **Campo modulo** | **Descrizione campo** |
    |----------------|-----------------------|
    | *Server name* (Nome server) | mydemoserver (il nome server è univoco a livello globale) |
    | *Sottoscrizione* | mysubscription (selezionare la voce dal menu a discesa) |
    | *Gruppo di risorse* | myresourcegroup (creare un nuovo gruppo di risorse o usarne uno esistente) |
    | *Select source* (Seleziona origine) | Vuoto (creare un server MySQL vuoto) |
    | *Accesso amministratore server* | myadmin (configurare il nome dell'account amministratore) |
    | *Password* | Definire la password dell'account amministratore |
    | *Conferma password* | Confermare la password dell'account amministratore |
    | *Località* | Asia sud-orientale (scegliere tra Europa settentrionale e Stati Uniti occidentali) |
    | *Versione* | 5.7 (scegliere la versione del server di Database di Azure per MySQL) |

4. Fare clic su **Piano tariffario** per specificare il livello di servizio e il livello delle prestazioni per il nuovo server. Selezionare la scheda **Utilizzo generico**. *Generazione 4*, *2 vCore*, *5 GB* e *7 giorni* sono i valori predefiniti per **Generazione di calcolo**, **vCore**, **Archiviazione** e **Periodo di conservazione backup**. È possibile lasciare questi dispositivi di scorrimento nella posizione in cui si trovano. Per abilitare l'archiviazione con ridondanza geografica dei backup del server, selezionare **Con ridondanza geografica** in **Opzioni di ridondanza per il backup**.

   ![create-server-pricing-tier](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5. Fare clic su **Crea** per eseguire il provisioning del server. Il provisioning richiede alcuni minuti.

    > Selezionare l'opzione **Aggiungi al dashboard** per tenere facilmente traccia delle distribuzioni.

## <a name="update-an-azure-database-for-mysql-server"></a>Aggiornare un'istanza di Database di Azure per il server MySQL
Dopo l'esecuzione del provisioning del nuovo server, l'utente dispone di diverse opzioni per la configurazione del server esistente, tra cui la reimpostazione della password amministratore e il ridimensionamento del server tramite la modifica di vCore o delle risorse di archiviazione.

### <a name="change-the-administrator-user-password"></a>Modificare la password amministratore
1. Nella pagina **Panoramica** del server fare clic su **Reimposta password** per visualizzare la finestra di reimpostazione della password.

   ![Panoramica](./media/howto-create-manage-server-portal/overview.png)

2. Immettere una nuova password e confermarla nella finestra come illustrato di seguito:

   ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3. Fare clic su **OK** per salvare la nuova password.

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

## <a name="delete-an-azure-database-for-mysql-server"></a>Eliminare un'istanza di Database di Azure per il server MySQL

1. Nella pagina **Panoramica** del server fare clic sul pulsante **Elimina** per visualizzare la richiesta di conferma dell'eliminazione.

    ![delete](./media/howto-create-manage-server-portal/delete.png)

2. Digitare il nome del server nella casella di input per la doppia conferma.

    ![confirm-delete](./media/howto-create-manage-server-portal/confirm.png)

3. Fare clic sul pulsante **Elimina** per confermare l'eliminazione del server. Attendere la visualizzazione del messaggio "Il server MySQL è stato eliminato" nella barra di notifica.

## <a name="list-the-azure-database-for-mysql-databases"></a>Elencare i database di Database di Azure per MySQL
Nella pagina **Panoramica** del server scorrere verso il basso fino al riquadro dei database nella parte inferiore. Tutti i database nel server sono elencati nella tabella.

   ![show-databases](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mysql-server"></a>Visualizzare i dettagli di un'istanza di Database di Azure per il server MySQL
Fare clic su **Proprietà** in **Impostazioni** per visualizzare informazioni dettagliate sul server.

![properties](./media/howto-create-manage-server-portal/properties.png)

## <a name="next-steps"></a>Passaggi successivi

[Guida introduttiva: Creare Database di Azure per il server MySQL con il portale di Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)