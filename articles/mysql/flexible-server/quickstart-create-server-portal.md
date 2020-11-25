---
title: 'Avvio rapido: Creare un server flessibile di Database di Azure per MySQL - Portale di Azure'
description: Questo articolo illustra come usare il portale di Azure per creare un server flessibile di Database di Azure per MySQL in pochi minuti.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/22/2020
ms.openlocfilehash: 864152d1f1d0074305cbba448946bc05888b4f3b
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566759"
---
# <a name="quickstart-use-the-azure-portal-to-create-an-azure-database-for-mysql-flexible-server"></a>Avvio rapido: Usare il portale di Azure per creare un server flessibile di Database di Azure per MySQL

Il server flessibile di Database di Azure per MySQL è un servizio gestito che consente di eseguire, gestire e dimensionare server MySQL a disponibilità elevata nel cloud. Questo argomento di avvio rapido illustra come creare un server flessibile tramite il portale di Azure.

> [!IMPORTANT] 
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica.

Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure
Accedere al [portale di Azure](https://portal.azure.com/). Immettere le credenziali per accedere al portale. La visualizzazione predefinita è il dashboard del servizio.

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Creare un server flessibile di Database di Azure per MySQL

Creare un server flessibile con un set definito di [risorse di calcolo e di archiviazione](./concepts-compute-storage.md). Il server viene creato all'interno di un [gruppo di risorse di Azure](../../azure-resource-manager/management/overview.md).

Per creare un server flessibile, completare questa procedura:

1. Cercare e selezionare **Server database di Azure per MySQL** nel portale:
    
    > :::image type="content" source="./media/quickstart-create-server-portal/find-mysql-portal.png" alt-text="Screenshot che mostra la ricerca di server di Database di Azure per MySQL.":::

2. Selezionare **Aggiungi**. 

3. Nella pagina **Selezionare l'opzione di distribuzione di Database di Azure per MySQL** selezionare **Server flessibile**:
     
    > :::image type="content" source="./media/quickstart-create-server-portal/deployment-option.png" alt-text="Screenshot che mostra l'opzione Server flessibile.":::    

4. Nella scheda **Informazioni di base** digitare le informazioni seguenti: 

    > :::image type="content" source="./media/quickstart-create-server-portal/create-form.png" alt-text="Screenshot che mostra la scheda Informazioni di base per la creazione di un server flessibile."::: 
                                    
    |**Impostazione**|**Valore consigliato**|**Descrizione**|
    |---|---|---|
    Subscription|Nome della sottoscrizione utente|Sottoscrizione di Azure da usare per il server. Se si hanno più sottoscrizioni, scegliere quella in cui si vuole che venga fatturata la risorsa.|
    Resource group|**myresourcegroup**| Nuovo nome di gruppo di risorse o uno esistente nella sottoscrizione.|
    Nome server |**mydemoserver**|Nome univoco che identifica il server flessibile. Al nome del server specificato viene aggiunto il nome di dominio `mysql.database.azure.com`. Il nome del server può contenere solo lettere minuscole, numeri e il segno meno (-) Deve contenere tra 3 e 63 caratteri.|
    Nome utente amministratore |**mydemouser**| L'account di accesso da usare per la connessione al server. Il nome dell'utente amministratore non può essere **azure_superuser**, **admin**, **administrator**, **root**, **guest** o **public**.|
    Password |Password| Nuova password per l'account amministratore del server. Deve contenere tra 8 e 128 caratteri. Deve inoltre contenere caratteri di tre categorie seguenti: lettere maiuscole, lettere minuscole, numeri (da 0 a 9) e caratteri non alfanumerici (!, $, #, % e così via).|
    Region|Area più vicina ai propri utenti| Località più vicina agli utenti.|
    Versione|**5.7**| Una versione principale di MySQL.|
    Calcolo e archiviazione | **Possibilità di burst**, **Standard_B1ms**, **10 GiB**, **7 giorni** | Configurazioni di calcolo, archiviazione e backup per il nuovo server. Selezionare **Configura server**. **Possibilità di burst**, **Standard_B1ms**, **10 GiB** e **7 giorni** sono i valori predefiniti per **Livello di calcolo**, **Dimensioni di calcolo**, **Dimensioni di archiviazione** e **Periodo di conservazione**. È possibile lasciare questi valori così come sono o modificarli. Per salvare la selezione delle opzioni di calcolo e archiviazione, scegliere **Salva** per continuare con la configurazione. Lo screenshot seguente mostra le opzioni di calcolo e archiviazione.|
    
    > :::image type="content" source="./media/quickstart-create-server-portal/compute-storage.png" alt-text="Screenshot che mostra le opzioni di calcolo e archiviazione.":::

5. Configurare le opzioni di rete.

    Nella scheda **Rete** è possibile scegliere come deve essere raggiunto il server. Per la connessione al server flessibile di Database di Azure per MySQL, sono disponibili due opzioni: 
   - Accesso pubblico (indirizzi IP consentiti)
   - Accesso privato (integrazione rete virtuale) 
   
   Se si usa l'accesso pubblico, l'accesso al server è limitato agli indirizzi IP consentiti aggiunti a una regola del firewall. Questo metodo impedisce la connessione di applicazioni e strumenti esterni al server e ai database al suo interno, a meno che non venga creata una regola per aprire il firewall per uno specifico indirizzo IP o intervallo di indirizzi. Se si usa l'accesso privato (integrazione rete virtuale), l'accesso al server è limitato alla rete virtuale. Questo argomento di avvio rapido illustra come abilitare l'accesso pubblico per la connessione al server. [Altre informazioni sui metodi di connettività sono disponibili nell'articolo sui concetti.](./concepts-networking.md)

    > [!NOTE]
    > Non è possibile cambiare il metodo di connettività dopo aver creato il server. Se ad esempio si seleziona l'opzione **Accesso pubblico (indirizzi IP consentiti)** quando si crea il server, non è in seguito possibile passare ad **Accesso privato (integrazione rete virtuale)** . È consigliabile creare il server con accesso privato per proteggere l'accesso tramite integrazione rete virtuale. [Altre informazioni sull'accesso privato sono disponibili nell'articolo sui concetti.](./concepts-networking.md)

    > :::image type="content" source="./media/quickstart-create-server-portal/networking.png" alt-text="Screenshot che mostra la scheda Rete.":::  

