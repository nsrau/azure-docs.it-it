---
title: Pianificare un modello di soluzione per un'offerta di applicazione Azure
description: Informazioni sugli elementi necessari per creare un piano di modello di soluzione per una nuova offerta di applicazione Azure tramite il portale di Marketplace commerciale nel centro per i partner Microsoft.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 38c7072472a13d7fe3d529933ca17a51e6a86733
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94577805"
---
# <a name="plan-a-solution-template-for-an-azure-application-offer"></a>Pianificare un modello di soluzione per un'offerta di applicazione Azure

Questo articolo illustra i requisiti per la pubblicazione di un piano di modello di soluzione per un'offerta applicazione Azure. Il piano di un modello di soluzione è uno dei due tipi di piani supportati dalle offerte applicazione Azure. Per informazioni sulla differenza tra questi due tipi di piano, vedere [tipi di piani](plan-azure-application-offer.md#plans). Se non è già stato fatto, leggere [pianificare un'offerta di applicazione Azure](plan-azure-application-offer.md).

Il tipo di piano del modello di soluzione richiede un [modello di Azure Resource Manager (modello ARM)](/azure/azure-resource-manager/templates/overview) per distribuire automaticamente l'infrastruttura della soluzione.

## <a name="solution-template-requirements"></a>Requisiti del modello di soluzione

| Requisiti | Dettagli |
| ------------ | ------------- |
| Fatturazione e misurazione | I piani di modelli di soluzione non sono transazionali, ma possono essere usati per distribuire le offerte di macchine virtuali a pagamento fatturate tramite Microsoft Commercial Marketplace. Le risorse distribuite dal modello ARM della soluzione vengono configurate nella sottoscrizione di Azure del cliente. Le macchine virtuali con pagamento in base al consumo vengono sottoposte a transazione con il cliente tramite Microsoft e fatturate tramite la sottoscrizione di Azure del cliente. <br><br> Per la fatturazione BYOL (Bring your own License), sebbene Microsoft fattura i costi di infrastruttura sostenuti per la sottoscrizione del cliente, è necessario effettuare direttamente le spese di licenza software con il cliente. |
| Disco rigido virtuale (VHD) compatibile con Azure | Le macchine virtuali devono essere compilate in Windows o Linux. Per altre informazioni, vedere:<ul><li>[Creare un asset tecnico della VM di Azure](/azure/marketplace/partner-center-portal/vm-certification-issues-solutions#how-to-address-a-vulnerability-or-exploit-in-a-vm-offer.md) (per i dischi rigidi virtuali di Windows)</li><li>[Distribuzioni Linux approvate in Azure](/azure/virtual-machines/linux/endorsed-distros) (per dischi rigidi virtuali Linux).</li></ul> |
| Attribuzione dell'uso da parte dei clienti | L'abilitazione dell'attribuzione dell'utilizzo dei clienti è obbligatoria in tutti i modelli di soluzione pubblicati in Azure Marketplace. Per ulteriori informazioni sull'attribuzione dell'utilizzo dei clienti e su come abilitarla, vedere l' [attribuzione dell'utilizzo dei clienti partner di Azure](azure-partner-customer-usage-attribution.md). |
| Usare i dischi gestiti | [Managed disks](/azure/virtual-machines/windows/managed-disks-overview) è l'opzione predefinita per i dischi salvati in modo permanente di macchine virtuali IaaS (Infrastructure as a Service) in Azure. È necessario usare Managed disks nei modelli di soluzione.<ul><li>Per aggiornare i modelli di soluzione, seguire le istruzioni in [usare Managed disks in Azure Resource Manager Templates](/azure/virtual-machines/using-managed-disks-template-deployments)e usare gli [esempi](https://github.com/Azure/azure-quickstart-templates)forniti.</li><li>Per pubblicare il disco rigido virtuale come immagine in Azure Marketplace, importare il disco rigido virtuale sottostante dei dischi gestiti in un account di archiviazione usando [Azure PowerShell](/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd) o l'interfaccia della riga di comando di [Azure](/azure/virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd)</ul> |
| Pacchetto di distribuzione | È necessario un pacchetto di distribuzione che consentirà ai clienti di distribuire il piano. Se si creano più piani che richiedono la stessa configurazione tecnica, è possibile usare lo stesso pacchetto del piano. Per informazioni dettagliate, vedere la sezione successiva: pacchetto di distribuzione. |
|||

## <a name="deployment-package"></a>Pacchetto di distribuzione

Il pacchetto di distribuzione contiene tutti i file di modello necessari per questo piano, nonché eventuali risorse aggiuntive, incluse in un pacchetto con estensione zip.

