---
title: Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight
description: Informazioni su come usare Archiviazione Azure Data Lake Storage Gen2 con cluster Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: howto
ms.date: 01/10/2019
ms.author: hrasheed
ms.openlocfilehash: 1d43c7b6dd1bdec0a2507d8ce1a3883f5ce31a39
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54479397"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight

Azure Data Lake Storage Gen2 è un set di funzionalità dedicate all'analisi dei Big Data e basate su Archiviazione BLOB di Azure. È il risultato della combinazione delle funzionalità di Archiviazione BLOB di Azure e Azure Data Lake Storage Gen1. Da questa combinazione nasce un servizio che offre le funzionalità di Azure Data Lake Storage Gen1, ad esempio la semantica dei file system, la sicurezza a livello di directory e file e la scalabilità, insieme alle funzionalità di archiviazione a basso costo e a più livelli, elevata disponibilità e ripristino di emergenza di Archiviazione BLOB di Azure.

## <a name="data-lake-storage-gen2-availability"></a>Disponibilità di Data Lake Storage Gen2

Azure Data Lake Storage Gen2 è disponibile come opzione di archiviazione per quasi tutti i tipi di cluster Azure HDInsight, sia come account di archiviazione predefinito che come account di archiviazione aggiuntivo. HBase, tuttavia, può avere un solo account Data Lake Storage Gen2.

> [!Note] 
> Dopo aver selezionato Data Lake Storage Gen2 come **tipo di archiviazione primario**, non è possibile selezionare un account Data Lake Storage Gen1 come risorsa di archiviazione aggiuntiva.

## <a name="creating-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Creazione di un cluster HDInsight con Data Lake Storage Gen2

Per creare un cluster HDInsight che usa Data Lake Storage Gen2 per l'archiviazione, usare la procedura seguente per creare un account Data Lake Storage Gen2 configurato correttamente.