6. Selezionare **Rivedi e crea** per rivedere la configurazione del server flessibile.

7. Selezionare **Crea** per effettuare il provisioning del server. Il provisioning può richiedere alcuni minuti.

8. Selezionare **Notifiche** sulla barra degli strumenti (il pulsante con la campanella) per monitorare il processo di distribuzione. Al termine della distribuzione, è possibile selezionare **Aggiungi al dashboard** per creare un riquadro del server flessibile nel dashboard del portale di Azure. Questo riquadro funge da collegamento alla pagina **Panoramica** del server. Se si seleziona **Vai alla risorsa**, viene visualizzata la pagina **Panoramica** del server.

Per impostazione predefinita, vengono creati i database seguenti nel server: information_schema, mysql, performance_schema e sys.

> [!NOTE]
> Per evitare problemi di connettività, controllare se la rete consente il traffico in uscita sulla porta 3306, che è quella usata dal server flessibile di Database di Azure per MySQL.  

## <a name="connect-to-the-server-by-using-mysqlexe"></a>Connettersi al server usando mysql.exe

Se il server flessibile è stato creato con l'opzione per l'accesso privato (integrazione rete virtuale), sarà necessario connettersi da una risorsa all'interno della stessa rete virtuale del server. È possibile creare una macchina virtuale e aggiungerla alla rete virtuale creata con il server flessibile.

Se il server flessibile è stato creato con l'opzione per l'accesso pubblico (indirizzi IP consentiti), è possibile aggiungere il proprio indirizzo IP locale all'elenco di regole del firewall nel server.

È possibile usare [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) o [MySQL Workbench](./connect-workbench.md) per connettersi al server dall'ambiente locale. 

Se si usa mysql.exe, connettersi con il comando seguente. Nel comando usare i propri valori per nome server, nome utente e password. 

```bash
 mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p
```
## <a name="clean-up-resources"></a>Pulire le risorse
La creazione di un server flessibile di Database di Azure per MySQL in un gruppo di risorse è stata completata. Se non si prevede di aver bisogno di queste risorse in futuro, è possibile eliminarle eliminando il gruppo di risorse o è possibile eliminare semplicemente il server MySQL. Per rimuovere il gruppo di risorse, seguire questa procedura:

1. Accedere al portale di Azure e selezionare **Gruppi di risorse**.
1. Nell'elenco di gruppi di risorse selezionare il nome del gruppo di risorse desiderato.
1. Nella pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.
1. Nella finestra di dialogo di conferma, digitare il nome del gruppo di risorse e quindi selezionare **Elimina**.

Per eliminare il server, è possibile selezionare **Elimina** nella relativa pagina **Panoramica**, come illustrato qui:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-server-portal/delete-server.png" alt-text="Screenshot che mostra come eliminare un server.":::

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app Web PHP (Laravel) con MySQL](tutorial-php-database-app.md)