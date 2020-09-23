---
title: Normalizzazione dei dati in Sentinel di Azure | Microsoft Docs
description: Questo articolo illustra come Azure Sentinel normalizza i dati provenienti da molte origini diverse e descrive lo schema di normalizzazione.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: yelevin
ms.openlocfilehash: 0c6129a24e6ed083114971df5f254eca54924400
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939810"
---
# <a name="normalization-in-azure-sentinel"></a>Normalizzazione in Sentinel di Azure

Questo articolo illustra la normalizzazione dello schema dei dati in Sentinel di Azure e in particolare lo schema delle connessioni di rete e delle sessioni, in cui è incluso un collegamento.

## <a name="what-is-normalization"></a>Che cos'è la normalizzazione

L'utilizzo di diversi tipi di dati e tabelle presenta problemi. È necessario acquisire familiarità con molti tipi di dati e schemi diversi, dove è necessario scrivere e usare un set univoco di regole di analisi, cartelle di lavoro e query di caccia per ciascuna, anche per quelle che condividono le comunanze (ad esempio, relative ai dispositivi firewall). Anche la correlazione tra i diversi tipi di dati, necessari per l'analisi e la caccia, è difficile. Azure Sentinel offre un'esperienza semplice per la gestione dei dati da diverse origini in visualizzazioni uniformi e normalizzate.

Azure Sentinel **Common Information Model** è costituito da tre aspetti:

- Gli **schemi normalizzati** coprono set comuni di tipi di eventi stimabili (tabelle) facili da usare e per compilare funzionalità unificate in (ad esempio, la tabella di rete). Lo schema include anche una convenzione sulle colonne normalizzate e le definizioni per la standardizzazione di valori e formati (rappresentazione coerente standard di dati come indirizzi IP).

- I **parser** mappano i dati esistenti di tipi diversi allo schema normalizzato. In base al modello, i dati possono essere analizzati nello schema normalizzato in fase di query (mediante funzioni) o tempo di inserimento. Al momento, è supportata solo l'analisi in fase di query.

- Il **contenuto di ogni schema normalizzato** include regole di analisi, cartelle di lavoro interattive, query di ricerca e contenuto aggiuntivo.

### <a name="current-release"></a>Versione corrente

Con questa versione, lo [schema di sessioni e connessioni di rete normalizzate](./normalization-schema.md) (v 1.0.0) è disponibile per l'anteprima pubblica. Lo schema descrive le connessioni di rete o le sessioni come quelle registrate da firewall, Wire data, NSG, NetFlow, sistemi proxy e gateway di sicurezza Web.  Gli analizzatori della fase di query e le regole analitiche selezionate sono disponibili insieme allo schema e ne fanno uso.

Lo schema è attualmente disponibile solo tramite parser in fase di query. vedere la sezione parser.

