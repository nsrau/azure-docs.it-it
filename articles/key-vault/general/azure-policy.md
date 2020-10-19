---
title: Integrare Azure Key Vault con Criteri di Azure
description: Informazioni su come integrare Azure Key Vault con Criteri di Azure
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 10/15/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 6c1ccbfc221970980d5d0b15e82f9f8483c48bce
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043766"
---
# <a name="integrate-azure-key-vault-with-azure-policy"></a>Integrare Azure Key Vault con Criteri di Azure

[Criteri di Azure](../../governance/policy/index.yml) è uno strumento di governance che consente agli utenti di controllare e gestire con scalabilità il proprio ambiente Azure. Criteri di Azure consente di inserire protezioni per le risorse di Azure per garantire che rispettino la conformità alle regole dei criteri assegnate. Consente anche agli utenti di eseguire il controllo, l'applicazione in tempo reale e la correzione dell'ambiente Azure. I risultati dei controlli eseguiti dai criteri saranno disponibili per gli utenti in un dashboard di conformità in cui è possibile visualizzare un elenco delle risorse e dei componenti conformi e non.  Per altre informazioni, vedere [Panoramica del servizio Criteri di Azure](../../governance/policy/overview.md).

Esempi di scenari d'uso

- Si intende migliorare il comportamento in ambito di sicurezza della società implementando i requisiti relativi alle dimensioni minime delle chiavi e ai periodi di validità massimi dei certificati negli insiemi di credenziali delle chiavi dell'azienda, ma non si conoscono i team conformi e quelli che non lo sono.
- Attualmente non si dispone di una soluzione per eseguire un controllo all'interno dell'organizzazione o si eseguono controlli manuali dell'ambiente chiedendo ai singoli team all'interno dell'organizzazione di segnalare la conformità. Si cerca un modo per automatizzare questa attività, nonché eseguire controlli in tempo reale e garantirne l'accuratezza.
- Si intende applicare i criteri di sicurezza aziendali e impedire a singoli utenti di creare certificati autofirmati, ma non si dispone di un modo automatico per bloccare la creazione. 
- Si intende soddisfare alcuni requisiti per i team di test, ma si desidera mantenere controlli rigidi sull'ambiente di produzione. È necessario separare l'applicazione delle risorse in modo semplice e automatico.
- Si desidera garantire la possibilità di eseguire il rollback dell'applicazione di nuovi criteri in caso di problemi di un sito live. È necessaria una soluzione rapida per disattivare l'applicazione dei criteri. 
- Il controllo dell'ambiente viene seguito tramite una soluzione di terze parti e si intende usare un'offerta Microsoft interna.

## <a name="types-of-policy-effects-and-guidance"></a>Tipi di effetti dei criteri e indicazioni

**Controllo**. Quando l'effetto di un criterio è impostato su Controllo, il criterio non comporta alcuna modifica sostanziale dell'ambiente. Nel dashboard di conformità dei criteri vengono contrassegnati i componenti non conformi, ad esempio i certificati che non rispettano la conformità alle definizioni dei criteri in un ambito specificato. Il controllo è l'impostazione predefinita se non è selezionato alcun effetto dei criteri.

**Nega**. Quando l'effetto di un criterio è impostato su Nega, il criterio blocca la creazione di nuovi componenti, ad esempio i certificati, nonché nuove versioni dei componenti esistenti non conformi alla definizione dei criteri. Le risorse non conformi esistenti in un insieme di credenziali delle chiavi non sono interessate. Le funzionalità di controllo continuano a essere attive.

## <a name="available-built-in-policy-definitions"></a>Definizioni dei criteri predefinite disponibili

Key Vault ha creato una serie di criteri, che è possibile usare per gestire oggetti chiave, certificato e segreto. Tali criteri sono predefiniti, ovvero non richiedono la scrittura di codice JSON personalizzato per essere abilitati e sono disponibili nel portale di Azure per l'assegnazione. È comunque possibile personalizzare determinati parametri in base alle esigenze dell'organizzazione.

