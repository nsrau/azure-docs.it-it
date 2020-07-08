---
title: Misurazioni utente effettive di Gestione traffico di Microsoft Azure
description: In questa introduzione, informazioni sul funzionamento di gestione traffico di Azure misurazioni utente reale.
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: e38e1255b1a84ab5d3fd37e16eb65c76001dbfa1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76938441"
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Panoramica di Misurazioni utente reale in Gestione traffico

Quando si configura un profilo di Gestione traffico per usare il metodo di routing delle prestazioni, il servizio esamina da dove provengono le richieste di query DNS e prende decisioni di routing per indirizzare tali richiedenti all'area di Azure che dia loro la latenza più bassa. Questa operazione viene eseguita usando le informazioni esistenti sulla latenza di rete che Gestione traffico mantiene per reti di utenti finali diverse.

Misurazioni utente reale consente di misurare le misurazioni di latenza di rete per le aree di Azure, dalle applicazioni client che usano gli utenti finali, e a Gestione traffico di considerare anche tali informazioni per prendere decisioni di routing. Se si sceglie di usare Misurazioni utente reale, è possibile aumentare l'accuratezza del routing per le richieste provenienti dalle reti in cui si trovano gli utenti finali. 

## <a name="how-real-user-measurements-work"></a>Funzionamento di Misurazioni utente reale

Misurazioni utente reale fa in modo che le applicazioni client misurino la latenza per le aree di Azure come rilevata dalle reti degli utenti finali in cui vengono usate. Ad esempio, se si dispone di una pagina Web a cui accedono utenti da posizioni diverse (ad esempio nelle aree America del Nord), è possibile usare Misurazioni utente reale con il metodo di routing delle prestazioni per ottenerle nella migliore area di Azure in cui l'applicazione server è ospitata.

Inizia incorporando un JavaScript fornito da Azure (con una chiave univoca in esso) nelle pagine Web. Al termine dell'operazione, ogni volta che un utente visita la pagina Web, JavaScript esegue una query a Gestione traffico per ottenere informazioni sulle aree di Azure che deve misurare. Il servizio restituisce un set di endpoint allo script, che quindi misura tali aree consecutivamente scaricando un'immagine a un singolo pixel ospitata in tali aree di Azure e annotando la latenza tra il momento in cui la richiesta è stata inviata e l'ora di ricezione del primo byte. Queste misurazioni vengono quindi riferite al servizio di Gestione traffico.

Nel tempo, questo si verifica più volte e in molte reti facendo sì che Gestione traffico ottenga informazioni più accurate sulle caratteristiche di latenza delle reti in cui si trovano gli utenti finali. Queste informazioni iniziano ad essere incluse nelle decisioni di routing prese da Gestione traffico. Di conseguenza, tale operazione comporta una maggiore accuratezza in quelle decisioni basate sulle misurazioni utente reale inviate.

Quando si usa Misurazioni utente reale, la fatturazione viene eseguita in base al numero di misurazioni inviate a Gestione traffico. Per altri dettagli sui prezzi, visitare la [pagina dei prezzi di Gestione Traffico](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faqs"></a>Domande frequenti

* [Quali sono i vantaggi offerti dall'utilizzo di Misurazioni utente reale?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-benefits-of-using-real-user-measurements)

* [È possibile usare Misurazioni utente reale con aree non di Azure?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-non-azure-regions)

* [Quale metodo di routing trae vantaggio dalla funzionalità Misurazioni utente reale?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#which-routing-method-benefits-from-real-user-measurements)

* [È necessario abilitare Misurazioni utente reale per ogni profilo separatamente?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-enable-real-user-measurements-each-profile-separately)

* [Come si disattiva Misurazioni utente reale in una sottoscrizione?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-turn-off-real-user-measurements-for-my-subscription)

* [È possibile usare Misurazioni utente reale con applicazioni client diverse dalle pagine Web?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-client-applications-other-than-web-pages)

* [Quante misurazioni vengono effettuate ogni volta che viene eseguito il rendering di una pagina Web con Misurazioni utente reale abilitata?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered)

* [È presente un ritardo prima dell'esecuzione dello script di Misurazioni utente reale nella pagina Web?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage)

* [È possibile usare misurazioni utente reale solo con le aree di Azure che si intende misurare?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure)

* [È possibile limitare il numero di misurazioni effettuate a un numero specifico?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-limit-the-number-of-measurements-made-to-a-specific-number)

* [È possibile visualizzare le misurazioni effettuate dall'applicazione client nell'ambito di Misurazioni utente reale?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements)

* [È possibile modificare lo script di misurazione di Gestione traffico?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-modify-the-measurement-script-provided-by-traffic-manager)

* [Gli altri utenti sono in grado di vedere la chiave di un utente che viene usata con Misurazioni utente reale?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements)

* [È possibile che altri utenti usino la chiave di Misurazioni utente reale di un utente senza autorizzazione?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-others-abuse-my-rum-key)

* [È necessario inserire il codice JavaScript di misurazione in tutte le pagine Web?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages)

* [Gestione traffico è in grado di identificare le informazioni sugli utenti finali se si usa Misurazioni utente reale?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements)

* [La pagina Web che misura con Misurazioni utente reale deve usare Gestione traffico per il routing?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing)

* [È necessario ospitare un qualsiasi servizio nelle aree di Azure da usare con Misurazioni utente reale?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements)

* [L'utilizzo della larghezza di banda di Azure aumenta quando si usa Misurazioni utente reale?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements)

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come usare [Misurazioni utente reale con le pagine Web](traffic-manager-create-rum-web-pages.md)
- Informazioni [sul funzionamento di gestione traffico](traffic-manager-overview.md)
- Altre informazioni su [Mobile Center](https://docs.microsoft.com/mobile-center/)
- Ulteriori informazioni sui [metodi di routing del traffico](traffic-manager-routing-methods.md) supportati da Gestione traffico
- Informazioni su come [creare un profilo di Gestione traffico](traffic-manager-create-profile.md)

