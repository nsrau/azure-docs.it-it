---
title: Informazioni sui DNS nello Stack di Azure | Documenti Microsoft
description: "La comprensione di DNS e le funzionalità nello Stack di Azure"
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: darmour
editor: 
ms.assetid: 60f5ac85-be19-49ac-a7c1-f290d682b5de
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: scottnap
ms.openlocfilehash: 381947a5c936b2bbcae1cf61cdd36eb1653be30b
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="introducing-idns-for-azure-stack"></a>Introduzione a nomi IDN per Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

nomi IDN è una funzione nello Stack di Azure che consente di risolvere i nomi DNS esterni (ad esempio http://www.bing.com).
Consente inoltre di registrare i nomi di rete virtuale interna. In questo modo, è possibile risolvere le macchine virtuali nella stessa rete virtuale con nome anziché come indirizzo IP, senza dover fornire voci dei server DNS personalizzate.

Si tratta di qualcosa che è sempre stato presente in Azure, ma è disponibile in Windows Server 2016 e Stack di Azure.

## <a name="what-does-idns-do"></a>Scopo di nomi IDN
Con nomi IDN nello Stack di Azure, sono disponibili le seguenti funzionalità, senza dover specificare voci dei server DNS personalizzate:

* Condiviso di servizi di risoluzione DNS per i carichi di lavoro tenant.
* Servizio DNS autorevole per la risoluzione dei nomi e la registrazione DNS nella rete virtuale del tenant.
* Servizio Recursive DNS per la risoluzione dei nomi Internet da macchine virtuali tenant. Tenant non è più necessario specificare le voci DNS personalizzate per la risoluzione dei nomi Internet (ad esempio, www.bing.com).

È comunque possibile portare il proprio servizio DNS e se si desidera utilizzare i server DNS personalizzati. Ma, a questo punto, se si desidera essere in grado di risolvere il DNS Internet i nomi e in grado di connettersi ad altre macchine virtuali nella stessa rete virtuale, non è necessario specificare un valore e funzionerà solo.

## <a name="what-does-idns-not-do"></a>Quali sono nomi IDN non?
I nomi IDN non consente di eseguire la creazione di un record DNS per un nome che può essere risolto dall'esterno della rete virtuale.

In Azure, è possibile specificare un'etichetta del nome DNS che può essere associata a un indirizzo IP pubblico. È possibile scegliere l'etichetta (prefisso), ma Azure sceglie il suffisso è basato sull'area in cui creare l'indirizzo IP pubblico.

![Etichetta del nome della schermata di DNS](media/azure-stack-understanding-dns-in-tp2/image3.png)

Nell'immagine precedente, Azure creerà un"record" in DNS per l'etichetta del nome DNS specificato nella zona **westus.cloudapp.azure.com**. Il prefisso e il suffisso insieme costituiscono un dominio nome (completo) che può essere risolto da un punto qualsiasi sulla rete Internet pubblica.

Stack di Azure supporta solo i nomi IDN per la registrazione del nome interno, pertanto non è possibile eseguire le operazioni seguenti:

* Creare un record DNS in una zona DNS ospitato esistente (ad esempio, local.azurestack.external).
* Creare una zona DNS (ad esempio, Contoso.com).
* Creare un record nella zona DNS personalizzato.
* Supporta l'acquisto di nomi di dominio.