Tutte le applicazioni Azure devono includere questi due file nella cartella radice di un archivio zip:

- Un file di modello di Resource Manager denominato [mainTemplate.json](/azure/azure-resource-manager/managed-applications/publish-service-catalog-app?tabs=azure-powershell#create-the-arm-template.md). Questo modello definisce le risorse da distribuire nella sottoscrizione di Azure del cliente. Per esempi di modelli di Resource Manager, vedere la [raccolta di modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/) o il repository [GitHub: modelli di avvio rapido di Azure Resource Manager](https://github.com/azure/azure-quickstart-templates) corrispondente.
- Definizione dell'interfaccia utente per l'esperienza di creazione di applicazioni Azure denominata [createUiDefinition.json](/azure/azure-resource-manager/managed-application-createuidefinition-overview). Nell'interfaccia utente specificare gli elementi che consentono ai consumer di fornire i valori dei parametri.

Le dimensioni massime dei file supportate sono:

- Fino a 1 GB di dimensioni totali dell'archivio zip compresso
- Fino a 1 GB per ogni singolo file non compresso nell'archivio zip

Tutte le nuove offerte per applicazioni Azure devono includere anche un GUID di [attribuzione di utilizzo da parte dei clienti e dei partner di Azure](azure-partner-customer-usage-attribution.md).

## <a name="azure-regions"></a>Aree di Azure

È possibile pubblicare il piano nell'area pubblica di Azure, nell'area di Azure per enti pubblici o in entrambi. Prima di eseguire la pubblicazione in [Azure per enti pubblici](/azure/azure-government/documentation-government-manage-marketplace-partners), testare e convalidare il piano nell'ambiente, in quanto determinati endpoint possono essere diversi. Per configurare e testare il piano, richiedere un account di valutazione per la [versione di valutazione di Microsoft Azure per enti pubblici](https://azure.microsoft.com/global-infrastructure/government/request/).

L'editore è responsabile di tutti i controlli di conformità, le misure di sicurezza e le procedure consigliate. Azure per enti pubblici usa data center e reti fisicamente isolati (situati solo negli Stati Uniti).

Per un elenco di paesi e aree geografiche supportati dal Marketplace commerciale, vedere Supporto per la [disponibilità geografica e la valuta](marketplace-geo-availability-currencies.md).

I servizi di Azure per enti pubblici gestiscono dati soggetti a determinati requisiti e normative governativi. Ad esempio, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Per aumentare la consapevolezza sulle certificazioni per questi programmi, è possibile specificare fino a 100 collegamenti descrittivi. Questi possono essere collegamenti alla presentazione direttamente nel programma o a descrizioni della conformità a tali standard nei propri siti Web. Questi collegamenti sono visibili solo ai clienti di Azure per enti pubblici.

## <a name="choose-who-can-see-your-plan"></a>Scegliere chi può visualizzare il piano

È possibile configurare ogni piano in modo che sia visibile a tutti (pubblico) o solo a un gruppo di destinatari specifico (privato). È possibile creare fino a 100 piani e fino a 45 di essi può essere privato. Potrebbe essere necessario creare un piano privato per offrire diverse opzioni di prezzo o configurazioni tecniche a specifici clienti.

Si concede l'accesso a un piano privato usando gli ID sottoscrizione di Azure con l'opzione per includere una descrizione di ogni ID sottoscrizione assegnato. È possibile aggiungere un massimo di 10 ID sottoscrizione manualmente o fino a 10.000 ID sottoscrizione usando. File CSV. Gli ID sottoscrizione di Azure vengono rappresentati come GUID e le lettere devono essere minuscole.

> [!NOTE]
> Se si pubblica un piano privato, è possibile modificarne la visibilità a Public in un secondo momento. Tuttavia, dopo aver pubblicato un piano pubblico, non è possibile modificarne la visibilità in privato.

Per i piani di modelli di soluzione, è anche possibile scegliere di nascondere il piano da Azure Marketplace. Questa operazione può essere eseguita se il piano viene distribuito indirettamente solo tramite un altro modello di soluzione o un'applicazione gestita.

> [!NOTE]
> I piani privati non sono supportati con le sottoscrizioni di Azure stabilite tramite un rivenditore del programma Cloud Solution Provider (CSP).

Per ulteriori informazioni, vedere [offerte private in Microsoft Commercial Marketplace](private-offers.md).

## <a name="next-steps"></a>Passaggi successivi

- [Come creare un'offerta di applicazione Azure nel Marketplace commerciale](create-new-azure-apps-offer.md)
