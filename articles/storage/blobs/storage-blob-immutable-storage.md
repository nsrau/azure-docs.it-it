---
title: Funzionalità di archiviazione non modificabile dell'archivio BLOB di Azure (anteprima) | Microsoft Docs
description: Archiviazione di Azure ora offre il supporto WORM per l'archiviazione di oggetti BLOB, che consente di archiviare i dati in uno stato non cancellabile e non modificabile per un intervallo di tempo specificato dall'utente. Questa funzionalità consente alle organizzazioni in molti settori regolamentati, e in particolare alle organizzazioni di intermediazione, di archiviare i dati in conformità alle norme SEC 17a-4(f) e ad altre normative.
services: storage
author: sangsinh
manager: twooley
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 05/29/2018
ms.author: sangsinh
ms.openlocfilehash: 195537b271c442b954d6d6e6fa8d1491c07822e8
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970245"
---
# <a name="immutable-storage-feature-of-azure-blob-storage-preview"></a>Funzionalità di archiviazione non modificabile dell'archivio BLOB di Azure (anteprima)

La funzionalità di archiviazione non modificabile per i BLOB di Azure consente agli utenti di archiviare dati aziendali critici nell'archiviazione BLOB di Azure in uno stato WORM (Write Once Read Many). Questo stato rende i dati non cancellabili e non modificabili per un intervallo di tempo specificato dall'utente. Per la durata dell'intervallo di conservazione, i BLOB possono essere creati e letti, ma non modificati o eliminati.

## <a name="overview"></a>Panoramica

La funzionalità di archiviazione non modificabile consente alle organizzazioni in molti settori regolamentati, e in particolare alle organizzazioni di intermediazione, di archiviare i dati in conformità alle norme SEC 17a-4(f) e ad altre normative.

Le applicazioni tipiche includono:

- **Conformità alle normative**: la funzionalità di archiviazione non modificabile per i BLOB di Azure è progettata per consentire agli istituti finanziari e ai settori correlati di gestire la conformità alle normative SEC 17a-4(f), CFTC 1.31©-(d), FINRA e così via.

- **Conservazione sicura dei documenti**: gli utenti ottengono la massima protezione dei dati poiché il servizio di archiviazione BLOB assicura che i dati non possano essere modificati o eliminati da alcun utente, inclusi quelli con i privilegi amministrativi dell'account.

- **Blocco a fini giudiziari**: l'archiviazione non modificabile per i BLOB di Azure consente agli utenti di archiviare informazioni riservate di importanza critica per una controversia legale o un'indagine giudiziaria in uno stato inalterabile per il periodo di tempo desiderato.

La funzionalità di archiviazione non modificabile offre:

- **Supporto per i criteri di conservazione basati sul tempo:** gli utenti impostano criteri per archiviare i dati per un intervallo di tempo specificato.

- **Supporto per i criteri di blocco a fini giudiziari:** quando il periodo di conservazione non è noto, gli utenti possono impostare blocchi a fini giudiziari per archiviare i dati in modo non modificabile finché non viene rimosso il blocco.  Quando viene impostato un blocco a fini giudiziari, i BLOB possono essere creati e letti, ma non modificati o eliminati. Ogni blocco a fini giudiziari è associato a un tag alfanumerico definito dall'utente che viene usato come stringa di identificazione, ad esempio un ID del caso.

- **Supporto per tutti i livelli BLOB:** i criteri WORM sono indipendenti dal livello di archiviazione BLOB di Azure e si applicano a tutti i livelli (ad accesso frequente, ad accesso sporadico e archivio). Questo consente ai clienti di archiviare i dati nel livello con i costi ottimali per i carichi di lavoro, mantenendo al tempo stesso la non modificabilità dei dati.

- **Configurazione a livello di contenitore:** la funzionalità di archiviazione non modificabile consente agli utenti di configurare i criteri di conservazione basati sul tempo e i tag di blocco a fini giudiziari a livello di contenitore.  Gli utenti possono creare e bloccare i criteri di conservazione basati sul tempo, estendere gli intervalli di conservazione, impostare e rimuovere blocchi a fini giudiziari e così via tramite semplici impostazioni a livello di contenitore.  Questi criteri verranno applicati a tutti i BLOB nel contenitore, nuovi ed esistenti.

