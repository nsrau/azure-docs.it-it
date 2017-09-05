---
title: Creare una macchina virtuale di Azure classica che esegue MySQL | Documentazione Microsoft
description: Creare una macchina virtuale di Azure che esegue Windows Server 2012 R2 e il database MySQL usando il modello di distribuzione classica.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 98fa06d2-9b92-4d05-ac16-3f8e9fd4feaa
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: cynthn
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 11850e5ce20efae88a7af9c1d2e4761ed2b70cd7
ms.contentlocale: it-it
ms.lasthandoff: 08/11/2017

---
# <a name="install-mysql-on-a-virtual-machine-created-with-the-classic-deployment-model-running-windows-server-2016"></a>Installare MySQL in una macchina virtuale creata con il modello di distribuzione classica che esegue Windows Server 2016
[MySQL](https://www.mysql.com) è un database SQL open source molto diffuso. Questa esercitazione illustra come installare ed eseguire la **versione community di MySQL 5.7.18** come MySQL Server in una macchina virtuale che esegue **Windows Server 2016**. Con altre versioni di MySQL o Windows Server, l'esperienza potrebbe essere leggermente diversa.

Per istruzioni sull'installazione di MySQL su Linux, fare riferimento a: [Come installare MySQL in Azure](../../linux/mysql-install.md).

> [!IMPORTANT]
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.

## <a name="create-a-virtual-machine-running-windows-server-2016"></a>Creare una macchina virtuale che esegue Windows Server 2016
Se non si dispone ancora di una macchina virtuale che esegue Windows Server 2016, è possibile crearla seguendo questa [esercitazione](./tutorial.md).

## <a name="attach-a-data-disk"></a>Collegamento di un disco dati
Dopo aver creato la macchina virtuale, facoltativamente è possibile collegare un disco dati. L'aggiunta di dischi dati è consigliata per i carichi di lavoro di produzione e per evitare l'esaurimento dello spazio nell'unità del sistema operativo (C:), che include il sistema operativo.

Vedere [Come collegare un disco dati a una macchina virtuale Windows](../attach-managed-disk-portal.md) e seguire le istruzioni per collegare un disco vuoto. Impostare le preferenze della cache dell'host su **Nessuno** o **Sola lettura**.

## <a name="log-on-to-the-virtual-machine"></a>Accesso alla macchina virtuale
Quindi, [collegarsi alla macchina virtuale](./connect-logon.md) in modo da poter installare MySQL.

## <a name="install-and-run-mysql-community-server-on-the-virtual-machine"></a>Installare ed eseguire MySQL Community Server nella macchina virtuale
Per installare, configurare ed eseguire la versione Community di MySQL Server, seguire questa procedura:

> [!NOTE]
> Durante il download di elementi con Internet Explorer, è possibile impostare l'opzione **Sicurezza avanzata** di IE su Off e semplificare il processo di download. Dal menu Start fare clic su Strumenti di amministrazione/Server Manager/Server locale, quindi fare clic su **Sicurezza avanzata** di Internet Explorer e impostare la configurazione su Off.
>
>

1. Dopo avere eseguito la connessione alla macchina virtuale tramite Desktop remoto, fare clic su **Internet Explorer** nella schermata Start.
2. Selezionare il pulsante **Strumenti** nell'angolo superiore destro (l'icona della ruota dentata) e quindi fare clic su **Opzioni Internet**. Fare clic sulla scheda **Sicurezza**, quindi sull'icona **Siti attendibili** e infine sul pulsante **Siti**. Aggiungere http://*.mysql.com all'elenco dei siti attendibili. Fare clic su **Chiudi** e quindi su **OK**.
3. Nella barra degli indirizzi di Internet Explorer digitare https://dev.mysql.com/downloads/mysql/.
4. Usare il sito di MySQL per individuare e scaricare l'ultima versione del programma di installazione di MySQL per Windows. Quando si sceglie il programma di installazione di MySQL, scaricare la versione che contiene il set di file completo (ad esempio il file mysql-installer-community-5.7.18.0.msi da 352,8 MB) e salvare il programma di installazione.
5. Al termine del download del programma di installazione, fare clic su **Esegui** per avviare l'installazione.
6. Nella pagina **Contratto di Licenza** accettare il contratto di licenza e fare clic su **Avanti**.
7. Nella pagina **Choosing a Setup Type** (Scegli un tipo di installazione) fare clic sul tipo di installazione desiderata, quindi fare clic su **Avanti**. Nei passaggi seguenti si presuppone che sia stato selezionato il tipo di installazione **Server only** .
8. Se viene visualizzata la pagina **Check Requirements** (Verifica requisiti), fare clic su **Esegui** per consentire l'installazione di eventuali componenti mancanti. Seguire eventuali istruzioni visualizzate, ad esempio il runtime C++ Redistributable.
9. Nella pagina **Installation** (Installazione) fare clic su **Execute** (Esegui). Al termine dell'installazione, fare clic su **Next**.

10. Nella pagina **Product Configuration** (Configurazione prodotto) fare clic su **Avanti**.

11. Nella pagina **Type and Networking** specificare il tipo di configurazione e le opzioni di connettività desiderate, inclusa se necessario la porta TCP. Fare clic su **Show Advanced Options** (Mostra opzioni avanzate) e quindi su **Next** (Avanti).
    ![](./media/mysql-2008r2/MySQL_TypeNetworking.png)

