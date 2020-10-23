---
title: Domande frequenti
description: Domande frequenti sul servizio Ancoraggi nello spazio di Azure
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 05/18/2020
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: eb02de25cfcb0dde01699c1169d03f0d822c4ae2
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92097406"
---
# <a name="frequently-asked-questions-about-azure-spatial-anchors"></a>Domande frequenti su Ancoraggi nello spazio di Azure

Ancoraggi nello spazio di Azure è un servizio cloud gestito e una piattaforma per sviluppatori che consente esperienze di realtà mista multiutente con riconoscimento dello spazio in dispositivi HoloLens, iOS e Android.

Per altre informazioni, vedere [Panoramica di Ancoraggi nello spazio di Azure](overview.md).

## <a name="azure-spatial-anchors-product-faqs"></a>Domande frequenti su Ancoraggi nello spazio di Azure

**D: Quali dispositivi sono supportati da Ancoraggi nello spazio di Azure?**

**R:** Ancoraggi nello spazio di Azure consente agli sviluppatori di creare app per HoloLens, per dispositivi iOS con supporto ARKit e per dispositivi Android con supporto ARCore; per iOS e Android sono inclusi sia telefoni che tablet.

**D: È necessario essere connessi al cloud per usare Ancoraggi nello spazio di Azure?**