# <a name="certificate-policies"></a>[Criteri certificato](#tab/certificates)

### <a name="certificates-should-have-the-specified-maximum-validity-period-preview"></a>I certificati devono avere il periodo di validità massimo specificato (anteprima)

Questo criterio consente di gestire il periodo di validità massimo dei certificati archiviati nell'insieme di credenziali delle chiavi. Per motivi di sicurezza, è consigliabile limitare il periodo di validità massimo dei certificati. Se una chiave privata del certificato fosse stata compromessa senza rilevamento, l'uso di certificati di breve durata riduce al minimo l'intervallo di tempo per i danni e riduce il valore del certificato per un utente malintenzionato.

### <a name="certificates-should-use-allowed-key-types-preview"></a>I certificati devono usare i tipi di chiave consentiti (anteprima)

Questo criterio consente di limitare il tipo di certificati che possono essere presenti nell'insieme di credenziali delle chiavi. È possibile usare il criterio per verificare che le chiavi private del certificato siano RSA oppure ECC o che siano supportate da un modulo di protezione hardware. Nell'elenco seguente è possibile scegliere i tipi di certificato consentiti.

- RSA
- RSA - modulo di protezione hardware
- ECC
- ECC - modulo di protezione hardware

### <a name="certificates-should-have-the-specified-lifetime-action-triggers-preview"></a>I certificati devono contenere i trigger delle azioni specificate per la durata (anteprima)

Questo criterio consente di gestire l'azione di durata specificata per i certificati che rientrano in un determinato numero di giorni dalla scadenza o che hanno raggiunto una certa percentuale di vita valida.

### <a name="certificates-should-be-issued-by-the-specified-integrated-certificate-authority-preview"></a>I certificati devono essere rilasciati dall'autorità di certificazione integrata specificata (anteprima)

Se si usa un'autorità di certificazione integrata di Key Vault (Digicert o GlobalSign) e si vuole che gli utenti usino uno di questi provider o entrambi, è possibile usare questo criterio per controllare o applicare la selezione. Questo criterio può essere usato anche per controllare o negare la creazione di certificati autofirmati nell'insieme di credenziali delle chiavi.

### <a name="certificates-should-be-issued-by-the-specified-non-integrated-certificate-authority-preview"></a>I certificati devono essere rilasciati dall'autorità di certificazione non integrata specificata (anteprima)

Se si usa un'autorità di certificazione interna o un'autorità di certificazione non integrata con l'insieme di credenziali delle chiavi e si vuole che gli utenti usino un'autorità di certificazione da un elenco fornito, è possibile usare questo criterio per creare un elenco consentito di autorità di certificazione in base al nome dell'autorità emittente. Questo criterio può essere usato anche per controllare o negare la creazione di certificati autofirmati nell'insieme di credenziali delle chiavi.

### <a name="certificates-using-elliptic-curve-cryptography-should-have-allowed-curve-names-preview"></a>Per i certificati che usano la crittografia a curva ellittica sono necessari nomi di curva consentiti (anteprima)

Se si usano i certificati con crittografia a curva ellittica (ECC), è possibile personalizzare un elenco di nomi di curve consentiti. L'opzione predefinita consente di usare tutti i nomi di curva seguenti.

- P-256
- P-256K
- P-384
- P-521

## <a name="certificates-using-rsa-cryptography-manage-minimum-key-size-for-rsa-certificates-preview"></a>I certificati che usano la crittografia RSA devono avere le dimensioni minime della chiave specificate (anteprima)

Se si usano i certificati RSA, è possibile scegliere una dimensione minima della chiave dei certificati stessi. È possibile selezionare un'opzione nell'elenco seguente.

- 2048 bit
- 3072 bit
- 4096 bit

## <a name="manage-certificates-that-are-within-a-specified-number-of-days-of-expiration-preview"></a>Gestisci i certificati che si trovano entro un numero di giorni specificato dalla scadenza (anteprima)

