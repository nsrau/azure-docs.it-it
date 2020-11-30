---
title: Requisiti della larghezza di banda di Remote Desktop Protocol Windows desktop virtuale-Azure
titleSuffix: Azure
description: Informazioni sui requisiti della larghezza di banda RDP per desktop virtuale di Windows.
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: c9ef72241ac62efd8555de59bb52949321364035
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325180"
---
# <a name="remote-desktop-protocol-rdp-bandwidth-requirements"></a>Requisiti per la larghezza di banda Remote Desktop Protocol (RDP)

Remote Desktop Protocol (RDP) è una tecnologia sofisticata che usa varie tecniche per perfezionare il recapito della grafica remota del server al dispositivo client. A seconda del caso d'uso, della disponibilità delle risorse di elaborazione e della larghezza di banda di rete, RDP regola dinamicamente i vari parametri per offrire la migliore esperienza utente.

Remote Desktop Protocol multiplexing di più canali virtuali dinamici (DVCs) in un singolo canale di dati inviato attraverso trasporti di rete diversi. Sono disponibili DVCs separate per la grafica remota, l'input, il reindirizzamento dei dispositivi, la stampa e altro. I partner WVD possono anche implementare le estensioni che usano le interfacce DVC.
La quantità di dati inviati tramite RDP dipende dall'attività dell'utente. Ad esempio, un utente può utilizzare contenuti testuali di base per la maggior parte della sessione e utilizzare una larghezza di banda minima, ma quindi generare una stampa di un documento di pagina 200 sulla stampante locale. Questo processo di stampa utilizzerà una notevole quantità di larghezza di banda di rete.

Quando si usa una sessione remota, la larghezza di banda disponibile per la rete influisca significativamente sulla qualità dell'esperienza utente. Diverse applicazioni e risoluzioni di visualizzazione richiedono configurazioni di rete diverse, quindi è essenziale assicurarsi che la configurazione di rete soddisfi le proprie esigenze.

## <a name="estimating-bandwidth-utilization"></a>Stima dell'utilizzo della larghezza di banda

RDP usa diversi algoritmi di compressione per diversi tipi di dati. La tabella seguente illustra la stima dei trasferimenti di dati:

