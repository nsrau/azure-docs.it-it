---
title: Panoramica della rete WAN virtuale di Azure | Microsoft Docs
description: Informazioni sulla connettività tra succursali scalabile automatizzata della rete WAN virtuale.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 07/18/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 67dd6ba9b94ed9d58d91fb644ce9ee9e44ae9e45
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159170"
---
# <a name="what-is-azure-virtual-wan-preview"></a>Che cos'è la rete WAN virtuale di Azure? (Anteprima)

La rete WAN virtuale di Azure è un servizio di rete che offre connettività tra succursali ottimizzata e automatizzata tramite Azure. La rete WAN virtuale consente di connettere e configurare i dispositivi delle succursali per la comunicazione con Azure. Questa operazione può essere eseguita manualmente oppure usando i dispositivi di provider preferiti tramite un partner di rete WAN virtuale. L'uso di dispositivi di provider preferiti consente la facilità d'uso, semplifica le operazioni di connettività e agevola la gestione della configurazione. Il dashboard predefinito della rete WAN di Azure offre informazioni dettagliate immediate per la risoluzione dei problemi, che consentono di risparmiare tempo e permette di visualizzare con facilità la connettività da sito a sito su larga scala.

> [!IMPORTANT]
> La rete WAN virtuale di Azure è attualmente disponibile come anteprima pubblica gestita. Per usare la rete WAN virtuale, è necessario [registrarsi per l'anteprima](#enroll).
>
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo offre una panoramica rapida della connettività di rete per i carichi di lavoro di Azure e non di Azure. La rete WAN virtuale offre i vantaggi seguenti:

* **Soluzioni di connettività integrate con una configurazione di tipo hub-spoke:** automatizzare la configurazione da sito a sito e la connettività tra siti locali e un hub di Azure da un'ampia gamma di origini, tra cui soluzioni dei partner per la rete WAN virtuale.
* **Installazione e configurazione automatizzate dello spoke:** connettere reti virtuali e carichi di lavoro all'hub di Azure senza problemi.
* **Risoluzione dei problemi intuitiva:** è possibile visualizzare l'interno flusso all'interno di Azure e usare queste informazioni per intraprendere le azioni necessarie.

![Diagramma della rete WAN virtuale](./media/virtual-wan-about/virtualwan.png)

## <a name="vendor"></a>Collaborazione con un partner di rete WAN virtuale

1. Il controller (VPN/SDWAN) del dispositivo della succursale viene autenticato per esportare le informazioni specifiche del sito in Azure usando un'entità servizio di Azure.
2. Il controller (VPN/SDWAN) del dispositivo della succursale ottiene la configurazione della connettività di Azure e aggiorna il dispositivo locale. Ciò consente di automatizzare il download della configurazione, la modifica e l'aggiornamento del dispositivo VPN locale.
3. Quando il dispositivo ha la configurazione di Azure corretta, viene stabilita una connessione da sito a sito (due tunnel attivi) alla rete WAN di Azure. Azure richiede che il controller (VPN/SDWAN) della succursale supporti IKEv2. BGP è facoltativo.

## <a name="resources"></a>Risorse della rete WAN virtuale

Per configurare una rete WAN virtuale completa, si creano le risorse seguenti:

* **virtualWAN:** la risorsa virtualWAN rappresenta una sovrapposizione virtuale della rete di Azure ed è una raccolta di più risorse. Sono inclusi collegamenti a tutti gli hub virtuali che si desidera avere all'interno della rete WAN virtuale. Le risorse della rete WAN virtuale vengono isolate l'una dall'altra e non possono contenere un hub comune. Gli hub virtuali nella rete WAN virtuale non comunicano tra loro.

* **Sito:** la risorsa sito, nota come vpnsite, rappresenta il dispositivo VPN locale e le relative impostazioni. Se si collabora con un partner di rete WAN virtuale, si avrà a disposizione una soluzione predefinita per esportare automaticamente queste informazioni in Azure.

* **Hub:** un hub virtuale è una rete virtuale gestita da Microsoft. L'hub contiene vari endpoint di servizio per abilitare la connettività dalla rete locale (vpnsite). L'hub è l'elemento centrale della rete in un'area. Può esistere solo un unico hub per ogni area di Azure. Quando si crea un hub usando il portale di Azure, viene creata automaticamente una rete virtuale dell'hub virtuale e un gateway VPN dell'hub virtuale.

  Un gateway dell'hub non equivale a un gateway di rete virtuale, usato per ExpressRoute e Gateway VPN. Quando si usa una rete WAN virtuale, ad esempio, non si crea una connessione da sito a sito dal sito locale direttamente alla rete virtuale. È invece necessario creare una connessione da sito a sito all'hub. Il traffico passa sempre attraverso il gateway dell'hub. Questo significa che le reti virtuali non hanno bisogno di un gateway di rete virtuale proprio. La rete WAN virtuale consente alle reti virtuali di sfruttare i vantaggi di una facile scalabilità tramite l'hub virtuale e il gateway di hub virtuale. 

* **Connessione di rete virtuale hub:** la risorsa connessione di rete virtuale hub viene usata per connettere facilmente l'hub alla rete virtuale. Al momento è possibile connettersi solo alle reti virtuali all'interno della stessa area dell'hub.

##<a name="enroll"></a>Registrarsi per l'anteprima

Prima di poter configurare la rete WAN virtuale, è necessario registrare la sottoscrizione nell'anteprima. In caso contrario, non sarà possibile utilizzare la rete WAN virtuale nel portale. Per eseguire la registrazione, inviare un messaggio di posta elettronica all'indirizzo <azurevirtualwan@microsoft.com> con l'ID della sottoscrizione. Si riceverà un messaggio di posta elettronica dopo la registrazione della sottoscrizione.

## <a name="faq"></a>Domande frequenti

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="feedback"></a>Commenti e suggerimenti per l'anteprima

I commenti e i suggerimenti degli utenti sono molto apprezzati. Inviare un messaggio di posta elettronica all'indirizzo <azurevirtualwan@microsoft.com> per segnalare eventuali problemi o per fornire commenti e suggerimenti (positivi o negativi) sulla rete WAN virtuale. Includere il nome della propria società tra parentesi quadre "[ ]" nella riga dell'oggetto. Se si sta segnalando un problema, includere anche l'ID sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

Per creare una connessione da sito a sito tramite la rete WAN virtuale, è possibile richiedere la collaborazione di un [partner di rete WAN virtuale](https://aka.ms/virtualwan) oppure creare manualmente la connessione. Per creare manualmente la connessione, vedere [Create a Site-to-Site connection using Virtual WAN](virtual-wan-site-to-site-portal.md) (Creare una connessione da sito a sito tramite la rete WAN virtuale).