È possibile analizzare i dati in rappresentazioni aggiuntive e utilizzare il [modello di denominazione delle entità OSSEM](https://ossemproject.com/cdm/entities/intro.html#) per creare colonne che saranno coerenti con le tabelle normalizzate esistenti e future.

## <a name="normalized-schema-and-parsing"></a>Schema normalizzato e analisi

### <a name="how-our-normalized-schemas-are-defined"></a>Modalità di definizione degli schemi normalizzati

Azure Sentinel è allineato con il modello di informazioni comuni [OSSEM (Open Source Security Events Metadata)](https://ossemproject.com/intro.html) , che consente la correlazione delle entità stimabili tra le tabelle normalizzate. OSSEM è un progetto gestito dalla community che è incentrato principalmente sulla documentazione e sulla standardizzazione dei log eventi di sicurezza da diverse origini dati e sistemi operativi. Il progetto fornisce inoltre un modello CIM (Common Information Model) che può essere utilizzato per gli ingegneri dei dati durante le procedure di normalizzazione dei dati, in modo da consentire agli analisti della sicurezza di eseguire query e analizzare i dati in diverse origini dati.

[OSSEM CIM](https://ossemproject.com/cdm/intro.html) definisce un set di entità, ad esempio file, host, IP, process, e definisce un set di attributi per ciascuna entità di questo tipo. Inoltre, il CIM definisce un set di tabelle (ad esempio, la tabella della [sessione di rete](https://ossemproject.com/cdm/tables/network_session.html) ) che usano gli attributi rilevanti di queste entità, consentendo una correlazione semplice e prevedibile. Si noti che le entità possono essere annidate. ad esempio, l'entità di origine può contenere un'entità file che avrà un attributo del nome.

Per altre informazioni sulla struttura di entità OSSEM, vedere il [riferimento ufficiale a OSSEM](https://ossemproject.com/cdm/guidelines/entity_structure.html).

### <a name="how-the-normalized-schemas-are-implemented-in-azure-sentinel"></a>Modalità di implementazione degli schemi normalizzati in Sentinel di Azure

Nell'implementazione di OSSEM CIM in Azure Sentinel, viene proiettata la rappresentazione OSSEM a una rappresentazione Log Analytics tabulare, se questa rappresentazione è una tabella predefinita o è stata creata utilizzando parser o funzioni in fase di query che eseguono il mapping dei dati esistenti a questa rappresentazione. Per la rappresentazione tabulare, vengono concatenati i nomi di entità OSSEM e i nomi di attributo ed è possibile eseguirne il mapping collettivamente a un singolo nome di colonna. Ad esempio, un'entità di origine contenente un'entità file contenente un'entità hash contenente un attributo MD5 verrà implementata come Log Analytics colonna seguente: SrcFileHashMd5. (OSSEM USA *snake_case* per impostazione predefinita, mentre Sentinel di Azure e log Analytics usano *PascalCase*. In OSSEM tale colonna verrebbe src_file_hash_md5.)

Nell'implementazione di Sentinel di Azure possono esistere campi personalizzati aggiuntivi, a causa dei requisiti di piattaforma Log Analytics e dei casi di utilizzo specifici dei clienti di Azure Sentinel.

### <a name="schema-reference"></a>Riferimento dello schema

Per altre informazioni, vedere il [documento di riferimento allo schema](./normalization-schema.md), nonché la documentazione ufficiale del [progetto OSSEM](https://ossemproject.com/cdm/intro.html).

Il riferimento allo schema include anche il valore e la standardizzazione del formato. I campi di origine, originali o analizzati, potrebbero non essere in un formato standard o utilizzare l'elenco di valori standard dello schema e, pertanto, devono essere convertiti nella rappresentazione standard dello schema per essere completamente normalizzati.

## <a name="parsers"></a>Parser

### <a name="what-is-parsing"></a>Che cos'è l'analisi

Con un set di base di tabelle normalizzate definite disponibili, è necessario trasformare i dati (analizzarli/eseguirne il mapping) in tali tabelle. Ovvero si estraggono dati specifici dal formato non elaborato in colonne note dello schema normalizzato. L'analisi in Sentinel di Azure viene eseguita in fase di **query** . i parser sono compilati come log Analytics funzioni utente (usando kusto Query Language-KQL) che trasformano i dati nelle tabelle esistenti, ad esempio CommonSecurityLog, le tabelle di log personalizzate, syslog, nello schema delle tabelle normalizzate.

L'altro tipo di analisi, non ancora supportato in Sentinel di Azure, **è in fase di inserimento** , consentendo a di raccogliere i dati direttamente nelle tabelle normalizzate quando vengono inserite dalle origini dati. L'analisi del tempo di inserimento garantisce prestazioni migliori quando viene eseguita una query sul modello di dati direttamente senza la necessità di usare funzioni.

### <a name="using-query-time-parsers"></a>Utilizzo di parser per la fase di query

#### <a name="installing-a-parser"></a>Installazione di un parser

I parser del tempo di query disponibili sono disponibili nel [repository GitHub ufficiale](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/Normalized%20Schema%20-%20Networking%20(v1.0.0))di Azure Sentinel. Per ogni parser viene usato il controllo delle versioni per consentire ai clienti di usare e monitorare facilmente gli aggiornamenti futuri. Per installare un parser:

1. Copiare il contenuto del parser pertinente da ogni file KQL pertinente nel collegamento di GitHub precedente negli Appunti

1. Nel portale di Azure Sentinel aprire la pagina Logs e incollare il contenuto del file KQL nella schermata Logs e fare clic su **Save**.

    :::image type="content" source="./media/normalization/install-new-parser.png" alt-text="Installare un nuovo parser":::

1. Nella finestra di dialogo Salva, compilare i campi come indicato di seguito:
    1. **Nome**: è consigliabile usare lo stesso valore usato nel campo alias di **funzione** (nell'esempio precedente, *CheckPoint_Network_NormalizedParser*)
    
    1. **Salva con nome**: seleziona **funzione**

    1. **Alias di funzione**: deve essere denominato nella convenzione di denominazione seguente: *PRODUCT_Network_NormalizedParser* (nell'esempio precedente *CheckPoint_Network_NormalizedParser*).

    1. **Categoria**: è possibile selezionare una categoria esistente o crearne una nuova, ad esempio *NormalizedNetworkSessionsParsers*
    
        :::image type="content" source="./media/normalization/save-new-parser.png" alt-text="Salvare il parser":::

Per utilizzare correttamente i parser, è necessario installare anche il parser dello schema di rete vuoto (che consente di creare una visualizzazione tabulare vuota di tutti i campi dello schema delle sessioni di rete) e il metaparser di rete (che unisce tutti i parser abilitati per creare una singola visualizzazione dei dati da diverse origini nello schema di rete). L'installazione di questi due parser viene eseguita in modo analogo ai passaggi precedenti.

Quando si salva una funzione di query, potrebbe essere necessario chiudere Esplora query e riaprirla per applicare la nuova funzione.

#### <a name="using-the-parsers"></a>Uso dei parser

Una volta abilitata, è possibile usare il meta-parser per eseguire una query su una visualizzazione unificata su tutti i parser attualmente abilitati. A tale scopo, passare alla pagina dei log di Sentinel ed eseguire una query sul metaparser:

:::image type="content" source="./media/normalization/query-parser.png" alt-text="Eseguire query sul parser":::
 
È anche possibile accedere al meta-parser o ai singoli parser usando Esplora query nella pagina logs di Sentinel, facendo clic su "Esplora query":

:::image type="content" source="./media/normalization/query-explorer.png" alt-text="Esplora query":::

Nel riquadro a destra, espandere la sezione "query salvate" e trovare la cartella "NormalizedNetworkParsers" (o il nome della categoria scelto durante la creazione dei parser):

:::image type="content" source="./media/normalization/find-parser.png" alt-text="Trovare il parser":::

È possibile fare clic su ogni singolo parser e visualizzare la funzione sottostante utilizzata ed eseguirla (o accedervi direttamente tramite il relativo alias, come descritto in precedenza). Si noti che alcuni parser possono mantenere i campi originali affiancati ai campi normalizzati per praticità. Questa operazione può essere facilmente modificata nella query del parser.

#### <a name="customizing-parsers"></a>Personalizzazione dei parser

È possibile ripetere i passaggi precedenti (ricerca del parser in Esplora query), fare clic sul parser pertinente e visualizzarne l'implementazione della funzione.
Ad esempio, è possibile decidere di modificare il metaparser per aggiungere/rimuovere singoli parser.

:::image type="content" source="./media/normalization/customize-parser.png" alt-text="Personalizzare il parser":::
 
Una volta modificata la funzione, fare di nuovo clic su "Salva" e utilizzare lo stesso nome, alias e categoria. Verrà aperta una finestra di dialogo di sostituzione-premere "OK":

:::image type="content" source="./media/normalization/are-you-sure.png" alt-text="Sei sicuro":::

#### <a name="additional-information"></a>Informazioni aggiuntive

Altre informazioni sulle [query salvate](../azure-monitor/log-query/saved-queries.md) , ovvero l'implementazione dei parser in fase di query, sono disponibili in log Analytics.


## <a name="next-steps"></a>Passaggi successivi

In questo documento è stato illustrato lo schema di normalizzazione di Sentinel di Azure. Per lo schema di riferimento stesso, vedere [riferimento allo schema di normalizzazione dei dati di Sentinel di Azure](./normalization-schema.md).

* [Blog di Sentinel di Azure](https://aka.ms/azuresentinelblog). Post di blog sulla sicurezza e sulla conformità di Azure.
