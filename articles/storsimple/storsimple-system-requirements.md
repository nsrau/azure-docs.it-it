<properties 
   pageTitle="Requisiti di sistema di StorSimple" 
   description="Descrizione dei requisiti di sistema e delle procedure consigliate per il software, la disponibilità elevata e le funzionalità di rete in una soluzione StorSimple di Azure." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="alkohli" 
   manager="AdinaH" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="06/03/2015"
   ms.author="alkohli"/>

# Requisiti di sistema di StorSimple

## Panoramica

Benvenuti in Microsoft Azure StorSimple. Questo articolo descrive i requisiti di sistema importanti e le procedure consigliate per il dispositivo StorSimple e per i client di archiviazione che accedono al dispositivo. Tra i requisiti sono inclusi i seguenti:

- **Requisiti software per i client di archiviazione**: descrive i sistemi operativi supportati e gli eventuali requisiti aggiuntivi per tali sistemi.
- **Requisiti di disponibilità elevata per StorSimple**: descrive i requisiti di disponibilità elevata e le procedure consigliate per il dispositivo StorSimple e il computer host. 
- **Requisiti di rete per il dispositivo StorSimple**: fornisce informazioni sulle porte che devono essere aperte nel firewall per consentire il traffico iSCSI, cloud o di gestione.

Prima di distribuire il sistema StorSimple di Azure è consigliabile leggere attentamente le informazioni seguenti e quindi fare riferimento a tali informazioni durante la distribuzione e il funzionamento successivo.

## Requisiti software per i client di archiviazione 

Di seguito sono indicati i requisiti software per i client di archiviazione che accedono al dispositivo StorSimple.

| Sistemi operativi supportati | Versione richiesta | Dettagli/Note |
| --------------------------- | ---------------- | ------------- |
| Windows Server | 2008 R2 SP1, 2012, 2012 R2 |<ul><li>I volumi iSCSI StorSimple sono supportati solo per l'uso nei tipi di dischi Windows seguenti:<ul><li>Volume semplice su disco di base</li><li>Volume semplice e con mirroring su disco dinamico</li></ul>L'uso di Gestione snapshot StorSimple su Windows Server è necessario per eseguire il backup o il ripristino di dischi dinamici con mirroring e per tutti i backup coerenti con l'applicazione.</li><li>Gestione snapshot StorSimple è supportato solo su Windows Server 2008 R2 SP1 (64 bit), Windows 2012 R2 e Windows Server 2012. <ul><li>Se si usa Windows Server 2012, è necessario installare .NET 3.5 o 4.5 prima di installare Gestione snapshot StorSimple.</li><li>Se si usa Windows Server 2008 R2 SP1, prima di installare Gestione snapshot StorSimple è necessario installare Windows Management Framework 3.0.</li></ul></li><li>Le funzionalità ODX e di thin provisioning di Windows Server 2012 sono supportate se si usa un volume iSCSI StorSimple.<ul><li>StorSimple può creare solo volumi con thin provisioning. Non è in grado di creare volumi con provisioning completo o parziale.</li><li>La riformattazione di un volume con thin provisioning può richiedere molto tempo. È consigliabile eliminare il volume e quindi crearne uno nuovo invece di riformattarlo.</li><li>Se si preferisce comunque riformattare un volume:<ul><li>Eseguire il comando seguente prima della riformattazione per evitare ritardi nel recupero dello spazio: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Al termine della formattazione, eseguire il comando seguente per riabilitare il recupero dello spazio:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Applicare l'hotfix per Windows Server 2012, come descritto nell'articolo KB2870270 della Knowledge Base, al computer Windows Server.</li></ul></li></ul><li>L'adattatore StorSimple per SharePoint è supportato solo in SharePoint 2010 e SharePoint 2013. Per RBS è necessario SQL Server Enterprise Edition, versioni 2008 R2 e 2012.</li></ul>|
| VMWare | Testato con VMware vSphere 5.1 come client iSCSI | La funzionalità VAAI-Block è testata con VMware vSphere v5.1 nei dispositivi StorSimple. 
| Linux RHEL/CentOS | Supporto per client Linux iSCSI con iniziatore Open-iSCSI versioni 5 e 6 | Supportato solo con iniziatore Open-iSCSI. |
| Linux | SUSE Linux 11 | |
 >[AZURE.NOTE]In base a test limitati eseguiti internamente, IBM AIX attualmente non è supportato con StorSimple.