1. Creare un'identità gestita assegnata dall'utente, se non è già disponibile. Vedere [Creare, elencare, eliminare o assegnare un ruolo a un'identità gestita assegnata dall'utente mediante il portale di Azure](/../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal#create-a-user-assigned-managed-identity.md).

    ![Creare un'identità gestita assegnata dall'utente](./media/hdinsight-hadoop-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

1. Creare un account di archiviazione di Azure Data Lake Storage Gen2. Verificare che l'opzione **Hierarchical filesystem** (File system gerarchico) sia abilitata. Vedere [Avvio rapido: Creare un account di archiviazione di Azure Data Lake Storage Gen2](/../storage/blobs/data-lake-storage-quickstart-create-account.md) per altre informazioni.

    ![Screenshot che mostra la creazione dell'account di archiviazione nel portale di Azure](./media/hdinsight-hadoop-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)
 
1. Assegnare l'identità gestita al ruolo **Collaboratore ai dati del BLOB di archiviazione (anteprima)** nell'account di archiviazione. Vedere [Gestire i diritti di accesso a dati di code e BLOB di Azure con il controllo degli accessi in base al ruolo (anteprima)](/../storage/common/storage-auth-aad-rbac#assign-a-role-scoped-to-the-storage-account-in-the-azure-portal.md)

    1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione.
    1. Selezionare l'account di archiviazione, quindi selezionare **Controllo di accesso (IAM)** per visualizzare le impostazioni di controllo di accesso per l'account. Selezionare la scheda **Assegnazioni di ruolo** per visualizzare l'elenco di assegnazioni di ruolo.
    
        ![Screenshot che mostra le impostazioni di controllo di accesso per l'archiviazione](./media/hdinsight-hadoop-data-lake-storage-gen2/portal-access-control.png)
    
    1. Fare clic sul pulsante **Aggiungi un'assegnazione di ruolo** per aggiungere un nuovo ruolo.
    1. Nella finestra **Aggiungi assegnazione di ruolo** selezionare il ruolo **Collaboratore ai dati del BLOB di archiviazione (anteprima)**. Selezionare quindi la sottoscrizione a cui sono associati l'identità gestita e l'account di archiviazione. Individuare l'identità gestita assegnata dall'utente creata in precedenza. Infine selezionare l'identità gestita, che apparirà in **Membri selezionati**.
    
        ![Screenshot che mostra come assegnare un ruolo di controllo degli accessi in base al ruolo](./media/hdinsight-hadoop-data-lake-storage-gen2/add-rbac-role2.png)
    
    1. Fare clic su **Salva**. L'identità assegnata dall'utente selezionata è ora elencata nel ruolo **Collaboratore**.

    1. Al termine della configurazione iniziale, è possibile creare un cluster tramite il portale. Il cluster deve trovarsi nella stessa area di Azure dell'account di archiviazione. Nella sezione **Archiviazione** del menu di creazione del cluster selezionare le opzioni seguenti:
        
        * Per **Tipo di archiviazione primario** fare clic su **Azure Data Lake Storage Gen2**.
        * In **Selezionare un account di archiviazione** cercare e selezionare l'account di archiviazione di Data Lake Storage Gen2 appena creato.
        
            ![Impostazioni di archiviazione per l'uso di Data Lake Storage Gen2 con Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
        
        * In **Identità** selezionare la sottoscrizione corretta e l'identità gestita assegnata dall'utente appena creata.
        
            ![Impostazioni di identità per l'uso di Data Lake Storage Gen2 con Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/managed-identity-cluster-creation.png)

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Controllo di accesso per Azure Data Lake Storage Gen2 in HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Quali tipi di autorizzazioni sono supportati da Data Lake Storage Gen2?

Azure Data Lake Storage Gen2 implementa un modello di controllo di accesso che supporta sia il controllo degli accessi in base al ruolo di Azure che gli elenchi di controllo di accesso (ACL) di tipo POSIX. Data Lake Storage Gen1 supporta solo gli elenchi di controllo di accesso per controllare l'accesso ai dati.

Il controllo degli accessi in base al ruolo di Azure usa le assegnazioni di ruolo per applicare in modo efficace set di autorizzazioni a utenti, gruppi ed entità servizio per le risorse di Azure. Tali risorse di Azure sono in genere vincolate alle risorse di primo livello (ad esempio gli account di archiviazione di Azure). Per Archiviazione di Azure, e anche per Azure Data Lake Storage Gen2, questo meccanismo è stato esteso alla risorsa del file system.

 Per altre informazioni sulle autorizzazioni per i file con il controllo degli accessi in base al ruolo, vedere [Controllo degli accessi in base al ruolo di Azure](/../storage/blobs/data-lake-storage-access-control#azure-role-based-access-control-rbac.md).

Per altre informazioni sulle autorizzazioni per i file con gli elenchi di controllo di accesso, vedere [Elenchi di controllo di accesso per file e directory](/../storage/blobs/data-lake-storage-access-control#access-control-lists-on-files-and-directories.md).


### <a name="how-do-i-control-access-to-my-data-in-gen2"></a>Come si controlla l'accesso ai dati in Gen2?

La capacità del cluster HDInsight di accedere ai file in Data Lake Storage Gen2 è controllata dalle identità gestite. Un'identità gestita è un'identità registrata in Azure AD le cui credenziali sono gestite da Azure. Non è necessario registrare le entità servizio in Azure AD e gestire credenziali come i certificati.

Sono disponibili due tipi di identità gestite per i servizi di Azure: assegnata dal sistema e assegnata dall'utente. Azure HDInsight usa identità gestite assegnate dall'utente per accedere ad Azure Data Lake Storage Gen2. Un'identità gestita assegnata dall'utente viene creata come risorsa di Azure autonoma. Tramite un processo di creazione, Azure crea un'identità nel tenant di Azure AD considerato attendibile dalla sottoscrizione in uso. Dopo la creazione, l'identità può essere assegnata a una o più istanze del servizio di Azure. Il ciclo di vita di un'identità assegnata dall'utente viene gestito separatamente dal ciclo di vita delle istanze del servizio di Azure a cui l'identità è assegnata. Per altre informazioni sulle identità gestite, vedere [Funzionamento delle identità gestite per le risorse di Azure](/../active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka.md).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-using-hive-or-other-services"></a>Come si impostano le autorizzazioni per consentire agli utenti di Azure AD di eseguire query sui dati in Data Lake Storage Gen2 usando Hive o altri servizi?

Usare i gruppi di sicurezza di Azure AD come entità assegnate negli elenchi di controllo di accesso. Evitare di assegnare direttamente singoli utenti o entità servizio con autorizzazioni di accesso ai file. Quando si usano i gruppi di sicurezza di Azure AD per controllare il flusso delle autorizzazioni, è possibile aggiungere e rimuovere utenti ed entità servizio senza riapplicare gli elenchi di controllo di accesso a un'intera struttura di directory. È sufficiente aggiungere o rimuovere gli utenti dal gruppo di sicurezza di Azure AD appropriato. Gli elenchi di controllo di accesso non vengono ereditati, quindi per riapplicarli è necessario aggiornare l'elenco di controllo di accesso in ogni file e sottodirectory.

## <a name="next-steps"></a>Passaggi successivi

* [Usare l'anteprima di Azure Data Lake Storage Gen2 con cluster Azure HDInsight](/../storage/blobs/data-lake-storage-use-hdi-cluster.md)
* [Azure HDInsight integration with Data Lake Storage Gen2 preview - ACL and security update](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/) (Integrazione di Azure HDInsight con Data Lake Storage Gen2 (anteprima) - Aggiornamento di ACL e sicurezza)
* [Anteprima di Azure Data Lake Storage Gen2 - Introduzione](/../storage/blobs/data-lake-storage-introduction.md)