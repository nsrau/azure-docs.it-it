---
title: Rilevamento delle minacce a livello di servizio di Azure-Centro sicurezza di Azure
description: Questo argomento presenta gli avvisi del livello di servizio di Azure disponibili nel centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: memildin
ms.openlocfilehash: d4033989830323856ac14ed06eea7df74806f128
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665701"
---
# <a name="threat-detection-for-the-azure-service-layer-in-azure-security-center"></a>Rilevamento delle minacce per il livello di servizio di Azure nel centro sicurezza di Azure

Questo argomento presenta gli avvisi del Centro sicurezza di Azure disponibili quando si monitorano i livelli di servizio di Azure seguenti:

* [Livello rete di Azure](#network-layer)
* [Livello di gestione di Azure (Azure Resource Manager) (anteprima)](#management-layer)
* [Insieme di credenziali chiave Azure](#azure-keyvault)

## Livello rete di Azure<a name="network-layer"></a>

L'analisi a livello di rete del Centro sicurezza si basa su [dati IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)di esempio, ovvero le intestazioni dei pacchetti raccolte dai router core di Azure. In base a questo feed di dati, il Centro sicurezza usa i modelli di apprendimento automatico per identificare e contrassegnare le attività di traffico dannoso. Il Centro sicurezza usa anche il database di intelligence per le minacce di Microsoft per arricchire gli indirizzi IP.

Alcune configurazioni di rete possono impedire al centro sicurezza di generare avvisi sull'attività di rete sospetta. Per consentire al centro sicurezza di generare avvisi di rete, verificare quanto segue:

- La macchina virtuale ha un indirizzo IP pubblico o si trova in un servizio di bilanciamento del carico con un indirizzo IP pubblico.

- Il traffico in uscita di rete della macchina virtuale non è bloccato da una soluzione di ID esterno.

- Alla macchina virtuale è stato assegnato lo stesso indirizzo IP per l'intera ora durante la quale si è verificata la comunicazione sospetta. Questo vale anche per le macchine virtuali create come parte di un servizio gestito (ad esempio, AKS, databricks).

Per un elenco degli avvisi di livello rete di Azure, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-azurenetlayer).

Per informazioni dettagliate sul modo in cui il Centro sicurezza può usare i segnali relativi alla rete per applicare la protezione dalle minacce, vedere [rilevamento di DNS euristici nel centro sicurezza](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).


## Livello di gestione di Azure (Azure Resource Manager) (anteprima)<a name ="management-layer"></a>

Il livello di protezione del Centro sicurezza basato su Azure Resource Manager è attualmente in fase di anteprima.

Il Centro sicurezza offre un ulteriore livello di protezione usando gli eventi Azure Resource Manager, che è considerato il piano di controllo per Azure. Analizzando i record di Azure Resource Manager, il Centro sicurezza rileva operazioni insolite o potenzialmente dannose nell'ambiente della sottoscrizione di Azure.

Per un elenco degli avvisi di Azure Resource Manager (anteprima), vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-azureresourceman).



>[!NOTE]
> Alcune delle analisi precedenti sono basate su Microsoft Cloud App Security. Per trarre vantaggio da queste analisi, è necessario attivare una licenza Cloud App Security. Se si dispone di una licenza di Cloud App Security, questi avvisi sono abilitati per impostazione predefinita. Per disabilitarli:
>
> 1. Nel pannello **Centro sicurezza** selezionare criteri di **sicurezza**. Per la sottoscrizione che si vuole modificare, selezionare **Modifica impostazioni**.
> 2. Selezionare **rilevamento minacce**.
> 3. In **Abilita integrazioni**deselezionare **Consenti Microsoft cloud app Security per accedere ai dati**e selezionare **Salva**.

>[!NOTE]
>Il Centro sicurezza archivia i dati dei clienti correlati alla sicurezza nella stessa area geografica della risorsa. Se Microsoft non ha ancora distribuito il Centro sicurezza nell'area geografica della risorsa, archivia i dati nel Stati Uniti. Quando Cloud App Security è abilitata, queste informazioni vengono archiviate in base alle regole di posizione geografica di Cloud App Security. Per altre informazioni, vedere [archiviazione dei dati per servizi non a livello di](https://azuredatacentermap.azurewebsites.net/)area.

## Azure Key Vault (anteprima)<a name="azure-keyvault"></a>

Azure Key Vault è un servizio cloud che protegge le chiavi di crittografia e i segreti, come certificati, stringhe di connessione e password. 

Il Centro sicurezza di Azure include la protezione avanzata dalle minacce di Azure nativa per Azure Key Vault, offrendo un ulteriore livello di intelligence per la sicurezza. Il Centro sicurezza rileva tentativi insoliti e potenzialmente dannosi di accesso o exploit Key Vault account. Questo livello di protezione consente di risolvere le minacce senza essere un esperto di sicurezza e senza la necessità di gestire sistemi di monitoraggio della sicurezza di terze parti.  

Quando si verificano attività anomale, il Centro sicurezza Mostra gli avvisi e, facoltativamente, li invia tramite posta elettronica agli amministratori della sottoscrizione. Questi avvisi includono i dettagli dell'attività sospetta e consigli su come analizzare e correggere le minacce. 

> [!NOTE]
> Questo servizio non è attualmente disponibile in Azure per enti pubblici e aree cloud sovrane.

Per un elenco degli avvisi di Azure Key Vault, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-azurekv).
