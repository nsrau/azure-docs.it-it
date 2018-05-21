---
title: Protezione dei dati presenti in Azure Data Lake Store | Microsoft Azure
description: Informazioni su come proteggere i dati presenti in Archivio Data Lake di Azure usando gruppi ed elenchi di controllo di accesso
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: 5b83f02c55d0aa7b2e122d7fc8c9ef5734cdd924
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
---
# <a name="securing-data-stored-in-azure-data-lake-store"></a>Protezione dei dati presenti in Archivio Data Lake di Azure
La protezione dei dati presenti in Archivio Data Lake di Azure prevede un approccio suddiviso in tre fasi.  Il controllo degli accessi in base al ruolo e gli elenchi di controllo di accesso devono essere impostati in modo da consentire completamente l'accesso ai dati per utenti e gruppi di sicurezza.

1. Creazione di gruppi di sicurezza in Azure Active Directory, Questi gruppi di sicurezza vengono usati per implementare il controllo degli accessi in base al ruolo nel portale di Azure. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo in Microsoft Azure](../role-based-access-control/role-assignments-portal.md).
2. Assegnazione dei gruppi di sicurezza AAD all'account di Archivio Data Lake di Azure. In questo modo è possibile controllare l'accesso all'account di Archivio Data Lake dal portale e le operazioni di gestione dal portale o dalle API.
3. Assegnazione dei gruppi di sicurezza AAD come elenchi di controllo di accesso al file system di Archivio Data Lake.
4. È anche possibile impostare un intervallo di indirizzi IP per i client che possono accedere ai dati in Archivio Data Lake.

Questo articolo fornisce istruzioni sull'uso del portale di Azure per eseguire le attività appena descritte. Per informazioni dettagliate sull'implementazione della sicurezza a livello di account e di dati in Archivio Data Lake, vedere [Security in Azure Data Lake Store](data-lake-store-security-overview.md)(Sicurezza in Archivio Azure Data Lake). Per informazioni di approfondimento su come vengono implementati gli elenchi di controllo di accesso in Azure Data Lake Store, vedere la panoramica sul [controllo di accesso in Data Lake Store](data-lake-store-access-control.md).

## <a name="prerequisites"></a>prerequisiti
Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un account Azure Data Lake Store**. Per istruzioni su come crearne uno, vedere [Introduzione ad Archivio Data Lake di Azure](data-lake-store-get-started-portal.md)

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

1. Aprire un account di Archivio Data Lake di Azure. Nel riquadro sinistro fare clic su **Tutte le risorse** e nel pannello Tutte le risorse fare clic sul nome dell'account al quale si vuole assegnare un utente o un gruppo di sicurezza.

