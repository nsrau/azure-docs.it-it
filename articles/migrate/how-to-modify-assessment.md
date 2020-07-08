---
title: Personalizzare le valutazioni per la valutazione di Azure Migrate server | Microsoft Docs
description: Viene descritto come personalizzare le valutazioni create con Azure Migrate server Assessment
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: de526da255d0ffb2d4c8f13d87d9b9e230c8bbd7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85561829"
---
# <a name="customize-an-assessment"></a>Personalizzare una valutazione

Questo articolo descrive come personalizzare le valutazioni create da Azure Migrate server assessment.

[Azure Migrate](migrate-services-overview.md) offre un hub centrale per tenere traccia dell'individuazione, della valutazione e della migrazione ad Azure di carichi di lavoro e app locali, oltre che di VM del cloud privato/pubblico. L'hub fornisce gli strumenti di Azure Migrate per la valutazione e la migrazione, nonché offerte di ISV terzi.

È possibile usare lo strumento Azure Migrate server assessment per creare valutazioni per le macchine virtuali VMware locali e le VM Hyper-V, in preparazione per la migrazione ad Azure. Lo strumento Server Assessment consente di valutare i server locali per la migrazione alle macchine virtuali IaaS di Azure e alla soluzione Azure VMware (AVS). 

## <a name="about-assessments"></a>Informazioni sulle valutazioni

Le valutazioni create con server assessment sono uno snapshot temporizzato dei dati. È possibile creare due tipi di valutazioni con Azure Migrate: Valutazione server.

