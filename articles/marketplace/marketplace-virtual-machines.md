---
title: Guida alla pubblicazione di offerte di macchine virtuali per Azure Marketplace
description: Questo articolo descrive i requisiti per pubblicare una macchina virtuale e una versione di prova gratuita del software da distribuire dal Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
ms.service: marketplace
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: ccb6fc9c522e8d05d0184fc5e248d070efb9921d
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64937730"
---
# <a name="virtual-machine-offer-publishing-guide"></a>Guida alla pubblicazione di offerte di macchine virtuali

Le immagini delle macchine virtuali sono uno dei modi principali di pubblicare una soluzione nel Marketplace di Azure. Usare questa guida per comprendere i requisiti per questa offerta. 

Sono offerte di transazioni che vengono distribuite e fatturate attraverso il Marketplace. L'utente viene invitato all'azione tramite "Scarica adesso".

## <a name="free-trial"></a>Versione di valutazione gratuita 

È possibile fare in modo che gli utenti possano testare la propria offerta accedendo a licenze software per un periodo limitato quando usano il modello di fatturazione Bring Your Own License (BYOL). Di seguito sono riportati i requisiti per implementare questa offerta. 

|Requisiti  |Dettagli  |
|---------|---------|
|Periodo di valutazione gratuita ed esperienza di valutazione     |   I clienti possono provare l'app gratuitamente per un periodo di tempo limitato. I clienti non devono pagare costi di licenza o di sottoscrizione per l'offerta. I clienti non devono pagare il prodotto o il servizio proprietario di Microsoft sottostante. Tutte le opzioni di valutazione sono distribuite nella sottoscrizione di Azure. L'utente ha il controllo esclusivo dell'ottimizzazione dei costi e della gestione. È possibile scegliere una versione di valutazione gratuita o una demo interattiva. Indipendentemente dall'opzione selezionata, per la prova gratuita è necessario concedere ai clienti un periodo prestabilito di tempo per provare l'offerta senza costi aggiuntivi.|
|Soluzione pronta all'uso e facilmente configurabile    |  L'app deve essere semplice e rapida da configurare e installare.       |
|Disponibilità/Tempo di attività    |    L'app o la piattaforma SaaS deve avere un tempo di attività pari ad almeno il 99,9%.     |
|Azure Active Directory     |    L'offerta deve consentire l'accesso Single Sign-On (SSO) federato di Azure Active Directory (accesso SSO federato di Azure AD) con il consenso abilitato.     |

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
| Disco rigido virtuale (VHD) compatibile con Azure | Le macchine virtuali devono essere compilate in Windows o Linux. <ul> <li>Per altre informazioni sulla creazione di un disco rigido virtuale Linux, vedere [Distribuzioni di Linux approvate in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Per altre informazioni sulla creazione di un disco rigido virtuale Windows, [vedere Creare un disco rigido virtuale compatibile con Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |  

>[!Note]
>Cloud Solution Provider (CSP) partner canale acconsentire esplicitamente a questo punto è disponibile.  Vedi [Cloud Solution Provider](./cloud-solution-providers.md) per altre informazioni sul marketing dell'offerta tramite Microsoft CSP partner canali.

## <a name="next-steps"></a>Passaggi successivi

Se non è già stato fatto, 

- [Registrarsi](https://azuremarketplace.microsoft.com/sell) in Marketplace.

Se la registrazione è già stata effettuata e si sta creando una nuova offerta o lavorando su una esistente,

- [Accedere al portale Cloud Partner](https://cloudpartner.azure.com) per creare o completare l'offerta.
- Per altre informazioni, vedere [Offerta di una macchina virtuale](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-virtual-machine-offer).
