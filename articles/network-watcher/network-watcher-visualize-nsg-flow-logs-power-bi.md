---
title: Visualizzare i log dei flussi dei gruppi di sicurezza di rete di Azure con Power BI | Microsoft Docs
description: Questo articolo illustra come visualizzare i log dei flussi dei gruppi di sicurezza di rete con Power BI.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 1e4f95fa-f5f0-4e03-bc25-008fbfc4934c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 7cac733717b4af048e58cf3d574c5743889100c1
ms.contentlocale: it-it
ms.lasthandoff: 03/22/2017

---

# <a name="visualizing-network-security-group-flow-logs-with-power-bi"></a>Visualizzare i log dei flussi dei gruppi di sicurezza di rete con Power BI

I log dei flussi dei gruppi di sicurezza di rete permettono di visualizzare le informazioni sul traffico IP in ingresso e in uscita nei gruppi di sicurezza di rete. Questi log mostrano i flussi in ingresso e in uscita in base a regole, la scheda di rete a cui si applica il flusso, informazioni a 5 tuple sul flusso, ad esempio l'indirizzo IP di origine/destinazione, la porta di origine/destinazione o il protocollo, e se il traffico è stato consentito o negato.

Cercando manualmente nei file di log può essere difficile ottenere informazioni dettagliate sui dati di log dei flussi. Questo articolo offre una soluzione per visualizzare i log dei flussi più recenti e ottenere informazioni sul traffico di rete.

## <a name="scenario"></a>Scenario

Nello scenario seguente Power BI Desktop viene connesso all'account di archiviazione configurato come sink per i dati di registrazione dei flussi dei gruppi di sicurezza di rete. Dopo la connessione all'account di archiviazione, Power BI scarica e analizza i log per fornire una rappresentazione visiva del traffico registrato dai gruppi di sicurezza di rete.

Usando gli oggetti visivi inclusi nel modello è possibile esaminare quanto segue:

* Talker principali
* Dati di flusso della serie temporale in base alla direzione e alla regola decisa
* Flussi in base all'indirizzo MAC dell'interfaccia di rete
* Flussi in base al gruppo di sicurezza di rete e alla regola
* Flussi in base alla porta di destinazione

Il modello incluso è modificabile. È quindi possibile aggiungervi nuovi dati e oggetti visivi o modificare le query in base alle esigenze.

## <a name="setup"></a>Configurazione

Prima di iniziare, è necessario abilitare la registrazione dei flussi dei gruppi di sicurezza di rete in uno o più gruppi di sicurezza di rete nell'account usato. Per istruzioni in proposito, vedere [Introduzione alla registrazione dei flussi per i gruppi di sicurezza di rete](network-watcher-nsg-flow-logging-overview.md).

È necessario che il client di Power BI Desktop sia installato nel computer e che lo spazio disponibile nel computer sia sufficiente per scaricare e caricare i dati di log presenti nell'account di archiviazione.

![Diagramma di Visio][1]

### <a name="steps"></a>Passi