12. Nella pagina **Accounts and Roles** specificare una password radice complessa per MySQL. Aggiungere altri account utente di MySQL in base alle esigenze, quindi fare clic su **Next**.

    ![](./media/mysql-2008r2/MySQL_AccountsRoles_Filled.png)
13. Nella pagina **Servizio Windows** specificare le modifiche alle impostazioni predefinite per l'esecuzione di MySQL Server come servizio di Windows in base alle esigenze e quindi fare clic su **Avanti**.

    ![](./media/mysql-2008r2/MySQL_WindowsService.png)
14. Le opzioni in **Plugins and Extensions** (Plug-in ed estensioni) sono facoltative. Fare clic su **Avanti** per continuare.
15. Nella pagina **Advanced Options** (Opzioni avanzate) specificare le modifiche alle opzioni di accesso in base alle esigenze e quindi fare clic su **Avanti**.

    ![](./media/mysql-2008r2/MySQL_AdvOptions.png)
16. Nella pagina **Applica configurazione server** fare clic su **Execute** (Esegui). Dopo aver completato la procedura di configurazione, fare clic su **Finish**.
17. Nella pagina **Product Configuration** (Configurazione prodotto) fare clic su **Avanti**.
18. Nella pagina **Installation Complete** (Installazione completata) fare clic su **Copy Log to Clipboard** (Copia log negli Appunti) se si desidera esaminare il log in un secondo momento, quindi fare clic su **Finish** (Fine).
19. Nella schermata Start digitare **mysql** e fare clic su **MySQL 5.7 Command-Line Client** (Client della riga di comando di MySQL 5.7).
20. Immettere la password radice specificata nel passaggio 12. Verrà visualizzato un prompt in cui è possibile inviare comandi per la configurazione di MySQL. Per informazioni dettagliate sui comandi e la sintassi, vedere i [manuali di riferimento di MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-configuration.html).

    ![](./media/mysql-2008r2/MySQL_CommandPrompt.png)
21. È inoltre possibile configurare impostazioni predefinite per la configurazione del server, ad esempio le unità e le directory di base e di dati. Per altre informazioni, vedere [6.1.2 Server Configuration Defaults](https://dev.mysql.com/doc/refman/5.7/en/server-configuration-defaults.html) (Impostazioni predefinite per la configurazione del server 6.1.2).

## <a name="configure-endpoints"></a>Configurare gli endpoint

Affinché il servizio MySQL sia disponibile per i computer client su Internet, è necessario configurare un endpoint per la porta TCP e creare una regola di Windows Firewall. Il valore predefinito per la porta in cui il servizio MySQL Server è in ascolto dei client MySQL è 3306. È possibile specificare un'altra porta, purché sia coerente con il valore specificato alla pagina **Type and Networking** (Tipo e rete) nel passaggio 11 della procedura precedente.

> [!NOTE]
> Per un uso in produzione, considerare le implicazioni di sicurezza legate alla disponibilità del servizio MySQL Server per tutti i computer su Internet. È possibile definire il set di indirizzi IP di origine che sono autorizzati a usare l'endpoint con un elenco di controllo di accesso (ACL). Per altre informazioni, vedere [Come configurare gli endpoint in una macchina virtuale](setup-endpoints.md).
>
>

Per configurare un endpoint per il servizio MySQL Server:

1. Nel portale di Azure fare clic su **Macchine virtuali (classico)**, quindi sul nome della macchina virtuale di MySQL e infine su **Endpoint**.
2. Nella barra dei comandi fare clic su **Aggiungi**.
3. Nella pagina **Aggiungi endpoint** digitare un nome univoco per **Nome**.
4. Selezionare **TCP** come protocollo.
5. Digitare il numero della porta, ad esempio **3306**, sia in **Porta pubblica** che in **Porta privata**, quindi fare clic su **OK**.

## <a name="add-a-windows-firewall-rule-to-allow-mysql-traffic"></a>Aggiungere una regola di Windows Firewall per consentire il traffico di MySQL
Per aggiungere una regola di Windows Firewall che consenta il traffico di MySQL da Internet, è necessario eseguire il comando seguente in un _prompt dei comandi di Windows PowerShell con privilegi elevati_ nella macchina virtuale di MySQL Server.

    New-NetFirewallRule -DisplayName "MySQL57" -Direction Inbound –Protocol TCP –LocalPort 3306 -Action Allow -Profile Public

## <a name="test-your-remote-connection"></a>Testare la connessione remota
Per testare la connessione remota alla macchina virtuale di Azure in esecuzione nel servizio MySQL Server, è necessario fornire il nome DNS del servizio cloud contenente la rete virtuale.

1. Nel portale di Azure fare clic su **Macchine virtuali (classico)**, quindi sul nome della macchina virtuale di MySQL Server e infine su **Panoramica**.
2. Dal dashboard della macchina virtuale prendere nota del valore in **Nome DNS**. Di seguito è fornito un esempio:

   ![](media/mysql-2008r2/MySQL_DNSName.png)
3. Da un computer locale che esegue MySQL o il client MySQL, eseguire questo comando per accedere come utente MySQL:

     mysql -u <yourMysqlUsername> -p -h <yourDNSname>

   Ad esempio, usando il nome utente MySQL _dbadmin3_ e il nome DNS _testmysql.cloudapp.net_ per la macchina virtuale, il comando che è possibile usare per avviare MySQL è il seguente:

     mysql -u dbadmin3 -p -h testmysql.cloudapp.net

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'esecuzione di MySQL, vedere la [documentazione di MySQL](http://dev.mysql.com/doc/).

