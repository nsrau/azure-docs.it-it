<properties
   pageTitle="Classificazione dei dati per Azure | Microsoft Azure"
   description="Questo articolo contiene un'introduzione ai concetti fondamentali della classificazione dei dati e mette in evidenza l'importanza di tale classificazione, in particolar modo nel contesto del cloud computing e dell'uso di Microsoft Azure."
   services="virtual-machines, cloud-services, storage"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>

<tags
   ms.service="azure-security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/11/2016"
   ms.author="yurid"/>

# Classificazione dei dati per Azure

Questo articolo contiene un'introduzione ai concetti fondamentali della classificazione dei dati e mette in evidenza l'importanza di tale classificazione, in particolar modo nel contesto del cloud computing e dell'uso di Microsoft Azure.

## Concetti fondamentali della classificazione dei dati per Azure

Per ottimizzare la classificazione dei dati in un'organizzazione, è necessario conoscere a fondo le esigenze dell'organizzazione e sapere con esattezza dove si trovano gli asset di dati.
 
Gli stati di base dei dati sono tre:

- Inattivi 
- In corso 
- In transito 
 
Tutti e tre gli stati richiedono soluzioni tecniche univoche per la classificazione dei dati, ma i principi di classificazione dei dati applicati devono essere sempre gli stessi. I dati classificati come riservati devono rimanere tali quando sono inattivi, in fase di elaborazione e in transito.
 
I dati possono anche essere strutturati o non strutturati. I processi di classificazione tipici per i dati strutturati disponibili in database e fogli di calcolo sono meno complessi e meno lunghi da gestire di quelli per i dati non strutturati, ad esempio documenti, codice sorgente e posta elettronica.

> [AZURE.TIP] Per altre informazioni sulle funzionalità di Azure e sulle procedure consigliate per la crittografia dei dati, vedere [Procedure consigliate per la crittografia dei dati in Azure](azure-security-data-encryption-best-practices.md).

In generale, le organizzazioni avranno più dati non strutturati che strutturati. Indipendentemente dal fatto che i dati siano strutturati o non strutturati, è importante gestire la riservatezza dei dati. Se implementata in modo appropriato, la classificazione dei dati assicura che gli asset di dati sensibili o riservati vengano gestiti con una supervisione più attenta degli asset di dati considerati pubblici o che possono essere liberamente distribuiti.

### Controllo dell'accesso ai dati 

L'autenticazione e l'autorizzazione vengono spesso confuse e i rispettivi ruoli fraintesi. Come illustrato nella figura seguente, rappresentano in realtà due concetti piuttosto diversi.

![Accesso e controllo dei dati](./media/azure-security-data-classification/azure-security-data-classification-fig1.png)

### Autenticazione 

L'autenticazione in genere è costituita da almeno due parti: un nome utente o un ID utente per identificare un utente e un token, ad esempio una password, per verificare che la credenziale del nome utente sia valida. Il processo non fornisce all'utente autenticato l'accesso a nessun elemento o servizio, ma verifica che l'utente sia chi dice di essere.

> [AZURE.TIP] [Azure Active Directory](./active-directory/active-directory-whatis.md) fornisce servizi di gestione delle identità basati sul cloud che consentono di autenticare e autorizzare gli utenti.

### Autorizzazione
 
L'autorizzazione è il processo che offre a un utente autenticato la possibilità di accedere a un'applicazione, a un set di dati, a un file di dati o a un altro oggetto. Quando si assegnano agli utenti autenticati i diritti per usare, modificare o eliminare elementi a cui possono accedere, è necessario prestare attenzione alla classificazione dei dati.

Per una corretta autorizzazione, è necessario implementare un meccanismo per convalidare le esigenze dei singoli utenti di accedere a file e informazioni basato su una combinazione di ruolo, criteri di sicurezza e considerazioni sui criteri di rischio. È possibile, ad esempio, che non tutti i dipendenti abbiano la necessità di accedere ai dati di specifiche applicazioni line-of-business (LOB) e che solo un piccolo subset di dipendenti abbia bisogno di accedere ai file delle risorse umane. Invece per le organizzazioni che controllano non solo chi può accedere ai dati, ma anche quando e come, deve essere attivo un sistema efficace per autenticare gli utenti.