2. Nel pannello dell'account Data Lake Store fare clic su **Controllo di accesso (IAM)**. Per impostazione predefinita, il pannello elenca i proprietari della sottoscrizione come proprietario.
   
    ![Assegnare un gruppo di sicurezza all'account Azure Data Lake Store](./media/data-lake-store-secure-data/adl.select.user.icon.png "Assegnare un gruppo di sicurezza all'account Azure Data Lake Store")

3. Nel pannello **Controllo di accesso (IAM)** fare clic su **Aggiungi** per aprire il pannello **Aggiungi autorizzazioni**. Nel pannello **Aggiungi autorizzazioni** selezionare un ruolo per l'utente o il gruppo in **Ruolo**. Cercare il gruppo di sicurezza creato in precedenza in Azure Active Directory e selezionarlo. Se sono presenti molti utenti e gruppi tra cui cercare, usare la casella di testo **Seleziona** per filtrare in base al nome del gruppo. 
   
    ![Aggiungere un ruolo per l'utente](./media/data-lake-store-secure-data/adl.add.user.1.png "Aggiungere un ruolo per l'utente")
   
    I ruoli **Proprietario** e **Collaboratore** offrono l'accesso a un'ampia gamma di funzioni di amministrazione sull'account di Data Lake. Gli utenti che interagiranno con i dati presenti in Data Lake, ma che devono comunque visualizzare le informazioni sulla gestione degli account possono essere aggiunti al ruolo **Lettore**. L'ambito di questi ruoli è limitato alle operazioni di gestione correlate all'account di Archivio Data Lake di Azure.
   
    Per le operazioni sui dati, le operazioni consentite agli utenti sono definite da singole autorizzazioni a livello di file system. Pertanto, un utente con il ruolo Lettore può visualizzare solo le impostazioni amministrative associate all'account, ma potrebbe anche leggere e scrivere dati, in base alle autorizzazioni per il file system che gli sono state assegnate. Le autorizzazioni per il file system di Archivio Data Lake sono descritte nella sezione [Assegnare utenti o gruppi di sicurezza come elenchi di controllo di accesso al file system di Archivio Data Lake di Azure](#filepermissions).

    > [!IMPORTANT]
    > Solo il ruolo **Proprietario** può consentire automaticamente l'accesso al file system. I ruoli **Collaboratore**, **Lettore** e tutti gli altri richiedono elenchi di controllo di accesso per poter consentire qualsiasi livello di accesso a cartelle e file.  Il ruolo **Proprietario** concede autorizzazioni di utente con privilegi utente a file e cartelle che non possono essere sostituiti tramite elenchi di controllo di accesso. Per altre informazioni sul mapping dei criteri di controllo degli accessi in base al ruolo all'accesso ai dati, vedere [Controllo degli accessi in base al ruolo per la gestione degli account](data-lake-store-security-overview.md#rbac-for-account-management).

4. Se si vuole aggiungere un gruppo/utente non elencato nel pannello **Aggiungi autorizzazioni**, è possibile invitarlo digitandone l'indirizzo e-mail nella casella di testo **Seleziona** e quindi selezionandolo nell'elenco.
   
    ![Aggiungere un gruppo di sicurezza](./media/data-lake-store-secure-data/adl.add.user.2.png "Aggiungere un gruppo di sicurezza")
   
5. Fare clic su **Save**. Il gruppo di sicurezza dovrebbe essere ora aggiunto all'elenco, come illustrato di seguito.
   
    ![Gruppo di sicurezza aggiunto](./media/data-lake-store-secure-data/adl.add.user.3.png "Gruppo di sicurezza aggiunto")

6. L'utente/gruppo di sicurezza dispone ora dell'autorizzazione di accesso all'account di Archivio Data Lake di Azure. Se si desidera assegnare l'accesso a un utente specifico, è possibile aggiungerlo al gruppo di sicurezza. Analogamente, se si desidera revocare l'accesso per un utente, è possibile rimuoverlo dal gruppo di sicurezza. È possibile anche assegnare più gruppi di sicurezza a un account. 

## <a name="filepermissions"></a>Assegnare utenti o gruppi di sicurezza come elenchi di controllo di accesso al file system di Azure Data Lake Store
Con l'assegnazione di utenti/gruppi di sicurezza al file system di Azure Data Lake, si imposta il controllo di accesso sui dati presenti in Archivio Data Lake di Azure.

1. Nel pannello dell'account di Archivio Data Lake, fare clic su **Esplora dati**.
   
    ![Visualizzare i dati tramite Esplora dati](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Visualizzare i dati tramite Esplora dati")
2. Nel pannello **Esplora dati** fare clic sulla cartella per cui si vuole configurare l'elenco di controllo di accesso e quindi fare clic su **Accesso**. Per assegnare elenchi di controllo di accesso a un file, è necessario prima di tutto fare clic su **Accesso** dal pannello **Anteprima file**.
   
    ![Configurare elenchi di controllo di accesso nel file system di Data Lake](./media/data-lake-store-secure-data/adl.acl.1.png "Configurare elenchi di controllo di accesso nel file system di Data Lake")
3. Il pannello **Accesso** elenca i proprietari e le autorizzazioni già assegnati alla radice. Fare clic sull'icona **Aggiungi** per aggiungere altri ACL di accesso.
    > [!IMPORTANT]
    > L'impostazione di autorizzazioni di accesso per un singolo file non necessariamente concede a un utente/gruppo l'accesso al file. Il percorso del file deve essere accessibile all'utente o al gruppo assegnato. Per altre informazioni ed esempi, vedere [Scenari comuni correlati alle autorizzazioni](data-lake-store-access-control.md#common-scenarios-related-to-permissions).
   
    ![Elencare gli accessi standard e personalizzati](./media/data-lake-store-secure-data/adl.acl.2.png "Elencare gli accessi standard e personalizzati")
   
   * I ruoli **Proprietari** e **Everyone else** (Tutti gli altri) forniscono accesso in stile UNIX, in cui è possibile specificare la lettura, la scrittura e l'esecuzione (rwx) in tre classi di utente distinte: proprietario, gruppo e altri.
   * **Autorizzazioni assegnate** corrisponde agli elenchi di controllo di accesso POSIX, che permettono di impostare autorizzazioni per utenti o gruppi non anonimi specifici oltre al gruppo o al proprietario del file. 
     
     Per altre informazioni, vedere l'articolo sugli [elenchi di controllo di accesso HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Per altre informazioni sull'implementazione degli elenchi di controllo di accesso in Data Lake Store, vedere [Controllo di accesso in Data Lake Store](data-lake-store-access-control.md).
4. Fare clic sull'icona **Aggiungi** per aprire il pannello **Assegna autorizzazioni**. In questo pannello fare clic su **Seleziona utente o gruppo** e nel pannello **Seleziona utente o gruppo** cercare il gruppo di sicurezza creato in precedenza in Azure Active Directory. Se è presente un elevato numero di gruppi, usare la casella di testo nella parte superiore per filtrare in base al nome del gruppo. Fare clic sul gruppo che si desidera aggiungere e quindi su **Seleziona**.
   
    ![Aggiungere un gruppo](./media/data-lake-store-secure-data/adl.acl.3.png "Aggiungere un gruppo")
5. Fare clic su **Selezionare le autorizzazioni**, selezionare le autorizzazioni e specificare se si vuole assegnare le autorizzazioni come ACL di accesso, ACL predefinito o entrambi. Fare clic su **OK**.
   
    ![Assegnare autorizzazioni a un gruppo](./media/data-lake-store-secure-data/adl.acl.4.png "Assegnare autorizzazioni a un gruppo")
   
    Per altre informazioni sulle autorizzazioni in Data Lake Store e gli ACL predefiniti/di accesso, vedere [Controllo di accesso in Data Lake Store](data-lake-store-access-control.md).
6. Dopo aver fatto clic su **OK** nel pannello **Selezionare le autorizzazioni**, il nuovo gruppo aggiunto e le autorizzazioni associate sono ora elencati nel pannello **Accesso**.
   
    ![Assegnare autorizzazioni a un gruppo](./media/data-lake-store-secure-data/adl.acl.5.png "Assegnare autorizzazioni a un gruppo")
   
   > [!IMPORTANT]
   > Nella versione corrente l'impostazione **Autorizzazioni assegnate** può contenere fino a 28 voci. Per aggiungere più di 28 utenti, è necessario creare gruppi di sicurezza, aggiungere gli utenti ai gruppi di sicurezza e fornire ai gruppi di sicurezza creati l'accesso all'account Data Lake Store.
   > 
   > 
7. Se necessario, è possibile modificare le autorizzazioni di accesso anche dopo aver aggiunto il gruppo. Selezionare o deselezionare la casella di controllo per ogni tipo di autorizzazione (lettura, scrittura, esecuzione), in modo da aggiungerla o rimuoverla dal gruppo di sicurezza. Fare clic su **Salva** per salvare le modifiche o su **Ignora** per annullare le modifiche.

## <a name="set-ip-address-range-for-data-access"></a>Impostare l'intervallo di indirizzi IP per l'accesso ai dati
Archivio Azure Data Lake consente di bloccare ulteriormente l'accesso all'archivio dati a livello di rete. È possibile abilitare il firewall, specificare un indirizzo IP e definire un intervallo di indirizzi IP per i client attendibili. Dopo l'abilitazione, solo i client con indirizzo IP compreso nell'intervallo definito possono connettersi all'archivio.

![Impostazioni del firewall e accesso IP](./media/data-lake-store-secure-data/firewall-ip-access.png "Impostazioni del firewall e indirizzo IP")

## <a name="remove-security-groups-for-an-azure-data-lake-store-account"></a>Rimuovere gruppi di sicurezza da un account di Archivio Data Lake di Azure
Quando si rimuovono gruppi di sicurezza da un account di Archivio Data Lake di Azure, si modifica semplicemente l'accesso alle operazioni di gestione eseguite sull'account tramite il portale di Azure o le API di Gestione risorse di Azure.  

L'accesso ai dati è invariato e continua a essere gestito tramite gli ACL di accesso.  L'eccezione a questo comportamento sono gli utenti o i gruppi inclusi nel ruolo Proprietari.  Gli utenti o i gruppi rimossi dal ruolo Proprietari non sono più utenti con privilegi avanzati e il loro accesso dipende di nuovo dalle impostazioni degli ACL di accesso. 

1. Nel pannello dell'account Data Lake Store fare clic su **Controllo di accesso (IAM)**. 
   
    ![Assegnare un gruppo di sicurezza all'account Azure Data Lake](./media/data-lake-store-secure-data/adl.select.user.icon.png "Assegnare un gruppo di sicurezza all'account Azure Data Lake")
2. Nel pannello **Controllo di accesso (IAM)** fare clic su uno o più gruppi di sicurezza che si vuole rimuovere. Fare clic su **Rimuovi**.
   
    ![Gruppo di sicurezza rimosso](./media/data-lake-store-secure-data/adl.remove.group.png "Gruppo di sicurezza rimosso")

## <a name="remove-security-group-acls-from-azure-data-lake-store-file-system"></a>Rimuovere elenchi di controllo di accesso di gruppi di sicurezza dal file system di Archivio Data Lake di Azure
Quando si rimuovono elenchi di controllo di accesso dei gruppi di sicurezza dal file system di Azure Data Lake Store, si modifica l'accesso ai dati presenti in Data Lake Store.

1. Nel pannello dell'account di Archivio Data Lake, fare clic su **Esplora dati**.
   
    ![Creare directory nell'account Data Lake](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Creare directory nell'account Data Lake")
2. Nel pannello **Esplora dati** fare clic sulla cartella per cui si vuole rimuovere l'elenco di controllo di accesso e quindi fare clic su **Accesso**. Per rimuovere elenchi di controllo di accesso per un file, è necessario prima di tutto fare clic sul file per visualizzarne l'anteprima e quindi fare clic su **Accesso** dal pannello **Anteprima file**. 
   
    ![Configurare elenchi di controllo di accesso nel file system di Data Lake](./media/data-lake-store-secure-data/adl.acl.1.png "Configurare elenchi di controllo di accesso nel file system di Data Lake")
3. Nel pannello **Accesso** fare clic sul gruppo di sicurezza che si vuole rimuovere. Nel pannello **Dettagli accesso** fare clic su **Rimuovi**.
   
    ![Assegnare autorizzazioni a un gruppo](./media/data-lake-store-secure-data/adl.remove.acl.png "Assegnare autorizzazioni a un gruppo")

## <a name="see-also"></a>Vedere anche 
* [Panoramica di Archivio Data Lake di Azure](data-lake-store-overview.md)
* [Copiare i dati da BLOB di archiviazione di Azure ad Archivio Data Lake](data-lake-store-copy-data-azure-storage-blob.md)
* [Usare Azure Data Lake Analytics con Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usare Azure HDInsight con Archivio Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Introduzione ad Archivio Data Lake mediante PowerShell](data-lake-store-get-started-powershell.md)
* [Introduzione ad Archivio Data Lake mediante .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Accesso ai log di diagnostica per Azure Data Lake Store](data-lake-store-diagnostic-logs.md)

