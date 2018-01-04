---
title: Configurare cluster HDInsight aggiunti al dominio - Azure | Microsoft Docs
description: Maggiori informazioni su come configurare i cluster HDInsight aggiunti al dominio
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: 0cbb49cc-0de1-4a1a-b658-99897caf827c
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/15/2017
ms.author: saurinsh
ms.openlocfilehash: 0a9ed1cad8b8d4c566a0da16ac78d096efe187a5
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2017
---
# <a name="configure-domain-joined-hdinsight-sandbox-environment"></a>Configurare l'ambiente sandbox dominio HDInsight

Informazioni su come configurare un cluster Azure HDInsight con la versione autonoma di Active Directory e [cane Apache](http://hortonworks.com/apache/ranger/) per sfruttare i criteri di controllo degli accessi di autenticazione avanzata e un accesso basato sui ruoli. Per altre informazioni, consultare [Introduce Domain-joined HDInsight clusters](apache-domain-joined-introduction.md) (Introduzione ai cluster HDInsight aggiunti al dominio).

Senza dominio aggiunti a un cluster HDInsight, ogni cluster può avere solo un account utente Hadoop HTTP e un account utente SSH.  È possibile ottenere l'autenticazione a più utenti utilizzando:

-   Un computer autonomo Active Directory in esecuzione in Azure IaaS
-   Azure Active Directory
-   Active Directory in esecuzione in ambiente locale del cliente.

L'utilizzo di un autonomo Active Directory in esecuzione in Azure IaaS viene trattato in questo articolo. È l'architettura più semplice di che un cliente può seguire per ottenere il supporto di più utenti in HDInsight. 

> [!IMPORTANT]
> Oozie non è abilitato su HDInsight appartenente al dominio.

## <a name="prerequisite"></a>Prerequisito
* Sottoscrizione di Azure

## <a name="create-an-active-directory"></a>Creare un ambiente Active Directory

Modello di gestione risorse di Azure rende più semplice creare risorse di Azure. In questa sezione, utilizzare un [modello di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/active-directory-new-domain-ha-2-dc/) per creare una nuova foresta e del dominio con due macchine virtuali. Due macchine virtuali fungere da controller di dominio primario e un controller di dominio di backup.

**Per creare un dominio con due controller di dominio**

1. Fare clic sull'immagine seguente per aprire il modello nel portale di Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Factive-directory-new-domain-ha-2-dc%2Fazuredeploy.json" target="_blank"><img src="./media/apache-domain-joined-configure/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Il modello è simile a:

    ![Aggiunto a un dominio di HDInsight creare macchine virtuali del dominio dell'insieme di strutture](./media/apache-domain-joined-configure/hdinsight-domain-joined-create-arm-template.png)

2. Immettere i valori seguenti:

    - **Sottoscrizione**: selezionare una sottoscrizione di Azure.
    - **Nome del gruppo di risorse**: digitare il nome di un gruppo di risorse.  Un gruppo di risorse viene utilizzato per gestire le risorse di Azure che sono correlate a un progetto.
    - **Percorso**: selezionare una località di Azure è vicino a è.
    - **Nome utente amministratore**: questo è il nome utente amministratore di dominio. Questo utente non è l'account utente HTTP del cluster HDInsight. Questo è l'account utilizzato nel corso dell'esercitazione.
    - **Password amministratore**: immettere la password per l'amministratore di dominio.
    - **Nome di dominio**: il nome di dominio deve essere un nome in due parti. Ad esempio: contoso.com, o contoso. Local o hdinsight.test.
    - **Prefisso DNS**: digitare un prefisso DNS
    - **La porta RDP PDC**: (per questa esercitazione, usare il valore predefinito)
    - **Integrazione applicativa dei dati la porta RDP**: (per questa esercitazione, usare il valore predefinito)
    - **posizione degli elementi**: (per questa esercitazione, usare il valore predefinito)
    - **token di firma di accesso condiviso percorso elementi**: (lasciare vuoto per questa esercitazione.)

Impiegato circa 20 minuti per creare le risorse.

## <a name="setup-ldaps"></a>Programma di installazione LDAPS

Lightweight Directory Access Protocol (LDAP) viene utilizzato per leggere e scrivere in Active Directory.

**Per connettersi al controller di dominio primario tramite desktop remoto**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Aprire il gruppo di risorse e quindi aprire la macchina virtuale di dominio primario (PDC) controller. Il nome PDC predefinito è adPDC. 
3. Fare clic su **Connetti** per connettersi al controller di dominio primario tramite desktop remoto.

    ![Aggiunto a un dominio di HDInsight connessione desktop remoto PDC](./media/apache-domain-joined-configure/hdinsight-domain-joined-remote-desktop-pdc.png)


**Per aggiungere i servizi certificati di Active Directory**

4. Aprire **Server Manager** se non è aperto.
5. Fare clic su **Gestisci**, quindi fare clic su **Aggiungi ruoli e funzionalità**.

    ![Aggiunto a un dominio di HDInsight Aggiungi ruoli e funzionalità](./media/apache-domain-joined-configure/hdinsight-domain-joined-add-roles.png)
5. Da "Prima di iniziare", fare clic su **Avanti**.
6. Selezionare **Installazione basata su ruoli o basata su funzionalità**, quindi fare clic su **Avanti**.
7. Selezionare il PDC e quindi fare clic su **Avanti**.  Il nome PDC predefinito è adPDC.
8. Selezionare **Servizi certificati Active Directory**.
9. Fare clic su **Aggiungi funzionalità** dalla finestra di dialogo popup.
10. Seguire la procedura guidata, usare le impostazioni predefinite per il resto della procedura.
11. Fare clic su **Chiudi** per chiudere la procedura guidata.

**Per configurare il certificato di Active Directory**

1. In Server Manager fare clic sull'icona di notifica gialla e quindi fare clic su **servizi configurare Active Directory certificati**.

    ![Aggiunto a un dominio di HDInsight consente di configurare il certificato di Active Directory](./media/apache-domain-joined-configure/hdinsight-domain-joined-configure-ad-certificate.png)

2. Fare clic su * * selezionare servizi di ruolo a sinistra, **autorità di certificazione**, quindi fare clic su **Avanti**.
3. Seguire la procedura guidata, usare le impostazioni predefinite per il resto della procedura (fare clic su **configura** l'ultimo passaggio).
4. Fare clic su **Chiudi** per chiudere la procedura guidata.

## <a name="optional-create-ad-users-and-groups"></a>(Facoltativo) Creare gruppi di utenti e Active Directory

**Per creare utenti e gruppi in Active Directory**
1. Connettersi al controller di dominio primario tramite desktop remoto
1. Aprire **utenti e computer Active Directory**.
2. Nel riquadro a sinistra, selezionare il nome di dominio.
3. Fare clic su di **creare un nuovo utente nel contenitore corrente** icona nel menu in alto.

    ![Gli utenti di creare aggiunti a un dominio di HDInsight](./media/apache-domain-joined-configure/hdinsight-domain-joined-create-ad-user.png)
4. Seguire le istruzioni per creare alcuni utenti. Ad esempio, hiveuser1 e hiveuser2.
5. Fare clic su di **creare un nuovo gruppo nel contenitore corrente** icona nel menu in alto.
6. Seguire le istruzioni per creare un gruppo denominato **HDInsightUsers**.  Questo gruppo viene usato quando si crea un cluster HDInsight più avanti in questa esercitazione.

> [!IMPORTANT]
> Prima di creare un cluster di HDInsight appartenenti a un dominio, è necessario riavviare la macchina virtuale PDC.

## <a name="create-an-hdinsight-cluster-in-the-vnet"></a>Creare un cluster HDInsight nella rete virtuale

In questa sezione, utilizzare il portale di Azure per aggiungere un cluster HDInsight alla rete virtuale creata utilizzando il modello di gestione risorse in precedenza nell'esercitazione. In questo articolo descrive solo le informazioni specifiche per la configurazione di cluster appartenenti a un dominio.  Per informazioni generali, vedere [basati su Linux creare cluster HDInsight tramite il portale di Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).  

**Per creare un cluster di HDInsight appartenenti a un dominio**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Aprire il gruppo di risorse che è stato creato utilizzando il modello di gestione risorse in precedenza nell'esercitazione.
3. Aggiungere un cluster HDInsight al gruppo di risorse.
4. Selezionare **personalizzato** opzione:

    ![Opzione create HDInsight aggiunti a un dominio personalizzato](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-custom-configuration-option.png)

    Sono presenti sei sezioni usando l'opzione di configurazione personalizzato: nozioni di base, archiviazione, applicazione, dimensioni del Cluster, le impostazioni avanzate e riepilogo.
5. Nel **nozioni di base** sezione:

    - Tipo di cluster: selezionare **pacchetto di sicurezza Enterprise**. Attualmente il pacchetto di protezione aziendale può essere abilitato solo per i seguenti tipi di cluster: Hadoop, Query interattive e Spark.

        ![Pacchetto di sicurezza enterprise aggiunti a un dominio di HDInsight](./media/apache-domain-joined-configure/hdinsight-creation-enterprise-security-package.png)
    - Nome utente account di accesso del cluster: questo è l'utente di Hadoop HTTP. Questo account è diverso dall'account di amministratore di dominio.
    - Gruppo di risorse: selezionare il gruppo di risorse creati in precedenza con il modello di gestione risorse.
    - Percorso: Il percorso deve essere uguale a quello utilizzato quando si crea la rete virtuale e i controller di dominio utilizzando il modello di gestione risorse.

6. Nel **impostazioni avanzate** sezione:

    - Impostazioni di dominio:

        ![HDInsight dominio dominio impostazioni avanzate](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-advanced-domain-settings.png)
        
        - Nome di dominio: immettere il nome di dominio è stato utilizzato in [creare un ambiente Active Directory](#create-an-active-directory).
        - Nome utente di dominio: immettere il nome utente amministratore di Active Directory è stato utilizzato in [creare un ambiente Active Directory](#create-an-active-directory).
        - Unità organizzativa: Vedere la schermata per un esempio.
        - URL LDAPS: Vedere la schermata per un esempio
        - Il gruppo di utenti di accesso: immettere il nome del gruppo utente creato nel [Create AD utenti e gruppi](#optionally-createad-users-and-groups)
    - Rete virtuale: selezionare la rete virtuale è stato creato in [creare un ambiente Active Directory](#create-an-active-directory). Il nome predefinito utilizzato nel modello è **adVNET**.
    - Subnet: È il nome predefinito utilizzato nel modello **adSubnet**.



Al termine dell'esercitazione, è consigliabile eliminare il cluster. Con HDInsight, i dati vengono archiviati in Archiviazione di Azure ed è possibile eliminare tranquillamente un cluster quando non viene usato. Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, economicamente ha senso eliminare i cluster quando non vengono usati. Per istruzioni sull'eliminazione di un cluster, vedere [Gestire cluster Hadoop in HDInsight tramite il portale di Azure](../hdinsight-administer-use-management-portal.md#delete-clusters).

## <a name="next-steps"></a>Passaggi successivi
* Per configurare i criteri ed eseguire query Hive, vedere [Configure Hive policies for Domain-joined HDInsight clusters](apache-domain-joined-run-hive.md) (Configurare criteri Hive per cluster HDInsight aggiunti al dominio).
* Per usare SSH per la connessione a cluster HDInsight aggiunti al dominio, vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

