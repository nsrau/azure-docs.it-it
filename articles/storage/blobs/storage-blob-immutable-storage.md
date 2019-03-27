---
title: Archiviazione non modificabile per i BLOB del servizio di archiviazione di Azure | Microsoft Docs
description: Archiviazione di Azure offre il supporto WORM (Write Once, Read Many) per l'archiviazione di oggetti BLOB, che consente agli utenti di archiviare i dati in uno stato non cancellabile e non modificabile per un intervallo specificato.
services: storage
author: xyh1
ms.service: storage
ms.topic: article
ms.date: 03/26/2019
ms.author: hux
ms.subservice: blobs
ms.openlocfilehash: 32328b89e8a220269f0d07c3700566db5b899d5b
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445696"
---
# <a name="store-business-critical-data-in-azure-blob-storage"></a>Archiviare dati critici in Archiviazione BLOB di Azure

Archiviazione non modificabile per l'archiviazione Blob di Azure consente agli utenti di archiviare oggetti di dati aziendali critici in uno stato WORM (Write Once, Read Many). Questo stato rende i dati non cancellabili e non modificabili per un intervallo di tempo specificato dall'utente. Gli oggetti BLOB possono essere creati e letto, ma non modificati o eliminati, per la durata dell'intervallo di conservazione. Archiviazione non modificabile è abilitata per utilizzo generico v2 e account di archiviazione Blob in tutte le aree di Azure.

## <a name="overview"></a>Panoramica

Archiviazione non modificabile aiuta l'organizzazione sanitaria, istituti finanziari e settori correlati, in particolare alle organizzazioni di intermediario, per archiviare i dati in modo sicuro. Può anche essere sfruttata in qualsiasi scenario per proteggere i dati critici dalla modifica o eliminazione. 

Le applicazioni tipiche includono:

