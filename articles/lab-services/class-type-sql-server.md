---
title: Configurare un Lab per la gestione e lo sviluppo con il database SQL di Azure | Azure Lab Services
description: Informazioni su come configurare un Lab per la gestione e lo sviluppo con il database SQL di Azure.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 9fc0a965869207ba8d1b4eb6f45e878ae4b93c3a
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88079023"
---
# <a name="set-up-a-lab-to-manage-and-develop-with-sql-server"></a>Configurare un Lab per la gestione e lo sviluppo con SQL Server

Questo articolo descrive come configurare un Lab per una classe di sviluppo e gestione SQL Server di base in Azure Lab Services.  I concetti relativi ai database sono uno dei corsi introduttivi che hanno insegnato nella maggior parte dei reparti di informatica dell'Università. Structured Query Language (SQL) è uno standard internazionale.  SQL è il linguaggio standard per la gestione dei database relazionali, tra cui l'aggiunta, l'accesso e la gestione del contenuto in un database.  È più indicato per l'elaborazione rapida, l'affidabilità comprovata, la facilità e la flessibilità di utilizzo.

Questo articolo illustra come configurare un modello di macchina virtuale in un Lab con [Visual Studio 2019](https://visualstudio.microsoft.com/vs/), [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)e [Azure Data Studio](https://github.com/microsoft/azuredatastudio).  Per questo Lab verrà usato un [database di SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) condiviso per l'intero Lab. Il [database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) è una piattaforma distribuita come servizio (PaaS) motore di database offerta da Azure.

## <a name="lab-configuration"></a>Configurazione del lab

Per configurare questo Lab, è necessario disporre di una sottoscrizione di Azure e di un account Lab per iniziare. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. Una volta ottenuto una sottoscrizione di Azure, è possibile creare un nuovo account Lab in Azure Lab Services. Per ulteriori informazioni sulla creazione di un nuovo account Lab, vedere [esercitazione per la configurazione di un account Lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account). È anche possibile usare un account lab esistente.

### <a name="lab-account-settings"></a>Impostazioni dell'account Lab

Abilitare le impostazioni descritte nella tabella seguente per l'account Lab. Per altre informazioni su come abilitare le immagini del Marketplace, vedere [specificare le immagini del Marketplace disponibili per gli autori del Lab](specify-marketplace-images.md).

| Impostazione dell'account lab | Istruzioni |
| ------------------- | ------------ |
| Immagine del Marketplace | Abilitare la "Visual Studio 2019 community (versione più recente) nell'immagine di Windows 10 Enterprise N (x64)" per l'uso nell'account Lab. |

### <a name="shared-resource-configuration"></a>Configurazione di risorse condivise

Per usare una risorsa condivisa in Lab Services, è necessario innanzitutto creare la rete virtuale e le risorse.  Per creare la rete virtuale e connetterla al Lab, seguire la [procedura per creare un Lab con una risorsa condivisa in Azure Lab Services](how-to-create-a-lab-with-shared-resource.md).  Tenere presente che tutte le risorse esterne ai servizi Lab verranno fatturate separatamente e non verranno incluse nelle stime dei costi del Lab.

>[!WARNING]
>Le risorse condivise per un lab devono essere impostate prima della creazione del Lab.  Se il VNET non è associato all' [account Lab](how-to-connect-peer-virtual-network.md) *prima* della creazione del Lab, il Lab non avrà accesso alla risorsa condivisa.

Ora che il lato rete di elementi è gestito, consente di creare un database SQL Server.  Verrà creato un [database singolo](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal) poiché è l'opzione di distribuzione più rapida per il database SQL di Azure.  Per altre opzioni di distribuzione, creare un [pool elastico](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool#creating-a-new-sql-database-elastic-pool-using-the-azure-portal), un' [istanza gestita](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)o una [macchina virtuale SQL](https://docs.microsoft.com/azure/virtual-machines/windows/sql/quickstart-sql-vm-create-portal).

1. Dal menu portale di Azure scegliere **Crea nuova risorsa**.
2. Scegliere **database SQL** e fare clic sul pulsante **Crea** .
3. Nella scheda **nozioni di base** del modulo **Crea database SQL** Selezionare il gruppo di risorse per il database.  Si userà *SQLDB-RG*.
4. Per **nome database**immettere *classlabdb*.
5. In impostazioni **Server** fare clic su **Crea nuovo** per creare un nuovo server in cui si desidera mantenere il database.
6. Nel nuovo riquadro a comparsa **Server** immettere il nome del server.  Si userà *classlabdbserver*.  Il nome del server deve essere univoco a livello globale.
7. Immettere *azureuser* per l' **account di accesso amministratore server**.
8. Immettere una password memorabile.  La password deve avere una lunghezza di almeno otto caratteri e contenere caratteri speciali.
9. Scegliere Region (area) per la **località**.  Se possibile, immettere la stessa località dell'account Lab e di VNET con peering per ridurre al minimo la latenza.
10. Fare clic su **OK** per tornare al modulo **Crea database SQL** .
11. Fare clic su **Configura collegamento database** nell'impostazione **calcolo e archiviazione** .
12. Modificare le impostazioni del database in base alle esigenze della classe.  È possibile scegliere tra le opzioni di provisioning e senza server.  Per questo esempio, si userà l'opzione senza server con scalabilità automatica con numero massimo di vcore di 4, Vcore min di 1. Si manterrà l'impostazione di sospensione di autopause almeno un'ora. Fare clic su **Applica**.
13. Fare clic su **Avanti: pulsante rete** .
14. Nella scheda rete scegliere endpoint privato per il metodo di **connettività**.
15. Nella sezione **endpoint privati** fare clic su **Aggiungi endpoint privato**.
16. Nella finestra a comparsa **Crea endpoint privato** scegliere lo stesso gruppo di risorse della rete virtuale con peering per l'account Lab.
17. Per **località**, scegliere lo stesso percorso della rete virtuale.
18. Per **nome**immettere *labsql-endpt*.
19. Lasciare la sottorisorsa di destinazione impostata su SqlServer.
20. Per **rete virtuale**scegliere la stessa rete virtuale con peering per l'account Lab.
21. Per **subnet**scegliere la subnet in cui si desidera che sia ospitato l'endpoint.  L'IP assegnato all'endpoint sarà compreso nell'intervallo assegnato a tale subnet.
22. Impostare **integra con DNS privato** su **No**. Per semplicità, verrà usato il DNS di Azure tramite la zona DNS privata o i server DNS.
23. Fare clic su **OK**.
24. Fare clic su **Avanti: Impostazioni aggiuntive**.
25. Per l'impostazione **Usa dati esistenti** , scegliere **esempio**.  I dati del database AdventureWorksLT verranno utilizzati durante la creazione del database.
26. Fare clic su **Rivedi e crea**.
27. Fare clic su **Crea**.

Una volta completata la distribuzione del database SQL, è possibile creare il Lab e installare il software nel computer modello Lab.

### <a name="lab-settings"></a>Impostazioni del lab

Usare le impostazioni nella tabella seguente quando si configura un Lab della classe. Per altre informazioni su come creare un Lab per le aule, vedere [l'esercitazione sulla configurazione di una classe Lab](tutorial-setup-classroom-lab.md).

| Impostazioni del lab | Valore/istruzioni |
| ------------ | ------------------ |
| Dimensioni della macchina virtuale | Media. Queste dimensioni sono più indicate per database relazionali, memorizzazione nella cache in memoria e analisi. |
| Immagine di macchina virtuale | Community di Visual Studio 2019 (versione più recente) in Windows 10 Enterprise N (x64) |

Ora che il Lab è stato creato, modificare il computer modello con il software necessario.

## <a name="visual-studio"></a>Visual Studio

L'immagine scelta sopra include [Visual Studio 2019 community](https://visualstudio.microsoft.com/vs/community/).  Tutti i carichi di lavoro e i set di strumenti sono già installati nell'immagine.  Usare il Programma di installazione di Visual Studio per [installare gli strumenti facoltativi](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019) eventualmente desiderati.  [Accedere a Visual Studio](https://docs.microsoft.com/visualstudio/ide/signing-in-to-visual-studio?view=vs-2019#how-to-sign-in-to-visual-studio) per sbloccare la Community Edition.

Visual Studio include il set di strumenti di **elaborazione e archiviazione dati** , che include SQL Server Data Tools (SSDT).  Per ulteriori informazioni sulle funzionalità di SSDT, vedere [Panoramica di SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/sql-server-data-tools?view=sql-server-ver15).  Per verificare che la connessione al SQL Server condiviso per la classe venga completata correttamente, vedere [connettersi a un database e visualizzare gli oggetti esistenti](https://docs.microsoft.com/sql/ssdt/how-to-connect-to-a-database-and-browse-existing-objects?view=sql-server-ver15). Se richiesto, aggiungere l'indirizzo IP del computer modello all' [elenco dei computer consentiti](https://docs.microsoft.com/azure/azure-sql/database/firewall-configure) in grado di connettersi all'istanza di SQL Server.

Visual Studio supporta diversi carichi di lavoro, inclusi i carichi di lavoro **Web & cloud** e **Desktop & per dispositivi mobili** .  Entrambi i carichi di lavoro supportano SQL Server come origine dati. Per altre informazioni sull'uso di ASP.NET Core per SQL Server, vedere [creare un'app del ASP.NET Core e del database SQL nel servizio app Azure](https://docs.microsoft.com/azure/app-service/tutorial-dotnetcore-sqldb-app) .  Usare la libreria [System. Data. SqlClient](https://docs.microsoft.com/dotnet/api/system.data.sqlclient) per connettersi a un database SQL da un'app [Novell](https://docs.microsoft.com/xamarin) .

## <a name="install-azure-data-studio"></a>Installare Azure Data Studio

[Azure Data Studio](https://github.com/microsoft/azuredatastudio) è un ambiente desktop multipiattaforma e multipiattaforma per professionisti dei dati che usano la famiglia di piattaforme dati locali e cloud in Windows, MacOS e Linux.

1. Scaricare il [programma di installazione di Azure Data Studio *System* per Windows](https://go.microsoft.com/fwlink/?linkid=2127432). Per trovare i programmi di installazione per altri sistemi operativi supportati, visitare la pagina di download [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download) .
2. Nella pagina **contratto di licenza** selezionare Accetto **il contratto**. Fare clic su **Avanti**.
3. Nella pagina **Select Destination Location** (Selezionare il percorso di destinazione) fare clic su **Next** (Avanti).
4. Nella pagina **Select Start Menu Folder** (Selezionare la cartella del menu Start) fare clic su **Next** (Avanti).
5. Nella pagina **Seleziona attività aggiuntive selezionare** **Crea un'icona del desktop** se si desidera un'icona del desktop.  Fare clic su **Avanti**.
6. In **pronto per l'installazione**fare clic su **Avanti**.
7. Attendere l'esecuzione del programma di installazione.  Fare clic su **Fine**.

Ora che Azure Data Studio è stato installato, è necessario configurare la connessione al database SQL di Azure.

1. Nella pagina di **benvenuto** per Azure Data Studio fare clic sul collegamento **nuova connessione** .
2. Nella casella **Dettagli connessione** immettere le informazioni necessarie.
    - Imposta il **Server** su *classlabdbserver.database.Windows.NET*
    - Imposta il nome **utente** su *azureuser*
    - Impostare la **password** su password utilizzata per creare il database.
    - Controllare **memorizza password**.
    - Per **database**, selezionare *classlabdb*.
3. Fare clic su **Connetti**.

## <a name="install-sql-server-management-studio"></a>Installare SQL Server Management Studio

[SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15) è un ambiente integrato per la gestione di qualsiasi infrastruttura SQL.  SSMS è uno strumento utilizzato dagli amministratori di database per la distribuzione, il monitoraggio e l'aggiornamento dell'infrastruttura di dati.

1. [Scaricare SQL Server Management Studio](https://aka.ms/ssmsfullsetup). Una volta scaricato, avviare il programma di installazione.
2. Nella pagina di **benvenuto** fare clic su **Installa**.
3. Nella pagina **installazione completata** fare clic su **Chiudi**.
4. Avviare SQL Server Management Studio.  
5. Nella pagina **processo di configurazione delle dipendenze** fare clic su **Chiudi**.

Non è installato SSMS, è possibile [connettersi ed eseguire query su un SQL Server](https://docs.microsoft.com/sql/ssms/tutorials/connect-query-sql-server). Quando si configura la connessione, usare i valori seguenti:

- Tipo di server: motore di database
- Nome server: *classlabdbserver.database.Windows.NET*
- Autenticazione: autenticazione SQL Server
- Account di accesso: *azureuser*
- Password: password usata per creare il database.

## <a name="cost-estimate"></a>Stima dei costi

Verrà ora trattata una possibile stima dei costi per questa classe. La stima non include il costo di esecuzione del SQL Server.  Per informazioni aggiornate sui prezzi dei database, vedere [prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database) .

Verrà usata una classe di 25 studenti. Sono previste 20 ore di tempo della classe pianificata. Ogni studente riceve inoltre una quota di 10 ore per il lavoro o assegnazioni al di fuori dell'orario di classe pianificato. Le dimensioni della macchina virtuale scelte sono medie, ovvero 42 unità Lab.

Di seguito è riportato un esempio di una possibile stima dei costi per questa classe:

25 studenti \* (20 ore pianificate \+ 10 ore di quota) * 0,42 USD all'ora = 315,00 USD

>[!IMPORTANT]
>La stima dei costi è solo a scopo esemplificativo. Per informazioni aggiornate sui prezzi, vedere [Azure Lab Services prezzi](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Passaggi successivi

I passaggi successivi sono comuni per la configurazione di qualsiasi Lab.

- [Creare, gestire e pubblicare un modello](how-to-create-manage-template.md)
- [Aggiungere utenti](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Impostare la quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Impostare una pianificazione](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Collegamenti per la registrazione tramite posta elettronica agli studenti](how-to-configure-student-usage.md#send-invitations-to-users)