1. Scaricare e aprire il [modello di log dei flussi Power BI di Network Watcher](https://aka.ms/networkwatcherpowerbiflowlogstemplate) nell'applicazione Power BI Desktop.
1. Immettere i parametri di query obbligatori.
    1. **StorageAccountName**: specifica il nome dell'account di archiviazione contenente i log dei flussi dei gruppi di sicurezza di rete da caricare e visualizzare.
    1. **NumberOfLogFiles**: specifica il numero di file di log da scaricare e visualizzare in Power BI. Ad esempio, se si specifica 50, vengono scaricati e visualizzati i 50 file di log più recenti. Se vengono abilitati e configurati due gruppi di sicurezza di rete per l'invio di log dei flussi dei gruppi di sicurezza di rete a questo account, è possibile visualizzare i log corrispondenti alle ultime 25 ore.

    ![schermata principale di Power BI][2]

1. Immettere la chiave di accesso per l'account di archiviazione. Per trovare le chiavi di accesso valide, accedere all'account di archiviazione nel portale di Azure e selezionare **Chiavi di accesso** dal menu Impostazioni. Fare clic su **Connetti** e quindi applicare le modifiche.

    ![chiavi di accesso][3]

    ![chiave di accesso 2][4]

4.  I log vengono scaricati e analizzati ed è quindi possibile usare gli oggetti visivi creati in precedenza.

## <a name="understanding-the-visuals"></a>Informazioni sugli oggetti visivi

Il modello include un set di oggetti visivi che aiutano a comprendere i dati dei registri dei flussi dei gruppi di sicurezza di rete. Le immagini seguenti mostrano un esempio dell'aspetto del dashboard popolato con i dati. Di seguito vengono esaminati in dettaglio i singoli oggetti visivi 

![Power BI][5]
 
L'oggetto visivo Top Talkers (Talker principali) mostra gli indirizzi IP che hanno avviato il maggior numero di connessioni nel periodo specificato. La dimensione delle caselle corrisponde al relativo numero di connessioni. 

![Top Talkers][6]

I grafici di serie temporali riportati di seguito mostrano il numero dei flussi nel periodo specificato. Il grafico superiore è segmentato in base alla direzione del flusso, mentre quello inferiore è segmentato in base alla decisione presa, ovvero consenso o negazione. Con questo oggetto visivo è possibile esaminare le tendenze relative al traffico nel tempo e identificare eventuali cali o picchi anomali nel traffico o nella sua segmentazione.

![flussi nel periodo][7]

I grafici seguenti mostrano i flussi per ogni interfaccia di rete. Il grafico superiore è segmentato in base alla direzione dei flussi, mentre quello inferiore è segmentato in base alla decisione presa. Questi dati permettono di ottenere informazioni dettagliate sulla macchina virtuale che ha comunicato di più rispetto alle altre e sul traffico consentito o negato verso una determinata macchina virtuale.

![flussi per scheda di rete][8]

Il grafico ad anello riportato di seguito mostra una suddivisione dei flussi in base alla porta di destinazione. Con queste informazioni è possibile visualizzare le porte di destinazione più usate nel periodo specificato.

![grafico ad anello][9]

Il grafico a barre riportato di seguito mostra i flussi in base ai gruppi di sicurezza di rete e alle regole. Con queste informazioni è possibile visualizzare i gruppi di sicurezza di rete responsabili della maggior parte del traffico e la suddivisione del traffico in un gruppo di sicurezza di rete in base alle regole.

![grafico a barre][10]
 
I grafici informativi riportati di seguito mostrano i gruppi di sicurezza di rete presenti nei log, il numero di flussi acquisiti nel periodo specificato e la data del log acquisito più di recente. Queste informazioni permettono di capire quali gruppi di sicurezza di rete vengono registrati e l'intervallo di date dei flussi.

![grafico informativo 1][11]

![grafico informativo 2][12]

Questo modello include i filtri dei dati indicati di seguito, che permettono di visualizzare solo i dati rilevanti. È possibile applicare filtri ai gruppi di risorse, ai gruppi di sicurezza di rete e alle regole. È anche possibile applicare filtri alle informazioni a 5 tuple, alle decisioni e all'orario di scrittura del log.

![filtri dei dati][13]

## <a name="conclusion"></a>Conclusione

Questo scenario ha permesso di dimostrare come l'uso dei registri dei flussi dei gruppi di sicurezza di rete inclusi in Network Watcher e Power BI permetta di visualizzare e comprendere il traffico. Usando il modello incluso, Power BI scarica i log direttamente dall'archivio e li elabora in locale. Il tempo necessario a caricare il modello varia a seconda del numero di file richiesti e della dimensione totale dei file scaricati.

È possibile personalizzare il modello in base alle esigenze. Power BI e i log dei flussi dei gruppi di sicurezza di rete possono essere usati in molti modi diversi. 

## <a name="notes"></a>Note

* Per impostazione predefinita, i log vengono archiviati in `https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/`

    * Se esistono altri dati in un'altra directory, è necessario modificare le query per il pull e l'elaborazione dei dati.

* Non è consigliabile usare il modello incluso con più di 1 GB di log.

* In presenza di una grande quantità di log, è consigliabile prendere in considerazione una soluzione con un altro archivio dati, come Data Lake o SQL Server.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come visualizzare i log dei flussi dei gruppi di sicurezza di rete con Elastick Stack, vedere [Visualize network traffic patterns to and from your VMs using open source tools](network-watcher-using-open-source-tools.md) (Visualizzare i modelli di traffico di rete da e verso le macchine virtuali con strumenti open source)

[1]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure7.png
[8]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure8.png
[9]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure9.png
[10]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure10.png
[11]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure11.png
[12]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure12.png
[13]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure13.png

