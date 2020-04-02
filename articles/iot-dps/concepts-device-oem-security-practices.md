---
title: Procedure di sicurezza per i produttori - Servizio di provisioning dei dispositivi di Azure IoTSecurity practices for manufacturers - Azure IoT Device Provisioning Service
description: Panoramica delle procedure di sicurezza comuni per gli OEM e le aziende di produzione di dispositivi che preparano i dispositivi per la registrazione nel servizio di provisioning dei dispositivi (DPS) di Azure IoT.
author: timlt
ms.author: timlt
ms.date: 3/02/2020
ms.topic: conceptual
ms.service: iot-dps
ms.custom: iot-p0-scenario, iot-devices-deviceOEM
ms.reviewer: nberdy
ms.openlocfilehash: 3854f353e4ea0b78c0162681e0b89d37419105d8
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529529"
---
# <a name="security-practices-for-azure-iot-device-manufacturers"></a>Procedure di sicurezza per i produttori di dispositivi IoT di AzureSecurity practices for Azure IoT device manufacturers
Man mano che sempre più produttori rilasciano dispositivi IoT, è utile identificare indicazioni sulle procedure comuni. Questo articolo riepiloga le procedure di sicurezza consigliate da considerare quando si producono dispositivi da usare con il servizio di provisioning dei dispositivi (DPS) di Azure.This article summarizes recommended security practices to consider when you manufacture devices for use with Azure IoT Device Provisioning Service (DPS).  

> [!div class="checklist"]
> * Selezione delle opzioni di autenticazione del dispositivo
> * Installazione di certificati su dispositivi IoT
> * Integrazione di un TPM (Trusted Platform Module) nel processo di produzione

## <a name="selecting-device-authentication-options"></a>Selezione delle opzioni di autenticazione del dispositivo
L'obiettivo finale di qualsiasi misura di sicurezza del dispositivo IoT è quello di creare una soluzione IoT sicura. Tuttavia, problemi quali limitazioni hardware, costi e livelli di competenze in materia di sicurezza influiscono sulle opzioni scelte. Inoltre, il tuo approccio alla sicurezza influisce sul modo in cui i tuoi dispositivi IoT si connettono al cloud. Sebbene siano presenti [diversi elementi di sicurezza IoT](https://www.microsoft.com/research/publication/seven-properties-highly-secure-devices/) da considerare, un elemento chiave che ogni cliente incontra è il tipo di autenticazione da usare. 

Tre tipi di autenticazione ampiamente utilizzati sono certificati X.509, Trusted Platform Modules (TPM) e chiavi simmetriche. Sebbene esistano altri tipi di autenticazione, la maggior parte dei clienti che creano soluzioni nell'IoT di Azure usano uno di questi tre tipi. Il resto di questo articolo esamina i pro e i contro dell'uso di ogni tipo di autenticazione.

### <a name="x509-certificate"></a>Certificato X.509
I certificati X.509 sono un tipo di identità digitale che è possibile utilizzare per l'autenticazione. Lo standard del certificato X.509 è documentato in [IETF RFC 5280](https://tools.ietf.org/html/rfc5280). In Azure IoT esistono due modi per autenticare i certificati:In Azure IoT, there are two ways to authenticate certificates:
- Identificazione personale. Un algoritmo di identificazione personale viene eseguito su un certificato per generare una stringa esadecimale. La stringa generata è un identificatore univoco per il certificato. 
- Autenticazione CA basata su una catena completa. Una catena di certificati è un elenco gerarchico di tutti i certificati necessari per autenticare un certificato di entità finale (EE). Per autenticare un certificato EE, è necessario autenticare ogni certificato nella catena, inclusa una CA radice attendibile. 

Pro per X.509:
- X.509 is the most secure authentication type supported in Azure IoT.
- X.509 consente un elevato livello di controllo ai fini della gestione dei certificati.
- Molti fornitori sono disponibili per fornire soluzioni di autenticazione basate su X.509.

Contro per X.509:
- Molti clienti potrebbero dover fare affidamento su fornitori esterni per i propri certificati.
- La gestione dei certificati può essere costosa e aumenta il costo totale della soluzione.
- La gestione del ciclo di vita dei certificati può essere difficile se la logistica non è ben pensata. 