- **Supporto per la registrazione di controllo:** ogni contenitore include un log di controllo che contiene fino a cinque comandi di conservazione basati sul tempo per i criteri di conservazione basati sul tempo bloccati, con un massimo di tre log per le estensioni dell'intervallo di conservazione.  Per la conservazione basata sul tempo, il log contiene l'ID utente, il tipo di comando, i timestamp e il periodo di conservazione. Per i blocchi a fini giudiziari, il log contiene l'ID utente, il tipo di comando, i timestamp e i tag del blocco a fini giudiziari. Questo log viene mantenuto per tutta la durata del contenitore, in base alle linee guida delle normative SEC 17a-4(f). Un log più completo di tutte le attività del piano di controllo è disponibile nel [log attività di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). È responsabilità dell'utente archiviare questi log in modo permanente, come potrebbe essere richiesto per scopi legali o di altro tipo.

 La funzionalità è abilitata in tutte le aree pubbliche di Azure.

## <a name="how-it-works"></a>Funzionamento

L'archiviazione non modificabile per i BLOB di Azure supporta due tipi di criteri non modificabili o WORM: conservazione basata sul tempo e blocchi a fini giudiziari. Per informazioni dettagliate su come creare questi criteri non modificabili, vedere la sezione [Introduzione](#Getting-started).
Quando in un contenitore si applica un criterio di conservazione basato sul tempo o un blocco a fini giudiziari, tutti i BLOB esistenti passano allo stato non modificabile (vengono protetti da scrittura ed eliminazione). Anche tutti i nuovi BLOB caricati nel contenitore passeranno allo stato non modificabile.

> [!IMPORTANT]
> I criteri di conservazione basati sul tempo devono essere *bloccati* perché il BLOB sia in uno stato non modificabile (protetto da scrittura ed eliminazione) per la conformità a SEC 17a-4(f) e altri requisiti normativi. È consigliabile che i criteri vengano bloccati entro un periodo di tempo ragionevole, in genere entro 24 ore. Non è consigliabile usare lo stato *sbloccato* per scopi diversi da valutazioni delle funzionalità a breve termine.

 Quando si applica un criterio di conservazione basato sul tempo a un contenitore, tutti i BLOB nel contenitore rimarranno nello stato non modificabile per la durata del periodo di conservazione *effettivo*. Il periodo di conservazione effettivo per i BLOB esistenti è uguale alla differenza tra l'ora di creazione del BLOB e il periodo di conservazione specificato dall'utente. Per i nuovi BLOB, il periodo di conservazione effettivo è uguale all'intervallo di conservazione specificato dall'utente. Poiché gli utenti possono modificare il periodo di conservazione, verrà usato il valore più recente dell'intervallo di conservazione specificato dall'utente per calcolare il periodo di conservazione effettivo.

> [!TIP]
> Esempio: un utente crea un criterio di conservazione basato sul tempo con un intervallo di conservazione di cinque anni.
> Nel contenitore è presente un BLOB esistente, testblob1, che è stato creato un anno fa. Il periodo di conservazione effettivo per testblob1 sarà di quattro anni.
> Un nuovo BLOB, testblob2, viene caricato nel contenitore. Il periodo di conservazione effettivo per il nuovo BLOB sarà di cinque anni.

### <a name="legal-holds"></a>Blocchi a fini giudiziari

