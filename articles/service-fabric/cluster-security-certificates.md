---
title: Autenticazione basata su certificati X. 509 in un cluster Service Fabric
description: Informazioni sull'autenticazione basata su certificati nei cluster Service Fabric e su come rilevare, attenuare e correggere i problemi relativi ai certificati.
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: sfrev
ms.openlocfilehash: 699015e322c599dea996b3a8b9dbc0a4589440ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81429668"
---
# <a name="x509-certificate-based-authentication-in-service-fabric-clusters"></a>Autenticazione basata su certificati X. 509 nei cluster Service Fabric

In questo articolo viene completata l'introduzione alla [sicurezza del cluster Service Fabric](service-fabric-cluster-security.md)e vengono illustrati i dettagli dell'autenticazione basata sui certificati nei cluster Service Fabric. Si presuppone che il lettore abbia familiarità con i concetti fondamentali relativi alla sicurezza e anche con i controlli che Service Fabric espone per controllare la sicurezza di un cluster.  

Argomenti trattati in questo titolo:

* Nozioni fondamentali sull'autenticazione basata su certificati
* Identità e rispettivi ruoli
* Regole di configurazione del certificato
* Risoluzione dei problemi e domande frequenti

## <a name="certificate-based-authentication-basics"></a>Nozioni fondamentali sull'autenticazione basata su certificati
Come breve aggiornamento, in sicurezza, un certificato è uno strumento progettato per associare le informazioni relative a un'entità (oggetto) al loro possesso di una coppia di chiavi crittografiche asimmetriche, quindi costituisce un costrutto di base della crittografia a chiave pubblica. È possibile usare le chiavi rappresentate da un certificato per la protezione dei dati o per dimostrare l'identità dei titolari di chiavi; Quando viene usato insieme a un sistema di infrastruttura a chiave pubblica (PKI), un certificato può rappresentare tratti aggiuntivi dell'oggetto, ad esempio la proprietà di un dominio Internet o determinati privilegi concessi dall'emittente del certificato (noto come autorità di certificazione o CA). Un'applicazione comune di certificati supporta il protocollo di crittografia Transport Layer Security (TLS), consentendo di proteggere le comunicazioni su una rete di computer. In particolare, il client e il server utilizzano i certificati per garantire la riservatezza e l'integrità delle comunicazioni e anche per condurre l'autenticazione reciproca.

In Service Fabric, il livello fondamentale di un cluster (Federazione) si basa anche su TLS (tra gli altri protocolli) per ottenere una rete affidabile e sicura di nodi partecipanti. Le connessioni al cluster tramite Service Fabric API client usano TLS per proteggere il traffico e anche per stabilire le identità delle entità. In particolare, se usato per l'autenticazione in Service Fabric, un certificato può essere usato per dimostrare le seguenti attestazioni: a) il presentatore della credenziale del certificato ha il possesso della chiave privata del certificato b) l'hash SHA-1 del certificato (' identificazione personale ') corrisponde a una dichiarazione inclusa nella definizione del cluster o c) il nome comune del soggetto distinto del certificato corrisponde a una e l'autorità emittente del certificato è nota o attendibile.

Nell'elenco precedente ' b ' è comunemente noto come ' blocco identificazione personale '; in questo caso, la dichiarazione fa riferimento a un certificato specifico e il livello di attendibilità dello schema di autenticazione si basa sul presupposto che non sia fattibile per forgiare un certificato che produce lo stesso valore hash di un altro, pur continuando a essere un oggetto valido e ben formato in tutti gli altri aspetti. L'elemento ' c'rappresenta una forma alternativa di dichiarazione di un certificato, in cui il livello di attendibilità dello schema si basa sulla combinazione dell'oggetto del certificato e dell'autorità emittente. In questo caso, la dichiarazione fa riferimento a una classe di certificati. ogni due certificati con le stesse caratteristiche vengono considerati completamente equivalenti. 

Le sezioni seguenti illustrano in dettaglio il modo in cui il runtime di Service Fabric USA e convalida i certificati per garantire la sicurezza del cluster.

## <a name="identities-and-their-respective-roles"></a>Identità e rispettivi ruoli
Prima di approfondire i dettagli dell'autenticazione o proteggere i canali di comunicazione, è importante elencare gli attori partecipanti e i ruoli corrispondenti che svolgono in un cluster:
- il runtime di Service Fabric, denominato ' System ': il set di servizi che fornisce le astrazioni e le funzionalità che rappresentano il cluster. Quando si fa riferimento alla comunicazione nel cluster tra istanze di sistema, verrà usato il termine "identità cluster"; Quando si fa riferimento al cluster come destinatario/destinazione del traffico dall'esterno del cluster, verrà usato il termine "identità server".
- applicazioni ospitate, dette "applicazioni": codice fornito dal proprietario del cluster, orchestrato ed eseguito nel cluster
- client: entità a cui è consentito connettersi ed eseguire le funzionalità in un cluster in base alla configurazione del cluster. Si distingue tra due livelli di privilegi: "User" e "admin", rispettivamente. Un client "utente" è limitato principalmente alle operazioni di sola lettura, ma non a tutte le funzionalità di sola lettura, mentre un client admin ha accesso illimitato alla funzionalità del cluster. Per informazioni dettagliate, vedere ruoli di [sicurezza in un cluster Service Fabric](service-fabric-cluster-security-roles.md).
- (Solo Azure) i servizi Service Fabric che orchestrano ed espongono controlli per il funzionamento e la gestione di cluster Service Fabric, detti semplicemente "servizio". A seconda dell'ambiente, il ' servizio ' può fare riferimento al provider di risorse Service Fabric di Azure o ad altri provider di risorse di proprietà e gestiti dal team di Service Fabric.

