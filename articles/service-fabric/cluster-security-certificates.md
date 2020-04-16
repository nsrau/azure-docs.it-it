---
title: Autenticazione basata su certificati X.509 in un cluster di service fabric
description: Informazioni sull'autenticazione basata su certificati nei cluster di Service Fabric e su come rilevare, ridurre e risolvere i problemi relativi ai certificati.
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: sfrev
ms.openlocfilehash: 699015e322c599dea996b3a8b9dbc0a4589440ab
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429668"
---
# <a name="x509-certificate-based-authentication-in-service-fabric-clusters"></a>Autenticazione basata su certificati X.509 nei cluster di Service Fabric

Questo articolo illustra l'introduzione alla sicurezza del [cluster di Service Fabric](service-fabric-cluster-security.md)e illustra i dettagli dell'autenticazione basata su certificati nei cluster di Service Fabric. Si presuppone che il lettore abbia familiarità con i concetti fondamentali relativi alla sicurezza e anche con i controlli esposti da Service Fabric per controllare la sicurezza di un cluster.  

Argomenti trattati sotto questo titolo:

* Nozioni di base sull'autenticazione basata su certificati
* Identità e rispettivi ruoli
* Regole di configurazione dei certificati
* Risoluzione dei problemi e domande frequenti

## <a name="certificate-based-authentication-basics"></a>Nozioni di base sull'autenticazione basata su certificati
Come breve aggiornamento, in sicurezza, un certificato è uno strumento destinato a associare le informazioni relative a un'entità (il soggetto) al loro possesso di una coppia di chiavi crittografiche asimmetriche e costituisce pertanto un costrutto di base di crittografia a chiave pubblica. Le chiavi rappresentate da un certificato possono essere utilizzate per proteggere i dati o per dimostrare l'identità dei titolari di chiavi; se utilizzato in combinazione con un sistema di infrastruttura a chiave pubblica (PKI, Public Key Infrastructure), un certificato può rappresentare ulteriori caratteristiche del soggetto, ad esempio la proprietà di un dominio Internet o determinati privilegi concessi dall'autorità emittente del certificato (nota come Autorità di certificazione o CA). Un'applicazione comune di certificati supporta il protocollo crittografico TLS (Transport Layer Security), consentendo comunicazioni protette su una rete di computer. In particolare, il client e il server utilizzano certificati per garantire la privacy e l'integrità della loro comunicazione, nono per condurre l'autenticazione reciproca.

In Service Fabric, il livello fondamentale di un cluster (Federazione) si basa anche su TLS (tra gli altri protocolli) per ottenere una rete affidabile e sicura di nodi partecipanti. Le connessioni al cluster tramite le API client di Service Fabric utilizzano anche TLS per proteggere il traffico e per stabilire le identità delle entità. In particolare, se utilizzato per l'autenticazione in Service Fabric, un certificato può essere utilizzato per dimostrare le seguenti attestazioni: a) il presentatore della credenziale del certificato è in possesso della chiave privata del certificato b) l'hash SHA-1 del certificato ('thumbprint') corrisponde a una dichiarazione inclusa nella definizione del cluster o c) il nome comune distinto del soggetto corrisponde a una dichiarazione inclusa nella definizione del cluster e l'autorità emittente del certificato è nota o attendibile.

Nell'elenco precedente, 'b' è colloquialmente noto come 'pinning di impronte librariche'; in questo caso, la dichiarazione si riferisce a un certificato specifico e la forza dello schema di autenticazione si basa sulla premessa che è computazionalmente impossibile falsificare un certificato che produce lo stesso valore hash di un altro, pur essendo un oggetto valido e ben formato sotto tutti gli altri aspetti. La voce 'c' rappresenta una forma alternativa di dichiarazione di un certificato, in cui la solidità del regime si basa sulla combinazione del soggetto del certificato e dell'autorità emittente. In questo caso, la dichiarazione fa riferimento a una classe di certificati: due certificati con le stesse caratteristiche sono considerati completamente equivalenti. 

Nelle sezioni seguenti verrà illustrato in modo approfondito come il runtime di Service Fabric utilizza e convalida i certificati per garantire la sicurezza del cluster.

## <a name="identities-and-their-respective-roles"></a>Identità e rispettivi ruoli
Prima di entrare nei dettagli dell'autenticazione o proteggere i canali di comunicazione, è importante elencare gli attori partecipanti e i ruoli corrispondenti che svolgono in un cluster:
- il runtime di Service Fabric, denominato 'system': il set di servizi che forniscono le astrazioni e le funzionalità che rappresentano il cluster. Quando si fa riferimento alla comunicazione in-cluster tra istanze di sistema, si userà il termine 'identità del cluster'; quando si fa riferimento al cluster come destinatario/destinazione del traffico proveniente dall'esterno del cluster, verrà utilizzato il termine 'identità del server'.
- applicazioni ospitate, denominate 'applicazioni': codice fornito dal proprietario del cluster, orchestrato ed eseguito nel cluster
- client: entità autorizzate a connettersi ed eseguire funzionalità in un cluster, in base alla configurazione del cluster. Distinguiamo tra due livelli di privilegi: rispettivamente "utente" e "amministratore". Un client 'utente' è limitato principalmente alle operazioni di sola lettura (ma non a tutte le funzionalità di sola lettura), mentre un client 'admin' ha accesso illimitato alle funzionalità del cluster. Per altri dettagli, vedere [Ruoli di sicurezza in un cluster di Service Fabric.](service-fabric-cluster-security-roles.md)
- (solo Azure) i servizi service Fabric che orchestrano ed espongono i controlli per il funzionamento e la gestione dei cluster di Service Fabric, denominati semplicemente "servizio". A seconda dell'ambiente, il 'servizio' può fare riferimento al provider di risorse di Azure Service Fabric o ad altri provider di risorse di proprietà e gestiti dal team di Service Fabric.