Il servizio può riscontrare un'interruzione se un certificato non monitorato in modo adeguato non viene ruotato prima della scadenza. Questo criterio è essenziale per garantire che i certificati archiviati nell'insieme di credenziali delle chiavi siano monitorati. È consigliabile applicare questo criterio più volte con soglie di scadenza diverse, ad esempio 180, 90, 60 e 30 giorni. Il criterio può essere usato per monitorare e valutare la scadenza dei certificati nell'organizzazione.

# <a name="key-policies"></a>[Criteri per le chiavi](#tab/keys)

### <a name="keys-should-not-be-active-for-longer-than-the-specified-number-of-days-preview"></a>Le chiavi non devono rimanere attive per un periodo più lungo del numero di giorni specificato (anteprima)

Per assicurarsi che le chiavi non rimangano attive per un periodo più lungo del numero di giorni specificato, è possibile usare questo criterio per controllare per quanto tempo sono state attive.

**Se per la chiave è impostata una data di attivazione**, questo criterio calcolerà il numero di giorni trascorsi dalla **data di attivazione** della chiave alla data corrente. Se il numero di giorni supera la soglia impostata, la chiave verrà contrassegnata come non conforme al criterio.

**Se per la chiave non è impostata una data di attivazione**, questo criterio calcolerà il numero di giorni trascorsi dalla **data di creazione** della chiave alla data corrente. Se il numero di giorni supera la soglia impostata, la chiave verrà contrassegnata come non conforme al criterio.

### <a name="keys-should-be-the-specified-cryptographic-type-rsa-or-ec-preview"></a>Le chiavi devono essere del tipo di crittografia RSA o EC specificato (anteprima)

Questo criterio consente di limitare il tipo di chiavi che possono essere presenti nell'insieme di credenziali delle chiavi. È possibile usare il criterio per verificare che le chiavi siano RSA oppure ECC o che siano supportate da un modulo di protezione hardware. Nell'elenco seguente è possibile scegliere i tipi di certificato consentiti.

- RSA
- RSA - modulo di protezione hardware
- ECC
- ECC - modulo di protezione hardware

### <a name="keys-using-elliptic-curve-cryptography-should-have-the-specified-curve-names-preview"></a>Per le chiavi che usano la crittografia a curva ellittica sono necessari nomi di curva consentiti (anteprima)

Se si usano chiavi con crittografia a curva ellittica (ECC), è possibile personalizzare un elenco di nomi di curve consentiti. L'opzione predefinita consente di usare tutti i nomi di curva seguenti.

- P-256
- P-256K
- P-384
- P-521

### <a name="keys-should-have-expirations-dates-set-preview"></a>Per le chiavi devono essere impostate date di scadenza (anteprima)

Questo criterio controlla tutte le chiavi negli insiemi di credenziali delle chiavi e contrassegna come non conformi quelle per cui non è impostata una data di scadenza. È anche possibile usare questo criterio per bloccare la creazione delle chiavi per cui non è impostata una data di scadenza.

### <a name="keys-should-have-more-than-the-specified-number-of-days-before-expiration-preview"></a>Per le chiavi è necessario considerare più tempo rispetto al numero specificato di giorni prima della scadenza (anteprima)

Se una chiave è troppo vicina alla scadenza, un ritardo dell'organizzazione nel ruotarla può generare un'interruzione. Le chiavi devono essere ruotate un numero specificato di giorni prima della scadenza per fornire tempo sufficiente per rispondere a un errore. Questo criterio controlla le chiavi troppo vicine alla data di scadenza e consente di impostare questa soglia in giorni. È anche possibile usare questo criterio per impedire la creazione di nuove chiavi troppo vicine alla data di scadenza.

### <a name="keys-should-be-backed-by-a-hardware-security-module-preview"></a>Le chiavi devono essere supportate da un modulo di protezione hardware (anteprima)

