<properties
   pageTitle="Integrazione continua per l'infrastruttura di servizi | Microsoft Azure"
   description="Panoramica di come configurare l'integrazione continua per un'applicazione Service Fabric tramite Visual Studio Team Services (VSTS)."
   services="service-fabric"
   documentationCenter="na"
   authors="mthalman-msft"
   manager="timlt"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="07/25/2016"
   ms.author="mthalman" />

# Configurare l'integrazione continua per un'applicazione Service Fabric con Visual Studio Team Services

Questo articolo descrive la procedura per configurare l'integrazione continua per un'applicazione di Azure Service Fabric usando Visual Studio Team Services (VSTS) per compilarla, crearne il pacchetto e distribuirla automaticamente.

Questo documento descrive la procedura corrente e può essere modificato nel tempo.

## Prerequisiti

Attenersi alla procedura seguente per iniziare:

1. Assicurarsi di avere accesso a un account di Team Services o [crearlo](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services) manualmente.

2. Assicurarsi di avere accesso a un progetto team di Team Services o [crearlo](https://www.visualstudio.com/docs/setup-admin/create-team-project) manualmente.

3. Assicurarsi di disporre di un cluster di Service Fabric in cui distribuire l'applicazione o crearne uno usando il [portale di Azure](service-fabric-cluster-creation-via-portal.md), un [modello di Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) o [Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).

4. Verificare di aver già creato un progetto di applicazione di Service Fabric (.sfproj).

>[AZURE.NOTE] Non sono più necessari agenti di compilazione personalizzati. Gli agenti di compilazione ospitati di Team Services sono ora preinstallati con il software di gestione del cluster di Service Fabric, il che consente la distribuzione delle applicazioni direttamente da tali agenti.

## Configurare e condividere i file di origine

Prima di tutto è necessario preparare un profilo di pubblicazione destinato all'uso da parte del processo di distribuzione che verrà eseguito all'interno di Team Services. Il profilo di pubblicazione deve essere configurato impostando il cluster preparato in precedenza come destinazione:

1.	Scegliere un profilo di pubblicazione all'interno del progetto di applicazione che si desidera usare per il flusso di lavoro di integrazione continua e seguire le [istruzioni di pubblicazione](service-fabric-publish-app-remote-cluster.md) su come pubblicare un'applicazione in un cluster remoto. In realtà non è necessario pubblicare l'applicazione. È sufficiente fare clic sul collegamento ipertestuale **Save** (Salva) nella finestra di dialogo di pubblicazione dopo aver configurato le opzioni in modo appropriato.
2.	Se si desidera che l'applicazione venga aggiornata per ogni distribuzione che si verifica all'interno di Team Services, sarà necessario configurare il profilo di pubblicazione per consentire l'aggiornamento. Nella stessa finestra di dialogo di pubblicazione del passaggio 1 assicurarsi che la casella di controllo **Upgrade the Application** (Aggiorna l'applicazione) sia selezionata. Sono disponibili maggiori informazioni sulla [configurazione delle impostazioni di aggiornamento aggiuntive](service-fabric-visualstudio-configure-upgrade.md). Fare clic sul collegamento ipertestuale **Save** (Salva) per salvare le impostazioni per il profilo di pubblicazione.
3.	Assicurarsi di aver salvato le modifiche nel profilo di pubblicazione e annullare la finestra di dialogo di pubblicazione.
4.	Ora è il momento di [condividere i file di origine del progetto di applicazione](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services#vs) con Team Services. Quando i file di origine sono accessibili in Team Services, è possibile procedere al passaggio successivo della generazione di compilazioni.

## Creare una definizione di compilazione

Una definizione di compilazione di Team Services descrive un flusso di lavoro costituito da un set di istruzioni di compilazione che vengono eseguite in sequenza. L'obiettivo della definizione di compilazione che verrà creata è di produrre un pacchetto applicazione di Service Fabric e di includere alcuni altri file supplementari, che possono essere usati per distribuire l'applicazione in un cluster. Sono disponibili maggiori informazioni sulle [definizioni di compilazione](https://www.visualstudio.com/docs/build/define/create) di Team Services.

### Creare una definizione

1.	Aprire il progetto team in Visual Studio Team Services.
2.	Selezionare la scheda **Build** (Compilazione).
3.	Scegliere il segno **+** verde per creare una nuova definizione di compilazione.
4.	Nella finestra di dialogo visualizzata selezionare **vuota** nella parte inferiore.
5.	Selezionare **Avanti**.
6.	Selezionare il repository e il ramo associato all'applicazione di Service Fabric.
7.	Selezionare la coda dell'agente che si desidera usare. Sono supportati agenti ospitati.
8.	Selezionare **Create**.
9.	Passare alla scheda **Variabili** e aggiungere le seguenti variabili:
   1. Nome: BuildConfiguration, Valore: Release
   2. Nome: BuildPlatform, Valore: x64
9. Tornare alla scheda **Build** e aggiungere le operazioni di compilazione seguenti alla definizione:
   1. Programma di installazione di NuGet
   2. Visual Studio Build (nome: soluzione Build)
      1. Piattaforma: $(BuildPlatform)
      2. Configurazione: $(BuildConfiguration)
   3. Visual Studio Build (nome: pacchetto dell'applicazione)
      1. Soluzione: *\*\\*.sfproj
      2. Argomenti di MSBuild: /t:Package /p:PackageLocation=$(build.artifactstagingdirectory)\\applicationpackage
      3. Piattaforma: $(BuildPlatform)
      4. Configurazione: $(BuildConfiguration)
   4. Update Service Fabric App Versions
      1. Pacchetto dell'applicazione: $(build.artifactstagingdirectory)\\applicationpackage
   5. Copiare i file
      1. Cartella di origine: $(build.sourcesdirectory)
      2. Contenuti: \*\*\PublishProfiles\\\*.xml &lt;new line&gt; \*\*\ApplicationParameters\\\*.xml
      3. Cartella di destinazione: $(build.artifactstagingdirectory)\\projectartifacts
   6. Pubblicare gli elementi della compilazione
      1. Percorso della pubblicazione: $(build.artifactstagingdirectory)
      2. Nome elemento: drop
      3. Tipo di elemento: Server
10. Salvare la definizione di compilazione e assegnare un nome.

### Verificare i valori del campo predefinito delle attività

1.	Verificare il campo di input **Solution** (Soluzione) per le istruzioni di compilazione **NuGet restore** e **Build solution**. Per impostazione predefinita, queste istruzioni verranno eseguite su tutti i file di soluzione contenuti nel repository associato. Se si desidera che la definizione di compilazione operi solo su uno di questi file di soluzione, aggiornare in modo esplicito il percorso a tale file.
2.	Verificare il campo di input **Solution** (Soluzione) per l'istruzione di compilazione **Package application**. Per impostazione predefinita, questa istruzione di compilazione presuppone che nel repository sia presente solo un progetto di applicazione di Service Fabric (.sfproj). Se nel repository sono presenti più file di questo tipo e si desidera usare solo uno di essi come destinazione per questa definizione di compilazione, è necessario aggiornare in modo esplicito il percorso a tale file. Se si desidera creare un pacchetto di più progetti di applicazione nel repository, è necessario creare altre istruzioni di **Compilazione Visual Studio** nella definizione di compilazione, ognuna con un progetto di applicazione di destinazione. Sarà anche necessario aggiornare il campo **MSBuild Arguments** (Argomenti MSBuild) per ognuna di queste istruzioni di compilazione in modo che il percorso del pacchetto sia univoco per ciascuna di esse.
3.	Verificare il comportamento di controllo delle versioni definito nell'istruzione di compilazione **Update Service Fabric App Versions**. Per impostazione predefinita, questa istruzione aggiunge il numero di compilazione a tutti i valori di versione nei file manifesto del pacchetto applicazione. Per altre informazioni, vedere la [pagina della documentazione delle attività](https://go.microsoft.com/fwlink/?LinkId=820529). Ciò è utile per supportare l'aggiornamento dell'applicazione in quanto ogni distribuzione dell'aggiornamento richiede valori di versione diversi rispetto alla distribuzione precedente. Se non si intende usare l'aggiornamento dell'applicazione nel flusso di lavoro, è possibile disabilitare questa istruzione di compilazione.
4.	Salvare le modifiche apportate alla definizione di compilazione.

### Prova

Selezionare **Queue Build** (Accoda compilazione) per avviare manualmente una compilazione. Le compilazioni vengono attivate anche al momento del push o dell'archiviazione. Dopo aver verificato la corretta esecuzione della compilazione, è possibile definire una definizione di versione che distribuirà l'applicazione in un cluster.

## Creare una definizione di versione

Una definizione di versione di Team Services descrive un flusso di lavoro costituito da un set di attività che vengono eseguite in sequenza. L'obiettivo della definizione di versione che verrà creata è di prendere un pacchetto applicazione e distribuirlo in un cluster. Quando usate insieme, la definizione di compilazione e la definizione di versione possono eseguire l'intero flusso di lavoro dall'avvio con file di origine alla fine con un'applicazione in esecuzione nel cluster. Sono disponibili maggiori informazioni sulle [definizioni di versione](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition) di Team Services.

### Creare una definizione

1.	Aprire il progetto in Visual Studio Team Services.
2.	Selezionare la scheda **Release** (Versione).
3.	Selezionare il segno verde **+** per creare una nuova definizione di versione e selezionare **Create release definition** (Crea definizione di versione) nel menu.
4.	Nella finestra di dialogo visualizzata selezionare **vuota** nella parte inferiore.
5.	Selezionare **Avanti**.
6.	Selezionare la definizione di compilazione da usare come origine di questa definizione di versione. La definizione di versione farà riferimento agli elementi che sono stati prodotti dalla definizione di compilazione selezionata.
7.	Selezionare la casella di controllo **Continuous deployment** (Distribuzione continua) se si desidera che Team Services crei automaticamente una nuova versione e distribuisca l'applicazione di Service Fabric ogni volta che viene completata una compilazione.
8.	Selezionare la coda dell'agente che si desidera usare. Sono supportati agenti ospitati.
9.	Selezionare **Create**.
10.	Modificare il nome della definizione facendo clic sull'icona a forma di matita nella parte superiore della pagina.
11.	Selezionare il pulsante **Add tasks** (Aggiungi attività).
12.	Nella finestra di dialogo visualizzata fare clic sul pulsante Aggiungi dell'attività **Service Fabric Application Deployment** (Distribuzione dell'applicazione Service Fabric) nella categoria **Deploy** (Distribuzione).
13.	Chiudere la finestra di dialogo.
14.	Definire il percorso per il profilo di pubblicazione che si desidera usare per distribuire l'applicazione nel cluster nel campo di immissione **Publish Profile** (Profilo di pubblicazione) dell'attività. Questo percorso fa riferimento a un file contenuto negli elementi di pubblicazione prodotti dalla definizione di compilazione associata. Un esempio di percorso è: $(system.defaultworkingdirectory)/MyBuildDefinition/drop/projectartifacts/Solution/AppProject/PublishProfiles/Cloud.xml.
15.	Definire il percorso al pacchetto dell'applicazione da distribuire nel cluster nel campo di immissione **Application Package** (Pacchetto dell'applicazione) dell'attività. Questo percorso fa riferimento a una posizione contenuta negli elementi di pubblicazione prodotti dalla definizione di compilazione associata. Un esempio di percorso è: $(system.defaultworkingdirectory)/MyBuildDefinition/drop/applicationpackage.
16.	Selezionare il cluster in cui deve essere distribuita l'applicazione dal campo di input **Cluster Connection** (Connessione cluster) dell'attività. La connessione del cluster fornisce le informazioni necessarie per consentire all'attività di distribuzione di connettersi al cluster. Se non si dispone ancora di una connessione per il cluster, selezionare il collegamento ipertestuale **Manage** (Gestisci) accanto al campo per aggiungerne una. Nella pagina visualizzata seguire questa procedura:
    1. Selezionare **New Service Endpoint** (Nuovo endpoint servizio) e quindi selezionare **Azure Service Fabric** dal menu.
    2. Selezionare il tipo di autenticazione usato dal cluster di destinazione per questo endpoint.
    2. Definire un nome per la connessione nel campo **Connection Name** (Nome connessione). In genere, si userà il nome del cluster.
    3. Definire l'URL dell'endpoint di connessione client nel campo **Cluster Endpoint** (Endpoint cluster). Esempio: https://contoso.westus.cloudapp.azure.com:19000.
    4. Definire le credenziali di Azure Active Directory da usare per connettersi al cluster nei campi **Username** (Nome utente) e **Password**.
    5. Per l'autenticazione basata su certificati, definire la codifica Base64 del file del certificato client nel campo **Client Certificate** (Certificato client). Per informazioni su come ottenere questo valore, vedere la finestra popup di guida su tale campo. Se il certificato è protetto da password, definire la password nel campo **Password**.
    6. Confermare le modifiche facendo clic su **OK**. Dopo essere tornati alla definizione di versione, fare clic sull'icona di aggiornamento sul campo **Cluster Connection** (Connessione cluster) per visualizzare l'endpoint appena aggiunto.
17.	Salvare la definizione di versione.

La definizione creata è costituita da un'attività: **Deploy Service Fabric Application** (Distribuisci un'applicazione di Service Fabric). Per altre informazioni su questa attività, vedere la [pagina della documentazione delle attività](https://go.microsoft.com/fwlink/?LinkId=820528).

### Prova

Selezionare **Create Release** (Crea versione) nel menu pulsante **Release** (Versione) per creare manualmente una versione. Nella finestra di dialogo visualizzata selezionare la compilazione sulla quale si desidera basare la versione e quindi fare clic su **Create** (Crea). Se è abilitata la distribuzione continua, le versioni saranno anche create automaticamente quando la definizione di compilazione associata completa una compilazione.

## Passaggi successivi

Per sapere di più sull'integrazione continua con applicazioni di Service Fabric, leggere gli articoli seguenti:

 - [Documentazione di Team Services](https://www.visualstudio.com/docs/overview)
 - [Gestione della compilazione in Team Services](https://www.visualstudio.com/docs/build/overview)
 - [Gestione della versione in Team Services](https://www.visualstudio.com/docs/release/overview)

<!---HONumber=AcomDC_0727_2016-->
