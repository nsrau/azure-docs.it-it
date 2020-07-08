---
title: Gestione dei certificati in un cluster Service Fabric
description: Informazioni sulla gestione dei certificati in un cluster Service Fabric protetto con certificati X. 509.
ms.topic: conceptual
ms.date: 04/10/2020
ms.custom: sfrev
ms.openlocfilehash: 6be9cbe77ef5e64659e56447d0a5b6be30b05272
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84324743"
---
# <a name="certificate-management-in-service-fabric-clusters"></a>Gestione dei certificati nei cluster Service Fabric

Questo articolo illustra gli aspetti di gestione dei certificati usati per proteggere le comunicazioni nei cluster di Service Fabric di Azure e integra l'introduzione alla [sicurezza Service Fabric cluster](service-fabric-cluster-security.md) , oltre che all' [autenticazione basata su certificati X. 509 in Service Fabric](cluster-security-certificates.md). Si presuppone che il lettore abbia familiarità con i concetti fondamentali sulla sicurezza e con i controlli che Service Fabric espone per configurare la sicurezza di un cluster.  

Aspetti trattati in questo titolo:

* Che cos'è esattamente "Gestione certificati"?
* Ruoli ed entità necessari per la gestione dei certificati
* Il percorso di un certificato
* Approfondimento su un esempio
* Risoluzione dei problemi e domande frequenti

Per prima cosa, tuttavia, questo articolo tenta di abbinare il proprio lato teorico con esempi pratici, che richiedono, bene, specifiche di servizi, tecnologie e così via. Poiché una parte significativa del pubblico è Microsoft-Internal, si farà riferimento a servizi, tecnologie e prodotti specifici per Microsoft Azure. Per chiarimenti o indicazioni, vedere la sezione relativa ai commenti in cui non si applicano i dettagli specifici di Microsoft.

## <a name="defining-certificate-management"></a>Definizione della gestione dei certificati
Come illustrato nell' [Articolo complementare](cluster-security-certificates.md), un certificato è un oggetto crittografico che associa essenzialmente una coppia di chiavi asimmetriche con attributi che descrivono l'entità che rappresenta. Tuttavia, è anche un oggetto "deperibile", perché la sua durata è finita ed è soggetta a compromessi. la divulgazione accidentale o un exploit efficace può rendere inutile il rendering di un certificato dal punto di vista della sicurezza. Ciò implica la necessità di modificare i certificati, regolarmente o in risposta a un evento imprevisto della sicurezza. Un altro aspetto della gestione (e di un intero argomento) è la protezione delle chiavi private del certificato o dei segreti che proteggono le identità delle entità che coinvolgono il provisioning e il provisioning dei certificati. Si fa riferimento ai processi e alle procedure usati per ottenere i certificati e per trasferirli in modo sicuro (e sicuro) a dove sono necessari come "Gestione certificati". Alcune delle operazioni di gestione, ad esempio la registrazione, l'impostazione dei criteri e i controlli di autorizzazione, esulano dall'ambito di questo articolo. Altri ancora, ad esempio il provisioning, il rinnovo, la riattivazione o la revoca, riguardano solo le Service Fabric; Tuttavia, li affrontiamo qui a un certo livello, in quanto la comprensione di queste operazioni può aiutare a proteggere correttamente il cluster di uno. 

L'obiettivo è quello di automatizzare il più possibile la gestione dei certificati per garantire la disponibilità ininterrotta del cluster e offrire garanzie di sicurezza, dato che il processo è senza tocco utente. Questo obiettivo è attualmente raggiungibile nei cluster di Azure Service Fabric; il resto dell'articolo definirà prima di tutto la gestione dei certificati e in seguito si concentrerà sull'abilitazione del rollover.

In particolare, gli argomenti nell'ambito sono:
  - presupposti correlati alla separazione delle attribuzioni tra proprietario e piattaforma, nel contesto della gestione dei certificati
  - pipeline estesa di certificati da rilascio a consumo
  - rotazione del certificato: perché, come e quando
  - cosa potrebbe andare male?

Aspetti come la protezione/gestione dei nomi di dominio, la registrazione in certificati o la configurazione dei controlli di autorizzazione per applicare il rilascio di certificati non rientrano nell'ambito di questo articolo. Fare riferimento all'autorità di registrazione (RA) del servizio di infrastruttura a chiave pubblica (PKI) preferito. Microsoft-Consumer interni: contattare la sicurezza di Azure.