Il modulo di protezione hardware o HSM (Hardware Security Module) è un modulo che archivia chiavi. Fornisce un livello di protezione fisico per le chiavi di crittografia. La chiave di crittografia non può lasciare un modulo di protezione hardware fisico, che fornisce un livello di sicurezza maggiore rispetto a una chiave software. Alcune organizzazioni hanno requisiti di conformità che impongono l'uso di chiavi HSM. Usare questo criterio per controllare le chiavi archiviate nell'insieme di credenziali delle chiavi che non sono supportate da un modulo di protezione hardware. È anche possibile usare questo criterio per bloccare la creazione delle chiavi non supportate da un modulo di protezione hardware. Questo criterio si applica a tutti i tipi di chiavi, ovvero RSA e ECC.

### <a name="keys-using-rsa-cryptography-should-have-a-specified-minimum-key-size-preview"></a>Le chiavi che usano la crittografia RSA devono avere le dimensioni minime specificate (anteprima)

L'uso di chiavi RSA di dimensioni inferiori non è una procedura di progettazione sicura. L'organizzazione può essere soggetta a standard di controllo e certificazione che impongono l'uso di dimensioni minime delle chiavi. Il criterio seguente consente di impostare un requisito minimo per le dimensioni delle chiavi nell'insieme di credenziali delle chiavi. È possibile controllare le chiavi che non soddisfano questo requisito minimo. Questo criterio può anche essere usato per bloccare la creazione di nuove chiavi che non soddisfano il requisito minimo di dimensioni.

### <a name="keys-should-have-the-specified-maximum-validity-period-preview"></a>Le chiavi devono avere il periodo di validità massimo specificato (anteprima)

È possibile gestire i requisiti di conformità aziendali specificando il tempo di validità massimo di una chiave, in giorni, all'interno dell'insieme di credenziali delle chiavi. Le chiavi con una validità più lunga rispetto alla soglia impostata verranno contrassegnate come non conformi. È anche possibile usare questo criterio per bloccare la creazione di nuove chiavi con una data di scadenza più lunga del periodo di validità massimo specificato.

# <a name="secret-policies"></a>[Criteri per i segreti](#tab/secrets)

### <a name="secrets-should-not-be-active-for-longer-than-the-specified-number-of-days-preview"></a>I segreti non devono rimanere attivi per un periodo più lungo del numero di giorni specificato (anteprima)

Per assicurarsi che i segreti non rimangano attivi per un periodo più lungo del numero di giorni specificato, è possibile usare questo criterio per controllare per quanto tempo sono stati attivi.

**Se per il segreto è impostata una data di attivazione**, questo criterio calcolerà il numero di giorni trascorsi dalla **data di attivazione** del segreto alla data corrente. Se il numero di giorni supera la soglia impostata, il segrego verrà contrassegnato come non conforme al criterio.

**Se per il segreto non è impostata una data di attivazione**, questo criterio calcolerà il numero di giorni trascorsi dalla **data di creazione** del segreto alla data corrente. Se il numero di giorni supera la soglia impostata, il segrego verrà contrassegnato come non conforme al criterio.

### <a name="secrets-should-have-content-type-set-preview"></a>I segreti devono avere un tipo di contenuto impostato (anteprima)

Qualsiasi file in testo normale o codificato può essere archiviato come segreto nell'insieme di credenziali delle chiavi. Tuttavia, l'organizzazione può scegliere di impostare criteri di rotazione diversi e restrizioni su password, stringhe di connessione o certificati archiviati come chiavi. Un tag del tipo di contenuto può consentire a un utente di verificare cosa viene archiviato in un oggetto segreto senza leggere il valore del segreto. È possibile usare questo criterio per controllare i segreti per cui non è impostato un tag del tipo di contenuto. È anche possibile usare questo criterio per impedire la creazione di nuovi segreti se non è stato impostato un tag del tipo di contenuto.

### <a name="secrets-should-have-expiration-date-set-preview"></a>Per i segreti deve essere impostata una data di scadenza (anteprima)

Questo criterio controlla tutti i segreti nell'insieme di credenziali delle chiavi e contrassegna come non conformi quelli per cui non è impostata una data di scadenza. È anche possibile usare questo criterio per bloccare la creazione di segreti per cui non è impostata una data di scadenza.

### <a name="secrets-should-have-more-than-the-specified-number-of-days-before-expiration-preview"></a>Per i segreti è necessario considerare più tempo rispetto al numero specificato di giorni prima della scadenza (anteprima)

