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
ms.date: 01/10/2018
ms.author: saurinsh
ms.openlocfilehash: 4921e329c2ec8ce3d5bbf8a0851146e13d5f6cd3
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="configure-domain-joined-hdinsight-sandbox-environment"></a>Configurare l'ambiente sandbox HDInsight aggiunto al dominio

In questo articolo viene descritto come configurare un cluster Azure HDInsight con un'istanza autonoma di Active Directory e [Apache Ranger](http://hortonworks.com/apache/ranger/) per sfruttare l'autenticazione avanzata e i criteri di controllo degli accessi in base al ruolo. Per altre informazioni, consultare [Introduce Domain-joined HDInsight clusters](apache-domain-joined-introduction.md) (Introduzione ai cluster HDInsight aggiunti al dominio).

Senza un cluster HDInsight aggiunto al dominio, ogni cluster può avere solo un account utente Hadoop HTTP e un account utente SSH.  È possibile ottenere l'autenticazione multiutente usando:

-   Un'istanza autonoma di Active Directory in esecuzione nell'ambiente IaaS di Azure.
-   Azure Active Directory.
-   Active Directory in esecuzione nell'ambiente locale del cliente.

Questo articolo illustra l'uso di un'istanza autonoma di Active Directory in esecuzione nell'ambiente IaaS di Azure. Si tratta dell'architettura più semplice che un cliente può scegliere per ottenere il supporto multiutente in HDInsight. Questo articolo descrive due approcci per questa configurazione:

- Opzione 1: usare un modello di Azure Resource Manager per creare sia l'istanza autonoma di Active Directory che il cluster HDInsight.
- Opzione 2: l'intero processo viene suddiviso nei passaggi seguenti:
    - Creare un'istanza di Active Directory usando un modello.
    - Configurare LDAPS.
    - Creare utenti e gruppi di AD
    - Creare un cluster HDInsight

> [!IMPORTANT]
> Oozie non è abilitato su HDInsight appartenente al dominio.

## <a name="prerequisite"></a>Prerequisito
* Sottoscrizione di Azure

## <a name="option-1-one-step-approach"></a>Opzione 1: approccio in un solo passaggio
In questa sezione si apre un modello di Azure Resource Manager dal portale di Azure. Il modello viene usato per creare un'istanza autonoma di Active Directory e un cluster HDInsight. Attualmente, è possibile creare cluster Interactive Query, cluster Spark e cluster Hadoop aggiunti al dominio.

1. Fare clic sull'immagine seguente per aprire il modello nel portale di Azure. Il modello è disponibile in [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/).
   
    Per creare un cluster Spark:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/http%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fdomain-joined%2Fspark%2Ftemplate.json" target="_blank"><img src="../hbase/media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Per creare un cluster Interactive Query:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/http%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fdomain-joined%2Finteractivequery%2Ftemplate.json" target="_blank"><img src="../hbase/media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Per creare un cluster Hadoop:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/http%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fdomain-joined%2Fhadoop%2Ftemplate.json" target="_blank"><img src="../hbase/media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Immettere i valori, selezionare **Accetto le condizioni riportate sopra**, selezionare **Aggiungi al dashboard** e quindi fare clic su **Acquista**. Passare il puntatore del mouse sul simbolo di informazioni accanto ai campi per visualizzare le descrizioni. La maggior parte dei valori è stata popolata. È possibile usare i valori predefiniti o valori personalizzati.

    - **Gruppo di risorse**: immettere un nome per il gruppo di risorse di Azure.
    - **Località**: scegliere una località vicina.
    - **Nuovo nome account di archiviazione**: immettere un nome per l'account di archiviazione di Azure. Il nuovo account di archiviazione viene usato dal controller di dominio primario (PDC), dal controller di dominio di backup (BDC) e dal cluster HDInsight come account di archiviazione predefinito.
    - **Nome utente amministratore**: immettere il nome utente dell'amministratore di dominio.
    - **Password amministratore**: immettere la password dell'amministratore di dominio.
    - **Nome dominio**: il nome predefinito è *contoso.com*.  Se si cambia il nome di dominio, è necessario aggiornare anche il campo **Certificato LDAP sicuro** e il campo **Organizational Unit DN** (Nome di dominio unità organizzativa).
    - **Nome cluster**: immettere il nome del cluster HDInsight.
    - **Tipo di cluster**: non modificare questo valore. Se si vuole modificare il tipo di cluster, usare il modello specifico nell'ultimo passaggio.

    Alcuni valori sono hardcoded nel modello, ad esempio il numero di istanze di nodi del ruolo di lavoro è due.  Per modificare i valori hardcoded, fare clic su **Modifica modello**.

    ![Comando Modifica modello nel cluster HDInsight aggiunto al dominio](./media/apache-domain-joined-configure/hdinsight-domain-joined-edit-template.png)

Dopo avere completato il modello, nel gruppo di risorse vengono create 23 risorse.

## <a name="option-2-multi-step-approach"></a>Opzione 2: approccio in più passaggi

Questa sezione include quattro passaggi:

