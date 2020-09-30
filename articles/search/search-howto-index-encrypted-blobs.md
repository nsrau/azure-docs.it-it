---
title: Eseguire ricerche nel contenuto crittografato di archiviazione BLOB di Azure
titleSuffix: Azure Cognitive Search
description: Informazioni su come indicizzare ed estrarre il testo da documenti crittografati nell'archiviazione BLOB di Azure con ricerca cognitiva di Azure.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 3330b4d5df366a5e886157e875f40d7e370c7442
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91543034"
---
# <a name="how-to-index-encrypted-blobs-using-blob-indexers-and-skillsets-in-azure-cognitive-search"></a>Come indicizzare i BLOB crittografati usando gli indicizzatori BLOB e skillsets in Azure ricerca cognitiva

Questo articolo illustra come usare [ricerca cognitiva di Azure](search-what-is-azure-search.md) per indicizzare i documenti che sono stati crittografati in precedenza nell' [Archivio BLOB di Azure](../storage/blobs/storage-blobs-introduction.md) usando [Azure Key Vault](../key-vault/general/overview.md). In genere, un indicizzatore non può estrarre contenuto dai file crittografati perché non ha accesso alla chiave di crittografia. Tuttavia, sfruttando l'abilità personalizzata [DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile) seguita da [DocumentExtractionSkill](cognitive-search-skill-document-extraction.md), è possibile fornire l'accesso controllato alla chiave per decrittografare i file e quindi fare in modo che il contenuto venga Estratto da essi. In questo modo si sblocca la possibilità di indicizzare i documenti senza doversi preoccupare dei dati archiviati in modo non crittografato.

Questa guida usa il post e le API REST di ricerca per eseguire le attività seguenti:

> [!div class="checklist"]
> * Inizia con documenti interi (testo non strutturato) come PDF, HTML, DOCX e PPTX nell'archivio BLOB di Azure che sono stati crittografati con Azure Key Vault.
> * Definire una pipeline che decrittografa i documenti ed estrae il testo da essi.
> * Definire un indice per archiviare l'output.
> * Eseguire la pipeline per creare e caricare l'indice.
> * Esplorare i risultati tramite un ricerca full-text e una sintassi di query avanzata.

Se non si ha una sottoscrizione di Azure, aprire un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Questo esempio presuppone che siano già stati caricati i file nell'archivio BLOB di Azure e che siano stati crittografati nel processo. Per informazioni su come caricare e crittografare i file inizialmente, vedere [questa esercitazione](../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md) per informazioni su come eseguire questa operazione.