In un cluster protetto, ognuno di questi ruoli può essere configurato con la propria identità distinta, dichiarata come associazione di un nome di ruolo predefinito e della credenziale corrispondente. Service Fabric supporta la dichiarazione delle credenziali come certificati o entità servizio basata su dominio. (Le identità basate su Windows/Kerberos sono anche supportate, ma non rientrano nell'ambito di questo articolo, fare riferimento alla sicurezza basata su [Windows nei cluster di Service Fabric.)](service-fabric-windows-cluster-windows-security.md) Nei cluster di Azure i ruoli client possono anche essere dichiarati come [identità basate su Azure Active Directory.](service-fabric-cluster-creation-setup-aad.md)

Come accennato in precedenza, il runtime di Service Fabric definisce due livelli di privilegio in un cluster: 'admin' e 'user'. Un client di amministratore e un componente di "sistema" opererebbero entrambi con privilegi di amministratore e quindi non sono distinguibili l'uno dall'altro. Dopo aver stabilito una connessione in/al cluster, un chiamante autenticato verrà concesso dal runtime di Service Fabric uno dei due ruoli come base per l'autorizzazione successiva. Nelle sezioni seguenti verrà esaminata l'autenticazione in modo approfondito.

## <a name="certificate-configuration-rules"></a>Regole di configurazione dei certificati
### <a name="validation-rules"></a>Regole di convalida
Le impostazioni di sicurezza di un cluster di Service Fabric descrivono, in linea di principio, gli aspetti seguenti:The security settings of a Service Fabric cluster describe, in principle, the following aspects:
- il tipo di autenticazione; questa è una caratteristica del cluster, tempo di creazione e immutabile. Esempi di tali impostazioni sono 'ClusterCredentialType', 'ServerCredentialType' e i valori consentiti sono 'none', 'x509' o 'windows'. Questo articolo è incentrato sull'autenticazione di tipo x509.This article focuses on the x509-type authentication.
- le regole di convalida (autenticazione); queste impostazioni vengono impostate dal proprietario del cluster e descrivono quali credenziali devono essere accettate per un determinato ruolo. Gli esempi saranno esaminati in profondità immediatamente sotto.
- impostazioni utilizzate per modificare o modificare leggermente il risultato dell'autenticazione; Esempi di codice includono l'applicazione di flag (de-)che limitano gli elenchi di revoche di certificati e così via.

> [!NOTE]
> Esempi di configurazione del cluster forniti di seguito sono estratti dal manifesto del cluster in formato XML, come il formato più digested che supporta direttamente la funzionalità di Service Fabric descritta in questo articolo. Le stesse impostazioni possono essere espresse direttamente nelle rappresentazioni JSON di una definizione di cluster, sia che si tratti di un manifesto del cluster json autonomo o di un modello di Gestione risorse di Azure.The same settings can be expressed directly in the JSON representations of a cluster definition, whether a standalone json cluster manifest, or an Azure Resource Mangement template.

Una regola di convalida del certificato comprende gli elementi seguenti:A certificate validation rule comprises the following elements:
- il ruolo corrispondente: client, client amministratore (ruolo privilegiato)
- le credenziali accettate per il ruolo, dichiarate tramite identificazione personale o nome comune soggetto

#### <a name="thumbprint-based-certificate-validation-declarations"></a>Dichiarazioni di convalida del certificato basate su identificazione personaleThumbprint-based certificate validation declarations
Nel caso di regole di convalida basate sull'identificazione personale, le credenziali presentate da un chiamante che richiede una connessione in/al cluster verranno convalidate come segue:
  - la credenziale è un certificato valido e ben formato: la sua catena può essere costruita, le firme corrispondono
  - il certificato è valido per l'ora (NotBefore <- ora < NotAfter)
  - l'hash SHA-1 del certificato corrisponde alla dichiarazione, come confronto tra stringhe senza distinzione tra maiuscole e minuscole escludendo tutti gli spazi vuoti

Eventuali errori di attendibilità rilevati durante la creazione o la convalida della catena verranno soppressi per le dichiarazioni basate sull'identificazione personale, ad eccezione dei certificati scaduti, anche se esistono disposizioni anche per tale caso. In particolare, gli errori correlati a: stato di revoca è sconosciuto o offline, radice non attendibile, utilizzo della chiave non valido, catena parziale sono considerati errori non irreversibili; la premessa, in questo caso, è che il certificato è semplicemente una busta per una coppia di chiavi - la sicurezza sta nel fatto che il proprietario del cluster ha impostato in luoghi per salvaguardare la chiave privata.

L'estratto seguente da un manifesto del cluster esemplifica un set di regole di convalida basate sull'identificazione personale:The following excerpt from a cluster manifest exemplifies such a set of thumbprint-based validation rules:

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

Ognuna delle voci precedenti si riferisce a un'identità specifica come descritto in precedenza; ogni voce consente inoltre di specificare più valori, come elenco di stringhe separate da virgole. In questo esempio, dopo aver convalidato correttamente le credenziali in ingresso, il presentatore di un certificato con identificazione personale SHA-1 'd5ec... 4264' riceverà il ruolo di "amministratore"; al contrario, un chiamante che esegue l'autenticazione con il certificato '7c8f... 01b0' riceverà un ruolo di "utente", limitato alle operazioni principalmente di sola lettura. Chiamante in ingresso che presenta un certificato la cui identificazione personale è 'abcd... 1234' o 'ef01... 5678' verrà accettato come nodo peer nel cluster. Infine, un client che si connette a un endpoint di gestione del cluster si aspetta che l'identificazione personale del certificato server sia 'ef01... 5678'. 

Come accennato in precedenza, Service Fabric effettua disposizioni per l'accettazione di certificati scaduti; il motivo è che i certificati hanno una durata limitata e, quando vengono dichiarati dall'identificazione personale (che fa riferimento a un'istanza specifica del certificato), consentendo la scadenza di un certificato comporterà la mancata connessione al cluster o un crollo definitivo del cluster. È fin troppo facile dimenticare o trascurare la rotazione di un certificato con identificazione personale, e purtroppo il recupero da una situazione del genere è difficile.

A tal fine, il proprietario del cluster può dichiarare in modo esplicito che i certificati autofirmati dichiarati dall'identificazione personale sono considerati validi, come indicato di seguito:The cluster owner can explicitly state that self-signed certificates declared by thumbprint are considered valid, as follows:

```xml
  <Section Name="Security">
    <Parameter Name="AcceptExpiredPinnedClusterCertificate" Value="true" />
  </Section>
```
Questo comportamento non si estende ai certificati emessi dalla CA; in tal caso, un certificato scaduto revocato e noto, da essere compromesso, potrebbe diventare "valido" non appena non figura più nell'elenco di revoche di certificati della CA e quindi presentare un rischio per la sicurezza. Con i certificati autofirmati, il proprietario del cluster è considerato l'unica parte responsabile della protezione della chiave privata del certificato, che non è il caso dei certificati emessi dalla CA: il proprietario del cluster potrebbe non essere a conoscenza di come o quando il certificato è stato dichiarato compromesso.

#### <a name="common-name-based-certificate-validation-declarations"></a>Dichiarazioni comuni di convalida dei certificati basati su nomeCommon name-based certificate validation declarations
Le dichiarazioni basate su nomi comuni assumono uno dei seguenti formati:
- nome comune del soggetto (solo)
- nome comune del soggetto con emittente pinning

Consideriamo innanzitutto un estratto da un manifesto del cluster che esemplifica entrambi gli stili di dichiarazione:
```xml
    <Section Name="Security/ServerX509Names">
      <Parameter Name="server.demo.system.servicefabric.azure-int" Value="" />
    </Section>
    <Section Name="Security/ClusterX509Names">
      <Parameter Name="cluster.demo.system.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```
Le dichiarazioni fanno riferimento rispettivamente alle identità del server e del cluster; Si noti che le dichiarazioni basate su CN hanno le proprie sezioni nel manifesto del cluster, separate dallo standard 'Security'. In entrambe le dichiarazioni, il 'Nome' rappresenta il nome comune del soggetto distinto del certificato e il campo 'Valore' rappresenta l'autorità emittente prevista, come indicato di seguito:

- nel primo caso, la dichiarazione indica che l'elemento del nome comune del distinto oggetto del certificato server deve corrispondere alla stringa "server.demo.system.servicefabric.azure-int"; il campo vuoto 'Valore' indica l'aspettativa che la radice della catena di certificati sia attendibile nel nodo/computer in cui viene convalidato il certificato server; in Windows, ciò significa che il certificato può concatenarsi a qualsiasi certificato installato nell'archivio 'CA radice attendibile';
- nel secondo caso, la dichiarazione indica che il presentatore di un certificato viene accettato come nodo peer nel cluster se il nome comune del certificato corrisponde alla stringa "cluster.demo.system.servicefabric.azure-int" *e* l'identificazione personale dell'autorità emittente diretta del certificato corrisponde a una delle voci separate da virgole nel campo 'Value'. (Questo tipo di regola è colloquialmente noto come 'nome comune con blocco dell'autorità emittente'.)

In entrambi i casi, la catena del certificato viene compilata e si prevede che sia priva di errori; ovvero gli errori di revoca, la catena parziale o gli errori di attendibilità non validi nel tempo vengono considerati irreversibili e la convalida del certificato avrà esito negativo. Il blocco degli emittenti comporterà l'considering dello stato "radice non attendibile" come un errore non irreversibile. nonostante le apparenze, si tratta di una forma più rigorosa di convalida, in quanto consente al proprietario del cluster di vincolare il set di emittenti autorizzati/accettati alla propria infrastruttura PKI.

Dopo aver creato la catena di certificati, viene convalidata rispetto a un criterio TLS/SSL standard con il soggetto dichiarato come nome remoto. un certificato verrà considerato una corrispondenza se il nome comune del soggetto o uno dei relativi nomi alternativi del soggetto corrisponde alla dichiarazione CN dal manifesto del cluster. In questo caso sono supportati i caratteri jolly e la corrispondenza delle stringhe non fa distinzione tra maiuscole e minuscole.

(Dobbiamo chiarire che la sequenza descritta in precedenza potrebbe essere eseguita per ogni tipo di utilizzo della chiave dichiarato dal certificato; se il certificato specifica l'utilizzo della chiave di autenticazione client, la catena viene compilata e valutata prima per un ruolo client. In caso di esito positivo, la valutazione viene completata e la convalida ha esito positivo. Se il certificato non dispone dell'utilizzo dell'autenticazione client o la convalida non è riuscita, il runtime di Service Fabric creerà e valuterà la catena per l'autenticazione del server.)

Per completare l'esempio, nell'estratto seguente viene illustrata la dichiarazione dei certificati client in base al nome comune:
```xml
    <Section Name="Security/AdminClientX509Names">
      <Parameter Name="admin.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
    <Section Name="Security/ClientX509Names">
      <Parameter Name="user.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```

Le dichiarazioni di cui sopra corrispondono rispettivamente alle identità admin e user; la convalida dei certificati dichiarati in questo modo è esattamente come descritto per gli esempi precedenti, dei certificati cluster e server.

> [!NOTE]
> Le dichiarazioni basate sui nomi comuni hanno lo scopo di semplificare la rotazione e, in generale, la gestione dei certificati del cluster. Tuttavia, si consiglia di rispettare le seguenti raccomandazioni per garantire la disponibilità continua e la sicurezza del cluster:
  * preferiscono emittente pinning a fare affidamento su radici attendibili
  * evitare di mescolare emittenti di MMI diverse
  * assicurarsi che tutte le autorità di certificazione previste siano elencate nella dichiarazione del certificato; un'emittente non corrispondente comporterà una convalida non riuscita
  * assicurarsi che gli endpoint dei criteri dei certificati dell'infrastruttura PKI siano individuabili, disponibili e accessibili, ovvero che gli endpoint AIA, CRL o OCSP siano dichiarati nel certificato foglia e che siano accessibili in modo che la creazione della catena di certificati possa essere completata.

Legando tutto insieme, dopo aver ricevuto una richiesta di connessione in un cluster protetto con certificati X.509, il runtime di Service Fabric utilizzerà le impostazioni di sicurezza del cluster per convalidare le credenziali della parte remota come descritto in precedenza; in caso di esito positivo, il chiamante/parte remota viene considerato autenticato. Se la credenziale corrisponde a più regole di convalida, il runtime concederà al chiamante il ruolo con privilegi più elevati di una delle regole corrispondenti. 

### <a name="presentation-rules"></a>Regole di presentazione
Nella sezione precedente è stato descritto il funzionamento dell'autenticazione in un cluster protetto da certificati. in questa sezione verrà illustrato come il runtime di Service Fabric individua e carica i certificati utilizzati per la comunicazione all'in-cluster. le chiamiamo regole di "presentazione".

Come per le regole di convalida, le regole di presentazione specificano un ruolo e la dichiarazione di credenziali associata, espressa dall'identificazione personale o dal nome comune. A differenza delle regole di convalida, le dichiarazioni basate su nomi comuni non dispongono di disposizioni per l'aggiunta dell'emittente; ciò consente una maggiore flessibilità e prestazioni migliori. Le regole di presentazione vengono dichiarate nelle sezioni 'NodeType' del manifesto del cluster, per ogni tipo di nodo distinto. le impostazioni vengono suddivise dalle sezioni Sicurezza del cluster per consentire a ogni tipo di nodo di avere la configurazione completa in un'unica sezione. Nei cluster di Azure Service Fabric, le dichiarazioni di certificato del tipo di nodo vengono predefinite alle impostazioni corrispondenti nella sezione Sicurezza della definizione del cluster.

#### <a name="thumbprint-based-certificate-presentation-declarations"></a>Dichiarazioni di presentazione del certificato basate su identificazione personaleThumbprint-based certificate presentation declarations
Come descritto in precedenza, il runtime di Service Fabric distingue il ruolo come peer di altri nodi nel cluster e come server per le operazioni di gestione del cluster. In linea di principio, queste impostazioni possono essere configurate distintamente, ma in pratica tendono ad allinearsi. Per il resto di questo articolo, si presuppone che le impostazioni corrispondano per semplicità.

Consideriamo il seguente estratto da un manifesto del cluster:
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
L'elemento 'ClusterCertificate' illustra lo schema completo, inclusi i parametri facoltativi ('X509FindValueSecondary') o quelli con i valori predefiniti appropriati ('X509StoreName'); le altre dichiarazioni mostrano una forma abbreviata. La dichiarazione del certificato del cluster precedente indica che le impostazioni di sicurezza dei nodi di tipo 'nt1vm' vengono inizializzate con il certificato 'cc71.The cluster certificate declaration above states that the security settings of nodes of type 'nt1vm' are initialized with certificate 'cc71.The cluster certificate declaration above states that the security settings of nodes of type 'nt1vm' are initialized with certificate 'cc71 1984' come primario, e il '49e2.. 19d6' come secondario; entrambi i certificati devono essere trovati nell'archivio certificati LocalMachine\'My' (o il percorso equivalente Linux, *var/lib/sfcerts*).

#### <a name="common-name-based-certificate-presentation-declarations"></a>Dichiarazioni comuni di presentazione del certificato basato su nome
I certificati di tipo nodo possono anche essere dichiarati in base al nome comune del soggetto, come indicato di seguito:The node type certificates can also be declared by subject common name, as examplelified below:

```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindBySubjectName" X509FindValue="demo.cluster.azuredocpr.system.servicefabric.azure-int" Name="ClusterCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```

Per entrambi i tipi di dichiarazione, un nodo di Service Fabric leggerà la configurazione all'avvio, localizzerà e caricherà i certificati specificati e li ordinerà in ordine decrescente del relativo attributo NotAfter; I certificati scaduti vengono ignorati e il primo elemento dell'elenco viene selezionato come credenziale client per qualsiasi connessione di Service Fabric tentata da questo nodo. In effetti, Service Fabric favorisce il certificato in scadenza più lontano.

Si noti che, per le dichiarazioni di presentazione basate su nome comune, un certificato viene considerato una corrispondenza se il relativo nome comune del soggetto è uguale al campo X509FindValue (o X509FindValueSecondary) della dichiarazione come confronto esatto tra stringhe con distinzione tra maiuscole e minuscole. Ciò è in contrasto con le regole di convalida, che supporta la corrispondenza con caratteri jolly, nonché confronti tra stringhe senza distinzione tra maiuscole e minuscole.  

### <a name="miscellaneous-certificate-configuration-settings"></a>Impostazioni di configurazione dei certificati varie
È stato menzionato in precedenza che le impostazioni di sicurezza di un cluster di Service Fabric consentono anche di modificare sottilmente il comportamento del codice di autenticazione. Mentre l'articolo sulle impostazioni del cluster di [Service Fabric](service-fabric-cluster-fabric-settings.md) rappresenta l'elenco completo e più aggiornato delle impostazioni, espanderemo il significato di alcune delle impostazioni di sicurezza selezionate qui, per completare l'esposizione completa sull'autenticazione basata su certificati. Per ogni impostazione, spiegheremo la finalità, il valore/comportamento predefinito, come influisce sull'autenticazione e quali valori sono accettabili.

Come accennato, la convalida del certificato implica sempre la creazione e la valutazione della catena del certificato. Per i certificati emessi dalla CA, questa chiamata all'API del sistema operativo apparentemente semplice comporta in genere diverse chiamate in uscita a vari endpoint dell'infrastruttura PKI emissione, memorizzazione nella cache delle risposte e così via. Data la prevalenza delle chiamate di convalida dei certificati in un cluster di Service Fabric, eventuali problemi negli endpoint dell'infrastruttura PKI possono comportare una minore disponibilità del cluster o la suddivisione a titolo definitivo. Mentre le chiamate in uscita non possono essere soppresse (vedere di seguito nella sezione domande frequenti per ulteriori informazioni su questo), le seguenti impostazioni possono essere utilizzate per mascherare gli errori di convalida causati da chiamate CRL non riuscite.

  * CrlCheckingFlag - nella sezione 'Security', stringa convertita in UINT. Il valore di questa impostazione viene utilizzato da Service Fabric per mascherare gli errori di stato della catena di certificati modificando il comportamento della creazione della catena; viene passato alla chiamata Win32 CryptoAPI [CertGetCertificateChain](https://docs.microsoft.com/windows/win32/api/wincrypt/nf-wincrypt-certgetcertificatechain) come parametro 'dwFlags' e può essere impostato su qualsiasi combinazione valida di flag accettati dalla funzione. Il valore 0 impone al runtime di Service Fabric di ignorare eventuali errori di stato di attendibilità, operazione non consigliata, in quanto il suo utilizzo costituirebbe un'esposizione significativa alla sicurezza. Il valore predefinito è 0x40000000 (CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT).

  Quando usare: per i test locali, con certificati autofirmati o certificati per sviluppatori non completamente formati o che non dispongono di un'infrastruttura a chiave pubblica appropriata per supportare i certificati. Può anche essere usato come mitigazione in ambienti air-gapped durante la transizione tra PKI.

  Come usare: verrà fornito un esempio che forza il controllo di revoca ad accedere solo agli URL memorizzati nella cache. Assumendo:
  ```C++
  #define CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY         0x80000000
  ```
  quindi la dichiarazione nel manifesto del cluster diventa:
  ```xml
    <Section Name="Security">
      <Parameter Name="CrlCheckingFlag" Value="0x80000000" />
    </Section>
  ```

  * IgnoreCrlOfflineError - nella sezione 'Security', boolean con un valore predefinito 'false'. Rappresenta un collegamento per l'eliminazione di uno stato di errore di creazione della catena 'revoche offline' (o di uno stato di errore di convalida dei criteri della catena successivo).

  Quando utilizzare: test locali o con certificati per sviluppatori non supportati da un'infrastruttura PKI appropriata. Utilizzare come mitigazione in ambienti con aria sbarrata o quando l'infrastruttura PKI è nota per essere inaccessibile.

  Modalità d'uso:
  ```xml
    <Section Name="Security">
      <Parameter Name="IgnoreCrlOfflineError" Value="true" />
    </Section>
  ```

  Altre impostazioni degne di nota (tutte nella sezione "Sicurezza"):
  * AcceptExpiredPinnedClusterCertificate - illustrato nella sezione dedicata alla convalida del certificato basata sull'identificazione personale. consente di accettare certificati cluster autofirmati scaduti. 
  * CertificateExpirySafetyMargin - intervallo, espresso in minuti prima del timestamp NotAfter del certificato e durante il quale il certificato viene considerato a rischio per la scadenza. Service Fabric monitora i certificati del cluster ed emette periodicamente i report sull'integrità rimanente. All'interno dell'intervallo di "sicurezza", questi rapporti sanitari sono elevati allo stato di "avviso". Il valore predefinito è 30 giorni.
  * CertificateHealthReportingInterval : controlla la frequenza dei report di integrità relativi alla validità del tempo rimanente dei certificati del cluster. I report verranno generati una sola volta per questo intervallo. Il valore è espresso in secondi, con un valore predefinito di 8 ore.
  * EnforcePrevalidationOnSecurityChanges - booleano, controlla il comportamento dell'aggiornamento del cluster in seguito al rilevamento delle modifiche delle impostazioni di sicurezza. Se impostato su "true", l'aggiornamento del cluster tenterà di garantire che almeno uno dei certificati corrispondenti a una delle regole di presentazione possa superare una regola di convalida corrispondente. La pre-convalida viene eseguita prima che le nuove impostazioni vengano applicate a qualsiasi nodo, ma viene eseguito solo sul nodo che ospita la replica primaria del servizio Gestione cluster al momento dell'avvio dell'aggiornamento. Al di questi tempi di scrittura, l'impostazione ha un valore predefinito 'false' e verrà impostata su 'true' per i nuovi cluster di Azure Service Fabric con una versione di runtime a partire da 7.1.As of this writing, the setting has a default of 'false' and will be set to 'true' for new Azure Service Fabric clusters with a runtime version starting with 7.1.
 
### <a name="end-to-end-scenario-examples"></a>Scenario end-to-end (esempi)
Abbiamo esaminato le regole di presentazione, le regole di convalida e i flag di modifica, ma come funziona tutto questo insieme? In questa sezione verranno illustrati due esempi end-to-end che illustrano come è possibile sfruttare le impostazioni di sicurezza per gli aggiornamenti sicuri dei cluster. Si noti che non si tratta di una tesi completa sulla gestione corretta dei certificati in Service Fabric, cercare un articolo complementare su tale argomento.

La separazione delle regole di presentazione e convalida pone l'ovvia domanda (o preoccupazione) se possono divergere e quali sarebbero le conseguenze. È infatti possibile che la selezione di un certificato di autenticazione da parte di un nodo non superi le regole di convalida di un altro nodo. Infatti, questa discrepanza è la causa principale degli incidenti relativi all'autenticazione. Allo stesso tempo, la separazione di queste regole consente a un cluster di continuare a funzionare durante un aggiornamento che modifica le impostazioni di sicurezza del cluster. Si consideri che, aumentando prima le regole di convalida come primo passaggio, tutti i nodi del cluster convergeranno sulle nuove impostazioni pur utilizzando le credenziali correnti. 

Tenere presente che, in un cluster di Service Fabric, un aggiornamento procede attraverso (fino a 5) 'domini di aggiornamento' o UD. Solo i nodi nell'UD corrente vengono aggiornati/modificati in un determinato momento e l'aggiornamento procederà all'UD successiva solo se la disponibilità del cluster lo consente. Per ulteriori dettagli, fare riferimento agli aggiornamenti del cluster di [Service Fabric](service-fabric-cluster-upgrade.md) e ad altri articoli sullo stesso argomento. Le modifiche al certificato/sicurezza sono particolarmente rischiose, poiché possono isolare i nodi dal cluster o lasciare il cluster al limite della perdita del quorum.

Utilizzeremo la notazione seguente per descrivere le impostazioni di sicurezza di un nodo:

Nk: "P: "TP" , V:
  - 'Nk' rappresenta un nodo nel dominio di aggiornamento *k*
  - 'P' rappresenta le regole di presentazione correnti del nodo (supponendo che si faccia riferimento solo ai certificati del cluster); 
  - 'V' rappresenta le regole di convalida correnti del nodo (solo certificato cluster)'V' represents the node's current validation rules (cluster certificate only)
  - 'TP' rappresenta una dichiarazione basata sull'identificazione personale (TP), con 'A' che è un'identificazione personale del certificato
  - La dichiarazione basata sul nome comune (CN) del certificato rappresenta una dichiarazione basata sul nome comune (CN" , con il nome comune del soggetto del certificato 

#### <a name="rotating-a-cluster-certificate-declared-by-thumbprint"></a>Rotazione di un certificato del cluster dichiarato dall'identificazione personale
Nella sequenza seguente viene descritto come utilizzare un aggiornamento in due fasi per introdurre in modo sicuro un certificato del cluster secondario, dichiarato dall'identificazione personale. la prima fase introduce la nuova dichiarazione del certificato nelle regole di convalida e la seconda fase la introduce nelle regole di presentazione:
  - Stato iniziale: N0 , P: , TP , V: , TP , , , , , , , - il cluster è a riposo, tutti i nodi condividono una configurazione comune
  - al momento del completamento dell'aggiornamento del dominio 0: N0 , P: , TP , V: Nk : P: TP , V: , TP , a , i nodi in UD0 presenteranno il certificato A e accetteranno i certificati A o B; tutti gli altri nodi presentano e accettano solo il certificato A
  - dopo aver completato l'ultimo dominio di aggiornamento: N0 , P: , TP , V: - tutti i nodi presenti il certificato A, tutti i nodi accetterebbero il certificato A o B
      
A questo punto, il cluster è di nuovo in equilibrio e la seconda fase dell'aggiornamento/modifica delle impostazioni di sicurezza può iniziare:
  - al momento del completamento dell'aggiornamento del dominio 0: N0 , P: , TP , TP , B , V, TP , A , TP , B , , , , , , , Nk - "P:"TP", V: " TP , TP " B , - i nodi in UD0 inizieranno a presentare B, che viene accettato da qualsiasi altro nodo del cluster.
  - al momento del completamento dell'ultimo dominio di aggiornamento: N0 : P: , TP , TP , B , V, TP , A , TP , B , , , , , , , , - tutti i nodi sono passati alla presentazione del certificato B. Il certificato A può ora essere ritirato/rimosso dalla definizione del cluster con un successivo set di aggiornamenti.

#### <a name="converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations"></a>Conversione di un cluster da dichiarazioni di certificati basati su thumbprint a common-name
Analogamente, la modifica del tipo di dichiarazione del certificato (dall'identificazione personale al nome comune) seguirà lo stesso modello di cui sopra. Si noti che le regole di convalida consentono di dichiarare i certificati di un determinato ruolo sia tramite identificazione personale che con nome comune nella stessa definizione di cluster. Al contrario, però, le regole di presentazione consentono una sola forma di dichiarazione. Per inciso, l'approccio sicuro per convertire un certificato del cluster dall'identificazione personale al nome comune consiste nell'introdurre il certificato di destinazione desiderato prima tramite identificazione personale e quindi modificare tale dichiarazione in uno basato su nome comune. Nell'esempio seguente si presuppone che l'identificazione personale "A" e il nome comune del soggetto 'B' facciano riferimento allo stesso certificato. 

  - Stato iniziale: N0 , P: , TP , V: , TP , , , , , , , - il cluster è a riposo, tutti i nodi condividono una configurazione comune, con A l'identificazione personale del certificato primario.
  - al momento del completamento dell'aggiornamento del dominio 0: N0 , P: , TP , V: Nk : "P:"TP", V:'TP'A'- i nodi in UD0 presenteranno il certificato A e accetteranno certificati con identificazione personale A o nome comune B; tutti gli altri nodi presentano e accettano solo il certificato A
  - dopo aver completato l'ultimo dominio di aggiornamento: N0 , P: , TP , V: - tutti i nodi presenti il certificato A - tutti i nodi accetterebbero il certificato A (TP) o B (CN)

A questo punto possiamo procedere con la modifica delle regole di presentazione con un successivo aggiornamento:
  - al momento del completamento dell'aggiornamento del dominio 0: N0 , : . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . Nk : P: TP , V: , TP , CN , B , - i nodi in UD0 presenteranno il certificato B trovato da CN e accetteranno certificati con identificazione personale A o nome comune B; tutti gli altri nodi presentano e accettano solo il certificato A, selezionato in base all'identificazione personale
  - dopo aver completato l'ultimo dominio di aggiornamento: N0 , P: , CN , B , V: , TP , CN , B , , , , , , - tutti i nodi presentano il certificato B trovato da CN, tutti i nodi accetterebbero il certificato A (TP) o B (CN)
    
Il completamento della fase 2 contrassegna anche la conversione del cluster in certificati basati su nomi comuni; le dichiarazioni di convalida basate sull'identificazione personale possono essere rimosse in un successivo aggiornamento del cluster.

> [!NOTE]
> In Azure Service Fabric clusters, the workflows presented above are orchestrated by the Service Fabric Resource Provider; il proprietario del cluster è comunque responsabile del provisioning dei certificati nel cluster in base alle regole indicate (presentazione o convalida) ed è incoraggiato a eseguire modifiche in più passaggi.

In un articolo separato verrà affrontato l'argomento della gestione e del provisioning dei certificati in un cluster di Service Fabric.In a separate article we will address the topic of managing and provisioning certificates into a Service Fabric cluster.

## <a name="troubleshooting-and-frequently-asked-questions"></a>Risoluzione dei problemi e domande frequenti
Anche se il debug dei problemi relativi all'autenticazione nei cluster di Service Fabric non è facile, siamo fiduciosi che i suggerimenti e i suggerimenti seguenti possono essere utili. Il modo più semplice per avviare le indagini consiste nell'esaminare i registri eventi di Service Fabric nei nodi del cluster, non necessariamente solo quelli che mostrano sintomi, ma anche i nodi che si trovano ma non sono in grado di connettersi a uno dei loro vicini. In Windows, gli eventi significativi vengono in genere registrati rispettivamente nei canali 'Registri applicazioni e servizi' o 'Amministrazione', rispettivamente. A volte può essere utile [abilitare la registrazione CAPI2](https://docs.microsoft.com/archive/blogs/benjaminperkins/enable-capi2-event-logging-to-troubleshoot-pki-and-ssl-certificate-issues), per acquisire ulteriori dettagli relativi alla convalida del certificato, al recupero di CRL/CTL e così via (ricorda di disabilitarlo dopo aver completato la riproduzione, può essere abbastanza dettagliato).)

I sintomi tipici che si manifestano in un cluster che presentano problemi di autenticazione sono:Typical symptoms that manifest themselves in a cluster experiencing authentication issues are: 
  - nodi sono in attivo/in bicicletta 
  - i tentativi di connessione vengono rifiutati
  - tentativi di connessione sono il timeout

Ognuno dei sintomi può essere causato da problemi diversi, e la stessa causa principale può mostrare manifestazioni diverse; come tale, ci limiteremo elencare un piccolo campione di problemi tipici, con consigli per risolverli. 

* I nodi possono scambiare messaggi ma non connettersi. Una possibile causa per i tentativi di connessione da terminare è l'errore 'certificato non corrispondente' - una delle parti in un Service Fabric-to- Service Fabric connessioni presenta un certificato che non supera le regole di convalida del destinatario. Può essere accompagnato da uno dei seguenti errori: 
  ```C++
  0x80071c44    -2147017660 FABRIC_E_SERVER_AUTHENTICATION_FAILED
  ```
  Per diagnosticare/indagare ulteriormente: su ciascuno dei nodi che tentano la connessione, determinare quale certificato viene presentato; esaminare il certificato e provare a emulare le regole di convalida (verificare l'identificazione personale o l'uguaglianza dei nomi comuni, controllare le identificazioni personale dell'autorità di certificazione se specificate).

  Un altro codice di errore di accompagnamento comune può essere:
  ```C++
  0x800b0109    -2146762487 CERT_E_UNTRUSTEDROOT
  ```
  In questo caso, il certificato viene dichiarato con il nome comune e si applica una delle condizioni seguenti:
    - le autorità di certificazione non sono bloccate e il certificato radice non è attendibile, o
    - le autorità di certificazione sono bloccate, ma la dichiarazione non include l'identificazione personale dell'autorità emittente diretta di questo certificato

* Un nodo è in su, ma non può connettersi ad altri nodi; altri nodi non ricevono traffico in ingresso dal nodo in errore. In questo caso, è possibile che il caricamento del certificato non riesca nel nodo locale. Cercare i seguenti errori:
  - certificate not found - assicurarsi che i certificati dichiarati nelle regole di presentazione possano essere risolti dal contenuto dell'archivio certificati LocalMachine-My (o come specificato). 
    Le possibili cause di errore possono includere:Possible causes for failure may include: 
      - caratteri non validi nella dichiarazione di identificazione personale
      - il certificato non è installato
      - il certificato è scaduto
      - la dichiarazione del nome comune include il prefisso 'CN'
      - la dichiarazione specifica un carattere jolly e non esiste alcuna corrispondenza esatta nell'archivio dei certificati (dichiarazione: CN .mydomain.com, certificato effettivo: CN .

  - credenziali sconosciute - indica una chiave privata mancante corrispondente al certificato, in genere accompagnata da codice di errore: 
    ```C++ 
    0x8009030d  -2146893043 SEC_E_UNKNOWN_CREDENTIALS
    0x8009030e  -2146893042 SEC_E_NO_CREDENTIALS
    ```
    Per rimediare, verificare l'esistenza della chiave privata; Verificare che a SFAdmins venga concesso l'accesso di lettura ed esecuzione alla chiave privata.

  - tipo di provider non valido - indica un certificato Crypto New Generation (CNG) ("Microsoft Software Key Storage Provider"); in questo momento, Service Fabric supporta solo i certificati CAPI1. Tipicamente accompagnato da codice di errore:
    ```C++
    0x80090014  -2146893804 NTE_BAD_PROV_TYPE
    ```
    Per risolvere il problema, ricreare il certificato del cluster utilizzando un provider CAPI1 (ad esempio "Microsoft Enhanced RSA and AES Cryptographic Provider"). Per ulteriori informazioni sui provider crittografici, fare riferimento a [Informazioni sui provider di crittografia](https://docs.microsoft.com/windows/win32/seccertenroll/understanding-cryptographic-providers)

