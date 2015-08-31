<properties 
    pageTitle="Note sulla versione dell'aggiornamento 0.1 di StorSimple serie 8000 - Ottobre 2014 | Microsoft Azure"
    description="Vengono descritte le nuove funzionalità, i problemi e le soluzioni alternative per la versione di ottobre 2014 di StorSimple."
    services="storsimple"
    documentationCenter="NA"
    authors="SharS"
    manager="carolz"
    editor="" />
 <tags 
    ms.service="storsimple"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="TBD"
    ms.date="08/19/2015"
    ms.author="v-sharos" />

# Note sulla versione dell'aggiornamento 0.1 di StorSimple serie 8000 - Ottobre 2014  

## Panoramica

Nelle note sulla versione seguenti sono indicati i problemi critici non risolti relativi all'aggiornamento 0.1 di StorSimple serie 8000 rilasciato a ottobre 2014. Contengono inoltre un elenco degli aggiornamenti software e firmware di StorSimple inclusi in questa versione. Si tratta della prima versione dopo la versione di rilascio di StorSimple serie 8000 resa disponibile a livello generale a luglio 2014 e corrisponde alla versione del software 6.3.9600.17312.

Subito dopo l'installazione del dispositivo, si consiglia di ricercare e applicare eventuali aggiornamenti disponibili. È inoltre possibile attivare gli aggiornamenti automatici per scaricare e installare gli aggiornamenti ad alta priorità non appena vengono rilasciati da Microsoft. Per ulteriori informazioni, vedere come [Aggiornare il dispositivo StorSimple](storsimple-update-device.md).

Prima di distribuire gli aggiornamenti nella soluzione StorSimple, esaminare le informazioni contenute nelle note sulla versione.

>[AZURE.IMPORTANT]
> 
-	Per installare l'aggiornamento di ottobre, utilizzare il servizio StorSimple Manager e non Windows PowerShell per StorSimple.  
-	Solitamente, per il completamento degli aggiornamenti, sono necessarie circa 3 ore.  
-	La versione di ottobre di StorSimple non contiene aggiornamenti per il dispositivo virtuale StorSimple. È ancora possibile applicare tutti gli aggiornamenti di Windows disponibili, tra cui le recenti correzioni della sicurezza ma, per il dispositivo virtuale, non è possibile visualizzare modifiche nella versione.  

Prima di effettuare l’aggiornamento del dispositivo StorSimple, assicurarsi che i seguenti prerequisiti siano soddisfatti.

