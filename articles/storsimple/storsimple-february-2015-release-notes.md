<properties 
    pageTitle="Note sulla versione dell'aggiornamento 0.3 di StorSimple serie 8000 - Febbraio 2015 | Microsoft Azure"
    description="Vengono descritte le nuove funzionalità, i problemi e le soluzioni alternative per la versione di febbraio 2015 di StorSimple."
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

# Note sulla versione dell'aggiornamento 0.3 di StorSimple serie 8000 - Febbraio 2015

## Panoramica

Nelle note sulla versione seguenti sono indicati i problemi critici non risolti relativi all'aggiornamento 0.3 di StorSimple serie 8000 rilasciato a febbraio 2015. Contengono inoltre un elenco degli aggiornamenti software e firmware di StorSimple inclusi in questa versione. Si tratta della terza versione dopo la versione di rilascio di StorSimple serie 8000 resa disponibile a livello generale a luglio 2014.
  
Questo aggiornamento non modifica la versione del software del dispositivo rispetto all'aggiornamento di gennaio, che continua a essere la versione 6.3.9600.17312. È possibile verificare che l'aggiornamento sia stato installato controllando la data dell'**ultimo aggiornamento**. Se la data è il 10 febbraio 2015 o successiva, l'aggiornamento è stato installato correttamente.

Si consiglia di cercare e installare eventuali aggiornamenti disponibili subito dopo l'installazione del dispositivo StorSimple. È inoltre possibile attivare gli aggiornamenti automatici per scaricare e installare gli aggiornamenti ad alta priorità non appena vengono rilasciati da Microsoft. Per ulteriori informazioni, vedere [Aggiornare il dispositivo StorSimple](storsimple-update-device.md).

Esaminare le informazioni contenute nelle note sulla versione prima di distribuire l'aggiornamento della soluzione StorSimple.

>[AZURE.IMPORTANT]
>
> - Utilizzare il servizio StorSimple Manager e non Windows PowerShell per StorSimple per installare l'aggiornamento di febbraio.   
> - L’installazione di questo aggiornamento richiede circa un'ora. Tuttavia, se si installano aggiornamenti cumulativi, il processo può richiedere circa 3 ore.  
> -	La versione di febbraio di StorSimple non contiene aggiornamenti per il dispositivo virtuale StorSimple. Anche se è possibile applicare gli aggiornamenti di Windows disponibili al dispositivo virtuale, incluse le ultime correzioni rapide per la protezione, ma nella versione per il dispositivo virtuale non apparirà alcuna modifica.  

Prima dell'aggiornamento del dispositivo StorSimple assicurarsi che siano soddisfatti i prerequisiti seguenti.