## Requisiti di disponibilità elevata per StorSimple

La piattaforma hardware inclusa nella soluzione StorSimple offre caratteristiche di disponibilità e affidabilità di livello aziendale e costituisce la base per un'infrastruttura di archiviazione con disponibilità e tolleranza di errore elevate per il data center. Tuttavia, esistono requisiti e procedure consigliate da seguire per avere la certezza che la soluzione StorSimple di Azure sia sempre disponibile. Prima di distribuire StorSimple di Azure, esaminare attentamente i requisiti e le procedure consigliate seguenti per il dispositivo StorSimple e i computer host connessi.

### Procedure e requisiti di disponibilità elevata per il dispositivo StorSimple

Esaminare con attenzione le informazioni seguenti per avere la certezza della disponibilità elevata del dispositivo StorSimple.

#### PCM (Power and Cooling Modules)

I dispositivi StorSimple includono moduli PCM ridondanti con supporto per lo swapping a caldo. Ogni PCM dispone di capacità sufficiente per fornire il servizio per l'intero chassis. Per avere la certezza della disponibilità elevata, entrambi i PCM devono essere installati.

- Collegare i PCM a fonti di alimentazione differenti per garantire la disponibilità in caso di interruzione dell'alimentazione.

- In caso di errore di un PCM, richiedere immediatamente una sostituzione.

- Rimuovere un PCM non funzionante solo quando il modulo sostitutivo è disponibile e si è pronti a effettuare l'installazione.

- Non rimuovere tutti e due i PCM contemporaneamente. Il modulo PCM include la batteria. La rimozione di entrambi i moduli PCM comporterà un arresto non protetto della batteria.

#### Moduli controller

I dispositivi StorSimple comprendono moduli controller ridondanti con supporto per lo swapping a caldo. Questi moduli funzionano in modalità attiva o passiva. In qualsiasi momento, un modulo controller è attivo e fornisce il servizio, mentre l'altro modulo è passivo. Il modulo controller passivo è acceso e diventa operativo se quello attivo non funziona o viene rimosso. Ogni modulo controller dispone di capacità sufficiente per fornire il servizio per l'intero chassis. Per avere la certezza della disponibilità elevata, entrambi i moduli controller devono essere installati.

- Verificare che entrambi i moduli controller siano sempre installati.

- In caso di errore di un modulo controller, richiedere immediatamente una sostituzione.

- Rimuovere un modulo controller non funzionante solo quando il modulo sostitutivo è disponibile e si è pronti a effettuare l'installazione. La rimozione di un modulo per periodi prolungati influirà sul flusso d'aria e quindi sul raffreddamento del sistema.

- Assicurarsi che le connessioni di rete a entrambi i moduli controller siano identiche e che le interfacce di rete connesse abbiano la stessa configurazione di rete.

- Se un modulo controller smette di funzionare o deve essere sostituito, assicurarsi che l'altro modulo si trovi in stato attivo prima di procedere alla sostituzione.

- Non rimuovere tutti e due i moduli controller contemporaneamente.

- Se è in corso il failover di un controller, non arrestare il modulo controller in standby né rimuoverlo dallo chassis.

- Dopo il failover di un controller, attendere almeno cinque minuti prima di rimuovere uno dei moduli.

#### Interfacce di rete

