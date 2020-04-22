---
title: Guida alla pubblicazione di offerte di macchine virtuali per Azure Marketplace
description: Questo articolo descrive i requisiti per pubblicare una macchina virtuale e una versione di prova gratuita del software da distribuire dal Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 2fa67d81546db86535c179a9c59d0602c1175cba
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687504"
---
# <a name="virtual-machine-offer-publishing-guide"></a>Guida alla pubblicazione di offerte di macchine virtuali

Le immagini delle macchine virtuali sono uno dei modi principali di pubblicare una soluzione nel Marketplace di Azure. Usare questa guida per comprendere i requisiti per questa offerta. 

Sono offerte di transazioni che vengono distribuite e fatturate attraverso il Marketplace. L'utente viene invitato all'azione tramite "Scarica adesso".

## <a name="free-trial"></a>Versione di valutazione gratuita 

È possibile fare in modo che gli utenti possano testare la propria offerta accedendo a licenze software per un periodo limitato quando usano il modello di fatturazione Bring Your Own License (BYOL). 

## <a name="test-drive"></a>Test drive

Vengono distribuite una o più macchine virtuali tramite le applicazioni IaaS (infrastructure-as-a-service) o SaaS (software-as-a-service). Un vantaggio dell'opzione di pubblicazione del test drive è il provisioning automatico di una macchina virtuale o dell'intera soluzione con una presentazione guidata ospitata da un partner. Un test drive fornisce al cliente una valutazione senza alcun costo aggiuntivo. Non è necessario che il cliente sia già un utente di Azure per partecipare all'esperienza di prova. 

Per iniziare, contattare Microsoft all'indirizzo [amp-testdrive](mailto:amp-testdrive@microsoft.com). 

|Requisiti  |Dettagli |
|---------|---------|
| Si dispone di un'app di Marketplace   |    Una o più macchine virtuali tramite IaaS o SaaS.      |

## <a name="interactive-demo"></a>Demo interattiva

Fornire ai clienti un'esperienza guidata della soluzione usando una demo interattiva. Il vantaggio dell'opzione di pubblicazione della demo interattiva è dato dalla possibilità di fornire un'esperienza di valutazione di una soluzione complessa senza dover eseguire complicate operazioni di provisioning. 

## <a name="virtual-machine-offer"></a>Offerta per le macchine virtuali

Usare il tipo di offerta per le macchine virtuali quando si distribuisce un'appliance virtuale all'abbonamento associato al cliente. Le macchine virtuali sono completamente abilitate per la commercializzazione tramite i modelli di licenza con pagamento in base al consumo o Bring Your Own License (BYOL). Microsoft gestisce la transazione commerciale e addebita il costo al cliente per conto dell'utente. Si ottiene il vantaggio di usare la relazione di pagamento preferita tra il cliente e Microsoft, compresi eventuali contratti Enterprise.

> [!NOTE]
> A questo punto, gli impegni monetari associati a un contratto Enterprise possono essere usati per l'utilizzo di Azure della macchina virtuale, ma non per i costi di licenza del software.  
> 
> [!NOTE]
> È possibile limitare l'individuazione e la distribuzione della VM a un set specifico di clienti pubblicando l'immagine e i prezzi in un'inserzione privata. Le offerte private consentono agli utenti di creare offerte esclusive per i clienti principali e di proporre software e condizioni di utilizzo personalizzati. Tali condizioni personalizzate consentono di porre in evidenza numerosi scenari, con offerte contenenti condizioni e prezzi particolari, nonché l'accesso anticipato a versioni software limitate. Le offerte private consentono di offrire a un gruppo ristretto di clienti prezzi o prodotti specifici, creando un nuovo SKU con i dettagli selezionati.  
> *   Per altre informazioni sulle offerte private, visitare la pagina relativa alle offerte private in Azure Marketplace all'indirizzo [azure.microsoft.com/blog/private-offers-on-azure-marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace).  

| Requisito | Dettagli |  
|:--- |:--- | 
| Fatturazione e misurazione | La macchina virtuale deve supportare la fatturazione mensile BYOL o con pagamento in base al consumo. |  
| Disco rigido virtuale (VHD) compatibile con Azure | Le macchine virtuali devono essere compilate in Windows o Linux. <ul> <li>Per altre informazioni sulla creazione di un disco rigido virtuale Linux, vedere [Distribuzioni di Linux approvate in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Per altre informazioni sulla creazione di un disco rigido virtuale Windows, [vedere Creare un disco rigido virtuale compatibile con Azure](./partner-center-portal/azure-vm-create-offer.md).</li> </ul> |  

>[!Note]
>L'opt-in del canale per i partner Cloud Solution Provider (CSP) è ora disponibile.  Per ulteriori informazioni sul marketing dell'offerta tramite i canali partner Microsoft CSP, consulta [Cloud Solution Provider.](./cloud-solution-providers.md)

## <a name="next-steps"></a>Passaggi successivi

Se non è già stato fatto, 

- [Scopri](https://azuremarketplace.microsoft.com/sell) di più sul mercato.

Se la registrazione è già stata effettuata e si sta creando una nuova offerta o lavorando su una esistente,

- [Accedi al Centro per i partner](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) per creare o completare la tua offerta.
- Per altre informazioni, vedere [Creare un'offerta](./partner-center-portal/azure-vm-create-offer.md) di macchina virtuale.
