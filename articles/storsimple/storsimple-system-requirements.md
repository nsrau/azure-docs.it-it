<properties 
   pageTitle="Requisiti di sistema StorSimple | Microsoft Azure" 
   description="Descrive i requisiti relativi a software, rete e alta disponibilità e le procedure consigliate per una soluzione Microsoft Azure StorSimple." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="alkohli" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="03/15/2016"
   ms.author="alkohli"/>

# Software, disponibilità elevata e requisiti di rete di StorSimple

## Panoramica

Benvenuti in Microsoft Azure StorSimple. Questo articolo descrive i requisiti di sistema importanti e le procedure consigliate per il dispositivo StorSimple e per i client di archiviazione che accedono al dispositivo. Prima di distribuire il sistema StorSimple è consigliabile leggere attentamente queste informazioni e quindi farvi riferimento, se necessario, durante la distribuzione e il successivo funzionamento.

I requisiti di sistema includono:

- **Requisiti software per i client di archiviazione**: descrive i sistemi operativi supportati e gli eventuali requisiti aggiuntivi per tali sistemi.
- **Requisiti di rete per il dispositivo StorSimple**: fornisce informazioni sulle porte che devono essere aperte nel firewall per consentire il traffico iSCSI, cloud o di gestione.
- **Requisiti di disponibilità elevata per StorSimple**: descrive i requisiti di disponibilità elevata e le procedure consigliate per il dispositivo StorSimple e il computer host. 


## Requisiti software per i client di archiviazione 

Di seguito sono indicati i requisiti software per i client di archiviazione che accedono al dispositivo StorSimple.