**R:** Ancoraggi nello spazio di Azure attualmente richiede una connessione di rete a Internet. Saremo lieti di ricevere i commenti degli utenti sul [sito dei commenti e suggerimenti Microsoft](https://feedback.azure.com/forums/919252-azure-spatial-anchors).

**D: Quali sono i requisiti di connettività per Ancoraggi nello spazio di Azure?**

**R:** Ancoraggi nello spazio di Azure funziona con connessioni Wi-Fi e mobili a banda larga.

**D: In che modo Ancoraggi nello spazio di Azure può individuare con precisione gli ancoraggi?**

**R:** Molti fattori influiscono sulla precisione di individuazione degli ancoraggi: le condizioni di illuminazione, gli oggetti nell'ambiente e persino la superficie su cui è posizionato l'ancoraggio. Per determinare se la precisione può soddisfare le proprie esigenze, provare gli ancoraggi in ambienti rappresentativi di dove si intende usarli. In caso di ambienti in cui la precisione non soddisfa le proprie esigenze, vedere [Registrazione e diagnostica in Ancoraggi nello spazio di Azure](./concepts/logging-diagnostics.md).

**D: Quanto tempo occorre per creare e individuare gli ancoraggi?**

**R:** Il tempo necessario per creare e individuare gli ancoraggi dipende da molti fattori: la connessione di rete, l'elaborazione e il carico del dispositivo e l'ambiente specifico. Abbiamo clienti che creano applicazioni in molti settori industriali, tra cui produzione, vendita al dettaglio e giochi, a indicare che il servizio consente un'esperienza utente straordinaria per i loro scenari.

## <a name="privacy-faq"></a>Domande frequenti sulla privacy

**D: Quando l'applicazione inserisce un ancoraggio nello spazio in una posizione, possono accedervi tutte le app?**

**R:** Gli ancoraggi sono isolati dall'account Azure. Solo le app a cui si concede l'accesso al proprio account potranno accedere agli ancoraggi all'interno dell'account.

**D: Come vengono archiviati i dati da Ancoraggi nello spazio di Azure?**

**R:** Tutti i dati vengono archiviati crittografati con una chiave di crittografia dei dati gestita da Microsoft e vengono archiviati a livello di area per ogni risorsa.

**D: Quali informazioni su un ambiente vengono trasmesse e archiviate nel servizio quando si usa Ancoraggi nello spazio di Azure? Le immagini dell'ambiente vengono trasmesse e archiviate?**

**R**: Quando si creano o si individuano gli ancoraggi, le immagini dell'ambiente vengono elaborate nel dispositivo in un formato derivato. Questo formato derivato viene trasmesso e archiviato nel servizio.

Per garantire la trasparenza, di seguito è riportata un'immagine di un ambiente e della nuvola di punti rada (spare point cloud) derivata. La nuvola di punti mostra la rappresentazione geometrica dell'ambiente che viene trasmesso e archiviato nel servizio. Per ogni punto della nuvola di punti rada, viene trasmesso e archiviato un hash delle caratteristiche visive di tale punto. L'hash deriva dai dati in pixel ma non ne contiene.

Ancoraggi nello spazio di Azure è conforme alle [Condizioni del contratto del servizio Azure](https://go.microsoft.com/fwLink/?LinkID=522330&amp;amp;clcid=0x9) e all'[Informativa sulla privacy di Microsoft](https://go.microsoft.com/fwlink/?LinkId=521839&amp;clcid=0x409).

![Un ambiente e la relativa nuvola di punti rada (spare point cloud) derivata](./media/sparse-point-cloud.png)
*Figura 1: Un ambiente e la relativa nuvola di punti rada (spare point cloud) derivata*

**D: È possibile inviare informazioni di diagnostica a Microsoft?**

**R**: Sì. Ancoraggi nello spazio di Azure ha una modalità diagnostica che gli sviluppatori possono scegliere esplicitamente tramite l'API Ancoraggi nello spazio di Azure. Ciò è utile, ad esempio, nel caso di un ambiente in cui non è possibile creare e individuare gli ancoraggi in modo prevedibile. Potrebbe venire richiesto l'invio di report di diagnostica contenente informazioni utili per eseguire il debug. Per altre informazioni, vedere [Registrazione e diagnostica in Ancoraggi nello spazio di Azure](./concepts/logging-diagnostics.md).

## <a name="availability-and-pricing-faqs"></a>Domande frequenti su disponibilità e prezzi

**D: Viene fornito un contratto di servizio?**

**R:** Come per i servizi Azure, puntiamo ad una disponibilità superiore al 99,9%. 

**D: È possibile pubblicare le app con Ancoraggi nello spazio di Azure negli App Store? È possibile usare gli Ancoraggi nello spazio di Azure per scenari di produzione cruciali?**

**R:** Sì, gli ancoraggi nello spazio di Azure sono disponibili a livello generale e hanno un contratto di servizio standard di Azure. Invitiamo gli utenti a sviluppare app per le distribuzioni di produzione e a [condividere commenti e suggerimenti](https://feedback.azure.com/forums/919252-azure-spatial-anchors) sul prodotto.

**D: Sono previste delle limitazioni?**

**R**: Sì, sono previste delle limitazioni.  Non è previsto che venga usato per lo sviluppo e il test di applicazioni tipiche. Per le distribuzioni di produzione, verranno soddisfatte le esigenze dei clienti su larga scala. Per altre informazioni, [contattare Microsoft](mailto:azuremrs@microsoft.com). 

**D: In quali aree è disponibile Ancoraggi nello spazio di Azure?**

**R:** Gli ancoraggi nello spazio di Azure sono attualmente disponibili nelle aree Stati Uniti occidentali 2, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti centro-meridionali, Europa occidentale, Europa settentrionale, Regno Unito meridionale e Australia orientale. In futuro saranno disponibili altre aree.

Ciò significa che sia l'ambiente di calcolo che l'archiviazione alla base di questo servizio risiedono in queste aree. Detto questo, non esistono restrizioni per la posizione in cui si trovano i client. 

**D: Vengono applicati addebiti per Ancoraggi nello spazio di Azure?**

**R:** È possibile trovare i dettagli sui prezzi nella [pagina dei prezzi](https://azure.microsoft.com/pricing/details/spatial-anchors/).

## <a name="technical-faqs"></a>Domande frequenti tecniche

**D: Come funziona Ancoraggi nello spazio di Azure?**

**R:** Ancoraggi nello spazio di Azure dipende da sistemi di rilevamento della realtà mista o della realtà aumentata. Questi sistemi percepiscono l'ambiente con telecamere e tracciano il dispositivo in 6 gradi di libertà (6DoF) mentre si muove nello spazio.

Dato un sistema di rilevamento 6DoF come blocco predefinito, Ancoraggi nello spazio di Azure consente di designare alcuni punti di interesse nell'ambiente reale come punti di "ancoraggio". Si potrebbe, ad esempio, usare un ancoraggio per il rendering del contenuto in un punto specifico del mondo reale.

Quando si crea un ancoraggio, l'SDK client acquisisce le informazioni sull'ambiente intorno a quel punto e le trasmette al servizio. Se un altro dispositivo cerca l'ancoraggio nello stesso spazio, vengono trasmessi dati simili al servizio. Tali dati vengono confrontati con i dati dell'ambiente archiviati in precedenza. La posizione dell'ancoraggio relativa al dispositivo viene quindi inviata per l'uso nell'applicazione.

**D: Come si integra Ancoraggi nello spazio di Azure con ARKit e ARCore in iOS e Android?**

**R:** Ancoraggi nello spazio di Azure sfrutta le funzionalità di rilevamento native di ARKit e ARCore. Inoltre, gli SDK per iOS e Android offrono funzionalità come la persistenza degli ancoraggi in un servizio cloud gestito e la possibilità di consentire alle app di individuare nuovamente tali ancoraggi semplicemente collegandosi al servizio.

**D: Come si integra Ancoraggi nello spazio di Azure con HoloLens?**

**R:** Ancoraggi nello spazio di Azure sfrutta le funzionalità di rilevamento native di HoloLens. Viene fornito un SDK di Ancoraggi nello spazio di Azure per la creazione di app in HoloLens. L'SDK si integra con le funzionalità native di HoloLens e fornisce funzionalità aggiuntive. Queste funzionalità includono la possibilità per gli sviluppatori di app di rendere persistenti gli ancoraggi in un servizio cloud gestito e di consentire alle app di individuare nuovamente tali ancoraggi collegandosi al servizio.

**D: Quali piattaforme e linguaggi sono supportati da Ancoraggi nello spazio di Azure?**

**R:** Gli sviluppatori possono creare app con Ancoraggi nello spazio di Azure usando strumenti e framework familiari per il proprio dispositivo:

- Unity in HoloLens, iOS e Android
- Xamarin in iOS e Android
- Swift o Objective-C in iOS
- Java o Android NDK in Android
- C++/WinRT in HoloLens

Iniziare a [sviluppare un'app qui](index.yml).

**D: Funziona con Unreal?**

**R:** Il supporto per Unreal verrà considerato in futuro.

**D: Quali porte e protocolli vengono utilizzati da Ancoraggi nello spazio di Azure?**

**R:** Ancoraggi nello spazio di Azure comunica tramite porta TCP 443 usando un protocollo crittografato. Per l'autenticazione, viene utilizzata [Azure Active Directory](../active-directory/index.yml), che comunica tramite HTTPS sulla porta 443.