In un cluster sicuro ognuno di questi ruoli può essere configurato con una propria identità distinta, dichiarata come associazione di un nome di ruolo predefinito e delle credenziali corrispondenti. Service Fabric supporta la dichiarazione di credenziali come certificati o entità servizio basata su dominio. Sono supportate anche le identità Windows-/Kerberos-based, ma non rientrano nell'ambito di questo articolo. fare riferimento alla [sicurezza basata su Windows in cluster Service Fabric](service-fabric-windows-cluster-windows-security.md). Nei cluster di Azure i ruoli client possono anche essere dichiarati come [identità basate su Azure Active Directory](service-fabric-cluster-creation-setup-aad.md).

Come accennato in precedenza, il runtime di Service Fabric definisce due livelli di privilegio in un cluster:' admin ' è User '. Un client amministratore e un componente ' System ' funzionano con i privilegi ' admin ' e pertanto non sono distinguibili tra loro. Quando si stabilisce una connessione in/al cluster, un chiamante autenticato viene concesso dal runtime di Service Fabric uno dei due ruoli come base per l'autorizzazione successiva. Nelle sezioni seguenti verrà esaminata l'autenticazione approfondita.

## <a name="certificate-configuration-rules"></a>Regole di configurazione del certificato
### <a name="validation-rules"></a>Regole di convalida
Le impostazioni di sicurezza di un cluster di Service Fabric descrivono, in linea di principio, gli aspetti seguenti:
- tipo di autenticazione. si tratta di una caratteristica non modificabile in fase di creazione del cluster. Esempi di tali impostazioni sono ' ClusterCredentialType ',' ServerCredentialType ' e i valori consentiti sono ' none ',' X509' o ' Windows '. Questo articolo è incentrato sull'autenticazione del tipo X509.
- regole di convalida (autenticazione). Queste impostazioni vengono impostate dal proprietario del cluster e descrivono quali credenziali verranno accettate per un determinato ruolo. Gli esempi verranno esaminati in dettaglio immediatamente sotto.
- impostazioni utilizzate per modificare o modificare in modo impercettibile il risultato dell'autenticazione; gli esempi includono i flag (de-) che limitano l'applicazione degli elenchi di revoche di certificati e così via.

> [!NOTE]
> Gli esempi di configurazione del cluster forniti di seguito sono estratti dal manifesto del cluster in formato XML, come il formato più digest che supporta direttamente le funzionalità di Service Fabric descritte in questo articolo. Le stesse impostazioni possono essere espresse direttamente nelle rappresentazioni JSON di una definizione del cluster, sia che si tratti di un manifesto del cluster JSON autonomo o di un modello Management di risorse di Azure.

Una regola di convalida del certificato comprende gli elementi seguenti:
- ruolo corrispondente: client, client di amministrazione (ruolo con privilegi)
- credenziale accettata per il ruolo, dichiarata tramite identificazione personale o nome comune del soggetto

#### <a name="thumbprint-based-certificate-validation-declarations"></a>Dichiarazioni di convalida del certificato basate su identificazione digitale
Nel caso delle regole di convalida basate sull'identificazione personale, le credenziali presentate da un chiamante che richiede una connessione in/al cluster verranno convalidate come indicato di seguito:
  - la credenziale è un certificato valido e ben formato: la catena può essere compilata, le firme corrispondono
  - il certificato è ora valido (NotBefore <= Now < NotAfter)
  - l'hash SHA-1 del certificato corrisponde alla dichiarazione, come confronto di stringhe senza distinzione tra maiuscole e minuscole, esclusi tutti gli spazi vuoti

Eventuali errori di attendibilità rilevati durante la compilazione o la convalida della catena verranno eliminati per le dichiarazioni basate sull'identificazione personale, ad eccezione dei certificati scaduti, sebbene esistano anche le disposizioni per quel caso. In particolare, gli errori correlati a: stato di revoca sconosciuto o offline, radice non attendibile, utilizzo chiave non valido, catena parziale sono considerati errori non irreversibili; il presupposto, in questo caso, è che il certificato è semplicemente una busta per una coppia di chiavi. la sicurezza risiede nel fatto che il proprietario del cluster ha impostato la misura in posizioni per salvaguardare la chiave privata.

Il seguente Estratto da un manifesto del cluster esemplifica tale set di regole di convalida basate sull'identificazione personale:

```xml
<Section Name="Security">
  <Parameter Name="ClusterCredentialType" Value="X509" />
  <Parameter Name="ServerAuthCredentialType" Value="X509" />
  <Parameter Name="AdminClientCertThumbprints" Value="d5ec...4264" />
  <Parameter Name="ClientCertThumbprints" Value="7c8f...01b0" />
  <Parameter Name="ClusterCertThumbprints" Value="abcd...1234,ef01...5678" />
  <Parameter Name="ServerCertThumbprints" Value="ef01...5678" />
</Section>
```