Se un segreto è troppo vicino alla scadenza, un ritardo dell'organizzazione nel ruotarlo può generare un'interruzione. I segreti devono essere ruotati un numero specificato di giorni prima della scadenza per fornire tempo sufficiente per rispondere a un errore. Questo criterio controlla i segreti troppo vicini alla data di scadenza e consente di impostare questa soglia in giorni. È anche possibile usare questo criterio per impedire la creazione di nuovi segreti troppo vicini alla data di scadenza.

### <a name="secrets-should-have-the-specified-maximum-validity-period-preview"></a>I segreti devono avere il periodo di validità massimo specificato (anteprima)

È possibile gestire i requisiti di conformità aziendali specificando il tempo di validità massimo di un segreto, in giorni, all'interno dell'insieme di credenziali delle chiavi. I segreti con una validità più lunga rispetto alla soglia impostata verranno contrassegnati come non conformi. È anche possibile usare questo criterio per bloccare la creazione di nuovi segreti con una data di scadenza più lunga del periodo di validità massimo specificato.

---

## <a name="example-scenario"></a>Scenario di esempio

Si gestisce un insieme di credenziali delle chiavi usato da più team che contiene 100 certificati e si vuole verificare che nessun certificato nell'insieme di credenziali delle chiavi sia valido per più di due anni.

