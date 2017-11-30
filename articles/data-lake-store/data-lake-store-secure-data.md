---
title: Protezione dei dati presenti in Azure Data Lake Store | Microsoft Azure
description: Informazioni su come proteggere i dati presenti in Archivio Data Lake di Azure usando gruppi ed elenchi di controllo di accesso
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: e246abaf8d1d7939765e1c878f6dfaf6375294f6
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="securing-data-stored-in-azure-data-lake-store"></a>Protezione dei dati presenti in Archivio Data Lake di Azure
La protezione dei dati presenti in Archivio Data Lake di Azure prevede un approccio suddiviso in tre fasi.

1. Creazione di gruppi di sicurezza in Azure Active Directory, Questi gruppi di sicurezza vengono usati per implementare il controllo degli accessi in base al ruolo nel portale di Azure. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo in Microsoft Azure](../active-directory/role-based-access-control-configure.md).
2. Assegnazione dei gruppi di sicurezza AAD all'account di Archivio Data Lake di Azure. In questo modo è possibile controllare l'accesso all'account di Archivio Data Lake dal portale e le operazioni di gestione dal portale o dalle API.
3. Assegnazione dei gruppi di sicurezza AAD come elenchi di controllo di accesso al file system di Archivio Data Lake.
4. È anche possibile impostare un intervallo di indirizzi IP per i client che possono accedere ai dati in Archivio Data Lake.