- Assicurarsi che entrambi i controller di dispositivo siano in esecuzione prima di cercare nuovi aggiornamenti. Se uno dei due controller non è in esecuzione, la ricerca ha esito negativo. Per verificare che lo stato dei controller sia integro, passare a **Stato hardware** nella pagina **Manutenzione**. Se vi sono componenti di tipo **Richiesta attenzione**, contattare il supporto tecnico Microsoft prima di continuare.  
- Assicurarsi che gli IP fissi, sia per il controller 0 che per il controller 1, siano instradabili e in grado di effettuare la connessione a Internet in quanto vengono utilizzati per fornire gli aggiornamenti al dispositivo. È possibile utilizzare il [cmdlet di connessione di test](https://technet.microsoft.com/library/hh849808.aspx) per eseguire il ping di un indirizzo noto all'esterno alla rete, ad esempio outlook.com, per verificare che il controller disponga della connettività alla rete esterna.  
- Assicurarsi che le porte 80 e 443 siano disponibili sul dispositivo StorSimple per le comunicazioni in uscita. Per ulteriori informazioni, vedere [Requisiti di rete per il dispositivo StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).  
- Se la versione del software del dispositivo è precedente alla 6.3.9600.17312 (aggiornamento di ottobre 2014), disabilitare le porte DATI 2 e DATI 3, se attivate, prima di avviare l'aggiornamento. Se si lasciano le porte DATI 2 e DATI 3 abilitate quando viene applica l'aggiornamento, il controller del dispositivo potrebbe passare alla modalità di ripristino. Considerare che quando si disattivano le interfacce di rete, tutti i volumi associati verranno disconnessi e gli I/O verranno interrotti per la durata dell'aggiornamento.  

## Novità della versione di ottobre

Questo aggiornamento include i seguenti miglioramenti:

- Per gestire i controller del dispositivo ora è possibile utilizzare l'interfaccia utente del servizio StorSimple Manager. Le azioni di gestione includono il riavvio, l’arresto o l’attivazione di un controller. Per altre informazioni, accedere a [Gestione dei controller del dispositivo StorSimple](storsimple-manage-device-controller.md).  
- È possibile pianificare l'allocazione della larghezza di banda WAN in base a combinazioni quali giorno della settimana e ora del giorno. In questo modo è possibile ottimizzare l'utilizzo della larghezza di banda WAN fuori dagli orari di punta. Per diversi contenitori di volume, sono consentiti diversi modelli di larghezza di banda. Per altre informazioni, accedere a [Gestione dei modelli di larghezza di banda di StorSimple](storsimple-manage-bandwidth-templates.md).  
- È possibile configurare le notifiche di posta elettronica per inviare notifiche in modo proattivo agli amministratori, e non solo, riguardo problemi presenti o futuri. Per altre informazioni, vedere [Configurazione delle impostazioni di avviso](storsimple-manage-alerts.md#configure-alert-settings).  

## Problemi risolti nella versione di ottobre


Nella seguente tabella è disponibile un riepilogo dei problemi risolti in questo aggiornamento.

| No. | Funzionalità | Problema | Si applica a un dispositivo fisico | Si applica a un dispositivo virtuale |
|-----|---------|-------|---------------------------------|--------------------------------|
| 1 | Interfacce di rete | Nella versione precedente, le interfacce di rete DATI 2 DATI 3 sono state scambiate nel software. Tale problema è stato risolto nel presente aggiornamento. Prima di installare l'aggiornamento, rimuovere le impostazioni e disattivare le interfacce di rete. Dopo aver installato l'aggiornamento, sarà necessario configurare nuovamente tali interfacce. | Sì | No |
| 2 | Pacchetto di supporto | Nella versione precedente, se si eseguiva il cmdlet **Export-HcsSupportPackage** di Windows PowerShell per recuperare i registri Baseboard Management Controller (BMC), l'operazione non riuscita e veniva visualizzato il seguente avviso: "L’operazione ha avuto esito positivo su questo controller, ma non sul controller peer a causa dei seguenti errori. Verificare l’integrità del peer e la sua possibilità di connettersi al nodo corrente". Questo problema ora è stato corretto. | Sì | No |
| 3 | Failover del dispositivo | Nella versione precedente era possibile riscontrare delle incoerenze nei dati qualora un processo **Individua backup** aveva esito negativo durante un failover del dispositivo. Questo problema ora è stato corretto. | Sì | No |
| 4 | Failover del dispositivo | Nella versione precedente, dopo un failover del dispositivo, i backup erano visibili, ma il contenitore del volume associato non era presente sul dispositivo di destinazione. Questo problema ora è stato corretto. | Sì | No |
| 5 | Failover del dispositivo | Nella versione precedente era presente un errore nell'enumerazione dei backup sul cloud durante l'operazione di ripristino del Registro di sistema che, in caso di problemi di connettività cloud, poteva causare problemi di incoerenza dei dati. | Sì | No |
| 6 | Aggiornamento del firmware | Nella versione precedente, il processo di aggiornamento del firmware del dispositivo aveva esito negativo e veniva visualizzato un errore in cui si dichiarava che i cmdlet non erano riconoscibili e che, conseguentemente, l'aggiornamento aveva esito negativo. Il controller passava poi in modalità di ripristino. Questo problema ora è stato corretto. | Sì | No |
| 7 | Installazione | Ora sono stati corretti gli errori causati dalla creazione errata dell’immagine del dispositivo durante l'installazione. | Sì | No |
| 8 | Ripristino delle impostazioni predefinite | Se desiderato, ora è possibile ignorare la verifica del firmware per il ripristino delle impostazioni predefinite. Si tratta di una modifica della versione precedente. | Sì | No |
| 9 | Ripristino delle impostazioni predefinite | Nella versione precedente, durante l'esecuzione di un cmdlet di ripristino delle impostazioni predefinite, i controlli della versione del firmware venivano effettuati esclusivamente per alcuni componenti hardware. Al termine del primo riavvio nel processo, che poteva causare un errore del ripristino, sono stati effettuati ulteriori verifiche del firmware. Questa correzione assicura che tutti i controlli del firmware vengano eseguiti durante l’esecuzione del cmdlet di ripristino delle impostazioni predefinite e precedentemente al primo riavvio del sistema. | Sì | No |
| 10 | Rotazione delle chiavi dell’account di archiviazione | Il cmdlet **Invoke-HcsmServiceDataEncryptionKeyChange** utilizzato per ruotare le chiavi dell’account di archiviazione ora richiede all'utente di immettere la chiave di crittografia dei dati del servizio. Si tratta di una modifica rispetto alla versione precedente in cui la chiave di crittografia dei dati del servizio era considerata come un parametro incorporato. | Sì | No |
| 11 | Failback entro 24 ore | Durante il ripristino di emergenza, la pulizia del dispositivo di origine non è stata eseguita correttamente, provocando un esito negativo del failback. Tale problema è stato risolto in questa versione. | Sì | No |

## Problemi noti nella versione di ottobre

Nella tabella seguente viene fornito un riepilogo dei problemi noti in questa versione.

| No. | Funzionalità | Problema | Commenti/Soluzione alternativa | Si applica a un dispositivo fisico | Si applica a un dispositivo virtuale |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Ripristino delle impostazioni predefinite | In alcuni casi, quando si esegue un ripristino delle impostazioni predefinite, il dispositivo StorSimple potrebbe bloccarsi e l’utente potrebbe visualizzare il messaggio: **Ripristino delle impostazioni predefinite in corso (fase 8)**. Ciò si verifica se si preme CTRL + C mentre il cmdlet è in esecuzione. | Non premere CTRL + C dopo l'avvio di un ripristino delle impostazioni predefinite. Se si è già in questo stato, contattare il supporto tecnico Microsoft per i passaggi successivi. | Sì | No |
| 2 | Ripristino delle impostazioni predefinite | Non eseguire un ripristino delle impostazioni predefinite di un dispositivo StorSimple aggiornato alla versione di ottobre 2014. | Questa operazione funziona solo se è installata una patch. Contattare il supporto tecnico Microsoft per ottenere la patch obbligatoria. | Sì | |	
| 3 | Quorum disco | In rari casi, se la maggior parte dei dischi nello chassis EBOD di un dispositivo 8600 sono disconnessi generando un’assenza di quorum disco, il pool di archiviazione sarà offline. Il pool rimarrà in tale stato anche se i dischi vengono riconnessi. | Sarà necessario riavviare il dispositivo. Se il problema persiste, contattare il supporto tecnico Microsoft per i passaggi successivi. | Sì | No |
| 4 | Errori di snapshot nel cloud | In rari casi, uno snapshot cloud potrebbe non riuscire a causa dell’errore **Raggiunto il limite massimo di backup**. Ciò si verifica se si superano i 255 cloni online sullo stesso dispositivo, dallo stesso volume originale eliminato. | | Sì | Sì |
| 5 | ID controller non corretto | Quando viene eseguita la sostituzione di un controller, il controller 0 potrebbe essere visualizzato come controller 1. Durante la sostituzione del controller, quando l'immagine viene caricata dal nodo peer, l'ID del controller può presentarsi inizialmente come ID del controller peer. In rari casi, questo comportamento può verificarsi anche dopo un riavvio del sistema. Non è necessaria alcuna azione da parte dell’utente. Questa situazione si risolverà dopo la sostituzione del controller. | Sì | No |
| 6 | Grafici di monitoraggio del dispositivo | Nel servizio StorSimple Manager, i grafici di monitoraggio del dispositivo non funzionano quando l’autenticazione di base o NTLM è abilitata nella configurazione del server proxy per il dispositivo. | Modificare la configurazione del proxy Web per il dispositivo registrato con il servizio StorSimple Manager in modo che l'autenticazione sia impostata su NESSUNA. A tale scopo, eseguire il cmdlet Set-HcsWebProxy di Windows PowerShell per StorSimple. | Sì | Sì |
| 7 | Account di archiviazione | L’utilizzo del servizio di archiviazione per eliminare l'account di archiviazione non è supportato. Tale operazione causerebbe una situazione in cui non è possibile recuperare i dati dell'utente. | Sì | Sì |
| 8 | Failover del dispositivo | I failover multipli di un contenitore di volumi dallo stesso dispositivo di origine verso dispositivi di destinazione diversi non sono supportati. | Il failover da un singolo dispositivo inattivo a più dispositivi causerà la perdita della proprietà dei dati dei contenitori di volumi sul primo dispositivo sottoposto a failover. Dopo un tale failover, questi contenitori di volumi appariranno o si comporteranno in maniera diversa quando vengono visualizzati nel portale di gestione. | Sì | No |
| 9 | Installazione | Durante l’installazione dell'adattatore StorSimple per SharePoint è necessario fornire un IP del dispositivo affinché l'installazione possa essere completata correttamente. | | Sì | No |
| 10 | Proxy Web | Se nella configurazione del proxy Web è specificato il protocollo HTTPS, la comunicazione tra dispositivo e servizio ne sarà interessata e il dispositivo verrà portato offline. Nel processo, inoltre, verranno generati pacchetti di supporto, consumando risorse significative sul dispositivo. | Assicurarsi che l'URL del proxy Web abbia HTTP come protocollo specificato. Ulteriori informazioni su come [Configurare il proxy Web per il dispositivo](storsimple-configure-web-proxy.md). | Sì | No |
| 11 | Proxy Web | Se si configura e si abilita il proxy Web su un dispositivo registrato, è necessario riavviare il controller attivo sul dispositivo. | | Sì | No |
| 12 | Elevata latenza del cloud ed elevato carico di lavoro I/O | Quando il dispositivo StorSimple rileva una combinazione di latenze cloud molto elevate (nell’ordine di secondi) e carico di lavoro I/O elevato, i volumi del dispositivo entrano in uno stato con funzionalità ridotte e gli I/O potrebbero non riuscire a causa di un errore di "dispositivo non pronto". | In questo caso è necessario riavviare manualmente i controller del dispositivo o eseguire un failover del dispositivo per risolvere il problema. | Sì | No |

## Aggiornamenti del dispositivo fisico nella versione di ottobre

Quando questi aggiornamenti vengono applicati a un dispositivo fisico, la versione del software passa a 6.3.9600.17312. A meno che non sia diversamente specificato, queste note sulla versione si applicano a tutti i modelli del dispositivo StorSimple. Per altre informazioni su tali aggiornamenti, vedere [Aggiornamento software del dispositivo fisico di ottobre 2014 per il dispositivo Microsoft Azure StorSimple](http://support.microsoft.com/kb/2986997).

## Aggiornamenti firmware e controller SAS presenti nella versione di ottobre

Questa versione aggiorna il driver e il firmware per il controller SAS del dispositivo fisico. Per altre informazioni sull'aggiornamento del controller SAS, vedere [Aggiornamento di ottobre 2014 per i controller SAS LSI nel dispositivo Microsoft Azure StorSimple](http://support.microsoft.com/kb/2987020).

Questa versione applica anche un aggiornamento del firmware cumulativo che risolve i problemi di affidabilità con i componenti hardware del dispositivo. Per altre informazioni sull’aggiornamento del firmware, vedere [Aggiornamento firmware di ottobre 2014 per il dispositivo Microsoft Azure StorSimple](http://support.microsoft.com/kb/2987015).

## Aggiornamenti del dispositivo virtuale nella versione di ottobre

Questa versione non contiene aggiornamenti per il dispositivo virtuale. L’applicazione di tale aggiornamento non cambia la versione del software di un dispositivo virtuale.
 

<!---HONumber=August15_HO8-->