---
title: 'Avvio rapido: Creare un server flessibile di Database di Azure per MySQL - Portale di Azure'
description: Questo articolo contiene informazioni dettagliate su come usare il portale di Azure per creare rapidamente un server flessibile di Database di Azure per MySQL in alcuni minuti.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/29/2020
ms.openlocfilehash: 70f2cf183a9bd93b6066516cb68e99ee21cdc1ac
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569629"
---
# <a name="quickstart-use-the-azure-portal-to-create-an-azure-database-for-mysql-flexible-server"></a>Avvio rapido: Usare il portale di Azure per la creazione di un server flessibile di Database di Azure per MySQL

Il server flessibile di Database di Azure per MySQL è un servizio gestito che consente di eseguire, gestire e dimensionare server MySQL a disponibilità elevata nel cloud. Questo argomento di avvio rapido illustra come creare in alcuni minuti un server flessibile usando il portale di Azure.

> [!IMPORTANT] 
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica

Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure
Aprire il Web browser e quindi passare al [portale di Azure](https://portal.azure.com/). Immettere le credenziali per accedere al portale. La visualizzazione predefinita è il dashboard del servizio.

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Creare un server flessibile di Database di Azure per MySQL

Creare un server flessibile con un set definito di [risorse di calcolo e di archiviazione](./concepts-compute-storage.md). Il server viene creato all'interno di un [gruppo di risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/overview).

Per creare un server flessibile, seguire questa procedura:

1. Nell'angolo in alto a sinistra del portale selezionare **Crea una risorsa** (+).

2. Selezionare **Database** > **Database di Azure per MySQL**. È anche possibile immettere **MySQL** nella casella di ricerca per trovare il servizio.

    > :::image type="content" source="./media/quickstart-create-server-portal/navigate-to-mysql.png" alt-text="Opzione Database di Azure per MySQL":::

3. Selezionare **Flexible server** (Server flessibile) come opzione di distribuzione.
     
    > :::image type="content" source="./media/quickstart-create-server-portal/deployment-option.png" alt-text="Opzione Database di Azure per MySQL":::    

4. Compilare il modulo **Informazioni di base** con le informazioni seguenti: 

    > :::image type="content" source="./media/quickstart-create-server-portal/create-form.png" alt-text="Opzione Database di Azure per MySQL"::: 
                                    
    |**Impostazione**|**Valore consigliato**|**Descrizione**|
    |---|---|---|
    Subscription|Nome della sottoscrizione utente|Sottoscrizione di Azure da usare per il server. Se si dispone di più sottoscrizioni, scegliere quella in cui si desidera che venga fatturata la risorsa.|
    Resource group|*myresourcegroup*| Nuovo nome di gruppo di risorse o uno esistente nella sottoscrizione.|
    Nome server |*mydemoserver*|Nome univoco che identifica il server flessibile. Al nome del server specificato viene aggiunto il nome di dominio *mysql.database.azure.com*. Il server può contenere solo lettere minuscole, numeri e il segno meno (-). Deve contenere almeno da 3 a 63 caratteri.|
    Nome utente amministratore |*mydemouser*| Account di accesso da usare per la connessione al server. Il nome di accesso dell'amministratore non può essere **azure_superuser**, **admin**, **administrator**, **root**, **guest** o **public**.|
    Password |Password| Nuova password per l'account amministratore del server. Deve contenere tra 8 e 128 caratteri. La password deve contenere caratteri di tre delle categorie seguenti: lettere maiuscole, lettere minuscole, numeri (da 0 a 9) e caratteri non alfanumerici (!, $, #, % e così via).|
    Region|Area più vicina ai propri utenti| Località più vicina agli utenti.|
    Versione|5.7| Versione principale di MySQL.|
    Calcolo e archiviazione | **Possibilità di burst**, **Standard_B1ms**, **10 GiB**, **7 giorni** | Configurazioni di calcolo, archiviazione e backup per il nuovo server. Selezionare **Configura server**. *Possibilità di burst*, *Standard_B1ms*, *10 GiB*, *7 giorni* sono i valori predefiniti per il **livello di calcolo**, le **dimensioni di calcolo**, l'**archiviazione** e il **periodo di conservazione backup**. È possibile lasciare questi dispositivi di scorrimento nella posizione in cui si trovano oppure regolarli. Per salvare la selezione di queste opzioni di calcolo e archiviazione, scegliere **Salva** per continuare con le configurazioni. Lo screenshot seguente mostra le opzioni di calcolo e archiviazione.|
    
    > :::image type="content" source="./media/quickstart-create-server-portal/compute-storage.png" alt-text="Opzione Database di Azure per MySQL":::

5. Configurare le opzioni dei servizi di rete

    Nella scheda Servizi di rete è possibile scegliere come deve essere raggiunto il server. Il server flessibile di Database di Azure per MySQL offre due opzioni per la connessione al server, l'*accesso pubblico (indirizzi IP consentiti)* e l'*accesso privato (integrazione rete virtuale)* . Con l'*accesso pubblico (indirizzi IP consentiti)* , l'accesso al server è limitato agli indirizzi IP consentiti aggiunti a una regola del firewall. Impedisce alle applicazioni e agli strumenti esterni di connettersi al server e ai database nel server, a meno che non venga creata una regola per aprire il firewall per un indirizzo IP specifico o un intervallo di indirizzi. Con l'*accesso privato (integrazione rete virtuale)* , l'accesso al server è limitato alla rete virtuale. Questo argomento di avvio rapido illustra come abilitare l'accesso pubblico per la connessione al server. Altre informazioni sui metodi di connettività sono disponibili nell'[articolo sui concetti](./concepts-networking.md).

    > [!NOTE]
    > Il metodo di connettività non può essere modificato dopo la creazione del server. Se ad esempio è stata selezionata l'opzione per l'*accesso pubblico (indirizzi IP consentiti)* durante la creazione, non è possibile passare all'*accesso privato (integrazione rete virtuale)* dopo la creazione. È consigliabile creare un server con accesso privato per accedere in modo sicuro al server tramite l'integrazione rete virtuale. Altre informazioni sull'accesso privato sono disponibili nell'[articolo sui concetti](./concepts-networking.md).

    > :::image type="content" source="./media/quickstart-create-server-portal/networking.png" alt-text="Opzione Database di Azure per MySQL":::  

6. Selezionare **Rivedi e crea** per rivedere la configurazione del server flessibile.

7. Selezionare **Crea** per effettuare il provisioning del server. Il provisioning può richiedere alcuni minuti.

8. Selezionare **Notifiche** sulla barra degli strumenti (icona a forma di campana) per monitorare il processo di distribuzione. Al termine della distribuzione, è possibile selezionare **Aggiungi al dashboard** per creare un riquadro del server flessibile nel dashboard del portale di Azure come collegamento alla pagina **Panoramica** del server. Selezionare **Vai alla risorsa** per aprire la pagina **Panoramica** del server.

Per impostazione predefinita, vengono creati i database seguenti nel server: **information_schema**, **mysql**, **performance_schema** e **sys**.

> [!NOTE]
> Controllare se la rete consente il traffico in uscita sulla porta 3306 usata dal server flessibile di Database di Azure per MySQL per evitare problemi di connettività.  

## <a name="connect-to-using-mysql-command-line-client"></a>Connettersi tramite il client della riga di comando mysql

Se il server flessibile è stato creato con l'opzione per l'*accesso privato (integrazione rete virtuale)* , sarà necessario connettersi al server da una risorsa all'interno della stessa rete virtuale usata dal server. È possibile creare una macchina virtuale e aggiungerla alla rete virtuale creata con il server flessibile.

Se il server flessibile è stato creato con l'opzione per l'*accesso pubblico (indirizzi IP consentiti)* , è possibile aggiungere l'indirizzo IP locale all'elenco di regole del firewall nel server.

È possibile scegliere [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) o [MySQL Workbench](./connect-workbench.md) per connettersi al server dall'ambiente locale. 

Con mysql.exe, connettersi usando il comando seguente. Sostituire i valori con il nome server e la password effettivi. 

```bash
 mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p
```
## <a name="clean-up-resources"></a>Pulire le risorse
La creazione di un server flessibile di Database di Azure per MySQL in un gruppo di risorse è stata completata.  Se non si prevede di aver bisogno di queste risorse in futuro, è possibile eliminarle eliminando il gruppo di risorse o è possibile eliminare semplicemente il server MySQL. Per rimuovere il gruppo di risorse, seguire questa procedura:

1. Accedere al portale di Azure e selezionare **Gruppi di risorse**.
1. Nell'elenco dei gruppi di risorse scegliere il nome del gruppo di risorse.
1. Nella pagina Panoramica del gruppo di risorse selezionare **Elimina gruppo di risorse**.
1. Nella finestra di dialogo di conferma, digitare il nome del gruppo di risorse e quindi selezionare **Elimina**.

Per eliminare il server, è possibile fare clic sul pulsante **Elimina** nella pagina **Panoramica** del server come illustrato di seguito:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-server-portal/delete-server.png" alt-text="Opzione Database di Azure per MySQL":::

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app Web PHP (Laravel) con MySQL](tutorial-php-database-app.md)