Ogni modulo controller del dispositivo StorSimple ha quattro interfacce di rete 1 Gigabit e due interfacce di rete 10 Gigabit Ethernet.

- Verificare che le connessioni di rete a entrambi i moduli controller siano identiche e che le interfacce di rete a cui sono connesse le interfacce dei moduli abbiano la stessa configurazione di rete.

- Quando possibile, distribuire le connessioni di rete tra diversi commutatori per avere la certezza della disponibilità del servizio in caso di errore di un dispositivo di rete.

- Quando si scollega l'unica o l'ultima interfaccia abilitata per iSCSI rimanente (con indirizzi IP assegnati), disabilitare innanzitutto l'interfaccia e quindi scollegare i cavi. Se si scollega prima l'interfaccia, si verificherà il failover del controller attivo in quello passivo. Se anche le interfacce del controller passivo sono scollegate, entrambi i controller verranno riavviati più volte prima che venga ristabilita la connessione per uno dei controller.

- Collegare alla rete almeno due interfacce DATA da ogni modulo controller.

- Se sono state abilitate le due interfacce 10 GbE, distribuirle tra diversi commutatori.

- Quando possibile, usare la funzionalità MPIO sui server per assicurarsi che i server possano tollerare un problema di collegamento, rete o interfaccia.