| Tipo di dati | Direzione | Come eseguire la stima |
|---|---|---|
| Grafica remota | Da host sessione a client | [Vedere le linee guida dettagliate](#estimating-bandwidth-used-by-remote-graphics) |
| Heartbeat | Entrambe le direzioni | ~ 20 byte ogni 5 secondi  |
| Input | Da client a host sessione | La quantità di dati è basata sull'attività dell'utente, minore di 100 byte per la maggior parte delle operazioni  |
| trasferimenti di file | Entrambe le direzioni | I trasferimenti di file utilizzano la compressione bulk. Usare la compressione zip per l'approssimazione |
| Stampa | Da host sessione a client | Il trasferimento del processo di stampa dipende dal driver e dall'utilizzo della compressione bulk, utilizzare la compressione zip per l'approssimazione |

Altri scenari possono modificare i requisiti della larghezza di banda in base alla modalità d'uso, ad esempio:

* Conferenze vocali o videoconferenze
* Comunicazione in tempo reale
* Streaming video 4K

## <a name="estimating-bandwidth-used-by-remote-graphics"></a>Stima della larghezza di banda utilizzata dalla grafica remota

È difficile prevedere l'uso della larghezza di banda da parte del desktop remoto. Le attività dell'utente generano la maggior parte del traffico di desktop remoto. Ogni utente è univoco e le differenze nei modelli di lavoro possono cambiare in modo significativo l'utilizzo della rete.

Il modo migliore per comprendere i requisiti di larghezza di banda consiste nel monitorare le connessioni utente effettive. Il monitoraggio può essere eseguito dai contatori delle prestazioni incorporati o dalle apparecchiature di rete.

In molti casi, tuttavia, è possibile stimare l'utilizzo della rete comprendendo il modo in cui Remote Desktop Protocol funziona e analizzando i modelli di lavoro degli utenti.

Il protocollo remoto recapita la grafica generata dal server remoto per visualizzarla in un monitoraggio locale. In particolare, fornisce la bitmap del desktop interamente composta nel server.
Mentre l'invio di una bitmap desktop sembra una semplice attività al primo approccio, richiede una quantità significativa di risorse. Ad esempio, l'immagine di un desktop 1080p nel formato non compresso è di circa 8Mb di dimensioni. La visualizzazione di questa immagine nel monitor connesso localmente con una frequenza di aggiornamento dello schermo modesta di 30 Hz richiede una larghezza di banda di circa 237 MB/s.

Per ridurre la quantità di dati trasferiti in rete, RDP usa la combinazione di più tecniche, tra cui

* Ottimizzazioni frequenza frame
* Classificazione del contenuto della schermata
* Codec specifici del contenuto
* Codifica di immagini progressive
* Memorizzazione nella cache sul lato client

Per comprendere meglio la grafica remota, tenere presente quanto segue:

* Più ricca è la grafica, più la larghezza di banda che verrà eseguita
  * Il testo, gli elementi dell'interfaccia utente della finestra e le aree a tinta unita utilizzano una larghezza di banda inferiore rispetto a qualsiasi altra.
  * Le immagini naturali sono i collaboratori più significativi per l'uso della larghezza di banda. Tuttavia, la memorizzazione nella cache sul lato client contribuisce alla riduzione.
* Vengono trasmesse solo le parti modificate dello schermo. Se non sono presenti aggiornamenti visibili sullo schermo, non viene inviato alcun aggiornamento.
* La riproduzione video e altri contenuti con frequenza elevata sono essenzialmente una presentazione di immagini. RDP USA in modo dinamico i codec video appropriati per recapitarli con la frequenza dei fotogrammi quasi originale. Tuttavia, è ancora grafica ed è ancora il contributo più significativo all'utilizzo della larghezza di banda.
* Tempo di inattività in Desktop remoto indica nessun aggiornamento dello schermo o minimo. Pertanto, l'utilizzo della rete è minimo durante i periodi di inattività.
* Quando la finestra client desktop remoto è ridotta a icona, non vengono inviati aggiornamenti grafici dall'host della sessione.

Tieni presente che lo stress sulla rete dipende sia dalla frequenza dei fotogrammi di output del carico di lavoro dell'app che dalla risoluzione dello schermo. Se aumenta la frequenza dei fotogrammi o la risoluzione dello schermo, aumenterà anche la larghezza di banda richiesta. Un carico di lavoro leggero con schermo ad alta risoluzione, ad esempio, richiede una disponibilità della larghezza di banda maggiore rispetto a un carico di lavoro leggero con risoluzione normale o bassa. Diverse risoluzioni dello schermo richiedono una disponibilità di larghezze di banda differenti.

Nella tabella seguente vengono descritte le stime dei dati utilizzati dai diversi scenari grafici. Questi numeri si applicano a una singola configurazione di monitoraggio con risoluzione 1920x1080 e con modalità grafica predefinita e in modalità grafica H. 264/AVC 444.

| Scenario | Modalità predefinita | Modalità H. 264/AVC 444 | Anteprima | Descrizione dello scenario |
|:---|---:|---:|---|---|
| Idle | 0,3 kbps | 0,3 kbps |:::image type="content" source="media/idle.png" alt-text="Screenshot della connessione inattiva":::| L'utente sta sospendendo il lavoro e non è disponibile alcun aggiornamento dello schermo attivo |
| Microsoft Word | 100-150 kbps | 200-300 kbps |:::image type="content" source="media/word.gif" alt-text="Animazione di Microsoft Word":::| L'utente collabora attivamente con Microsoft Word, digitando, incollando grafica e passando da un documento all'altra |
| Microsoft Excel | 150-200 Kbps | 400-500 Kbps |:::image type="content" source="media/excel.gif" alt-text="Animazione di Microsoft Excel":::| L'utente lavora attivamente con Microsoft Excel, più celle con formule e grafici vengono aggiornate simultaneamente |
| Microsoft PowerPoint | 4-4.5 Mbps | 1.6-1.8 Mbps |:::image type="content" source="media/powerpoint.gif" alt-text="Animazione di Microsoft PowerPoint":::| L'utente lavora attivamente con Microsoft PowerPoint, digitando, incollando. Modifica anche grafica avanzata e uso degli effetti della transizione diapositiva |
| Esplorazione Web | 6-6,5 Mbps | 0.9-1 Mbps |:::image type="content" source="media/web.gif" alt-text="Animazione di esplorazione Web":::| L'utente sta lavorando attivamente con un sito web ricco di grafica che contiene più immagini statiche e animate. L'utente scorre le pagine orizzontalmente e verticalmente |
| Raccolta immagini | 3.3-3.6 Mbps | 0.7-0.8 Mbps |:::image type="content" source="media/image-gallery.gif" alt-text="Animazione della raccolta immagini":::| L'utente sta lavorando attivamente con l'applicazione raccolta immagini. esplorazione, zoom, ridimensionamento e rotazione delle immagini |
| Riproduzione di video | 8.5-9,5 Mbps | 2,5-2,8 Mbps |:::image type="content" source="media/video.gif" alt-text="Animazione della riproduzione video":::| L'utente sta guardando un video di 30 FPS che utilizza 1/2 dello schermo |
| Riproduzione video a schermo intero | 7.5-8,5 Mbps | 2,5-3,1 Mbps |:::image type="content" source="media/fullscreen-video.gif" alt-text="Animazione della riproduzione video a schermo intero":::| L'utente sta guardando un video di 30 FPS ingrandito a schermo intero |

## <a name="dynamic-bandwidth-allocation"></a>Allocazione della larghezza di banda dinamica

Remote Desktop Protocol è un protocollo moderno progettato per adattarsi in modo dinamico alle mutevoli condizioni della rete.
Invece di usare i limiti rigidi sull'utilizzo della larghezza di banda, RDP usa il rilevamento continuo della rete che monitora attivamente la larghezza di banda di rete disponibile e il tempo di round trip del pacchetto. In base ai risultati, RDP seleziona dinamicamente le opzioni di codifica grafica e alloca la larghezza di banda per il reindirizzamento dei dispositivi e altri canali virtuali.  
Questa tecnologia consente a RDP di usare la pipe di rete completa, se disponibile, e di eseguire rapidamente il failback quando la rete è necessaria per un altro.
RDP rileva che e regola la qualità dell'immagine, la frequenza dei fotogrammi o gli algoritmi di compressione se altre applicazioni richiedono la rete.

## <a name="limit-network-bandwidth-use-with-throttle-rate"></a>Limitare l'uso della larghezza di banda di rete con frequenza di limitazione

Nella maggior parte degli scenari non è necessario limitare l'utilizzo della larghezza di banda poiché la limitazione può influire sull'esperienza utente. Tuttavia, nelle reti vincolate può essere utile limitare l'utilizzo della rete. Un altro esempio è costituito da reti con lease a cui viene addebitata la quantità di traffico utilizzata.

In questi casi, è possibile limitare il traffico di rete in uscita RDP specificando una velocità di limitazione nei criteri QoS.

  >[!NOTE]
  > [Verificare che il SHORTPATH RDP sia abilitato](./shortpath.md) : la limitazione della frequenza di limitazione non è supportata per il trasporto con connessione inversa.

### <a name="implement-throttle-rate-limiting-on-session-host-using-group-policy"></a>Implementare la limitazione della velocità di limitazione per l'host sessione usando Criteri di gruppo

È possibile utilizzare la qualità del servizio (QoS) basata su criteri in Criteri di gruppo per impostare la frequenza di limitazione predefinita.

Per creare un criterio QoS per gli host di sessione aggiunti a un dominio, prima di tutto accedere a un computer in cui è stata installata la gestione Criteri di gruppo. Aprire Gestione Criteri di gruppo (fare clic su Start, scegliere Strumenti di amministrazione, quindi selezionare Gestione Criteri di gruppo), quindi completare i passaggi seguenti:

1. In Gestione Criteri di gruppo individuare il contenitore in cui deve essere creato il nuovo criterio. Se, ad esempio, tutti i computer ospitano una sessione in un'unità organizzativa denominata **host sessione**, i nuovi criteri devono essere creati nell'unità organizzativa host sessione.

2. Fare clic con il pulsante destro del mouse sul contenitore appropriato, quindi selezionare **Crea un oggetto Criteri di gruppo in questo dominio e collegarlo qui**.

3. Nella finestra di dialogo **nuovo oggetto Criteri** di gruppo digitare un nome per il nuovo oggetto Criteri di gruppo nella casella **nome** e quindi fare clic su **OK**.

4. Fare clic con il pulsante destro del mouse sul criterio appena creato, quindi scegliere **modifica**.

5. Nel Editor Gestione Criteri di gruppo, espandere **Configurazione computer**, espandere **impostazioni di Windows**, fare clic con il pulsante destro del mouse su **QoS basata su criteri** e quindi scegliere **Crea nuovo criterio**.

6. Nella finestra di dialogo **QoS basata su criteri** , nella pagina di apertura, digitare un nome per il nuovo criterio nella casella **nome** . Selezionare **specificare la velocità di limitazione in uscita** e impostare il valore richiesto, quindi fare clic su **Avanti**.

7. Nella pagina successiva selezionare solo le **applicazioni con questo nome eseguibile** , immettere il nome **svchost.exe** e quindi fare clic su **Avanti**. Questa impostazione indica ai criteri di assegnare la priorità solo al traffico corrispondente dal servizio Desktop remoto.

8. Nella terza pagina, assicurarsi che siano selezionati sia l' **indirizzo IP di origine** che **qualsiasi indirizzo IP di destinazione** . Selezionare **Avanti**. Queste due impostazioni assicurano che i pacchetti vengano gestiti indipendentemente dal computer (indirizzo IP) che ha inviato i pacchetti e dal computer (indirizzo IP) che riceverà i pacchetti.

9. Nella pagina quattro selezionare **UDP** dall'elenco **a discesa selezionare il protocollo di cui si applica questo criterio QoS** .

10. Sotto l'intestazione **specificare il numero di porta di origine**, selezionare **da questa porta o intervallo di origine**. Nella casella di testo associata digitare **3390**. Selezionare **Fine**.

I nuovi criteri creati avranno effetto solo dopo l'aggiornamento Criteri di gruppo nei computer host della sessione. Sebbene Criteri di gruppo venga aggiornato periodicamente, è possibile forzare un aggiornamento immediato attenendosi alla procedura seguente:

1. In ogni host della sessione per cui si desidera aggiornare Criteri di gruppo aprire un prompt dei comandi come amministratore (*Esegui come amministratore*).

2. Al prompt dei comandi immettere

   ```console
   gpupdate /force
   ```

### <a name="implement-throttle-rate-limiting-on-session-host-using-powershell"></a>Implementare la limitazione della velocità di limitazione per l'host sessione con PowerShell

È possibile impostare la velocità di limitazione per il shortpath RDP usando il cmdlet di PowerShell seguente:

```powershell
New-NetQosPolicy -Name "RDP Shortpath" -AppPathNameMatchCondition "svchost.exe" -IPProtocolMatchCondition UDP -IPSrcPortStartMatchCondition 3390 -IPSrcPortEndMatchCondition 3390 -DSCPAction 46 -NetworkProfile All
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sui requisiti di larghezza di banda per desktop virtuali Windows, vedere [informazioni sui requisiti della larghezza di banda Remote Desktop Protocol (RDP) per desktop virtuale di Windows](rdp-bandwidth.md).
* Per ulteriori informazioni sulla connettività di rete per desktop virtuali Windows, vedere [informazioni sulla connettività di rete per desktop virtuali Windows](network-connectivity.md).
* Per iniziare a usare la qualità del servizio (QoS) per desktop virtuale di Windows, vedere [implementare la qualità del servizio (QoS) per desktop virtuale di Windows](rdp-quality-of-service-qos.md).