In caso di blocchi a fini giudiziari, tutti i BLOB nuovi ed esistenti rimarranno nello stato non modificabile finché non viene rimosso il blocco a fini giudiziari.
Per informazioni dettagliate su come impostare e rimuovere blocchi a fini giudiziari, vedere la sezione [Introduzione](#Getting-started).

A un contenitore possono essere applicati contemporaneamente sia un criterio di conservazione basato sul tempo che un blocco a fini giudiziari. Tutti i BLOB nel contenitore rimarranno nello stato non modificabile finché non vengono rimossi tutti i blocchi a fini giudiziari, anche se il relativo periodo di conservazione effettivo è scaduto. Viceversa, un BLOB rimarrà in uno stato non modificabile fino alla scadenza del periodo di conservazione effettivo anche se sono stati rimossi tutti i blocchi a fini giudiziari.
La tabella seguente illustra i tipi di operazioni BLOB che verranno disabilitate per i diversi scenari di archiviazione non modificabile.
Per informazioni dettagliate sull'API REST per i BLOB, fare riferimento alla documentazione dell'[API del servizio BLOB di Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).

|Scenario  |Stato BLOB  |Operazioni BLOB non consentite  |
|---------|---------|---------|
|L'intervallo di conservazione effettivo nel BLOB non è ancora scaduto e/o è impostato un blocco a fini giudiziari     |Non modificabile: protetto da eliminazione e scrittura         |Delete Container, Delete Blob, Put Blob1, Put Block, Put Block List, Set Blob Metadata, Put Page, Set Blob Properties, Snapshot Blob, Incremental Copy Blob, Append Block         |
|L'intervallo di conservazione effettivo nel BLOB è scaduto     |Solo protetto da scrittura (le operazioni di eliminazione sono consentite)         |Put Blob, Put Block, Put Block List, Set Blob Metadata, Put Page, Set Blob Properties, Snapshot Blob, Incremental Copy Blob, Append Block         |
|Tutti i blocchi a fini giudiziari sono stati rimossi e nessun criterio di conservazione basato sul tempo è impostato nel contenitore     |Modificabile         |Nessuna         |
|Nessun criterio WORM creato (conservazione basata sul tempo o blocco a fini giudiziari)     |Modificabile         |Nessuna         |

> [!NOTE]
> La prima operazione Put Blob e le operazioni Put Block List e Put Block necessarie per creare un BLOB sono consentite nei primi due scenari della tabella precedente, mentre tutte le operazioni successive non sono consentite.
> La funzionalità di archiviazione non modificabile è disponibile solo negli account per utilizzo generico v2 e negli account di archiviazione BLOB e deve essere creata tramite [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="pricing"></a>Prezzi

Non sono previsti costi aggiuntivi per l'uso di questa funzionalità e il prezzo dei dati non modificabili è lo stesso dei normali dati modificabili. Per informazioni dettagliate sui prezzi, fare riferimento alla [pagina dei prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

### <a name="restrictions"></a>Restrizioni

Durante l'anteprima pubblica si applicano le restrizioni seguenti:

- **Non archiviare dati di produzione o dati aziendali critici**
- Si applicano tutte le restrizioni relative all'anteprima e all'accordo di riservatezza

## <a name="getting-started"></a>Introduzione

L'archiviazione non modificabile di Azure per i BLOB di Azure è supportata nelle versioni più recenti del [portale di Azure](http://portal.azure.com), dell'[interfaccia della riga di comando 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) di Azure e di Azure [PowerShell](https://github.com/Azure/azure-powershell/releases/tag/Azure.Storage.v4.4.0-preview-May2018)

### <a name="azure-portal"></a>Portale di Azure

1. Creare un nuovo contenitore o selezionare un contenitore esistente per archiviare i BLOB che devono essere mantenuti nello stato non modificabile.
 Il contenitore deve essere in un account di archiviazione per utilizzo generico v2.
2. Fare clic sul criterio di accesso nelle impostazioni del contenitore e quindi fare clic su **+ Aggiungi criteri** nel criterio **Archivio BLOB non modificabile** come illustrato di seguito.

    ![Portale](media/storage-blob-immutable-storage/portal-image-1.png)

3. Per abilitare la conservazione basata sul tempo, scegliere Conservazione basata su tempo dal menu a discesa.

    ![Conservazione](media/storage-blob-immutable-storage/portal-image-2.png)

4. Immettere l'intervallo di conservazione desiderato espresso in giorni (il valore minimo è un giorno)

    ![Intervallo di conservazione](media/storage-blob-immutable-storage/portal-image-5-retention-interval.png)

    Come si può notare dalla figura precedente, lo stato iniziale dei criteri è sbloccato. Questo consente di testare la funzionalità con un intervallo di conservazione più limitato e apportare modifiche ai criteri prima di bloccarli. Il blocco è essenziale per la conformità alle normative SEC 17a-4 e così via.

5. Bloccare i criteri facendo clic con il pulsante destro del mouse sui tre puntini (...). Verrà visualizzato il menu seguente:

    ![Criteri di blocco](media/storage-blob-immutable-storage/portal-image-4-lock-policy.png)

    Fare clic su Criteri di blocco. Lo stato dei criteri verrà visualizzato come bloccato. Una volta bloccato, il criterio non può più essere eliminato e saranno consentite solo estensioni dell'intervallo di conservazione.

6. Per abilitare i blocchi a fini giudiziari, fare clic su + Aggiungi criteri e scegliere Blocco a fini giudiziari dal menu a discesa

    ![Blocco a fini giudiziari](media/storage-blob-immutable-storage/portal-image-legal-hold-selection-7.png)

7. Creare un blocco a fini giudiziari con uno o più tag

    ![Impostare i tag del blocco a fini giudiziari](media/storage-blob-immutable-storage/portal-image-set-legal-hold-tags.png)

### <a name="cli-20"></a>Interfaccia della riga di comando 2.0

Installare l'[estensione dell'interfaccia della riga di comando](http://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) con `az extension add -n storage-preview`

Se l'estensione è già installata, usare il comando seguente per abilitare la funzionalità di archiviazione non modificabile: `az extension update -n storage-preview`

La funzionalità è inclusa nei gruppi di comandi seguenti (eseguire "-h" su di essi per visualizzare i comandi): `az storage container immutability-policy` e `az storage container legal-hold`.

### <a name="powershell"></a>PowerShell

La funzionalità di archiviazione non modificabile è supportata in [PowerShell versione 4.4.0 (anteprima)](https://github.com/Azure/azure-powershell/releases/tag/Azure.Storage.v4.4.0-preview-May20180).
Per abilitare la funzionalità, eseguire i passaggi seguenti:

1. Assicurarsi di avere installato la versione più recente di PowerShellGet usando `Install-Module PowerShellGet –Repository PSGallery –Force`
2. Rimuovere eventuali installazioni precedenti di Azure PowerShell
3. Installare AzureRM (Azure può essere installato in modo analogo da questo repository) `Install-Module AzureRM –Repository PSGallery –AllowClobber`
4. Installare la versione di anteprima dei cmdlet del piano di gestione dell'archiviazione`Install-Module -Name AzureRM.Storage -AllowPrerelease -Repository PSGallery -AllowClobber`

Di seguito è riportato un codice PowerShell di esempio che illustra l'uso della funzionalità.

## <a name="client-libraries"></a>Librerie client

La funzionalità di archiviazione non modificabile per i BLOB di Azure è supportata nelle seguenti versioni della libreria client

- [Libreria client .NET (versione di anteprima 7.2.0 e successive)](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/7.2.0-preview)
- [Libreria client Node.js (versione 4.0.0 e successive)](https://www.npmjs.com/package/azure-arm-storage)
- [Libreria client Python (versione 2.0.0 Release Candidate 2 e successive)](https://pypi.org/project/azure-mgmt-storage/2.0.0rc1/)

## <a name="supported-values"></a>Valori supportati

- L'intervallo di conservazione minimo è di un giorno, quello massimo è di 400 anni
- Per un determinato account di archiviazione, il numero massimo di contenitori per ogni account di archiviazione con criteri non modificabili bloccati è 1000
- Per un determinato account di archiviazione, il numero massimo di contenitori con un'impostazione per un blocco a fini giudiziari è 1000
- Per un determinato contenitore, il numero massimo di tag di blocco a fini giudiziari è 10
- La lunghezza massima di un tag di blocco a fini giudiziari è di 23 caratteri alfanumerici, la lunghezza minima è di tre caratteri
- Per un determinato contenitore, il numero massimo di estensioni dell'intervallo di conservazione consentite per i criteri non modificabili bloccati è tre
- Per un determinato contenitore con un criterio non modificabile bloccato, sono disponibili un massimo di cinque log di criteri di conservazione basati sul tempo e un massimo di 10 log di criteri di blocco a fini giudiziari, che vengono mantenuti per tutta la durata del contenitore.

## <a name="faq"></a>Domande frequenti

**La funzionalità si applica solo ai BLOB in blocchi o anche ai BLOB di pagine e di aggiunta?**

La funzionalità di archiviazione non modificabile per i BLOB può essere usata con qualsiasi tipo di BLOB.  È tuttavia consigliabile usare la funzionalità principalmente per i BLOB in blocchi. A differenza dei BLOB in blocchi, i BLOB di pagina e i BLOB di aggiunta devono essere creati fuori da un contenitore WORM e quindi copiati al suo interno.  Una volta copiati in un contenitore WORM, non sono consentite ulteriori *aggiunte* a un BLOB di aggiunta o modifiche a un BLOB di pagine.

**Per usare questa funzionalità è sempre necessario creare un nuovo account di archiviazione?**

È possibile utilizzare la funzionalità di archiviazione non modificabile con tutti gli account per utilizzo generico v2 esistenti o i nuovi account di archiviazione, se il tipo di account è per utilizzo generico v2. Questa funzionalità è disponibile solo con l'archiviazione BLOB.

**Che cosa accade se si tenta di eliminare un contenitore con un criterio di conservazione basato sul tempo *bloccato* o un blocco a fini giudiziari?**

L'operazione di eliminazione del contenitore avrà esito negativo se è presente almeno un BLOB con un criterio di conservazione basato sul tempo bloccato o un blocco a fini giudiziari. L'operazione di eliminazione del contenitore verrà completata se non è presente alcun BLOB con un intervallo di conservazione attivo e non sono impostati blocchi a fini giudiziari. È necessario eliminare i BLOB prima di poter eliminare il contenitore.

**Che cosa accade se si tenta di eliminare un account di archiviazione con un contenitore WORM che dispone di un criterio di conservazione basato sul tempo *bloccato* o un blocco a fini giudiziari?**

L'eliminazione dell'account di archiviazione avrà esito negativo se è presente almeno un contenitore WORM con un blocco a fini giudiziari o un BLOB con un intervallo di conservazione attivo.  È necessario eliminare tutti i contenitori WORM prima di poter eliminare l'account di archiviazione.  Vedere la domanda 2 per informazioni sull'eliminazione del contenitore.

**È possibile spostare i dati tra i diversi livelli BLOB (ad accesso frequente, ad accesso sporadico e archivio) quando il BLOB è in stato non modificabile?**

Sì, è possibile usare il comando Set Blob Tier per spostare i dati tra i livelli BLOB, mantenendo al tempo stesso i dati nello stato non modificabile. La funzionalità di archiviazione non modificabile è supportata nei livelli BLOB ad accesso frequente, ad accesso sporadico e archivio.

**Che cosa accade se non si effettua il pagamento e il periodo di conservazione non è scaduto?**

In caso di mancato pagamento, verranno applicati i normali criteri di conservazione dei dati, in base al periodo di tolleranza specificato nei termini e nelle condizioni del contratto con Microsoft.

**È disponibile una versione di valutazione o un periodo di tolleranza per provare la funzionalità?**

Sì, quando si crea un criterio di conservazione basato sul tempo, questo è in uno stato *sbloccato*. In questo stato è possibile apportare qualsiasi modifica all'intervallo di conservazione, ad esempio aumentandolo o riducendolo oppure perfino eliminando il criterio. Una volta bloccato, il criterio rimane bloccato per sempre in modo da impedire l'eliminazione. Inoltre, il periodo di conservazione non può più essere ridotto quando il criterio è bloccato. È consigliabile usare lo stato *sbloccato* solo per scopi di valutazione e bloccare i criteri entro un periodo di 24 ore, in modo da evitare di rischiare la mancata conformità con SEC 17a-4(f) e altre normative.

**La funzionalità è disponibile nei cloud nazionali e per enti pubblici?**

La funzionalità di archiviazione non modificabile è attualmente disponibile solo nelle aree pubbliche di Azure. Qualora si sia interessati a un cloud nazionale specifico, inviare un messaggio di posta elettronica a azurestoragefeedback@microsoft.com.

## <a name="sample-code"></a>Codice di esempio

Uno script PowerShell di esempio è riportato di seguito per riferimento.
Questo script crea un nuovo account di archiviazione e un contenitore, quindi mostra come impostare e rimuovere blocchi a fini giudiziari, creare e bloccare i criteri di conservazione basati sul tempo (ImmutabilityPolicy), estendere l'intervallo di conservazione e così via.

```powershell
\$ResourceGroup = "\<Enter your resource group\>”

\$StorageAccount = "\<Enter your storage account name\>"

\$container = "\<Enter your container name\>"

\$container2 = "\<Enter another container name\>”

\$location = "\<Enter the storage account location\>"

\# Login to the Azure Resource Manager Account

Login-AzureRMAccount

Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Storage"

\# Create your Azure Resource Group

New-AzureRmResourceGroup -Name \$ResourceGroup -Location \$location

\# Create your Azure storage account

New-AzureRmStorageAccount -ResourceGroupName \$ResourceGroup -StorageAccountName
\$StorageAccount -SkuName Standard_LRS -Location \$location -Kind Storage

\# Create a new container

New-AzureRmStorageContainer -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -Name \$container

\# Create Container 2 with Storage Account object

\$accountObject = Get-AzureRmStorageAccount -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount

New-AzureRmStorageContainer -StorageAccount \$accountObject -Name \$container2

\# Get container

Get-AzureRmStorageContainer -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -Name \$container

\# Get Container with Account object

\$containerObject = Get-AzureRmStorageContainer -StorageAccount \$accountObject
-Name \$container

\#list container

Get-AzureRmStorageContainer -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount

\#remove container (Add -Force to dismiss prompt)

Remove-AzureRmStorageContainer -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -Name \$container2

\#with Account object

Remove-AzureRmStorageContainer -StorageAccount \$accountObject -Name
\$container2

\#with Container object

\$containerObject2 = Get-AzureRmStorageContainer -StorageAccount \$accountObject
-Name \$container2

Remove-AzureRmStorageContainer -InputObject \$containerObject2

\#Set LegalHold

Add-AzureRmStorageContainerLegalHold -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -Name \$container -Tag tag1,tag2

\#with Account object

Add-AzureRmStorageContainerLegalHold -StorageAccount \$accountObject -Name
\$container -Tag tag3

\#with Container object

Add-AzureRmStorageContainerLegalHold -Container \$containerObject -Tag tag4,tag5

\#Clear LegalHold

Remove-AzureRmStorageContainerLegalHold -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -Name \$container -Tag tag2

\#with Account object

Remove-AzureRmStorageContainerLegalHold -StorageAccount \$accountObject -Name
\$container -Tag tag3,tag5

\#with Container object

Remove-AzureRmStorageContainerLegalHold -Container \$containerObject -Tag tag4

\# create/update ImmutabilityPolicy

\#\# with account/container name

Set-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -ContainerName \$container
-ImmutabilityPeriod 10

\#with Account object

Set-AzureRmStorageContainerImmutabilityPolicy -StorageAccount \$accountObject
-ContainerName \$container -ImmutabilityPeriod 1 -Etag \$policy.Etag

\#with Container object

\$policy = Set-AzureRmStorageContainerImmutabilityPolicy -Container
\$containerObject -ImmutabilityPeriod 7

\#\# with ImmutabilityPolicy object

Set-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy \$policy
-ImmutabilityPeriod 5

\#get ImmutabilityPolicy

Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -ContainerName \$container

\#with Account object

Get-AzureRmStorageContainerImmutabilityPolicy -StorageAccount \$accountObject
-ContainerName \$container

\#with Container object

Get-AzureRmStorageContainerImmutabilityPolicy -Container \$containerObject

\#Lock ImmutabilityPolicy (Add -Force to dismiss prompt)

\#\# with ImmutabilityPolicy object

\$policy = Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container

\$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy
\$policy -force

\#\# with account/container name

\$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container
-Etag \$policy.Etag

\#with Account object

\$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -StorageAccount
\$accountObject -ContainerName \$container -Etag \$policy.Etag

\#with Container object

\$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -Container
\$containerObject -Etag \$policy.Etag -force

\#Extend ImmutabilityPolicy

\#\# with ImmutabilityPolicy object

\$policy = Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container

\$policy = Set-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy
\$policy -ImmutabilityPeriod 11 -ExtendPolicy

\#\# with account/container name

\$policy = Set-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container
-ImmutabilityPeriod 11 -Etag \$policy.Etag -ExtendPolicy

\#with Account object

\$policy = Set-AzureRmStorageContainerImmutabilityPolicy -StorageAccount
\$accountObject -ContainerName \$container -ImmutabilityPeriod 12 -Etag
\$policy.Etag -ExtendPolicy

\#with Container object

\$policy = Set-AzureRmStorageContainerImmutabilityPolicy -Container
\$containerObject -ImmutabilityPeriod 13 -Etag \$policy.Etag -ExtendPolicy

\#Remove ImmutabilityPolicy (Add -Force to dismiss prompt)

\#\# with ImmutabilityPolicy object

\$policy = Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container

Remove-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy \$policy

\#\# with account/container name

Remove-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container
-Etag \$policy.Etag

\#with Account object

Remove-AzureRmStorageContainerImmutabilityPolicy -StorageAccount \$accountObject
-ContainerName \$container -Etag \$policy.Etag

\#with Container object

Remove-AzureRmStorageContainerImmutabilityPolicy -Container \$containerObject
-Etag \$policy.Etag
```