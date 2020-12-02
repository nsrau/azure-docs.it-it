---
title: Pianificare un'applicazione gestita di Azure per un'offerta di applicazione Azure
description: Informazioni sugli elementi necessari per creare un piano di applicazione gestita per una nuova offerta di applicazione Azure tramite il portale di Marketplace commerciale nel centro per i partner Microsoft.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: a915b5a348e3167d2cf81906b19abd9850584ce9
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460985"
---
# <a name="plan-an-azure-managed-application-for-an-azure-application-offer"></a>Pianificare un'applicazione gestita di Azure per un'offerta di applicazione Azure

Un piano di _applicazione gestita_ di Azure è un modo per pubblicare un'offerta di applicazione Azure in Azure Marketplace. Se non è già stato fatto, leggere [pianificare un'offerta applicazione Azure per il Marketplace commerciale](plan-azure-application-offer.md).

Le applicazioni gestite sono offerte per transazioni distribuite e fatturate tramite Azure Marketplace. L'opzione di elenco visualizzata da un utente è Get Now.

Usare un applicazione Azure: piano dell'applicazione gestita quando sono necessarie le condizioni seguenti:

- Si distribuirà una soluzione basata sulla sottoscrizione per il cliente usando una macchina virtuale (VM) o un'intera soluzione basata su infrastruttura distribuita come servizio (IaaS).
- L'utente o il cliente richiede che la soluzione sia gestita da un partner. Un partner può essere ad esempio un integratore di sistemi o un provider di servizi gestiti (MSP).

## <a name="managed-application-offer-requirements"></a>Requisiti dell'offerta di applicazione gestita

