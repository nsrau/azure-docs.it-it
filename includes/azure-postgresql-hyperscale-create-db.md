---
title: File di inclusione
description: File di inclusione
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 09/12/2019
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: fadbcf04f1cd474cf2d23963e88016d240272263
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71279888"
---
Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql---hyperscale-citus"></a>Creare un Database di Azure per PostgreSQL - Hyperscale (Citus)

Seguire questa procedura per creare un database di Azure per il server PostgreSQL:
1. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.
2. Selezionare **Database** nella pagina **Nuovo** e selezionare **Database di Azure per PostgreSQL** nella pagina **Database**.
3. Per l'opzione di distribuzione, fare clic sul pulsante **Crea** in **Gruppo di server Hyperscale (Citus) - ANTEPRIMA**.
4. Compilare il modulo dei dettagli del nuovo server con le informazioni seguenti:
   - Gruppo di risorse: fare clic sul collegamento **Crea nuovo** sotto la casella di testo di questo campo. Immettere un nome, ad esempio **myresourcegroup**.
   - Nome gruppo server: immettere un nome univoco per il nuovo gruppo di server, che verrà usato anche per un sottodominio di server.
   - Nome utente amministratore: attualmente deve essere il valore **citus** e non può essere modificato.
   - Password: deve essere composta da almeno otto caratteri e deve contenere caratteri appartenenti a tre delle categorie seguenti: lettere maiuscole, lettere minuscole, numeri (0-9) e caratteri non alfanumerici (!, $, #, % e così via).
   - Località: usare la località più vicina agli utenti per consentire loro l'accesso più rapido ai dati.

   > [!IMPORTANT]
   > La password dell'amministratore del server qui specificata è necessaria per accedere al server e ai relativi database. Prendere nota di queste informazioni per usarle in seguito.

5. Fare clic su **Configura gruppo di server**. Lasciare invariate le impostazioni di tale sezione e fare clic su **Salva**.
6. Fare clic su **Avanti: Rete >** nella parte inferiore della schermata.

7. Nella scheda **Rete** fare clic sul pulsante di opzione **Endpoint pubblico**.
   ![Endpoint pubblico selezionato](./media/azure-postgresql-hyperscale-create-db/network-public-endpoint.png)
8. Fare clic sul collegamento **+ Aggiungere l'indirizzo IP client corrente**.
   ![IP client aggiunto](./media/azure-postgresql-hyperscale-create-db/network-add-client-ip.png)

   > [!NOTE]
   > Il server PostgreSQL Azure comunica sulla porta 5432. Se si sta cercando di connettersi da una rete aziendale, il traffico in uscita sulla porta 5432 potrebbe non essere consentito dal firewall della rete. In questo caso, non è possibile connettersi al server di database SQL di Azure, a meno che il reparto IT non apra la porta 5432.
   >

9. Fare clic su **Rivedi e crea** e quindi su **Crea** per effettuare il provisioning del server. Il provisioning richiede alcuni minuti.
10. La pagina verrà reindirizzata per monitorare la distribuzione. Quando lo stato attivo passa da **La distribuzione è in corso** a **La distribuzione è stata completata**, fare clic sulla voce di menu **Output** nella parte sinistra della pagina.
11. La pagina degli output conterrà un nome host di coordinatore con accanto un pulsante per copiare il valore negli Appunti. Prendere nota di queste informazioni per un uso successivo.

## <a name="connect-to-the-database-using-psql"></a>Connettersi al database usando psql

Quando si crea il server di Database di Azure per PostgreSQL, viene creato un database predefinito denominato **citus**. Per connettersi al server di database, è necessaria una stringa di connessione e la password amministratore.

1. Ottenere la stringa di connessione. Nella pagina del gruppo di server fare clic sulla voce di menu **Stringhe di connessione**. Si trova in **Impostazioni**. Trovare la stringa contrassegnata con  **C++ (libpq)** . Sarà nel formato:

   ```
   host=hostname.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require
   ```

   Copiare la stringa. È necessario sostituire "{your\_password}" con la password amministrativa scelta in precedenza. Il sistema archivia la password in testo crittografato. Non è quindi possibile visualizzarla nella stringa di connessione.

2. Aprire una finestra del terminale nel computer locale.

3. Quando richiesto, connettersi al server di Database di Azure per PostgreSQL usando l'utilità [psql](https://www.postgresql.org/docs/current/app-psql.html). Passare la stringa di connessione tra virgolette, accertandosi che contenga la password:
   ```bash
   psql "{connection_string}"
   ```

   Il comando seguente, ad esempio, si connette al nodo coordinatore del gruppo di server **mydemoserver**:

   ```bash
   psql "host=mydemoserver-c.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```