> [AZURE.TIP] In Microsoft Azure verificare di usare il controllo degli accessi in base al ruolo di Azure per concedere solo il livello di accesso necessario agli utenti per eseguire i processi. Per altre informazioni, vedere [Usare le assegnazioni di ruolo per gestire l'accesso alle risorse di Azure Active Directory](./active-directory/role-based-access-control-configure.md).

### Ruoli e responsabilità nel cloud computing 

Anche se i provider di servizi cloud possono essere d'aiuto nella gestione dei rischi, i clienti devono assicurarsi che la gestione e l'applicazione della classificazione dei dati vengano implementate in modo appropriato per fornire il livello adeguato di servizi di gestione dati.
 
Le responsabilità della classificazione dei dati dipenderanno dal modello di servizio cloud in uso, come illustrato nella figura seguente. I tre modelli primari di servizio cloud sono l'infrastruttura distribuita come servizio (IaaS, Infrastructure as a Service), la piattaforma distribuita come servizio (PaaS, Platform as a Service) e il software come un servizio (SaaS, Software as a Service). L'implementazione di meccanismi di classificazione dei dati varierà anche a seconda della dipendenza dal provider di servizi cloud e dalle sue aspettative.

![Ruoli](./media/azure-security-data-classification/azure-security-data-classification-fig2.png)

Anche se ciascuno è responsabile della classificazione dei propri dati, i provider di servizi cloud devono impegnarsi per iscritto indicando come proteggeranno e gestiranno la privacy dei dati dei clienti archiviati nel cloud.

- I **provider IaaS** devono solo assicurarsi che l'ambiente virtuale consenta le funzionalità di classificazione dei dati e risponda ai requisiti di conformità del cliente. I provider IaaS hanno un ruolo meno importante nella classificazione dei dati perché devono solo assicurarsi che i dati del cliente rispondano ai requisiti di conformità. I provider devono tuttavia assicurarsi che i propri ambienti virtuali rispondano ai requisiti di classificazione dei dati oltre a proteggere i propri data center.
- Le responsabilità dei **provider PaaS** possono essere miste, perché la piattaforma può essere usata in un approccio a livelli per garantire la sicurezza per uno strumento di classificazione. I provider PaaS possono essere responsabili dell'autenticazione ed eventualmente di alcune regole di autorizzazione e devono fornire funzionalità di sicurezza e classificazione dei dati al livello dell'applicazione. Similmente ai provider IaaS, i provider PaaS devono assicurarsi che la piattaforma sia conforme ai requisiti pertinenti di classificazione dei dati.
- I **provider SaaS** verranno spesso considerati parte di una catena di autorizzazione e dovranno assicurarsi che i dati archiviati nell'applicazione SaaS possano essere controllati dal tipo di classificazione. Le applicazioni SaaS possono essere usate per le applicazioni LOB e in base alla loro natura devono fornire i mezzi per autenticare e autorizzare i dati usati e archiviati. 

## Processo di classificazione 

Diverse organizzazioni che comprendono la necessità della classificazione dei dati e vogliono implementarla devono affrontare una sfida fondamentale: da dove iniziare?

Un modo semplice ed efficace per implementare la classificazione dei dati consiste nell'usare il modello PLAN (pianificazione), DO (esecuzione), CHECK (verifica), ACT (azione) di [MOF](https://technet.microsoft.com/solutionaccelerators/dd320379.aspx). La figura seguente illustra le attività necessarie per implementare correttamente la classificazione dei dati in questo modello.

1. **PLAN**. Identificare gli asset di dati e un responsabile dei dati per distribuire il programma di classificazione e sviluppare i profili di protezione. 
2. **DO**. Dopo avere stabilito i criteri di classificazione dei dati, distribuire il programma e implementare le tecnologie di applicazione necessarie per i dati riservati.  
3. **CHECK**. Controllare e convalidare i report per assicurarsi che gli strumenti e i metodi in uso rispondano in modo efficace ai criteri di classificazione. 
4. **ACT**. Rivedere lo stato dell'accesso ai dati e rivedere i file e i dati che richiedono una revisione usando una metodologia di riclassificazione e revisione per adottare le modifiche necessarie e per affrontare i nuovi rischi.  

![Plan, Do, Check, Act](./media/azure-security-data-classification/azure-security-data-classification-fig3.png)
 
###Selezionare un modello di terminologia adatto alle proprie esigenze
 
Esistono diversi tipi di processi per classificare i dati, tra cui processi manuali, processi basati sulla posizione che classificano i dati in base alla posizione di un utente o di un sistema, processi basati sull'applicazione, ad esempio la classificazione specifica del database, e processi automatizzati usati da svariate tecnologie, alcune delle quali vengono descritte nella sezione "Protezione dei dati riservati" più avanti in questo articolo.
 
Questo articolo presenta due modelli di terminologia generalizzati basati su modelli di uso comune e riconosciuti nel settore. Questi modelli di terminologia, che forniscono entrambi tre livelli di riservatezza della classificazione, vengono illustrati nella tabella seguente.

> [AZURE.NOTE] Quando si classifica un file o una risorsa che combina dati che normalmente vengono classificati a livelli diversi, il più alto livello di classificazione presente deve stabilire la classificazione generale. Ad esempio, un file contenente dati sensibili e con restrizioni deve essere classificato come con restrizioni.

| **Riservatezza** | **Modello di terminologia 1** | **Modello di terminologia 2** |
|--------------------|---------------------------|-------------------------|
| Alto | Riservate | Con restrizioni |
| Medio | Solo per uso interno | Sensibili |
| Basso | Pubblico | Senza restrizioni |

#### Riservate (con restrizioni) 

Le informazioni classificate come riservate o con restrizioni includono dati che possono causare gravi problemi a una o più persone e/o organizzazioni, se vengono compromessi o persi. Tali informazioni vengono spesso fornite solo quando necessario e possono includere:

- Dati personali, incluse le informazioni personali, ad esempio codici fiscali o numeri di carta di identità, numeri di passaporto, numeri di carta di credito, numeri di patente, cartelle cliniche e numeri ID polizza assicurativa sanitaria.  
- Record finanziari, inclusi i numeri di conto finanziario, ad esempio numeri di conto corrente o di investimento. 
- Materiale aziendale, ad esempio documenti o dati che costituiscono una proprietà intellettuale univoca o specifica.  
- Dati legali, incluso il potenziale materiale coperto da segreto professionale. 
- Dati di autenticazione, incluse le chiavi di crittografia private, le coppie nome utente e password o altre sequenze di identificazione, ad esempio file di chiave biometrica privata. 

I dati classificati come riservati hanno spesso requisiti normativi e di conformità per la gestione dei dati.

#### Solo per uso interno (sensibili)
 
Le informazioni classificate con sensibilità media includono file e dati che non hanno gravi conseguenze per una persona e/o un'organizzazione, se vengono persi o eliminati definitivamente. Tali informazioni possono includere:

- Messaggi di posta elettronica, molti dei quali possono essere eliminati o distribuiti senza causare una crisi (escluse le cassette postali o i messaggi da persone identificate nella classificazione riservata).  
- Documenti e file che non includono dati riservati.
 
In genere, questa classificazione include tutto ciò che non è riservato. Questa classificazione può includere la maggior parte dei dati aziendali, perché la maggior parte dei file gestiti o usati quotidianamente possono essere classificati come sensibili. Fatta eccezione per i dati resi pubblici o che sono riservati, tutti i dati in un'organizzazione aziendale possono essere classificati come sensibili per impostazione predefinita.

#### Pubbliche (senza restrizioni)
 
Le informazioni classificate come pubbliche includono dati e file non critici per le esigenze o le operazioni aziendali. Questa classificazione può includere anche dati rilasciati intenzionalmente al pubblico per uso personale, ad esempio materiale di marketing o comunicati stampa. Questa classificazione può includere anche altri dati, ad esempio messaggi di posta elettronica indesiderata archiviati da un servizio di posta elettronica.

### Definire la proprietà dei dati
 
È importante stabilire una precisa catena di custodia per la proprietà di tutti gli asset di dati. La tabella seguente identifica diversi ruoli di proprietà dei dati nelle attività di classificazione dei dati e i rispettivi diritti.

| **Ruolo** | **Creazione** | **Modifica/Eliminazione** | **Delega** | **Lettura** | **Archiviazione/Ripristino** |
|-----------------|---------------|---------------------|---------------|-------------|-----------------------|
| Proprietario | X | X | X | X | X |
| Responsabile | | | X | | |
| Amministratore | | | | | X |
| Utente* | | X | | X | |
****Un responsabile può concedere agli utenti diritti aggiuntivi, ad esempio di modifica ed eliminazione*

> [AZURE.NOTE] Questa tabella è solo un esempio rappresentativo e non include un elenco completo di ruoli e diritti.

Il **proprietario degli asset di dati** è il creatore originale dei dati e può delegare la proprietà e assegnare un responsabile. Quando viene creato un file, il proprietario deve poter assegnare una classificazione e ha quindi la responsabilità di sapere che cosa deve essere classificato come riservato in base ai criteri dell'organizzazione. Tutti i dati del proprietario di un asset di dati possono essere classificati automaticamente come solo per uso interno (sensibili) a meno che non sia responsabile della proprietà o della creazione di tipi di dati riservati (con restrizioni). Il ruolo del proprietario verrà frequentemente modificato dopo la classificazione dei dati. Il proprietario, ad esempio, può creare un database di informazioni classificate e cederne i diritti al responsabile dei dati.

> [AZURE.NOTE] I proprietari di asset di dati usano spesso una combinazione di servizi, dispositivi e supporti, alcuni personali e altri appartenenti all'organizzazione. Criteri organizzativi chiari consentono di assicurarsi che l'utilizzo di dispositivi, ad esempio portatili e Smart Device, segua le linee guida relative alla classificazione dei dati.

Il **responsabile dell'asset di dati** viene incaricato dal proprietario dell'asset (o dal delegato) di gestire l'asset in base ai contratti con il proprietario dell'asset o ai requisiti dei criteri applicabili. Idealmente, il ruolo di responsabile può essere implementato in un sistema automatizzato. Il responsabile di un asset si assicura che vengano forniti i controlli di accesso necessari ed è incaricato della gestione e della protezione degli asset a lui delegati. Le responsabilità del responsabile dell'asset possono includere:

- Protezione dell'asset in base alle istruzioni del proprietario dell'asset o al contratto con il proprietario dell'asset 
- Verifica della conformità con i criteri di classificazione 
- Notifica ai proprietari degli asset delle modifiche ai controlli e/o alle procedure di protezione concordate prima che tali modifiche vengano applicate 
- Segnalazione al proprietario dell'asset delle modifiche o della rimozione delle responsabilità del responsabile dell'asset 
- Un **amministratore** rappresenta un utente che deve assicurarsi che venga mantenuta l'integrità, ma che non è l'utente, il responsabile o il proprietario di un asset di dati. Diversi ruoli di amministratore forniscono infatti servizi di gestione dei contenitori di dati senza avere accesso ai dati. Il ruolo di amministratore include il backup e il ripristino dei dati, la gestione dei record degli asset e la scelta, l'acquisizione e il funzionamento dei dispositivi e della risorsa di archiviazione che ospitano gli asset. 
- L'utente dell'asset include chiunque abbia accesso ai dati o a un file. L'assegnazione dell'accesso viene spesso delegata dal proprietario al responsabile dell'asset.  

### Implementazione
  
Le considerazioni sulla gestione si applicano a tutte le metodologie di classificazione. Queste considerazioni devono includere i dettagli su chi, che cosa, dove, quando e perché si userà, si accederà, si modificherà o si eliminerà un asset di dati. Per la gestione degli asset è indispensabile comprendere come un'organizzazione considera i rischi, ma è possibile applicare una semplice metodologia come quella definita nel processo di classificazione dei dati. Altre considerazioni sulla classificazione dei dati includono l'introduzione di nuove applicazioni e strumenti e la gestione delle modifiche dopo l'implementazione di un metodo di classificazione.

### Riclassificazione
 
È necessario riclassificare o modificare lo stato della classificazione di un asset di dati quando un utente o un sistema determina che l'importanza o il profilo di rischio di un asset di dati è cambiato. Questa attività è importante per assicurarsi che lo stato della classificazione continui a essere aggiornato e valido. La maggior parte del contenuto non classificato manualmente può essere classificata automaticamente o in base all'utilizzo da un responsabile o da un proprietario dei dati.

### Riclassificazione manuale dei dati
 
Idealmente, questa attività assicura che i dettagli di una modifica vengano acquisiti e controllati. I motivi più comuni per scegliere la riclassificazione manuale sono la riservatezza, il formato cartaceo dei record o la necessità di riesaminare i dati classificati in modo errato in origine. Poiché questo documento considera la classificazione e lo spostamento dei dati nel cloud, le attività di riclassificazione manuale dovranno essere esaminate singolarmente con attenzione e sarà opportuno rivedere la gestione dei rischi per poter soddisfare i requisiti di classificazione. In genere tale attività considererà i criteri dell'organizzazione su ciò che deve essere classificato, ovvero lo stato di classificazione predefinito (tutti i dati e i file sensibili, ma non riservati), ed escluderà i dati ad alto rischio.

### Riclassificazione automatica dei dati
 
La riclassificazione automatica dei dati usa la stessa regola generale della classificazione manuale, fatta eccezione per il fatto che le soluzioni automatizzate possono assicurare che le regole vengano seguite e applicate in base alle esigenze. La classificazione dei dati può essere eseguita nell'ambito di un criterio di applicazione della classificazione dei dati, che può essere applicato quando i dati sono archiviati, sono in uso e in transito usando la tecnologia di autorizzazione.

- In base all'applicazione. Per impostazione predefinita, l'uso di determinate applicazioni imposta un livello di classificazione. Ad esempio, i dati del software CRM (Customer Relationship Management), delle risorse umane e degli strumenti di gestione dei record sanitari sono riservati per impostazione predefinita. 
- In base alla posizione. La posizione dei dati consente di identificare la riservatezza dei dati. Ad esempio, è più probabile che i dati archiviati da un reparto risorse umane o finanziario siano riservati per natura.  
 
### Conservazione, ripristino ed eliminazione dei dati 

Il ripristino e l'eliminazione dei dati, come la riclassificazione, sono aspetti essenziali della gestione degli asset di dati. I principi del ripristino e dell'eliminazione dei dati verranno definiti da un criterio di conservazione dei dati e applicati nello stesso modo della riclassificazione. Le operazioni necessarie verranno eseguite dai ruoli di responsabile e amministratore come attività di collaborazione.

In assenza di un criterio di conservazione dei dati può verificarsi la perdita dei dati o la mancata conformità con i requisiti di individuazione normativi e legali. La maggior parte delle organizzazioni che non hanno un criterio di conservazione dei dati ben definito usa in genere un criterio predefinito in base al quale vengono conservati tutti i dati. Tuttavia, tale criterio di conservazione presenta rischi aggiuntivi negli scenari di servizi cloud.

Ad esempio, un criterio di conservazione dei dati per i provider di servizi cloud può basarsi sulla "durata della sottoscrizione" (finché si paga per il servizio, i dati vengono conservati). È possibile che un contratto di questo tipo non risponda ai criteri di conservazione aziendali o normativi. La definizione di un criterio per i dati riservati può assicurare che i dati vengano archiviati e rimossi in base alle procedure consigliate. È anche possibile creare un criterio di archiviazione per indicare ufficialmente quali dati devono essere eliminati e quando.

I criteri di conservazione dei dati devono soddisfare i requisiti obbligatori normativi e di conformità, oltre ai requisiti di conservazione legali aziendali. I dati classificati possono far sorgere dubbi sulla durata della conservazione e sulle eccezioni per i dati archiviati con un provider. È più probabile che tali dubbi riguardino i dati non classificati correttamente.

> [AZURE.TIP] Per altre informazioni sui criteri di conservazione dei dati di Azure e su altri argomenti, vedere il [Contratto di Sottoscrizione Microsoft Online](https://azure.microsoft.com/support/legal/subscription-agreement/).

## Protezione dei dati riservati
  
Una volta classificati i dati, trovare e implementare modi per proteggere i dati riservati è parte integrante di qualsiasi strategia di distribuzione della protezione dei dati personali. La protezione dei dati riservati richiede particolare attenzione a come i dati vengono archiviati e trasmessi nelle architetture convenzionali, oltre che nel cloud.

Questa sezione fornisce informazioni di base su alcune tecnologie che possono automatizzare le attività di applicazione che consentono di proteggere i dati classificati come riservati.
 
Come illustra la figura seguente, queste tecnologie possono essere distribuite come soluzioni locali o basate sul cloud oppure in modalità ibrida, con alcune tecnologie distribuite in locale e altre nel cloud. Alcune tecnologie, ad esempio la crittografia e Rights Management, si estendono anche ai dispositivi utente.

![Tecnologie](./media/azure-security-data-classification/azure-security-data-classification-fig4.png)

### Software di Rights Management  

Una soluzione per evitare la perdita di dati è il software di Rights Management. Diversamente dagli approcci che cercano di interrompere il flusso di informazioni nei punti di uscita in un'organizzazione, il software di Rights Management opera in profondità nell'ambito delle tecnologie di archiviazione dati. I documenti vengono crittografati e il controllo su chi può decrittografarli usa controlli di accesso definiti in una soluzione per il controllo dell'autenticazione, ad esempio un servizio directory.

> [AZURE.TIP] È possibile usare Azure Rights Management (Azure RMS) come soluzione per la protezione delle informazioni per proteggere i dati in scenari diversi. Per altre informazioni su questa soluzione di Azure, vedere [Informazioni su Azure Rights Management](https://docs.microsoft.com/rights-management/understand-explore/what-is-azure-rms).

Alcuni vantaggi del software di Rights Management includono:

- Informazioni sensibili protette. Gli utenti possono proteggere i dati direttamente usando le applicazioni abilitate per Rights Management. Non sono necessari passaggi aggiuntivi: la creazione di documenti, l'invio di posta elettronica e la pubblicazione di dati offrono un'esperienza di protezione dei dati personali coerente. 
- Dati sempre protetti. I clienti hanno sempre il controllo su chi accede ai dati, sia nel cloud che nell'infrastruttura IT esistente o nel desktop dell'utente. Le organizzazioni possono scegliere di crittografare i dati e di limitare l'accesso in base ai requisiti aziendali. 
- Criteri di protezione delle informazioni predefiniti. Gli amministratori e gli utenti possono usare criteri standard per diversi scenari aziendali comuni, ad esempio "Informazioni aziendali riservate - Sola lettura" e "Non inoltrare". È supportata un'ampia gamma di diritti di utilizzo, ad esempio lettura, copia, stampa, salvataggio, modifica e inoltro, per garantire flessibilità nella definizione dei diritti di utilizzo. 

> [AZURE.TIP] È possibile proteggere i dati in Archiviazione di Azure usando [Crittografia del servizio di archiviazione di Azure](./storage/storage-service-encryption.md) per i dati inattivi. È anche possibile usare [Crittografia dischi di Azure](azure-security-disk-encryption.md) per proteggere i dati contenuti nei dischi virtuali usati per Macchine virtuali di Azure.

### Gateway di crittografia

I gateway di crittografia operano nei rispettivi livelli per fornire servizi di crittografia reinstradando ogni accesso ai dati basati sul cloud. Questo approccio non va confuso con quello di una rete privata virtuale (VPN). I gateway di crittografia sono progettati per fornire un livello trasparente alle soluzioni basate sul cloud.

I gateway di crittografia possono offrire un mezzo per gestire e proteggere i dati classificati come riservati crittografando i dati in transito, oltre che quelli inattivi.
 
I gateway di crittografia vengono inseriti nel flusso di dati tra i dispositivi utente e i data center applicazione per fornire servizi di crittografia/decrittografia. Queste soluzioni, come le VPN, sono prevalentemente soluzioni locali. Sono progettate per fornire alle terze parti il controllo sulle chiavi di crittografia, il che consente di ridurre il rischio di affidare a un solo provider la gestione sia dei dati che delle chiavi. Tali soluzioni sono progettate, analogamente alla crittografia, per operare facilmente e in modo trasparente tra gli utenti e il servizio.

> [AZURE.TIP] È possibile usare Azure ExpressRoute per estendere le reti locali in Microsoft Cloud tramite una connessione privata dedicata. Per altre informazioni su questa funzionalità, vedere [Panoramica tecnica relativa a ExpressRoute](./expressroute/expressroute-introduction.md). Un'altra opzione per la connettività cross-premise tra la rete locale e [Azure è una VPN da sito a sito](./vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

### Prevenzione della perdita dei dati 
La perdita dei dati (talvolta denominata fuga di dati) è un aspetto importante da considerare e la prevenzione della perdita dei dati esterni a causa di dipendenti malintenzionati o distratti è importantissima per molte organizzazioni.
 
Le tecnologie di prevenzione della perdita dei dati (DLP) consentono di assicurarsi che soluzioni come i servizi di posta elettronica non trasmettano i dati classificati come riservati. Le organizzazioni possono sfruttare le funzionalità DLP nei prodotti esistenti per evitare la perdita dei dati. Tali funzionalità usano criteri che possono essere creati facilmente da zero o usando un modello fornito dal provider di software.
 
Le tecnologie DLP possono eseguire un'analisi approfondita del contenuto tramite corrispondenze delle parole chiave, corrispondenze del dizionario, valutazione di espressioni regolari ed esame di altri contenuti per rilevare quelli che violano i criteri DLP organizzativi. La tecnologia DLP, ad esempio, consente di prevenire la perdita dei tipi seguenti di dati:

- Codici fiscali o numeri di carta di identità 
- Informazioni bancarie 
- Numeri di carta di credito  
- Indirizzi IP 

Alcune tecnologie DLP offrono anche la possibilità di eseguire l'override della configurazione DLP, ad esempio se un'organizzazione deve trasmettere informazioni sui codici fiscali a un processore di retribuzioni. È anche possibile configurare DLP in modo che gli utenti ricevano una notifica ancora prima che tentino di inviare informazioni sensibili che non devono essere trasmesse.

> [AZURE.TIP] È possibile usare le funzionalità DLP di Office 365 per proteggere i documenti. Per altre informazioni, vedere la pagina relativa ai [controlli di conformità di Office 365: prevenzione della perdita dei dati](https://blogs.office.com/2013/10/28/office-365-compliance-controls-data-loss-prevention/).

## Vedere anche

- [Procedure consigliate per la crittografia in Azure](azure-security-data-encryption-best-practices.md)
- [Procedure consigliate per la sicurezza con il controllo di accesso e la gestione delle identità di Azure](azure-security-identity-management-best-practices.md)
- [Blog del team di sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/)
- [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx)

<!---HONumber=AcomDC_0511_2016-->