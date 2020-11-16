---
title: Implementare la qualità del servizio (QoS) per desktop virtuale Windows (anteprima)
titleSuffix: Azure
description: Come configurare QoS (anteprima) per desktop virtuale di Windows.
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: b61faf74d96e2571e91f7bf9d10eac88cdbf8345
ms.sourcegitcommit: 18046170f21fa1e569a3be75267e791ca9eb67d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2020
ms.locfileid: "94639378"
---
# <a name="implement-quality-of-service-qos-for-windows-virtual-desktop-preview"></a>Implementare la qualità del servizio (QoS) per desktop virtuale Windows (anteprima)

> [!IMPORTANT]
> Il supporto dei criteri di qualità del servizio (QoS) per desktop virtuale di Windows è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliabile usarla per carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il [ShortPath RDP](./shortpath.md) fornisce un trasporto diretto basato su UDP tra desktop remoto client e l'host sessione. Il shortpath RDP consente la configurazione dei criteri di qualità del servizio (QoS) per i dati RDP.
QoS nel desktop virtuale Windows consente il traffico RDP in tempo reale sensibile ai ritardi di rete per "tagliare la linea" davanti al traffico meno sensibile. Un esempio di traffico meno sensibile potrebbe essere il download di una nuova app, in cui un altro secondo da scaricare non è una grande quantità di dati. QoS utilizza gli oggetti di Windows Criteri di gruppo per identificare e contrassegnare tutti i pacchetti in flussi in tempo reale e consentire alla rete di fornire al traffico RDP una parte dedicata della larghezza di banda.

Se si supporta un gruppo di utenti di grandi dimensioni in cui si verificano i problemi descritti in questo articolo, è probabile che sia necessario implementare QoS. Una piccola impresa con pochi utenti potrebbe non avere bisogno di QoS, ma dovrebbe essere utile anche in questo caso.

Senza alcuna forma di QoS, è possibile che vengano visualizzati i seguenti problemi:

* Jitter: pacchetti RDP che arrivano a frequenze diverse, che possono causare problemi visivi e audio
* Perdita di pacchetti: pacchetti eliminati, che determinano una ritrasmissione che richiede ulteriore tempo
* Tempo di round trip ritardato (RTT): i pacchetti RDP impiegano molto tempo per raggiungere le proprie destinazioni, causando ritardi evidenti tra l'input e la reazione dell'applicazione remota.

Il modo meno complesso per risolvere questi problemi consiste nell'aumentare le dimensioni delle connessioni dati, sia internamente che a Internet. Poiché spesso il costo è proibitivo, QoS fornisce un modo per gestire le risorse che si hanno anziché aggiungere larghezza di banda in modo più efficace. Per risolvere i problemi di qualità, è consigliabile usare prima QoS, quindi aggiungere larghezza di banda solo se necessario.

Per rendere effettiva la funzionalità QoS, è necessario applicare impostazioni QoS coerenti nell'intera organizzazione. Qualsiasi parte del percorso che non supporta le priorità QoS può peggiorare la sessione RDP di qualità.

## <a name="introduction-to-qos-queues"></a>Introduzione alle code QoS

Per fornire QoS, i dispositivi di rete devono disporre di un modo per classificare il traffico e devono essere in grado di distinguere RDP da altro traffico di rete.

Quando il traffico di rete entra in un router, il traffico viene inserito in una coda. Se un criterio QoS non è configurato, esiste una sola coda e tutti i dati vengono considerati come First-in, First-out con la stessa priorità. Ciò significa che il traffico RDP potrebbe rimanere bloccato dietro il traffico, in cui un ritardo di pochi millisecondi non costituisce un problema.

Quando si implementa QoS, è possibile definire più code usando una delle diverse funzionalità di gestione della congestione, ad esempio l'accodamento prioritario di Cisco e le funzionalità di prevenzione [ponderata (CBWFQ) basate su classi](https://www.cisco.com/en/US/docs/ios/12_0t/12_0t5/feature/guide/cbwfq.html#wp17641) , ad esempio la [ponderazione del rilevamento casuale (WRED)](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/qos_conavd/configuration/15-mt/qos-conavd-15-mt-book/qos-conavd-cfg-wred.html).