### <a name="trusted-platform-module-tpm"></a>TPM (Trusted Platform Module)
Il TPM, noto anche come [ISO/IEC 11889](https://www.iso.org/standard/66510.html), è uno standard per la generazione e l'archiviazione sicura delle chiavi crittografiche. Il TPM si riferisce anche a un dispositivo I/O virtuale o fisico che interagisce con i moduli che implementano lo standard. Un dispositivo TPM può esistere come hardware discreto, hardware integrato, modulo basato su firmware o modulo basato su software. 

Esistono due differenze fondamentali tra TPM e chiavi simmetriche: 
- I chip TPM possono anche archiviare certificati X.509.
- L'attestazione TPM in DPS utilizza la chiave di approvazione TPM (EK), una forma di autenticazione asimmetrica. Con l'autenticazione asimmetrica, una chiave pubblica viene utilizzata per la crittografia e una chiave privata separata viene utilizzata per la decrittografia. Al contrario, le chiavi simmetriche utilizzano l'autenticazione simmetrica, in cui la chiave privata viene utilizzata sia per la crittografia che per la decrittografia. 

Pro per il TPM:
- I TPM sono inclusi come hardware standard in molti dispositivi Windows, con supporto incorporato per il sistema operativo. 
- L'attestazione TPM è più facile da proteggere rispetto all'attestazione della chiave simmetrica basata su token SAS (Shared Access Signature).
- Puoi facilmente scadere e rinnovare o rotolare le credenziali del dispositivo. DPS esegue automaticamente il rollover delle credenziali dell'hub IoT ogni volta che è necessario eseguire nuovamente il provisioning di un dispositivo TPM.

Contro per TPM: 
- I TPM sono complessi e possono essere difficili da usare. 
- Lo sviluppo di applicazioni con TPM è difficile a meno che non si disponga di un TPM fisico o di un emulatore di qualità.
- Potrebbe essere necessario riprogettare la scheda del dispositivo per includere un TPM nell'hardware. 
- Se si esegue il rollinging del file EK su un TPM, viene eliminata l'identità del TPM e ne viene creato uno nuovo. Anche se il chip fisico rimane lo stesso, ha una nuova identità nella soluzione IoT.

### <a name="symmetric-key"></a>Chiave simmetrica
Con le chiavi simmetriche, la stessa chiave viene utilizzata per crittografare e decrittografare i messaggi. Di conseguenza, la stessa chiave è nota sia al dispositivo che al servizio che la autentica. Azure IoT supporta le connessioni a chiavi simmetriche basate su token SAS. L'autenticazione a chiave simmetrica richiede una significativa responsabilità del proprietario per proteggere le chiavi e ottenere uno stesso livello di sicurezza con l'autenticazione X.509.Symmetric key authentication requires significant owner responsibility to secure the keys and achieve a equal level of security with X.509 authentication. Se si utilizzano chiavi simmetriche, è consigliabile proteggere le chiavi utilizzando un modulo di sicurezza hardware (HSM).

Pro per la chiave simmetrica:
- L'uso delle chiavi simmetriche è il modo più semplice e più basso per iniziare a usare l'autenticazione.
- L'uso di chiavi simmetriche semplifica il processo perché non c'è nulla di più da generare. 

Sconzioni per chiave simmetrica: 
- Le chiavi simmetriche richiedono un grado significativo di sforzo per proteggere le chiavi. La stessa chiave viene condivisa tra dispositivo e cloud, il che significa che la chiave deve essere protetta in due posizioni. Al contrario, la sfida con i certificati TPM e X.509 sta dimostrando il possesso della chiave pubblica senza rivelare la chiave privata.
- Le chiavi simmetriche semplificano il rispetto delle procedure di sicurezza insufficienti. Una tendenza comune con le chiavi simmetriche è quella di codificare le chiavi non crittografate sui dispositivi. Mentre questa pratica è conveniente, lascia i tasti vulnerabili. È possibile ridurre alcuni rischi archiviando in modo sicuro la chiave simmetrica nel dispositivo. Tuttavia, se la priorità è in definitiva la sicurezza piuttosto che la convenienza, utilizzare i certificati X.509 o TPM per l'autenticazione. 

### <a name="shared-symmetric-key"></a>Chiave simmetrica condivisa
Esiste una variazione dell'autenticazione a chiave simmetrica nota come chiave simmetrica condivisa. Questo approccio prevede l'utilizzo della stessa chiave simmetrica in tutti i dispositivi. Si consiglia di evitare l'utilizzo di chiavi simmetriche condivise sui dispositivi. 

Pro per la chiave simmetrica condivisa:
- Semplice da implementare e poco costoso da produrre su larga scala. 

Contro per la chiave simmetrica condivisa: 
- Altamente vulnerabile agli attacchi. Il vantaggio di una facile attuazione è di gran lunga superiore al rischio. 
- Chiunque può impersonare i tuoi dispositivi se ottiene la chiave condivisa.
- Se si fa affidamento su una chiave simmetrica condivisa che viene compromessa, è probabile che si perda il controllo dei dispositivi. 

### <a name="making-the-right-choice-for-your-devices"></a>Fare la scelta giusta per i tuoi dispositivi
Per scegliere un metodo di autenticazione, assicurarsi di considerare i vantaggi e i costi di ogni approccio per il processo di produzione univoco.  Per l'autenticazione del dispositivo, in genere esiste una relazione inversa tra la sicurezza di un determinato approccio e la sua comodità.  

## <a name="installing-certificates-on-iot-devices"></a>Installazione di certificati su dispositivi IoT
Se si usano certificati X.509 per autenticare i dispositivi IoT, in questa sezione vengono fornite indicazioni su come integrare i certificati nel processo di produzione. Dovrai prendere diverse decisioni.  Sono incluse le decisioni sulle variabili di certificato comuni, su quando generare i certificati e su quando installarli. 

Se sei abituato a usare le password, potresti chiederti perché non puoi usare lo stesso certificato in tutti i tuoi dispositivi, nello stesso modo in cui potresti usare la stessa password in tutti i tuoi dispositivi. In primo luogo, l'utilizzo della stessa password ovunque è pericoloso. La pratica ha esposto le aziende ai principali attacchi DDoS, tra cui quello che ha abbattuto DNS sulla costa orientale degli Stati Uniti diversi anni fa. Non utilizzare mai la stessa password ovunque, anche con account personali. In secondo luogo, un certificato non è una password, è un'identità univoca. Una password è come un codice segreto che chiunque può utilizzare per aprire una porta in un edificio protetto.  Si è verificato un problema sconosciuto.  Un certificato è come una patente di guida con la tua foto e altri dettagli, che puoi mostrare a una guardia per entrare in un edificio protetto. È legato a quello che sei.  A condizione che la guardia corrisponda accuratamente alle persone con le patenti di guida, solo tu puoi utilizzare la tua patente (identità) per ottenere l'ingresso. 

### <a name="variables-involved-in-certificate-decisions"></a>Variabili coinvolte nelle decisioni relative ai certificati
Considerare le seguenti variabili e il modo in cui ognuna influisce sul processo di produzione complessivo. 

#### <a name="where-the-certificate-root-of-trust-comes-from"></a>Da dove proviene la radice del certificato di attendibilità
La gestione di un'infrastruttura a chiave pubblica (PKI) può essere costosa e complessa.  Soprattutto se la vostra azienda non ha alcuna esperienza nella gestione di un'infrastruttura PKI. Le opzioni possibili sono:
- Utilizzare un'infrastruttura PKI di terze parti. È possibile acquistare certificati di firma intermedia da un fornitore di certificati di terze parti. In alternativa, è possibile utilizzare un'autorità di certificazione (CA) privata. 
- Utilizzare un'infrastruttura PKI autogestita. È possibile gestire il proprio sistema PKI e generare i propri certificati.
- Usare il servizio di sicurezza [di Azure Sphere.Use](https://azure.microsoft.com/services/azure-sphere/) the Azure Sphere security service. Questa opzione si applica solo ai dispositivi Azure Sphere.This option applies only to Azure Sphere devices. 

#### <a name="where-certificates-are-stored"></a>Posizione di archiviazione dei certificati
Esistono alcuni fattori che influiscono sulla decisione sulla posizione di archiviazione dei certificati. Questi fattori includono il tipo di dispositivo, i margini di profitto previsti (se è possibile permettersi uno spazio di archiviazione sicuro), le funzionalità del dispositivo e la tecnologia di sicurezza esistente sul dispositivo che potresti essere in grado di utilizzare. Valutare le opzioni seguenti:
- In un modulo di sicurezza hardware (HSM). L'utilizzo di un HSM è altamente raccomandato. Verificare se nella scheda di controllo del dispositivo è già installato un HSM. Se si sa di non disporre di un HSM, collaborare con il produttore dell'hardware per identificare un HSM che soddisfi le proprie esigenze.
- In una posizione sicura su disco, ad esempio un ambiente di esecuzione attendibile (TEE).
- Nel file system locale o in un archivio certificati. Ad esempio, l'archivio certificati di Windows.For example, the Windows certificate store. 

#### <a name="connectivity-at-the-factory"></a>Connettività in fabbrica
La connettività in fabbrica determina come e quando si otterranno i certificati da installare nei dispositivi. Le opzioni di connettività sono le seguenti:
- Connettività. La disponibilità di connettività è ottimale, semplifica il processo di generazione dei certificati in locale. 
- Nessuna connettività. In questo caso, si utilizza un certificato firmato da una CA per generare certificati del dispositivo in locale e offline. 
- Nessuna connettività. In questo caso, è possibile ottenere i certificati generati in anticipo. In alternativa, è possibile utilizzare un'infrastruttura PKI non in linea per generare certificati in locale.

#### <a name="audit-requirement"></a>Requisito di audit
A seconda del tipo di dispositivi prodotti, potrebbe essere necessario creare un audit trail della modalità di installazione delle identità dei dispositivi nei dispositivi. Il controllo aggiunge costi di produzione significativi. Quindi, nella maggior parte dei casi, farlo solo se necessario. Se non sei sicuro che sia necessario un controllo, riconfondi l'ufficio legale della tua azienda. Le opzioni di controllo sono: 
- Non un'industria sensibile. Non è richiesto alcun controllo.
- Industria sensibile. I certificati devono essere installati in una stanza sicura in base ai requisiti di certificazione di conformità. Se è necessaria una sala sicura per installare i certificati, è probabile che si sia già a conoscenza di come i certificati vengono installati nei dispositivi. E probabilmente hai già un sistema di audit in atto. 

#### <a name="length-of-certificate-validity"></a>Durata della validità del certificato
Analogamente alla patente di guida, i certificati hanno una data di scadenza che viene impostata al momento della creazione. Di seguito sono riportate le opzioni per la durata della validità del certificato:
- Rinnovo non richiesto.  Questo approccio usa un lungo periodo di rinnovo, pertanto non dovrai mai rinnovare il certificato per tutta la durata del dispositivo. Anche se tale approccio è conveniente, è anche rischioso.  È possibile ridurre il rischio utilizzando l'archiviazione sicura come un HSM sui dispositivi. Tuttavia, è consigliabile evitare l'utilizzo di certificati di lunga durata.
- Rinnovo richiesto.  Sarà necessario rinnovare il certificato per tutta la durata del dispositivo. La validità del certificato dipende dal contesto e sarà necessaria una strategia per il rinnovo.  La strategia deve includere dove si ottengono i certificati e il tipo di funzionalità over-the-air che i dispositivi devono utilizzare nel processo di rinnovo. 

### <a name="when-to-generate-certificates"></a>Quando generare i certificatiWhen to generate certificates
Le funzionalità di connettività Internet in fabbrica influiranno sul processo di generazione dei certificati. Sono disponibili diverse opzioni per la generazione di certificati:You have several options for when to generate certificates: 

- Certificati precaricati.  Alcuni fornitori HSM offrono un servizio premium in cui il fornitore HSM installa i certificati per il cliente. In primo luogo, i clienti concedono al fornitore HSM l'accesso a un certificato di firma. Il fornitore HSM installa quindi i certificati firmati da tale certificato di firma in ogni HSM acquistato dal cliente. Tutto ciò che il cliente deve fare è installare l'HSM sul dispositivo. Anche se questo servizio aggiunge costi, aiuta a semplificare il processo di produzione.  E risolve la questione di quando installare i certificati.
- Certificati generati dal dispositivo.  Se i dispositivi generano certificati internamente, è necessario estrarre il certificato X.509 pubblico dal dispositivo per registrarlo in DPS. 
- Fabbrica connessa.  Se la tua fabbrica dispone di connettività, puoi generare certificati del dispositivo ogni volta che ne hai bisogno.
- Fabbrica offline con la propria infrastruttura PKI. Se la factory non dispone di connettività e si utilizza la propria infrastruttura PKI con supporto offline, è possibile generare i certificati quando necessario.
- Fabbrica offline con PKI di terze parti. Se la factory non dispone di connettività e si utilizza un'infrastruttura PKI di terze parti, è necessario generare i certificati in anticipo. E sarà necessario generare i certificati da una posizione che dispone di connettività. 

### <a name="when-to-install-certificates"></a>Quando installare i certificati
Dopo aver generato i certificati per i dispositivi IoT, è possibile installarli nei dispositivi. 

Se si utilizzano certificati precaricati con un HSM, il processo è semplificato. Dopo aver installato l'HSM nel dispositivo, il codice del dispositivo può accedervi. Quindi si chiameranno le API HSM per accedere al certificato archiviato in HSM. Questo approccio è il più conveniente per il processo di produzione. 

Se non si utilizza un certificato precaricato, è necessario installare il certificato come parte del processo di produzione. L'approccio più semplice consiste nell'installare il certificato nell'HSM nello stesso momento in cui si lampeggia l'immagine iniziale del firmware. Il processo deve aggiungere un passaggio per installare l'immagine in ogni dispositivo. Dopo questo passaggio, è possibile eseguire i controlli di qualità finali e qualsiasi altro passaggio, prima di impacchettare e spedire il dispositivo. 

Sono disponibili strumenti software che consentono di eseguire il processo di installazione e il controllo di qualità finale in un unico passaggio. È possibile modificare questi strumenti per generare un certificato o per estrarre un certificato da un archivio certificati pregenerato. Quindi il software può installare il certificato in cui è necessario installarlo. Gli strumenti software di questo tipo consentono di eseguire la produzione di qualità di produzione su larga scala. 

Dopo aver installato i certificati nei dispositivi, il passaggio successivo consiste nell'imparare a registrare i dispositivi con [DPS](about-iot-dps.md). 

## <a name="integrating-a-tpm-into-the-manufacturing-process"></a>Integrazione di un TPM nel processo di produzione
Se si utilizza un TPM per autenticare i dispositivi IoT, questa sezione offre indicazioni. Le linee guida riguardano i dispositivi TPM 2.0 ampiamente utilizzati che dispongono del supporto della chiave HMAC (Hash-based Message Authentication Code). La specifica TPM per i chip TPM è uno standard ISO gestito dal Trusted Computing Group. Per ulteriori informazioni sul TPM, vedere le specifiche per [TPM 2.0](https://trustedcomputinggroup.org/tpm-library-specification/) e [ISO/IEC 11889](https://www.iso.org/standard/66510.html). 

### <a name="taking-ownership-of-the-tpm"></a>Assunzione della proprietà del TPM
Un passaggio critico nella produzione di un dispositivo con un chip TPM consiste nell'assumere la proprietà del TPM. Questo passaggio è necessario per poter fornire una chiave al proprietario del dispositivo. Il primo passo è quello di estrarre la chiave di approvazione (EK) dal dispositivo. Il passo successivo è quello di rivendicare effettivamente la proprietà. La modalità di esecuzione dipende dal TPM e dal sistema operativo utilizzato. Se necessario, contattare il produttore del TPM o lo sviluppatore del sistema operativo del dispositivo per determinare come richiedere la proprietà. 

Nel processo di produzione, è possibile estrarre l'EK e rivendicare la proprietà in momenti diversi, il che aggiunge flessibilità. Molti produttori sfruttano questa flessibilità aggiungendo un modulo di sicurezza hardware (HSM) per migliorare la sicurezza dei propri dispositivi. In questa sezione vengono fornite indicazioni su quando estrarre l'ek, quando richiedere la proprietà del TPM e considerazioni per l'integrazione di questi passaggi in una sequenza temporale di produzione. 

> [!IMPORTANT]
> Le indicazioni seguenti presuppongono l'utilizzo di un TPM discreto, firmware o integrato. Nei luoghi in cui è applicabile, la guida aggiunge note sull'utilizzo di un TPM non discreto o software. Se si utilizza un TPM software, potrebbero essere necessari passaggi aggiuntivi non inclusi in questa guida. I TPM software dispongono di un'ampia gamma di implementazioni che esulano dall'ambito di questo articolo.  In generale, è possibile integrare un TPM software nella seguente sequenza temporale di produzione generale. Tuttavia, mentre un TPM emulato da software è adatto per la prototipazione e il test, non può fornire lo stesso livello di sicurezza di un TPM discreto, firmware o integrato. Come pratica generale, evitare di utilizzare un TPM software nell'ambiente di produzione.

### <a name="general-manufacturing-timeline"></a>Sequenza temporale generale di produzione
La sequenza temporale seguente mostra come un TPM passa attraverso un processo di produzione e finisce in un dispositivo. Ogni processo di produzione è unico e questa sequenza temporale mostra i modelli più comuni. La timeline offre indicazioni su quando eseguire determinate azioni con i tasti. 

#### <a name="step-1-tpm-is-manufactured"></a>Passaggio 1: viene prodotto il TPM
- Se acquisti TPM da un produttore per l'uso nei tuoi dispositivi, verifica se estrarranno le chiavi di approvazione pubblica (EK_pubs) per te. È utile se il produttore fornisce l'elenco dei EK_pubs con i dispositivi spediti. 
    > [!NOTE]
    > È possibile concedere al produttore del TPM l'accesso in scrittura all'elenco di registrazione usando i criteri di accesso condiviso nel servizio di provisioning.  Questo approccio consente di aggiungere automaticamente le TmM all'elenco di registrazione.  Ma questo è all'inizio del processo di produzione, e richiede fiducia nel produttore TPM. Fatelo a vostro rischio e pericolo. 

- Se produci TPM da vendere ai produttori di dispositivi, prendi in considerazione la possibilità di fornire ai tuoi clienti un elenco di EK_pubs insieme ai loro TPM fisici.  Fornire ai clienti EK_pubs consente di risparmiare un passaggio nel processo. 
- Se produci TPM da utilizzare con i tuoi dispositivi, identifica quale punto del tuo processo è il più conveniente per estrarre il EK_pub. È possibile estrarre il EK_pub in uno qualsiasi dei punti rimanenti della sequenza temporale. 

#### <a name="step-2-tpm-is-installed-into-a-device"></a>Passaggio 2: il TPM viene installato in un dispositivoStep 2: TPM is installed into a device
A questo punto del processo di produzione, è necessario sapere con quale istanza DPS verrà utilizzata la periferica. Di conseguenza, è possibile aggiungere dispositivi all'elenco di registrazione per il provisioning automatico. Per ulteriori informazioni sul provisioning automatico dei dispositivi, consultate la documentazione di [DPS.](about-iot-dps.md)
- Se non hai estratto il EK_pub, ora è un buon momento per farlo. 
- A seconda del processo di installazione del TPM, questo passaggio è anche un buon momento per assumere la proprietà del TPM. 

#### <a name="step-3-device-has-firmware-and-software-installed"></a>Passaggio 3: Il dispositivo ha installato firmware e software
A questo punto del processo, installare il client DPS insieme all'ambito dell'ID e all'URL globale per il provisioning.
- Ora è l'ultima occasione per estrarre il EK_pub. Se una terza parte installerà il software sul dispositivo, è una buona idea estrarre prima il EK_pub.
- Questo punto del processo di produzione è ideale per assumere la proprietà del TPM.  
    > [!NOTE]
    > Se si utilizza un TPM software, è possibile installarlo ora.  Estrarre il EK_pub contemporaneamente.

#### <a name="step-4-device-is-packaged-and-sent-to-the-warehouse"></a>Passaggio 4: Il dispositivo viene imbavagliato e inviato al magazzino
Un dispositivo può stare in un magazzino per 6-12 mesi prima di essere distribuito. 

#### <a name="step-5-device-is-installed-into-the-location"></a>Passo 5: Il dispositivo è installato nel percorso
Dopo che il dispositivo arriva alla sua posizione finale, passa attraverso il provisioning automatico con DPS.

Per ulteriori informazioni, vedere Concetti di [provisioning automatico](concepts-auto-provisioning.md) e [attestazione TPM](concepts-tpm-attestation.md). 

## <a name="resources"></a>Risorse

Oltre alle procedure di sicurezza consigliate in questo articolo, Azure IoT offre risorse per la selezione di hardware protetto e la creazione di distribuzioni IoT sicure:In addition to the recommended security practices in this article, Azure IoT provides resources to help with selecting secure hardware and creating secure IoT deployments: 
- Consigli sulla [sicurezza](../iot-fundamentals/security-recommendations.md) di Azure IoT per guidare il processo di distribuzione. 
- Il [Centro sicurezza](https://azure.microsoft.com/services/security-center/) di Azure offre un servizio che consente di creare distribuzioni IoT sicure. 
- Per informazioni sulla valutazione dell'ambiente hardware, vedere il white paper [Valutazione della sicurezza IoT](https://download.microsoft.com/download/D/3/9/D3948E3C-D5DC-474E-B22F-81BA8ED7A446/Evaluating_Your_IOT_Security_whitepaper_EN_US.pdf). 
- Per informazioni sulla selezione dell'hardware protetto, vedere [L'hardware protetto corretto per la distribuzione IoT](https://download.microsoft.com/download/C/0/5/C05276D6-E602-4BB1-98A4-C29C88E57566/The_right_secure_hardware_for_your_IoT_deployment_EN_US.pdf). 