## <a name="roles-and-entities-involved-in-certificate-management"></a>Ruoli ed entità necessari per la gestione dei certificati
L'approccio di sicurezza in un cluster Service Fabric è un caso di "proprietario del cluster che lo dichiara, Service Fabric Runtime lo impone". Ciò significa che quasi nessuno dei certificati, delle chiavi o di altre credenziali delle identità che partecipano al funzionamento di un cluster proviene dal servizio stesso; sono tutti dichiarati dal proprietario del cluster. Inoltre, il proprietario del cluster è anche responsabile del provisioning dei certificati nel cluster, del rinnovo in base alle esigenze e della sicurezza dei certificati sempre. In particolare, il proprietario del cluster deve verificare che:
  - i certificati dichiarati nella sezione NodeType del manifesto del cluster possono essere trovati in ogni nodo di tale tipo, in base alle [regole di presentazione](cluster-security-certificates.md#presentation-rules)
  - i certificati dichiarati in precedenza vengono installati inclusi tra le chiavi private corrispondenti
  - i certificati dichiarati nelle regole di presentazione devono superare le [regole di convalida](cluster-security-certificates.md#validation-rules) 

Service Fabric, per sua parte, presuppone le responsabilità di:
  - individuazione/individuazione di certificati corrispondenti alle dichiarazioni nella definizione del cluster  
  - concessione dell'accesso alle chiavi private corrispondenti a entità controllate da Service Fabric in base a una "necessità"
  - convalida dei certificati in modo rigoroso in base alle procedure consigliate per la protezione stabilite e alla definizione del cluster
  - generazione di avvisi per la scadenza imminente dei certificati o errori per eseguire i passaggi di base della convalida dei certificati
  - convalida (a un certo livello) che gli aspetti relativi ai certificati della definizione del cluster sono soddisfatti dalla configurazione sottostante degli host 

Segue che il carico di gestione dei certificati, come le operazioni attive, rientra esclusivamente nel proprietario del cluster. Nelle sezioni seguenti verranno esaminate in dettaglio tutte le operazioni di gestione, con i meccanismi disponibili e il relativo effetto sul cluster.

## <a name="the-journey-of-a-certificate"></a>Il percorso di un certificato
Ci rivediamo rapidamente la progressione di un certificato dal rilascio al consumo nel contesto di un cluster Service Fabric:

  1. Un proprietario del dominio si registra con l'autorità di certificazione di un'infrastruttura a chiave pubblica (PKI) di un dominio o di un oggetto che desidera associare ai certificati seguenti; i certificati, a loro volta, costituiscono prove della proprietà di detto dominio o oggetto
  2. Il proprietario del dominio designa inoltre nell'autorità di certificazione le identità dei richiedenti autorizzati, entità che hanno diritto a richiedere la registrazione dei certificati con il dominio o l'oggetto specificato. in Microsoft Azure, il provider di identità predefinito è Azure Active Directory e i richiedenti autorizzati sono designati dall'identità AAD corrispondente (o tramite gruppi di sicurezza)
  3. Un richiedente autorizzato si iscrive quindi a un certificato tramite un servizio di gestione dei segreti. in Microsoft Azure, l'SMS scelto è Azure Key Vault (AKV), che archivia in modo sicuro e consente il recupero di segreti/certificati da parte di entità autorizzate. AKV inoltre rinnova/richiavi il certificato come configurato nei criteri dei certificati associati. (AKV USA AAD come provider di identità).
  4. Un recupero autorizzato, a cui si farà riferimento come "agente di provisioning": Recupera il certificato, comprensivo della relativa chiave privata, dall'insieme di credenziali e lo installa nei computer che ospitano il cluster.
  5. Il servizio Service Fabric (in esecuzione con privilegi elevati in ogni nodo) concede l'accesso al certificato per consentire Service Fabric entità; questi vengono definiti da gruppi locali e divisi tra ServiceFabricAdministrators e ServiceFabricAllowedUsers
  6. Il runtime di Service Fabric accede e usa il certificato per stabilire la Federazione o per eseguire l'autenticazione a richieste in ingresso da client autorizzati
  7. L'agente di provisioning monitora il certificato dell'insieme di credenziali e attiva il flusso di provisioning al rilevamento del rinnovo. Successivamente, il proprietario del cluster aggiorna la definizione del cluster, se necessario, per indicare l'intenzione di eseguire il rollover del certificato.
  8. L'agente di provisioning o il proprietario del cluster è anche responsabile della pulizia/eliminazione dei certificati inutilizzati
  
Per i nostri scopi, i primi due passaggi nella sequenza precedente sono in gran parte non correlati. l'unica connessione è che il nome comune del soggetto del certificato è il nome DNS dichiarato nella definizione del cluster.

Questi passaggi sono illustrati di seguito. si notino le differenze nel provisioning tra i certificati dichiarati per identificazione personale e il nome comune, rispettivamente.

*Fig. 1.* Emissione e provisioning del flusso di certificati dichiarati dall'identificazione personale.
![Provisioning dei certificati dichiarati dall'identificazione personale][Image1]

*Figg. 2.* Emissione e provisioning del flusso di certificati dichiarati dal nome comune del soggetto.
![Provisioning dei certificati dichiarati dal nome comune del soggetto][Image2]

### <a name="certificate-enrollment"></a> Registrazione certificato
Questo argomento è illustrato in dettaglio nella [documentazione](../key-vault/create-certificate.md)di Key Vault; qui viene inclusa una sintesi per la continuità e un riferimento più semplice. Continuando con Azure come contesto e usando Azure Key Vault come servizio di gestione dei segreti, un richiedente di certificati autorizzato deve avere almeno le autorizzazioni di gestione dei certificati per l'insieme di credenziali, concesso dal proprietario dell'insieme di credenziali; il richiedente esegue quindi la registrazione in un certificato come indicato di seguito:
    - Crea un criterio di certificato in Azure Key Vault (AKV), che specifica il dominio/oggetto del certificato, l'emittente desiderata, il tipo di chiave e la lunghezza, l'utilizzo della chiave previsto e altro ancora; per informazioni dettagliate, vedere [certificati in Azure Key Vault](../key-vault/certificate-scenarios.md) . 
    - Crea un certificato nello stesso insieme di credenziali con i criteri specificati in precedenza; questo, a sua volta, genera una coppia di chiavi come oggetti dell'insieme di credenziali, una richiesta di firma del certificato firmata con la chiave privata e che viene quindi inoltrata all'emittente designata per la firma
    - una volta che l'emittente (autorità di certificazione) risponde con il certificato firmato, il risultato viene unito nell'insieme di credenziali e il certificato è disponibile per le operazioni seguenti:
      - in {vaultUri}/Certificates/{Name}: certificato che include la chiave pubblica e i metadati
      - in {vaultUri}/Keys/{Name}: chiave privata del certificato, disponibile per le operazioni di crittografia (wrap/unwrap, Sign/Verify)
      - in {vaultUri}/Secrets/{Name}: certificato inclusivo della relativa chiave privata, disponibile per il download come file pfx o PEM non protetto  
    Tenere presente che un certificato dell'insieme di credenziali è in realtà una linea cronologica di istanze di certificato, che condivide un criterio. Le versioni dei certificati verranno create in base agli attributi di durata e rinnovo del criterio. Si consiglia vivamente che i certificati dell'insieme di credenziali non condividano oggetti o domini/nomi DNS; il provisioning di istanze di certificati da diversi certificati dell'insieme di credenziali, con oggetti identici, ma sostanzialmente diversi altri attributi, ad esempio autorità emittenti, usi chiave e così via, può compromettere l'utilizzo di un cluster.

A questo punto, un certificato esiste nell'insieme di credenziali, pronto per l'utilizzo. In poi:

### <a name="certificate-provisioning"></a>Provisioning del certificato
È stato indicato un "agente di provisioning", ovvero l'entità che recupera il certificato, comprensivo della relativa chiave privata, dall'insieme di credenziali e lo installa in ogni host del cluster. Ricordare che Service Fabric non esegue il provisioning dei certificati. In questo contesto, il cluster verrà ospitato in una raccolta di VM di Azure e/o set di scalabilità di macchine virtuali. In Azure il provisioning di un certificato da un insieme di credenziali a una macchina virtuale/VMSS può essere ottenuto con i meccanismi seguenti, presupponendo, come sopra, che l'agente di provisioning abbia precedentemente concesso le autorizzazioni ' Get ' nell'insieme di credenziali per il proprietario dell'insieme di credenziali: 
  - ad hoc: un operatore Recupera il certificato dall'insieme di credenziali (come PFX/PKCS #12 o PEM) e lo installa in ogni nodo
  - come set di scalabilità di macchine virtuali ' Secret ' durante la distribuzione: il servizio di calcolo recupera, usando la relativa identità di prima entità per conto dell'operatore, il certificato da un insieme di credenziali abilitato per la distribuzione di modelli e lo installa in ogni nodo del set di scalabilità di macchine virtuali ([come in questo](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#certificates)caso); Si noti che questo consente il provisioning dei soli segreti con versione
  - uso dell' [estensione della macchina virtuale Key Vault](../virtual-machines/extensions/key-vault-windows.md); in questo modo è possibile eseguire il provisioning dei certificati utilizzando dichiarazioni senza versione, con aggiornamento periodico dei certificati osservati. In questo caso, la macchina virtuale/VMSS dovrebbe avere un' [identità gestita](../virtual-machines/windows/security-policy.md#managed-identities-for-azure-resources), un'identità a cui è stato concesso l'accesso agli insiemi di credenziali contenenti i certificati osservati.

Il meccanismo ad hoc non è consigliato per diversi motivi, a partire dalla sicurezza alla disponibilità e non verrà discusso più avanti. per informazioni dettagliate, vedere [certificati nei set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#certificates).

Il provisioning di VMSS-/Compute-based presenta vantaggi in materia di sicurezza e disponibilità, ma presenta anche restrizioni. Richiede, per impostazione predefinita, la dichiarazione di certificati come segreti con versione, che lo rende adatto solo per i cluster protetti con certificati dichiarati dall'identificazione personale. Al contrario, il provisioning basato sull'estensione della macchina virtuale Key Vault installerà sempre la versione più recente di ogni certificato osservato, che lo rende adatto solo per i cluster protetti con certificati dichiarati dal nome comune del soggetto. Per enfatizzare, non usare un meccanismo di provisioning di aggiornamento automatico, ad esempio l'estensione KVVM, per i certificati dichiarati da un'istanza (ovvero per identificazione digitale). il rischio di perdita della disponibilità è considerevole.

Potrebbero esistere altri meccanismi di provisioning; le versioni precedenti sono attualmente accettate per i cluster di Azure Service Fabric.

### <a name="certificate-consumption-and-monitoring"></a>Utilizzo e monitoraggio del certificato
Come indicato in precedenza, il runtime di Service Fabric è responsabile dell'individuazione e dell'utilizzo dei certificati dichiarati nella definizione del cluster. L'articolo sull' [autenticazione basata su certificati](cluster-security-certificates.md) ha illustrato in dettaglio come Service Fabric implementa le regole di presentazione e di convalida, rispettivamente, e non verrà rivisitato qui. Verranno esaminati l'accesso e la concessione delle autorizzazioni, nonché il monitoraggio.

Ricordare che i certificati in Service Fabric vengono usati per molti scopi, dall'autenticazione reciproca nel livello federativo all'autenticazione TLS per gli endpoint di gestione. Questa operazione richiede l'accesso alla chiave privata del certificato da parte di diversi componenti o servizi di sistema. Il runtime di Service Fabric analizza regolarmente l'archivio certificati, cercando le corrispondenze per ogni regola di presentazione Nota; per ogni certificato corrispondente, viene individuata la chiave privata corrispondente e l'elenco di controllo di accesso discrezionale viene aggiornato in modo da includere le autorizzazioni, in genere in lettura ed esecuzione, concesse alla rispettiva identità che le richiede. Questo processo viene definito in modo informale come ' ACLing '. Il processo viene eseguito in una cadenza di 1 minuto e copre anche i certificati ' applicazione ', ad esempio quelli usati per crittografare le impostazioni o come certificati dell'endpoint. ACLing segue le regole di presentazione, quindi è importante tenere presente che i certificati dichiarati dall'identificazione personale e che vengono aggiornati in modo automatico senza l'aggiornamento della configurazione del cluster risultante non saranno accessibili.    

### <a name="certificate-rotation"></a>Rotazione del certificato
Si noti che IETF [RFC 3647](https://tools.ietf.org/html/rfc3647) definisce formalmente il [rinnovo](https://tools.ietf.org/html/rfc3647#section-4.4.6) come rilascio di un certificato con gli stessi attributi del certificato da sostituire, ovvero l'emittente, la chiave pubblica e le informazioni del soggetto vengono mantenute e la [reimpostazione](https://tools.ietf.org/html/rfc3647#section-4.4.7) della chiave come rilascio di un certificato con una nuova coppia di chiavi e non prevede alcuna restrizione se l'emittente potrebbe cambiare. Dato che la distinzione può essere importante (si consideri il caso di certificati dichiarati dal nome comune del soggetto con il blocco dell'autorità emittente), si sceglierà il termine neutro "Rotation" per coprire entrambi gli scenari. Tenere presente che, quando usato in modo informale, il termine "rinnovo" si riferisce effettivamente alla reimpostazione. 

Abbiamo visto in precedenza che Azure Key Vault supporta la rotazione automatica dei certificati: i criteri di associazione dei certificati definiscono il punto nel tempo, se per giorni prima della scadenza o percentuale della durata totale, quando il certificato viene ruotato nell'insieme di credenziali. Per distribuire il nuovo certificato a tutti i nodi del cluster, l'agente di provisioning deve essere richiamato dopo questo momento e prima della scadenza del certificato precedente. Service Fabric fornirà assistenza per la generazione di avvisi di integrità quando la data di scadenza di un certificato (e attualmente in uso nel cluster) si verifica prima di un intervallo predeterminato. Un agente di provisioning automatico, ovvero l'estensione della macchina virtuale dell'insieme di credenziali delle credenziali, configurato per osservare il certificato dell'insieme di credenziali, effettuerà periodicamente il polling dell'insieme di credenziali, rileverà la rotazione e recupererà e installerà il nuovo certificato Il provisioning eseguito tramite la funzionalità' Secrets ' di VM/VMSS richiede un operatore autorizzato per aggiornare la VM/VMSS con l'URI dell'insieme di credenziali delle chiavi con versione corrispondente al nuovo certificato.

In entrambi i casi, il certificato ruotato viene ora sottoposta a provisioning in tutti i nodi ed è stato descritto il meccanismo Service Fabric impiega per rilevare le rotazioni. Esaminiamo cosa accade successivamente, presupponendo la rotazione applicata al certificato del cluster dichiarato dal nome comune del soggetto (tutti applicabili al momento della stesura di questo articolo e Service Fabric versione Runtime 7.1.409):
  - per le nuove connessioni all'interno di e nel cluster, il runtime di Service Fabric troverà e selezionerà il certificato corrispondente con la data di scadenza più lontana (la proprietà' NotAfter ' del certificato, spesso abbreviata come ' na ')
  - le connessioni esistenti verranno mantenute attive/consentite per la scadenza naturale o in caso contrario. un gestore interno riceverà una notifica dell'esistenza di una nuova corrispondenza

Questo si traduce nelle osservazioni importanti seguenti:
  - Il certificato di rinnovo può essere ignorato se la data di scadenza è prima di quella del certificato attualmente in uso.
  - La disponibilità del cluster o delle applicazioni ospitate ha la precedenza sulla direttiva per ruotare il certificato. il cluster convergerà al nuovo certificato alla fine, ma senza garanzie temporali. Ne consegue che:
  - Potrebbe non essere immediatamente ovvio a un osservatore che il certificato ruotato ha completamente sostituito il suo predecessore; l'unico modo per assicurarsi che sia (per i certificati del cluster) per riavviare i computer host. Si noti che non è sufficiente riavviare i nodi Service Fabric, in quanto i componenti in modalità kernel che formano le connessioni di lease in un cluster non saranno interessati. Si noti anche che il riavvio della macchina virtuale/VMSS può causare una perdita temporanea della disponibilità. Per i certificati dell'applicazione è sufficiente riavviare solo le rispettive istanze dell'applicazione.
  - L'introduzione di un certificato ricon chiave che non soddisfi le regole di convalida può interrompere efficacemente il cluster. L'esempio più comune è il caso di un'autorità emittente imprevista: i certificati del cluster sono dichiarati dal nome comune del soggetto con blocco dell'autorità emittente, ma il certificato ruotato è stato emesso da un'autorità emittente nuova/non dichiarata.     

### <a name="certificate-cleanup"></a>Pulizia certificati
Al momento non sono disponibili disposizioni in Azure per la rimozione esplicita dei certificati. Spesso si tratta di un'attività non semplice per determinare se un determinato certificato viene utilizzato in un determinato momento. Questa operazione è più difficile per i certificati dell'applicazione rispetto ai certificati del cluster. Service Fabric se stesso, non è l'agente di provisioning, non eliminerà un certificato dichiarato dall'utente in qualsiasi circostanza. Per i meccanismi di provisioning standard:
  - Verrà eseguito il provisioning dei certificati dichiarati come segreti VM/VMSS a condizione che vi si faccia riferimento nella definizione VM/VMSS e che possano essere recuperati dall'insieme di credenziali. l'eliminazione di un segreto o di un certificato dell'insieme di credenziali avrà esito negativo per le distribuzioni di VM/VMSS successive; Analogamente, la disabilitazione di una versione segreta nell'insieme di credenziali
  - Le versioni precedenti dei certificati di cui è stato effettuato il provisioning tramite l'estensione della macchina virtuale dell'insieme di credenziali delle credenziali possono essere presenti in un nodo VM/VMSS. L'agente recupera e installa solo la versione corrente e non rimuove i certificati. La ricreazione dell'immagine di un nodo, che in genere si verifica ogni mese, reimposterà l'archivio certificati al contenuto dell'immagine del sistema operativo e quindi le versioni precedenti verranno rimosse in modo implicito. Si consideri, tuttavia, che la scalabilità verticale di un set di scalabilità di macchine virtuali comporterà l'installazione solo della versione corrente dei certificati osservati. Non presupporre l'omogeneità dei nodi per quanto riguarda i certificati installati.   

## <a name="simplifying-management---an-autorollover-example"></a>Gestione semplificata-esempio di autorollover
Sono stati descritti i meccanismi, le restrizioni, le regole e le definizioni intricate e sono state apportate previsioni terribili delle interruzioni. È possibile, ad esempio, indicare come configurare la gestione automatica dei certificati per evitare tutti questi problemi. Questa operazione viene eseguita nel contesto di un cluster di Service Fabric di Azure in esecuzione in un set di scalabilità di macchine virtuali PaaSv2, usando Azure Key Vault per la gestione dei segreti e sfruttando le identità gestite, come indicato di seguito:
  - La convalida dei certificati viene modificata dal blocco dell'identificazione personale al soggetto + blocco dell'autorità emittente: qualsiasi certificato con un oggetto specificato da un'autorità emittente specificata è ugualmente attendibile
    - I certificati vengono registrati in e ottenuti da un archivio attendibile (Key Vault) e aggiornati da un agente, in questo caso l'estensione della macchina virtuale dell'insieme di credenziali delle chiavi
    - Il provisioning dei certificati è stato modificato in fase di distribuzione e in base alla versione (come fatto da ComputeRP) per la post-distribuzione e l'uso di URI dell'insieme di credenziali delle credenziali senza versione
    - L'accesso a Vault viene concesso tramite identità gestite assegnate dall'utente; l'identità UA viene creata e assegnata al set di scalabilità di macchine virtuali durante la distribuzione
    - Dopo la distribuzione, l'agente (l'estensione della macchina virtuale KV) eseguirà il polling e aggiornerà i certificati osservati in ogni nodo del set di scalabilità di macchine virtuali. la rotazione del certificato è quindi completamente automatizzata, in quanto SF rileverà automaticamente il certificato valido più lontano

La sequenza è completamente configurabile tramite script e automatizzata e consente una distribuzione iniziale senza tocco da parte dell'utente di un cluster configurato per il rollover automatico del certificato. Di seguito sono riportati i passaggi dettagliati. Verrà usata una combinazione di cmdlet di PowerShell e frammenti di modelli JSON. La stessa funzionalità è realizzabile con tutti i mezzi supportati per interagire con Azure.

[!NOTE] In questo esempio si presuppone che esista già un certificato nell'insieme di credenziali; per la registrazione e il rinnovo di un certificato gestito da un insieme di credenziali di un insieme di credenziali è necessario procedere manualmente come descritto in precedenza in questo articolo. Per gli ambienti di produzione, usare i certificati gestiti dall'insieme di credenziali delle credenziali. uno script di esempio specifico di un'infrastruttura a chiave pubblica interna di Microsoft è incluso di seguito.
Il rollover automatico dei certificati è opportuno solo per i certificati emessi dall'autorità di certificazione. l'uso di certificati autofirmati, inclusi quelli generati durante la distribuzione di un cluster Service Fabric nel portale di Azure, è insensato, ma è ancora possibile per le distribuzioni locali/ospitate dallo sviluppatore, dichiarando l'identificazione personale dell'autorità di certificazione come il certificato foglia.

### <a name="starting-point"></a>Punto di partenza
Per brevità, si presuppone il seguente stato iniziale:
  - Il cluster Service Fabric esiste ed è protetto con un certificato emesso da un'autorità di certificazione dichiarato dall'identificazione personale.
  - Il certificato viene archiviato in un insieme di credenziali ed eseguito il provisioning come segreto del set di scalabilità di macchine virtuali
  - Lo stesso certificato verrà usato per convertire il cluster in dichiarazioni di certificati comuni basate sul nome, quindi può essere convalidato dall'oggetto e dall'emittente; in caso contrario, ottenere il certificato emesso dall'autorità di certificazione destinato a questo scopo e aggiungerlo alla definizione del cluster tramite identificazione personale, come illustrato [qui](service-fabric-cluster-security-update-certs-azure.md) .

Di seguito è riportato un estratto JSON da un modello corrispondente a tale stato. si noti che questa operazione omette molte impostazioni obbligatorie e illustra solo gli aspetti correlati al certificato:
```json
  "resources": [
    {   ## VMSS definition
      "apiVersion": "[variables('vmssApiVersion')]",
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('vmNodeTypeName')]",
      "location": "[variables('computeLocation')]",
      "properties": {
        "virtualMachineProfile": {
          "extensionProfile": {
            "extensions": [
            {
                "name": "[concat('ServiceFabricNodeVmExt','_vmNodeTypeName')]",
                "properties": {
                  "type": "ServiceFabricNode",
                  "autoUpgradeMinorVersion": true,
                  "publisher": "Microsoft.Azure.ServiceFabric",
                  "settings": {
                    "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                    "nodeTypeRef": "[variables('vmNodeTypeName')]",
                    "dataPath": "D:\\SvcFab",
                    "durabilityLevel": "Bronze",
                    "certificate": {
                        "thumbprint": "[parameters('primaryClusterCertificateTP')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                    }
                  },
                  "typeHandlerVersion": "1.1"
                }
            },}},
          "osProfile": {
            "adminPassword": "[parameters('adminPassword')]",
            "adminUsername": "[parameters('adminUsername')]",
            "secrets": [
            {
                "sourceVault": {
                    "id": "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
                },
                "vaultCertificates": [
                {
                    "certificateStore": "[parameters('certificateStoreValue')]",
                    "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
                },
            ]}]
        },
    },
    {   ## cluster definition
        "apiVersion": "[variables('sfrpApiVersion')]",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "certificate": {
            "thumbprint": "[parameters('primaryClusterCertificateTP')]",
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },
    }
  ]
```   

Il precedente dice essenzialmente che il certificato con identificazione personale ```json [parameters('primaryClusterCertificateTP')] ``` e trovato nell'URI dell'insieme di credenziali delle credenziali ```json [parameters('clusterCertificateUrlValue')] ``` viene dichiarato come unico certificato del cluster, dall'identificazione personale. Quindi, verranno configurate le risorse aggiuntive necessarie per garantire il rollover del certificato.

### <a name="setting-up-prerequisite-resources"></a>Impostazione delle risorse dei prerequisiti
Come indicato in precedenza, un certificato di cui viene effettuato il provisioning come segreto del set di scalabilità di macchine virtuali viene recuperato dall'insieme di credenziali dal servizio del provider di risorse Microsoft. Compute, usando la relativa identità iniziale e per conto dell'operatore di distribuzione. Per l'autorollover, che cambierà, si passerà a usare un'identità gestita, assegnata al set di scalabilità di macchine virtuali e a cui vengono concesse le autorizzazioni per i segreti dell'insieme di credenziali.

Tutti gli estratti successivi devono essere distribuiti in maniera concomitante. sono elencati singolarmente per l'analisi e le spiegazioni di riproduzione.

Per prima cosa definire un'identità assegnata dall'utente (i valori predefiniti sono inclusi come esempi). per informazioni aggiornate, vedere la [documentazione ufficiale](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm#create-a-user-assigned-managed-identity) :
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "userAssignedIdentityName": {
      "type": "string",
      "defaultValue": "sftstuaicus",
      "metadata": {
        "description": "User-assigned managed identity name"
      }
    },
  },
  "variables": {
      "vmssApiVersion": "2018-06-01",
      "sfrpApiVersion": "2018-02-01",
      "miApiVersion": "2018-11-30",
      "kvApiVersion": "2018-02-14",
      "userAssignedIdentityResourceId": "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('userAssignedIdentityName'))]"
  },    
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('userAssignedIdentityName')]",
      "apiVersion": "[variables('miApiVersion')]",
      "location": "[resourceGroup().location]"
    },
  ]}
```

Quindi concedere a questa identità l'accesso ai segreti dell'insieme di credenziali. per informazioni aggiornate, vedere la [documentazione ufficiale](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy) :
```json
  "resources":
  [{
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "[variables('kvApiVersion')]",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[reference(variables('userAssignedIdentityResourceId'), variables('miApiVersion')).tenantId]",
            "objectId": "[reference(variables('userAssignedIdentityResourceId'), variables('miApiVersion')).principalId]",
            "dependsOn": [
              "[variables('userAssignedIdentityResourceId')]"
            ],
            "permissions": {
              "secrets": [
                "get",
                "list"
              ]}}]}}]