**Tipo di valutazione** | **Dettagli**
--- | --- 
**Macchina virtuale di Azure** | Valutazioni per la migrazione dei server locali in macchine virtuali di Azure. <br/><br/> È possibile valutare le [macchine virtuali VMware](how-to-set-up-appliance-vmware.md)locali, le [VM Hyper-V](how-to-set-up-appliance-hyper-v.md)e i [server fisici](how-to-set-up-appliance-physical.md) per la migrazione ad Azure usando questo tipo di valutazione. (concepts-assessment-calculation.md)
**Soluzione Azure VMware (AVS)** | Valutazioni per la migrazione dei server locali alla [soluzione VMware di Azure (AVS)](https://docs.microsoft.com/azure/azure-vmware/introduction). <br/><br/> È possibile valutare le [macchine virtuali VMware](how-to-set-up-appliance-vmware.md) locali per la migrazione ad Azure VMware Solution (AVS) con questo tipo di valutazione. [Altre informazioni](concepts-azure-vmware-solution-assessment-calculation.md)

Una valutazione delle VM di Azure in server Assessment offre due opzioni di criteri di ridimensionamento:

**Criteri di ridimensionamento** | **Dettagli** | **Dati**
--- | --- | ---
**Basata sulle prestazioni** | Valutazioni che fanno raccomandazioni basate sui dati sulle prestazioni raccolti | **Azure VM Assessment**: la raccomandazione sulle dimensioni delle macchine virtuali è basata sui dati di utilizzo della CPU e della memoria.<br/><br/> L'indicazione del tipo di disco (disco rigido/SSD standard o dischi gestiti Premium) si basa sui IOPS e la velocità effettiva dei dischi locali.<br/><br/> **Valutazione della soluzione VMware di Azure (AVS)**: la raccomandazione dei nodi AVS si basa sui dati di utilizzo della CPU e della memoria.
**Così come sono in locale** | Valutazioni che non utilizzano dati sulle prestazioni per apportare raccomandazioni. | **Valutazione delle VM di Azure**: le indicazioni sulle dimensioni delle VM sono basate sulle dimensioni della macchina virtuale locale<br/><br> Il tipo di disco consigliato è basato su quello selezionato nell'impostazione del tipo di archiviazione per la valutazione.<br/><br/> **Valutazione della soluzione VMware di Azure (AVS)**: la raccomandazione dei nodi AVS si basa sulle dimensioni della macchina virtuale locale.


## <a name="how-is-an-assessment-done"></a>Come viene eseguita una valutazione?

Una valutazione effettuata in Azure Migrate server Assessment prevede tre fasi. La valutazione inizia con un'analisi di idoneità, seguita dalla determinazione della dimensione e termina con una stima dei costi mensili. Un computer passa alla fase successiva soltanto se supera quella precedente. Ad esempio, se un computer non supera il controllo di idoneità di Azure, viene contrassegnato come non idoneo per Azure e la dimensione e i costi non vengono calcolati. [Altre informazioni.](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation)

## <a name="whats-in-an-azure-vm-assessment"></a>Che cosa è in una valutazione di VM di Azure?

**Proprietà** | **Dettagli**
--- | ---
**Posizione di destinazione** | Area di Azure in cui si vuole eseguire la migrazione.<br/> Server Assessment supporta attualmente le aree di destinazione seguenti: Australia orientale, Australia sudorientale, Brasile meridionale, Canada centrale, Canada orientale, India centrale, Stati Uniti centrali, Cina orientale, Cina settentrionale, Asia orientale, Stati Uniti orientali, Stati Uniti orientali, Germania centrale, Germania nordorientale, Giappone orientale, Giappone occidentale, Corea centrale, Corea meridionale, Stati Uniti centro-settentrionali, Europa settentrionale, Stati Uniti centro-meridionali, Asia sudorientale, India meridionale Regno Unito occidentale Regno Unito meridionale , US Gov Texas, US Gov Virginia, Stati Uniti centro-occidentali, Europa occidentale, India occidentale, Stati Uniti occidentali e Uniti occidentali.
**Tipo di archiviazione** | È possibile usare questa proprietà per specificare il tipo di dischi che si vuole spostare in Azure.<br/><br/> Per il dimensionamento locale è possibile specificare il tipo di archiviazione di destinazione come dischi gestiti da Premium, dischi gestiti da SDD Standard o dischi gestiti da HDD Standard. Per il dimensionamento basato sulle prestazioni, è possibile specificare il tipo di disco di destinazione come dischi automatici, gestiti con gestione Premium, dischi gestiti da HDD Standard o dischi gestiti da SDD Standard.<br/><br/> Quando si specifica il tipo di archiviazione automatico, la preferenza dei dischi è basata sui dati delle relative prestazioni (numero di operazioni di I/O al secondo e velocità effettiva). Se si specifica il tipo di archiviazione Premium o standard, la valutazione indicherà uno SKU del disco all'interno del tipo di archiviazione selezionato. Se si vuole ottenere un contratto di Service per macchine virtuali a istanza singola pari al 99,9%, è possibile specificare il tipo di archiviazione come dischi gestiti Premium. che garantisce che tutti i dischi nella valutazione vengano raccomandati come Managed Disks Premium. Azure
**Istanze riservate (RI)** | Questa proprietà consente di specificare se sono presenti [istanze riservate](https://azure.microsoft.com/pricing/reserved-vm-instances/) in Azure. le stime dei costi nella valutazione vengono quindi effettuate in sconti per il ri. Le istanze riservate sono attualmente supportate solo per l'offerta con pagamento in base al consumo in Azure Migrate.
**Criterio di dimensionamento** | Il criterio da usare per dimensionare correttamente le VM per Azure. È possibile eseguire il ridimensionamento in *base alle prestazioni* o ridimensionare le macchine virtuali *in locale*, senza considerare la cronologia delle prestazioni.
**Cronologia delle prestazioni** | Durata da considerare per la valutazione dei dati sulle prestazioni dei computer. Questa proprietà è applicabile solo quando il criterio di dimensionamento è *basato sulle prestazioni*.
**Utilizzo percentile** | Valore percentile dell'esempio di prestazioni da tenere in considerazione per il dimensionamento corretto. Questa proprietà è applicabile solo quando il ridimensionamento è *basato sulle prestazioni*.
**Serie VM** |     È possibile specificare la serie di macchine virtuali che si intende tenere in considerazione per il dimensionamento corretto. Se ad esempio si ha un ambiente di produzione di cui non si intende eseguire la migrazione a macchine virtuali serie A in Azure, è possibile escludere la serie A dall'elenco o dalla serie e il dimensionamento viene eseguito solo nella serie selezionata.
**Fattore di comfort** | Azure Migrate server Assessment considera un buffer (fattore di comfort) durante la valutazione. che viene applicato ai dati sull'utilizzo delle VM (CPU, memoria, disco e rete). Il fattore di comfort tiene conto di aspetti come utilizzo stagionale, breve cronologia delle prestazioni e probabile aumento dell'utilizzo futuro.<br/><br/> Da una VM con 10 core e un utilizzo del 20%, ad esempio, si ottiene normalmente una VM con 2 core. Con un fattore di comfort pari a 2.0x, invece, il risultato è una VM con 4 core.
**Offerta** | [Offerta di Azure](https://azure.microsoft.com/support/legal/offer-details/) sottoscritta. Azure Migrate stima il costo di conseguenza.
**Valuta** | Valuta di fatturazione.
**Sconto (%)** | Qualsiasi sconto specifico della sottoscrizione ricevuto oltre all'offerta Azure.<br/> L'impostazione predefinita è 0%.
**Tempo di attività macchina virtuale** | Se non si prevede di eseguire ininterrottamente le macchine virtuali in Azure, è possibile specificare la durata (numero di giorni al mese e numero di ore al giorno) dell'esecuzione. Il costo verrà stimato di conseguenza.<br/> Il valore predefinito è di 31 giorni al mese e di 24 ore al giorno.
**Vantaggio Azure Hybrid** | Specificare se si dispone di Software Assurance e si è idonei per [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se il valore è impostato su Sì, alle macchine virtuali Windows si applicano i prezzi di Azure per sistemi non Windows. Il valore predefinito è Yes.

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Cosa c'è in una valutazione della soluzione VMware di Azure (AVS)?

Ecco cosa è incluso in una valutazione AVS in server assessment:


| **Proprietà** | **Dettagli** |
| - | - |
| **Posizione di destinazione** | Specifica la posizione del cloud privato AVS in cui si vuole eseguire la migrazione.<br/><br/> AVS assessment in server Assessment supporta attualmente le aree di destinazione seguenti: Stati Uniti orientali, Europa occidentale, Stati Uniti occidentali. |
| **Tipo di archiviazione** | Specifica il motore di archiviazione da usare in AVS.<br/><br/> Si noti che AVS Assessment supporta solo rete VSAN come tipo di archiviazione predefinito. |
**Istanze riservate (RIs)** | Questa proprietà consente di specificare istanze riservate in AVS. I servizi di installazione remota non sono attualmente supportati per i nodi AVS. |
**Tipo di nodo** | Specifica il [tipo di nodo AVS](https://docs.microsoft.com/azure/azure-vmware/concepts-private-clouds-clusters) usato per eseguire il mapping delle macchine virtuali locali. Si noti che il tipo di nodo predefinito è AV36. <br/><br/> Azure Migrate consiglierà un numero di nodi necessario per la migrazione delle macchine virtuali in AVS. |
**Impostazione di ITF, livello RAID** | Specifica l'errore applicabile da tollerare e le combinazioni RAID. L'opzione di ITF selezionata combinata con il requisito del disco della macchina virtuale locale determina lo spazio di archiviazione rete VSAN totale richiesto in AVS. |
**Criterio di dimensionamento** | Imposta i criteri da usare per _dimensionare correttamente_ le VM per AVS. È possibile optare per il dimensionamento in _base alle prestazioni_ o _come locale_ senza considerare la cronologia delle prestazioni. |
**Cronologia delle prestazioni** | Imposta la durata da considerare per la valutazione dei dati sulle prestazioni dei computer. Questa proprietà è applicabile solo quando i criteri di ridimensionamento sono _basati sulle prestazioni_. |
**Utilizzo percentile** | Specifica il valore percentile del set di campionamento delle prestazioni da considerare per il corretto dimensionamento. Questa proprietà è applicabile solo quando il ridimensionamento è basato sulle prestazioni.|
**Fattore di comfort** | Azure Migrate server Assessment considera un buffer (fattore di comfort) durante la valutazione. che viene applicato ai dati sull'utilizzo delle VM (CPU, memoria, disco e rete). Il fattore di comfort tiene conto di aspetti come utilizzo stagionale, breve cronologia delle prestazioni e probabile aumento dell'utilizzo futuro.<br/><br/> Da una VM con 10 core e un utilizzo del 20%, ad esempio, si ottiene normalmente una VM con 2 core. Con un fattore di comfort pari a 2.0x, invece, il risultato è una VM con 4 core. |
**Offerta** | Visualizza l' [offerta di Azure](https://azure.microsoft.com/support/legal/offer-details/) in cui si è iscritti. Azure Migrate stima il costo di conseguenza.|
**Valuta** | Mostra la valuta di fatturazione per l'account. |
**Sconto (%)** | Elenca gli sconti specifici della sottoscrizione ricevuti dall'offerta di Azure. L'impostazione predefinita è 0%. |
**Vantaggio Azure Hybrid** | Specifica se il Software Assurance è idoneo per [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Sebbene non abbia alcun effetto sui prezzi delle soluzioni VMware di Azure a causa del prezzo basato sui nodi, i clienti possono comunque applicare le licenze del sistema operativo locale (basate su Microsoft) in AVS usando i vantaggi di Azure Hybrid. Altri fornitori di sistemi operativi software dovranno fornire le proprie condizioni di licenza, ad esempio RHEL. |
**oversubscription vCPU** | Specifica il rapporto tra il numero di core virtuali associato a 1 core fisico nel nodo AVS. Il valore predefinito nei calcoli è 4 vCPU: 1 core fisico in AVS. <br/><br/> Gli utenti dell'API possono impostare questo valore come intero. Si noti che vCPU oversubscription > 4:1 può iniziare a causare un peggioramento delle prestazioni, ma può essere usato per i carichi di lavoro di tipo server Web. |

## <a name="edit-assessment-properties"></a>Modificare le proprietà di valutazione

Per modificare le proprietà della valutazione dopo la creazione di una valutazione, procedere come segue:

1. Nel progetto Azure Migrate fare clic su **Server**.
2. In **Azure migrate: server Assessment**, fare clic sul conteggio assessment.
3. In **valutazione**fare clic sulla valutazione pertinente > **modificare le proprietà**.
5. Personalizzare le proprietà di valutazione in base alle tabelle precedenti.
6. Fare clic su **Salva** per aggiornare la valutazione.


Quando si crea una valutazione, è anche possibile modificare le proprietà di valutazione.


## <a name="next-steps"></a>Passaggi successivi

- [Altre](concepts-assessment-calculation.md) informazioni su come vengono calcolate le valutazioni delle VM di Azure.
- [Altre](concepts-azure-vmware-solution-assessment-calculation.md) informazioni su come vengono calcolate le valutazioni AVS.