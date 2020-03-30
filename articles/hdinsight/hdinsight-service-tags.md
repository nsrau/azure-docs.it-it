---
title: Tag del servizio del gruppo di sicurezza di rete per Azure HDInsightNetwork security group (NSG) service tags for Azure HDInsight
description: Usare i tag del servizio HDInsight per consentire il traffico in ingresso verso il cluster dai nodi dei servizi di integrità e di gestione di HDInsight, senza aggiungere in modo esplicito indirizzi IP ai gruppi di sicurezza di rete.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/10/2020
ms.openlocfilehash: a72753d5553e79a8ed28c3afcc7e54af6c2d230c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79117228"
---
# <a name="network-security-group-nsg-service-tags-for-azure-hdinsight"></a>Tag del servizio del gruppo di sicurezza di rete per Azure HDInsightNetwork security group (NSG) service tags for Azure HDInsight

I tag del servizio HDInsight per i gruppi di sicurezza di rete (NSG) sono gruppi di indirizzi IP per i servizi di integrità e gestione. Questi gruppi consentono di ridurre al minimo la complessità per la creazione di regole di sicurezza. [I tag di](../virtual-network/security-overview.md#service-tags) servizio forniscono un metodo alternativo per consentire il traffico in ingresso da indirizzi IP specifici senza immettere ognuno degli [indirizzi IP](hdinsight-management-ip-addresses.md) di gestione nei gruppi di sicurezza di rete.

Questi tag di servizio vengono creati e gestiti dal servizio HDInsight.These service tags are created and managed by the HDInsight service. Non è possibile creare un tag di servizio personalizzato o modificare un tag esistente. Microsoft gestisce i prefissi degli indirizzi che corrispondono al tag del servizio e aggiorna automaticamente il tag del servizio quando gli indirizzi cambiano.

## <a name="getting-started-with-service-tags"></a>Introduzione ai tag di servizio

Sono disponibili due opzioni per l'utilizzo dei tag di servizio nei gruppi di sicurezza di rete:You have two options for using service tags in your network security groups:

1. Usare un singolo tag di servizio HDInsight: questa opzione consente di aprire la rete virtuale a tutti gli indirizzi IP utilizzati dal servizio HDInsight per monitorare i cluster in tutte le aree. Questa opzione è il metodo più semplice, ma potrebbe non essere appropriata se si dispone di requisiti di sicurezza restrittivi.

1. Usa più tag del servizio regionale: questa opzione aprirà la rete virtuale solo per gli indirizzi IP utilizzati da HDInsight in quell'area specifica. Tuttavia, se si usano più aree, sarà necessario aggiungere più tag di servizio alla rete virtuale.

## <a name="use-a-single-global-hdinsight-service-tag"></a>Usare un singolo tag di servizio HDInsight globaleUse a single global HDInsight service tag

Il modo più semplice per iniziare a usare i tag `HDInsight` di servizio con il cluster HDInsight consiste nell'aggiungere il tag globale a una regola del gruppo di sicurezza di rete.

1. Dal [portale di Azure](https://portal.azure.com/)selezionare il gruppo di sicurezza di rete.

1. In **Impostazioni**, selezionare Regole di **sicurezza in ingresso**, quindi selezionare **Aggiungi**.

1. Nell'elenco a discesa **Origine** selezionare **Tag servizio.**

1. Nell'elenco a discesa **Tag servizio** di origine selezionare **HDInsight**.

    ![Tag di servizio per l'aggiunta del portale di AzureAzure portal add service tag](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

Questo tag contiene gli indirizzi IP dei servizi di integrità e gestione per tutte le aree in cui HDInsight è disponibile e garantisce che il cluster possa comunicare con i servizi di integrità e gestione necessari indipendentemente da dove viene creato.

## <a name="use-regional-hdinsight-service-tags"></a>Usare i tag del servizio HDInsight regionaliUse regional HDInsight service tags

Se l'opzione one non funziona perché sono necessarie autorizzazioni più restrittive, è possibile consentire solo i tag di servizio applicabili per la propria area geografica. I tag di servizio applicabili possono essere uno, due o tre tag di servizio, a seconda dell'area in cui viene creato il cluster.

Per scoprire quali tag di servizio aggiungere per la propria regione, leggere le sezioni seguenti del documento.

### <a name="use-a-single-regional-service-tag"></a>Utilizzare un singolo tag di servizio regionaleUse a single regional service tag

Se si preferisce l'opzione del tag di servizio due e il cluster si trova in una delle aree elencate in questa tabella, è sufficiente aggiungere un singolo tag del servizio regionale al gruppo di sicurezza di rete.

| Country | Region | Tag di servizio |
| ---- | ---- | ---- |
| Australia | Australia orientale | HDInsight.AustraliaEast |
| &nbsp; | Australia sud-orientale | HDInsight.AustraliaSoutheast |
| &nbsp; | Australia centrale | HDInsight.AustraliaCentral |
| Cina | Cina orientale 2 | HDInsight.ChinaEast2 |
| &nbsp; | Cina settentrionale 2 | HDInsight.ChinaNorth2 |
| Stati Uniti | Stati Uniti centro-settentrionali | HDInsight.NorthCentralUS |
| &nbsp; | Stati Uniti occidentali 2 | HDInsight.WestUS2 |
| &nbsp; | Stati Uniti centro-occidentali | HDInsight.WestCentralUS |
| Canada | Canada orientale | HDInsight.CanadaEast |
| Brasile | Brasile meridionale | HDInsight.BrasileSud |
| Corea del Sud | Corea centrale | HDInsight.KoreaCentral |
| &nbsp; | Corea meridionale | HDInsight.KoreaSouth |
| India | India centrale | HDInsight.CentralIndia |
| &nbsp; | India meridionale | HDInsight.SouthIndia |
| Giappone | Giappone occidentale | HDInsight.JapanWest |
| Francia | Francia centrale| HDInsight.FranceCentral |
| Regno Unito | Regno Unito meridionale | HDInsight.UKSouth |
| Azure Government | USDoD Centrale   | HDInsight.USDoDCentral |
| &nbsp; | USGov Texas | HDInsight.USGovTexas |
| &nbsp; | UsDoD Est | HDInsight.USDoDEast |
| &nbsp; | USGov Arizona | HDInsight.USGovArizona |

### <a name="use-multiple-regional-service-tags"></a>Usare più tag di servizio regionaleUse multiple regional service tags

Se si preferisce l'opzione di tag di servizio due e l'area in cui viene creato il cluster non è stata elencata in precedenza, è necessario consentire più tag di servizio regionali. La necessità di utilizzarne più di uno è dovuta alle differenze nella disposizione dei fornitori di risorse per le varie aree.

Le restanti aree sono suddivise in gruppi in base ai tag del servizio regionale che utilizzano.

#### <a name="group-1"></a>Gruppo 1

Se il cluster viene creato in una delle aree `HDInsight.WestUS` della `HDInsight.EastUS` tabella seguente, consentire i tag del servizio e, oltre al tag del servizio regionale, elencato. Le aree in questa sezione richiedono tre tag di servizio.

Ad esempio, se il cluster `East US 2` viene creato nell'area, sarà necessario aggiungere i tag di servizio seguenti al gruppo di sicurezza di rete:For example, if your cluster is created in the region, then you'll need to add the following service tags to your network security group:

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| Country | Region | Tag di servizio |
| ---- | ---- | ---- |
| Stati Uniti | Stati Uniti orientali 2 | HDInsight.EastUS2 |
| &nbsp; | Stati Uniti centrali | HDInsight.CentralUS |
| &nbsp; | Stati Uniti NorthCentral | HDInsight. NorthCentralUS |
| &nbsp; | Stati Uniti centro-meridionali | HDInsight.SouthCentralUS |
| &nbsp; | Stati Uniti orientali | HDInsight.EastUS |
| &nbsp; | Stati Uniti occidentali | HDInsight.WestUS |
| Giappone | Giappone orientale | HDInsight.JapanEast |
| Europa | Europa settentrionale | HDInsight.NorthEurope |
| &nbsp; | Europa occidentale| HDInsight.WestEurope |
| Asia | Asia orientale | HDInsight.EastAsia |
| &nbsp; | Asia sud-orientale | HDInsight.SoutheastAsia |
| Australia | Australia orientale | HDInsight.AustraliaEast |

#### <a name="group-2"></a>Gruppo 2

I cluster nelle regioni della **Cina Nord** e **Cina Est,** devono consentire due tag di servizio: `HDInsight.ChinaNorth` e `HDInsight.ChinaEast`.

#### <a name="group-3"></a>Gruppo 3

I cluster nelle regioni del **Gov Iowa degli Stati Uniti** e degli Stati Uniti **Gov Virginia**, devono consentire due tag di servizio: `HDInsight.USGovIowa` e `HDInsight.USGovVirginia`.

#### <a name="group-4"></a>Gruppo 4

I cluster nelle regioni della **Germania centrale** e della Germania `HDInsight.GermanyCentral` **Nord-est,** devono consentire due tag di servizio: e `HDInsight.GermanyNorthEast`.

## <a name="next-steps"></a>Passaggi successivi

- [Gruppi di sicurezza di rete - tag del servizioNetwork security groups - service tags](../virtual-network/security-overview.md#security-rules)
- [Creare reti virtuali per i cluster di Azure HDInsightCreate virtual networks for Azure HDInsight clusters](hdinsight-create-virtual-network.md)
