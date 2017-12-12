---
title: Livelli di archiviazione di Azure ad accesso frequente, ad accesso sporadico e archivio per i BLOB | Microsoft Docs
description: Livelli di archiviazione ad accesso frequente, ad accesso sporadico e archivio per account di archiviazione BLOB di Azure.
services: storage
documentationcenter: 
author: michaelhauss
manager: vamshik
editor: tysonn
ms.assetid: eb33ed4f-1b17-4fd6-82e2-8d5372800eef
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/05/2017
ms.author: mihauss
ms.openlocfilehash: 501fc59efb8bacf58fea2825752d3a33c6ea5963
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2017
---
# <a name="azure-blob-storage-hot-cool-and-archive-preview-storage-tiers"></a>Archivio BLOB di Azure: livelli di archiviazione ad accesso frequente, ad accesso sporadico e archivio (anteprima)

## <a name="overview"></a>Panoramica

Archiviazione di Azure offre tre livelli di archiviazione per l'archivio di oggetti BLOB, per consentire di archiviare i dati nel modo economicamente più conveniente in base alla modalità d'uso. Il **livello di archiviazione ad accesso frequente** di Azure è ottimizzato per l'archiviazione di dati a cui si accede di frequente. Il **livello di archiviazione ad accesso sporadico** di Azure è ottimizzato per l'archiviazione di dati a cui si accede poco frequentemente e che vengono archiviati per almeno 30 giorni. Il **livello di archiviazione archivio** (anteprima) di Azure è ottimizzato per l'archiviazione di dati a cui si accede raramente e che vengono archiviati per almeno 180 giorni con requisiti di latenza flessibili, nell'ordine di ore. Il livello di archiviazione archivio è disponibile solo a livello di BLOB e non a livello di account di archiviazione. I dati nel livello di archiviazione ad accesso sporadico possono tollerare una disponibilità leggermente più bassa, ma richiedono ugualmente una durabilità elevata e caratteristiche di velocità effettiva e tempo di accesso simili a quelle dei dati ad accesso frequente. Per i dati ad accesso sporadico, contratti di servizio con una disponibilità leggermente più bassa e costi di accesso più alti rispetto a quelli per i dati ad accesso frequente sono compromessi accettabili in cambio di costi di archiviazione più bassi. L'archiviazione archivio è offline e offre i costi di archiviazione più bassi, ma anche i costi di accesso più alti.

La quantità di dati archiviati nel cloud è attualmente in crescita esponenziale. Per gestire i costi in base alle esigenze di archiviazione crescenti, può essere utile organizzare i dati in base ad attributi quali la frequenza di accesso e il periodo di conservazione pianificato, in modo da ottimizzare i costi. I dati archiviati nel cloud possono essere diversi dal punto di vista della generazione, dell'elaborazione e dell'accesso nel corso del tempo. Alcuni dati presentano accessi attivi e modifiche continue nel corso della rispettiva durata. Alcuni dati presentano un accesso frequente nelle fasi iniziali e l'accesso si riduce drasticamente con il passare del tempo. Alcuni dati rimangono inattivi sul cloud e gli utenti vi accedono raramente, se non mai, dopo l'archiviazione.

Tutti questi scenari di accesso ai dati usufruiscono di un livello di archiviazione diverso, ottimizzato per un particolare modello di accesso. Con i livelli di archiviazione ad accesso frequente, ad accesso sporadico e archivio, l'archiviazione BLOB di Azure soddisfa l'esigenza di avere livelli di archiviazione differenziati con modelli di determinazione prezzi distinti.

## <a name="blob-storage-accounts"></a>Account di archiviazione BLOB

**Account di archiviazione BLOB** sono account di archiviazione specializzati per l'archiviazione dei dati non strutturati come BLOB (oggetti) in Archiviazione di Azure. Gli account di archiviazione BLOB permettono ora di scegliere tra livelli di archiviazione ad accesso frequente e sporadico a livello di account oppure tra livelli di archiviazione ad accesso frequente, ad accesso sporadico e archivio a livello di BLOB, a seconda dei modelli di accesso. Archiviare i dati a cui si accede frequentemente, poco frequentemente e raramente rispettivamente nei livelli di archiviazione ad accesso frequente, ad accesso sporadico e archivio. Gli account di archiviazione BLOB sono simili agli account di archiviazione di uso generico esistenti e condividono tutte le straordinarie funzionalità di durabilità, disponibilità, scalabilità e prestazioni già usate, inclusa la coerenza API al 100% per i BLOB in blocchi e i BLOB di aggiunta.

> [!NOTE]
> Gli account di archiviazione BLOB supportano solo i BLOB in blocchi e i BLOB di aggiunta, non i BLOB di pagine.

Gli account di archiviazione BLOB espongono a livello di account l'attributo **Livello di accesso**, che consente di specificare l'account di archiviazione predefinito come **Frequente** o **Sporadico**. Il livello predefinito per l'account di archiviazione viene applicato a tutti i BLOB per cui non è impostato un livello esplicito a livello di BLOB. Se il modello di utilizzo dei dati cambia, è anche possibile passare da uno di questi livelli di archiviazione a un altro in qualsiasi momento. Il **livello archivio** (anteprima) è applicabile unicamente a livello di BLOB.