| Sistemi operativi supportati | Versione richiesta | Requisiti aggiuntivi/note |
| --------------------------- | ---------------- | ------------- |
| Windows Server | 2008 R2 SP1, 2012, 2012 R2 |I volumi iSCSI StorSimple sono supportati per l'uso solo con i tipi di disco di Windows seguenti:<ul><li>Volume semplice su disco di base</li><li>Volume semplice e con mirroring su disco dinamico</li></ul>Le funzionalità ODX e di thin provisioning di Windows Server 2012 sono supportate se si usa un volume iSCSI StorSimple.<br><br>StorSimple può creare volumi con thin provisioning e con provisioning completo. Non è in grado di creare volumi con provisioning parziale.<br><br>La riformattazione di un volume con thin provisioning può richiedere molto tempo. È consigliabile eliminare il volume e quindi crearne uno nuovo invece di riformattarlo. Se tuttavia si preferisce riformattare un volume:<ul><li>Eseguire il comando seguente prima della riformattazione per evitare ritardi nel recupero dello spazio: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Al termine della formattazione, eseguire il comando seguente per riabilitare il recupero dello spazio:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Applicare l'hotfix per Windows Server 2012 descritto nell'articolo [KB 2878635](https://support.microsoft.com/kb/2870270) al computer Windows Server.</li></ul></li></ul></ul> Se si configura Gestione snapshot StorSimple o l'adattatore StorSimple per SharePoint, passare a [Requisiti software per i componenti facoltativi](#software-requirements-for-optional-components).|
| VMWare ESX | 5\.1, 5.5 e 6.0 | Supportato con VMware vSphere come client iSCSI. Funzionalità VAAI-Block supportata con VMware vSphere in dispositivi StorSimple. 
| Linux RHEL/CentOS | 5 e 6 | Supporto per client Linux iSCSI con iniziatore Open-iSCSI versioni 5 e 6. |
| Linux | SUSE Linux 11 | |
 > [AZURE.NOTE] IBM AIX attualmente non è supportato con StorSimple.

## Requisiti software per i componenti facoltativi

I requisiti software seguenti riguardano i componenti facoltativi di StorSimple, ovvero Gestione snapshot StorSimple e l'adattatore StorSimple per SharePoint.

| Componente | Piattaforma host | Requisiti aggiuntivi/note |
| --------------------------- | ---------------- | ------------- |
| Gestione snapshot StorSimple | Windows Server 2008R2 SP1, 2012, 2012R2 | L'uso di Gestione snapshot StorSimple su Windows Server è necessario per eseguire il backup o il ripristino di dischi dinamici con mirroring e per qualsiasi backup coerente con l'applicazione.<br> Gestione snapshot StorSimple è supportato solo su Windows Server 2008 R2 SP1 (64 bit), Windows 2012 R2 e Windows Server 2012.<ul><li>Se si usa Windows Server 2012, è necessario installare .NET 3.5 o 4.5 prima di installare Gestione snapshot StorSimple.</li><li>Se si usa Windows Server 2008 R2 SP1, è necessario installare Windows Management Framework 3.0 prima di installare Gestione snapshot StorSimple.</li></ul> |
| Adattatore StorSimple per SharePoint | Windows Server 2008R2 SP1, 2012, 2012R2 |<ul><li>L'adattatore StorSimple per SharePoint è supportato solo in SharePoint 2010 e SharePoint 2013.</li><li>Per RBS è necessario SQL Server Enterprise Edition, versione 2008 R2 o 2012.</li></ul>|
 
## Requisiti di rete per il dispositivo StorSimple

Il dispositivo StorSimple è un dispositivo bloccato. È tuttavia necessario aprire le porte nel firewall per consentire il traffico iSCSI, cloud o di gestione. La tabella seguente mostra l'elenco delle porte che devono essere aperte nel firewall. In questa tabella, *in* *ingresso* fa riferimento alla direzione da cui le richieste client in ingresso accedono al dispositivo. *In* *uscita* fa riferimento alla direzione in cui il dispositivo StorSimple invia i dati all'esterno, oltre la distribuzione: ad esempio, in uscita verso Internet.

| Numero porta<sup>1, 2</sup> | In ingresso/In uscita | Ambito porta | Obbligatoria | Note |
|------------------------|-----------|------------|----------|-------| 
|TCP 80 (HTTP)<sup>3</sup>| In uscita | WAN | No |<ul><li>La porta in uscita viene usata per accedere a Internet e recuperare gli aggiornamenti.</li><li>Il proxy Web in uscita è configurabile dall'utente.</li><li>Per consentire gli aggiornamenti del sistema, questa porta deve inoltre essere aperta per gli IP fissi dei controller.</li></ul> |
|TCP 443 (HTTPS)<sup>3</sup>| In uscita | WAN | Sì |<ul><li>La porta in uscita viene usata per accedere ai dati nel cloud.</li><li>Il proxy Web in uscita è configurabile dall'utente.</li><li>Per consentire gli aggiornamenti del sistema, questa porta deve inoltre essere aperta per gli IP fissi dei controller.</li></ul>|
|UDP 53 (DNS) | In uscita | WAN | In alcuni casi; vedere le note. |Questa porta è obbligatoria solo se si usa un server DNS basato su Internet. |
| UDP 123 (NTP) | In uscita | WAN | In alcuni casi; vedere le note. |Questa porta è obbligatoria solo se si usa un server NTP basato su Internet. |
| TCP 9354 | In uscita | WAN | Sì |La porta in uscita viene usata dal servizio StorSimple per comunicare con il servizio StorSimple Manager. |
| 3260 (iSCSI) | In ingresso | LAN | No | Questa porta viene usata per accedere ai dati tramite iSCSI.|
| 5985 | In ingresso | LAN | No | La porta in ingresso viene usata da Gestione snapshot StorSimple per comunicare con il dispositivo StorSimple.<br>La porta viene usata anche per connettersi in remoto a Windows PowerShell per StorSimple tramite HTTP. |
| 5986 | In ingresso | LAN | No | Questa porta viene utilizzata quando ci si connette in modalità remota a Windows PowerShell per StorSimple tramite HTTPS. |

<sup>1</sup> Nessuna porta in ingresso deve essere aperta sulla rete Internet pubblica.

<sup>2</sup> Se per più porte è disponibile una configurazione del gateway, l'ordine del traffico instradato in uscita sarà determinato in base all'ordine di routing delle porte descritto in [Routing delle porte](#routing-metric) di seguito.

<sup>3</sup> Gli indirizzi IP fissi per il controller sul dispositivo StorSimple devono essere instradabili e in grado di connettersi a Internet. Gli indirizzi IP fissi vengono usati per l'installazione degli aggiornamenti nel dispositivo. Se i controller del dispositivo non possono connettersi a Internet tramite gli indirizzi IP fissi, non sarà possibile aggiornare il dispositivo StorSimple.

> [AZURE.IMPORTANT] Verificare che il firewall non modifichi o decrittografi il traffico SSL tra il dispositivo StorSimple e Azure.

### Modelli URL per le regole del firewall 

Gli amministratori di rete possono spesso configurare regole del firewall avanzate in base ai modelli URL in modo da filtrare il traffico in entrata e in uscita. Il dispositivo StorSimple e il servizio StorSimple Manager dipendono da altre applicazioni Microsoft, come ad esempio il bus di servizio di Azure, il controllo di accesso di Azure Active Directory, gli account di archiviazione e i server di Microsoft Update. I modelli URL associati a queste applicazioni possono essere usati per configurare le regole del firewall. È importante comprendere che i modelli di URL associati alle suddette applicazioni possono variare. Questo a sua volta richiederà, da parte dell'amministratore di rete, il monitoraggio e l'aggiornamento delle regole del firewall per StorSimple a seconda delle esigenze.

Nella maggior parte dei casi è consigliabile impostare liberamente le regole del firewall. Tuttavia, è possibile utilizzare le informazioni seguenti per impostare regole del firewall avanzate indispensabili per creare ambienti protetti.

> [AZURE.NOTE] Gli indirizzi IP (di origine) del dispositivo devono essere sempre impostati su tutte le interfacce di rete abilitate. Gli indirizzi IP di destinazione devono essere impostati sugli [intervalli IP dei data center di Azure](https://www.microsoft.com/it-IT/download/confirmation.aspx?id=41653).


| Modello URL | Componente/funzionalità | Indirizzi IP dispositivo |
|------------------------------------------------------------------|---------------------------------------------------------------|-----------------------------------------|
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` | Servizio StorSimple Manager<br>Servizio di controllo di accesso<br>Bus di servizio di Azure| Interfacce di rete abilitate per il cloud |
|`http://crl.microsoft.com/pki/*` |Revoca del certificato |Interfacce di rete abilitate per il cloud |
| `https://*.core.windows.net/*` | Account di archiviazione di Azure e monitoraggio | Interfacce di rete abilitate per il cloud |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com`| Server di Microsoft Update<br> | Solo indirizzi IP fissi del controller |
| `http://*.deploy.akamaitechnologies.com` |Rete CDN di Akamai |Solo indirizzi IP fissi del controller |
| `https://*.partners.extranet.microsoft.com/*` | Pacchetto di supporto | Interfacce di rete abilitate per il cloud |

### Metrica di routing

Una metrica di routing è associata alle interfacce e al gateway che instrada i dati alle reti specificate. La metrica di routing è usata dal protocollo di routing per calcolare il percorso migliore per una determinata destinazione, se apprende dell'esistenza di più percorsi per la stessa destinazione. Minore è la metrica di routing, maggiore è la preferenza.

Nel contesto di StorSimple, se sono configurate più interfacce di rete e gateway per il traffico del canale, le metriche di routing entreranno in gioco per determinare l'ordine relativo in cui le interfacce verranno usate. Le metriche di routing non possono essere modificate dall'utente. È tuttavia possibile usare il cmdlet `Get-HcsRoutingTable` per stampare la tabella di routing (e le metriche) sul dispositivo StorSimple. Altre informazioni sul cmdlet Get-HcsRoutingTable sono disponibili nell'articolo relativo alla [risoluzione dei problemi di distribuzione di StorSimple](storsimple-troubleshoot-deployment.md).

Gli algoritmi delle metriche di routing sono diversi a seconda della versione del software in esecuzione sul dispositivo StorSimple.

**Versioni precedenti all'aggiornamento 1**

Comprendono le versioni software precedenti all'aggiornamento 1, ad esempio la versione GA, 0.1, 0.2 o 0.3. L'ordine basato sulle metriche di routing è il seguente:

   *Ultima interfaccia di rete 10 GbE configurata > Altra interfaccia di rete 10 GbE > Ultima interfaccia di rete 1 GbE configurata > Altra interfaccia di rete 1 GbE*


**Versioni a partire dall'aggiornamento 1 e precedenti all'aggiornamento 2**

Sono comprese le versioni software come 1, 1.1 e 1.2. L'ordine in base alle metriche di routing viene deciso come segue:

   *DATA 0 > Ultima interfaccia di rete 10 GbE configurata > Altra interfaccia di rete 10 GbE > Ultima interfaccia di rete 1 GbE configurata > Altra interfaccia di rete 1 GbE*

   In Update 1 la metrica di routing assegna a DATA 0 la massima priorità. Pertanto, tutto il traffico cloud viene indirizzato tramite DATA 0. Tenere presente questo criterio se sono presenti più interfacce di rete abilitate per il cloud nel dispositivo StorSimple.


**Versioni a partire dall'aggiornamento 2**

L'aggiornamento 2 presenta diversi miglioramenti correlati alle reti e le metriche di routing sono cambiate. Il comportamento può essere spiegato come segue.

- È stato assegnato un set di valori predefiniti alle interfacce di rete. 	
		
- Si consideri la tabella di esempio seguente con valori assegnati alle varie interfacce di rete quando sono abilitate o disabilitate per il cloud, ma con un gateway configurato. Si noti che i valori assegnati qui sono puramente esemplificativi.

		
	| Interfaccia di rete | Abilitata per il cloud | Disabilitata per il cloud con gateway |
	|-----|---------------|---------------------------|
	| Data 0 | 1 | - |
	| Data 1 | 2 | 20 |
	| Data 2 | 3 | 30 |
	| Data 3 | 4 | 40 |
	| Data 4 | 5 | 50 |
	| Data 5 | 6 | 60 |


- L'ordine in cui il traffico cloud verrà instradato tramite le interfacce di rete è:
	 
	*Data 0 > Data 1 > Data 2 > Data 3 > Data 4 > Data 5*

	Questo può essere spiegato dall'esempio seguente.

	Si consideri un dispositivo StorSimple con due interfacce di rete abilitate per il cloud, Data 0 e Data 5. Le interfacce da Data 1 a Data 4 sono disabilitate per il cloud ma dispongono di un gateway configurato. L'ordine in cui verrà instradato il traffico per questo dispositivo sarà:

	*Data 0 (1) > Data 5 (6) > Data 1 (20) > Data 2 (30) > Data 3 (40) > Data 4 (50)*
	
	*in cui i numeri tra parentesi indicano le rispettive metriche di routing.*
	
	Se Data 0 restituisce un errore, il traffico cloud verrà instradato tramite Data 5. Poiché un gateway è configurato su tutte le altre reti, se sia Data 0 che Data 5 restituiscono un errore il traffico cloud verrà instradato tramite Data 1.
 

- Se un'interfaccia di rete abilitata per il cloud restituisce un errore, vengono eseguiti 3 tentativi con un ritardo di 30 secondi per connettersi all'interfaccia. Se tutti i tentativi hanno esito negativo, il traffico viene instradato alla successiva interfaccia abilitata per il cloud disponibile, come determinato dalla tabella di routing. Se tutte le interfacce di rete abilitate per il cloud restituiscono un errore, il dispositivo eseguirà il failover a un altro controller (in questo caso non verrà eseguito nessun riavvio).
	
- Se si verifica un errore VIP per un'interfaccia di rete abilitata per iSCSI, verranno eseguiti 3 tentativi con un ritardo di 2 secondi. Questo comportamento è analogo alle versioni precedenti. Se tutte le interfacce di rete iSCSI restituiscono un errore, si verificherà un failover del controller (accompagnato da un riavvio).


- Viene anche generato un avviso sul dispositivo StorSimple quando si verifica un errore VIP. Per altre informazioni, vedere l'articolo [Avviso riferimento rapido](storsimple-manage-alerts.md).
	
- Per quanto riguarda i tentativi, iSCSI avrà la precedenza sul cloud.

	Si consideri l'esempio seguente: un dispositivo StorSimple ha due interfacce di rete abilitate, Data 0 e Data 1. Data 0 è abilitata per il cloud, mentre Data 1 è abilitata sia per il cloud che per iSCSI. Nessun'altra interfaccia di rete su questo dispositivo è abilitata per il cloud o iSCSI.
		
	Se Data 1 restituisce un errore, poiché è l'ultima interfaccia di rete iSCSI si verificherà un failover del controller a Data 1 sull'altro controller.


### Procedure di rete consigliate

Oltre ai requisiti di rete sopra illustrati, per ottenere prestazioni ottimali nell'uso della soluzione StorSimple, è opportuno rispettare le procedure consigliate seguenti:

- Verificare che per il dispositivo StorSimple sia sempre disponibile una larghezza di banda dedicata pari a 40 Mbps (o superiore). La larghezza di banda non deve essere condivisa con altre applicazioni.

- Verificare che la connettività di rete a Internet sia sempre disponibile. Connessioni Internet sporadiche o non affidabili ai dispositivi, o nessuna connettività a Internet, hanno per effetto una configurazione non supportata.

- Isolare il traffico iSCSI e cloud prevedendo nel dispositivo interfacce di rete dedicate per l'accesso iSCSI e cloud. Per altre informazioni, vedere la procedura per [modificare le interfacce di rete](storsimple-modify-device-config.md#modify-network-interfaces) sul dispositivo StorSimple.

- Evitare di usare una configurazione LACP (Link Aggregation Control Protocol) per le interfacce di rete. Si tratta di una configurazione non supportata.


## Requisiti di disponibilità elevata per StorSimple

La piattaforma hardware inclusa nella soluzione StorSimple offre funzionalità di disponibilità e affidabilità e costituisce la base per un'infrastruttura di archiviazione con disponibilità elevata e a tolleranza di errore nel data center. Tuttavia, esistono requisiti e procedure consigliate da seguire per avere la certezza che la soluzione StorSimple sia sempre disponibile. Prima di distribuire StorSimple, esaminare attentamente i requisiti e le procedure consigliate seguenti per il dispositivo StorSimple e i computer host connessi.

Per altre informazioni sul monitoraggio e il mantenimento dei componenti hardware del dispositivo StorSimple, vedere [Utilizzare il servizio StorSimple Manager per monitorare le componenti hardware e lo stato](storsimple-monitor-hardware-status.md) e [Sostituzione di componenti hardware di StorSimple](storsimple-hardware-component-replacement.md).

### Procedure e requisiti di disponibilità elevata per il dispositivo StorSimple

Esaminare con attenzione le informazioni seguenti per avere la certezza della disponibilità elevata del dispositivo StorSimple.

#### PCM

I dispositivi StorSimple includono moduli di alimentazione e raffreddamento (PCM, Power and Cooling Module) ridondanti con supporto per lo swapping a caldo. Ogni PCM dispone di capacità sufficiente per fornire il servizio per l'intero chassis. Per avere la certezza della disponibilità elevata, entrambi i PCM devono essere installati.

- Collegare i PCM a fonti di alimentazione differenti per garantire la disponibilità in caso di interruzione dell'alimentazione.
- In caso di errore di un PCM, richiedere immediatamente una sostituzione.
- Rimuovere un PCM non funzionante solo quando il modulo sostitutivo è disponibile e si è pronti a effettuare l'installazione.
- Non rimuovere tutti e due i PCM contemporaneamente. Il modulo PCM include il modulo batteria di backup. La rimozione di entrambi i PCM comporterà un arresto non protetto dalla batteria e lo stato del dispositivo non verrà salvato. Per altre informazioni sulla batteria, vedere [Mantenere il modulo della batteria di backup](storsimple-battery-replacement.md#maintain-the-backup-battery-module).

#### Moduli controller

I dispositivi StorSimple comprendono moduli controller ridondanti con supporto per lo swapping a caldo. Questi moduli funzionano in modalità attiva o passiva. In qualsiasi momento, un modulo controller è attivo e fornisce il servizio, mentre l'altro modulo è passivo. Il modulo controller passivo è acceso e diventa operativo se quello attivo non funziona o viene rimosso. Ogni modulo controller dispone di capacità sufficiente per fornire il servizio per l'intero chassis. Per avere la certezza della disponibilità elevata, entrambi i moduli controller devono essere installati.

- Verificare che entrambi i moduli controller siano sempre installati.

- In caso di errore di un modulo controller, richiedere immediatamente una sostituzione.

- Rimuovere un modulo controller non funzionante solo quando il modulo sostitutivo è disponibile e si è pronti a effettuare l'installazione. La rimozione di un modulo per periodi prolungati influirà sul flusso d'aria e quindi sul raffreddamento del sistema.

- Assicurarsi che le connessioni di rete a entrambi i moduli controller siano identiche e che le interfacce di rete connesse abbiano la stessa configurazione di rete.

- Se un modulo controller smette di funzionare o deve essere sostituito, assicurarsi che l'altro modulo si trovi in stato attivo prima di procedere alla sostituzione. Per verificare che un controller sia attivo, vedere [Identificare il controller attivo sul dispositivo](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).

- Non rimuovere tutti e due i moduli controller contemporaneamente. Se è in corso il failover di un controller, non arrestare il modulo controller in standby né rimuoverlo dallo chassis.

- Dopo il failover di un controller, attendere almeno cinque minuti prima di rimuovere uno dei moduli.

#### Interfacce di rete

Ogni modulo controller del dispositivo StorSimple ha quattro interfacce di rete 1 Gigabit e due interfacce di rete 10 Gigabit Ethernet.

- Verificare che le connessioni di rete a entrambi i moduli controller siano identiche e che le interfacce di rete a cui sono connesse le interfacce dei moduli abbiano la stessa configurazione di rete.

- Quando possibile, distribuire le connessioni di rete tra diversi commutatori per avere la certezza della disponibilità del servizio in caso di errore di un dispositivo di rete.

- Quando si scollega l'unica o l'ultima interfaccia abilitata per iSCSI rimanente (con indirizzi IP assegnati), disabilitare innanzitutto l'interfaccia e quindi scollegare i cavi. Se si scollega prima l'interfaccia, si verificherà il failover del controller attivo in quello passivo. Se anche le interfacce del controller passivo sono scollegate, entrambi i controller verranno riavviati più volte prima che venga ristabilita la connessione per uno dei controller.

- Collegare alla rete almeno due interfacce DATA da ogni modulo controller.

- Se sono state abilitate le due interfacce 10 GbE, distribuirle tra diversi commutatori.

- Quando possibile, usare la funzionalità MPIO sui server per assicurarsi che i server possano tollerare un problema di collegamento, rete o interfaccia.

Per altre informazioni sulle funzionalità di rete del dispositivo per disponibilità e prestazioni elevate, vedere [Installare il dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) o [Installare il dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### SSD e HDD

I dispositivi StorSimple includono unità SSD (Solid State Drive) e unità disco rigido protette mediante spazi con mirroring. Mediante gli spazi con mirroring è possibile assicurarsi che il dispositivo sia in grado di tollerare l'errore di una o più unità SSD o HDD.

- Accertarsi che tutti i moduli SSD e HDD siano installati.

- In caso di errore di un'unità SSD o HDD, richiedere immediatamente una sostituzione.

- In caso di errore di un'unità SSD o HDD o se è necessaria la sostituzione, assicurarsi di rimuovere solo l'unità SSD o HDD che richiede la sostituzione.

- Non rimuovere più di un'unità SSD o HDD dal sistema in qualsiasi momento. Un errore di due o più dischi di un determinato tipo (HDD, SSD) o più errori consecutivi in un breve intervallo di tempo possono provocare un problema di funzionamento del sistema e una potenziale perdita di dati. In questo caso, [contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md) per assistenza.

- Durante la sostituzione, monitorare **Stato hardware** nella pagina **Manutenzione** delle unità SSD e HDD. Un segno di spunta verde indica che i dischi funzionano correttamente, mentre un punto esclamativo rosso indica un problema di funzionamento di un'unità SSD o HDD.

- È consigliabile configurare snapshot nel cloud per tutti i volumi che è necessario proteggere in caso di errore di sistema.

#### Chassis EBOD

Il modello 8600 del dispositivo StorSimple include uno chassis EBOD (Extended Bunch of Disks) in aggiunta a quello principale. Uno chassis EBOD contiene controller EBOD e unità HDD protetti mediante spazi con mirroring. Grazie all'uso di spazi con mirroring si ha la sicurezza che il dispositivo sia in grado di tollerare l'errore di una o più unità HDD. Lo chassis EBOD è connesso allo chassis principale mediante cavi SAS ridondanti.

- Verificare che entrambi i moduli controller dello chassis EBOD, entrambi i cavi SAS e tutte le unità disco rigido siano sempre installati.

- In caso di guasto di un modulo controller dell'enclosure EBOD, richiedere immediatamente una sostituzione.

- Se un modulo controller dello chassis EBOD smette di funzionare, prima di sostituirlo assicurarsi che l'altro modulo controller sia attivo. Per verificare che un controller sia attivo, vedere [Identificare il controller attivo sul dispositivo](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).

- Durante la sostituzione di un modulo controller EBOD, monitorare costantemente lo stato del componente nel servizio StorSimple Manager accedendo a **Manutenzione** - **Stato hardware**.

- Se un cavo SAS non funziona o deve essere sostituito (per determinare lo stato del cavo, coinvolgere il supporto tecnico Microsoft), assicurarsi di rimuovere solo il cavo SAS che richiede la sostituzione.

- Non rimuovere contemporaneamente entrambi i cavi SAS dal sistema in qualsiasi momento.

### Indicazioni sulla disponibilità elevata per i computer host

Esaminare attentamente le procedure consigliate seguenti per assicurare la disponibilità elevata degli host connessi al dispositivo StorSimple.

- Configurare StorSimple con [configurazioni cluster di file server a 2 nodi][1]. Se si rimuovono singoli punti di errore e si crea ridondanza sul lato host, l'intera soluzione diventa altamente disponibile.

- Usare condivisioni disponibili in modo continuo con Windows Server 2012 (SMB 3.0) per la disponibilità elevata durante il failover dei controller di archiviazione. Per altre informazioni sulla configurazione di cluster di file server e sulle condivisioni disponibili in modo continuo con Windows Server 2012, vedere questo [video dimostrativo](http://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## Passaggi successivi

- [Ulteriori informazioni sui Limiti di StorSimple](storsimple-limits.md).
- [Informazioni su come distribuire la soluzione StorSimple](storsimple-deployment-walkthrough-u2.md).
 
<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx

<!----HONumber=AcomDC_0316_2016-->