- **Conformità alle normative**: l'archiviazione non modificabile per l'Archiviazione BLOB di Azure aiuta le organizzazioni a soddisfare i requisiti di SEC 17a-4(f), CFTC 1.31(d), FINRA e altre normative. Un white paper tecnico da Cohasset associa illustra in dettaglio come non modificabile gli indirizzi di archiviazione questi requisiti normativi è scaricabile tramite il [Microsoft Service Trust Portal](https://aka.ms/AzureWormStorage). Il [Centro protezione Azure](https://www.microsoft.com/trustcenter/compliance/compliance-overview) contiene informazioni dettagliate sulle nostre certificazioni di conformità.

- **Conservazione sicura dei documenti**: Archiviazione non modificabile per l'archiviazione Blob di Azure garantisce che i dati non possono essere modificati o eliminati da tutti gli utenti, inclusi gli utenti con privilegi di amministratore account.

- **Blocco a fini giudiziari**: Archiviazione non modificabile per l'archiviazione Blob di Azure consente agli utenti di archiviare informazioni riservate che sono fondamentale per controversia legale o business usare in uno stato a prova di manomissione per tutta la durata desiderata finché non viene rimosso lo stato di attesa. Questa funzionalità non è limitata solo ai casi d'uso valido, ma può anche essere considerata un'attesa basata su eventi o un blocco di enterprise, in cui è necessaria la necessità di proteggere i dati in base ai criteri aziendali o i trigger di evento.

Archiviazione non modificabile supporta quanto segue:

- **[Supporto di criteri di conservazione basati sul tempo](#time-based-retention)**: Gli utenti possono impostare criteri per archiviare i dati per un intervallo specificato. Quando un criterio di conservazione basati sul tempo è impostato, BLOB possono essere creati e leggere, ma non modificati o eliminati. Una volta scaduto il periodo di conservazione, i BLOB possono essere eliminati ma non verrà sovrascritti.

- **[Supporto dei criteri a fini giudiziari](#legal-holds)**: Se l'intervallo di conservazione non è noto, gli utenti possono impostare giudiziari per archiviare i dati immutably fino a quando non viene cancellata la giudiziari.  Quando è impostato un criterio a fini giudiziari, i BLOB possono da creati e letti, ma non modificare o eliminare. Ogni a fini giudiziari sono associato un tag definito dall'utente alfanumerici (ad esempio un ID case, nome dell'evento e così via) che viene usato come una stringa di identificazione. 

- **Supporto per tutti i livelli di BLOB**: i criteri WORM sono indipendenti dal livello di Archiviazione BLOB di Azure e si applicano a tutti i livelli (ad accesso frequente, ad accesso sporadico e archivio). Gli utenti possono trasferire i dati nel livello con i costi ottimali per i carichi di lavoro, mantenendo al tempo stesso la non modificabilità dei dati.

- **Configurazione a livello di contenitore**: gli utenti possono configurare criteri di conservazione basati sul tempo e tag di blocco a fini giudiziari a livello di contenitore. Utilizzando le impostazioni a livello di contenitore semplici, gli utenti possono creare e i criteri di conservazione basati sul tempo di blocco, estendere gli intervalli di conservazione, set e deselezionare giudiziari e altro ancora. Questi criteri si applicano a tutti i BLOB nel contenitore, nuovi ed esistenti.

- **Supporto per la registrazione di controllo**: ogni contenitore include un log di controllo. Sono presenti fino a cinque comandi di conservazione basati sul tempo per i criteri di conservazione basati sul tempo bloccati, con un massimo di tre log per le estensioni dell'intervallo di conservazione. Per la conservazione basata sul tempo, il log contiene l'ID utente, il tipo di comando, i timestamp e l'intervallo di conservazione. Per i blocchi a fini giudiziari, il log contiene l'ID utente, il tipo di comando, i timestamp e i tag di blocco a fini giudiziari. Questo log viene mantenuto per tutta la durata del contenitore, in conformità alle linee guida delle normative SEC 17a-4(f). Il [Log attività di Azure](../../azure-monitor/platform/activity-logs-overview.md) viene visualizzato un log più completo di tutte le attività del piano di controllo, durante l'abilitazione [log di diagnostica di Azure](../../azure-monitor/platform/diagnostic-logs-overview.md) mantiene, unitamente a operazioni del piano dati. È responsabilità dell'utente archiviare questi log in modo permanente, come potrebbe essere richiesto per scopi legali o di altro tipo.

## <a name="how-it-works"></a>Funzionamento

L'archiviazione non modificabile per Archiviazione BLOB di Azure supporta due tipi di criteri non modificabili o WORM: conservazione basata sul tempo e blocchi a fini giudiziari. Quando un criterio di conservazione basati sul tempo o a fini giudiziari viene applicata in un contenitore, tutti i BLOB esistenti di spostare in uno stato immutabile WORM inferiore a 30 secondi. Tutti i nuovi BLOB caricati in tale contenitore verrà spostata anche nello stato non modificabile. Dopo aver spostato tutti i BLOB nello stato non modificabile, il criterio non modificabile è confermato e tutto sovrascrivere o eliminare le operazioni per gli oggetti nuovi ed esistenti nel contenitore non modificabile non sono consentite.

Eliminazione dell'Account e contenitore inoltre non sono consentiti se sono presenti eventuali BLOB protetti da un criterio non modificabile. L'operazione di eliminazione del contenitore avrà esito negativo se esiste almeno un BLOB con criteri di conservazione basati sul tempo bloccati o un blocco a fini giudiziari. L'eliminazione dell'account di archiviazione avrà esito negativo se è presente almeno un contenitore WORM con un blocco a fini giudiziari o un BLOB con un intervallo di conservazione attivo. 

### <a name="time-based-retention"></a>Conservazione basata su tempo

> [!IMPORTANT]
> I criteri di conservazione basati sul tempo devono essere *bloccati* perché il BLOB sia in uno stato non modificabile (protetto da scrittura ed eliminazione) per la conformità a SEC 17a-4(f) e ad altri requisiti normativi. È consigliabile bloccare i criteri in un periodo di tempo ragionevole, in genere entro 24 ore. Non è consigliabile usare lo stato *sbloccato* per scopi diversi da valutazioni delle funzionalità a breve termine.

Quando vengono applicati criteri di conservazione basati sul tempo a un contenitore, tutti i BLOB nel contenitore rimangono nello stato non modificabile per la durata del periodo di conservazione *effettivo*. Il periodo di conservazione effettivo per i BLOB esistenti è uguale alla differenza tra l'ora di modifica del blob e il periodo di memorizzazione specificato dall'utente.

Per i nuovi BLOB, il periodo di conservazione effettivo è uguale all'intervallo di conservazione specificato dall'utente. Poiché gli utenti possono estendere l'intervallo di conservazione, l'archiviazione non modificabile usa il valore più recente dell'intervallo di conservazione specificato dall'utente per calcolare il periodo di conservazione effettivo.

> [!TIP]
> **Esempio:** Un utente crea criteri di conservazione basati sul tempo con un intervallo di conservazione di cinque anni.
>
> Il blob esistente in quel contenitore _testblob1_, è stato creato un anno fa. Periodo di memorizzazione effettivo _testblob1_ è quattro anni.
>
> Un nuovo blob _testblob2_, viene ora caricato nel contenitore. Il periodo di conservazione effettivo per il nuovo BLOB è di cinque anni.

### <a name="legal-holds"></a>Blocchi a fini giudiziari

Quando si imposta un blocco a fini giudiziari, tutti i BLOB nuovi ed esistenti rimangono nello stato non modificabile finché il blocco a fini giudiziari non viene rimosso. Per altre informazioni su come impostare e rimuovere blocchi a fini giudiziari, vedere la sezione [Introduzione](#getting-started).

A un contenitore possono essere applicati contemporaneamente sia criteri di conservazione basati sul tempo che un blocco a fini giudiziari. Tutti i BLOB nel contenitore rimangono nello stato non modificabile finché non vengono rimossi tutti i blocchi a fini giudiziari, anche se il relativo periodo di conservazione effettivo è scaduto. Viceversa, un BLOB rimane in uno stato non modificabile fino alla scadenza del periodo di conservazione effettivo anche se sono stati rimossi tutti i blocchi a fini giudiziari.

La tabella seguente illustra i tipi di operazioni BLOB che vengono disabilitate per i diversi scenari di archiviazione non modificabile. Per altre informazioni, vedere la documentazione dell'[API del servizio BLOB di Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).

|Scenario  |Stato BLOB  |Operazioni BLOB non consentite  |
|---------|---------|---------|
|L'intervallo di conservazione effettivo nel BLOB non è ancora scaduto e/o è impostato un blocco a fini giudiziari     |Non modificabile: protetto da eliminazione e scrittura         | Inserire un Blob<sup>1</sup>, inserire il blocco<sup>1</sup>, Put Block List<sup>1</sup>, eliminare i metadati dei Blob di Set di contenitori, Blob, eliminazione, pagina, impostare le proprietà del Blob, Snapshot Blob, Blob, copia incrementale Aggiungi blocco         |
|L'intervallo di conservazione effettivo nel BLOB è scaduto     |Solo protetto da scrittura (le operazioni di eliminazione sono consentite)         |Put Blob<sup>1</sup>, Put Block<sup>1</sup>, Put Block List<sup>1</sup>, Set Blob Metadata, Put Page, Set Blob Properties,  Snapshot Blob, Incremental Copy Blob, Append Block         |
|Tutti i blocchi a fini giudiziari sono stati rimossi e non sono impostati criteri di conservazione basati sul tempo nel contenitore     |Modificabile         |Nessuna         |
|Non sono stati creati criteri WORM (conservazione basata sul tempo o blocco a fini giudiziari)     |Modificabile         |Nessuna         |

<sup>1</sup> l'applicazione consente di creare un nuovo blob una volta queste operazioni. Tutte le successive sovrascrivere non sono consentite operazioni su un percorso blob esistente in un contenitore non modificabile.

## <a name="pricing"></a>Prezzi

Per l'uso di questa funzionalità non sono previsti costi aggiuntivi. Il prezzo dei dati non modificabili è lo stesso dei normali dati modificabili. Per informazioni sui prezzi di Archiviazione BLOB di Azure, vedere la [pagina dei prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="getting-started"></a>Introduzione
È disponibile solo per utilizzo generico v2 e account di archiviazione Blob di archiviazione non modificabile. Questi account deve essere gestito attraverso [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Per informazioni sull'aggiornamento di un account di archiviazione v1 generico esistente, vedere [esegue l'aggiornamento di un account di archiviazione](../common/storage-account-upgrade.md).

Le versioni più recenti del [portale di Azure](https://portal.azure.com), [CLI di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), e [Azure PowerShell](https://github.com/Azure/azure-powershell/releases) supportano l'archiviazione non modificabile per l'archiviazione Blob di Azure. [Supporto delle librerie client](#client-libraries) viene anche fornito.


### <a name="azure-portal"></a>Portale di Azure

1. Creare un nuovo contenitore o selezionare un contenitore esistente per archiviare i BLOB che devono essere mantenuti nello stato non modificabile.
 Il contenitore deve essere in un account di archiviazione per utilizzo generico v2 o BLOB.
2. Selezionare **Criteri di accesso** nelle impostazioni del contenitore. Selezionare quindi **+ Aggiungi criteri** in **Archivio BLOB non modificabile**.

    ![Impostazioni del contenitore nel portale](media/storage-blob-immutable-storage/portal-image-1.png)

3. Per abilitare la conservazione basata sul tempo, scegliere **Conservazione basata su tempo** dal menu a discesa.

    ![Opzione "Conservazione basata su tempo" selezionata in "Tipo di criteri"](media/storage-blob-immutable-storage/portal-image-2.png)

4. Immettere l'intervallo di conservazione in giorni (i valori accettabili di 1 a 146000 giorni).

    ![Casella "Aggiorna periodo di conservazione a"](media/storage-blob-immutable-storage/portal-image-5-retention-interval.png)

    Lo stato iniziale del criterio viene sbloccato testare la funzionalità e apportare modifiche ai criteri prima bloccarla. I criteri di blocco sono essenziali per la conformità alle normative, ad esempio SEC 17a-4.

5. Bloccare i criteri. Fare doppio clic sui puntini di sospensione (**...** ), e viene visualizzato il menu seguente con le azioni aggiuntive:

    ![Comando per il blocco dei criteri nel menu](media/storage-blob-immutable-storage/portal-image-4-lock-policy.png)

6. Selezionare **criteri di blocco** e confermare il blocco. I criteri ora sono bloccato e non possono essere eliminato, saranno consentite solo le estensioni dell'intervallo di conservazione. Elimina BLOB e le sostituzioni non sono consentite. 

    ![Conferma "blocco" criteri nel menu](media/storage-blob-immutable-storage/portal-image-5-lock-policy.png)

7. Per abilitare i blocchi a fini giudiziari, selezionare **+ Aggiungi criteri**. Scegliere **Blocco a fini giudiziari** dal menu a discesa.

    !["Blocco a fini giudiziari" nel menu sotto "Tipo di criteri"](media/storage-blob-immutable-storage/portal-image-legal-hold-selection-7.png)

8. Creare un blocco a fini giudiziari con uno o più tag.

    ![Casella "Nome tag" sotto il tipo di criteri](media/storage-blob-immutable-storage/portal-image-set-legal-hold-tags.png)

9. Per cancellare un a fini giudiziari, è sufficiente rimuovere il tag di identificatore applicato a fini giudiziari.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

La funzionalità è inclusa nei gruppi di comandi seguenti: `az storage container immutability-policy` e `az storage container legal-hold`. Eseguire `-h` per visualizzare i comandi.

### <a name="powershell"></a>PowerShell

Il modulo di anteprima Az.Storage supporta l'archiviazione non modificabile.  Per abilitare la funzionalità, seguire questa procedura:

1. Assicurarsi di avere la versione più recente di PowerShellGet installata: `Install-Module PowerShellGet –Repository PSGallery –Force`.
2. Rimuovere eventuali installazioni precedenti di Azure PowerShell.
3. Installare Azure PowerShell: `Install-Module Az –Repository PSGallery –AllowClobber`.
4. Installare la versione di anteprima del modulo di Archiviazione di Azure PowerShell: `Install-Module Az.Storage -AllowPrerelease -Repository PSGallery -AllowClobber`.

La sezione [Codice PowerShell di esempio](#sample-powershell-code) più avanti in questo articolo illustra l'utilizzo della funzionalità.

## <a name="client-libraries"></a>Librerie client

Le librerie client seguenti supportano l'archiviazione non modificabile per Archiviazione BLOB di Azure:

- [Libreria client .NET versione 7.2.0-preview e versioni successive](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/7.2.0-preview)
- [Libreria client Node.js versione 4.0.0 e versioni successive](https://www.npmjs.com/package/azure-arm-storage)
- [Libreria client Python versione 2.0.0 versione finale candidata 2 e versioni successive](https://pypi.org/project/azure-mgmt-storage/2.0.0rc2/)
- [Libreria client Java](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/storage/resource-manager/Microsoft.Storage/preview/2018-03-01-preview)

## <a name="supported-values"></a>Valori supportati

- L'intervallo di conservazione minimo è di un giorno. Il valore massimo è 146,000 giorni (400 anni).
- Per un account di archiviazione, il numero massimo di contenitori con criteri non modificabili bloccati è 1.000.
- Per un account di archiviazione, il numero massimo di contenitori con un'impostazione di blocco a fini giudiziari è 1.000.
- Per un contenitore, il numero massimo di tag di blocco a fini giudiziari è 10.
- La lunghezza massima di un tag di blocco a fini giudiziari è di 23 caratteri alfanumerici. La lunghezza minima è di tre caratteri.
- Per un contenitore, il numero massimo di estensioni dell'intervallo di conservazione consentite per i criteri non modificabili bloccati è tre.
- Per un contenitore con criteri non modificabili bloccati, vengono conservati un massimo di cinque log di criteri di conservazione basati sul tempo e un massimo di 10 log di criteri di blocco a fini giudiziari per la durata del contenitore.

## <a name="faq"></a>Domande frequenti

**È possibile fornire la documentazione di conformità di WORM?**

Sì. Per la conformità di documento, Microsoft mantenuta una società di valutazione indipendente leader specializzata in governance delle informazioni e gestione record, associa Cohasset, per valutare la conformità ai requisiti specifici e non modificabile archiviazione Blob di Azure per il settore dei servizi finanziari. Cohasset convalidare che non modificabile archiviazione Blob di Azure quando viene utilizzata per conservare i BLOB basato sul tempo in uno stato WORM, soddisfi i requisiti di archiviazione rilevanti del CFTC regola 1.31(c)-(d) FINRA regola 4511 e SEC Rule 17a-4. Microsoft come destinazione questo set di regole, poiché tali versioni rappresentano le indicazioni prescrittive più presenti a livello globale per la conservazione dei record per gli istituti finanziari. Il report Cohasset è disponibile nel [Microsoft Service Trust Center](https://aka.ms/AzureWormStorage).

**La funzionalità si applica solo ai BLOB in blocchi o anche ai BLOB di pagine e di aggiunta?**

L'archiviazione non modificabile può essere usata con qualsiasi tipo di BLOB, ma è consigliabile usarla principalmente per i BLOB in blocchi. A differenza dei BLOB in blocchi, i BLOB di pagine e i BLOB di aggiunta devono essere creati al di fuori di un contenitore WORM e quindi copiati al suo interno. Dopo aver copiato questi BLOB nel contenitore WORM, non un'ulteriore *Accoda* per un accodamento sono consentite modifiche in un blob di pagine o blob.

**È necessario creare un nuovo account di archiviazione per usare questa funzionalità?**

No, è possibile usare archiviazione non modificabile con qualsiasi nuovo o esistente utilizzo generico v2 o l'account di archiviazione Blob. Questa funzionalità è destinata all'utilizzo con i BLOB in blocchi negli account per utilizzo generico v2 e Blob di archiviazione. Account di archiviazione generale utilizzo generico v1 non sono supportati ma può essere facilmente aggiornato per utilizzo generico v2. Per informazioni sull'aggiornamento di un account di archiviazione v1 generico esistente, vedere [esegue l'aggiornamento di un account di archiviazione](../common/storage-account-upgrade.md).

**È possibile applicare a fini giudiziari sia criteri di conservazione basati sul tempo?**

A un contenitore possono essere applicati contemporaneamente sia criteri di conservazione basati sul tempo che un blocco a fini giudiziari. Tutti i BLOB nel contenitore rimangono nello stato non modificabile finché non vengono rimossi tutti i blocchi a fini giudiziari, anche se il relativo periodo di conservazione effettivo è scaduto. Viceversa, un BLOB rimane in uno stato non modificabile fino alla scadenza del periodo di conservazione effettivo anche se sono stati rimossi tutti i blocchi a fini giudiziari.

**Sono i criteri a fini giudiziari solo per azioni legali o esistono altri scenari di utilizzo?**

No, giudiziari è semplicemente il termine generale utilizzato per un criterio di conservazione non basati sul tempo. Non dovrà essere usato solo per controversia legale proceedings correlati. I criteri di esenzione validi sono utili per la disabilitazione di sovrascrittura e le eliminazioni per la protezione aziendale importanti dati WORM, in cui il periodo di conservazione è sconosciuto. È possibile utilizzarlo come un criterio dell'organizzazione per proteggere i WORM carichi di lavoro cruciali o usarlo come un criterio di gestione temporanea prima di un trigger di evento personalizzato richiede l'uso di un criterio di conservazione basati sul tempo. 

**Che cosa accade se si tenta di eliminare un contenitore con un criterio di conservazione basato sul tempo *bloccato* o un blocco a fini giudiziari?**

L'operazione di eliminazione del contenitore avrà esito negativo se esiste almeno un BLOB con criteri di conservazione basati sul tempo bloccati o un blocco a fini giudiziari. L'operazione di eliminazione del contenitore verrà completata solo se non esistono BLOB con un intervallo di conservazione attivo e non sono impostati blocchi a fini giudiziari. È necessario eliminare i BLOB prima di poter eliminare il contenitore.

**Che cosa accade se si tenta di eliminare un account di archiviazione con un contenitore WORM che dispone di un criterio di conservazione basato sul tempo *bloccato* o un blocco a fini giudiziari?**

L'eliminazione dell'account di archiviazione avrà esito negativo se è presente almeno un contenitore WORM con un blocco a fini giudiziari o un BLOB con un intervallo di conservazione attivo. È necessario eliminare tutti i contenitori WORM prima di poter eliminare l'account di archiviazione. Per informazioni sull'eliminazione di contenitori, vedere la domanda precedente.

**È possibile spostare i dati tra i diversi livelli BLOB (ad accesso frequente, ad accesso sporadico e archivio) quando il BLOB è in stato non modificabile?**

Sì, è possibile utilizzare il comando Set Blob Tier per spostare dati tra i livelli di blob, mantenendo i dati nello stato non modificabile conforme. L'archiviazione non modificabile è supportata nei livelli BLOB ad accesso frequente, ad accesso sporadico e archivio.

**Che cosa accade se non si effettua il pagamento e il periodo di conservazione non è scaduto?**

In caso di mancato pagamento, verranno applicati i normali criteri di conservazione dati previsti nei termini e nelle condizioni del contratto stipulato con Microsoft.

**È disponibile una versione di valutazione o un periodo di tolleranza per provare la funzionalità?**

Sì. Quando vengono creati per la prima volta, i criteri di conservazione basati sul tempo si trovano in uno stato *sbloccato*. In questo stato è possibile apportare qualsiasi modifica all'intervallo di conservazione, ad esempio aumentandolo o riducendolo, ed è anche possibile eliminare i criteri. Dopo che sono stati bloccati, i criteri rimangono bloccati fino alla scadenza dell'intervallo di conservazione. Questo criterio di blocco impedisce l'eliminazione e la modifica all'intervallo di conservazione. È consigliabile usare lo stato *sbloccato* solo per scopi di valutazione e bloccare i criteri entro un periodo di 24 ore. Ciò consente la conformità a SEC 17a-4(f) e altre normative.

**È possibile usare l'eliminazione temporanea con i criteri di blob non modificabile?**

Sì. [Eliminazione temporanea per l'archiviazione Blob di Azure](storage-blob-soft-delete.md) applica per tutti i contenitori all'interno di un account di archiviazione indipendentemente dal fatto un criterio di conservazione basati sul tempo o a fini giudiziari. Si consiglia di abilitare l'eliminazione temporanea per una protezione aggiuntiva prima di tutti i criteri non modificabili di WORM siano applicati e confermati. 

**La funzionalità è disponibile nei cloud nazionali e per enti pubblici?**

L'archiviazione non modificabile è disponibile nelle aree pubbliche di Azure, in Cina e per gli enti pubblici. Se nell'area di archiviazione non modificabile non è disponibile, contattare il supporto e alla posta elettronica azurestoragefeedback@microsoft.com.

## <a name="sample-powershell-code"></a>Codice PowerShell di esempio

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Lo script di PowerShell di esempio seguente viene fornito a scopo di riferimento. Questo script crea un nuovo account di archiviazione e un nuovo contenitore. Viene quindi illustrato come impostare e rimuovere i blocchi a fini giudiziari, creare e bloccare i criteri di conservazione basati sul tempo (noti anche come criteri di immutabilità) ed estendere l'intervallo di conservazione.

Configurare e testare l'account di archiviazione di Azure:

```powershell
$ResourceGroup = "<Enter your resource group>”
$StorageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$container2 = "<Enter another container name>”
$location = "<Enter the storage account location>"

# Log in to the Azure Resource Manager account
Login-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $ResourceGroup -Location $location

# Create your Azure storage account
New-AzStorageAccount -ResourceGroupName $ResourceGroup -StorageAccountName `
    $StorageAccount -SkuName Standard_LRS -Location $location -Kind StorageV2

# Create a new container
New-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Create Container 2 with a storage account object
$accountObject = Get-AzStorageAccount -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount
New-AzStorageContainer -StorageAccount $accountObject -Name $container2

# Get a container
Get-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Get a container with an account object
$containerObject = Get-AzStorageContainer -StorageAccount $accountObject -Name $container

# List containers
Get-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount

# Remove a container (add -Force to dismiss the prompt)
Remove-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container2

# Remove a container with an account object
Remove-AzStorageContainer -StorageAccount $accountObject -Name $container2

# Remove a container with a container object
$containerObject2 = Get-AzStorageContainer -StorageAccount $accountObject -Name $container2
Remove-AzStorageContainer -InputObject $containerObject2
```

Impostare e cancellare i blocchi a fini giudiziari:

```powershell
# Set a legal hold
Add-AzStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag1>,<tag2>,...

# with an account object
Add-AzStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>

# with a container object
Add-AzStorageContainerLegalHold -Container $containerObject -Tag <tag4>,<tag5>,...

# Clear a legal hold
Remove-AzStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag2>

# with an account object
Remove-AzStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>,<tag5>

# with a container object
Remove-AzStorageContainerLegalHold -Container $containerObject -Tag <tag4>
```

Creare o aggiornare i criteri di immutabilità:
```powershell
# with an account name or container name
Set-AzStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container -ImmutabilityPeriod 10

# with an account object
Set-AzStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -ImmutabilityPeriod 1 -Etag $policy.Etag

# with a container object
$policy = Set-AzStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 7

# with an immutability policy object
Set-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -ImmutabilityPeriod 5
```

Recuperare i criteri di immutabilità:
```powershell
# Get an immutability policy
Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container

# with an account object
Get-AzStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container

# with a container object
Get-AzStorageContainerImmutabilityPolicy -Container $containerObject
```

Bloccare i criteri di immutabilità (aggiungere -Force per ignorare il prompt):
```powershell
# with an immutability policy object
$policy = Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
$policy = Lock-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -force

# with an account name or container name
$policy = Lock-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
$policy = Lock-AzStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -Etag $policy.Etag

# with a container object
$policy = Lock-AzStorageContainerImmutabilityPolicy -Container `
    $containerObject -Etag $policy.Etag -force
```

Estendere i criteri di immutabilità:
```powershell

# with an immutability policy object
$policy = Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container

$policy = Set-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy

# with an account name or container name
$policy = Set-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -ImmutabilityPeriod 11 -Etag $policy.Etag -ExtendPolicy

# with an account object
$policy = Set-AzStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -ImmutabilityPeriod 12 -Etag `
    $policy.Etag -ExtendPolicy

# with a container object
$policy = Set-AzStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 13 -Etag $policy.Etag -ExtendPolicy
```

Rimuovere un criterio di immutabilità (aggiungere -Force per ignorare il prompt):
```powershell
# with an immutability policy object
$policy = Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
Remove-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy

# with an account name or container name
Remove-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
Remove-AzStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -Etag $policy.Etag

# with a container object
Remove-AzStorageContainerImmutabilityPolicy -Container $containerObject `
    -Etag $policy.Etag

```