```

Nel passaggio successivo verrà:
  - assegnare l'identità assegnata dall'utente al set di scalabilità di macchine virtuali
  - dichiarare la dipendenza del set di scalabilità di macchine virtuali per la creazione dell'identità gestita e il risultato della concessione dell'accesso all'insieme di credenziali
  - dichiarare l'estensione della macchina virtuale dell'insieme di credenziali delle credenziali, richiedendo che recuperi i certificati osservati all'avvio ([documentazione ufficiale](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-windows))
  - aggiornare la definizione dell'estensione della macchina virtuale Service Fabric in modo che dipenda dall'estensione KVVM e per convertire il certificato del cluster in un nome comune. queste modifiche verranno apportate in un unico passaggio, poiché rientrano nell'ambito della stessa risorsa.

```json
  "parameters": {
    "kvvmextPollingInterval": {
      "type": "string",
      "defaultValue": "3600",
      "metadata": {
        "description": "kv vm extension polling interval in seconds"
      }
    },
    "kvvmextLocalStoreName": {
      "type": "string",
      "defaultValue": "MY",
      "metadata": {
        "description": "kv vm extension local store name"
      }
    },
    "kvvmextLocalStoreLocation": {
      "type": "string",
      "defaultValue": "LocalMachine",
      "metadata": {
        "description": "kv vm extension local store location"
      }
    },
    "kvvmextObservedCertificates": {
      "type": "array",
      "defaultValue": [
                "https://sftestcus.vault.azure.net/secrets/sftstcncluster",
                "https://sftestcus.vault.azure.net/secrets/sftstcnserver"
            ],
      "metadata": {
        "description": "kv vm extension observed certificates versionless uri"
      }
    },
    "certificateCommonName": {
      "type": "string",
      "defaultValue": "cus.cluster.sftstcn.system.servicefabric.azure-int",
      "metadata": {
        "description": "Certificate Common name"
      }
    },
  },
  "resources": [
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[variables('vmNodeTypeName')]",
    "location": "[variables('computeLocation')]",
    "dependsOn": [
      "[variables('userAssignedIdentityResourceId')]",
      "[concat('Microsoft.KeyVault/vaults/', concat(parameters('keyVaultName'), '/accessPolicies/add'))]"
    ],
    "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "[variables('userAssignedIdentityResourceId')]": {}
      }
    },
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
        {
          "name": "KVVMExtension",
          "properties": {
            "publisher": "Microsoft.Azure.KeyVault",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": "[parameters('kvvmextPollingInterval')]",
                    "linkOnRenewal": false,
                    "observedCertificates": "[parameters('kvvmextObservedCertificates')]",
                    "requireInitialSync": true
                }
            }
          }
        },
        {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeTypeName')]",
        "properties": {
          "type": "ServiceFabricNode",
          "provisionAfterExtensions" : [ "KVVMExtension" ],
          "publisher": "Microsoft.Azure.ServiceFabric",
          "settings": {
            "certificate": {
                "commonNames": [
                    "[parameters('certificateCommonName')]"
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            }
            },
            "typeHandlerVersion": "1.0"
          }
        },
  ] } ## extension profile
  },  ## VM profile
  "osProfile": {
    "adminPassword": "[parameters('adminPassword')]",
    "adminUsername": "[parameters('adminUsername')]",
  } 
  }
  ]