| Requisiti | Dettagli |
| ------------ | ------------- |
| Una sottoscrizione di Azure | Le applicazioni gestite devono essere distribuite nella sottoscrizione di un cliente, ma possono essere gestite da terze parti. |
| Fatturazione e misurazione | Le risorse vengono fornite nella sottoscrizione di Azure di un cliente. Le macchine virtuali che usano il modello di pagamento con pagamento in base al consumo vengono sottoposte a transazione con il cliente tramite Microsoft e fatturate tramite la sottoscrizione di Azure del cliente. <br><br> Per le macchine virtuali con licenza Bring-Your-Own, Microsoft addebita tutti i costi di infrastruttura sostenuti per la sottoscrizione del cliente, ma è possibile effettuare direttamente le spese di licenza software con il cliente. |
| Disco rigido virtuale (VHD) compatibile con Azure | Le macchine virtuali devono essere compilate in Windows o Linux. Per altre informazioni, vedere:<br> • [Creare un asset tecnico della macchina virtuale di Azure](./azure-vm-create-certification-faq.md#address-a-vulnerability-or-an-exploit-in-a-vm-offer) per i dischi rigidi virtuali di Windows.<br> •  [Distribuzioni Linux approvate in Azure](../virtual-machines/linux/endorsed-distros.md) (per i dischi rigidi virtuali Linux). |
| Attribuzione dell'uso da parte dei clienti | Tutte le nuove offerte per applicazioni Azure devono includere anche un GUID di [attribuzione di utilizzo da parte dei clienti e dei partner di Azure](azure-partner-customer-usage-attribution.md). Per ulteriori informazioni sull'attribuzione dell'utilizzo dei clienti e su come abilitarla, vedere l' [attribuzione dell'utilizzo dei clienti partner di Azure](azure-partner-customer-usage-attribution.md). |
| Pacchetto di distribuzione | È necessario un pacchetto di distribuzione che consentirà ai clienti di distribuire il piano. Se si creano più piani che richiedono la stessa configurazione tecnica, è possibile usare lo stesso pacchetto. Per informazioni dettagliate, vedere la sezione successiva: pacchetto di distribuzione. |
|||

> [!NOTE]
> Le applicazioni gestite devono essere distribuibili tramite Azure Marketplace. Se la comunicazione dei clienti rappresenta un problema, contattare i clienti interessati dopo aver abilitato la condivisione dei lead.

## <a name="deployment-package"></a>Pacchetto di distribuzione

Il pacchetto di distribuzione contiene tutti i file di modello necessari per questo piano, nonché eventuali risorse aggiuntive, incluse in un file con estensione zip.

Tutte le applicazioni Azure devono includere questi due file nella cartella radice di un archivio zip:

- Un file di modello di Resource Manager denominato [mainTemplate.json](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template). Questo modello definisce le risorse da distribuire nella sottoscrizione di Azure del cliente. Per esempi di modelli di Gestione risorse, vedere la [raccolta di modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/) o il repository di modelli di [avvio rapido GitHub: Azure Resource Manager](https://github.com/azure/azure-quickstart-templates) corrispondente.
- Definizione dell'interfaccia utente per l'esperienza di creazione di applicazioni Azure denominata [createUiDefinition.json](../azure-resource-manager/managed-applications/create-uidefinition-overview.md). Nell'interfaccia utente specificare gli elementi che consentono ai consumer di fornire i valori dei parametri.

Le dimensioni massime dei file supportate sono:

- Fino a 1 GB di dimensioni totali dell'archivio zip compresso
- Fino a 1 GB per ogni singolo file non compresso nell'archivio zip

Tutte le nuove offerte per applicazioni Azure devono includere anche un GUID di [attribuzione di utilizzo da parte dei clienti e dei partner di Azure](azure-partner-customer-usage-attribution.md).

## <a name="azure-regions"></a>Aree di Azure

È possibile pubblicare il piano nell'area pubblica di Azure, nell'area di Azure per enti pubblici o in entrambi. Prima di eseguire la pubblicazione in [Azure per enti pubblici](../azure-government/documentation-government-manage-marketplace-partners.md), testare e convalidare il piano nell'ambiente, in quanto determinati endpoint possono essere diversi. Per configurare e testare il piano, richiedere un account di valutazione per la [versione di valutazione di Microsoft Azure per enti pubblici](https://azure.microsoft.com/global-infrastructure/government/request/).

L'editore è responsabile di tutti i controlli di conformità, le misure di sicurezza e le procedure consigliate. Azure per enti pubblici usa data center e reti fisicamente isolati (situati solo negli Stati Uniti).

Per un elenco di paesi e aree geografiche supportati dal Marketplace commerciale, vedere Supporto per la [disponibilità geografica e la valuta](marketplace-geo-availability-currencies.md).

I servizi di Azure per enti pubblici gestiscono dati soggetti a determinati requisiti e normative governativi. Ad esempio, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Per aumentare la consapevolezza sulle certificazioni per questi programmi, è possibile specificare fino a 100 collegamenti descrittivi. Questi possono essere collegamenti alla presentazione direttamente nel programma o a descrizioni della conformità a tali standard nei propri siti Web. Questi collegamenti sono visibili solo ai clienti di Azure per enti pubblici.

## <a name="choose-who-can-see-your-plan"></a>Scegliere chi può visualizzare il piano

È possibile configurare ogni piano in modo che sia visibile a tutti (pubblico) o solo a un gruppo di destinatari specifico (privato). È possibile creare fino a 100 piani e fino a 45 di essi può essere privato. Potrebbe essere necessario creare un piano privato per offrire diverse opzioni di prezzo o configurazioni tecniche a specifici clienti.

Si concede l'accesso a un piano privato usando gli ID sottoscrizione di Azure con l'opzione per includere una descrizione di ogni ID sottoscrizione assegnato. È possibile aggiungere un massimo di 10 ID sottoscrizione manualmente o fino a 10.000 ID sottoscrizione usando. File CSV. Gli ID sottoscrizione di Azure vengono rappresentati come GUID e le lettere devono essere minuscole.

I piani privati non sono supportati con le sottoscrizioni di Azure stabilite tramite un rivenditore del programma Cloud Solution Provider (CSP). Per ulteriori informazioni, vedere [offerte private in Microsoft Commercial Marketplace](private-offers.md).

> [!NOTE]
> Se si pubblica un piano privato, è possibile modificarne la visibilità a Public in un secondo momento. Tuttavia, dopo aver pubblicato un piano pubblico, non è possibile modificarne la visibilità in privato.

## <a name="define-pricing"></a>Definire i prezzi

È necessario fornire il prezzo mensile per ogni piano. Questo prezzo è in aggiunta a tutti gli altri costi dell'infrastruttura di Azure o i costi software con pagamento in base al consumo sostenuti dalle risorse distribuite da questa soluzione.

Oltre al prezzo mensile, è anche possibile impostare i prezzi per l'utilizzo di unità non standard tramite la [fatturazione a consumo](partner-center-portal/azure-app-metered-billing.md). È anche possibile impostare il prezzo al mese su zero e addebitare esclusivamente la fatturazione a consumo.

I prezzi sono impostati in USD (USD = Stati Uniti dollaro) vengono convertiti nella valuta locale di tutti i mercati selezionati usando i tassi di cambio correnti quando vengono salvati. È tuttavia possibile scegliere di impostare i prezzi dei clienti per ogni mercato.

## <a name="just-in-time-jit-access"></a>Accesso just-in-time (JIT)

L'accesso JIT consente di richiedere l'accesso con privilegi elevati alle risorse di un'applicazione gestita per la risoluzione dei problemi o la manutenzione. L'accesso alle risorse è sempre in sola lettura, ma per un periodo di tempo specifico è possibile richiedere un accesso di livello maggiore. Per altre informazioni, vedere [Abilitare e richiedere l'accesso JIT per applicazioni gestite di Azure](../azure-resource-manager/managed-applications/request-just-in-time-access.md).

> [!NOTE]
> Informazioni che l'utente deve notare anche se skimmingBe di aggiornare il `createUiDefinition.json` file per supportare questa funzionalità.

## <a name="deployment-mode"></a>Modalità di distribuzione

È possibile configurare un piano di applicazione gestita per usare la modalità di distribuzione **completa** o **incrementale** . In modalità completa, una ridistribuzione dell'applicazione da parte del cliente comporta la rimozione delle risorse nel gruppo di risorse gestite se le risorse non sono definite nella [mainTemplate.jssu](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template). In modalità Incrementale una ridistribuzione dell'applicazione lascia invariate le risorse esistenti. Per altre informazioni, vedere [Azure Resource Manager modalità di distribuzione](../azure-resource-manager/templates/deployment-modes.md).

## <a name="notification-endpoint-url"></a>URL endpoint di notifica

Facoltativamente, è possibile fornire un endpoint del webhook HTTPS per ricevere notifiche relative a tutte le operazioni CRUD sulle istanze di applicazioni gestite di un piano.

## <a name="customize-allowed-customer-actions-optional"></a>Personalizzare le azioni dei clienti consentite (facoltativo)

Facoltativamente, è possibile specificare le azioni che i clienti possono eseguire sulle risorse gestite, oltre alle `*/read` azioni disponibili per impostazione predefinita.

Se si sceglie questa opzione, è necessario specificare le azioni di controllo o le azioni dati consentite oppure entrambe. Per altre informazioni, vedere [Informazioni sulle assegnazioni di rifiuto per le risorse di Azure](../role-based-access-control/deny-assignments.md). Per informazioni sulle azioni disponibili, vedere [Operazioni di provider di risorse con Azure Resource Manager](../role-based-access-control/resource-provider-operations.md). Ad esempio, per consentire agli utenti di riavviare le macchine virtuali, aggiungere `Microsoft.Compute/virtualMachines/restart/action` alle azioni consentite.

## <a name="choose-who-can-manage-the-application"></a>Scegliere chi può gestire l'applicazione

È necessario indicare chi può gestire un'applicazione gestita in ognuno dei cloud selezionati: _public Azure_ e _Azure Government cloud_. Raccogliere le seguenti informazioni:

- **Azure Active Directory ID tenant** : l'id tenant Azure ad (noto anche come ID directory) contenente le identità degli utenti, dei gruppi o delle applicazioni a cui si vogliono concedere le autorizzazioni. È possibile trovare l'ID tenant Azure AD nel portale di Azure, in [proprietà per Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).
- **Autorizzazioni** : aggiungere l'ID oggetto Azure Active Directory di ogni utente, gruppo o applicazione a cui si vuole concedere l'autorizzazione per il gruppo di risorse gestite. Identificare l'utente tramite l'ID entità di sicurezza, disponibile nel [pannello degli utenti di Azure Active Directory del portale di Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers).

Per ogni ID entità, viene associato uno dei ruoli predefiniti Azure AD (proprietario o collaboratore). Il ruolo selezionato descrive le autorizzazioni che l'entità avrà sulle risorse nella sottoscrizione del cliente. Per altre informazioni, vedere [Ruoli predefiniti di Azure](../role-based-access-control/built-in-roles.md). Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Introduzione al controllo degli accessi in base al ruolo nel portale di Azure](../role-based-access-control/overview.md).

> [!NOTE]
> Sebbene sia possibile aggiungere fino a 100 autorizzazioni per ogni area di Azure, è in genere più semplice creare un gruppo di utenti Active Directory e specificarne l'ID nell'"ID entità". In questo modo è possibile aggiungere altri utenti al gruppo di gestione dopo la distribuzione del piano e ridurre la necessità di aggiornare il piano solo per aggiungere altre autorizzazioni.

## <a name="policy-settings"></a>Impostazioni dei criteri

È possibile applicare [criteri di Azure](../governance/policy/index.yml) all'applicazione gestita per specificare i requisiti di conformità per la soluzione distribuita. Per le definizioni dei criteri e il formato dei valori dei parametri, vedere [Esempi di criteri di Azure](../governance/policy/samples/index.md).

È possibile configurare un massimo di cinque criteri e una sola istanza di ogni tipo di criterio. Alcuni tipi di criteri richiedono parametri aggiuntivi.

| Tipo di criteri | Parametri dei criteri richiesti |
| ------------ | ------------- |
| Crittografia del database SQL di Azure | No |
| Impostazioni di controllo di Azure SQL Server | Sì |
| Crittografia Azure Data Lake Store | No |
| Controllare l'impostazione di diagnostica | Sì |
| Controllare la conformità del percorso delle risorse | No |
|||

Per ogni tipo di criterio aggiunto, è necessario associare lo SKU dei criteri standard o free. Lo SKU Standard è necessario per i criteri di controllo. I nomi dei criteri sono limitati a 50 caratteri.

## <a name="next-steps"></a>Passaggi successivi

- [Come creare un'offerta di applicazione Azure nel Marketplace commerciale](create-new-azure-apps-offer.md)