1. Creare un'istanza di Active Directory usando un modello.
2. Configurare LDAPS.
3. Creare utenti e gruppi di AD
4. Creare un cluster HDInsight

### <a name="create-an-active-directory"></a>Creare un'istanza di Active Directory

Un modello di Azure Resource Manager semplifica la creazione delle risorse di Azure. In questa sezione si usa un [modello di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/active-directory-new-domain-ha-2-dc/) per creare una nuova foresta e un nuovo dominio con due macchine virtuali. Le due macchine virtuali fungono da controller di dominio primario e da controller di dominio di backup.

**Per creare un dominio con due controller di dominio**

1. Fare clic sull'immagine seguente per aprire il modello nel portale di Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Factive-directory-new-domain-ha-2-dc%2Fazuredeploy.json" target="_blank"><img src="./media/apache-domain-joined-configure/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Il modello è simile al seguente:

    ![Creazione di macchine virtuali del dominio e della foresta nell'ambiente HDInsight aggiunto al dominio](./media/apache-domain-joined-configure/hdinsight-domain-joined-create-arm-template.png)

2. Immettere i valori seguenti:

    - **Sottoscrizione**: selezionare una sottoscrizione di Azure.
    - **Nome gruppo di risorse**: digitare un nome del gruppo di risorse.  Il gruppo di risorse viene usato per gestire le risorse di Azure correlate a un progetto.
    - **Località**: scegliere una località di Azure vicina.
    - **Nome utente amministratore**: nome utente dell'amministratore di dominio. Questo utente non è l'account utente HTTP del cluster HDInsight. Si tratta dell'account usato nel corso dell'esercitazione.
    - **Password amministratore**: immettere la password per l'amministratore di dominio.
    - **Nome di dominio**: il nome di dominio deve essere costituito da due parti. Ad esempio: contoso.com, contoso.local o hdinsight.test.
    - **Prefisso DNS**: digitare un prefisso DNS
    - **PDC RDP Port** (Porta RDP PDC): (usare il valore predefinito per questa esercitazione)
    - **BDC RDP Port** (Porta RDP BDC): (usare il valore predefinito per questa esercitazione)
    - **artifacts location** (Posizione elementi): (usare il valore predefinito per questa esercitazione)
    - **artifacts location SAS token** (Token di firma di accesso condiviso posizione elementi): (lasciare vuoto per questa esercitazione)

La creazione delle risorse richiede circa 20 minuti.

### <a name="setup-ldaps"></a>Configurare LDAPS

Il protocollo LDAP (Lightweight Directory Access Protocol) viene usato per leggere e scrivere in AD.

**Per connettersi al controller di dominio primario (PDC) tramite Desktop remoto**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Aprire il gruppo di risorse e quindi aprire la macchina virtuale del controller di dominio primario (PDC). Il nome predefinito del controller di dominio primario (PDC) è adPDC. 
3. Fare clic su **Connetti** per connettersi al controller di dominio primario (PDC) tramite Desktop remoto.

    ![Connessione al controller di dominio primario (PDC) tramite Desktop remoto nell'ambiente HDInsight aggiunto al dominio](./media/apache-domain-joined-configure/hdinsight-domain-joined-remote-desktop-pdc.png)


**Per aggiungere Servizi certificati Active Directory**

4. Aprire **Server Manager** se non è già aperto.
5. Fare clic su **Gestisci** e quindi su **Aggiungi ruoli e funzionalità**.

    ![Aggiunta di ruoli e funzionalità nell'ambiente HDInsight aggiunto al dominio](./media/apache-domain-joined-configure/hdinsight-domain-joined-add-roles.png)
5. In "Prima di iniziare" fare clic su **Avanti**.
6. Selezionare **Installazione basata su ruoli o basata su funzionalità**, quindi fare clic su **Avanti**.
7. Selezionare il controller di dominio primario (PDC) e quindi fare clic su **Avanti**.  Il nome predefinito del controller di dominio primario (PDC) è adPDC.
8. Selezionare **Servizi certificati Active Directory**.
9. Fare clic su **Aggiungi funzionalità** nella finestra di dialogo popup.
10. Seguire la procedura guidata e usare le impostazioni predefinite per il resto della procedura.
11. Fare clic su **Chiudi** per chiudere la procedura guidata.

**Per configurare il certificato di AD**

1. Da Server Manager fare clic sull'icona di notifica gialla e quindi fare clic su **Configure Active Directory Certificate services** (Configura Servizi certificati Active Directory).

    ![Configurazione certificato di AD nell'ambiente HDInsight aggiunto al dominio](./media/apache-domain-joined-configure/hdinsight-domain-joined-configure-ad-certificate.png)

2. Fare clic su **Servizi ruolo** a sinistra, selezionare **Autorità di certificazione** e quindi fare clic su **Avanti**.
3. Seguire la procedura guidata e usare le impostazioni predefinite per il resto della procedura (fare clic su **Configura** nell'ultimo passaggio).
4. Fare clic su **Chiudi** per chiudere la procedura guidata.

### <a name="optional-create-ad-users-and-groups"></a>(Facoltativo) Creare utenti e gruppi di AD

**Per creare utenti e gruppi in AD**
1. Connettersi al controller di dominio primario (PDC) tramite Desktop remoto
1. Aprire **Utenti e computer di Active Directory**.
2. Selezionare il nome di dominio nel riquadro sinistro.
3. Fare clic sull'icona **Crea un nuovo utente nel contenitore corrente** nel menu in alto.

    ![Creazione di utenti nell'ambiente HDInsight aggiunto al dominio](./media/apache-domain-joined-configure/hdinsight-domain-joined-create-ad-user.png)
4. Seguire le istruzioni per creare alcuni utenti. Ad esempio, hiveuser1 e hiveuser2.
5. Fare clic sull'icona **Crea un nuovo gruppo nel contenitore corrente** nel menu in alto.
6. Seguire le istruzioni per creare un gruppo denominato **HDInsightUsers**.  Questo gruppo viene usato quando si crea un cluster HDInsight più avanti in questa esercitazione.

> [!IMPORTANT]
> Prima di creare un cluster HDInsight aggiunto al dominio, è necessario riavviare la macchina virtuale del controller di dominio primario (PDC).

### <a name="create-an-hdinsight-cluster-in-the-vnet"></a>Creare un cluster HDInsight nella rete virtuale

In questa sezione si usa il portale di Azure per aggiungere un cluster HDInsight alla rete virtuale creata in precedenza nell'esercitazione usando il modello di Resource Manager. L'articolo fornisce solo le informazioni specifiche per la configurazione del cluster aggiunto al dominio.  Per informazioni generali, vedere [Creare cluster basati su Linux in HDInsight tramite il portale di Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).  

**Per creare un cluster HDInsight aggiunto al dominio**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Aprire il gruppo di risorse creato usando il modello di Resource Manager in precedenza nell'esercitazione.
3. Aggiungere un cluster HDInsight al gruppo di risorse.
4. Selezionare l'opzione **Personalizzate**:

    ![Opzione di creazione personalizzata nell'ambiente HDInsight aggiunto al dominio](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-custom-configuration-option.png)

    L'opzione di configurazione personalizzata include sei sezioni: Informazioni di base, Archiviazione, Applicazione, Dimensioni del cluster, Impostazioni avanzate e Riepilogo.
5. Nella sezione **Informazioni di base**:

    - Tipo di cluster: selezionare **Pacchetto di sicurezza aziendale**. Attualmente il pacchetto di sicurezza aziendale può essere abilitato solo per i tipi di cluster seguenti: Hadoop, Interactive Query e Spark.

        ![Pacchetto di sicurezza aziendale nell'ambiente HDInsight aggiunto al dominio](./media/apache-domain-joined-configure/hdinsight-creation-enterprise-security-package.png)
    - Nome utente dell'account di accesso del cluster: si tratta dell'utente Hadoop HTTP. Questo account è diverso da quello dell'amministratore di dominio.
    - Gruppo di risorse: selezionare il gruppo di risorse creato in precedenza usando il modello di Resource Manager.
    - Località: la località deve corrispondere a quella usata al momento della creazione della rete virtuale e dei controller di dominio con il modello di Resource Manager.

6. Nella sezione **Impostazioni avanzate**:

    - Impostazioni del dominio:

        ![Impostazioni avanzate del dominio nell'ambiente HDInsight aggiunto al dominio](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-advanced-domain-settings.png)
        
        - Nome di dominio: immettere il nome di dominio usato in [Creare un'istanza di Active Directory](#create-an-active-directory).
        - Nome utente di dominio: immettere il nome utente dell'amministratore di AD usato in [Creare un'istanza di Active Directory](#create-an-active-directory).
        - Unità organizzativa: vedere lo screenshot per un esempio.
        - URL LDAPS: vedere lo screenshot per un esempio.
        - Gruppo utenti di accesso: immettere il nome del gruppo utenti creato in [Creare utenti e gruppi di AD](#optionally-createad-users-and-groups)
    - Rete virtuale: selezionare la rete virtuale creata in [Creare un'istanza di Active Directory](#create-an-active-directory). Il nome predefinito usato nel modello è **adVNET**.
    - Subnet: il nome predefinito usato nel modello è **adSubnet**.



Al termine dell'esercitazione, è consigliabile eliminare il cluster. Con HDInsight, i dati vengono archiviati in Archiviazione di Azure ed è possibile eliminare tranquillamente un cluster quando non viene usato. Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, economicamente ha senso eliminare i cluster quando non vengono usati. Per istruzioni sull'eliminazione di un cluster, vedere [Gestire cluster Hadoop in HDInsight tramite il portale di Azure](../hdinsight-administer-use-management-portal.md#delete-clusters).

## <a name="next-steps"></a>Passaggi successivi
* Per configurare i criteri ed eseguire query Hive, vedere [Configure Hive policies for Domain-joined HDInsight clusters](apache-domain-joined-run-hive.md) (Configurare criteri Hive per cluster HDInsight aggiunti al dominio).
* Per usare SSH per la connessione a cluster HDInsight aggiunti al dominio, vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