Ogni voce precedente si riferisce a un'identità specifica, come descritto in precedenza. ogni voce consente inoltre di specificare più valori, come un elenco di stringhe delimitate da virgole. In questo esempio, dopo aver convalidato correttamente le credenziali in ingresso, il presentatore di un certificato con l'identificazione personale SHA-1 5ec... al 4264' verrà concesso il ruolo ' admin '; viceversa, un chiamante che esegue l'autenticazione con il certificato ' 7c8f... a 01B0' verrà concesso un ruolo ' User ', limitato alle operazioni principalmente di sola lettura. Un chiamante in ingresso che presenta un certificato con identificazione personale ' abcd... 1234' o ' EF01... 5678' verrà accettato come nodo peer nel cluster. Infine, un client che si connette a un endpoint di gestione del cluster prevede che l'identificazione personale del certificato del server sia ' EF01... 5678'. 

Come indicato in precedenza, Service Fabric effettua il provisioning dell'accettazione dei certificati scaduti; il motivo è che i certificati hanno una durata limitata e, quando sono dichiarati dall'identificazione personale (che fa riferimento a un'istanza del certificato specifica), per consentire la scadenza di un certificato si verificano errori di connessione al cluster o di un collasso a destra del cluster. È troppo semplice dimenticare o trascurare la rotazione di un certificato con identificazione personale e, sfortunatamente, il ripristino da tale situazione è difficile.

A tale scopo, il proprietario del cluster può dichiarare in modo esplicito che i certificati autofirmati dichiarati dall'identificazione personale saranno considerati validi, come indicato di seguito:

```xml
  <Section Name="Security">
    <Parameter Name="AcceptExpiredPinnedClusterCertificate" Value="true" />
  </Section>
```
Questo comportamento non si estende ai certificati emessi dall'autorità di certificazione. in tal caso, un certificato scaduto, noto a compromesso, può diventare ' valido ' non appena non sarebbe più presente nell'elenco di revoche di certificati della CA e pertanto presenta un rischio per la sicurezza. Con i certificati autofirmati, il proprietario del cluster è considerato l'unico componente responsabile della salvaguardia della chiave privata del certificato, che non è il caso dei certificati emessi dall'autorità di certificazione. il proprietario del cluster potrebbe non essere in grado di riconoscere come o quando il certificato è stato dichiarato compromesso.

#### <a name="common-name-based-certificate-validation-declarations"></a>Dichiarazioni comuni di convalida del certificato basate sul nome
Le dichiarazioni comuni basate sul nome accettano uno dei seguenti formati:
- nome comune del soggetto (solo)
- nome comune del soggetto con blocco dell'autorità emittente

Consideriamo prima di tutto un Estratto da un manifesto del cluster esemplificando entrambi gli stili di dichiarazione:
```xml
    <Section Name="Security/ServerX509Names">
      <Parameter Name="server.demo.system.servicefabric.azure-int" Value="" />
    </Section>
    <Section Name="Security/ClusterX509Names">
      <Parameter Name="cluster.demo.system.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```
Le dichiarazioni fanno riferimento rispettivamente alle identità del server e del cluster. Si noti che le dichiarazioni basate su CN hanno sezioni proprie nel manifesto del cluster, separate dallo standard "Security". In entrambe le dichiarazioni,' name ' rappresenta il nome comune del soggetto distinto del certificato e il campo ' value ' rappresenta l'emittente prevista, come indicato di seguito:

- nel primo caso, la dichiarazione indica che l'elemento nome comune del soggetto distinto del certificato del server deve corrispondere alla stringa "Server. demo. System. servicefabric. Azure-int"; il campo ' valore ' vuoto indica la previsione che la radice della catena di certificati è attendibile nel nodo o nel computer in cui viene convalidato il certificato server. in Windows questo significa che il certificato può concatenarsi a uno dei certificati installati nell'archivio ' CA radice attendibile ';
- nel secondo caso, la dichiarazione indica che il presentatore di un certificato viene accettato come nodo peer nel cluster se il nome comune del certificato corrisponde alla stringa "cluster. demo. System. servicefabric. Azure-int" *e* l'identificazione personale dell'emittente diretta del certificato corrisponde a una delle voci separate da virgole nel campo ' value '. (Questo tipo di regola è comunemente noto come "nome comune con blocco dell'autorità emittente").

In entrambi i casi, la catena del certificato viene compilata e dovrebbe essere priva di errori; ovvero errori di revoca, catene parziali o temporali. gli errori di attendibilità non validi sono considerati irreversibili e la convalida del certificato avrà esito negativo. Se si bloccano le autorità emittenti, si considera lo stato "radice non attendibile" come errore non irreversibile. Nonostante l'aspetto, si tratta di una forma più restrittiva di convalida, perché consente al proprietario del cluster di vincolare il set di autorità emittenti autorizzate/accettate alla propria infrastruttura a chiave pubblica (PKI).

Una volta compilata la catena di certificati, viene convalidata in base a un criterio TLS/SSL standard con il soggetto dichiarato come nome remoto; un certificato verrà considerato una corrispondenza se il nome comune del soggetto o uno dei nomi alternativi del soggetto corrisponde alla dichiarazione CN del manifesto del cluster. In questo caso sono supportati i caratteri jolly e la corrispondenza tra stringhe non fa distinzione tra maiuscole e minuscole.

(È necessario chiarire che la sequenza descritta in precedenza può essere eseguita per ogni tipo di utilizzo della chiave dichiarato dal certificato; se il certificato specifica l'utilizzo della chiave di autenticazione client, la catena viene compilata e valutata per prima per un ruolo client. In caso di esito positivo, la valutazione viene completata e la convalida ha esito positivo. Se il certificato non dispone dell'utilizzo di autenticazione client o la convalida non è riuscita, il runtime di Service Fabric compilerà e valuterà la catena per l'autenticazione server.

Per completare l'esempio, nell'estratto seguente viene illustrato come dichiarare i certificati client in base al nome comune:
```xml
    <Section Name="Security/AdminClientX509Names">
      <Parameter Name="admin.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
    <Section Name="Security/ClientX509Names">
      <Parameter Name="user.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```

Le dichiarazioni precedenti corrispondono rispettivamente alle identità di amministratore e utente; la convalida dei certificati dichiarati in questo modo è esattamente come descritto negli esempi precedenti di certificati del cluster e del server.

> [!NOTE]
> Le dichiarazioni comuni basate sul nome sono destinate a semplificare la rotazione e, in generale, alla gestione dei certificati del cluster. Tuttavia, si consiglia di attenersi ai consigli seguenti per garantire la disponibilità e la sicurezza continue del cluster:
  * preferire l'aggiunta di autorità di certificazione per basarsi sulle radici attendibili
  * evitare di combinare autorità emittenti da PKI diversi
  * Verificare che tutte le autorità emittenti previste siano elencate nella dichiarazione del certificato. una mancata corrispondenza dell'emittente provocherà una convalida non riuscita
  * Verificare che gli endpoint dei criteri di certificazione dell'infrastruttura a chiave pubblica siano individuabili, disponibili e accessibili, ovvero che gli endpoint AIA, CRL o OCSP siano dichiarati nel certificato foglia e che siano accessibili in modo che la compilazione della catena di certificati possa essere completata.

Unendo tutti gli elementi, quando si riceve una richiesta di connessione in un cluster protetto con certificati X. 509, il runtime di Service Fabric utilizzerà le impostazioni di sicurezza del cluster per convalidare le credenziali della parte remota, come descritto in precedenza. in caso di esito positivo, il chiamante o la parte remota viene considerata autenticata. Se la credenziale corrisponde a più regole di convalida, il runtime consentirà al chiamante il ruolo con privilegi più elevati di qualsiasi regola corrispondente. 

### <a name="presentation-rules"></a>Regole presentazione
Nella sezione precedente è stata descritta la modalità di funzionamento dell'autenticazione in un cluster protetto da certificati; in questa sezione viene illustrato come il runtime di Service Fabric individua e carica i certificati utilizzati per la comunicazione nel cluster. Queste regole sono denominate "Presentation".

Come per le regole di convalida, le regole di presentazione specificano un ruolo e la dichiarazione delle credenziali associate, espresse in base all'identificazione personale o al nome comune. Diversamente dalle regole di convalida, le dichiarazioni comuni basate sul nome non dispongono di disposizioni per il blocco dell'autorità emittente. Ciò consente una maggiore flessibilità e prestazioni migliori. Le regole di presentazione sono dichiarate nelle sezioni ' NodeType ' del manifesto del cluster, per ogni tipo di nodo distinto; le impostazioni vengono suddivise dalle sezioni di sicurezza del cluster per consentire a ogni tipo di nodo di avere la configurazione completa in un'unica sezione. Nei cluster di Service Fabric di Azure, per impostazione predefinita le dichiarazioni dei certificati del tipo di nodo corrispondono alle impostazioni corrispondenti nella sezione sicurezza della definizione del cluster.

#### <a name="thumbprint-based-certificate-presentation-declarations"></a>Dichiarazioni di presentazione del certificato basate su identificazione digitale
Come descritto in precedenza, il runtime di Service Fabric distingue tra il ruolo del peer degli altri nodi nel cluster e come server per le operazioni di gestione dei cluster. In linea di principio, queste impostazioni possono essere configurate in modo distinto, ma in pratica tendono a essere allineate. Nella parte restante di questo articolo si presuppone che le impostazioni corrispondano alla semplicità.

Si prenda in considerazione il seguente Estratto da un manifesto del cluster:
```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindByThumbprint" X509FindValue="cc71...1984" X509FindValueSecondary="49e2...19d6" X509StoreName="my" Name="ClusterCertificate" />
        <ServerCertificate X509FindValue="cc71...1984" Name="ServerCertificate" />
        <ClientCertificate X509FindValue="cc71...1984" Name="ClientCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```
L'elemento ' ClusterCertificate ' Mostra lo schema completo, inclusi i parametri facoltativi (' X509FindValueSecondary ') o quelli con le impostazioni predefinite appropriate (' X509StoreName '); le altre dichiarazioni mostrano una forma abbreviata. La dichiarazione di certificato del cluster precedente indica che le impostazioni di sicurezza dei nodi di tipo ' nt1vm ' sono inizializzate con il certificato ' CC71.. 1984' come Primary è 49e2.. 19d6' Certificate As the secondary; si prevede che entrambi i certificati siano disponibili nell'archivio\'certificati LocalMachine My (o nel percorso equivalente di Linux, *var/lib/sfcerts*).

#### <a name="common-name-based-certificate-presentation-declarations"></a>Dichiarazioni di presentazione di certificati comuni basate sul nome
I certificati del tipo di nodo possono anche essere dichiarati dal nome comune del soggetto, come esemplificato di seguito:

```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindBySubjectName" X509FindValue="demo.cluster.azuredocpr.system.servicefabric.azure-int" Name="ClusterCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```

Per entrambi i tipi di dichiarazione, un nodo Service Fabric leggerà la configurazione all'avvio, individua e caricherà i certificati specificati e li ordina in ordine decrescente dell'attributo NotAfter; i certificati scaduti vengono ignorati e il primo elemento dell'elenco viene selezionato come credenziale client per qualsiasi Service Fabric connessione tentata da questo nodo. (In effetti, Service Fabric predilige il certificato in scadenza più lontano).

Si noti che, per le dichiarazioni di presentazione basate su nome comune, un certificato viene considerato una corrispondenza se il nome comune del soggetto è uguale al campo X509FindValue (o X509FindValueSecondary) della dichiarazione come un confronto esatto tra maiuscole e minuscole. Si tratta di una differenza rispetto alle regole di convalida, che supporta la corrispondenza con caratteri jolly, oltre ai confronti di stringhe senza distinzione tra maiuscole e minuscole.  

### <a name="miscellaneous-certificate-configuration-settings"></a>Impostazioni di configurazione del certificato varie
È stato indicato in precedenza che le impostazioni di sicurezza di un cluster di Service Fabric consentono anche di modificare leggermente il comportamento del codice di autenticazione. Mentre l'articolo sulle [impostazioni del cluster Service Fabric](service-fabric-cluster-fabric-settings.md) rappresenta l'elenco completo e più aggiornato delle impostazioni, si espanderà il significato di una selezione di alcune impostazioni di sicurezza, per completare l'esposizione completa sull'autenticazione basata sui certificati. Per ogni impostazione verrà illustrato l'intento, il valore predefinito o il comportamento, il modo in cui influiscono sull'autenticazione e quali valori sono accettabili.

Come indicato in precedenza, la convalida del certificato implica sempre la compilazione e la valutazione della catena del certificato. Per i certificati emessi dall'autorità di certificazione, questa chiamata API del sistema operativo apparentemente semplice in genere comporta diverse chiamate in uscita a diversi endpoint dell'infrastruttura PKI emittente, memorizzazione nella cache delle risposte e così via. Data la prevalenza delle chiamate di convalida dei certificati in un cluster di Service Fabric, eventuali problemi negli endpoint dell'infrastruttura a chiave pubblica possono comportare una riduzione della disponibilità del cluster o di una ripartizione di tipo Outright. Sebbene le chiamate in uscita non possano essere eliminate (vedere più avanti nella sezione Domande frequenti), è possibile usare le impostazioni seguenti per mascherare gli errori di convalida causati da chiamate CRL non riuscite.

  * CrlCheckingFlag-sotto la sezione ' Security ', stringa convertita in UINT. Il valore di questa impostazione viene usato da Service Fabric per mascherare gli errori di stato della catena di certificati modificando il comportamento della compilazione della catena; viene passato alla chiamata [CertGetCertificateChain](https://docs.microsoft.com/windows/win32/api/wincrypt/nf-wincrypt-certgetcertificatechain) CryptoAPI Win32 come parametro ' dwFlags ' e può essere impostato su qualsiasi combinazione valida di flag accettati dalla funzione. Il valore 0 impone al runtime di Service Fabric di ignorare gli eventuali errori di stato di attendibilità. questa operazione non è consigliata, in quanto l'utilizzo costituisce un'esposizione significativa alla sicurezza. Il valore predefinito è 0x40000000 (CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT).

  Quando usare: per i test locali, con certificati autofirmati o certificati per sviluppatori che non sono completamente formati/non dispongono di un'infrastruttura a chiave pubblica appropriata per supportare i certificati. Può anche usare come mitigazione negli ambienti gapped durante la transizione tra PKI.

  Come usare: verrà illustrato un esempio che impone il controllo della revoca per accedere solo agli URL memorizzati nella cache. Assumendo
  ```C++
  #define CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY         0x80000000
  ```
  la dichiarazione nel manifesto del cluster diventa:
  ```xml
    <Section Name="Security">
      <Parameter Name="CrlCheckingFlag" Value="0x80000000" />
    </Section>
  ```

  * IgnoreCrlOfflineError-sotto la sezione "Security", booleano con il valore predefinito "false". Rappresenta un collegamento per la disattivazione di uno stato di errore di compilazione della catena "revoca offline" (o uno stato di errore di convalida del criterio Chain successivo).

  Quando usare: test locali o con certificati per sviluppatori non supportati da un'infrastruttura a chiave pubblica corretta. Usare come mitigazione negli ambienti gapped o quando l'infrastruttura a chiave pubblica è nota come inaccessibile.

  Come usare:
  ```xml
    <Section Name="Security">
      <Parameter Name="IgnoreCrlOfflineError" Value="true" />
    </Section>
  ```

  Altre impostazioni rilevanti (tutte sotto la sezione "sicurezza"):
  * AcceptExpiredPinnedClusterCertificate-descritto nella sezione dedicata alla convalida del certificato basata sull'identificazione personale; consente di accettare certificati del cluster autofirmati scaduti. 
  * CertificateExpirySafetyMargin-Interval, espresso in minuti prima del timestamp NotAfter del certificato e durante il quale il certificato viene considerato a rischio per la scadenza. Service Fabric monitora i certificati del cluster ed emette periodicamente report sull'integrità sulla disponibilità rimanente. All'interno dell'intervallo di sicurezza, questi report sull'integrità vengono elevati allo stato "avviso". Il valore predefinito è 30 giorni.
  * CertificateHealthReportingInterval: controlla la frequenza dei report sull'integrità relativi alla validità del tempo rimanente dei certificati del cluster. I report verranno emessi solo una volta per questo intervallo. Il valore è espresso in secondi e il valore predefinito è 8 ore.
  * EnforcePrevalidationOnSecurityChanges-Boolean, controlla il comportamento dell'aggiornamento del cluster al rilevamento delle modifiche delle impostazioni di sicurezza. Se il valore è impostato su "true", l'aggiornamento del cluster tenterà di garantire che almeno uno dei certificati corrispondenti a una delle regole di presentazione possa superare una regola di convalida corrispondente. La pre-convalida viene eseguita prima che le nuove impostazioni vengano applicate a qualsiasi nodo, ma viene eseguito solo sul nodo che ospita la replica primaria del servizio Gestione cluster al momento dell'avvio dell'aggiornamento. Al momento della stesura di questo articolo, l'impostazione ha il valore predefinito ' false ' e verrà impostato su' true ' per i nuovi cluster Service Fabric di Azure con una versione di runtime che inizia con 7,1.
 
### <a name="end-to-end-scenario-examples"></a>Scenario end-to-end (esempi)
Sono state esaminate le regole di presentazione, le regole di convalida e i flag di modifica, ma come funziona tutti insieme? In questa sezione verranno illustrati due esempi end-to-end che dimostrano come è possibile sfruttare le impostazioni di sicurezza per gli aggiornamenti sicuri del cluster. Si noti che non si tratta di una dissertazione completa sulla corretta gestione dei certificati in Service Fabric, cercare un articolo complementare su tale argomento.

La separazione delle regole di presentazione e di convalida pone la domanda ovvia (o la preoccupazione) se può divergere e quali sono le conseguenze. È effettivamente possibile che la selezione di un nodo di un certificato di autenticazione non superi le regole di convalida di un altro nodo. Questa discrepanza è infatti la principale fonte di eventi imprevisti correlati all'autenticazione. Allo stesso tempo, la separazione di queste regole consente a un cluster di continuare a funzionare durante un aggiornamento che modifica le impostazioni di sicurezza del cluster. Si tenga presente che, aumentando prima le regole di convalida come primo passaggio, tutti i nodi del cluster convergeranno sulle nuove impostazioni continuando a usare le credenziali correnti. 

Si tenga presente che, in un cluster Service Fabric, un aggiornamento passa attraverso (fino a 5) "domini di aggiornamento" o "UDs". Solo i nodi nell'oggetto UD corrente vengono aggiornati/modificati in un determinato momento e l'aggiornamento proseguirà con il successivo UD solo se la disponibilità del cluster lo consente. Per altri dettagli, vedere [Service Fabric aggiornamenti del cluster](service-fabric-cluster-upgrade.md) e altri articoli sullo stesso argomento. Le modifiche al certificato o alla sicurezza sono particolarmente rischiose, poiché possono isolare i nodi dal cluster o lasciare il cluster alla periferia della perdita del quorum.

Per descrivere le impostazioni di sicurezza di un nodo, si userà la notazione seguente:

NK: {P:{TP = A}, V:{TP = A}}, dove:
  - ' NK ' rappresenta un nodo nel dominio di aggiornamento *k*
  - ' P ' rappresenta le regole di presentazione correnti del nodo (presupponendo che ci si riferisca solo ai certificati del cluster); 
  - ' V'rappresenta le regole di convalida correnti del nodo (solo certificato cluster)
  - ' TP = A ' rappresenta una dichiarazione basata sull'identificazione digitale (TP), con ' A ' che è un'identificazione personale del certificato
  - ' CN = B ' rappresenta una dichiarazione in base al nome comune (CN) con ' B ' come nome comune del soggetto del certificato 

#### <a name="rotating-a-cluster-certificate-declared-by-thumbprint"></a>Rotazione di un certificato del cluster dichiarato dall'identificazione personale
La sequenza seguente descrive il modo in cui è possibile usare un aggiornamento in 2 fasi per introdurre in modo sicuro un certificato del cluster secondario, dichiarato dall'identificazione personale; la prima fase introduce la nuova dichiarazione di certificato nelle regole di convalida e la seconda fase la introduce nelle regole di presentazione:
  - stato iniziale: N0 = {P:{TP = A}, V:{TP = A}},... NK = {P:{TP = A}, V:{TP = A}}-il cluster è inattivo, tutti i nodi condividono una configurazione comune
  - al termine dell'aggiornamento, dominio 0: N0 = {P:{TP = A}, V:{TP = A, TP = B}}... NK = {P:{TP = A}, V:{TP = A}}-i nodi in UD0 presenteranno il certificato A e accettano i certificati A o B; tutti gli altri nodi presenti e accettano solo un certificato
  - al termine dell'ultimo dominio di aggiornamento: N0 = {P:{TP = A}, V:{TP = A, TP = B}}... NK = {P:{TP = A}, V:{TP = A, TP = B}}-tutti i nodi presentano il certificato A, tutti i nodi accettano il certificato A o B
      
A questo punto, il cluster è di nuovo in equilibrio e la seconda fase delle impostazioni di sicurezza di aggiornamento/modifica può iniziare:
  - al termine dell'aggiornamento del dominio 0: N0 = {P:{TP = A, TP = B}, V:{TP = A, TP = B}}... NK = {P:{TP = A}, V:{TP = A, TP = B}}-i nodi in UD0 inizieranno a presentare B, che viene accettato da qualsiasi altro nodo del cluster.
  - al termine dell'ultimo dominio di aggiornamento: N0 = {P:{TP = A, TP = B}, V:{TP = A, TP = B}},... NK = {P:{TP = A, TP = B}, V:{TP = A, TP = B}}-tutti i nodi sono passati alla presentazione del certificato B. il certificato A può ora essere ritirato/rimosso dalla definizione del cluster con un set di aggiornamenti successivo.

#### <a name="converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations"></a>Conversione di un cluster da un'identificazione personale a dichiarazioni di certificati basate su nome comune
Analogamente, la modifica del tipo di dichiarazione del certificato (dall'identificazione digitale al nome comune) seguirà lo stesso modello indicato in precedenza. Si noti che le regole di convalida consentono di dichiarare i certificati di un determinato ruolo sia dall'identificazione personale che dal nome comune nella stessa definizione del cluster. Al contrario, tuttavia, le regole di presentazione consentono solo una forma di dichiarazione. Per inciso, l'approccio sicuro alla conversione di un certificato del cluster dall'identificazione personale al nome comune è quello di introdurre prima il certificato di destinazione per identificazione personale e quindi di modificare la dichiarazione in un nome comune. Nell'esempio seguente si presuppone che l'identificazione personale "A" e il nome comune del soggetto "B" facciano riferimento allo stesso certificato. 

  - stato iniziale: N0 = {P:{TP = A}, V:{TP = A}},... NK = {P:{TP = A}, V:{TP = A}}-il cluster è inattivo, tutti i nodi condividono una configurazione comune, con un'identificazione personale del certificato primaria
  - al termine dell'aggiornamento, dominio 0: N0 = {P:{TP = A}, V:{TP = A, CN = B}}... NK = {P:{TP = A}, V:{TP = A}}-i nodi in UD0 presenteranno il certificato A e accettano i certificati con l'identificazione personale A o il nome comune B; tutti gli altri nodi presenti e accettano solo un certificato
  - al termine dell'ultimo dominio di aggiornamento: N0 = {P:{TP = A}, V:{TP = A, CN = B}},... NK = {P:{TP = A}, V:{TP = A, CN = B}}-tutti i nodi presentano il certificato A, tutti i nodi accettano il certificato A (TP) o B (CN)

A questo punto è possibile procedere con la modifica delle regole di presentazione con un aggiornamento successivo:
  - al termine dell'aggiornamento, dominio 0: N0 = {P:{CN = B}, V:{TP = A, CN = B}}... NK = {P:{TP = A}, V:{TP = A, CN = B}}-i nodi in UD0 presenteranno il certificato B trovato dal CN e accettano i certificati con l'identificazione personale A o il nome comune B; tutti gli altri nodi presenti e accettano solo un certificato, selezionato per identificazione personale
  - al termine dell'ultimo dominio di aggiornamento: N0 = {P:{CN = B}, V:{TP = A, CN = B}},... NK = {P:{CN = B}, V:{TP = A, CN = B}}-tutti i nodi presentano il certificato B trovato dal CN, tutti i nodi accettano il certificato A (TP) o B (CN)
    
Il completamento della fase 2 contrassegna anche la conversione del cluster in certificati comuni basati sul nome; le dichiarazioni di convalida basate sull'identificazione personale possono essere rimosse in un aggiornamento cluster successivo.

> [!NOTE]
> Nei cluster di Azure Service Fabric i flussi di lavoro presentati sopra sono orchestrati dal provider di risorse Service Fabric. il proprietario del cluster è ancora responsabile del provisioning dei certificati nel cluster in base alle regole indicate (presentazione o convalida) ed è consigliato per eseguire le modifiche in più passaggi.

In un articolo separato si affronterà l'argomento relativo alla gestione e al provisioning dei certificati in un cluster Service Fabric.

## <a name="troubleshooting-and-frequently-asked-questions"></a>Risoluzione dei problemi e domande frequenti
Sebbene il debug dei problemi relativi all'autenticazione nei cluster Service Fabric non sia semplice, gli hint e i suggerimenti seguenti potrebbero essere utili. Il modo più semplice per iniziare le indagini consiste nell'esaminare i registri eventi Service Fabric nei nodi del cluster, non necessariamente solo quelli che mostrano i sintomi, ma anche i nodi che non sono in grado di connettersi a uno dei loro vicini. In Windows, gli eventi di importanza vengono in genere registrati rispettivamente nei canali "Applications and Services Logs\Microsoft-ServiceFabric\Admin" o "Operational". In alcuni casi può essere utile [abilitare la registrazione CAPI2](https://docs.microsoft.com/archive/blogs/benjaminperkins/enable-capi2-event-logging-to-troubleshoot-pki-and-ssl-certificate-issues), acquisire altri dettagli relativi alla convalida del certificato, al recupero di CRL/CTL e così via. ricordarsi di disabilitarla dopo aver completato la procedura di riproduzione, può essere piuttosto dettagliata.

I sintomi tipici che si manifestano in un cluster che riscontrano problemi di autenticazione sono: 
  - nodi inattivi/cicli 
  - tentativi di connessione rifiutati
  - timeout di tentativi di connessione

Ognuno dei sintomi potrebbe essere causato da problemi diversi e la stessa causa radice può presentare diverse manifestazioni; in questo modo, è possibile elencare solo un piccolo campione di problemi tipici, con consigli per correggerli. 

* I nodi possono scambiare messaggi ma non possono connettersi. È possibile che i tentativi di connessione vengano terminati sia l'errore ' certificato non corrispondente '-una delle entità in una connessione Service Fabric-Service Fabric presenta un certificato che non supera le regole di convalida del destinatario. Può essere accompagnato da uno degli errori seguenti: 
  ```C++
  0x80071c44    -2147017660 FABRIC_E_SERVER_AUTHENTICATION_FAILED
  ```
  Per diagnosticare/esaminare ulteriormente: in ogni nodo che tenta la connessione determinare quale certificato viene presentato; esaminare il certificato e provare a emulare le regole di convalida (verificare l'identificazione personale o l'uguaglianza dei nomi comuni, se specificato, controllare l'identificazione personale dell'autorità emittente).

  Un altro codice di errore comune può essere:
  ```C++
  0x800b0109    -2146762487 CERT_E_UNTRUSTEDROOT
  ```
  In questo caso, il certificato viene dichiarato dal nome comune e si applica una delle condizioni seguenti:
    - le autorità emittenti non sono bloccate e il certificato radice non è attendibile oppure
    - le autorità emittenti sono bloccate, ma la dichiarazione non include l'identificazione personale dell'emittente diretta del certificato.

* Un nodo è attivo, ma non è in grado di connettersi ad altri nodi. gli altri nodi non ricevono il traffico in ingresso dal nodo che ha avuto esito negativo. In questo caso, è possibile che il caricamento del certificato abbia esito negativo nel nodo locale. Individuare gli errori seguenti:
  - certificato non trovato: assicurarsi che i certificati dichiarati nelle regole di presentazione possano essere risolti dal contenuto dell'archivio certificati LocalMachine\My (o come specificato). 
    Possibili cause di errore possono includere: 
      - caratteri non validi nella dichiarazione di identificazione personale
      - il certificato non è installato
      - il certificato è scaduto
      - la dichiarazione di nome comune include il prefisso ' CN ='
      - la dichiarazione specifica un carattere jolly e non esiste alcuna corrispondenza esatta nell'archivio certificati (dichiarazione: CN = *. dominio. com, certificato effettivo: CN = Server. dominio. com)

  - credenziali sconosciute: indica una chiave privata mancante corrispondente al certificato, in genere accompagnata dal codice di errore: 
    ```C++ 
    0x8009030d  -2146893043 SEC_E_UNKNOWN_CREDENTIALS
    0x8009030e  -2146893042 SEC_E_NO_CREDENTIALS
    ```
    Per risolvere il problema, verificare l'esistenza della chiave privata; Verificare che a SFAdmins sia stato concesso l'accesso ' Read | Execute ' alla chiave privata.

  - tipo di provider errato: indica un certificato CNG (Crypto New Generation) ("provider di archiviazione chiavi software Microsoft"); a questo punto, Service Fabric supporta solo certificati CAPI1. Generalmente accompagnato dal codice di errore:
    ```C++
    0x80090014  -2146893804 NTE_BAD_PROV_TYPE
    ```
    Per risolvere il problema, ricreare il certificato del cluster usando un provider CAPI1, ad esempio "Microsoft Enhanced RSA and AES Cryptographic Provider". Per ulteriori informazioni sui provider di crittografia, vedere [informazioni sui provider di servizi di crittografia](https://docs.microsoft.com/windows/win32/seccertenroll/understanding-cryptographic-providers)