Questo articolo fornisce istruzioni sull'uso del portale di Azure per eseguire le attività appena descritte. Per informazioni dettagliate sull'implementazione della sicurezza a livello di account e di dati in Archivio Data Lake, vedere [Security in Azure Data Lake Store](data-lake-store-security-overview.md)(Sicurezza in Archivio Azure Data Lake). Per informazioni di approfondimento su come vengono implementati gli elenchi di controllo di accesso in Azure Data Lake Store, vedere la panoramica sul [controllo di accesso in Data Lake Store](data-lake-store-access-control.md).

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un account di Archivio Data Lake di Azure**. Per istruzioni su come crearne uno, vedere [Introduzione ad Archivio Data Lake di Azure](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Creare gruppi di sicurezza in Azure Active Directory
Per istruzioni su come creare gruppi di sicurezza AAD e come aggiungere utenti ai gruppi, vedere [Gestione dei gruppi di sicurezza in Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

> [!NOTE] 
> È possibile aggiungere utenti e altri gruppi a un gruppo in Azure AD tramite il portale di Azure. Tuttavia, per aggiungere un'entità servizio a un gruppo, usare il [modulo PowerShell di Azure AD](../active-directory/active-directory-accessmanagement-groups-settings-v2-cmdlets.md).
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-azure-data-lake-store-accounts"></a>Assegnare utenti o gruppi di sicurezza ad account di Archivio Data Lake di Azure
Quando si assegnano utenti o gruppi di sicurezza ad account di Archivio Data Lake di Azure, è possibile controllare l'accesso alle operazioni di gestione eseguite sull'account tramite il portale di Azure o le API di Gestione risorse di Azure. 

1. Aprire un account di Archivio Data Lake di Azure. Nel riquadro sinistro fare clic su **Sfoglia** e su **Data Lake Store**, quindi nel pannello Data Lake Store fare clic sul nome dell'account a cui si desidera assegnare un utente o un gruppo di sicurezza.

2. Nel pannello delle impostazioni dell'account Data Lake Store fare clic su **Controllo di accesso (IAM)**. Per impostazione predefinita il pannello elenca il gruppo **Amministratori della sottoscrizione** come proprietario.
   
    ![Assegnare un gruppo di sicurezza all'account Azure Data Lake Store](./media/data-lake-store-secure-data/adl.select.user.icon.png "Assegnare un gruppo di sicurezza all'account Azure Data Lake Store")

    Esistono due modi per aggiungere un gruppo e assegnare i ruoli appropriati.
   
    * Aggiungere un utente/gruppo all'account e assegnare ad esso un ruolo, oppure
    * Aggiungere un ruolo ed assegnare ad esso utenti/gruppi.
     
    In questa sezione si prenderà in esame il primo metodo, che prevede l'aggiunta di un gruppo e la conseguente assegnazione dei ruoli. È possibile eseguire una procedura simile anche per il secondo metodo, in cui si seleziona prima un ruolo e quindi si assegnano i gruppi.
4. Nel pannello **Utenti** fare clic su **Aggiungi** per aprire il pannello **Aggiungi accesso**. Nel pannello **Aggiungi accesso** fare clic su **Selezionare un ruolo** e scegliere un ruolo per l'utente o il gruppo.
   
    ![Aggiungere un ruolo per l'utente](./media/data-lake-store-secure-data/adl.add.user.1.png "Aggiungere un ruolo per l'utente")
   
    I ruoli **Proprietario** e **Collaboratore** offrono l'accesso a un'ampia gamma di funzioni di amministrazione sull'account di Data Lake. Gli utenti che interagiranno con i dati presenti in Data Lake potranno essere aggiunti al ruolo **Lettore **. L'ambito di questi ruoli è limitato alle operazioni di gestione correlate all'account di Archivio Data Lake di Azure.
   
    Per le operazioni sui dati, invece, l'ambito d'azione degli utenti viene definito dalle singole autorizzazioni a livello di file system. Pertanto, un utente con il ruolo Lettore può visualizzare solo le impostazioni amministrative associate all'account, ma potrebbe anche leggere e scrivere dati, in base alle autorizzazioni per il file system che gli sono state assegnate. Le autorizzazioni per il file system di Archivio Data Lake sono descritte nella sezione [Assegnare utenti o gruppi di sicurezza come elenchi di controllo di accesso al file system di Archivio Data Lake di Azure](#filepermissions).
5. Nel pannello **Aggiungi accesso** fare clic su **Aggiungi utenti** per aprire il pannello **Aggiungi utenti**. In questo pannello, cercare il gruppo di sicurezza precedentemente creato in Azure Active Directory. Se è presente un elevato numero di gruppi, usare la casella di testo nella parte superiore per filtrare in base al nome del gruppo. Fare clic su **Seleziona**.
   
    ![Aggiungere un gruppo di sicurezza](./media/data-lake-store-secure-data/adl.add.user.2.png "Aggiungere un gruppo di sicurezza")
   
    Se si desidera aggiungere un gruppo/utente non elencato, è possibile invitarlo selezionando l'icona **Invita** e specificando l'indirizzo di posta elettronica dell'utente/gruppo.
6. Fare clic su **OK**. Il gruppo di sicurezza dovrebbe essere ora aggiunto all'elenco, come illustrato di seguito.
   
    ![Gruppo di sicurezza aggiunto](./media/data-lake-store-secure-data/adl.add.user.3.png "Gruppo di sicurezza aggiunto")

7. L'utente/gruppo di sicurezza dispone ora dell'autorizzazione di accesso all'account di Archivio Data Lake di Azure. Se si desidera assegnare l'accesso a un utente specifico, è possibile aggiungerlo al gruppo di sicurezza. Analogamente, se si desidera revocare l'accesso per un utente, è possibile rimuoverlo dal gruppo di sicurezza. È possibile anche assegnare più gruppi di sicurezza a un account. 

## <a name="filepermissions"></a>Assegnare utenti o gruppi di sicurezza come elenchi di controllo di accesso al file system di Archivio Data Lake di Azure
Con l'assegnazione di utenti/gruppi di sicurezza al file system di Azure Data Lake, si imposta il controllo di accesso sui dati presenti in Archivio Data Lake di Azure.

1. Nel pannello dell'account di Archivio Data Lake, fare clic su **Esplora dati**.
   
    ![Creare directory nell'account Data Lake Store](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Creare directory nell'account Data Lake Store")
2. Nel pannello **Esplora dati** fare clic sul file o sulla cartella per cui si vuole configurare l'elenco di controllo di accesso e quindi fare clic su **Accesso**. Per assegnare l'elenco di controllo di accesso a un file, è necessario fare clic su **Accesso** dal pannello **Anteprima file**.
   
    ![Configurare elenchi di controllo di accesso nel file system di Data Lake](./media/data-lake-store-secure-data/adl.acl.1.png "Configurare elenchi di controllo di accesso nel file system di Data Lake")
3. Il pannello di **accesso** elenca i profili di accesso standard e personalizzato già assegnati all'elemento radice. Fare clic sull'icona **Aggiungi** per aggiungere elenchi di controllo per l'accesso personalizzato.
   
    ![Elencare gli accessi standard e personalizzati](./media/data-lake-store-secure-data/adl.acl.2.png "Elencare gli accessi standard e personalizzati")
   
   * **accesso standard** è un tipo di accesso in stile UNIX, in cui si specificano i permessi di lettura, scrittura ed esecuzione per tre diverse classi utente: proprietario, gruppo e altri.
   * **accesso personalizzato** corrisponde invece agli elenchi di controllo di accesso POSIX, che permettono di impostare autorizzazioni per utenti non anonimi o gruppi specifici e non solo il gruppo o il proprietario del file. 
     
     Per altre informazioni, vedere l'articolo sugli [elenchi di controllo di accesso HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Per altre informazioni sull'implementazione degli elenchi di controllo di accesso in Data Lake Store, vedere [Controllo di accesso in Data Lake Store](data-lake-store-access-control.md).
4. Fare clic sull'icona **Aggiungi** per aprire il pannello **Aggiungi accesso personalizzato**. In questo pannello fare clic su **Seleziona utente o gruppo** e, nel pannello **Seleziona utente o gruppo**, cercare il gruppo di sicurezza precedentemente creato in Azure Active Directory. Se è presente un elevato numero di gruppi, usare la casella di testo nella parte superiore per filtrare in base al nome del gruppo. Fare clic sul gruppo che si desidera aggiungere e quindi su **Seleziona**.
   
    ![Aggiungere un gruppo](./media/data-lake-store-secure-data/adl.acl.3.png "Aggiungere un gruppo")
5. Fare clic su **Selezionare le autorizzazioni**, selezionare le autorizzazioni e se si desidera assegnare le autorizzazioni come un ACL predefinito, ACL di accesso o entrambi. Fare clic su **OK**.
   
    ![Assegnare autorizzazioni a un gruppo](./media/data-lake-store-secure-data/adl.acl.4.png "Assegnare autorizzazioni a un gruppo")
   
    Per altre informazioni sulle autorizzazioni in Data Lake Store e gli ACL predefiniti/di accesso, vedere [Controllo di accesso in Data Lake Store](data-lake-store-access-control.md).
6. Nel pannello **Aggiungi accesso personalizzato** fare clic su **OK**. Il gruppo appena aggiunto, con le autorizzazioni associate, risulterà ora elencato nel pannello di **accesso** .
   
    ![Assegnare autorizzazioni a un gruppo](./media/data-lake-store-secure-data/adl.acl.5.png "Assegnare autorizzazioni a un gruppo")
   
   > [!IMPORTANT]
   > Nella versione corrente l'elenco di **accesso personalizzato**non può contenere più di nove voci. Per aggiungere più di 9 utenti, è necessario creare gruppi di sicurezza, aggiungere utenti a tali gruppi e fornire ai gruppi di sicurezza creati accesso all'account di Archivio Data Lake.
   > 
   > 
7. Se necessario, è possibile modificare le autorizzazioni di accesso anche dopo aver aggiunto il gruppo. Selezionare o deselezionare la casella di controllo per ogni tipo di autorizzazione (lettura, scrittura, esecuzione), in modo da aggiungerla o rimuoverla dal gruppo di sicurezza. Fare clic su **Salva** per salvare le modifiche o su **Ignora** per annullare le modifiche.

## <a name="set-ip-address-range-for-data-access"></a>Impostare l'intervallo di indirizzi IP per l'accesso ai dati
Archivio Azure Data Lake consente di bloccare ulteriormente l'accesso all'archivio dati a livello di rete. È possibile abilitare il firewall, specificare un indirizzo IP e definire un intervallo di indirizzi IP per i client attendibili. Dopo l'abilitazione, solo i client con indirizzo IP compreso nell'intervallo definito possono connettersi all'archivio.

![Impostazioni del firewall e accesso IP](./media/data-lake-store-secure-data/firewall-ip-access.png "Impostazioni del firewall e indirizzo IP")

## <a name="remove-security-groups-for-an-azure-data-lake-store-account"></a>Rimuovere gruppi di sicurezza da un account di Archivio Data Lake di Azure
Quando si rimuovono gruppi di sicurezza da un account di Archivio Data Lake di Azure, si modifica semplicemente l'accesso alle operazioni di gestione eseguite sull'account tramite il portale di Azure o le API di Gestione risorse di Azure.

1. Nel pannello dell'account di Data Lake Store fare clic su **Impostazioni**. Nel pannello **Impostazioni** fare clic su **Utenti**.
   
    ![Assegnare un gruppo di sicurezza all'account Azure Data Lake](./media/data-lake-store-secure-data/adl.select.user.icon.png "Assegnare un gruppo di sicurezza all'account Azure Data Lake")
2. Nel pannello **Utenti** fare clic sul gruppo di sicurezza da rimuovere.
   
    ![Gruppo di sicurezza da rimuovere](./media/data-lake-store-secure-data/adl.add.user.3.png "Gruppo di sicurezza da rimuovere")
3. Nel pannello relativo al gruppo di sicurezza fare clic su **Rimuovi**.
   
    ![Gruppo di sicurezza rimosso](./media/data-lake-store-secure-data/adl.remove.group.png "Gruppo di sicurezza rimosso")

## <a name="remove-security-group-acls-from-azure-data-lake-store-file-system"></a>Rimuovere elenchi di controllo di accesso di gruppi di sicurezza dal file system di Archivio Data Lake di Azure
Quando si rimuovono gli elenchi di controllo di accesso dei gruppi di sicurezza dal file system di Azure Data Lake Store si modifica l'accesso ai dati presenti in Archivio Data Lake.

1. Nel pannello dell'account di Archivio Data Lake, fare clic su **Esplora dati**.
   
    ![Creare directory nell'account Data Lake](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Creare directory nell'account Data Lake")
2. Nel pannello **Esplora dati** fare clic sul file o sulla cartella per cui si vuole rimuovere l'elenco di controllo di accesso e quindi fare clic sull'icona **Accesso** nel pannello dell'account. Per rimuovere l'elenco di controllo di accesso per un file, fare clic su **Accesso** dal pannello **Anteprima file**.
   
    ![Configurare elenchi di controllo di accesso nel file system di Data Lake](./media/data-lake-store-secure-data/adl.acl.1.png "Configurare elenchi di controllo di accesso nel file system di Data Lake")
3. Nel pannello di **accesso**, all'interno della sezione **Accesso personalizzato**, fare clic sul gruppo di sicurezza da rimuovere. Nel pannello **Accesso personalizzato** fare clic su **Rimuovi** e quindi su **OK**.
   
    ![Assegnare autorizzazioni a un gruppo](./media/data-lake-store-secure-data/adl.remove.acl.png "Assegnare autorizzazioni a un gruppo")

## <a name="see-also"></a>Vedere anche
* [Panoramica di Archivio Data Lake di Azure](data-lake-store-overview.md)
* [Copiare i dati da BLOB di archiviazione di Azure ad Archivio Data Lake](data-lake-store-copy-data-azure-storage-blob.md)
* [Usare Azure Data Lake Analytics con Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usare Azure HDInsight con Archivio Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Introduzione ad Archivio Data Lake mediante PowerShell](data-lake-store-get-started-powershell.md)
* [Introduzione ad Archivio Data Lake mediante .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Accesso ai log di diagnostica per Azure Data Lake Store](data-lake-store-diagnostic-logs.md)

