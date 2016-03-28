<properties 
   pageTitle="Protezione dei dati presenti in Archivio Data Lake di Azure | Azure" 
   description="Informazioni su come proteggere i dati presenti in Archivio Data Lake di Azure usando gruppi ed elenchi di controllo di accesso" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="03/15/2016"
   ms.author="nitinme"/>

# Protezione dei dati presenti in Archivio Data Lake di Azure

La protezione dei dati presenti in Archivio Data Lake di Azure prevede un approccio suddiviso in tre fasi.

1. Creazione di gruppi di sicurezza in Azure Active Directory, che consentono di implementare il controllo degli accessi in base al ruolo (RBAC) nel portale di Azure. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo in Microsoft Azure](../active-directory/role-based-access-control-configure.md).

2. Assegnazione dei gruppi di sicurezza AAD all'account di Archivio Data Lake di Azure. In questo modo è possibile controllare l'accesso all'account di Archivio Data Lake dal portale e le operazioni di gestione dal portale o dalle API.

3. Assegnazione dei gruppi di sicurezza AAD come elenchi di controllo di accesso al file system di Archivio Data Lake.

Questo articolo fornisce istruzioni sull'uso del portale di Azure per eseguire le attività appena descritte.

## Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Un account di Archivio Data Lake di Azure**. Per istruzioni su come crearne uno, vedere [Introduzione ad Archivio Data Lake di Azure](data-lake-store-get-started-portal.md)

## Creare gruppi di sicurezza in Azure Active Directory

Per istruzioni su come creare gruppi di sicurezza AAD e come aggiungere utenti ai gruppi, vedere [Gestione dei gruppi di sicurezza in Azure Active Directory](../active-directory/active-directory-accessmanagement-manage-groups.md).

## Assegnare utenti o gruppi di sicurezza ad account di Archivio Data Lake di Azure

Quando si assegnano utenti o gruppi di sicurezza ad account di Archivio Data Lake di Azure, è possibile controllare l'accesso alle operazioni di gestione eseguite sull'account tramite il portale di Azure o le API di Gestione risorse di Azure.

1. Aprire un account di Archivio Data Lake di Azure. Nel riquadro sinistro fare clic su **Sfoglia** e su **Archivio Data Lake** e quindi, nel pannello di Archivio Data Lake, fare clic sul nome dell'account a cui si desidera assegnare un utente o un gruppo di sicurezza.

2. Nel pannello dell'account di Archivio Data Lake fare clic sull'icona dell'utente.

	![Assegnare un gruppo di sicurezza a un account di Archivio Data Lake di Azure](./media/data-lake-store-secure-data/adl.select.user.icon.png "Assegnare un gruppo di sicurezza a un account di Archivio Data Lake di Azure")

3. Per impostazione predefinita, il pannello **Utente** elenca il gruppo **Amministratori della sottoscrizione** come proprietario.

	![Aggiungere utenti e ruoli](./media/data-lake-store-secure-data/adl.add.group.roles.png "Aggiungere utenti e ruoli")
 
	Esistono due modi per aggiungere un gruppo e assegnare i ruoli appropriati.

	* Aggiungere un utente/gruppo all'account e assegnare ad esso un ruolo, oppure
	* Aggiungere un ruolo ed assegnare ad esso utenti/gruppi.

	In questa sezione si prenderà in esame il primo metodo, che prevede l'aggiunta di un gruppo e la conseguente assegnazione dei ruoli. È possibile eseguire una procedura simile anche per il secondo metodo, in cui si seleziona prima un ruolo e quindi si assegnano i gruppi.
	
