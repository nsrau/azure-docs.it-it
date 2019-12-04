---
title: Informazioni sui lab per le classi in Azure Lab Services | Microsoft Docs
description: Informazioni su come configurare rapidamente un ambiente lab per le classi nel cloud, che consiste nel configurare una VM modello con il software necessario per la classe e creare una copia della macchina virtuale disponibile per ogni studente della classe.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/26/2019
ms.author: spelluru
ms.openlocfilehash: bbbea0c943758c2682f7b166e461807d2914f790
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561684"
---
# <a name="introduction-to-classroom-labs"></a>Introduzione ai lab per le classi
Azure Lab Services consente di configurare rapidamente un ambiente di lab per le classi nel cloud. Un docente crea un lab per le classi, esegue il provisioning delle macchine virtuali Windows o Linux, installa il software e gli strumenti necessari e li rende disponibili per gli studenti. Gli studenti nella classe si connettono alle macchine virtuali del lab e le usano per i progetti, per i compiti o per gli esercizi in classe. 

I lab per le classi sono tipi di lab gestiti da Azure. Il servizio controlla completamente la gestione dell'infrastruttura per un tipo di lab gestito, dall'attivazione delle macchine virtuali alla gestione degli errori e al ridimensionamento dell'infrastruttura. Specificare il tipo di infrastruttura necessario e installare qualsiasi strumento o software richiesto per la classe. 

## <a name="automatic-management-of-azure-infrastructure-and-scale"></a>Gestione automatica dell'infrastruttura e della scalabilità di Azure 
Azure Lab Services è un servizio gestito, quindi gestisce automaticamente il provisioning e la gestione dell'infrastruttura sottostante del lab. È quindi possibile concentrarsi esclusivamente sulla preparazione dell'esperienza lab ottimale per i propri utenti. Il servizio si occupa di tutto il resto e distribuisce le macchine virtuali del lab ai destinatari. Il lab è scalabile fino a centinaia di macchine virtuali con un solo clic.

## <a name="simple-experience-for-your-lab-users"></a>Esperienza semplice per gli utenti del lab 
Gli utenti che vengono invitati a partecipare al lab ottengono accesso immediato alle risorse messe a loro disposizione all'interno dei lab. Devono semplicemente accedere per vedere l'elenco completo delle macchine virtuali a cui hanno accesso nei vari lab. Per connettersi alle macchine virtuali e iniziare a lavorare non devono fare altro che selezionare un pulsante. Gli utenti non hanno bisogno di una sottoscrizione di Azure per usare il servizio. 

## <a name="cost-optimization-and-tracking"></a>Verifica e ottimizzazione dei costi  
È possibile tenere sotto controllo il budget controllando il numero esatto di ore per cui gli utenti del lab possono usare le macchine virtuali. È possibile impostare pianificazioni nel lab per permettere agli utenti di usare le macchine virtuali solo durante fasce orarie designate o configurare orari ricorrenti per l'arresto e l'avvio automatici. È possibile tenere traccia dell'utilizzo e dei limiti impostati dei singoli utenti.

## <a name="example-class-types"></a>Esempio di tipi di classe
È possibile configurare lab per diversi tipi di classi con Azure Lab Services. Per alcuni esempi di tipi di classi per cui è possibile configurare i lab con Azure Lab Services, vedere l'articolo [Esempi di tipi di classe in Azure Lab Services](class-types.md). 

## <a name="next-steps"></a>Passaggi successivi
Leggere informazioni introduttive sulla configurazione di un account lab necessario per creare un lab per le classi con Azure Lab Services:

- [Configurare un account Lab](tutorial-setup-lab-account.md)
