---
title: Procedure di sicurezza per i produttori-servizio Device provisioning in Azure
description: Panoramica delle procedure di sicurezza comuni per gli OEM e i produttori di dispositivi che preparano i dispositivi per la registrazione nel servizio Device provisioning (DPS) di Azure.
author: timlt
ms.author: timlt
ms.date: 3/02/2020
ms.topic: conceptual
ms.service: iot-dps
ms.custom: iot-p0-scenario, iot-devices-deviceOEM
ms.reviewer: nberdy
ms.openlocfilehash: 788738082cbf9995fb2f7282bc3f574903275528
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90527208"
---
# <a name="security-practices-for-azure-iot-device-manufacturers"></a>Procedure di sicurezza per i produttori di dispositivi Azure Internet
Poiché più produttori rilasciano i dispositivi Internet, è utile identificare le linee guida relative alle procedure comuni. Questo articolo riepiloga le procedure consigliate per la sicurezza da prendere in considerazione quando si producono dispositivi per l'uso con il servizio Device provisioning (DPS) di Azure.  

> [!div class="checklist"]
> * Selezione delle opzioni di autenticazione del dispositivo
> * Installazione dei certificati nei dispositivi Internet
> * Integrazione di un Trusted Platform Module (TPM) nel processo di produzione

## <a name="selecting-device-authentication-options"></a>Selezione delle opzioni di autenticazione del dispositivo
L'obiettivo principale della misura di sicurezza del dispositivo Internet è quello di creare una soluzione Internet delle cose sicure. Tuttavia, eventuali problemi quali limitazioni hardware, costi e livello di esperienza di sicurezza hanno un impatto negativo sulle opzioni selezionate. Inoltre, l'approccio alla sicurezza influisca sul modo in cui i dispositivi Internet delle cose si connettono al cloud. Sebbene ci siano [diversi elementi di sicurezza](https://www.microsoft.com/research/publication/seven-properties-highly-secure-devices/) da considerare, un elemento chiave che ogni cliente incontra è il tipo di autenticazione da usare. 

Tre tipi di autenticazione ampiamente usati sono certificati X. 509, TPM (Trusted Platform Module) e chiavi simmetriche. Sebbene esistano altri tipi di autenticazione, la maggior parte dei clienti che compilano soluzioni in Azure Internet usa uno di questi tre tipi. Nella parte restante di questo articolo vengono esaminati i vantaggi e gli svantaggi dell'utilizzo di ogni tipo di autenticazione.

### <a name="x509-certificate"></a>Certificato X.509
I certificati X. 509 sono un tipo di identità digitale che è possibile usare per l'autenticazione. Lo standard del certificato X. 509 è documentato in [IETF RFC 5280](https://tools.ietf.org/html/rfc5280). In Azure, esistono due modi per autenticare i certificati:
- Thumbprint. Un algoritmo di identificazione personale viene eseguito su un certificato per generare una stringa esadecimale. La stringa generata è un identificatore univoco per il certificato. 
- Autenticazione CA basata su una catena completa. Una catena di certificati è un elenco gerarchico di tutti i certificati necessari per autenticare un certificato di entità finale (EE). Per autenticare un certificato EE, è necessario autenticare ogni certificato nella catena, inclusa una CA radice attendibile. 

Vantaggi per X. 509:
- X. 509 è il tipo di autenticazione più sicuro supportato in Azure.
- X. 509 consente un elevato livello di controllo ai fini della gestione dei certificati.
- Molti fornitori sono disponibili per offrire soluzioni di autenticazione basate su X. 509.

Svantaggi per X. 509:
- Molti clienti potrebbero dover affidarsi a fornitori esterni per i propri certificati.
- La gestione dei certificati può essere costosa e aggiunge al costo totale della soluzione.
- La gestione del ciclo di vita dei certificati può essere difficile se la logistica non è ben pensata. 

### <a name="trusted-platform-module-tpm"></a>TPM (Trusted Platform Module)
TPM, noto anche come [ISO/IEC 11889](https://www.iso.org/standard/66510.html), è uno standard per la generazione e l'archiviazione sicure delle chiavi crittografiche. TPM fa riferimento anche a un dispositivo I/O fisico o virtuale che interagisce con i moduli che implementano lo standard. Un dispositivo TPM può esistere come hardware discreto, hardware integrato, un modulo basato su firmware o un modulo basato su software. 

Esistono due differenze principali tra le chiavi TPMs e simmetriche: 
- I chip TPM possono inoltre archiviare certificati X. 509.
- L'attestazione TPM in DPS usa la chiave di verifica dell'autenticità TPM (EK), una forma di autenticazione asimmetrica. Con l'autenticazione asimmetrica viene usata una chiave pubblica per la crittografia e viene usata una chiave privata separata per la decrittografia. Al contrario, le chiavi simmetriche utilizzano l'autenticazione simmetrica, in cui la chiave privata viene utilizzata sia per la crittografia che per la decrittografia. 

Vantaggi per TPM:
- TPMs sono inclusi come hardware standard in molti dispositivi Windows, con supporto incorporato per il sistema operativo. 
- L'attestazione TPM è più facile da proteggere rispetto all'attestazione della chiave simmetrica basata su token di firma di accesso condiviso (SAS).
- È possibile eseguire facilmente la scadenza e il rinnovo delle credenziali del dispositivo. DPS esegue automaticamente il Rolling delle credenziali dell'hub tutto ogni volta che un dispositivo TPM è a causa di un nuovo provisioning.

Svantaggi per TPM: 
- TPMs sono complessi e possono essere difficili da usare. 
- Lo sviluppo di applicazioni con TPMs è difficile a meno che non si disponga di un TPM fisico o di un emulatore di qualità.
- Potrebbe essere necessario riprogettare la bacheca del dispositivo per includere un TPM nell'hardware. 
- Se si esegue il rollback di EK su un TPM, viene eliminata l'identità del TPM e ne viene creato uno nuovo. Sebbene il chip fisico rimanga invariato, presenta una nuova identità nella soluzione Internet delle cose.

### <a name="symmetric-key"></a>Chiave simmetrica
Con le chiavi simmetriche, viene usata la stessa chiave per crittografare e decrittografare i messaggi. Di conseguenza, la stessa chiave è nota sia per il dispositivo che per il servizio che lo autentica. Azure Internet Azure supporta connessioni di chiavi simmetriche basate su token SAS. Per l'autenticazione con chiave simmetrica è necessaria una significativa responsabilità del proprietario per proteggere le chiavi e ottenere lo stesso livello di sicurezza con l'autenticazione X. 509. Se si usano chiavi simmetriche, la procedura consigliata consiste nel proteggere le chiavi usando un modulo di protezione hardware (HSM).

Vantaggi per la chiave simmetrica:
- L'uso di chiavi simmetriche è il modo più semplice e conveniente per iniziare a usare l'autenticazione.
- L'uso di chiavi simmetriche semplifica il processo perché non è presente alcun elemento aggiuntivo da generare. 

Svantaggi per la chiave simmetrica: 
- Le chiavi simmetriche interessano un livello di impegno significativo per la protezione delle chiavi. La stessa chiave viene condivisa tra il dispositivo e il cloud, il che significa che la chiave deve essere protetta in due posizioni. Al contrario, la sfida con i certificati TPM e X. 509 sta dimostrando il possesso della chiave pubblica senza rivelare la chiave privata.
- Con le chiavi simmetriche è facile seguire le procedure di sicurezza insufficienti. Una tendenza comune con le chiavi simmetriche consiste nel codificare in modo rigido le chiavi non crittografate nei dispositivi. Sebbene questa pratica risulti utile, lascia le chiavi vulnerabili. È possibile mitigare alcuni rischi archiviando in modo sicuro la chiave simmetrica sul dispositivo. Tuttavia, se la priorità è fondamentalmente la sicurezza anziché la convenienza, usare i certificati X. 509 o il TPM per l'autenticazione. 

### <a name="shared-symmetric-key"></a>Chiave simmetrica condivisa
Esiste una variante dell'autenticazione con chiave simmetrica nota come chiave simmetrica condivisa. Questo approccio prevede l'uso della stessa chiave simmetrica in tutti i dispositivi. È consigliabile evitare di usare chiavi simmetriche condivise nei dispositivi. 

Pro per chiave simmetrica condivisa:
- Semplice da implementare e economico da produrre su larga scala. 

Svantaggi per la chiave simmetrica condivisa: 
- Elevato rischio di attacco. Il vantaggio di un'implementazione semplificata è di gran lunga superiore al rischio. 
- Chiunque può rappresentare i dispositivi se ottengono la chiave condivisa.
- Se si fa affidamento su una chiave simmetrica condivisa che risulta compromessa, probabilmente si perderà il controllo dei dispositivi. 

### <a name="making-the-right-choice-for-your-devices"></a>Scelta ottimale per i dispositivi
Per scegliere un metodo di autenticazione, assicurarsi di prendere in considerazione i vantaggi e i costi di ogni approccio per il processo di produzione univoco.  Per l'autenticazione del dispositivo, in genere esiste una relazione inversa tra la protezione di un determinato approccio e la praticità.  

## <a name="installing-certificates-on-iot-devices"></a>Installazione dei certificati nei dispositivi Internet
Se si usano certificati X. 509 per autenticare i dispositivi Internet delle cose, questa sezione offre indicazioni su come integrare i certificati nel processo di produzione. È necessario prendere decisioni diverse.  Sono incluse le decisioni relative alle variabili di certificato comuni, quando generare certificati e quando installarli. 

Se si è abituati a usare le password, è possibile chiedersi perché non è possibile usare lo stesso certificato in tutti i dispositivi, nello stesso modo in cui si sarebbe in grado di usare la stessa password in tutti i dispositivi. Prima di tutto, l'uso della stessa password è pericoloso. La pratica ha esposto le aziende ai principali attacchi DDoS, incluso quello che ha disattivato il DNS sulla Costa degli Stati Uniti orientali diversi anni fa. Non usare mai la stessa password ovunque, anche con gli account personali. In secondo luogo, un certificato non è una password, bensì un'identità univoca. Una password è simile a un codice segreto che chiunque può usare per aprire una porta in una compilazione protetta.  Si tratta di un elemento che si conosce ed è possibile assegnare la password a chiunque per ottenere l'accesso.  Un certificato è come la licenza di un driver con la foto e altri dettagli, che è possibile mostrare a una protezione per accedere a un edificio protetto. È legata a chi sei.  A condizione che il Guard corrisponda in modo accurato alle persone con le licenze del driver, solo è possibile usare la licenza (identità) per ottenere l'ingresso. 

### <a name="variables-involved-in-certificate-decisions"></a>Variabili incluse nelle decisioni relative ai certificati
Si considerino le variabili seguenti e il modo in cui ognuno influisca sul processo di produzione complessivo. 

#### <a name="where-the-certificate-root-of-trust-comes-from"></a>Dove deriva la radice del certificato di attendibilità
Può essere costosa e complessa per la gestione di un'infrastruttura a chiave pubblica (PKI).  Soprattutto se la società non ha alcuna esperienza nella gestione di un'infrastruttura a chiave pubblica. Le opzioni disponibili sono:
- Usare un'infrastruttura a chiave pubblica di terze parti. È possibile acquistare certificati di firma intermedi da un fornitore di certificati di terze parti. In alternativa, è possibile usare un'autorità di certificazione (CA) privata. 
- Usare un'infrastruttura a chiave pubblica autogestita. È possibile gestire il proprio sistema PKI e generare i propri certificati.
- Usare il servizio di sicurezza [Azure Sphere](https://azure.microsoft.com/services/azure-sphere/) . Questa opzione si applica solo ai dispositivi Azure Sphere. 

#### <a name="where-certificates-are-stored"></a>Dove sono archiviati i certificati
Vi sono alcuni fattori che influiscono sulla decisione sulla posizione in cui vengono archiviati i certificati. Questi fattori includono il tipo di dispositivo, i margini di profitto previsti (se è possibile permettersi archiviazione sicura), le funzionalità del dispositivo e la tecnologia di sicurezza esistente sul dispositivo che potrebbe essere possibile usare. Valutare le opzioni seguenti:
- In un modulo di protezione hardware (HSM). Si consiglia vivamente di usare un modulo di protezione hardware. Controllare se nel pannello di controllo del dispositivo è già installato un modulo di protezione hardware. Se non si dispone di un modulo HSM, collaborare con il produttore dell'hardware per identificare un modulo di protezione hardware che soddisfi le proprie esigenze.
- In una posizione sicura su disco, ad esempio un ambiente di esecuzione attendibile.
- Nell'file system locale o in un archivio certificati. Ad esempio, l'archivio certificati di Windows. 

#### <a name="connectivity-at-the-factory"></a>Connettività presso la Factory
La connettività presso la Factory determina come e quando ottenere i certificati da installare nei dispositivi. Le opzioni di connettività sono le seguenti:
- Connettività. La connettività è ottimale, semplifica il processo di generazione dei certificati in locale. 
- Nessuna connettività. In questo caso, si usa un certificato firmato da un'autorità di certificazione per generare certificati di dispositivo localmente e offline. 
- Nessuna connettività. In questo caso, è possibile ottenere i certificati che sono stati generati in anticipo. In alternativa, è possibile usare un'infrastruttura a chiave pubblica offline per generare certificati localmente.

#### <a name="audit-requirement"></a>Requisito di controllo
A seconda del tipo di dispositivo prodotto, potrebbe essere necessario un requisito normativo per creare una audit trail di come le identità dei dispositivi siano installate nei dispositivi. Il controllo aggiunge un costo di produzione significativo. Nella maggior parte dei casi, quindi, eseguire questa operazione solo se necessario. Se non si è certi che sia necessario un controllo, rivolgersi al reparto legale della società. Le opzioni di controllo sono: 
- Non è un settore sensibile. Non è richiesto alcun controllo.
- Settore sensibile. I certificati devono essere installati in una stanza sicura in base ai requisiti di certificazione di conformità. Se è necessaria una stanza sicura per installare i certificati, è probabile che sia già noto come vengono installati i certificati nei dispositivi. Ed è probabile che si disponga già di un sistema di controllo. 

#### <a name="length-of-certificate-validity"></a>Lunghezza della validità del certificato
Come per la licenza di un driver, i certificati hanno una data di scadenza impostata al momento della creazione. Di seguito sono riportate le opzioni per la lunghezza della validità del certificato:
- Il rinnovo non è obbligatorio.  Questo approccio usa un periodo di rinnovo lungo, pertanto non sarà mai necessario rinnovare il certificato durante la durata del dispositivo. Sebbene questo approccio sia pratico, è anche rischioso.  È possibile ridurre il rischio usando un'archiviazione sicura come un modulo di protezione hardware nei dispositivi. Tuttavia, la procedura consigliata consiste nell'evitare di utilizzare certificati di lunga durata.
- Il rinnovo è obbligatorio.  È necessario rinnovare il certificato durante il ciclo di vita del dispositivo. La lunghezza della validità del certificato dipende dal contesto ed è necessaria una strategia per il rinnovo.  La strategia deve includere la posizione in cui vengono recuperati i certificati e il tipo di funzionalità in aria che i dispositivi devono usare nel processo di rinnovo. 

### <a name="when-to-generate-certificates"></a>Quando generare i certificati
Le funzionalità di connettività Internet presso la Factory influiscano sul processo di generazione dei certificati. Sono disponibili diverse opzioni per la generazione di certificati: 

- Certificati pre-caricati.  Alcuni fornitori di moduli di protezione hardware offrono un servizio Premium in cui il fornitore del modulo di protezione hardware installa i certificati per il cliente. Per prima cosa, i clienti possono accedere al fornitore del modulo di protezione hardware per un certificato di firma. Il fornitore del modulo di protezione hardware installa quindi i certificati firmati dal certificato di firma su ogni modulo di protezione hardware acquistato dal cliente. Tutto il cliente deve eseguire l'installazione del modulo di protezione hardware nel dispositivo. Sebbene questo servizio aggiunga costi, consente di semplificare il processo di produzione.  E risolve la domanda relativa a quando installare i certificati.
- Certificati generati dal dispositivo.  Se i dispositivi generano certificati internamente, è necessario estrarre il certificato X. 509 pubblico dal dispositivo per registrarlo in DPS. 
- Connected Factory.  Se la Factory ha connettività, è possibile generare i certificati del dispositivo ogni volta che è necessario.
- Factory offline con la propria infrastruttura a chiave pubblica. Se la factory non ha connettività e si usa l'infrastruttura a chiave pubblica (PKI) con supporto offline, è possibile generare i certificati quando sono necessari.
- Factory offline con infrastruttura a chiave pubblica di terze parti. Se la factory non ha connettività e si usa un'infrastruttura a chiave pubblica di terze parti, è necessario generare i certificati in anticipo. E sarà necessario generare i certificati da una posizione con connettività. 

### <a name="when-to-install-certificates"></a>Quando installare i certificati
Dopo aver generato i certificati per i dispositivi Internet, è possibile installarli nei dispositivi. 

Se si usano certificati pre-caricati con un modulo di protezione hardware, il processo viene semplificato. Al termine dell'installazione del modulo di protezione hardware, il codice del dispositivo può accedervi. Si chiameranno quindi le API HSM per accedere al certificato archiviato nel modulo di protezione hardware. Questo approccio è il più pratico per il processo di produzione. 

Se non si usa un certificato precaricato, è necessario installare il certificato come parte del processo di produzione. L'approccio più semplice consiste nell'installare il certificato nel modulo di protezione hardware nello stesso momento in cui si lampeggia l'immagine del firmware iniziale. Il processo deve aggiungere un passaggio per installare l'immagine in ogni dispositivo. Dopo questo passaggio, è possibile eseguire i controlli di qualità finali ed eventuali altri passaggi, prima di creare il pacchetto e spedire il dispositivo. 

Sono disponibili strumenti software che consentono di eseguire il processo di installazione e il controllo della qualità finale in un unico passaggio. È possibile modificare questi strumenti per generare un certificato o per eseguire il pull di un certificato da un archivio certificati pre-generato. Il software può quindi installare il certificato in cui è necessario installarlo. Gli strumenti software di questo tipo consentono di eseguire la produzione della qualità di produzione su larga scala. 

Dopo aver installato i certificati nei dispositivi, il passaggio successivo consiste nell'apprendere come registrare i dispositivi con [DPS](about-iot-dps.md). 

## <a name="integrating-a-tpm-into-the-manufacturing-process"></a>Integrazione di un modulo TPM nel processo di produzione
Se si usa un TPM per autenticare i dispositivi Internet delle cose, questa sezione offre indicazioni. Il materiale sussidiario riguarda i dispositivi TPM 2,0 ampiamente usati con supporto per la chiave HMAC (hash-based Message Authentication Code). La specifica TPM per i chip TPM è uno standard ISO gestito dal Trusted Computing Group. Per ulteriori informazioni su TPM, vedere le specifiche per [tpm 2,0](https://trustedcomputinggroup.org/tpm-library-specification/) e [ISO/IEC 11889](https://www.iso.org/standard/66510.html). 

### <a name="taking-ownership-of-the-tpm"></a>Assunzione della proprietà del TPM
Un passaggio essenziale per la produzione di un dispositivo con un chip TPM è quello di assumere la proprietà del TPM. Questo passaggio è necessario per poter fornire una chiave al proprietario del dispositivo. Il primo passaggio consiste nell'estrarre la chiave di verifica dell'autenticità dal dispositivo. Il passaggio successivo consiste nell'attestare effettivamente la proprietà. Il modo in cui eseguire questa operazione dipende dal TPM e dal sistema operativo in uso. Se necessario, contattare il produttore del TPM o lo sviluppatore del sistema operativo del dispositivo per determinare come richiedere la proprietà. 

Nel processo di produzione è possibile estrarre il EK e richiedere la proprietà in momenti diversi, in modo da aumentare la flessibilità. Molti produttori sfruttano questa flessibilità aggiungendo un modulo di protezione hardware (HSM) per migliorare la sicurezza dei dispositivi. In questa sezione vengono fornite indicazioni su quando estrarre l'oggetto EK, quando rivendicare la proprietà del TPM e considerazioni per l'integrazione di questi passaggi in una sequenza temporale di produzione. 

> [!IMPORTANT]
> Le linee guida riportate di seguito presuppongono l'utilizzo di un TPM discreto, firmware o integrato. Nelle posizioni in cui è applicabile, le linee guida aggiungono note sull'uso di un TPM non discreto o software. Se si utilizza un TPM Software, potrebbero essere presenti ulteriori passaggi che non sono inclusi in questa guida. I TPMs software hanno diverse implementazioni che esulano dall'ambito di questo articolo.  In generale, è possibile integrare un modulo TPM Software nella seguente sequenza temporale di produzione generale. Tuttavia, mentre un TPM emulato da software è adatto per la prototipazione e il test, non può fornire lo stesso livello di sicurezza di un TPM discreto, firmware o integrato. Come procedura generale, evitare di usare un TPM Software in produzione.

### <a name="general-manufacturing-timeline"></a>Sequenza temporale di produzione generale
La sequenza temporale seguente mostra il modo in cui un TPM passa attraverso un processo di produzione e finisce in un dispositivo. Ogni processo di produzione è univoco e questa sequenza temporale Mostra i modelli più comuni. La sequenza temporale offre indicazioni su quando eseguire determinate azioni con le chiavi. 

#### <a name="step-1-tpm-is-manufactured"></a>Passaggio 1: il TPM è prodotto
- Se si acquista TPMs da un produttore da usare nei dispositivi, vedere se verranno estratti i codici di verifica dell'autenticità pubblica (EK_pubs). È utile se il produttore fornisce l'elenco di EK_pubs con i dispositivi spediti. 
    > [!NOTE]
    > È possibile concedere al produttore del TPM l'accesso in scrittura all'elenco di registrazione usando i criteri di accesso condiviso nel servizio di provisioning.  Questo approccio consente di aggiungere il TPMs all'elenco di registrazione.  Ma si tratta di una fase iniziale del processo di produzione e richiede una relazione di trust con il produttore del TPM. Eseguire questa operazione a proprio rischio. 

- Se si produce TPMs per la vendita ai produttori di dispositivi, è consigliabile fornire ai clienti un elenco di EK_pubs insieme ai relativi TPMs fisici.  Fornire ai clienti EK_pubs salva un passaggio nel processo. 
- Se si produce TPMs da usare con i propri dispositivi, identificare quale punto del processo è il più utile per estrarre il EK_pub. È possibile estrarre il EK_pub in uno dei punti rimanenti della sequenza temporale. 

#### <a name="step-2-tpm-is-installed-into-a-device"></a>Passaggio 2: TPM è installato in un dispositivo
A questo punto del processo di produzione, è necessario stabilire quale istanza di DPS verrà usata dal dispositivo. Di conseguenza, è possibile aggiungere i dispositivi all'elenco di registrazione per il provisioning automatizzato. Per ulteriori informazioni sul provisioning automatico dei dispositivi, vedere la [documentazione di DPS](about-iot-dps.md).
- Se il EK_pub non è stato estratto, ora è opportuno eseguire questa operazione. 
- A seconda del processo di installazione del TPM, questo passaggio rappresenta anche il momento opportuno per assumere la proprietà del TPM. 

#### <a name="step-3-device-has-firmware-and-software-installed"></a>Passaggio 3: dispositivo con firmware e software installato
A questo punto del processo, installare il client DPS insieme all'ambito ID e all'URL globale per il provisioning.
- Ora è l'ultima possibilità per estrarre il EK_pub. Se il software viene installato nel dispositivo da terze parti, è consigliabile estrarre innanzitutto il EK_pub.
- Questo punto del processo di produzione è ideale per assumere la proprietà del TPM.  
    > [!NOTE]
    > Se si usa un modulo TPM Software, è possibile installarlo ora.  Estrarre il EK_pub nello stesso momento.

#### <a name="step-4-device-is-packaged-and-sent-to-the-warehouse"></a>Passaggio 4: il dispositivo è incluso nel pacchetto e inviato al magazzino
Un dispositivo può trovarsi in un magazzino per 6-12 mesi prima di essere distribuito. 

#### <a name="step-5-device-is-installed-into-the-location"></a>Passaggio 5: il dispositivo viene installato nel percorso
Quando il dispositivo raggiunge la posizione finale, viene eseguito il provisioning automatizzato con DPS.

Per ulteriori informazioni, vedere [provisioning](about-iot-dps.md#provisioning-process) e [attestazione TPM](concepts-tpm-attestation.md). 

## <a name="resources"></a>Risorse

Oltre alle procedure consigliate per la sicurezza in questo articolo, Azure è disponibile per la selezione di hardware sicuro e per la creazione di distribuzioni Internet sicure: 
- Consigli sulla [sicurezza](../iot-fundamentals/security-recommendations.md) di Azure per il processo di distribuzione. 
- Il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/) offre un servizio che consente di creare distribuzioni di Internet delle cose sicure. 
- Per informazioni sulla valutazione dell'ambiente hardware, vedere il white paper relativo alla [valutazione della sicurezza](https://download.microsoft.com/download/D/3/9/D3948E3C-D5DC-474E-B22F-81BA8ED7A446/Evaluating_Your_IOT_Security_whitepaper_EN_US.pdf)di Internet. 
- Per informazioni sulla selezione di hardware sicuro, vedere [l'hardware sicuro ideale per la distribuzione di Internet delle](https://download.microsoft.com/download/C/0/5/C05276D6-E602-4BB1-98A4-C29C88E57566/The_right_secure_hardware_for_your_IoT_deployment_EN_US.pdf)cose. 