- Assicurarsi che entrambi i controller di dispositivo siano in esecuzione prima di cercare nuovi aggiornamenti. Se uno dei due controller non è in esecuzione, la ricerca ha esito negativo. Per verificare che lo stato dei controller sia integro, passare a **Stato hardware** nella pagina **Manutenzione**. Se vi sono componenti di tipo **Richiesta attenzione**, contattare il supporto tecnico Microsoft prima di continuare.
- Assicurarsi che gli indirizzi IP fissi per il controller 0 e il controller 1 siano instradabili e che possano connettersi a Internet perché vengono utilizzati per fornire gli aggiornamenti per il dispositivo. È possibile utilizzare il [cmdlet Test-Connection](https://technet.microsoft.com/library/hh849808.aspx) per eseguire il ping di un indirizzo noto all'esterno alla rete, ad esempio outlook.com, per verificare che il controller disponga della connettività alla rete esterna.
- Assicurarsi che le porte 80 e 443 siano disponibili sul dispositivo StorSimple per le comunicazioni in uscita. Per ulteriori informazioni, vedere [Requisiti di rete per il dispositivo StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).
- Se la versione del software del dispositivo è precedente alla 6.3.9600.17312 (aggiornamento di ottobre 2014), disabilitare le porte DATI 2 e DATI 3, se attivate, prima di avviare l'aggiornamento. Se si lasciano le porte dati 2 e 3 abilitate quando si applica l'aggiornamento, il controller del dispositivo potrebbe passare alla modalità di ripristino. Considerare che quando si disattivano le interfacce di rete, tutti i volumi associati verranno disconnessi e gli I/O verranno interrotti per la durata dell'aggiornamento.  
  
## Novità nella versione di febbraio

Questo aggiornamento contiene una correzione per il problema del ripristino delle impostazioni predefinite che si è verificato in dispositivi aggiornati dalla versione di disponibilità generale alla versione di ottobre 2014. Per ulteriori informazioni, vedere i [problemi corretti in questa versione](#issues-fixed-in-the-february-release).

Questo aggiornamento non contiene nuove funzionalità.

## Problemi risolti nella versione di febbraio

Nella tabella seguente viene descritto il problema risolto in questo aggiornamento.
 
| No. | Funzionalità | Problema | Si applica a un dispositivo fisico | Si applica a un dispositivo virtuale |
|-----|---------|-------|---------------------------------|-------------------------------|
| 1 | Ripristino delle impostazioni predefinite | Si tenta di eseguire il ripristino delle impostazioni predefinite su un dispositivo su cui originariamente era installata la versione GA (versione 6.3.9600.17215) e che è stato successivamente aggiornato alla versione di ottobre (versione 6.3.9600.17312). Il ripristino delle impostazioni predefinite ha esito negativo e il dispositivo diventa instabile. | Sì | No |


## Problemi noti risolti nella versione di febbraio

Nella tabella seguente viene fornito un riepilogo dei problemi noti in questa versione.
 
| No. | Funzionalità | Problema | Commenti/Soluzione alternativa | Si applica a un dispositivo fisico | Si applica a un dispositivo virtuale |
|-----|---------|-------|----------------------------|-----------------------------|--------------------------|
| 1 | Ripristino delle impostazioni predefinite | In alcuni casi, quando si esegue un ripristino delle impostazioni predefinite, il dispositivo StorSimple potrebbe bloccarsi e l’utente potrebbe visualizzare il messaggio: **Ripristino delle impostazioni predefinite in corso (fase 8)**. Ciò si verifica se si preme CTRL + C mentre il cmdlet è in esecuzione. | Non premere CTRL + C dopo l'avvio di un ripristino delle impostazioni predefinite. Se si è già in questo stato, contattare il supporto tecnico Microsoft per i passaggi successivi. | Sì | No |
| 2 | Quorum disco | In rari casi, se la maggior parte dei dischi nello chassis EBOD di un dispositivo 8600 sono disconnessi generando un’assenza di quorum disco, il pool di archiviazione sarà offline. Il pool rimarrà in tale stato anche se i dischi vengono riconnessi. | Sarà necessario riavviare il dispositivo. Se il problema persiste, contattare il supporto tecnico Microsoft per i passaggi successivi. | Sì | No |
| 3 | Errori di snapshot nel cloud | In rari casi, uno snapshot cloud potrebbe non riuscire a causa dell’errore **Raggiunto il limite massimo di backup**. Ciò si verifica se si superano i 255 cloni online sullo stesso dispositivo, dallo stesso volume originale eliminato. | | Sì | Sì |
| 4 | ID controller non corretto | Quando viene eseguita la sostituzione di un controller, il controller 0 potrebbe essere visualizzato come controller 1. Durante la sostituzione del controller, quando l'immagine viene caricata dal nodo peer, l'ID del controller può presentarsi inizialmente come ID del controller peer. In rari casi, questo comportamento può verificarsi anche dopo un riavvio del sistema. | Non è necessaria alcuna azione da parte dell’utente. Questa situazione si risolverà dopo la sostituzione del controller. | Sì | No |
| 5 | Grafici di monitoraggio del dispositivo | Nel servizio StorSimple Manager, i grafici di monitoraggio del dispositivo non funzionano quando l’autenticazione di base o NTLM è abilitata nella configurazione del server proxy per il dispositivo. | Modificare la configurazione del proxy Web per il dispositivo registrato con il servizio StorSimple Manager in modo che l'autenticazione sia impostata su NESSUNA. A tale scopo, eseguire il cmdlet Set-HcsWebProxy di Windows PowerShell per StorSimple. | Sì | Sì |
| 6 | Account di archiviazione | L’utilizzo del servizio di archiviazione per eliminare l'account di archiviazione non è supportato. Tale operazione causerebbe una situazione in cui non è possibile recuperare i dati dell'utente. | | Sì | Sì |
| 7 | Failover del dispositivo | I failover multipli di un contenitore di volumi dallo stesso dispositivo di origine verso dispositivi di destinazione diversi non sono supportati. Il failover da un singolo dispositivo inattivo a più dispositivi causerà la perdita della proprietà dei dati dei contenitori di volumi sul primo dispositivo sottoposto a failover. Dopo un tale failover, questi contenitori di volumi appariranno o si comporteranno in maniera diversa quando vengono visualizzati nel portale di gestione. | Sì | No |
| 8 | Installazione | Durante l’installazione dell'adattatore StorSimple per SharePoint è necessario fornire un IP del dispositivo affinché l'installazione possa essere completata correttamente. | | Sì | No |
| 9 | Proxy Web | Se nella configurazione del proxy Web è specificato il protocollo HTTPS, la comunicazione tra dispositivo e servizio ne sarà interessata e il dispositivo verrà portato offline. Nel processo, inoltre, verranno generati pacchetti di supporto, consumando risorse significative sul dispositivo. | Assicurarsi che l'URL del proxy Web abbia HTTP come protocollo specificato. Ulteriori informazioni su come [Configurare il proxy Web per il dispositivo](storsimple-configure-web-proxy.md). | Sì | No |
| 10 | Proxy Web | Se si configura e si abilita il proxy Web su un dispositivo registrato, è necessario riavviare il controller attivo sul dispositivo. | | Sì | No |
| 11 | Elevata latenza del cloud ed elevato carico di lavoro I/O | Quando il dispositivo StorSimple rileva una combinazione di latenze cloud molto elevate (nell’ordine di secondi) e carico di lavoro I/O elevato, i volumi del dispositivo entrano in uno stato con funzionalità ridotte e gli I/O potrebbero non riuscire a causa di un errore di "dispositivo non pronto". | In questo caso è necessario riavviare manualmente i controller del dispositivo o eseguire un failover del dispositivo per risolvere il problema. | Sì | No |

## Aggiornamenti del dispositivo fisico nella versione di febbraio

Questo aggiornamento contiene una correzione per il problema del ripristino delle impostazioni predefinite che si è verificato in dispositivi aggiornati dalla versione di disponibilità generale alla versione di ottobre 2014. Non contiene tutti gli aggiornamenti nel dispositivo StorSimple.

## Aggiornamenti firmware e controller SAS (Serial Attached SCSI) presenti nella versione di febbraio

Questa versione non contiene aggiornamenti per il controller SAS (Serial Attached SCSI) o il firmware. L'aggiornamento del driver era nella versione di ottobre 2014.

## Aggiornamenti del dispositivo virtuale nella versione di febbraio

Questa versione non contiene aggiornamenti per il dispositivo virtuale. L’applicazione di tale aggiornamento non cambia la versione del software di un dispositivo virtuale.
 

<!---HONumber=August15_HO8-->