4. Nel pannello **Utenti** fare clic su **Aggiungi** per aprire il pannello **Aggiungi accesso**. Nel pannello **Aggiungi accesso** fare clic su **Selezionare un ruolo** e scegliere quindi un ruolo per l'utente o il gruppo.

	 ![Aggiungere un ruolo per l'utente](./media/data-lake-store-secure-data/adl.add.user.1.png "Aggiungere un ruolo per l'utente")

	I ruoli **Proprietario** e **Collaboratore** offrono l'accesso a un'ampia gamma di funzioni di amministrazione sull'account di Data Lake. Gli utenti che interagiranno con i dati presenti in Data Lake potranno essere aggiunti al ruolo **Lettore**. L'ambito di questi ruoli è limitato alle operazioni di gestione correlate all'account di Archivio Data Lake di Azure.

	Per le operazioni sui dati, invece, l'ambito d'azione degli utenti viene definito dalle singole autorizzazioni a livello di file system. Pertanto, un utente con il ruolo Lettore può visualizzare solo le impostazioni amministrative associate all'account, ma potrebbe anche leggere e scrivere dati, in base alle autorizzazioni per il file system che gli sono state assegnate. Le autorizzazioni per il file system di Archivio Data Lake sono descritte nella sezione [Assegnare utenti o gruppi di sicurezza come elenchi di controllo di accesso al file system di Archivio Data Lake di Azure.](#filepermissions).



5. Nel pannello **Aggiungi accesso** fare clic su **Aggiungi utenti** per aprire il pannello **Aggiungi utenti**. In questo pannello, cercare il gruppo di sicurezza precedentemente creato in Azure Active Directory. Se è presente un elevato numero di gruppi, usare la casella di testo nella parte superiore per filtrare in base al nome del gruppo. Fare clic su **Seleziona**.

	![Aggiungere un gruppo di sicurezza](./media/data-lake-store-secure-data/adl.add.user.2.png "Aggiungere un gruppo di sicurezza")

	Se si desidera aggiungere un gruppo/utente non elencato, è possibile invitarlo selezionando l'icona **Invita** e specificando l'indirizzo di posta elettronica dell'utente/gruppo.

6. Fare clic su **OK**. Il gruppo di sicurezza dovrebbe essere ora aggiunto all'elenco, come illustrato di seguito.

	![Gruppo di sicurezza aggiunto](./media/data-lake-store-secure-data/adl.add.user.3.png "Gruppo di sicurezza aggiunto")

7. L'utente/gruppo di sicurezza dispone ora dell'autorizzazione di accesso all'account di Archivio Data Lake di Azure. Se si desidera assegnare l'accesso a un utente specifico, è possibile aggiungerlo al gruppo di sicurezza. Analogamente, se si desidera revocare l'accesso per un utente, è possibile rimuoverlo dal gruppo di sicurezza. È possibile anche assegnare più gruppi di sicurezza a un account.

## <a name="filepermissions"></a>Assegnare utenti o gruppi di sicurezza come elenchi di controllo di accesso al file system di Archivio Data Lake di Azure

Con l'assegnazione di utenti/gruppi di sicurezza al file system di Azure Data Lake, si imposta il controllo di accesso sui dati presenti in Archivio Data Lake di Azure. Nella versione corrente, è possibile impostare gli elenchi di controllo di accesso solo nel nodo radice del file system.

1. Nel pannello dell'account di Archivio Data Lake, fare clic su **Esplora dati**.

	![Creare directory nell'account di Archivio Data Lake](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Creare directory nell'account di Data Lake")

2. Nel pannello **Esplora dati** fare clic sulla cartella principale dell'account e, nel pannello dell'account, fare clic sull'icona **Accedi**.

	![Impostare elenchi di controllo accesso nel file system di Data Lake](./media/data-lake-store-secure-data/adl.acl.1.png "Impostare elenchi di controllo accesso nel file system di Data Lake")

3. Il pannello di **accesso** elenca i profili di accesso standard e personalizzato già assegnati all'elemento radice. Fare clic sull'icona **Aggiungi** per aggiungere elenchi di controllo per l'accesso personalizzato.

	![Elencare profili di accesso standard e personalizzato](./media/data-lake-store-secure-data/adl.acl.2.png "Elencare profili di accesso standard e personalizzato")

	* L'accesso standard è un tipo di accesso in stile UNIX, in cui si specificano i permessi di lettura, scrittura ed esecuzione per tre diverse classi utente: proprietario, gruppo e altri.
	* L'accesso personalizzato corrisponde invece agli elenchi di controllo di accesso POSIX, che consentono di impostare autorizzazioni per utenti non anonimi o gruppi specifici, e non solo il gruppo o il proprietario del file.
	
	Per altre informazioni, vedere l'articolo sugli [elenchi di controllo di accesso HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists).

4. Fare clic sull'icona **Aggiungi** per aprire il pannello **Aggiungi accesso personalizzato**. In questo pannello fare clic su **Seleziona utente o gruppo** e, nel pannello **Seleziona utente o gruppo**, cercare il gruppo di sicurezza precedentemente creato in Azure Active Directory. Se è presente un elevato numero di gruppi, usare la casella di testo nella parte superiore per filtrare in base al nome del gruppo. Fare clic sul gruppo che si desidera aggiungere e quindi su **Seleziona**.

	![Aggiungere un gruppo](./media/data-lake-store-secure-data/adl.acl.3.png "Aggiungere un gruppo")

5. Fare clic su **Selezionare le autorizzazioni**, selezionare le autorizzazioni che si desidera assegnare al gruppo e quindi fare clic su **OK**.

	![Assegnare autorizzazioni a un gruppo](./media/data-lake-store-secure-data/adl.acl.4.png "Assegnare autorizzazioni a un gruppo")


	>[AZURE.NOTE] L'autorizzazione di esecuzione è sempre necessaria per l'enumerazione delle directory e, spesso, anche per concedere a un utente o un gruppo l'accesso in sola lettura ai dati.


6. Nel pannello **Aggiungi accesso personalizzato** fare clic su **OK**. Il gruppo appena aggiunto, con le autorizzazioni associate, risulterà ora elencato nel pannello di **accesso**.

	![Assegnare autorizzazioni a un gruppo](./media/data-lake-store-secure-data/adl.acl.5.png "Assegnare autorizzazioni a un gruppo")

	> [AZURE.IMPORTANT] Nella versione corrente, l'elenco **Accesso personalizzato** non può contenere più di 9 voci. Per aggiungere più di 9 utenti, è necessario creare gruppi di sicurezza, aggiungere utenti a tali gruppi e fornire ai gruppi di sicurezza creati accesso all'account di Archivio Data Lake.

7. Se necessario, è possibile modificare le autorizzazioni di accesso anche dopo aver aggiunto il gruppo. Selezionare o deselezionare la casella di controllo per ogni tipo di autorizzazione (lettura, scrittura, esecuzione), in modo da aggiungerla o rimuoverla dal gruppo di sicurezza. Fare clic su **Salva** per salvare le modifiche o su **Ignora** per annullare le modifiche.

## Rimuovere gruppi di sicurezza da un account di Archivio Data Lake di Azure

Quando si rimuovono gruppi di sicurezza da un account di Archivio Data Lake di Azure, si modifica semplicemente l'accesso alle operazioni di gestione eseguite sull'account tramite il portale di Azure o le API di Gestione risorse di Azure.

1. Nel pannello dell'account di Archivio Data Lake fare clic sull'icona dell'utente.

	![Assegnare un gruppo di sicurezza a un account di Data Lake di Azure](./media/data-lake-store-secure-data/adl.select.user.icon.png "Assegnare un gruppo di sicurezza a un account di Data Lake di Azure")

2. Nel pannello **Utenti** fare clic sul gruppo di sicurezza da rimuovere.

	![Gruppo di sicurezza da rimuovere](./media/data-lake-store-secure-data/adl.add.user.3.png "Gruppo di sicurezza da rimuovere")

3. Nel pannello relativo al gruppo di sicurezza fare clic su **Rimuovi**.

	![Gruppo di sicurezza rimosso](./media/data-lake-store-secure-data/adl.remove.group.png "Gruppo di sicurezza rimosso")

## Rimuovere elenchi di controllo di accesso di gruppi di sicurezza dal file system di Archivio Data Lake di Azure

Quando si rimuovono gli elenchi di controllo di accesso dei gruppi di sicurezza dal file system di Archivio Data Lake di Azure, si modifica l'accesso ai dati presenti in Archivio Data Lake.

1. Nel pannello dell'account di Archivio Data Lake, fare clic su **Esplora dati**.

	![Creare directory nell'account di Data Lake](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Creare directory nell'account di Data Lake")

2. Nel pannello **Esplora dati** fare clic sulla cartella principale dell'account e, nel pannello dell'account, fare clic sull'icona **Accedi**.

	![Impostare elenchi di controllo accesso nel file system di Data Lake](./media/data-lake-store-secure-data/adl.acl.1.png "Impostare elenchi di controllo accesso nel file system di Data Lake")

3. Nel pannello di **accesso**, all'interno della sezione **Accesso personalizzato**, fare clic sul gruppo di sicurezza da rimuovere. Nel pannello **Accesso personalizzato** fare clic su **Rimuovi** e quindi su **OK**.

	![Assegnare autorizzazioni a un gruppo](./media/data-lake-store-secure-data/adl.remove.acl.png "Assegnare autorizzazioni a un gruppo")


## Vedere anche

- [Panoramica di Archivio Data Lake di Azure](data-lake-store-overview.md)
- [Copiare i dati da BLOB di archiviazione di Azure ad Archivio Data Lake](data-lake-store-copy-data-azure-storage-blob.md)
- [Usare Azure Data Lake Analytics con Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usare Azure HDInsight con Archivio Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Introduzione ad Archivio Data Lake mediante PowerShell](data-lake-store-get-started-powershell.md)
- [Introduzione ad Archivio Data Lake mediante .NET SDK](data-lake-store-get-started-net-sdk.md)

<!---HONumber=AcomDC_0316_2016-->