1. Si assegna il criterio **I certificati devono avere il periodo di validità massimo specificato**, si specifica che il periodo di validità massimo di un certificato sia di 24 mesi e si imposta l'effetto del criterio su "controllo". 
1. Nel [report di conformità nel portale di Azure](#view-compliance-results) viene rilevato che 20 certificati non sono conformi e che sono validi per più di due anni e che i certificati rimanenti sono conformi. 
1. Si contattano i proprietari di questi certificati e si comunica il nuovo requisito di sicurezza, in base al quale i certificati non possono essere validi per più di due anni. Alcuni team rispondono e 15 certificati vengono rinnovati con un periodo di validità massimo di 2 anni o meno. Altri team non rispondono e nell'insieme di credenziali delle chiavi sono ancora presenti 5 certificati.
1. L'effetto dei criteri assegnati viene impostato su "nega". I 5 certificati non conformi non vengono revocati e continuano a funzionare. Tali certificati, tuttavia, non possono essere rinnovati con un periodo di validità maggiore di 2 anni. 

## <a name="enabling-and-managing-a-key-vault-policy-through-the-azure-portal"></a>Abilitazione e gestione di un criterio di Key Vault tramite il portale di Azure

### <a name="select-a-policy-definition"></a>Selezionare una definizione di criteri

1. Accedere al portale di Azure. 
1. Cercare "Criteri" nella barra di ricerca e selezionare **Criteri**.

    ![Panoramica del funzionamento di Azure Key Vault](../media/policy-img1.png)

1. Nella finestra Criteri selezionare **Definizioni**.

    ![Panoramica del funzionamento di Azure Key Vault](../media/policy-img2.png)

1. Nel filtro Categoria deselezionare **Seleziona tutto** e quindi selezionare **Key Vault**. 

    ![Panoramica del funzionamento di Azure Key Vault](../media/policy-img3.png)

1. A questo punto dovrebbe essere possibile visualizzare tutti i criteri disponibili per l'anteprima pubblica, ad Azure Key Vault. Assicurarsi di aver letto e compreso la sezione delle linee guida precedente e selezionare i criteri da assegnare a un ambito.  

    ![Panoramica del funzionamento di Azure Key Vault](../media/policy-img4.png)

### <a name="assign-a-policy-to-a-scope"></a>Assegnare criteri a un ambito 

1. Selezionare i criteri da applicare. In questo esempio viene visualizzato il criterio **Gestisci periodo di validità del certificato**. Fare clic sul pulsante di assegnazione nell'angolo superiore sinistro.

    ![Panoramica del funzionamento di Azure Key Vault](../media/policy-img5.png)
  
1. Selezionare la sottoscrizione in cui si desidera applicare i criteri. È possibile scegliere di limitare l'ambito a un singolo gruppo di risorse in una sottoscrizione. Se si desidera applicare i criteri all'intera sottoscrizione ed escludere alcuni gruppi di risorse, è anche possibile configurare un elenco di esclusione. Impostare il selettore di imposizione dei criteri su **Abilitato** se si desidera che l'effetto del criterio (controllo o negazione) venga applicato o su **Disabilitato** per disattivarne l'applicazione. 

    ![Panoramica del funzionamento di Azure Key Vault](../media/policy-img6.png)

1. Fare clic sulla scheda dei parametri nella parte superiore della schermata per specificare il periodo di validità massimo desiderato (in mesi). Selezionare **Controllo** o **Nega** come effetto del criterio seguendo le indicazioni delle sezioni precedenti e quindi selezionare il pulsante Rivedi e crea. 

    ![Panoramica del funzionamento di Azure Key Vault](../media/policy-img7.png)

### <a name="view-compliance-results"></a>Visualizzare i risultati di conformità

1. Tornare al pannello dei criteri e selezionare la scheda relativa alla conformità. Fare clic sull'assegnazione di criteri per cui visualizzare i risultati di conformità.

    ![Panoramica del funzionamento di Azure Key Vault](../media/policy-img8.png)

1. In questa pagina è possibile filtrare i risultati in base a insiemi di credenziali conformi o non conformi. È possibile visualizzare anche un elenco di insiemi di credenziali delle chiavi non conformi nell'ambito dell'assegnazione dei criteri. Un insieme di credenziali viene considerato non conforme se uno dei componenti (certificati) presenti nell'insieme non è conforme. È possibile selezionare un singolo insieme di credenziali per visualizzare i singoli componenti non conformi (certificati). 


    ![Panoramica del funzionamento di Azure Key Vault](../media/policy-img9.png)

1. Visualizzare il nome dei componenti in un insieme di credenziali non conformi


    ![Panoramica del funzionamento di Azure Key Vault](../media/policy-img10.png)

1. Se è necessario controllare se agli utenti viene negata la possibilità di creare risorse nell'insieme di credenziali delle chiavi, fare clic sulla scheda **Eventi dei componenti (anteprima)** per visualizzare un riepilogo delle operazioni di certificato negate con il richiedente e i timestamp delle richieste. 


    ![Panoramica del funzionamento di Azure Key Vault](../media/policy-img11.png)

## <a name="feature-limitations"></a>Limitazioni delle funzionalità

L'assegnazione di un criterio con un effetto "Nega" può richiedere fino a 30 minuti (caso medio) e 1 ora (caso peggiore) per iniziare a negare la creazione di risorse non conformi. La valutazione dei criteri dei componenti esistenti in un insieme di credenziali può richiedere fino a 1 ora (caso medio) e 2 ore (caso peggiore) prima che i risultati di conformità vengano visualizzabili nell'interfaccia utente del portale. Se i risultati di conformità vengono visualizzati come "non avviati", i motivi possono essere i seguenti:
- La valutazione dei criteri non è ancora stata completata. La latenza di valutazione iniziale può richiedere fino a 2 ore nello scenario peggiore. 
- Non sono presenti insiemi di credenziali delle chiavi nell'ambito dell'assegnazione dei criteri.
- Non sono presenti insiemi di credenziali delle chiavi con certificati nell'ambito dell'assegnazione di criteri.

> [!NOTE]
> Le [modalità Provider di risorse](../../governance/policy/concepts/definition-structure.md#resource-provider-modes) di Criteri di Azure, ad esempio quelle per Azure Key Vault, offrono informazioni sulla conformità nella pagina [Conformità dei componenti](../../governance/policy/how-to/get-compliance-data.md#component-compliance).

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul [Servizio Criteri di Azure](../../governance/policy/overview.md)
- Vedere esempi di Key Vault: [Definizioni dei criteri predefinite di Key Vault](../../governance/policy/samples/built-in-policies.md#key-vault)