+ [Archiviazione di Azure](https://azure.microsoft.com/services/storage/)
+ [Insieme di credenziali chiave Azure](https://azure.microsoft.com/services/key-vault/)
+ [Funzione di Azure](https://azure.microsoft.com/services/functions/)
+ [App desktop Postman](https://www.getpostman.com/)
+ [Creare](search-create-service-portal.md) o [trovare un servizio di ricerca esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> È possibile usare il servizio gratuito per questa guida. Un servizio di ricerca gratuito limita a tre indici, tre indicizzatori, tre origini dati e tre skillsets. Questa guida crea una di ognuna di esse. Prima di iniziare, assicurarsi che lo spazio nel servizio sia sufficiente per accettare le nuove risorse.

## <a name="1---create-services-and-collect-credentials"></a>1-creare servizi e raccogliere le credenziali

### <a name="set-up-the-custom-skill"></a>Configurare l'abilità personalizzata

Questo esempio usa il progetto [DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile) di esempio dal repository GitHub [Power Skills di ricerca di Azure](https://github.com/Azure-Samples/azure-search-power-skills) . In questa sezione si distribuirà la competenza a una funzione di Azure in modo che possa essere usata in un skillt. Uno script di distribuzione predefinito crea una risorsa di funzione di Azure denominata a partire da **psdbf-Function-app** e carica la competenza. Verrà richiesto di fornire una sottoscrizione e un gruppo di risorse. Assicurarsi di scegliere la stessa sottoscrizione in cui risiede l'istanza di Azure Key Vault.

A livello operativo, l'abilità DecryptBlobFile accetta l'URL e il token di firma di accesso condiviso per ogni BLOB come input e restituisce il file scaricato e decrittografato usando il contratto di riferimento file previsto da Azure ricerca cognitiva. Ricordare che DecryptBlobFile richiede la chiave di crittografia per eseguire la decrittografia. Come parte della configurazione, si creerà anche un criterio di accesso che concede l'accesso alla funzione DecryptBlobFile alla chiave di crittografia in Azure Key Vault.

1. Fare clic sul pulsante **Distribuisci in Azure** disponibile nella [pagina di destinazione di DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile#deployment), che consente di aprire il modello di gestione risorse fornito all'interno del portale di Azure.

1. Selezionare **la sottoscrizione in cui si trova l'istanza di Azure Key Vault** (questa guida non funzionerà se si seleziona una sottoscrizione diversa) e selezionare un gruppo di risorse esistente o crearne uno nuovo (se ne viene creato uno nuovo, sarà necessario anche selezionare un'area in cui eseguire la distribuzione).

1. Selezionare **Verifica + crea**, assicurarsi di accettare le condizioni e quindi selezionare **Crea** per distribuire la funzione di Azure.

    ![Modello ARM nel portale](media/indexing-encrypted-blob-files/arm-template.jpg "Modello ARM nel portale")

1. Attendere il completamento della distribuzione.

1. Passare all'istanza di Azure Key Vault nel portale. [Creare un criterio di accesso](../key-vault/general/assign-access-policy-portal.md) nel Azure Key Vault per concedere l'accesso alla chiave per l'abilità personalizzata.
 
    1. In **Impostazioni**selezionare **criteri di accesso**e quindi selezionare **Aggiungi criteri di accesso** .
     
       ![Aggiunta dei criteri di accesso all'insieme di credenziali delle credenziali](media/indexing-encrypted-blob-files/keyvault-access-policies.jpg "Criteri di accesso dell'insieme di credenziali delle credenziali")

    1. In **Configura da modello**selezionare **Azure Data Lake storage o archiviazione di Azure**.

    1. Per l'entità di protezione, selezionare l'istanza della funzione di Azure distribuita. È possibile eseguire la ricerca usando il prefisso di risorsa usato per crearlo nel passaggio 2, che ha un valore predefinito di prefisso **psdbf-Function-app**.

    1. Non selezionare alcun elemento per l'opzione applicazione autorizzata.
     
        ![Modello di criteri di accesso Add Vault](media/indexing-encrypted-blob-files/keyvault-add-access-policy.jpg "Modello di criteri di accesso dell'insieme di credenziali delle credenziali")

    1. Assicurarsi di fare clic su **Salva** nella pagina Criteri di accesso prima di uscire per aggiungere effettivamente i criteri di accesso.
     
         ![Criteri di accesso Save Vault](media/indexing-encrypted-blob-files/keyvault-save-access-policy.jpg "Salvare i criteri di accesso dell'insieme di credenziali delle credenziali")

1. Passare alla funzione **psdbf-Function-app** nel portale e prendere nota delle proprietà seguenti, che sarà necessario più avanti nella Guida:

    1. URL della funzione, disponibile in **Essentials** nella pagina principale della funzione.
    
        ![URL funzione](media/indexing-encrypted-blob-files/function-uri.jpg "Dove trovare l'URL della funzione di Azure")

    1. Il codice della chiave host, che è possibile trovare passando alle **chiavi dell'app**, facendo clic per visualizzare la chiave **predefinita** e copiando il valore.
     
        ![Codice chiave host funzione](media/indexing-encrypted-blob-files/function-host-key.jpg "Dove trovare il codice chiave host della funzione di Azure")

### <a name="cognitive-services"></a>Servizi cognitivi

L'esecuzione di arricchimento e competenze AI è supportata da servizi cognitivi, tra cui Analisi del testo e Visione artificiale per l'elaborazione del linguaggio naturale e dell'immagine. Se l'obiettivo è quello di completare un prototipo o un progetto effettivo, a questo punto è necessario effettuare il provisioning di Servizi cognitivi (nella stessa area di Ricerca cognitiva di Azure) in modo da poter collegare il servizio alle operazioni di indicizzazione.

Per questo esercizio è tuttavia possibile ignorare il provisioning delle risorse perché Ricerca cognitiva di Azure riesce a connettersi a Servizi cognitivi in background e offre 20 transazioni gratuite per ogni esecuzione dell'indicizzatore. Dopo l'elaborazione di 20 documenti, l'indicizzatore avrà esito negativo a meno che non venga collegata una chiave di servizi cognitivi al skillt. Per progetti di dimensioni maggiori, pianificare il provisioning di Servizi cognitivi al livello S0 con pagamento in base al consumo. Per altre informazioni, vedere [Collegare Servizi cognitivi](cognitive-search-attach-cognitive-services.md). Si noti che è necessaria una chiave di servizi cognitivi per eseguire un insieme di competenze con più di 20 documenti anche se nessuna delle competenze cognitive selezionate si connette ai servizi cognitivi, ad esempio con le competenze fornite se non viene aggiunta alcuna competenza.

### <a name="azure-cognitive-search"></a>Ricerca cognitiva di Azure

L'ultimo componente è Azure ricerca cognitiva, che è possibile [creare nel portale](search-create-service-portal.md). Per completare questa guida, è possibile usare il livello gratuito. 

Come per la funzione di Azure, raccogliere la chiave amministratore. Più avanti, quando si inizierà a strutturare le richieste, sarà necessario specificare l'endpoint e la chiave API di amministrazione usati per autenticare ogni richiesta.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Ottenere un URL e una chiave API di amministrazione per Ricerca cognitiva di Azure

1. [Accedere al portale di Azure](https://portal.azure.com/) e ottenere il nome del servizio di ricerca nella relativa pagina **Panoramica**. È possibile verificare il nome del servizio esaminando l'URL dell'endpoint. Se l'URL dell'endpoint fosse `https://mydemo.search.windows.net`, il nome del servizio sarebbe `mydemo`.

2. In **Impostazioni** > **Chiavi** ottenere una chiave amministratore per diritti completi sul servizio. Sono disponibili due chiavi amministratore interscambiabili, fornite per continuità aziendale nel caso in cui sia necessario eseguire il rollover di una di esse. È possibile usare la chiave primaria o secondaria nelle richieste per l'aggiunta, la modifica e l'eliminazione di oggetti.

   ![Ottenere il nome del servizio e le chiavi amministratore e di query](media/search-get-started-nodejs/service-name-and-keys.png)

Nell'intestazione di ogni richiesta inviata al servizio è necessario specificare una chiave API (api-key). La presenza di una chiave valida stabilisce una relazione di trust, in base a singole richieste, tra l'applicazione che invia la richiesta e il servizio che la gestisce.

## <a name="2---set-up-postman"></a>2 - Configurare Postman

Installare e configurare Postman.

### <a name="download-and-install-postman"></a>Scaricare e installare Postman

1. Scaricare il [codice sorgente della raccolta Postman](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json).
1. Selezionare **File** > **Importa** per importare il codice sorgente in Postman.
1. Selezionare la scheda **Collections** (Raccolte), quindi selezionare il pulsante **...** (puntini di sospensione).
1. Selezionare **Modifica**. 
   
   ![App Postman che mostra le opzioni di spostamento](media/indexing-encrypted-blob-files/postman-edit-menu.jpg "Passare al menu Modifica in Postman")
1. Nella finestra di dialogo **Edit** (Modifica) selezionare la scheda **Variables** (Variabili). 

Nella scheda **Variables** (Variabili) è possibile aggiungere valori che Postman recupera ogni volta che rileva una variabile specifica racchiusa tra doppie parentesi graffe. Ad esempio, Postman sostituisce il simbolo `{{admin-key}}` con il valore corrente impostato per `admin-key`. Postman effettua questa sostituzione negli URL, nelle intestazioni, nel corpo della richiesta e così via. 

Per ottenere il valore per `admin-key` , usare la chiave API di amministrazione di Azure ricerca cognitiva annotata in precedenza. Impostare sul `search-service-name` nome del servizio ricerca cognitiva di Azure in uso. Impostare `storage-connection-string` usando il valore nella scheda **chiavi di accesso** dell'account di archiviazione e impostare sul `storage-container-name` nome del contenitore BLOB nell'account di archiviazione in cui sono archiviati i file crittografati. Impostare `function-uri` sull'URL della funzione di Azure annotato in precedenza e impostare sul `function-code` codice chiave host della funzione di Azure annotato in precedenza. È possibile lasciare i valori predefiniti per le altre impostazioni.

![Scheda variabili app Postman](media/indexing-encrypted-blob-files/postman-variables-window.jpg "Finestra delle variabili di Postman")


| Variabile    | Informazioni sulla collocazione |
|-------------|-----------------|
| `admin-key` | Nella pagina **Chiavi** del servizio Ricerca cognitiva di Azure.  |
| `search-service-name` | Nome del servizio Ricerca cognitiva di Azure. L'URL è `https://{{search-service-name}}.search.windows.net`. | 
| `storage-connection-string` | Nell'account di archiviazione, selezionare la scheda **Chiavi di accesso**, selezionare **key1** > **Stringa di connessione**. | 
| `storage-container-name` | Nome del contenitore BLOB con i file crittografati da indicizzare. | 
| `function-uri` |  Nella funzione di Azure in **Essentials** nella pagina principale. | 
| `function-code` | Nella funzione di Azure passare a **chiavi app**, fare clic per visualizzare la chiave **predefinita** e copiare il valore. | 
| `api-version` | Lasciare **2020-06-30**. |
| `datasource-name` | Lasciare come **Encrypted-Blobs-DS**. | 
| `index-name` | Lascia come **Encrypted-Blobs-idx**. | 
| `skillset-name` | Lascia come **Encrypted-Blobs-SS**. | 
| `indexer-name` | Lasciare come **Encrypted-Blobs-IXR**. | 

### <a name="review-the-request-collection-in-postman"></a>Esaminare la raccolta di richieste in Postman

Quando si esegue questa guida, è necessario emettere quattro richieste HTTP: 

- **Richiesta PUT per creare l'indice**: Questo indice contiene i dati usati e restituiti da Ricerca cognitiva di Azure.
- **Inviare una richiesta per creare l'origine dati**: questa origine dati connette il servizio ricerca cognitiva di Azure all'account di archiviazione e quindi i file BLOB crittografati. 
- **Put request to create the skills**: il skillt specifica la definizione di abilità personalizzata per la funzione di Azure che determinerà i dati del file BLOB e un [DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) per estrarre il testo da ogni documento dopo che è stato decrittografato.
- **Richiesta PUT per creare l'indicizzatore**: L'esecuzione dell'indicizzatore legge i dati, applica il set di competenze e archivia i risultati. È necessario eseguire questa richiesta per ultima.

Il [codice sorgente](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json) contiene una raccolta di post che include le quattro richieste, oltre ad alcune richieste di completamento utili. Per eseguire le richieste, in subordinate selezionare la scheda relativa alle richieste e selezionare **Send** per ciascuna di esse.

## <a name="3---monitor-indexing"></a>3-monitorare l'indicizzazione

L'indicizzazione e l'arricchimento iniziano non appena si invia la richiesta Crea l'indicizzatore. A seconda del numero di documenti presenti nell'account di archiviazione, l'indicizzazione può richiedere qualche minuto. Per sapere se l'indicizzatore è ancora in esecuzione, usare la richiesta **Get Indexer status** fornita come parte della raccolta dei post e controllare la risposta per sapere se l'indicizzatore è in esecuzione oppure per visualizzare informazioni sugli errori e sugli avvisi.  

Se si usa il livello gratuito, è previsto il messaggio seguente: `"Could not extract content or metadata from your document. Truncated extracted text to '32768' characters"` . Questo messaggio viene visualizzato perché l'indicizzazione BLOB sul livello gratuito ha un [limite 32K per l'estrazione dei caratteri](search-limits-quotas-capacity.md#indexer-limits). Con livelli superiori questo messaggio non verrà visualizzato per il set di dati. 

## <a name="4---search"></a>4-ricerca

Al termine dell'esecuzione dell'indicizzatore, è possibile eseguire alcune query per verificare che i dati siano stati decrittografati e indicizzati correttamente. Passare al servizio ricerca cognitiva di Azure nel portale e usare [Esplora ricerche](search-explorer.md) per eseguire query sui dati indicizzati.

## <a name="next-steps"></a>Passaggi successivi

Ora che i file crittografati sono stati indicizzati correttamente, è possibile eseguire [l'iterazione in questa pipeline aggiungendo più competenze cognitive](cognitive-search-defining-skillset.md). Ciò consentirà di arricchire e ottenere informazioni aggiuntive sui dati.

Se si lavora con dati con crittografia doppia, è possibile analizzare le funzionalità di crittografia dell'indice disponibili in Azure ricerca cognitiva. Sebbene l'indicizzatore necessiti di dati decrittografati a scopo di indicizzazione, una volta che l'indice esiste, può essere crittografato con una chiave gestita dal cliente. In questo modo i dati vengono sempre crittografati quando sono inattivi. Per altre informazioni, vedere [configurare chiavi gestite dal cliente per la crittografia dei dati in Azure ricerca cognitiva](search-security-manage-encryption-keys.md).