Una semplice analogia è che QoS crea "corsie di carpooling" virtuali nella rete dati. Quindi, alcuni tipi di dati non rilevano mai o raramente un ritardo. Una volta create tali corsie, è possibile modificarne le dimensioni relative e gestire la larghezza di banda di connessione in modo più efficace, offrendo al tempo stesso un'esperienza di livello aziendale per gli utenti dell'organizzazione.

## <a name="qos-implementation-checklist"></a>Elenco di controllo dell'implementazione QoS

A livello generale, eseguire le operazioni seguenti per implementare QoS:

1. [Assicurarsi che la rete sia pronta](#make-sure-your-network-is-ready)
2. Assicurarsi [che la SHORTPATH RDP sia abilitata](./shortpath.md) : i criteri QoS non sono supportati per il trasporto con connessione inversa
3. [Implementare l'inserimento di marcatori DSCP](#insert-dscp-markers) negli host di sessione

Quando si prepara l'implementazione di QoS, tenere presenti le linee guida seguenti:

* Il percorso più breve per l'host di sessione è migliore
* Gli eventuali ostacoli tra, ad esempio i proxy o i dispositivi di ispezione dei pacchetti, non sono consigliati

## <a name="make-sure-your-network-is-ready"></a>Assicurarsi che la rete sia pronta

Se si sta considerando un'implementazione QoS, è necessario avere già stabilito i requisiti di larghezza di banda e altri [requisiti di rete](/windows-server/remote/remote-desktop-services/network-guidance?context=/azure/virtual-desktop/context/context).
  
La congestione del traffico in rete influirà significativamente sulla qualità dei supporti. Una mancanza di larghezza di banda comporta una riduzione delle prestazioni e un'esperienza utente insufficiente. Man mano che l'adozione e l'utilizzo di desktop virtuali Windows aumentano, utilizzare [log Analytics](./diagnostics-log-analytics.md) per identificare i problemi e quindi apportare modifiche utilizzando QoS e aggiunte della larghezza di banda selettiva.

### <a name="vpn-considerations"></a>Considerazioni sulla VPN

QoS funziona come previsto solo se implementato in tutti i collegamenti tra client e host sessione. Se si usa QoS in una rete interna e un utente accede da una posizione remota, è possibile assegnare una priorità solo all'interno della rete gestita interna. Sebbene le posizioni remote possano ricevere una connessione gestita implementando una rete privata virtuale (VPN), una VPN aggiunge intrinsecamente un sovraccarico del pacchetto e crea ritardi nel traffico in tempo reale.

In un'organizzazione globale con collegamenti gestiti che si estendono su più continenti, è consigliabile usare QoS perché la larghezza di banda per tali collegamenti è limitata rispetto alla LAN.

## <a name="insert-dscp-markers"></a>Inserisci marcatori DSCP

È possibile implementare QoS usando un oggetto Criteri di gruppo (GPO) per indirizzare gli host della sessione per inserire un marcatore DSCP nelle intestazioni dei pacchetti IP che lo identificano come un particolare tipo di traffico. I router e gli altri dispositivi di rete possono essere configurati in modo da riconoscere questi contrassegni e inserire il traffico in una coda separata con priorità più alta.

È possibile confrontare i contrassegni DSCP con timbri post che indicano ai lavoratori postali l'urgenza del recapito e il modo migliore per ordinarlo per una distribuzione rapida. Una volta configurata la rete in modo da assegnare la priorità ai flussi RDP, i pacchetti persi e i pacchetti in ritardo dovrebbero ridursi significativamente.

Quando tutti i dispositivi di rete usano le stesse classificazioni, contrassegni e priorità, è possibile ridurre o eliminare i ritardi, i pacchetti eliminati e il jitter. Dal punto di vista RDP, il passaggio di configurazione essenziale è la classificazione e il contrassegno dei pacchetti. Tuttavia, per il corretto funzionamento di QoS end-to-end, è necessario anche allineare con attenzione la configurazione RDP con la configurazione di rete sottostante.
Il valore DSCP indica a una rete configurata in modo corrispondente quale priorità assegnare a un pacchetto o a un flusso.

Si consiglia di usare il valore DSCP 46 che esegue il mapping alla classe DSCP di **invio rapido (EF)** .

### <a name="implement-qos-on-session-host-using-group-policy"></a>Implementare QoS nell'host sessione usando Criteri di gruppo

È possibile utilizzare la qualità del servizio (QoS) basata su criteri in Criteri di gruppo per impostare il valore DSCP predefinito.

Per creare un criterio QoS per gli host di sessione aggiunti a un dominio, prima di tutto accedere a un computer in cui è stata installata la gestione Criteri di gruppo. Aprire Gestione Criteri di gruppo (fare clic su Start, scegliere Strumenti di amministrazione, quindi selezionare Gestione Criteri di gruppo), quindi completare i passaggi seguenti:

1. In Gestione Criteri di gruppo individuare il contenitore in cui deve essere creato il nuovo criterio. Se, ad esempio, tutti i computer ospitano una sessione in un'unità organizzativa denominata **"host sessione"** , i nuovi criteri devono essere creati nell'unità organizzativa host sessione.

2. Fare clic con il pulsante destro del mouse sul contenitore appropriato, quindi selezionare **Crea un oggetto Criteri di gruppo in questo dominio e collegarlo qui**.

3. Nella finestra di dialogo **nuovo oggetto Criteri** di gruppo digitare un nome per il nuovo oggetto Criteri di gruppo nella casella **nome** e quindi fare clic su **OK**.

4. Fare clic con il pulsante destro del mouse sul criterio appena creato, quindi scegliere **modifica**.

5. Nel Editor Gestione Criteri di gruppo, espandere **Configurazione computer** , espandere **impostazioni di Windows** , fare clic con il pulsante destro del mouse su **QoS basata su criteri** e quindi scegliere **Crea nuovo criterio**.

6. Nella finestra di dialogo **QoS basata su criteri** , nella pagina di apertura, digitare un nome per il nuovo criterio nella casella **nome** . Selezionare **specifica il valore DSCP** e impostare il valore su **46**. Lasciare deselezionata la tariffa per la **limitazione delle richieste in uscita** e quindi fare clic su **Avanti**.

7. Nella pagina successiva selezionare solo le **applicazioni con questo nome eseguibile** , immettere il nome **svchost.exe** e quindi fare clic su **Avanti**. Questa impostazione indica ai criteri di assegnare la priorità solo al traffico corrispondente dal servizio Desktop remoto.

8. Nella terza pagina verificare che siano selezionati sia l' **indirizzo IP di origine** che **qualsiasi indirizzo IP di destinazione** , quindi fare clic su **Avanti**. Queste due impostazioni assicurano che i pacchetti vengano gestiti indipendentemente dal computer (indirizzo IP) che ha inviato i pacchetti e dal computer (indirizzo IP) che riceverà i pacchetti.

9. Nella pagina quattro selezionare **UDP** dall'elenco **a discesa selezionare il protocollo di cui si applica questo criterio QoS** .

10. Sotto l'intestazione **specificare il numero di porta di origine** , selezionare **da questa porta o intervallo di origine**. Nella casella di testo associata digitare **3390**. Selezionare **Fine**.

I nuovi criteri creati avranno effetto solo dopo l'aggiornamento Criteri di gruppo nei computer host della sessione. Sebbene Criteri di gruppo venga aggiornato periodicamente, è possibile forzare un aggiornamento immediato attenendosi alla procedura seguente:

1. In ogni host della sessione per cui si desidera aggiornare Criteri di gruppo aprire un prompt dei comandi come amministratore ( *Esegui come amministratore* ).

1. Al prompt dei comandi immettere

   ```console
   gpupdate /force
   ```

### <a name="implement-qos-on-session-host-using-powershell"></a>Implementare QoS nell'host sessione usando PowerShell

È possibile impostare QoS per RDP ShortPath usando il cmdlet di PowerShell seguente:

```powershell
New-NetQosPolicy -Name "RDP Shortpath" -AppPathNameMatchCondition "svchost.exe" -IPProtocolMatchCondition UDP -IPSrcPortStartMatchCondition 3390 -IPSrcPortEndMatchCondition 3390 -DSCPAction 46 -NetworkProfile All
```

## <a name="related-articles"></a>Articoli correlati

* [Criteri di qualità del servizio (QoS)](/windows-server/networking/technologies/qos/qos-policy-top)

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sui requisiti di larghezza di banda per desktop virtuali Windows, vedere [informazioni sui requisiti della larghezza di banda Remote Desktop Protocol (RDP) per desktop virtuale di Windows](rdp-bandwidth.md).
* Per ulteriori informazioni sulla connettività di rete per desktop virtuali Windows, vedere [informazioni sulla connettività di rete per desktop virtuali Windows](network-connectivity.md).
