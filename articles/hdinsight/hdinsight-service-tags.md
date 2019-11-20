---
title: Tag di servizio del gruppo di sicurezza di rete (NSG) per Azure HDInsight
description: Usare i tag del servizio HDInsight per consentire il traffico in ingresso verso il cluster dai nodi HDInsight Health and Management Services, senza aggiungere esplicitamente indirizzi IP ai gruppi di sicurezza di rete.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/19/2019
ms.openlocfilehash: 7e3ce33bdf0773ababe5eb190877a9288c094c5c
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74187085"
---
# <a name="network-security-group-nsg-service-tags-for-azure-hdinsight"></a>Tag di servizio del gruppo di sicurezza di rete (NSG) per Azure HDInsight

I tag del servizio HDInsight per i gruppi di sicurezza di rete (gruppi) sono gruppi di indirizzi IP per i servizi di gestione e integrità. Questi gruppi consentono di ridurre al minimo la complessità per la creazione delle regole di sicurezza. I [tag di servizio](../virtual-network/security-overview.md#service-tags) forniscono un metodo alternativo per consentire il traffico in ingresso da indirizzi IP specifici senza immettere ogni [indirizzo IP di gestione](hdinsight-management-ip-addresses.md) nei gruppi di sicurezza di rete.

Questi tag del servizio vengono creati e gestiti dal servizio HDInsight. Non è possibile creare un tag di servizio personalizzato o modificare un tag esistente. Microsoft gestisce i prefissi di indirizzo che corrispondono al tag del servizio e aggiorna automaticamente il tag di servizio in base alla modifica degli indirizzi.

## <a name="getting-started-with-service-tags"></a>Introduzione ai tag del servizio

Sono disponibili due opzioni per l'uso dei tag di servizio nei gruppi di sicurezza di rete:

1. Usa un singolo tag del servizio HDInsight: questa opzione consente di aprire la rete virtuale a tutti gli indirizzi IP utilizzati dal servizio HDInsight per monitorare i cluster in tutte le aree. Questa opzione è il metodo più semplice, ma potrebbe non essere appropriata se si hanno requisiti di sicurezza restrittivi.

1. Usare più tag di servizio internazionali: questa opzione consente di aprire la rete virtuale solo per gli indirizzi IP usati da HDInsight in tale area specifica. Tuttavia, se si usano più aree, sarà necessario aggiungere più tag di servizio alla rete virtuale.

## <a name="use-a-single-global-hdinsight-service-tag"></a>Usa un singolo tag del servizio HDInsight globale

Il modo più semplice per iniziare a usare i tag di servizio con il cluster HDInsight consiste nell'aggiungere il tag globale `HDInsight` a una regola del gruppo di sicurezza di rete. Per istruzioni su come aggiungere tag di servizio al gruppo di sicurezza di rete, vedere [gruppi di sicurezza: Tag del servizio](../virtual-network/security-overview.md#service-tags).

Questo tag contiene gli indirizzi IP dei servizi di gestione e integrità per tutte le aree in cui HDInsight è disponibile e garantisce che il cluster sia in grado di comunicare con i servizi di integrità e gestione necessari indipendentemente dalla posizione in cui viene creato.

## <a name="use-regional-hdinsight-service-tags"></a>Usare i tag di servizio HDInsight regionali

Se l'opzione non funziona perché sono necessarie autorizzazioni più restrittive, è possibile consentire solo i tag del servizio applicabili per la propria area. I tag di servizio applicabili possono essere uno, due o tre tag di servizio, a seconda dell'area in cui viene creato il cluster.

Per scoprire quali tag di servizio aggiungere per la propria area, leggere le sezioni seguenti del documento.

### <a name="use-a-single-regional-service-tag"></a>Usa un singolo tag di servizio a livello di area

Se si preferisce l'opzione due dei tag del servizio e il cluster si trova in una delle aree elencate in questa tabella, è sufficiente aggiungere un singolo tag di servizio regionale al gruppo di sicurezza di rete.

| Paese | Area | Tag servizio |
| ---- | ---- | ---- |
| Australia | Australia orientale | HDInsight. AustraliaEast |
| &nbsp; | Australia sudorientale | HDInsight. AustraliaSoutheast |
| &nbsp; | Australia centrale | HDInsight. AustraliaCentral |
| Cina | Cina orientale 2 | HDInsight. ChinaEast2 |
| &nbsp; | Cina settentrionale 2 | HDInsight. ChinaNorth2 |
| Stati Uniti | Stati Uniti centro-settentrionali | HDInsight. NorthCentralUS |
| &nbsp; | Stati Uniti occidentali 2 | HDInsight. WestUS2 |
| &nbsp; | Stati Uniti centro-occidentali | HDInsight. WestCentralUS |
| Canada | Canada orientale | HDInsight. CanadaEast |
| Brasile | Brasile meridionale | HDInsight. BrazilSouth |
| Corea del Sud | Corea del Sud centrale | HDInsight. KoreaCentral |
| &nbsp; | Corea del Sud meridionale | HDInsight. KoreaSouth |
| India | India centrale | HDInsight. CentralIndia |
| &nbsp; | India meridionale | HDInsight. SouthIndia |
| Giappone | Giappone occidentale | HDInsight. JapanWest |
| Francia | Francia centrale| HDInsight. FranceCentral |
| Regno Unito | Regno Unito meridionale | HDInsight. UKSouth |
| Azure per enti pubblici (Fairfax) | DOD Central   | HDInsight. USDoDCentral |
| &nbsp; | Governo degli Stati Uniti - Texas | HDInsight. USGovTexas |
| &nbsp; | DOD est | HDInsight. USDoDEast |

### <a name="use-multiple-regional-service-tags"></a>Usare più tag di servizio internazionali

Se si preferisce l'opzione 2 del tag di servizio e l'area in cui è stato creato il cluster non è stata elencata in precedenza, è necessario consentire più tag di servizio a livello di area. La necessità di usare più di uno è dovuto alle differenze nella disposizione dei provider di risorse per le diverse aree.

Le aree rimanenti sono divise in gruppi in base ai tag di servizio regionali usati.

#### <a name="group-1"></a>Gruppo 1

Se il cluster viene creato in una delle aree della tabella seguente, consentire i tag del servizio `HDInsight.WestUS` e `HDInsight.EastUS` oltre al tag del servizio regionale elencato. Le aree in questa sezione richiedono tre tag di servizio.

Ad esempio, se il cluster viene creato nell'area `East US 2`, sarà necessario aggiungere i tag di servizio seguenti al gruppo di sicurezza di rete:

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| Paese | Area | Tag servizio |
| ---- | ---- | ---- |
| Stati Uniti | Stati Uniti orientali 2 | HDInsight. EastUS2 |
| &nbsp; | Stati Uniti centrali | HDInsight. Centralus |
| &nbsp; | NorthCentral US | HDInsight. NorthCentralUS |
| &nbsp; | Stati Uniti centro-meridionali | HDInsight. SouthCentralUS |
| &nbsp; | Stati Uniti Orientali | HDInsight. Eastus |
| &nbsp; | Stati Uniti occidentali | HDInsight. Westus |
| Giappone | Giappone orientale | HDInsight. JapanEast |
| Europa | Europa settentrionale | HDInsight. NorthEurope |
| &nbsp; | Europa occidentale| HDInsight. WestEurope |
| Asia | Asia orientale | HDInsight. EastAsia |
| &nbsp; | Asia sudorientale | HDInsight. SoutheastAsia |
| Australia | Australia orientale | HDInsight. AustraliaEast |

#### <a name="group-2"></a>Gruppo 2

I cluster nelle aree di **Cina settentrionale** e **Cina orientale**devono consentire due tag di servizio: `HDInsight.ChinaNorth` e `HDInsight.ChinaEast`.

#### <a name="group-3"></a>Gruppo 3

I cluster nelle aree di **US gov Iowa** e **US gov Virginia**devono consentire due tag di servizio: `HDInsight.USGovIowa` e `HDInsight.USGovVirginia`.

#### <a name="group-4"></a>Gruppo 4

I cluster nelle aree di **Germania centrale** e **Germania nord-orientale**devono consentire due tag di servizio: `HDInsight.GermanyCentral` e `HDInsight.GermanyNorthEast`.

## <a name="next-steps"></a>Passaggi successivi

* [Gruppi di sicurezza di rete-tag del servizio](../virtual-network/security-overview.md#security-rules)
* [Creare reti virtuali per i cluster HDInsight di Azure](hdinsight-create-virtual-network.md)