> [!NOTE]
> La modifica del livello di archiviazione può comportare costi aggiuntivi. Per altri dettagli, vedere la sezione [Prezzi e fatturazione](#pricing-and-billing).

### <a name="hot-access-tier"></a>Livello di accesso frequente

L'archiviazione ad accesso frequente ha costi di archiviazione più alti rispetto a quella ad accesso sporadico e al livello archivio, ma costi di accesso più bassi. Gli scenari di utilizzo di esempio per il livello di archiviazione ad accesso frequente includono:

* Dati di uso attivo e o di cui è previsto l'accesso (lettura o scrittura) di frequente.
* Dati di gestione temporanea per l'elaborazione e l'eventuale migrazione al livello di archiviazione ad accesso sporadico.

### <a name="cool-access-tier"></a>Livello di accesso sporadico

Il livello di archiviazione ad accesso sporadico ha costi di archiviazione più bassi e costi di accesso più alti rispetto all'archiviazione ad accesso frequente. Questo livello è destinato ai dati che rimangono nel livello ad accesso sporadico per almeno 30 giorni. Gli scenari di utilizzo di esempio per il livello di archiviazione ad accesso sporadico includono:

* Set di dati di backup e ripristino di emergenza a breve termine.
* Contenuto multimediale meno recente ormai visualizzato non spesso, ma che deve essere immediatamente disponibile quando vi si accede.
* Set di dati di grandi dimensioni da archiviare a costi contenuti mentre vengono raccolti altri dati per l'elaborazione successiva, *ad esempio*, archiviazione a lungo termine di dati scientifici, dati di telemetria non elaborati di un impianto di produzione.

### <a name="archive-access-tier-preview"></a>Livello di accesso archivio (anteprima)

Il livello di archiviazione archivio ha costi di archiviazione più bassi e costi di recupero dati più alti rispetto all'archiviazione ad accesso sporadico e ad accesso frequente. Questo livello è destinato ai dati che possono tollerare alcune ore di latenza di recupero e rimarranno nel livello archivio per almeno 180 giorni.

Quando un BLOB si trova nel livello di archiviazione archivio, è offline e non può essere letto (eccetto i metadati che sono online e disponibili), copiato, sovrascritto o modificato. o creare snapshot del BLOB. È tuttavia possibile usare le operazioni esistenti per eliminarli, elencarli, ottenere proprietà o metadati dei BLOB oppure modificare il livello dei BLOB.

#### <a name="blob-rehydration"></a>Riattivazione di BLOB
Per leggere i dati nel livello archivio, è prima necessario modificare il livello di accesso del BLOB in frequente o sporadico. Questo processo è noto come riattivazione e può richiedere fino a 15 ore per BLOB di dimensioni inferiori a 50 GB. Il tempo ulteriore necessario per i BLOB di dimensioni maggiori varia in base al limite di velocità effettiva del BLOB.

Durante la riattivazione è possibile controllare lo stato di archiviazione del BLOB per assicurarsi che il livello sia stato modificato. Lo stato può essere "rehydrate-pending-to-hot" o "rehydrate-pending-to-cool" a seconda del livello di destinazione. Al termine, la proprietà relativa allo stato di archiviazione del BLOB viene rimossa e quella relativa al livello di accesso indica un livello frequente o sporadico.  

Gli scenari di utilizzo di esempio per il livello di archiviazione archivio includono:

* Set di dati di backup, archiviazione e ripristino di emergenza a lungo termine
* Dati originali (non elaborati) che devono essere conservati, anche dopo che sono stati elaborati in un formato utilizzabile finale, *ad esempio*, file multimediali non elaborati dopo la transcodifica in altri formati.
* Dati di conformità e di archiviazione che devono essere archiviati per un lungo periodo e a cui non si accede quasi mai, *ad esempio*, filmati di videocamere di sicurezza, vecchie radiografie/risonanze magnetiche per le organizzazioni sanitarie, registrazioni audio e trascrizioni di chiamate di clienti per i servizi finanziari.

### <a name="recommendations"></a>Raccomandazioni

Per informazioni sugli account di archiviazione, vedere [Informazioni sugli account di archiviazione di Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) .

Per le applicazioni che richiedono solo l'archivio BLOB in blocchi o di aggiunta, è consigliabile usare gli account di archiviazione BLOB, per sfruttare il modello di determinazione prezzi differenziato dell'archiviazione a livelli. Questo tuttavia potrebbe non essere possibile in determinate circostanze in cui è meglio usare gli account di archiviazione di uso generico, ad esempio:

* È necessario usare tabelle, code o file e si vogliono archiviare i BLOB nello stesso account di archiviazione. L'unico vantaggio tecnico derivante dall'archiviarli nello stesso account è quello di avere le stesse chiavi condivise.

* È ancora necessario usare il modello di distribuzione classica. Gli account di archiviazione BLOB sono disponibili solo con il modello di distribuzione Azure Resource Manager.

* È necessario usare BLOB di pagine. Gli account di archiviazione BLOB non supportano i BLOB di pagine. A meno che non vi siano esigenze specifiche per usare i BLOB di pagine, è consigliabile usare i BLOB in blocchi.

* Si usa una versione dell' [API REST dei servizi di archiviazione](https://msdn.microsoft.com/library/azure/dd894041.aspx) precedente alla 2014-02-14 o una libreria client con una versione precedente alla 4.x e non è possibile aggiornare l'applicazione.

> [!NOTE]
> Gli account di archiviazione BLOB sono attualmente supportati in tutte le aree di Azure.


## <a name="blob-level-tiering-feature-preview"></a>Funzionalità di suddivisione in livelli a livello di BLOB (anteprima)

L'organizzazione a livello di BLOB consente di modificare il livello dei dati a livello di oggetto con una sola operazione, [Imposta livello BLOB](/rest/api/storageservices/set-blob-tier). È possibile modificare facilmente il livello di accesso di un BLOB tra frequente, sporadico o archivio, in base alle variazioni dei modelli di utilizzo, senza dover spostare i dati da un account a un altro. Tutte le modifiche ai livelli sono immediate, ad eccezione della riattivazione di un BLOB dal livello archivio. L'ora dell'ultima modifica a livello di BLOB viene esposta tramite l'attributo **Access Tier Change Time** (Ora modifica livello di accesso) nelle proprietà del BLOB. Se un BLOB si trova nel livello archivio non può essere sovrascritto e quindi in questo scenario non è consentito caricare lo stesso BLOB. È possibile sovrascrivere un BLOB nei livelli ad accesso frequente e sporadico e in questo caso il nuovo BLOB eredita il livello del BLOB precedente che è stato sovrascritto.

In tutti e tre i livelli di archiviazione i BLOB possono coesistere nello stesso account. I BLOB a cui non viene assegnato un livello in modo esplicito lo ereditano dall'impostazione del livello di accesso dell'account. Se il livello di accesso viene derivato dall'account, l'attributo **Access Tier Inferred** (Livello di accesso derivato) è impostato su "true" e l'attributo **Livello di accesso** del BLOB corrisponde al livello dell'account. Nel portale di Azure la proprietà Access Tier Inferred (Livello di accesso derivato) è visualizzata insieme al livello di accesso del BLOB, ad esempio Hot (inferred) (Frequente - derivato) o Cool (inferred) (Sporadico - derivato).

> [!NOTE]
> Il livello di archiviazione archivio e l'organizzazione a livello di BLOB supportano solo BLOB in blocchi. Non è inoltre possibile modificare il livello di un BLOB in blocchi con snapshot.

### <a name="blob-level-tiering-billing"></a>Fatturazione per l'organizzazione a livello di BLOB

Quando un BLOB viene spostato in un livello ad accesso più sporadico (Frequente -> Sporadico, Frequente -> Archivio o Sporadico -> Archivio), l'operazione viene fatturata come operazione di scrittura nel livello di destinazione e vengono applicati i costi per le operazioni di scrittura (per decine di migliaia) e la scrittura dati (per GB). Se un BLOB viene spostato in un livello ad accesso più frequente (Archivio -> Sporadico, Archivio -> Frequente o Sporadico -> Frequente), l'operazione viene fatturata come un'operazione di lettura nel livello di origine e vengono applicati i costi per le operazioni di lettura (per decine di migliaia) e il recupero dati (per GB).

Per usare queste funzionalità in anteprima, seguire le istruzioni riportate nell'[annuncio sul blog di Archiviazione di Azure e suddivisione in livelli a livello di BLOB](https://azure.microsoft.com/blog/announcing-the-public-preview-of-azure-archive-blob-storage-and-blob-level-tiering).

Di seguito sono elencate alcune restrizioni applicabili alla fase di anteprima della suddivisione in livelli a livello di BLOB:

* Il livello di archiviazione archivio è supportato solo dai nuovi account di archiviazione BLOB creati nell'area Stati Uniti orientali 2, Stati Uniti orientali o Stati Uniti occidentali dopo la registrazione dell'anteprima.

* La suddivisione in livelli a livello di BLOB è supportata solo nei nuovi account di archiviazione BLOB creati in aree pubbliche dopo la registrazione dell'anteprima.

* La suddivisione in livelli a livello di BLOB e il livello archivio sono supportati unicamente nell'[archiviazione con ridondanza locale] (../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#locally-redundant-storage). In futuro verrà aggiunto il supporto per l'[archiviazione con ridondanza geografica](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#geo-redundant-storage) e l'[archiviazione con ridondanza geografica e accesso in lettura](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#read-access-geo-redundant-storage).

* Non è possibile modificare il livello di un BLOB con snapshot.

* Non è possibile copiare o creare uno snapshot di un BLOB nel livello archivio.

## <a name="comparison-of-the-storage-tiers"></a>Confronto tra i livelli di archiviazione

La tabella seguente illustra un confronto tra i livelli di archiviazione ad accesso frequente e ad accesso sporadico. Il livello archivio a livello di BLOB è in anteprima, quindi non è coperto da contratti di servizio.

| | **Livello di archiviazione ad accesso frequente** | **Livello di archiviazione ad accesso sporadico** | **Livello di archiviazione archivio**
| ---- | ----- | ----- | ----- |
| **Disponibilità** | 99,9% | 99% | N/D |
| **Disponibilità** <br> **(letture RA-GRS)**| 99,99% | 99,9% | N/D |
| **Costi di utilizzo** | Costi di archiviazione più elevati e costi di accesso e transazione più bassi | Costi di archiviazione più bassi e costi di accesso e transazione più elevati | Costi di archiviazione più bassi e costi di accesso e transazione più alti |
| **Dimensioni minime oggetti** | N/D | N/D | N/D |
| **Durata archiviazione minima** | N/D | N/D | 180 giorni
| **Latency** <br> **(tempo per il primo byte)** | millisecondi | millisecondi | Meno di 15 ore
| **Obiettivi di scalabilità e prestazioni** | Uguali a quelli degli account di archiviazione di uso generico | Uguali a quelli degli account di archiviazione di uso generico | Uguali a quelli degli account di archiviazione di uso generico |

> [!NOTE]
> Gli account di archiviazione BLOB supportano gli stessi obiettivi di prestazioni e scalabilità degli account di archiviazione di uso generico. Per ulteriori informazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) .


## <a name="pricing-and-billing"></a>Prezzi e fatturazione
Gli account di archiviazione BLOB usano un modello di determinazione prezzi per l'archiviazione BLOB basato sul livello di ogni BLOB. Quando si usa un account di archiviazione BLOB, tenere conto delle considerazioni seguenti relative alla fatturazione:

* **Costi di archiviazione**: oltre alla quantità di dati archiviati, il costo di archiviazione dei dati varia a seconda del livello di archiviazione. Il costo per gigabyte diminuisce passando a un livello ad accesso più sporadico.

* **Costi di accesso ai dati**: i costi di accesso ai dati aumentano passando a un livello ad accesso più sporadico. Per i dati nei livelli di archiviazione ad accesso sporadico e archivio vengono addebitati i costi per l'accesso ai dati per gigabyte per le operazioni di lettura.

* **Costi delle transazioni**: sono previsti costi per transazione per tutti i livelli e tali costi aumentano passando a un livello ad accesso più sporadico.

* **Costi di trasferimento dati con la replica geografica**: si applicano solo agli account per cui è configurata la replica geografica, incluse l'archiviazione con ridondanza geografica e RA-GRS. Il trasferimento dati con la replica geografica comporta un addebito per gigabyte.

* **Costi di trasferimento dati in uscita**: i trasferimenti dati in uscita (dati che vengono trasferiti al di fuori di un'area di Azure) vengono fatturati in base all'utilizzo di larghezza di banda per singolo gigabyte, come per gli account di archiviazione di uso generico.

* **Modifica del livello di archiviazione**: la modifica del livello di archiviazione da sporadico a frequente comporta un addebito corrispondente a quello per la lettura di tutti i dati esistenti nell'account di archiviazione. La modifica del livello di archiviazione dell'account da frequente a sporadico comporta tuttavia un addebito corrispondente a quello per la scrittura di tutti i dati nel livello ad accesso sporadico.

> [!NOTE]
> Per altri dettagli sul modello di determinazione prezzi per gli account di archiviazione BLOB, vedere la pagina [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/). Per altri dettagli sugli addebiti per i trasferimenti dati in uscita, vedere la pagina [Dettagli prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="quick-start"></a>Avvio rapido

Questa sezione presenta gli scenari seguenti usando il portale di Azure:

* Come creare un account di archiviazione BLOB.
* Come gestire un account di archiviazione BLOB.

Negli esempi seguenti non è possibile impostare il livello di accesso su archivio perché tale impostazione si applica all'intero account di archiviazione. Il livello archivio può essere impostato solo per un BLOB specifico.

### <a name="create-a-blob-storage-account-using-the-azure-portal"></a>Creare un account di archiviazione BLOB usando il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel menu Hub selezionare **Nuovo** > **Dati e archiviazione** > **Account di archiviazione**.

3. Immettere un nome per l'account di archiviazione.

    Questo nome deve essere globalmente univoco. Viene usato come parte dell'URL usato per accedere agli oggetti nell'account di archiviazione.  

4. Selezionare **Resource Manager** come modello di distribuzione.

    L'archiviazione a livelli può essere usata solo con gli account di archiviazione di Resource Manager, che è il modello di distribuzione consigliato per le nuove risorse. Per altre informazioni, vedere [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).  

5. Nell'elenco a discesa Tipologia account selezionare **Archivio BLOB**.

    Qui è possibile selezionare il tipo di account di archiviazione. L'archiviazione a livelli non è disponibile nell'archiviazione per utilizzo generico, ma solo nel tipo di account di archiviazione BLOB.     

    Quando si seleziona questa opzione, il livello di prestazioni viene impostato su Standard. L'archiviazione a livelli non è disponibile con il livello di prestazioni Premium.

6. Selezionare l'opzione di replica per l'account di archiviazione: **Archiviazione con ridondanza locale**, **Archiviazione con ridondanza geografica** o **Archiviazione con ridondanza geografica e accesso in lettura**. L'opzione predefinita è **Archiviazione con ridondanza geografica e accesso in lettura**.

    Le opzioni disponibili sono archiviazione con ridondanza locale, archiviazione con ridondanza geografica (due aree) e archiviazione con ridondanza geografica e accesso in lettura (due aree con accesso in lettura alla seconda).

    Per altre informazioni sulle opzioni di replica di Archiviazione di Azure, vedere [Replica di Archiviazione di Azure](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

7. Selezionare il livello di archiviazione corretto per le proprie esigenze: impostare il **livello di accesso** su **sporadico** o **frequente**. Il livello predefinito è **Frequente**.

8. Selezionare la sottoscrizione in cui creare il nuovo account di archiviazione.

9. Specificare un nuovo gruppo di risorse o selezionarne uno esistente. Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

10. Selezionare l'area per l'account di archiviazione.

11. Fare clic su **Crea** per creare l'account di archiviazione.

### <a name="change-the-storage-tier-of-a-blob-storage-account-using-the-azure-portal"></a>Modificare il livello di archiviazione di un account di archiviazione BLOB usando il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Per passare all'account di archiviazione, selezionare Tutte le risorse, quindi selezionare l'account di archiviazione.

3. Nel pannello Impostazioni fare clic su **Configurazione** per visualizzare e/o modificare la configurazione dell'account.

4. Selezionare il livello di archiviazione corretto per le proprie esigenze: impostare il **livello di accesso** su **sporadico** o **frequente**.

5. Fare clic su Salva nella parte superiore del pannello.

### <a name="change-the-storage-tier-of-a-blob-using-the-azure-portal"></a>Modificare il livello di archiviazione di un BLOB usando il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Per passare al BLOB nell'account di archiviazione, selezionare Tutte le risorse, selezionare l'account di archiviazione, selezionare il contenitore e quindi selezionare il BLOB.

3. Nel pannello delle proprietà del BLOB fare clic sul menu a discesa **Livello di accesso** per selezionare il livello di archiviazione **Frequente**, **Sporadico** o **Archivio**.

5. Fare clic su Salva nella parte superiore del pannello.

> [!NOTE]
> La modifica del livello di archiviazione può comportare costi aggiuntivi. Per altri dettagli, vedere la sezione [Prezzi e fatturazione](#pricing-and-billing).


## <a name="evaluating-and-migrating-to-blob-storage-accounts"></a>Valutazione e migrazione agli account di archiviazione BLOB
Questa sezione descrive come eseguire una transizione senza problemi all'uso di account di archiviazione BLOB. Esistono due scenari utente:

* È disponibile un account di archiviazione per utilizzo generico esistente e si vuole valutare una modifica per passare a un account di archiviazione BLOB con il livello di archiviazione corretto.
* Si è deciso di usare un account di archiviazione BLOB o ne già disponibile uno e si vuole valutare se è opportuno usare il livello di archiviazione ad accesso frequente o sporadico.

In entrambi i casi la prima cosa da fare è stimare il costo di archiviazione e accesso ai dati archiviati in un account di archiviazione BLOB e confrontarlo con i costi attuali.

## <a name="evaluating-blob-storage-account-tiers"></a>Valutazione dei livelli di account di archiviazione BLOB

Per stimare il costo di archiviazione e accesso ai dati archiviati in un account di archiviazione BLOB, è necessario valutare il modello di utilizzo esistente o simulare il modello di utilizzo previsto. In genere, è consigliabile conoscere quanto segue:

* Utilizzo dell'archiviazione: quanti dati vengono archiviati e come cambia questo valore ogni mese?

* Modelli di accesso alle risorse di archiviazione: quanti dati vengono letti e scritti nell'account, inclusi quelli nuovi? Quante transazioni vengono usate per accedere ai dati e che di che tipi di transazioni si tratta?

## <a name="monitoring-existing-storage-accounts"></a>Monitoraggio degli account di archiviazione esistenti

Per monitorare gli account di archiviazione esistenti e raccoglierne i dati, è possibile usare Analisi archiviazione di Azure, che esegue la registrazione e fornisce i dati delle metriche per un account di archiviazione. Analisi archiviazione può archiviare metriche che includono statistiche sulle transazioni aggregate e dati sulla capacità relativi alle richieste al servizio di archiviazione BLOB, sia per gli account di archiviazione per utilizzo generico che per gli account di archiviazione BLOB. I dati vengono archiviati in tabelle note nello stesso account di archiviazione.

Per altre informazioni, vedere [Informazioni sulle metriche di Analisi archiviazione](https://msdn.microsoft.com/library/azure/hh343258.aspx) e [Schema di tabella della metrica di Analisi archiviazione](https://msdn.microsoft.com/library/azure/hh343264.aspx)

> [!NOTE]
> Gli account di archiviazione BLOB espongono l'endpoint di servizio tabelle solo per l'archiviazione e l'accesso ai dati di metrica per tale account.

Per monitorare l'utilizzo della risorsa di archiviazione per il servizio di archiviazione BLOB, è necessario abilitare la metrica della capacità.
Con questa impostazione abilitata, i dati sulla capacità vengono registrati ogni giorno per il servizio BLOB di un account di archiviazione e registrati come una voce di tabella che viene scritta nella tabella *$MetricsCapacityBlob* nello stesso account di archiviazione.

Per monitorare il modello di accesso ai dati per il servizio di archiviazione BLOB, è necessario abilitare la metrica oraria delle transazioni a livello di API. Con questa impostazione attivata le transazioni vengono aggregate ogni ora per ogni API e registrate come una voce della tabella che viene scritta nella tabella *$MetricsHourPrimaryTransactionsBlob* nello stesso account di archiviazione. La tabella *$MetricsHourSecondaryTransactionsBlob* registra le transazioni nell'endpoint secondario quando si usano account di archiviazione con ridondanza geografica e accesso in lettura.

> [!NOTE]
> Se è disponibile un account di archiviazione per utilizzo generico in cui sono archiviati sia BLOB di pagine e dischi di macchina virtuale insieme a dati di BLOB in blocchi e di aggiunta, questo processo di stima non è applicabile. Questo avviene perché non c'è modo di distinguere la metrica della capacità da quella delle transazioni in base al tipo di BLOB solo per i BLOB in blocchi e quelli di aggiunta di cui può essere eseguita la migrazione a un account di archiviazione BLOB.

Per ottenere una buona approssimazione del modello di accesso e di utilizzo dei dati, è consigliabile scegliere un periodo di conservazione per le metriche che rappresenti l'utilizzo regolare ed estrapolarne i dati. Una possibilità consiste nel conservare i dati delle metriche per sette giorni e raccoglierli ogni settimana per analizzarli alla fine del mese. Un'altra possibilità è conservare i dati di metrica per gli ultimi 30 giorni e raccogliere e analizzare i dati alla fine del periodo di 30 giorni.

Per informazioni dettagliate su come abilitare, raccogliere e visualizzare i dati delle metriche, vedere [Abilitazione delle metriche di Archiviazione di Azure e visualizzazione dei dati delle metriche](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Anche l'archiviazione, l'accesso e il download dei dati di analisi vengono addebitati come avviene per i dati utente normali.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Uso della metrica di utilizzo per stimare i costi

### <a name="storage-costs"></a>Costi di archiviazione

L'ultima voce nella tabella della metrica della capacità *$MetricsCapacityBlob* con la chiave di riga *'data'* mostra la capacità di archiviazione utilizzata dai dati utente. L'ultima voce nella tabella della metrica della capacità *$MetricsCapacityBlob* con la chiave di riga *'analytics'* mostra la capacità di archiviazione utilizzata dai log di analisi.

Questa capacità totale utilizzata sia dai dati utente che dai log di analisi, se abilitati, può quindi essere usata per stimare i costi di archiviazione dei dati nell'account di archiviazione. Lo stesso metodo può essere usato anche per la stima dei costi di archiviazione per i BLOB in blocchi e di aggiunta negli account di archiviazione per utilizzo generico.

### <a name="transaction-costs"></a>Costi di transazione

La somma di *'TotalBillableRequests'*in tutte le voci relative a un'API nella tabella della metrica delle transazioni indica il numero totale di transazioni per quell'API specifica. *Ad esempio*, il numero totale di transazioni *'GetBlob'* in un dato periodo può essere calcolato dalla somma delle richieste fatturabili totali per tutte le voci con la chiave di riga *'user;GetBlob'*.

Per stimare i costi delle transazioni per gli account di archiviazione BLOB, è necessario suddividere le transazioni in tre gruppi, perché i prezzi vengono determinati in modo diverso.

* Transazioni di scrittura, ad esempio *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'* e *'CopyBlob'*.
* Transazioni di eliminazione, ad esempio *'DeleteBlob'* e *'DeleteContainer'*.
* Tutte le altre transazioni.

Per stimare i costi delle transazioni per gli account di archiviazione per utilizzo generico, è necessario aggregare tutte le transazioni indipendentemente dall'operazione o dall'API.

### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Costi di trasferimento dati con replica geografica e di accesso ai dati

Mentre l'analisi dell'archiviazione non fornisce la quantità di dati in lettura e scrittura in un account di archiviazione, è possibile effettuare una stima approssimativa esaminando la tabella della metrica delle transazioni. La somma di *'TotalIngress'* in tutte le voci relative a un'API nella tabella della metrica delle transazione indica la quantità totale di dati in ingresso, espressa in byte, per quell'API specifica. In modo analogo,, la somma di *'TotalEgress'* indica la quantità totale di dati in uscita, in byte.

Per stimare i costi di accesso ai dati per gli account di archiviazione BLOB, è necessario suddividere le transazioni in due gruppi.

* La quantità di dati recuperata dall'account di archiviazione può essere stimata esaminando la somma di *'TotalEgress'* principalmente per le operazioni *'GetBlob'* e *'CopyBlob'*.

* La quantità di dati scritta nell'account di archiviazione può essere stimata esaminando la somma di *'TotalIngress'* principalmente per le operazioni *'PutBlob'*, *'PutBlock'*, *'CopyBlob'* e *'AppendBlock'*.

I costi di trasferimento dati con replica geografica per gli account di archiviazione BLOB possono anche essere calcolati usando la stima per la quantità di dati scritti quando si usa un account di archiviazione con ridondanza geografica o con ridondanza geografica e accesso in lettura.

> [!NOTE]
> Per un esempio più dettagliato relativo al calcolo dei costi per l'uso del livello di archiviazione ad accesso frequente o sporadico, vedere la domanda frequente *Che cosa sono i livelli di accesso frequente e accesso sporadico e come si determina quale usare?* nella [pagina Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

## <a name="migrating-existing-data"></a>Migrazione di dati esistenti

Un account di archiviazione BLOB serve per archiviare solo BLOB in blocchi e BLOB di aggiunta. Gli account di archiviazione per uso generico esistenti, che consentono di archiviare tabelle, code, file e dischi oltre ai BLOB, non possono essere convertiti in account di archiviazione BLOB. Per usare i livelli di archiviazione, è necessario creare nuovi account di archiviazione BLOB ed eseguire la migrazione dei dati esistenti negli account appena creati.

È possibile usare i metodi seguenti per eseguire la migrazione dei dati esistenti agli account di archiviazione BLOB da dispositivi di archiviazione locali, da provider di archiviazione cloud di terze parti o dagli account di archiviazione di uso generico esistenti in Azure:

### <a name="azcopy"></a>AzCopy

AzCopy è un'utilità da riga di comando Windows progettata per offrire prestazioni elevate di copia dei dati da e verso Archiviazione di Azure. È possibile usare AzCopy per copiare i dati nell'account di archiviazione BLOB dagli account di archiviazione di uso generico esistenti o per caricare i dati da dispositivi di archiviazione locali all'account di archiviazione BLOB.

Per altre dettagli, vedere [Trasferire dati con l'utilità della riga di comando AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Libreria di spostamento dei dati

La libreria di spostamento dei dati di Archiviazione di Azure per .NET si basa sul framework di spostamento dei dati principali alla base di AzCopy. La libreria è progettata per operazioni di trasferimento dei dati affidabili, semplici e a prestazioni elevate simili a quelle di AzCopy. In questo modo è possibile sfruttare pienamente i vantaggi delle funzionalità fornite da AzCopy nell'applicazione in modo nativo senza dover eseguire e monitorare istanze esterne di AzCopy.

Per altre dettagli, vedere [Azure Storage Data Movement Library for .Net](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>API REST o libreria client

È possibile creare un'applicazione personalizzata per eseguire la migrazione dei dati all'account di archiviazione BLOB usando una delle librerie client di Azure o l'API REST dei servizi di archiviazione di Azure. Archiviazione di Azure fornisce librerie client avanzate per più linguaggi e piattaforme, ad esempio .NET, Java, C++, Node.js, PHP, Ruby e Python. Le librerie client offrono funzionalità avanzate, ad esempio la logica di ripetizione dei tentativi, la registrazione e i caricamenti paralleli. È possibile sviluppare usando direttamente l'API REST, che può essere chiamata da qualsiasi linguaggio in grado di eseguire richieste HTTP/HTTPS.

Per altri dettagli, vedere [Introduzione all'archivio BLOB di Azure con .NET](storage-dotnet-how-to-use-blobs.md).

> [!NOTE]
> I BLOB crittografati mediante la crittografia lato client archiviano i metadati correlati alla crittografia archiviati con il BLOB. È assolutamente essenziale che qualsiasi meccanismo di copia assicuri che i metadati dei BLOB e, in particolare modo, i metadati correlati alla crittografia vengano conservati. Se si copiano i BLOB senza i metadati, il contenuto dei BLOB non è più recuperabile. Per informazioni dettagliate sui metadati correlati alla crittografia, vedere [Crittografia lato client per Archiviazione di Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="faq"></a>domande frequenti

1. **Gli account di archiviazione esistenti sono ancora disponibili?**

    Sì, gli account di archiviazione esistenti sono ancora disponibili e non hanno subito variazioni di prezzo o di funzionalità.  Non consentono di scegliere un livello di archiviazione e in futuro non avranno funzionalità di suddivisione in livelli.

2. **Perché e quando iniziare a usare gli account di archiviazione BLOB?**

    Gli account di archiviazione BLOB sono specializzati per l'archiviazione dei BLOB e offrono nuove funzionalità incentrate sui BLOB. È consigliabile usare gli account di archiviazione BLOB per archiviare i BLOB perché in futuro verranno introdotte funzionalità, ad esempio l'archiviazione gerarchica e la suddivisione in livelli, basate su questo tipo di account. La scelta del momento per eseguire la migrazione dipende tuttavia dai requisiti aziendali dell'utente.

3. **Si può convertire un account di archiviazione esistente in un account di archiviazione BLOB?**

    No. L'account di archiviazione BLOB è un tipo diverso di account di archiviazione ed è necessario crearne uno nuovo ed eseguire la migrazione dei dati come illustrato in precedenza.

4. **Si possono archiviare oggetti in entrambi i livelli di archiviazione nello stesso account?**

    Sì. L'attributo *Livello di accesso* impostato a livello di account rappresenta il livello predefinito che si applica a tutti gli oggetti in tale account in assenza di un livello impostato in modo esplicito. Tuttavia, l'organizzazione a livello di BLOB (anteprima) consente di impostare il livello di accesso a livello di oggetto indipendentemente dal livello di accesso impostato per l'account. I BLOB in tutti e tre i livelli di archiviazione (frequente, sporadico o archivio) possono trovarsi nello stesso account.

5. **Si può modificare il livello di archiviazione nell'account di archiviazione BLOB?**

    Sì, è possibile modificare il livello di archiviazione impostando l'attributo *Livello di accesso* nell'account di archiviazione. La modifica del livello di archiviazione si applica a tutti gli oggetti archiviati nell'account per i quali non è impostato in modo esplicito un livello. La modifica del livello di archiviazione da frequente a sporadico comporta l'addebito sia dei costi per le operazioni di scrittura (per decine di migliaia) che di quelli per la scrittura dati (per GB) (solo account di archiviazione BLOB), mentre la modifica da sporadico a frequente comporta l'addebito sia dei costi per le operazioni di lettura (per decine di migliaia) che di quelli per il recupero dati (per GB) per la lettura di tutti i dati nell'account.

6. **Con quale frequenza si può modificare il livello di archiviazione nell'account di archiviazione BLOB?**

    Anche se non esistono limiti alla frequenza con cui è possibile modificare il livello di archiviazione, tenere presente che la modifica del livello di archiviazione da sporadico a frequente comporta addebiti elevati. Non si consiglia di modificare il livello di archiviazione di frequente.

7. **I BLOB nel livello di archiviazione ad accesso sporadico si comportano in modo diverso rispetto a quelli del livello di archiviazione ad accesso frequente?**

    I BLOB nel livello di archiviazione ad accesso frequente hanno la stessa latenza dei BLOB negli account di archiviazione per utilizzo generico. I BLOB nel livello di archiviazione ad accesso sporadico hanno una latenza simile, in millisecondi, a quella dei BLOB negli account di archiviazione per utilizzo generico. I BLOB nel livello di archiviazione archivio prevedono diverse ore di latenza.

    I BLOB nel livello di archiviazione ad accesso sporadico hanno un contratto di servizio con disponibilità leggermente inferiore rispetto a quello dei BLOB nel livello di archiviazione ad accesso frequente. Per informazioni dettagliate, vedere [Contratto di Servizio per Archiviazione](https://azure.microsoft.com/support/legal/sla/storage).

8. **È possibile archiviare i BLOB di pagine e i dischi delle macchine virtuali negli account di archiviazione BLOB?**

    Gli account di archiviazione BLOB supportano solo i BLOB in blocchi e i BLOB di aggiunta, non i BLOB di pagine. Il backup dei dischi delle macchine virtuali di Azure viene eseguito dai BLOB di pagine. Non sarà quindi possibile usare gli account di archiviazione BLOB per archiviare i dischi delle macchine virtuali. È tuttavia possibile archiviare i backup dei dischi delle macchine virtuali come BLOB in blocchi in un account di archiviazione BLOB.

9. **È necessario modificare le applicazioni esistenti per usare gli account di archiviazione BLOB?**

    Gli account di archiviazione BLOB offrono una coerenza API al 100% con gli account di archiviazione di uso generico per i BLOB in blocchi e i BLOB di aggiunta. Se l'applicazione usa BLOB in blocchi o di aggiunta e si usa la versione 2014-02-14 o successiva dell'[API REST dei servizi di archiviazione](https://msdn.microsoft.com/library/azure/dd894041.aspx), l'applicazione dovrebbe funzionare correttamente. Se si usa una versione meno recente del protocollo, è necessario aggiornare l'applicazione per poter usare la nuova versione e lavorare quindi facilmente con entrambi i tipi di account di archiviazione. In generale, è sempre consigliabile usare la versione più recente indipendentemente dal tipo di account di archiviazione usato.

10. **L'esperienza utente è diversa?**

    Gli account di archiviazione BLOB sono molto simili agli account di archiviazione di uso generico per l'archiviazione di BLOB in blocchi e BLOB di aggiunta e supportano tutte le principali funzionalità di Archiviazione di Azure, tra cui livelli elevati di durabilità e disponibilità, scalabilità, prestazioni e sicurezza. Fatta eccezione per le funzionalità e le restrizioni specifiche degli account di archiviazione BLOB e per i livelli di archiviazione illustrati sopra, tutto il resto rimane invariato.

## <a name="next-steps"></a>Passaggi successivi

### <a name="evaluate-blob-storage-accounts"></a>Valutare gli account di archiviazione BLOB

[Verificare la disponibilità degli account di archiviazione BLOB in base all'area](https://azure.microsoft.com/regions/#services)

[Valutare l'utilizzo degli account di archiviazione attuali abilitando le metriche di Archiviazione di Azure](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Verificare i prezzi di Archiviazione di Azure per i BLOB in base all'area](https://azure.microsoft.com/pricing/details/storage/)

[Verificare i prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/)

### <a name="start-using-blob-storage-accounts"></a>Iniziare a usare gli account di archiviazione BLOB

[Introduzione all'archivio BLOB di Azure](storage-dotnet-how-to-use-blobs.md)

[Spostamento dei dati da e verso Archiviazione di Azure](../common/storage-moving-data.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Trasferire dati con l'utilità della riga di comando AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Visualizzare ed esplorare gli account di archiviazione](http://storageexplorer.com/)