```
Si noti che, sebbene non sia elencato in modo esplicito, l'URL del certificato dell'insieme di credenziali è stato rimosso dalla sezione "OsProfile" del set di scalabilità di macchine virtuali.
L'ultimo passaggio consiste nell'aggiornare la definizione del cluster per modificare la dichiarazione di certificato da identificazione personale a nome comune. qui vengono anche aggiunte le identificazioni personali dell'autorità di certificazione:

```json
  "parameters": {
    "certificateCommonName": {
      "type": "string",
      "defaultValue": "cus.cluster.sftstcn.system.servicefabric.azure-int",
      "metadata": {
        "description": "Certificate Common name"
      }
    },
    "certificateIssuerThumbprint": {
      "type": "string",
      "defaultValue": "1b45ec255e0668375043ed5fe78a09ff1655844d,d7fe717b5ff3593764f4d90654d86e8362ec26c8,3ac7c3cac8de0dd392c02789c8be97474f456960,96ea05926e2e42cc207e358668be2c316857fb5e",
      "metadata": {
        "description": "Certificate issuer thumbprints separated by comma"
      }
    },
  },
  "resources": [
    {
      "apiVersion": "[variables('sfrpApiVersion')]",
      "type": "Microsoft.ServiceFabric/clusters",
      "name": "[parameters('clusterName')]",
      "location": "[parameters('clusterLocation')]",
      "properties": {
        "certificateCommonNames": {
          "commonNames": [{
              "certificateCommonName": "[parameters('certificateCommonName')]",
              "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
          }],
          "x509StoreName": "[parameters('certificateStoreValue')]"
        },
  ]
```

A questo punto, è possibile eseguire gli aggiornamenti indicati in precedenza in un'unica distribuzione; per la sua parte, il servizio del provider di risorse Service Fabric suddividerà l'aggiornamento del cluster in diversi passaggi, come descritto nel segmento sulla [conversione dei certificati del cluster da identificazione digitale al nome comune](cluster-security-certificates.md#converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations).

### <a name="analysis-and-observations"></a>Analisi e osservazioni
Questa sezione è un catch-all per spiegare i passaggi descritti in precedenza, nonché per attirare l'attenzione sugli aspetti importanti.

#### <a name="certificate-provisioning-explained"></a>Provisioning del certificato, illustrato
L'estensione KVVM, come agente di provisioning, viene eseguita in modo continuo in una frequenza predeterminata. Quando non riesce a recuperare un certificato osservato, continuerà a trovarsi nella riga successiva, quindi verrà ibernato fino al ciclo successivo. L'estensione SFVM, come agente di bootstrap del cluster, richiederà i certificati dichiarati prima che il cluster possa essere formato. Questo, a sua volta, indica che l'estensione SFVM può essere eseguita solo dopo il recupero riuscito dei certificati del cluster, indicato qui dalla ```json "provisionAfterExtensions" : [ "KVVMExtension" ]"``` clausola e dall'impostazione dell'estensione KeyVaultVM ```json "requireInitialSync": true``` . Ciò indica all'estensione KVVM che alla prima esecuzione (dopo la distribuzione o un riavvio) deve scorrere i relativi certificati osservati fino a quando tutti non vengono scaricati correttamente. Se si imposta questo parametro su false, associato a un errore di recupero dei certificati del cluster, si verificherà un errore nella distribuzione del cluster. Viceversa, la richiesta di una sincronizzazione iniziale con un elenco errato o non valido di certificati osservati provocherebbe un errore dell'estensione KVVM e, di conseguenza, un errore di distribuzione del cluster.  

#### <a name="certificate-linking-explained"></a>Collegamento al certificato, illustrato
È possibile che sia stato notato il flag ' linkOnRenewal ' dell'estensione KVVM e il fatto che sia impostato su false. Qui viene illustrato in dettaglio il comportamento controllato da questo flag e le sue implicazioni sul funzionamento di un cluster. Si noti che questo comportamento è specifico di Windows.

In base alla [definizione](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-windows#extension-schema):
```json
"linkOnRenewal": <Only Windows. This feature enables auto-rotation of SSL certificates, without necessitating a re-deployment or binding.  e.g.: false>,
```

I certificati usati per stabilire una connessione TLS vengono in genere [acquisiti come handle](https://docs.microsoft.com/windows/win32/api/sspi/nf-sspi-acquirecredentialshandlea) tramite il provider di supporto della sicurezza S-Channel, ovvero il client non accede direttamente alla chiave privata del certificato stesso. S-Channel supporta il reindirizzamento (collegamento) delle credenziali sotto forma di un'estensione di certificato ([CERT_RENEWAL_PROP_ID](https://docs.microsoft.com/windows/win32/api/wincrypt/nf-wincrypt-certsetcertificatecontextproperty#cert_renewal_prop_id)): se questa proprietà è impostata, il valore rappresenta l'identificazione personale del certificato ' rinnovo ', quindi S-Channel tenterà invece di caricare il certificato collegato. Infatti, questo elenco verrà attraversato (ed eventualmente aciclici) fino a quando non viene terminato con il certificato ' final ', uno senza un contrassegno di rinnovo. Questa funzionalità, quando viene usata in maniera oculata, è un'ottima mitigazione dalla perdita di disponibilità causata da certificati scaduti (ad esempio). In altri casi, è possibile che si verifichino interruzioni difficili da diagnosticare e mitigare. S-Channel esegue l'attraversamento dei certificati sulle proprietà di rinnovo in modo incondizionato, indipendentemente dal soggetto, dalle autorità emittenti o da altri attributi specifici che partecipano alla convalida del certificato risultante dal client. È possibile, effettivamente, che il certificato risultante non disponga di una chiave privata associata oppure che la chiave non sia stata ACL al consumer potenziale. 
 
Se è abilitato il collegamento, l'estensione della macchina virtuale dell'insieme di credenziali delle credenziali, al momento del recupero di un certificato osservato dall'insieme di credenziali, tenterà di trovare i certificati esistenti corrispondenti per collegarli tramite la proprietà dell'estensione per il rinnovo. La corrispondenza è (esclusivamente) in base al nome alternativo del soggetto (SAN) e funziona come esemplificato di seguito.
Si supponga che siano presenti due certificati esistenti, come segue: A: CN = "Alice ' s Accessories", SAN = {"alice.universalexports.com"}, Renewal ='' B: CN = "bit di Bob", SAN = {"bob.universalexports.com", "bob.universalexports.net"}, Renewal =''
 
Si supponga che un certificato C venga recuperato da KVVM ext: CN = "Mallory ' s malware", SAN = {"alice.universalexports.com", "bob.universalexports.com", "mallory.universalexports.com"}
 
L'elenco SAN di un oggetto è completamente incluso in C e pertanto A. Renewal = C. identificazione personale; L'elenco SAN di B presenta un'intersezione comune con C, ma non è completamente incluso, quindi B. il rinnovo rimane vuoto.
 
Qualsiasi tentativo di richiamare AcquireCredentialsHandle (S-Channel) in questo stato sul certificato A comporterà effettivamente l'invio di C alla parte remota. Nel caso di Service Fabric, il [sottosistema di trasporto](service-fabric-architecture.md#transport-subsystem) di un cluster USA S-Channel per l'autenticazione reciproca, quindi il comportamento descritto in precedenza influiscono direttamente sulla comunicazione fondamentale del cluster. Continuando l'esempio precedente e supponendo che sia il certificato del cluster, cosa accade dopo dipende da:
  - Se la chiave privata di C non è consentito all'account con cui viene eseguita l'infrastruttura, verranno visualizzati errori di acquisizione della chiave privata (SEC_E_UNKNOWN_CREDENTIALS o simile)
  - Se la chiave privata di C è accessibile, verranno visualizzati gli errori di autorizzazione restituiti dagli altri nodi (CertificateNotMatched, Unauthorized e così via) 
 
In entrambi i casi, il trasporto ha esito negativo e il cluster può diventare inattivo; i sintomi variano. Per peggiorare le cose, il collegamento dipende dall'ordine di rinnovo, che è determinato dall'ordine dell'elenco dei certificati osservati dell'estensione KVVM, dalla pianificazione del rinnovo nell'insieme di credenziali o anche dagli errori temporanei che comportano la modifica dell'ordine di recupero.

Per attenuare tali problemi, è consigliabile:
  - non combinare i SANs di diversi certificati dell'insieme di credenziali; ogni certificato dell'insieme di credenziali deve essere utilizzato in modo distinto e il soggetto e la SAN dovrebbero rifletterla con specificità
  - includere il nome comune del soggetto nell'elenco SAN (letteralmente, "CN = <subject common name> ")  
  - in caso di dubbi, disabilitare il collegamento al rinnovo per i certificati di cui è stato effettuato il provisioning con l'estensione KVVM 

#### <a name="why-use-a-user-assigned-managed-identity-what-are-the-implications-of-using-it"></a>Perché usare un'identità gestita assegnata dall'utente? Quali sono le implicazioni dell'utilizzo?
Come emerso dai frammenti di codice JSON precedenti, è necessaria una sequenza specifica di operazioni/aggiornamenti per garantire la riuscita della conversione e per mantenere la disponibilità del cluster. In particolare, la risorsa del set di scalabilità di macchine virtuali dichiara e usa la propria identità per recuperare i segreti in un singolo aggiornamento (dal punto di vista dell'utente). L'estensione della macchina virtuale Service Fabric (che avvia il cluster) dipende dal completamento dell'estensione della macchina virtuale dell'insieme di credenziali delle credenziali, che dipende dal corretto recupero dei certificati osservati. L'estensione KVVM usa l'identità del set di scalabilità di macchine virtuali per accedere all'insieme di credenziali, il che significa che i criteri di accesso nell'insieme di credenziali devono essere già stati aggiornati prima della distribuzione del set di scalabilità di macchine virtuali. 

Per eliminare la creazione di un'identità gestita o per assegnarla a un'altra risorsa, l'operatore di distribuzione deve avere il ruolo richiesto (ManagedIdentityOperator) nella sottoscrizione o nel gruppo di risorse, oltre ai ruoli necessari per gestire le altre risorse a cui si fa riferimento nel modello. 

Dal punto di vista della sicurezza, ricordare che la macchina virtuale (set di scalabilità) viene considerata un limite di sicurezza per quanto riguarda l'identità di Azure. Ciò significa che tutte le applicazioni ospitate nella macchina virtuale potrebbero, in principio, ottenere un token di accesso che rappresenta i token di accesso di identità gestiti dalla macchina virtuale ottenuti dall'endpoint IMDS non autenticato. Se si considera che la macchina virtuale sia un ambiente condiviso o multi-tenant, probabilmente questo metodo di recupero dei certificati del cluster non è indicato. Si tratta, tuttavia, dell'unico meccanismo di provisioning adatto per il rollover automatico dei certificati.

## <a name="troubleshooting-and-frequently-asked-questions"></a>Risoluzione dei problemi e domande frequenti

*D*: come eseguire la registrazione a livello di codice in un certificato gestito da un insieme di credenziali delle credenziali?
*R*: individuare il nome dell'autorità emittente dalla documentazione dell'insieme di credenziali delle credenziali, quindi sostituirlo nello script seguente.  
```PowerShell
  $issuerName=<depends on your PKI of choice>
    $clusterVault="sftestcus"
    $clusterCertVaultName="sftstcncluster"
    $clusterCertCN="cus.cluster.sftstcn.system.servicefabric.azure-int"
    Set-AzKeyVaultCertificateIssuer -VaultName $clusterVault -Name $issuerName -IssuerProvider $issuerName
    $distinguishedName="CN=" + $clusterCertCN
    $policy = New-AzKeyVaultCertificatePolicy `
        -IssuerName $issuerName `
        -SubjectName $distinguishedName `
        -SecretContentType 'application/x-pkcs12' `
        -Ekus "1.3.6.1.5.5.7.3.1", "1.3.6.1.5.5.7.3.2" `
        -ValidityInMonths 4 `
        -KeyType 'RSA' `
        -RenewAtPercentageLifetime 75        
    Add-AzKeyVaultCertificate -VaultName $clusterVault -Name $clusterCertVaultName -CertificatePolicy $policy
    
    # poll the result of the enrollment
    Get-AzKeyVaultCertificateOperation -VaultName $clusterVault -Name $clusterCertVaultName 
```

*D*: cosa accade quando un certificato viene emesso da un'autorità emittente non dichiarata o non specificata? Dove è possibile ottenere l'elenco completo delle autorità emittenti attive di una determinata infrastruttura a chiave pubblica?
*R*: se la dichiarazione di certificato specifica le identificazioni personali dell'autorità emittente e l'emittente diretta del certificato non è incluso nell'elenco di autorità di certificazione bloccate, il certificato verrà considerato non valido, indipendentemente dal fatto che la relativa radice sia considerata attendibile dal client. Pertanto, è fondamentale assicurarsi che l'elenco di autorità emittenti sia aggiornato o aggiornato. L'introduzione di una nuova autorità emittente è un evento raro e deve essere ampiamente pubblicizzata prima che inizi a emettere certificati. 

In generale, un'infrastruttura a chiave pubblica pubblicherà e gestirà un'istruzione per le procedure di certificazione, in conformità allo [standard IETF RFC 7382](https://tools.ietf.org/html/rfc7382). Tra le altre informazioni, sono incluse tutte le autorità emittenti attive. Il recupero di questo elenco a livello di codice può variare a seconda dell'infrastruttura PKI.   

Per PKI interni Microsoft, consultare la documentazione interna sugli endpoint/SDK utilizzati per recuperare le autorità emittenti autorizzate; è responsabilità del proprietario del cluster esaminare periodicamente questo elenco e verificare che la definizione del cluster includa *tutte le* autorità emittenti previste.

*D*: sono supportati più PKI? 
*R*: Sì; non è possibile dichiarare più voci CN nel manifesto del cluster con lo stesso valore, ma è possibile elencare le autorità emittenti da più PKI corrispondenti allo stesso CN. Non si tratta di una procedura consigliata e le procedure di trasparenza dei certificati possono impedire l'emissione di tali certificati. Tuttavia, come mezzo per eseguire la migrazione da un'infrastruttura a chiave pubblica a un'altra, si tratta di un meccanismo accettabile.

*D*: cosa accade se il certificato del cluster corrente non viene emesso da un'autorità di certificazione o non ha l'oggetto previsto? 
*R*: ottenere un certificato con l'oggetto designato e aggiungerlo alla definizione del cluster come database secondario, per identificazione personale. Una volta completato l'aggiornamento, avviare un altro aggiornamento della configurazione del cluster per convertire la dichiarazione di certificato in un nome comune. 

[Image1]:./media/security-cluster-certificate-mgmt/certificate-journey-thumbprint.png
[Image2]:./media/security-cluster-certificate-mgmt/certificate-journey-common-name.png