Per altre informazioni sulle funzionalità di rete del dispositivo per disponibilità e prestazioni elevate, vedere [Cablare il dispositivo 8100](https://msdn.microsoft.com/library/azure/dn757738.aspx) o [Cablare il dispositivo 8600](https://msdn.microsoft.com/library/azure/dn757762.aspx).

#### SSD e HDD

I dispositivi StorSimple includono unità SSD (Solid State Drive, unità a stato solido) e HDD (Hard Disk Drive, unità disco rigido) che sono protette mediante spazi con mirroring. Inoltre, per le unità HDD viene fornito un disco di riserva a caldo. Mediante gli spazi con mirroring è possibile assicurarsi che il dispositivo sia in grado di tollerare l'errore di una o più unità SSD o HDD.

- Accertarsi che tutti i moduli SSD e HDD siano installati.

- In caso di errore di un'unità SSD o HDD, richiedere immediatamente una sostituzione.

- In caso di errore di un'unità SSD o HDD o se è necessaria la sostituzione, assicurarsi di rimuovere solo l'unità SSD o HDD che richiede la sostituzione.

- Non rimuovere più di un'unità SSD o HDD dal sistema in qualsiasi momento.

- Durante la sostituzione, monitorare **Stato hardware** nella pagina **Manutenzione** delle unità SSD e HDD. Un segno di spunta verde indica che i dischi funzionano correttamente, mentre un punto esclamativo rosso indica un problema di funzionamento di un'unità SSD o HDD.

- Un errore di due o più dischi di un determinato tipo (HDD, SSD) o più errori consecutivi in un breve intervallo di tempo possono provocare un problema di funzionamento del sistema e una potenziale perdita di dati. In questo caso, contattare il supporto tecnico per assistenza. È consigliabile configurare snapshot nel cloud per tutti i volumi che è necessario proteggere in caso di errore di sistema.

#### Chassis EBOD

Il modello 8600 del dispositivo StorSimple include uno chassis EBOD (Extended Bunch of Disks) in aggiunta a quello principale. Uno chassis EBOD contiene controller EBOD e unità HDD protetti mediante spazi con mirroring. Grazie all'uso di spazi con mirroring si ha la sicurezza che il dispositivo sia in grado di tollerare l'errore di una o più unità HDD. Lo chassis EBOD è connesso allo chassis principale mediante cavi SAS ridondanti.

- Verificare che entrambi i moduli controller dello chassis EBOD, entrambi i cavi SAS e tutte le unità disco rigido siano sempre installati.

- In caso di errore di un modulo controller dello chassis EBOD o di un'unità HDD, richiedere immediatamente una sostituzione.

- Se un modulo controller dello chassis EBOD smette di funzionare, prima di sostituirlo assicurarsi che l'altro modulo controller sia attivo.

- In caso di errore di un'unità HDD o se è necessaria la sostituzione, assicurarsi di rimuovere solo l'unità HDD che richiede la sostituzione. Non rimuovere un'unità HDD finché i dischi e l'array non risultano funzionare correttamente.

- Non rimuovere più di un'unità HDD dal sistema in qualsiasi momento.

- Durante la sostituzione di un modulo controller EBOD o di un'unità HDD, monitorare costantemente lo stato del componente appropriato nel servizio StorSimple Manager mediante **Manutenzione** - **Stato hardware**.

- Se un cavo SAS non funziona o deve essere sostituito (per determinare lo stato del cavo, coinvolgere il supporto tecnico Microsoft), assicurarsi di rimuovere solo il cavo SAS che richiede la sostituzione.

- Non rimuovere contemporaneamente entrambi i cavi SAS dal sistema in qualsiasi momento.

### Requisiti di disponibilità elevata per i computer host

Esaminare attentamente i requisiti e le procedure consigliate seguenti per avere la certezza della disponibilità elevata degli host connessi al dispositivo StorSimple.

- Configurare StorSimple con [configurazioni cluster di file server a 2 nodi] (https://technet.microsoft.com/library/cc731844(v=WS.10).aspx). Se si rimuovono singoli punti di errore e si crea ridondanza sul lato host, l'intera soluzione diventa altamente disponibile.

- Usare condivisioni disponibili in modo continuo con Windows Server 2012 (SMB 3.0) per la disponibilità elevata durante il failover dei controller di archiviazione. Per altre informazioni sulla configurazione di cluster di file server e sulle condivisioni disponibili in modo continuo con Windows Server 2012, vedere questo [video dimostrativo](http://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## Requisiti di rete per il dispositivo StorSimple

Il dispositivo StorSimple è un dispositivo bloccato. È tuttavia necessario aprire le porte nel firewall per consentire il traffico iSCSI, cloud o di gestione. La tabella seguente mostra l'elenco delle porte che devono essere aperte nel firewall. In questa tabella, *in* *ingresso* fa riferimento alla direzione da cui le richieste client in ingresso accedono al dispositivo. *In* *uscita* fa riferimento alla direzione in cui il dispositivo StorSimple invia i dati all'esterno, oltre la distribuzione: ad esempio, in uscita verso Internet.

| Numero porta<sup>1, 2</sup> | In ingresso/In uscita | Ambito porta | Obbligatoria | Note |
|------------------------|-----------|------------|----------|-------| 
|TCP 80 (HTTP)<sup>3</sup>| In uscita | WAN | No |<ul><li>La porta in uscita viene usata per accedere a Internet e recuperare gli aggiornamenti.</li><li>Il proxy Web in uscita è configurabile dall'utente.</li><li>Per consentire gli aggiornamenti del sistema, questa porta deve inoltre essere aperta per gli IP fissi dei controller.</li></ul> |
|TCP 443 (HTTPS)<sup>3</sup>| In uscita | WAN | Sì |<ul><li>La porta in uscita viene usata per accedere ai dati nel cloud.</li><li>Il proxy Web in uscita è configurabile dall'utente.</li><li>Per consentire gli aggiornamenti del sistema, questa porta deve inoltre essere aperta per gli IP fissi dei controller.</li></ul>|
|UDP 53 (DNS) | In uscita | WAN | In alcuni casi; vedere le note. |Questa porta è obbligatoria solo se si usa un server DNS basato su Internet. |
| UDP 123 (NTP) | In uscita | WAN | In alcuni casi; vedere le note. |Questa porta è obbligatoria solo se si usa un server NTP basato su Internet. |
| TCP 9354 | In uscita | WAN | In alcuni casi; vedere le note. |La porta in uscita viene usata dal servizio StorSimple Manager per comunicare con il dispositivo. Questa porta è necessaria se la rete corrente non supporta l'uso di HTTP 1.1 per connettersi a Internet, ad esempio quando si usa un server proxy basato su HTTP 1.0.<br> Se ci si connette tramite un server proxy, fare riferimento ai [requisiti del bus di servizio](https://msdn.microsoft.com/library/azure/ee706729.aspx) per informazioni dettagliate. |
| 3260 (iSCSI) | In ingresso | LAN | No | Questa porta viene usata per accedere ai dati tramite iSCSI.|
| 5985 | In ingresso | LAN | No | La porta in ingresso viene usata da Gestione snapshot StorSimple per comunicare con il dispositivo StorSimple.<br>La porta viene usata anche per connettersi in remoto a Windows PowerShell per StorSimple tramite HTTP o HTTPS. |

<sup>1</sup> Nessuna porta in ingresso deve essere aperta sulla rete Internet pubblica.

<sup>2</sup> Se per più porte esiste una configurazione gateway, l'ordine del traffico instradato in uscita verrà determinato in base all'ordine di routing delle porte descritto di seguito.

<sup>3</sup> Gli indirizzi IP fissi per il controller sul dispositivo StorSimple devono essere instradabili e in grado di connettersi a Internet. Gli indirizzi IP fissi vengono usati per l'installazione degli aggiornamenti nel dispositivo. Se i controller del dispositivo non possono connettersi a Internet tramite gli indirizzi IP fissi, non sarà possibile aggiornare il dispositivo StorSimple.

### Routing delle porte

Il routing delle porte varia in base alla versione del software in esecuzione nel dispositivo StorSimple.

- Se il dispositivo esegue una versione del software precedente Update 1, ad esempio GA, 0.1, 0.2 o 0.3, il routing delle porte viene stabilito in base al criterio di priorità seguente:

     Ultima interfaccia di rete 10 GbE configurata > Altra interfaccia di rete 10 GbE > Ultima interfaccia di rete 1 GbE configurata > Altra interfaccia di rete 1 GbE

- Se il dispositivo esegue Update 1, il routing delle porte viene stabilito in base al criterio di priorità seguente:

     DATA 0 > Ultima interfaccia di rete 10 GbE configurata > Altra interfaccia di rete 10 GbE > Ultima interfaccia di rete 1 GbE configurata > Altra interfaccia di rete 1 GbE

In Update 1 la metrica di routing assegna a DATA 0 la massima priorità. Pertanto, tutto il traffico cloud viene indirizzato tramite DATA 0. Tenere presente questo criterio se sono presenti più interfacce di rete abilitate per il cloud nel dispositivo StorSimple.

### Procedure di rete consigliate

Oltre ai requisiti di rete sopra illustrati, per ottenere prestazioni ottimali nell'uso della soluzione StorSimple, è opportuno rispettare le procedure consigliate seguenti:

- Verificare che per il dispositivo StorSimple sia sempre disponibile una larghezza di banda dedicata pari a 40 Mbps (o superiore). La larghezza di banda non deve essere condivisa con altre applicazioni.

- Verificare che la connettività di rete a Internet sia sempre disponibile. Connessioni Internet sporadiche o non affidabili ai dispositivi, o nessuna connettività a Internet, hanno per effetto una configurazione non supportata.

- Isolare il traffico iSCSI e cloud prevedendo nel dispositivo interfacce di rete dedicate per l'accesso iSCSI e cloud. Per altre informazioni, vedere la procedura di [configurazione delle interfacce di rete](https://msdn.microsoft.com/library/dn772371.aspx) sul dispositivo StorSimple.

## Passaggi successivi

- [Limiti di StorSimple](storsimple-limits.md)
- [Distribuire la soluzione StorSimple](storsimple-deployment-walkthrough.md)
 

<!---HONumber=July15_HO2-->