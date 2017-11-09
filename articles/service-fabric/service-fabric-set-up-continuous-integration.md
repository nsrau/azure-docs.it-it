---
title: Impostare l'integrazione continua per i microservizi di Azure | Microsoft Docs
description: Panoramica di come configurare l'integrazione e la distribuzione continue per un'applicazione di Service Fabric tramite Visual Studio Team Services (VSTS).
services: service-fabric
documentationcenter: na
author: mthalman-msft
manager: timlt
editor: 
ms.assetid: 3e8c2290-9e7a-456a-9b2c-db44d1b3988d
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2016
ms.author: mthalman;mikhegn
ms.openlocfilehash: 76a1e013e824910c7a489e345b6563ae3951378f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-service-fabric-continuous-integration-and-deployment-with-visual-studio-team-services"></a>Configurare l'integrazione e la distribuzione continue di Service Fabric con Visual Studio Team Services
Questo articolo descrive la procedura per configurare l'integrazione e la distribuzione continue per un'applicazione di Azure Service Fabric tramite Visual Studio Team Services (VSTS).

Questo documento descrive la procedura corrente e può essere modificato nel tempo.

## <a name="prerequisites"></a>Prerequisiti
Attenersi alla procedura seguente per iniziare:

1. Assicurarsi di avere accesso a un account di Team Services o [crearlo](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services) manualmente.
2. Assicurarsi di avere accesso a un progetto team di Team Services o [crearlo](https://www.visualstudio.com/docs/setup-admin/create-team-project) manualmente.
3. Assicurarsi di avere un cluster di Service Fabric in cui distribuire l'applicazione o crearne uno usando il [portale di Azure](service-fabric-cluster-creation-via-portal.md), un [modello di Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) o [Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).
4. Verificare di aver già creato un progetto di applicazione di Service Fabric (.sfproj). È necessario che il progetto sia stato creato o aggiornato con Service Fabric SDK 2.1 o versione successiva; il valore di ProjectVersion del file con estensione SFPROJ deve essere 1.1 o versione successiva.

> [!NOTE]
> Non sono più necessari agenti di compilazione personalizzati. Gli agenti ospitati di Team Services sono ora preinstallati con il software di gestione dei cluster di Service Fabric, il che consente la distribuzione delle applicazioni direttamente dagli agenti.
> 
> 

## <a name="configure-and-share-your-source-files"></a>Configurare e condividere i file di origine
Prima di tutto è necessario preparare un profilo di pubblicazione destinato all'uso da parte del processo di distribuzione eseguito in Team Services.  Il profilo di pubblicazione deve essere configurato impostando il cluster preparato in precedenza come destinazione:

1. Scegliere un profilo di pubblicazione all'interno del progetto di applicazione che si vuole usare per il flusso di lavoro di integrazione continua. Seguire le [istruzioni di pubblicazione](service-fabric-publish-app-remote-cluster.md) su come pubblicare un'applicazione in un cluster remoto. In realtà non è necessario pubblicare l'applicazione. È possibile fare clic sul collegamento ipertestuale **Save** (Salva) nella finestra di dialogo di pubblicazione dopo aver configurato le opzioni in modo appropriato.
2. Se si vuole aggiornare l'applicazione per ogni distribuzione eseguita in Team Services, è necessario configurare il profilo di pubblicazione per consentire l'aggiornamento. Nella stessa finestra di dialogo di pubblicazione del passaggio 1 assicurarsi che la casella di controllo **Upgrade the Application** (Aggiorna l'applicazione) sia selezionata.  Sono disponibili maggiori informazioni sulla [configurazione delle impostazioni di aggiornamento aggiuntive](service-fabric-visualstudio-configure-upgrade.md). Fare clic sul collegamento ipertestuale **Save** (Salva) per salvare le impostazioni per il profilo di pubblicazione.
3. Assicurarsi di aver salvato le modifiche nel profilo di pubblicazione e annullare la finestra di dialogo di pubblicazione.
4. Ora è il momento di [condividere i file di origine del progetto di applicazione](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services#vs) con Team Services. Quando i file di origine sono accessibili in Team Services, è possibile procedere al passaggio successivo della generazione di compilazioni. 

## <a name="create-a-build-definition"></a>Creare una definizione di compilazione
Una definizione di compilazione di Team Services descrive un flusso di lavoro costituito da un set di istruzioni di compilazione che vengono eseguite in sequenza. L'obiettivo della definizione di compilazione che viene creata è di produrre un pacchetto dell'applicazione di Service Fabric e altri elementi, che possono essere usati per distribuire l'applicazione. Sono disponibili maggiori informazioni sulle [definizioni di compilazione](https://www.visualstudio.com/docs/build/define/create)di Team Services.

### <a name="create-a-definition-from-the-build-template"></a>Creare una definizione dal modello di build
1. Aprire il progetto team in Visual Studio Team Services.
2. Selezionare la scheda **Build** (Compilazione).
3. Scegliere il segno **+** verde per creare una nuova definizione di compilazione.
4. Nella finestra di dialogo visualizzata selezionare **Applicazione di Azure Service Fabric** all'interno della categoria del modello **Build**.
5. Selezionare **Avanti**.
6. Selezionare il repository e il ramo associato all'applicazione di Service Fabric.
7. Selezionare la coda dell'agente che si desidera usare. Sono supportati agenti ospitati.
8. Selezionare **Create**.
9. Salvare la definizione di compilazione e assegnare un nome.
10. Di seguito viene fornita una descrizione delle istruzioni di compilazione generate dal modello:

| Istruzione di compilazione | Descrizione |
| --- | --- |
| NuGet restore |Consente di ripristinare i pacchetti NuGet per la soluzione. |
| Compila la soluzione \*.sln |Consente di compilare l'intera soluzione. |
| Compila la soluzione \*.sfproj |Consente di generare il pacchetto dell'applicazione di Service Fabric usato per distribuire l'applicazione. Il percorso del pacchetto dell'applicazione viene specificato all'interno della directory degli elementi della compilazione. |
| Update Service Fabric App Versions |Consente di aggiornare i valori di versione contenuti nei file manifesto del pacchetto applicazione per consentire il supporto dell'aggiornamento. Per altre informazioni, vedere la [pagina della documentazione delle attività](https://go.microsoft.com/fwlink/?LinkId=820529) . |
| Copiare i file |Consente di copiare i file di parametri del profilo di pubblicazione e dell'applicazione negli elementi della compilazione per poter essere usati per la distribuzione. |
| Publish Artifact |Consente di pubblicare gli elementi della compilazione. Consente l'uso degli elementi della compilazione da parte di una definizione di versione. |

### <a name="verify-the-default-set-of-tasks"></a>Verificare il set predefinito di attività
1. Verificare il campo di input **Soluzione** per le istruzioni di compilazione **NuGet restore** e **Compila la soluzione**.  Per impostazione predefinita, queste istruzioni vengono eseguite su tutti i file di soluzione contenuti nel repository associato.  Se si desidera che la definizione di compilazione operi solo su uno di questi file di soluzione, aggiornare in modo esplicito il percorso a tale file.
2. Verificare il campo di input **Soluzione** per l'istruzione di compilazione **Package application** (Crea pacchetti applicazione).  Per impostazione predefinita, questa istruzione di compilazione presuppone che nel repository sia presente solo un progetto di applicazione di Service Fabric (.sfproj).  Se nel repository sono presenti più file di questo tipo e si desidera usare solo uno di essi come destinazione per questa definizione di compilazione, è necessario aggiornare in modo esplicito il percorso a tale file.  Se si vuole creare un pacchetto di più progetti di applicazione nel repository, è necessario creare altre istruzioni di **Compilazione Visual Studio** nella definizione di compilazione, ognuna con un progetto di applicazione di destinazione.  Sarà anche necessario aggiornare il campo **Argomenti MSBuild** per ognuna di queste istruzioni di compilazione in modo che il percorso del pacchetto sia univoco per ciascuna di esse.
3. Verificare il comportamento di controllo delle versioni definito nell'istruzione di compilazione **Update Service Fabric App Versions** .  Per impostazione predefinita, questa istruzione aggiunge il numero di compilazione a tutti i valori di versione nei file manifesto del pacchetto applicazione. Per altre informazioni, vedere la [pagina della documentazione delle attività](https://go.microsoft.com/fwlink/?LinkId=820529) . Ciò è utile per supportare l'aggiornamento dell'applicazione in quanto ogni distribuzione dell'aggiornamento richiede valori di versione diversi rispetto alla distribuzione precedente. Se non si intende usare l'aggiornamento dell'applicazione nel flusso di lavoro, è possibile disabilitare questa istruzione di compilazione. Deve essere disabilitata se l'intenzione è quella di produrre una compilazione che può essere usata per sovrascrivere un'applicazione di Service Fabric esistente. La distribuzione non riesce se la versione dell'applicazione prodotta dalla compilazione non corrisponde alla versione dell'applicazione nel cluster.
4. Se la soluzione contiene un progetto .NET Core, è necessario assicurarsi che la definizione di compilazione contenga un'istruzione per il ripristino delle dipendenze:
   1. Selezionare **Aggiungi istruzione di compilazione...**.
   2. Individuare l'attività **Riga di comando** all'interno della scheda Utilità e fare clic sul pulsante Aggiungi.
   3. Per i campi di input dell'attività, usare i valori seguenti:
   4. Strumento: dotnet
   5. Argomenti: restore
   6. Trascinare l'attività in modo da posizionarla immediatamente dopo il passaggio **NuGet restore** .
5. Salvare le modifiche apportate alla definizione di compilazione.

### <a name="try-it"></a>Prova
Selezionare **Accoda compilazione** per avviare manualmente una compilazione. Le compilazioni vengono attivate anche al momento del push o dell'archiviazione. Dopo aver verificato la corretta esecuzione della compilazione, è possibile procedere con la definizione di versione che distribuisce l'applicazione in un cluster.

## <a name="create-a-release-definition"></a>Creare una definizione di versione
Una definizione di versione di Team Services descrive un flusso di lavoro costituito da un set di attività che vengono eseguite in sequenza. L'obiettivo della definizione di versione creata è prendere un pacchetto dell'applicazione e distribuirlo in un cluster. Quando usate insieme, la definizione di compilazione e la definizione di versione possono eseguire l'intero flusso di lavoro dall'avvio con file di origine alla fine con un'applicazione in esecuzione nel cluster. Sono disponibili maggiori informazioni sulle [definizioni di versione](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)di Team Services.

### <a name="create-a-definition-from-the-release-template"></a>Creare una definizione del modello di versione
1. Aprire il progetto in Visual Studio Team Services.
2. Selezionare la scheda **Versione** .
3. Selezionare il segno verde **+** per creare una nuova definizione di versione e selezionare **Crea definizione di versione** nel menu.
4. Nella finestra di dialogo visualizzata selezionare **Distribuzione di Azure Service Fabric** all'interno della categoria del modello **Distribuzione**.
5. Selezionare **Avanti**.
6. Selezionare la definizione di compilazione da usare come origine di questa definizione di versione.  La definizione di versione fa riferimento agli elementi che sono stati prodotti dalla definizione di compilazione selezionata.
7. Selezionare la casella di controllo **Distribuzione continua** se si vuole che Team Services crei automaticamente una nuova versione e distribuisca l'applicazione di Service Fabric ogni volta che viene completata una compilazione.
8. Selezionare la coda dell'agente che si desidera usare. Sono supportati agenti ospitati.
9. Selezionare **Create**.
10. Modificare il nome della definizione facendo clic sull'icona a forma di matita nella parte superiore della pagina.
11. Selezionare il cluster in cui deve essere distribuita l'applicazione dal campo di input **Cluster Connection** (Connessione cluster) dell'attività. La connessione del cluster fornisce le informazioni necessarie per consentire all'attività di distribuzione di connettersi al cluster. Se non si dispone ancora di una connessione per il cluster, selezionare il collegamento ipertestuale **Manage** (Gestisci) accanto al campo per aggiungerne una. Nella pagina visualizzata seguire questa procedura:
    1. Selezionare **Nuovo endpoint del servizio** e quindi **Azure Service Fabric** nel menu.
    2. Selezionare il tipo di autenticazione usato dal cluster di destinazione per questo endpoint.
    3. Definire un nome per la connessione nel campo **Nome connessione** .  In genere, si userà il nome del cluster.
    4. Definire l'URL dell'endpoint di connessione client nel campo **Cluster Endpoint** (Endpoint cluster).  Esempio: tcp://contoso.westus.cloudapp.azure.com:19000.
    5. Definire le credenziali di Azure Active Directory da usare per connettersi al cluster nei campi **Nome utente** e **Password**.
    6. Per l'autenticazione basata su certificati, definire la codifica Base64 del file del certificato client nel campo **Client Certificate** (Certificato client).  Per informazioni su come ottenere questo valore, vedere la finestra popup di guida su tale campo.  Se il certificato è protetto da password, definire la password nel campo **Password** .
    7. Confermare le modifiche facendo clic su **OK**. Dopo essere tornati alla definizione di versione, fare clic sull'icona di aggiornamento sul campo **Cluster Connection** (Connessione cluster) per visualizzare l'endpoint appena aggiunto.
12. Salvare la definizione di versione.

> [!NOTE]
> Gli account Microsoft, ad esempio @hotmail.com o @outlook.com, non sono supportati con l'autenticazione di Azure Active Directory.
> 
> 

La definizione creata è costituita da un'attività di tipo **Service Fabric Application Deployment**(Distribuzione dell'applicazione Service Fabric). Per altre informazioni su questa attività, vedere la [pagina della documentazione delle attività](https://go.microsoft.com/fwlink/?LinkId=820528) .

### <a name="verify-the-template-defaults"></a>Verificare le impostazioni predefinite del modello
1. Verificare il campo di input **Profilo di pubblicazione** per l'attività **Distribuisci applicazione di Service Fabric**. Per impostazione predefinita, questo campo fa riferimento a un profilo di pubblicazione denominato Cloud.xml contenuto negli elementi della pubblicazione. Se si desidera fare riferimento a un profilo di pubblicazione diverso o se la compilazione contiene più pacchetti di applicazioni nei relativi elementi, è necessario aggiornare il percorso in modo appropriato.
2. Verificare il campo di input **Pacchetto dell'applicazione** per l'attività **Distribuisci applicazione di Service Fabric**. Per impostazione predefinita, questo campo fa riferimento al percorso del pacchetto dell'applicazione predefinito usato nel modello di definizione di compilazione.  Se il percorso del pacchetto applicazione predefinito è stato modificato nella definizione di compilazione, è necessario aggiornarlo anche qui in modo appropriato.

### <a name="try-it"></a>Prova
Selezionare **Crea versione** nel menu pulsante **Versione** per creare manualmente una versione. Nella finestra di dialogo visualizzata selezionare la compilazione sulla quale si desidera basare la versione e quindi fare clic su **Crea**. Se è abilitata la distribuzione continua, le versioni verranno create automaticamente quando la definizione di compilazione associata completa una compilazione.

## <a name="next-steps"></a>Passaggi successivi
Per sapere di più sull'integrazione continua con applicazioni di Service Fabric, leggere gli articoli seguenti:

* [Documentazione di Team Services](https://www.visualstudio.com/docs/overview)
* [Gestione della compilazione in Team Services](https://www.visualstudio.com/docs/build/overview)
* [Gestione della versione in Team Services](https://www.visualstudio.com/docs/release/overview)

